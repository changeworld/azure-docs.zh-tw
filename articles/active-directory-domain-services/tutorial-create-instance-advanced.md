---
title: 教學課程 - 建立 Azure Active Directory Domain Services 受控網域 | Microsoft Docs
description: 在本教學課程中，您將了解如何使用 Azure 入口網站建立和設定 Azure Active Directory Domain Services 受控網域並指定進階設定選項。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 78eef9c84bb7610b067855b22a3fa0f51bf08253
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024786"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain-with-advanced-configuration-options"></a>教學課程：使用進階設定選項建立並設定 Azure Active Directory Domain Services 受控網域

Azure Active Directory Domain Services (Azure AD DS) 提供受控網域服務，例如：網域加入、群組原則、LDAP、Kerberos/NTLM 驗證，與 Windows Server Active Directory 完全相容。 您不需要自行部署、管理和修補網域控制站，就可以使用這些網域服務。 Azure AD DS 會與您現有的 Azure AD 租用戶整合。 這項整合可讓使用者使用其公司認證進行登入，而您可以使用現有的群組和使用者帳戶來保護資源的存取。

您可以[使用預設的設定選項建立受控網域][tutorial-create-instance]，以進行網路連線和同步，或手動定義這些設定。 本教學課程說明如何使用 Azure 入口網站定義這些進階設定選項，以建立和設定 Azure AD DS 受控網域。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定受控網域的 DNS 和虛擬網路設定
> * 建立受控網域
> * 將系統管理使用者新增至網域管理
> * 啟用密碼雜湊同步處理

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 您必須擁有 Azure AD 租用戶的*全域管理員*權限，才能啟用 Azure AD DS。
* 您需要 Azure 訂用帳戶中的「參與者」權限，才能建立必要的 Azure AD DS 資源。

雖然 Azure AD DS 不需要，但建議針對 Azure AD 租用戶[設定自助式密碼重設 (SSPR)][configure-sspr]。 使用者不需 SSPR 即可變更其密碼，但是如果他們忘記密碼且需要加以重設，SSPR 會協助他們。

> [!IMPORTANT]
> 建立受控網域之後，您就無法將受控網域移至不同的資源群組、虛擬網路、訂用帳戶等項目。當您部署受控網域時，請仔細選取最適當的訂用帳戶、資源群組、區域和虛擬網路。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在本教學課程中，您會使用 Azure 入口網站來建立和設定受控網域。 若要開始使用，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-managed-domain-and-configure-basic-settings"></a>建立受控網域並設定基本設定

若要啟動 [啟用 Azure AD Domain Services] 精靈，請完成下列步驟：

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]。
1. 在搜尋列中輸入 [Domain Services]，然後從搜尋建議中選擇 [Azure AD Domain Services]。
1. 在 [Azure AD Domain Services] 頁面中，選取 [建立]。 [啟用 Azure AD Domain Services] 精靈隨即啟動。
1. 選取您要在其中建立受控網域的 Azure **訂用帳戶**。
1. 選取受控網域應該隸屬的**資源群組**。 選擇 [新建]，或選取現有的資源群組。

在建立受控網域時，您可以指定 DNS 名稱。 以下是選擇此 DNS 名稱時的一些考量：

* **內建網域名稱：** 預設會使用目錄的內建網域名稱 (具有 .onmicrosoft.com 尾碼)。 如果您想要啟用透過網際網路對受控網域進行安全 LDAP 存取，則無法建立數位憑證來保護對此預設網域建立的連線。 .onmicrosoft.com 網域屬於 Microsoft，因此憑證授權單位不會發行憑證。
* **自訂網域名稱：** 最常見的方法是指定自訂網域名稱，通常是您已擁有且可路由的名稱。 當您使用可路由的自訂網域時，流量就可以在應用程式需要支援時，流向正確的位置。
* **非路由式網域尾碼：** 我們通常會建議您避免使用無法路由的網域名稱尾碼，例如 contoso. local。 .local 尾碼是不可路由的，因此會導致 DNS 解析發生問題。

