---
title: 教學課程-在 Azure AD Domain Services 中建立樹系信任 |Microsoft Docs
description: 瞭解如何在適用于 Azure 入口網站中的內部部署 AD DS 網域建立單向輸出樹系 Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 3637a11724c1f0bab049077c5abbd817e168bd44
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931223"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>教學課程：在 Azure Active Directory Domain Services （預覽）中建立內部部署網域的輸出樹系信任

在無法同步處理密碼雜湊的環境中，或您有使用智慧卡以獨佔方式登入的使用者，因此他們不知道其密碼，您可以使用 Azure Active Directory Domain Services （AD DS）中的資源樹系。 資源樹系會使用從 Azure AD DS 到一或多個內部部署 AD DS 環境的單向輸出信任。 此信任關係可讓使用者、應用程式和電腦向 Azure AD DS 受控網域中的內部部署網域進行驗證。 Azure AD DS 資源樹系目前為預覽狀態。

![從 Azure AD DS 到內部部署 AD DS 的樹系信任圖表](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在內部部署 AD DS 環境中設定 DNS，以支援 Azure AD DS 連線能力
> * 在內部部署 AD DS 環境中建立單向的輸入樹系信任
> * 在 Azure AD DS 中建立單向輸出樹系信任
> * 測試和驗證信任關係以進行驗證和資源存取

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 使用資源樹系建立並在您的 Azure AD 租使用者中設定的 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance-advanced]。
    
    > [!IMPORTANT]
    > 請確定您使用*資源*樹系建立了 Azure AD DS 受控網域。 預設選項會建立*使用者*樹系。 只有資源樹系可以建立對內部內部部署 AD DS 環境的信任。 您也需要為受控網域使用最低的*企業*SKU。 如有需要，請[變更 AZURE AD DS 受控網域的 SKU][howto-change-sku]。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在本教學課程中，您會使用 Azure 入口網站來建立及設定來自 Azure AD DS 的輸出樹系信任。 若要開始使用，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="networking-considerations"></a>網路功能考量

裝載 Azure AD DS 資源樹系的虛擬網路需要內部部署 Active Directory 的網路連接。 應用程式和服務也需要對裝載 Azure AD DS 資源樹系的虛擬網路進行網路連線。 Azure AD DS 資源樹系的網路連線必須一律開啟且穩定，否則使用者可能無法驗證或存取資源。

在 Azure AD DS 中設定樹系信任之前，請確定 Azure 與內部部署環境之間的網路功能符合下列需求：

* 使用私人 IP 位址。 不要依賴具有動態 IP 位址指派的 DHCP。
* 避免重迭的 IP 位址空間，讓虛擬網路對等互連和路由能夠成功地在 Azure 與內部部署之間進行通訊。
* Azure 虛擬網路需要閘道子網來設定站對站（S2S） VPN 或 ExpressRoute 連線
* 建立具有足夠 IP 位址的子網，以支援您的案例。
* 請確定 Azure AD DS 有自己的子網，請勿與應用程式 Vm 和服務共用此虛擬網路子網。
* 對等互連虛擬網路不可轉移。
    * 您必須在想要使用 Azure AD DS 資源樹系信任至內部部署 AD DS 環境的所有虛擬網路之間建立 Azure 虛擬網路對等互連。
* 提供內部部署 Active Directory 樹系的持續網路連線能力。 請勿使用隨選連接。
* 請確定您的 Azure AD DS 資源樹系名稱與內部部署 Active Directory 樹系名稱之間有持續的名稱解析（DNS）。

## <a name="configure-dns-in-the-on-premises-domain"></a>設定內部部署網域中的 DNS

若要從內部部署環境正確地解析 Azure AD DS 受控網域，您可能需要將轉寄站新增至現有的 DNS 伺服器。 如果您尚未將內部部署環境設定為與 Azure AD DS 受控網域通訊，請從內部部署 AD DS 網域的管理工作站完成下列步驟：

1. 選取 [**開始] |系統管理工具 |DNS**
1. 以滑鼠右鍵選取 [DNS 伺服器]，例如*myAD01*，然後選取 [**屬性**]
1. 選擇 **[** 轉寄站]，然後選取 [**編輯**] 以新增其他轉寄站。
1. 新增 Azure AD DS 受控網域的 IP 位址，例如*10.0.1.4*和*10.0.1.5*。

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>在內部部署網域中建立輸入樹系信任

內部部署 AD DS 網域需要 Azure AD DS 受控網域的連入樹系信任。 此信任必須以手動方式建立于內部部署 AD DS 網域中，而無法從 Azure 入口網站建立。

若要在內部部署 AD DS 網域上設定輸入信任，請從內部部署 AD DS 網域的管理工作站完成下列步驟：

1. 選取 [**開始] |系統管理工具 |Active Directory 網域和信任**
1. 以滑鼠右鍵選取 [網域]，例如*onprem.contoso.com*，然後選取 [**屬性**]
1. 依序選擇 [**信任**] 索引標籤和 [**新增信任**]

   > [!NOTE]
   > 如果您沒有看到 [**信任**] 功能表選項，請檢查樹系*類型*的 [**屬性**]。 只有*資源*樹系可以建立信任。 如果樹系類型為*User*，您就無法建立信任。 目前沒有任何方法可以變更 Azure AD DS 受控網域的樹系類型。 您必須刪除並重新建立受控網域作為資源樹系。

