---
title: 教程 - 在 Azure AD 域服務中創建林信任 |微軟文檔
description: 瞭解如何在 Azure 門戶中為 Azure AD 域服務創建到本地 AD DS 域的單向出站林
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 5620d1cdc7dc71bdac17057b9a13a74150b12d5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612527"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>教程：在 Azure 活動目錄域服務（預覽）中創建到本地域的出站林信任

在無法同步密碼雜湊或使用者使用智慧卡專門登錄，以便他們不知道其密碼的環境中，可以在 Azure 活動目錄域服務 （AD DS） 中使用資源林。 資源林使用從 Azure AD DS 到一個或多個本地 AD DS 環境的單向出站信任。 此信任關係允許使用者、應用程式和電腦根據 Azure AD DS 託管域的本地域進行身份驗證。 Azure AD DS 資源林當前處於預覽狀態。

![從 Azure AD DS 到本地 AD DS 的林信任圖](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在本地 AD DS 環境中配置 DNS 以支援 Azure AD DS 連接
> * 在本地 AD DS 環境中創建單向入站林信任
> * 在 Azure AD DS 中創建單向出站林信任
> * 測試和驗證身份驗證和資源訪問的信任關係

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 使用資源林創建並在 Azure AD 租戶中配置的 Azure 活動目錄域服務託管域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance-advanced]。
    
    > [!IMPORTANT]
    > 請確保使用*資源*林創建 Azure AD DS 託管域。 預設選項將創建*使用者*林。 只有資源林才能創建對 AD DS 環境的信任。 您還需要為託管域使用最少的*企業*SKU。 如果需要[，更改 Azure AD DS 託管域的 SKU。][howto-change-sku]

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在本教程中，使用 Azure 門戶創建和配置 Azure AD DS 的出站林信任。 若要開始使用，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="networking-considerations"></a>網路考量

承載 Azure AD DS 資源林的虛擬網路需要連接到本地活動目錄。 應用程式和服務還需要與託管 Azure AD DS 資源林的虛擬網路進行網路連接。 與 Azure AD DS 資源林的網路連接必須始終打開且穩定，否則使用者可能無法對資源進行身份驗證或訪問。

在 Azure AD DS 中配置林信任之前，請確保 Azure 和本地環境之間的網路滿足以下要求：

* 使用私人 IP 位址。 不要依賴具有動態 IP 位址分配的 DHCP。
* 避免重疊的 IP 位址空間，以允許虛擬網路對等互連和路由在 Azure 和本地之間成功通信。
* Azure 虛擬網路需要閘道子網來配置網站到網站 （S2S） VPN 或 ExpressRoute 連接
* 創建具有足夠 IP 位址的子網以支援您的方案。
* 確保 Azure AD DS 有自己的子網，不要與應用程式 VM 和服務共用此虛擬網路子網。
* 對等虛擬網路不是過渡的。
    * 必須在要使用 Azure AD DS 資源林信任到本地 AD DS 環境的所有虛擬網路之間創建 Azure 虛擬網路對等互連。
* 提供與本地活動目錄林的連續網路連接。 不要使用按需連接。
* 確保 Azure AD DS 資源林名和本地活動目錄林名之間具有連續名稱解析 （DNS）。

## <a name="configure-dns-in-the-on-premises-domain"></a>在本地域中配置 DNS

要從本地環境正確解析 Azure AD DS 託管域，可能需要向現有 DNS 伺服器添加轉寄站。 如果尚未配置本地環境以與 Azure AD DS 託管域通信，請從本地 AD DS 域的管理工作站完成以下步驟：

1. 選擇 **"開始" |管理工具 |DNS**
1. 右選擇 DNS 伺服器（如*myAD01）* 選擇**屬性**
1. 選擇**轉寄站**，然後**編輯**以添加其他轉寄站。
1. 添加 Azure AD DS 託管域的 IP 位址，例如*10.0.1.4*和*10.0.1.5*。

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>在本地域中創建入站林信任

本地 AD DS 域需要 Azure AD DS 託管域的傳入林信任。 此信任必須在本地 AD DS 域中手動創建，無法從 Azure 門戶創建。

要配置本地 AD DS 域上的入站信任，請從本地 AD DS 域的管理工作站完成以下步驟：

1. 選擇 **"開始" |管理工具 |活動目錄域和信任**
1. 右選擇域，如*onprem.contoso.com，* 選擇**屬性**
1. 選擇 **"信任"** 選項卡，然後**選擇"新信任"**

   > [!NOTE]
   > 如果看不到 **"信任"** 功能表選項，請在 *"林"類型的***"屬性**"下進行檢查。 只有*資源*林才能創建信任。 如果林類型為 *"使用者*"，則無法創建信任。 當前無法更改 Azure AD DS 託管域的林類型。 您需要刪除託管域並將其重新創建為資源林。