> [!TIP]
> 如果您建立自訂網域名稱，請注意現有的 DNS 命名空間。 建議使用與任何現有 Azure 或內部部署 DNS 命名空間不同的功能變數名稱。
>
> 例如，如果您現有的 DNS 名稱空間是 contoso.com，請使用 corp.contoso.com 自訂網域名稱建立受控網域。 如果您需要使用安全 LDAP，必須註冊並擁有此自訂功能網域名稱，才能產生必要的憑證。
>
> 建議您為環境中的其他服務，或環境中現有 DNS 命名空間之間的條件式 DNS 轉寄站，建立一些額外的 DNS 記錄。 例如，如果您使用根 DNS 名稱執行裝載網站的 Web 伺服器，可能會發生需要其他 DNS 項目的命名衝突。
>
> 在這些教學課程和操作說明文章中，會使用 aadds.contoso.com 的自訂網域作為簡短的範例。 在所有命令中，指定您自己的網域名稱。

下列 DNS 名稱限制也適用於此：

* **網域前置詞限制：** 您無法使用超過 15 個字元的前置詞來建立受控網域。 您指定的網域名稱的前置詞字元數 (例如，aaddscontoso.com 網域名稱中的 aaddscontoso) 必須少於 15 個字元。
* **網路名稱衝突：** 受控網域的 DNS 網域名稱不應已存在於虛擬網路中。 具體而言，請檢閱下列會導致名稱衝突的案例：
    * Azure 虛擬網路上是否已有包含相同 DNS 網域名稱的 Active Directory 網域。
    * 您打算啟用受控網域的虛擬網路是否與內部部署網路建立 VPN 連線。 在此案例中，確定您在內部部署網路上沒有使用相同 DNS 網域名稱的網域。
    * 您在 Azure 虛擬網路上是否已有使用該名稱的 Azure 雲端服務。

在 Azure 入口網站中完成 [基本] 視窗中的欄位，以建立受控網域：

1. 輸入受控網域的 **DNS 網域名稱**，並將前面幾項列入考慮。
1. 選擇應該在其中建立受控網域的 Azure**位置**。 如果您選擇支援可用性區域的區域，Azure AD DS 資源會跨區域分散，以供額外的備援。

    > [!TIP]
    > 「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原，所有已啟用的地區中至少要有三個不同的區域。
    >
    > 您不需要針對要跨區域分散的 Azure AD DS 進行設定。 Azure 平台會自動處理在區域之間分散資源。 如需詳細資訊及查看區域可用性，請參閱[什麼是 Azure 中的可用性區域？][availability-zones]

1. **SKU** 將決定效能、備份頻率，以及您可以建立的樹系信任數目上限。 如果您的商務需求或要求條件有所變更，您可以在受控網域建立後變更 SKU。 如需詳細資訊，請參閱 [Azure AD DS SKU 概念][concepts-sku]。

    在本教學課程中，請選取*標準* SKU。
1. *樹系*是 Active Directory Domain Services 用來將一或多個網域分組的邏輯建構。 根據預設，受控網域會建立為*使用者*樹系。 這種類型的樹系會同步 Azure AD 中的所有物件，包括在內部部署 AD DS 環境中建立的任何使用者帳戶。

    *資源*樹系只會同步直接在 Azure AD 中建立的使用者和群組。 資源樹系目前為預覽狀態。 如需*資源*樹系的詳細資訊，包括您使用某一樹系的原因，以及如何建立與內部部署 AD DS 網域之間的樹系信任，請參閱 [Azure AD DS 資源樹系概觀][resource-forests]。

    在本教學課程中，請選擇建立*使用者*樹系。

    ![設定 Azure AD Domain Services 受控網域的基本設定](./media/tutorial-create-instance-advanced/basics-window.png)