1. 在 [Azure AD DS 功能變數名稱] 上輸入名稱，例如*aadds.contoso.com*，然後選取 **[下一步]**
1. 選取 [建立**樹系信任**] 選項，然後選擇 [建立一個**方式：連入**信任]。
1. 選擇**只建立此網域**的信任。 在下一個步驟中，您會在 Azure AD DS 受控網域的 Azure 入口網站中建立信任。
1. 選擇使用**全樹系驗證**，然後輸入並確認信任密碼。 在下一節中，也會在 Azure 入口網站中輸入相同的密碼。
1. 使用預設選項逐步執行接下來的幾個視窗，然後選擇 [**否，不要確認外寄信任**] 選項。
1. 選取 [完成]。

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>在 Azure AD DS 中建立輸出樹系信任

當內部部署 AD DS 網域設定為解析 Azure AD DS 受控網域和所建立的輸入樹系信任時，現在已建立輸出樹系信任。 此輸出樹系信任會完成內部部署 AD DS 網域與 Azure AD DS 受控網域之間的信任關係。

若要在 Azure 入口網站中建立 Azure AD DS 受控網域的輸出信任，請完成下列步驟：

1. 在 Azure 入口網站中，搜尋並選取  **Azure AD Domain Services**，然後選取您的受控網域，例如*aadds.contoso.com*
1. 從 Azure AD DS 受控網域左側的功能表中，選取 [**信任**]，然後選擇 [ **+ 新增**信任]。
1. 輸入 [顯示名稱] 來識別您的信任，然後輸入內部部署信任的樹系 DNS 名稱，例如*onprem.contoso.com*
1. 提供在上一節中設定內部部署 AD DS 網域的輸入樹系信任時，所使用的相同信任密碼。
1. 為內部部署 AD DS 網域（例如*10.0.2.4*和*10.0.2.5* ）至少提供兩部 DNS 伺服器
1. 準備就緒時，**儲存**輸出樹系信任

    [在 Azure 入口網站中建立輸出樹系信任](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>驗證資源驗證

下列常見案例可讓您驗證樹系信任是否正確地驗證使用者和資源的存取權：

* [Azure AD DS 資源樹系中的內部部署使用者驗證](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [使用內部部署使用者存取 Azure AD DS 資源樹系中的資源](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [啟用檔案及印表機共用](#enable-file-and-printer-sharing)
    * [建立安全性群組並新增成員](#create-a-security-group-and-add-members)
    * [建立跨樹系存取的檔案共用](#create-a-file-share-for-cross-forest-access)
    * [驗證資源的跨樹系驗證](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS 資源樹系中的內部部署使用者驗證

您應該已將 Windows Server 虛擬機器加入 Azure AD DS 資源網域。 使用這部虛擬機器來測試您的內部部署使用者可以在虛擬機器上進行驗證。

1. 使用遠端桌面和您的 Azure AD DS 系統管理員認證，連接到已加入 Azure AD DS 資源樹系的 Windows Server VM。 如果您收到網路層級驗證（NLA）錯誤，請檢查您所使用的使用者帳戶不是網域使用者帳戶。

    > [!NOTE]
    > 若要安全地連接到已加入 Azure AD Domain Services 的 Vm，您可以在支援的 Azure 區域中使用[azure 防禦主機服務](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 開啟命令提示字元，然後使用 `whoami` 命令來顯示目前已驗證使用者的辨別名稱：

    ```console
    whoami /fqdn
    ```

1. 使用 `runas` 命令，以來自內部部署網域的使用者身分進行驗證。 在下列命令中，將 `userUpn@trusteddomain.com` 取代為來自受信任內部部署網域之使用者的 UPN。 此命令會提示您輸入使用者的密碼：

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 如果驗證成功，就會開啟新的命令提示字元。 新命令提示字元的標題包含 `running as userUpn@trusteddomain.com`。
1. 在新的命令提示字元中使用 `whoami /fqdn`，以從內部部署 Active Directory 查看已驗證使用者的辨別名稱。

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>使用內部部署使用者存取 Azure AD DS 資源樹系中的資源

使用已加入 Azure AD DS 資源樹系的 Windows Server VM 時，您可以測試案例，當使用者從內部部署網域中的電腦向內部部署網域的使用者進行驗證時，可以存取資源樹系中主控的資源。 下列範例會示範如何建立和測試各種常見的案例。

#### <a name="enable-file-and-printer-sharing"></a>啟用檔案及印表機共用

1. 使用遠端桌面和您的 Azure AD DS 系統管理員認證，連接到已加入 Azure AD DS 資源樹系的 Windows Server VM。 如果您收到網路層級驗證（NLA）錯誤，請檢查您所使用的使用者帳戶不是網域使用者帳戶。

    > [!NOTE]
    > 若要安全地連接到已加入 Azure AD Domain Services 的 Vm，您可以在支援的 Azure 區域中使用[azure 防禦主機服務](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 開啟 [ **Windows 設定**]，然後搜尋並選取 [**網路和共用中心**]。
1. 選擇 [**變更 advanced 共用**設定] 的選項。
1. 在 [**網域設定檔**] 底下，選取 [**開啟檔案及印表機共用**]，然後**儲存變更**。
1. 關閉 [**網路和共用中心**]。

#### <a name="create-a-security-group-and-add-members"></a>建立安全性群組並新增成員

1. 開啟 [Active Directory 使用者及電腦]。
1. 在 [功能變數名稱] 上按一下滑鼠右鍵，選擇 [**新增**]，然後選取 [**組織單位**]。
1. 在 [名稱] 方塊中，輸入*LocalObjects*，然後選取 **[確定]** 。
1. 選取並以滑鼠右鍵按一下流覽窗格中的 [ **LocalObjects** ]。 依序選取 [**新增**] 和 [**群組**]。
1. 在 [**組名**] 方塊中，輸入*FileServerAccess* 。 針對 [**群組領域**]，選取 [**網域本機**]，然後選擇 **[確定]** 。
1. 在內容窗格中，按兩下 [ **FileServerAccess**]。 選取 [**成員**]，選擇 [**新增**]，然後選取 [**位置**]。
1. 從 [**位置**] 視圖中選取您的內部部署 Active Directory，然後選擇 **[確定]** 。
1. 在 [**輸入要選取的物件名稱**] 方塊中，輸入*Domain Users* 。 選取 [**檢查名稱**]，提供內部部署 Active Directory 的認證，然後選取 **[確定]** 。

    > [!NOTE]
    > 您必須提供認證，因為信任關係只有一種方式。 這表示來自 Azure AD DS 的使用者無法存取資源，或在受信任的（內部部署）網域中搜尋使用者或群組。

1. 來自內部部署 Active Directory 的**網域使用者**群組應該是**FileServerAccess**群組的成員。 選取 **[確定]** 以儲存群組並關閉視窗。

#### <a name="create-a-file-share-for-cross-forest-access"></a>建立跨樹系存取的檔案共用

1. 在已加入 Azure AD DS 資源樹系的 Windows Server VM 上，建立資料夾並提供名稱，例如*CrossForestShare*。
1. 以滑鼠右鍵選取資料夾，然後選擇 [**屬性**]。
1. 選取 [**安全性**] 索引標籤，然後選擇 [**編輯**]。
1. 在 [ *CrossForestShare 的許可權*] 對話方塊中，選取 [**新增**]。
1. 在 **[輸入要選取的物件名稱**] 中輸入*FileServerAccess* ，然後選取 **[確定]** 。
1. 從 [**群組或使用者名稱**] 清單中選取 [ *FileServerAccess* ]。 在 [ **FileServerAccess 的許可權**] 清單中，選擇 [*允許*] 作為 [**修改**] 和 [**寫入**] 許可權，然後選取 **[確定]** 。
1. 選取 [**共用**] 索引標籤，然後選擇 [ **Advanced 共用 ...** ]
1. 選擇 [**共用此資料夾**]，然後在 [**共用名稱**] 中為檔案共用輸入易記的名稱，例如*CrossForestShare*。
1. 選取 [**許可權**]。 在 [ **Everyone 的許可權**] 清單中，選擇 [**允許**] 做為 [**變更**] 許可權。
1. 選取 **[確定]** 兩次，然後按一下 [**關閉**]。

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>驗證資源的跨樹系驗證

1. 使用內部部署 Active Directory 的使用者帳戶，登入加入內部部署 Active Directory 的 Windows 電腦。
1. 使用**Windows Explorer**，連接到您使用完整主機名稱和共用（例如 `\\fs1.aadds.contoso.com\CrossforestShare`）所建立的共用。
1. 若要驗證寫入權限，請在資料夾中以滑鼠右鍵選取，選擇 [**新增**]，然後選取 [**文字檔**]。 使用預設名稱 [**新文字檔**]。

    如果已正確設定寫入權限，則會建立新的文字檔。 接著，下列步驟將會適當地開啟、編輯及刪除檔案。
1. 若要驗證讀取權限，請開啟 [**新的文字檔**]。
1. 若要驗證修改許可權，請將文字新增至檔案，然後關閉 [**記事本**]。 當系統提示您儲存變更時，請選擇 [**儲存**]。
1. 若要驗證刪除許可權，請以滑鼠右鍵選取 [**新的文字檔**]，然後選擇 [**刪除**]。 選擇 **[是]** 以確認檔案刪除。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在內部部署 AD DS 環境中設定 DNS，以支援 Azure AD DS 連線能力
> * 在內部部署 AD DS 環境中建立單向的輸入樹系信任
> * 在 Azure AD DS 中建立單向輸出樹系信任
> * 測試和驗證信任關係以進行驗證和資源存取

如需有關 Azure AD DS 中之樹系類型的詳細概念資訊，請參閱[什麼是資源][concepts-forest]樹系？和[樹系信任在 Azure AD DS 中如何運作？][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