1. 在 Azure AD DS 功能變數名稱上輸入名稱，如*aaddscontoso.com*，然後選擇 **"下一步"**
1. 選擇創建**林信任**的選項，然後創建**一個單向：傳入**信任。
1. 選擇**僅為此域**創建信任。 在下一步中，您將在 Azure 門戶中為 Azure AD DS 託管域創建信任。
1. 選擇使用**森林範圍的身份驗證**，然後輸入並確認信任密碼。 下一節的 Azure 門戶中也會輸入相同的密碼。
1. 使用預設選項執行接下來的幾個視窗，然後選擇 **"否"選項，不要確認傳出信任**。
1. 選擇 **"完成"**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>在 Azure AD DS 中創建出站林信任

配置本地 AD DS 域以解決 Azure AD DS 託管域，並創建了入站林信任，現在創建了出站林信任。 此出站林信任完成本地 AD DS 域和 Azure AD DS 託管域之間的信任關係。

要在 Azure 門戶中為 Azure AD DS 託管域創建出站信任，請完成以下步驟：

1. 在 Azure 門戶中，搜索並選擇**Azure AD 域服務**，然後選擇託管域，例如*aaddscontoso.com*
1. 從 Azure AD DS 託管域左側的功能表中，選擇 **"信任"，** 然後選擇 **"添加**信任"。
1. 輸入標識信任的顯示名稱，然後輸入本地受信任的林 DNS 名稱，如*onprem.contoso.com*
1. 提供上一節中為本地 AD DS 域配置入站林信任時使用的信任密碼。
1. 為本地 AD DS 域至少提供兩台 DNS 伺服器，如*10.0.2.4*和*10.0.2.5*
1. 準備就緒後，**保存**出站林信任

    [在 Azure 門戶中創建出站林信任](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>驗證資源身份驗證

以下常見方案允許您驗證林信任是否正確驗證使用者和對資源的訪問：

* [來自 Azure AD DS 資源林的本地使用者身份驗證](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [使用本地使用者訪問 Azure AD DS 資源林中的資源](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [啟用檔案及印表機共用](#enable-file-and-printer-sharing)
    * [創建安全性群組並添加成員](#create-a-security-group-and-add-members)
    * [為跨林訪問創建檔共用](#create-a-file-share-for-cross-forest-access)
    * [驗證對資源的跨林身份驗證](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>來自 Azure AD DS 資源林的本地使用者身份驗證

應將 Windows 伺服器虛擬機器連接到 Azure AD DS 資源網域。 使用此虛擬機器可以測試本地使用者可以在虛擬機器上進行身份驗證。

1. 使用遠端桌面和 Azure AD DS 管理員憑據連接到連接到 Azure AD DS 資源林的 Windows 伺服器 VM。 如果出現網路級身份驗證 （NLA） 錯誤，請檢查所使用的使用者帳戶不是域使用者帳戶。

    > [!NOTE]
    > 要安全地連接到連接到 Azure AD 域服務的 VM，可以在支援的 Azure 區域中使用[Azure 堡壘主機服務](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 打開命令提示並使用 該`whoami`命令顯示當前經過身份驗證的使用者的可分辨名稱：

    ```console
    whoami /fqdn
    ```

1. 使用`runas`命令從本地域進行身份驗證為使用者。 在以下命令中，從`userUpn@trusteddomain.com`受信任的本地域替換為使用者的 UPN。 該命令提示您輸入使用者的密碼：

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 如果身份驗證成功，將打開一個新的命令提示。 新命令提示符的標題包括`running as userUpn@trusteddomain.com`。
1. 在新`whoami /fqdn`命令提示中使用從本地活動目錄查看經過身份驗證的使用者的可分辨名稱。

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>使用本地使用者訪問 Azure AD DS 資源林中的資源

使用加入到 Azure AD DS 資源林的 Windows Server VM，可以測試以下方案：當使用者從本地域中的電腦與本地域中的使用者進行身份驗證時，可以訪問資源林中託管的資源。 以下示例演示如何創建和測試各種常見方案。

#### <a name="enable-file-and-printer-sharing"></a>啟用檔案及印表機共用

1. 使用遠端桌面和 Azure AD DS 管理員憑據連接到連接到 Azure AD DS 資源林的 Windows 伺服器 VM。 如果出現網路級身份驗證 （NLA） 錯誤，請檢查所使用的使用者帳戶不是域使用者帳戶。

    > [!NOTE]
    > 要安全地連接到連接到 Azure AD 域服務的 VM，可以在支援的 Azure 區域中使用[Azure 堡壘主機服務](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 打開**Windows 設置**，然後搜索並選擇**網路和共用中心**。
1. 選擇 **"更改高級共用設置"** 選項。
1. 在 **"域設定檔**"下，選擇 **"打開檔和印表機共用**"，然後**保存更改**。
1. 關閉**網路和共用中心**。

#### <a name="create-a-security-group-and-add-members"></a>創建安全性群組並添加成員

1. 開啟 [Active Directory 使用者和電腦]****。
1. 右選擇功能變數名稱，選擇 **"新建**"，然後選擇 **"組織單位**"。
1. 在名稱框中，鍵入 *"局部物件*"，然後選擇 **"確定**"。
1. 選擇並按右鍵功能窗格中的 **"本機物件**"。 選擇 **"新建**"，然後**組**。
1. 在**組名稱**框中鍵入*檔案伺服器訪問*。 對於**組作用域**，選擇 **"域本地**"，然後選擇 **"確定**"。
1. 在內容窗格中，按兩下**檔案伺服器訪問**。 選擇 **"成員**"，選擇 **"添加**"，然後選擇 **"位置**"。
1. 從**位置**視圖中選擇本地活動目錄，然後選擇 **"確定**"。
1. 在 **"輸入要選擇的物件名稱**"中鍵入*域使用者*。 選擇 **"檢查名稱**"，為本地活動目錄提供憑據，然後選擇 **"確定**"。

    > [!NOTE]
    > 您必須提供憑據，因為信任關係只是一種方式。 這意味著 Azure AD DS 中的使用者無法訪問資源或搜索受信任（本地）域中的使用者或組。

1. 本地活動目錄中的**域使用者**組應是**FileServerAccess**組的成員。 選擇 **"確定"** 以保存組並關閉視窗。

#### <a name="create-a-file-share-for-cross-forest-access"></a>為跨林訪問創建檔共用

1. 在加入 Azure AD DS 資源林的 Windows 伺服器 VM 上，創建資料夾並提供名稱，如*交叉林共用*。
1. 右選擇資料夾並選擇 **"屬性**"。
1. 選擇"**安全**"選項卡，然後選擇 **"編輯**"。
1. 在 *"跨林共用的許可權"對話方塊中*，選擇 **"添加**"。
1. 鍵入*檔案伺服器訪問*輸入**物件名稱以選擇**，然後選擇 **"確定**"。
1. 從**組或使用者名**清單中選擇*檔案伺服器存取權限*。 在 **"檔案伺服器存取權限"** 清單中，選擇 *"允許***修改**和**寫入**許可權"，然後選擇 **"確定**"。
1. 選擇"**共用**"選項卡，然後選擇 **"高級共用..."，"**
1. 選擇 **"共用此資料夾**"，然後在 **"共用共用共用**共用"中為檔共用輸入一個令人難忘的名稱，如*CrossForestShare*。
1. 選擇**許可權**。 在"**所有人的許可權"** 清單中，選擇 **"允許****更改**"許可權。
1. 選擇 **"確定**兩次"，然後**關閉**。

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>驗證對資源的跨林身份驗證

1. 使用本地活動目錄中的使用者帳戶登錄本地活動目錄的 Windows 電腦。
1. 使用**Windows 資源管理器**，連接到使用完全限定的主機名稱和共用（如`\\fs1.aaddscontoso.com\CrossforestShare`）創建的共用。
1. 要驗證寫入權限，請在資料夾中右選擇，選擇 **"新建**"，然後選擇 **"文字文件**"。 使用預設名稱 **"新文字文件**"。

    如果寫入權限設置正確，將創建新的文字文件。 然後，以下步驟將根據需要打開、編輯和刪除檔。
1. 要驗證讀取權限，請打開 **"新文字文件**"。
1. 要驗證修改許可權，請向檔添加文本並關閉**記事本**。 當提示保存更改時，選擇 **"保存**"。
1. 要驗證刪除許可權，請右選擇 **"新建文字文件"** 並選擇 **"刪除**"。 選擇 **"是**"以確認檔刪除。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在本地 AD DS 環境中配置 DNS 以支援 Azure AD DS 連接
> * 在本地 AD DS 環境中創建單向入站林信任
> * 在 Azure AD DS 中創建單向出站林信任
> * 測試和驗證身份驗證和資源訪問的信任關係

有關 Azure AD DS 中的林類型的詳細資訊，請參閱[什麼是資源林？][concepts-forest]和[在 Azure AD DS 中，林信任如何工作？][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