1. 若要手動設定其他選項，請選擇 [下一步 - 網路]。 否則，請選取 [檢閱 + 建立] 以接受預設的設定選項，然後跳到[部署您受控網域](#deploy-the-managed-domain)的區段。 當您選擇此建立選項時，系統會設定下列預設值：

    * 建立使用 10.0.1.0/24 IP 位址範圍且名為 aadds-vnet 的虛擬網路。
    * 建立使用 10.0.1.0/24 IP 位址範圍且名為 aadds-subnet 的子網路。
    * 將 Azure AD 中的「所有」使用者同步至受控網域。

## <a name="create-and-configure-the-virtual-network"></a>建立和設定虛擬網路

若要提供連線，需要有 Azure 虛擬網路和專用子網路。 此虛擬網路的子網路中已啟用 Azure AD DS。 您會在本教學課程中建立虛擬網路，但您也可以改為選擇使用現有虛擬網路。 不論是哪一種方法，您都必須建立專用的子網路，以供 Azure AD DS 使用。

此專用虛擬網路子網路的一些考量包括下列幾方面：

* 子網路的位址範圍中必須至少有 3-5 個可用的 IP 位址，才能支援 Azure AD DS 資源。
* 請勿選取「閘道」子網路來部署 Azure AD DS。 我們不支援將 Azure AD DS 部署到「閘道」子網路。
* 不要將任何其他虛擬機器部署到子網路。 應用程式和 VM 通常會使用網路安全性群組來保護連線能力。 若在個別子網路中執行這些工作負載，您不用中斷與受控網域的連線，就能套用這些網路安全性群組。
* 啟用 Azure AD DS 後，無法將受控網域移至不同的虛擬網路。

如需如何規劃和設定虛擬網路的詳細資訊，請參閱 [Azure Active Directory Domain Services 的網路考量][network-considerations]。

完成 [網路] 視窗中的欄位，如下所示：

1. 在 [網路] 頁面上，從下拉式功能表中選擇要用來部署 Azure AD DS 的虛擬網路，或選取 [建立新的虛擬網路]。
    1. 如果您選擇建立虛擬網路，請輸入虛擬網路的名稱 (例如 myVnet)，然後提供位址範圍，例如 10.0.1.0/24。
    1. 建立具有明確名稱的專用子網路，例如 DomainServices。 提供位址範圍，例如 10.0.1.0/24。

    [![](./media/tutorial-create-instance-advanced/create-vnet.png "Create a virtual network and subnet for use with Azure AD Domain Services")](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    請務必挑選私人 IP 位址範圍內的位址範圍。 不是您所擁有的 IP 位址範圍位於公用位址空間中會導致 Azure AD DS 內發生錯誤。

1. 選取虛擬網路子網路，例如 DomainServices。
1. 一切就緒後，選擇 [下一步 - 系統管理]。

## <a name="configure-an-administrative-group"></a>設定系統管理群組

名為「AAD DC 系統管理員」的特殊系統管理群組會用來管理 Azure AD DS 網域。 此群組的成員會獲得 VM 的系統管理權限，而這類 VM 已加入受控網域。 在加入網域的 VM 上，這個群組會新增到本機系統管理員群組。 此群組的成員也可以使用遠端桌面，從遠端連線到已加入網域的 VM。

> [!IMPORTANT]
> 在使用 Azure AD DS 的受控網域上，您不會有「網域系統管理員」或「企業系統管理員」權限。 服務會保留這些權限，而且不會提供給租用戶中的使用者使用。
>
> 相反地，「AAD DC 系統管理員」群組可讓您執行一些使用特殊權限的作業。 這些作業包括隸屬於 VM (已加入網域) 上的管理群組，以及設定群組原則。

精靈會在 Azure AD 目錄中自動建立「AAD DC 系統管理員」群組。 如果在 Azure AD 目錄中已有此名稱的現有群組存在，精靈會選取此群組。 在部署過程中，您可以選擇將其他使用者新增至此「AAD DC 系統管理員」群組。 這些步驟會在稍後完成。

1. 若要將其他使用者新增至此「AAD DC 系統管理員」，請選取 [管理群組成員資格]。

    ![設定「AAD DC 系統管理員」群組的群組成員資格](./media/tutorial-create-instance-advanced/admin-group.png)

1. 選取 [新增成員] 按鈕，然後搜尋並選取 Azure AD 目錄中的使用者。 例如，搜尋您自己的帳戶，並將其新增至「AAD DC 系統管理員」群組。
1. 如有需要，請變更或新增收件者，以在受控網域中有警示且需要注意時收到通知。
1. 一切就緒後，選擇 [下一步 - 同步]。

## <a name="configure-synchronization"></a>設定同步處理

Azure AD DS 可讓您同步 Azure AD 中的「所有」使用者和群組，或僅對特定群組進行「限域」同步。 如果您選擇同步「所有」使用者和群組，您之後就無法選擇只執行限域同步。 如需有關限域同步的詳細資訊，請參閱 [Azure AD Domain Services 的限域同步][scoped-sync]。

1. 在本教學課程中，請選擇同步「所有」使用者和群組。 此同步選項是預設選項。

    ![從 Azure AD 執行使用者和群組的完整同步](./media/tutorial-create-instance-advanced/sync-all.png)

1. 選取 [檢閱 + 建立]。

## <a name="deploy-the-managed-domain"></a>部署受控網域

在精靈的 [摘要] 頁面上，檢閱受控網域的組態設定。 您可以返回精靈的任何步驟以進行變更。 若要使用這些設定選項以一致的方式將受控網域重新部署至不同的 Azure AD 租用戶，您也可以**下載用於自動化的範本**。

1. 若要建立受控網域，請選取 [建立]。 系統會顯示注意事項，告知您某些設定選項 (例如 DNS 名稱或虛擬網路) 在 Azure AD DS 受控網域建立之後就無法變更。 若要繼續，請選取 [確定]。
1. 佈建受控網域的程序可能需要一小時的時間。 您會在入口網站中看到顯示 Azure AD DS 部署進度的通知。 選取通知以查看部署的詳細進度。

    ![Azure 入口網站中的部署進度通知](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. 選取您的資源群組 (例如 myResourceGroup)，然後從 Azure 資源清單中選擇您的受控網域，例如 aaddscontoso.com。 [概觀] 索引標籤會顯示受控網域目前「正在進行部署」。 完整佈建之前，您無法設定受控網域。

    ![佈建狀態期間的 Domain Services 狀態](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. 當受控網域完整佈建時，[概觀] 索引標籤會將網域狀態顯示為 [執行中]。

    ![成功布建後的 Domain Services 狀態](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

> [!IMPORTANT]
> 受控網域與 Azure AD 租用戶相關聯。 在佈建程序中，Azure AD DS 會在 Azure AD 租用戶中建立名為 *Domain Controller Services* 與 *AzureActiveDirectoryDomainControllerServices* 的兩個企業應用程式。 處理受控網域時需要這些企業應用程式。 不要刪除這些應用程式。

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>更新 Azure 虛擬網路的 DNS 設定

成功部署 Azure AD DS 之後，現在請將虛擬網路設定為允許其他已連線的 VM 和應用程式使用受控網域。 若要提供此連線，請更新虛擬網路的 DNS 伺服器設定，使其指向其中部署受控網域的兩個 IP 位址。

1. 受控網域的 [概觀] 索引標籤會顯示一些**必要的設定步驟**。 第一個設定步驟是更新虛擬網路的 DNS 伺服器設定。 正確設定 DNS 的設定之後，就不會再顯示此步驟。

    列出的位址是要在虛擬網路中使用的網域控制站。 在此範例中，這些位址是 *10.0.1.4* 和 *10.0.1.5*。 您稍後可以在 [屬性] 索引標籤上找到這些 IP 位址。

    ![使用 Azure AD Domain Services IP 位址來設定虛擬網路的 DNS 設定](./media/tutorial-create-instance-advanced/configure-dns.png)

1. 若要更新虛擬網路的 DNS 伺服器設定，請選取 [設定] 按鈕。 系統會自動為您的虛擬網路設定 DNS 設定。

> [!TIP]
> 如果您在先前步驟中選取現有的虛擬網路，則任何連線到網路的 VM 都只會在重新開機後取得新的 DNS 設定。 您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來重新啟動 VM。

## <a name="enable-user-accounts-for-azure-ad-ds"></a>啟用 Azure AD DS 的使用者帳戶

若要在受控網域上驗證使用者，Azure AD DS 需要其格式適用 NT LAN Manager (NTLM) 和 Kerberos 驗證的密碼雜湊。 除非您針對租用戶啟用 Azure AD DS，否則 Azure AD 不會以 NTLM 或 Kerberos 驗證所需的格式產生或儲存密碼雜湊。 基於安全性考量，Azure AD 也不會儲存任何純文字形式的密碼認證。 因此，Azure AD 無法根據使用者的現有認證自動產生這些 NTLM 或 Kerberos 密碼雜湊。

> [!NOTE]
> 正確設定後，可用的密碼雜湊就會儲存在受控網域中。 如果您刪除受控網域，當時已儲存的任何密碼雜湊也會一併刪除。
>
> 如果您後續建立受控網域，Azure AD 中已同步的認證資訊將無法重複使用 - 您必須重新設定密碼雜湊同步，以再次儲存密碼雜湊。 先前加入網域的 VM 或使用者將無法立即進行驗證 - Azure AD 需要在新的受控網域中產生密碼雜湊並儲存。
>
> 如需詳細資訊，請參閱 [Azure AD DS 和 Azure AD Connect 的密碼雜湊同步程序][password-hash-sync-process]。

在 Azure AD 中建立的僅限雲端使用者帳戶，與使用 Azure AD Connect 從內部部署目錄同步的使用者帳戶，其用於生產及儲存這些密碼雜湊的步驟不同。

僅限雲端使用者帳戶是您使用 Azure 入口網站或 Azure AD PowerShell Cmdlet 在 Azure AD 目錄中建立的帳戶。 些使用者帳戶不是從內部部署目錄進行同步。

在本教學課程中，讓我們使用基本的僅限雲端使用者帳戶。 如需使用 Azure AD Connect 所需的其他步驟詳細資訊，請參閱[同步從內部部署 AD 同步至受控網域的使用者帳戶密碼雜湊][on-prem-sync]。

> [!TIP]
> 如果您的 Azure AD 租用戶同時具有僅限雲端使用者以及來自您內部部署 AD 的使用者，則需要完成這兩組步驟。

對於僅限雲端的使用者帳戶，使用者必須變更其密碼，才能使用 Azure AD DS。 此密碼變更程序會在 Azure AD 中產生並儲存 Kerberos 和 NTLM 驗證的密碼雜湊。 在密碼變更前，帳戶不會從 Azure AD 同步處理到 Azure AD DS。 針對租用戶中所有需要使用 Azure AD DS 的雲端使用者來使其密碼過期 (這會在下一次登入時強制變更密碼)，或者指示雲端使用者手動變更其密碼。 在本教學課程中，我們將手動變更使用者密碼。

Azure AD 租用戶必須先[設定為可進行自助式密碼重設][configure-sspr]，才能讓使用者重設他們的密碼。

若要變更僅限雲端使用者的密碼，使用者必須完成下列步驟：

1. 前往 Azure AD 存取面板頁面：[https://myapps.microsoft.com](https://myapps.microsoft.com)。
1. 在右上角選取您的名稱，然後從下拉式功能表中選擇 [設定檔]。

    ![選取設定檔](./media/tutorial-create-instance-advanced/select-profile.png)

1. 在 [設定檔] 頁面上，選取 [變更密碼]。
1. 在 [變更密碼] 頁面上，輸入您現有 (舊的) 密碼，然後輸入新密碼並加以確認。
1. 選取 [提交]。

變更密碼之後，需要幾分鐘的時間，新密碼才能在 Azure AD DS 中使用，以及成功登入已加入受控網域的電腦。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定受控網域的 DNS 和虛擬網路設定
> * 建立受控網域
> * 將系統管理使用者新增至網域管理
> * 啟用 Azure AD DS 的使用者帳戶並產生密碼雜湊

若要查看此作用中的受控網域，請建立虛擬機器並將其加入網域。

> [!div class="nextstepaction"]
> [將 Windows Server 虛擬機器加入受控網域](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
