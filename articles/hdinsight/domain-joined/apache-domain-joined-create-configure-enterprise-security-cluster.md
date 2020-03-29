---
title: 創建、配置企業安全包群集 - Azure
description: 瞭解如何在 Azure HDInsight 中創建和配置企業安全包群集
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 4edafc0c07e967acfabf7fdc5b58c481b2cfccc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436056"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>在 Azure HDInsight 中創建和配置企業安全包群集

Azure HDInsight 的企業安全包 （ESP） 允許您訪問 Azure 中的 Apache Hadoop 群集的基於 Active 目錄的身份驗證、多使用者支援和基於角色的存取控制。 HDInsight ESP 群集使遵守嚴格公司安全性原則的組織能夠安全地處理敏感性資料。

本指南演示如何創建啟用 ESP 的 Azure HDInsight 群集。 它還演示如何創建啟用活動目錄和網域名稱系統 （DNS） 的 Windows IaaS VM。 使用本指南配置必要的資源，允許本地使用者登錄到啟用 ESP 的 HDInsight 群集。

您創建的伺服器將充當*實際*本地環境的替換。 您將使用它進行設置和配置步驟。 稍後，您將在您自己的環境中重複這些步驟。

本指南還將通過使用密碼雜湊與 Azure 活動目錄 （Azure AD） 同步來説明您創建混合標識環境。 本指南補充了[HDInsight 中的 ESP](apache-domain-joined-architecture.md)使用 。

在您自己的環境中使用此過程之前：

* 設置活動目錄和 DNS。
* 啟用 Azure AD。
* 將本地使用者帳戶同步到 Azure AD。

![Azure AD 體系結構圖](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>創建本地環境

在本節中，您將使用 Azure 快速入門部署範本創建新 VM、配置 DNS 以及添加新的 Active Directory 林。

1. 轉到快速入門部署範本，[使用新的活動目錄林創建 Azure VM。](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)

1. 選擇 **"部署到 Azure**"。
1. 登入您的 Azure 訂用帳戶。
1. 在 **"使用新的 AD 林"頁上創建 Azure VM**時，提供以下資訊：

    |屬性 | 值 |
    |---|---|
    |訂用帳戶|選擇要部署資源的訂閱。|
    |資源群組|選擇 **"創建新**"，然後輸入名稱`OnPremADVRG`|
    |Location|選取位置。|
    |管理員使用者名稱|`HDIFabrikamAdmin`|
    |管理員密碼|輸入密碼。|
    |網域名稱|`HDIFabrikam.com`|
    |Dns 首碼|`hdifabrikam`|

    保留其餘預設值。

    ![使用新的 Azure AD 林林創建 Azure VM 的範本](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. 查看**條款及條件**，然後選擇**我同意上述條款及條件**。
1. 選擇 **"購買**"，然後監視部署並等待它完成。 部署大約需要 30 分鐘才能完成。

## <a name="configure-users-and-groups-for-cluster-access"></a>為群集訪問配置使用者和組

在本節中，您將在本指南結束時創建有權訪問 HDInsight 群集的使用者。

1. 使用遠端桌面連線到網域控制站。
    1. 從 Azure 門戶導航到**資源組** > **PremADVRG** > **adVM** > **連接**。
    1. 從**IP 位址**下拉清單中，選擇公共 IP 位址。
    1. 選擇 **"下載 RDP 檔**"，然後打開該檔。
    1. 用作`HDIFabrikam\HDIFabrikamAdmin`使用者名。
    1. 輸入您為管理員帳戶選擇的密碼。
    1. 選取 [確定]****。

1. 從網域控制站**伺服器管理員**儀表板，導航到**工具** > **活動目錄使用者和電腦**。

    ![在伺服器管理員儀表板上，打開活動目錄管理](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 創建兩個新使用者 **：HDIAdmin**和**HDIUser**。 這兩個使用者將登錄到 HDInsight 群集。

    1. 從 **"活動目錄使用者和電腦"** 頁面，右鍵`HDIFabrikam.com`按一下 ，然後導航**到"新** > **使用者**"。

        ![創建新的活動目錄使用者](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. 在新**物件 - 使用者**頁上，輸入`HDIUser`**名字**和**使用者登錄名**。 其他欄位將自動填滿。 然後選擇 **"下一步**"。

        ![創建第一個管理員使用者物件](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. 在顯示的快顯視窗中，輸入新帳戶的密碼。 選擇**密碼永遠不會過期**，然後在彈出消息**確定**。
    1. 選擇 **"下一步**"，然後**完成**以創建新帳戶。
    1. 重複上述步驟以創建使用者`HDIAdmin`。

        ![創建第二個管理員使用者物件](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. 創建全域安全性群組。

    1. 從**活動目錄使用者和電腦**，按右鍵`HDIFabrikam.com`，然後導航到**新** > **組**。

    1. 在`HDIUserGroup`**"組名稱**文本"框中輸入。

    1. 選取 [確定]****。

    ![創建新的活動目錄組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![建立新的物件](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. 將成員添加到**HDIUserGroup**。

    1. 按右鍵**HDIUser**並選擇"**添加到組..."。**
    1. 在 **"輸入物件名稱以選擇**文本"框中，輸入`HDIUserGroup`。 然後在快顯視窗中選擇 **"確定**"，然後再次**選擇"確定**"。
    1. 對**HDIAdmin**帳戶重複上述步驟。

        ![將成員 HDIUser 添加到組 HDIUser 組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

現在，您已經創建了活動目錄環境。 您添加了兩個使用者和一個可以訪問 HDInsight 群集的使用者組。

使用者將與 Azure AD 同步。

### <a name="create-an-azure-ad-directory"></a>建立 Azure AD 目錄

1. 登入 Azure 入口網站。
1. 選擇 **"創建資源和**類型`directory`"。 選擇**Azure 活動目錄** > **創建**。
1. 在 **"組織名稱"** 下，輸入`HDIFabrikam`。
1. 在**初始功能變數名稱**下，輸入`HDIFabrikamoutlook`。
1. 選取 [建立]****。

    ![建立 Azure AD 目錄](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>創建自訂域

1. 從新的**Azure 活動目錄**中，**在"管理**"下，選擇**自訂功能變數名稱**。
1. 選擇 **= 添加自訂域**。
1. 在 **"自訂功能變數名稱**"下`HDIFabrikam.com`，輸入 ，然後選擇 **"添加域**"。
1. 然後完成[將 DNS 資訊添加到域註冊商](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)。

![創建自訂域](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>建立群組

1. 從新的**Azure 活動目錄**中，**在"管理**"下，選擇**組**。
1. 選擇 **= 新組**。
1. 在**組名**文字方塊中，輸入`AAD DC Administrators`。
1. 選取 [建立]****。

## <a name="configure-your-azure-ad-tenant"></a>配置 Azure AD 租戶

現在，您將配置 Azure AD 租戶，以便可以將使用者和組從本地活動目錄實例同步到雲。

創建活動目錄租戶管理員。

1. 登錄到 Azure 門戶並選擇 Azure AD 租戶**HDIFabrikam**。

1. 導航到**管理** > **使用者** > **新使用者**。

1. 為新使用者輸入以下詳細資訊：

    **身份**

    |屬性 |描述 |
    |---|---|
    |[使用者名稱]|在文字方塊中輸入 `fabrikamazureadmin`。 從功能變數名稱下拉清單中，選擇`hdifabrikam.com`|
    |名稱| 輸入 `fabrikamazureadmin`。|

    **密碼**
    1. 選擇 **"讓我創建密碼**"。
    1. 輸入您選擇的安全密碼。

    **群組和角色**
    1. 選擇**選定的 0 個組**。
    1. 選擇**AAD DC 管理員**，然後**選擇**。

    ![Azure AD 組對話方塊](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. 選擇**使用者**。
    1. 選擇**全域管理員**，然後**選擇**。

    ![Azure AD 角色對話方塊](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. 選取 [建立]****。

1. 然後讓新使用者登錄到 Azure 門戶，提示其更改密碼。 在配置 Microsoft Azure 活動目錄連接之前，您需要執行此操作。

## <a name="sync-on-premises-users-to-azure-ad"></a>將本地使用者同步到 Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>配置 Microsoft Azure 活動目錄連接

1. 從網域控制站下載[Microsoft Azure 活動目錄連接](https://www.microsoft.com/download/details.aspx?id=47594)。

1. 打開您下載的可執行檔，並同意授權條款。 選取 **[繼續]**。

1. 選擇 **"使用快速設置**"。

1. 在"**連接到 Azure AD"** 頁上，輸入 Azure AD 全域管理員的使用者名和密碼。 使用配置活動`fabrikamazureadmin@hdifabrikam.com`目錄租戶時創建的使用者名。 然後選擇 **"下一步**"。

    !["連接到 Azure AD"頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. 在 **"連接到活動目錄域服務**"頁上，輸入企業管理員帳戶的使用者名和密碼。 使用您之前`HDIFabrikam\HDIFabrikamAdmin`創建的使用者名及其密碼。 然後選擇 **"下一步**"。

   !["連接到 Azure AD"頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. 在**Azure AD 登錄配置**頁上，選擇 **"下一步**"。
   !["Azure AD 登錄配置"頁](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. 在 **"準備配置"** 頁上，選擇 **"安裝**"。

   !["準備配置"頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. 在 [設定完成]**** 頁面上，選取 [結束]****。
   !["配置完成"頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. 同步完成後，確認您在 IaaS 目錄中創建的使用者已同步到 Azure AD。
   1. 登入 Azure 入口網站。
   1. 選擇**Azure 活動目錄** > **HDIFabrikam** > **使用者**。

### <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

創建使用者分配的託管標識，可用於配置 Azure AD 域服務 （Azure AD DS）。 有關詳細資訊，請參閱使用[Azure 門戶創建、列出、刪除或將角色指派給使用者分配的託管標識](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 登入 Azure 入口網站。
1. 選擇 **"創建資源和**類型`managed identity`"。 選擇**使用者分配的託管標識** > **創建**。
1. 對於**資源名稱**，請輸入`HDIFabrikamManagedIdentity`。
1. 選取您的訂用帳戶。
1. 在 **"資源組**"下，選擇 **"新建"** 並輸入`HDIFabrikam-CentralUS`。
1. 在 **"位置**"下，選擇**美國中部**。
1. 選取 [建立]****。

![創建新的使用者分配的託管標識](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>啟用 Azure AD DS

按照以下步驟啟用 Azure AD DS。 有關詳細資訊，請參閱使用[Azure 門戶啟用 Azure AD DS。](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)

1. 創建虛擬網路以承載 Azure AD DS。 運行以下 PowerShell 代碼。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 登入 Azure 入口網站。
1. 選擇 **"創建資源**"，輸入`Domain services`，然後選擇**Azure AD 域服務** > **創建**。
1. 在**基礎知識**頁上：
    1. 在**目錄名稱**下，選擇您創建的 Azure AD 目錄 **：HDIFabrikam**。
    1. 對於**DNS 功能變數名稱**，請輸入*HDIFabrikam.com*。
    1. 選取您的訂用帳戶。
    1. 指定資源組**HDIFabrikam-中央美國**。 對於**位置**，請選擇**美國中部**。

        ![Azure AD DS 基本詳細資訊](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. 在 **"網路"** 頁上，選擇使用 PowerShell 腳本創建的網路 **（HDIFabrikam-VNET）** 和子**網 （AADDS 子網**）。 或者選擇 **"新建**"以立即創建虛擬網路。

    !["創建虛擬網路"步驟](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. 在 **"管理員"組**頁上，您應該會看到一條通知，指出已創建名為**AAD DC 管理員的**組來管理此組。 如果需要，可以修改此組的成員身份，但在這種情況下，您無需更改它。 選取 [確定]****。

    ![查看 Azure AD 管理員組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. 在 **"同步**"頁上，通過選擇 **"全部** > **確定**"來啟用完全同步。

    ![啟用 Azure AD DS 同步](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. 在 **"摘要"** 頁上，驗證 Azure AD DS 的詳細資訊，然後選擇 **"確定**"。

    !["啟用 Azure AD 域服務"的摘要](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

啟用 Azure AD DS 後，本地 DNS 伺服器在 Azure AD VM 上運行。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>配置 Azure AD DS 虛擬網路

使用以下步驟配置 Azure AD DS 虛擬網路 **（HDIFabrikam-AADDSVNET）** 以使用自訂 DNS 伺服器。

1. 查找自訂 DNS 伺服器的 IP 位址。
    1. 選擇`HDIFabrikam.com`Azure AD DS 資源。
    1. 在 **"管理**"下，選擇**屬性**。
    1. 在**虛擬網路上的 IP 位址**下查找 IP 位址。

    ![查找 Azure AD DS 的自訂 DNS IP 位址](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. 配置**HDIFabrikam-AADDSVNET**以使用自訂 IP 位址 10.0.0.4 和 10.0.0.5。

    1. 在 **"設置"** 下，選擇**DNS 伺服器**。
    1. 選擇**自訂**。
    1. 在文字方塊中，輸入第一個 IP 位址 *（10.0.0.4*）。
    1. 選取 [儲存]****。
    1. 重複這些步驟以添加其他 IP 位址 *（10.0.0.5*）。

在我們的方案中，我們將 Azure AD DS 配置為使用 IP 位址 10.0.0.4 和 10.0.0.5，在 Azure AD DS 虛擬網路上設置相同的 IP 位址：

![自訂 DNS 伺服器頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>保護 LDAP 流量

羽量級目錄訪問協定 （LDAP） 用於讀取和寫入 Azure 活動目錄。 通過使用安全通訊端層 （SSL） 或傳輸層安全 （TLS） 技術，可以使 LDAP 流量保密和安全。 您可以通過安裝格式正確的證書通過 SSL （LDAPS） 啟用 LDAP。

有關安全 LDAP 的詳細資訊，請參閱[為 Azure AD DS 託管域配置 LDAPS。](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)

在本節中，您將創建自簽章憑證、下載證書並為**HDIFabrikam** Azure AD DS 託管域配置 LDAPS。

以下腳本為**HDIFabrikam**創建證書。 證書保存在*本地電腦*路徑中。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> 任何創建有效的公開金鑰加密標準 （PKCS） \#10 請求的實用程式或應用程式都可用於形成 SSL 憑證請求。

驗證憑證是否安裝在電腦**的個人**存儲中：

1. 啟動微軟管理主控台 （MMC）。
1. 添加在本地電腦上管理證書的**證書**管理單元。
1. 擴展**證書（本地電腦）** > **個人** > **證書**。 **個人**存儲中應存在新證書。 此憑證發行給完全限定的主機名稱。

    ![驗證本地證書創建](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. 在右側的窗格中，按右鍵您創建的證書。 指向**所有任務**，然後選擇 **"匯出**"。

1. 在 **"匯出私密金鑰**"頁上，選擇 **"是"，匯出私密金鑰**。 將導入金鑰的電腦需要私密金鑰來讀取加密的消息。

    ![證書匯出嚮導的匯出私密金鑰頁](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. 在 **"匯出檔案格式"** 頁上，保留預設設置，然後選擇 **"下一步**"。
1. 在 **"密碼"** 頁上，鍵入私密金鑰的密碼。 對於**加密**，請選擇**三重DES-SHA1**。 然後選擇 **"下一步**"。
1. 在"**檔匯出"** 頁上，鍵入匯出的證書檔的路徑和名稱，然後選擇 **"下一步**"。 檔案名必須具有 .pfx 副檔名。 此檔在 Azure 門戶中配置為建立安全連線。
1. 為 Azure AD DS 託管域啟用 LDAPS。
    1. 在 Azure 門戶中，選擇`HDIFabrikam.com`域 。
    1. 在 **"管理**"下，選擇 **"安全 LDAP"。**
    1. 在 **"安全 LDAP"** 頁上，**在"安全 LDAP**"下，選擇**啟用**。
    1. 流覽您在電腦上匯出的 .pfx 證書檔。
    1. 輸入證書密碼。

    ![啟用安全 LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. 現在，您已經啟用了 LDAPS，通過啟用埠 636 確保可以訪問的。
    1. 在**HDIFabrikam-CentralUS**資源組中，選擇網路安全性群組**AADDS-HDIFabrikam.com-NSG**。
    1. 在 **"設置"** 下，選擇 **"入站安全規則** > **添加**"。
    1. 在 **"添加入站安全規則"** 頁上，輸入以下屬性，然後選擇 **"添加**：

        | 屬性 | 值 |
        |---|---|
        | 來源 | 任意 |
        | 來源連接埠範圍 | * |
        | Destination | 任意 |
        | 目標埠範圍 | 636 |
        | 通訊協定 | 任意 |
        | 動作 | Allow |
        | 優先順序 | \<所需數位> |
        | 名稱 | Port_LDAP_636 |

    !["添加入站安全規則"對話方塊](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikam 託管標識**是使用者分配的託管標識。 為託管標識啟用 HDInsight 域服務參與者角色，該標識將允許讀取、創建、修改和刪除域服務操作。

![建立使用者指派的受控識別](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>創建啟用 ESP 的 HDInsight 群集

此步驟需要以下先決條件：

1. **在美國西部**的地方創建新的資源組*HDIFabrikam-WestUS* 。
1. 創建將承載支援 ESP 的 HDInsight 群集的虛擬網路。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 在承載 Azure AD DS （`HDIFabrikam-AADDSVNET`） 的虛擬網路與將承載啟用 ESP 的 HDInsight 群集`HDIFabrikam-HDIVNet`（） 的虛擬網路之間創建對等關係。 使用以下 PowerShell 代碼對兩個虛擬網路進行對等。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 創建新的 Azure 資料存儲湖存儲 Gen2 帳戶，稱為**Hdigen2 存儲**。 使用使用者託管的身份**HDIFabrikam 管理標識**配置帳戶。 有關詳細資訊，請參閱將[Azure 資料存儲湖存儲第 2 代與 Azure HDInsight 群集結合](../hdinsight-hadoop-use-data-lake-storage-gen2.md)使用。

1. 在**HDIFabrikam-AADDSVNET**虛擬網路上設置自訂 DNS。
    1. 轉到 Azure 門戶>**資源組** > **，在 PremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS 伺服器**上。
    1. 選擇 **"自訂"** 並輸入*10.0.0.4*和*10.0.0.5*。
    1. 選取 [儲存]****。

        ![為虛擬網路保存自訂 DNS 設置](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. 創建新的啟用 ESP 的 HDInsight Spark 群集。
    1. 選擇 **"自訂"（大小、設置、應用）。**
    1. 輸入**基礎知識**的詳細資訊（第 1 節）。 確保**群集類型**為**Spark 2.3 （HDI 3.6）。** 確保**資源組**為**HDIFabrikam-中央美國**。

    1. 對於**安全與網路**（第 2 節），請填寫以下詳細資訊：
        * 在 **"企業安全包**"下，選擇 **"已啟用**"。
        * 選擇**群集管理員使用者**並選擇您創建的本地管理員使用者的**HDIAdmin**帳戶。 按一下 **"選擇**"。
        * 選擇**群集訪問組** > **HDIUser 組**。 將來添加到此組的任何使用者都將能夠訪問 HDInsight 群集。

            ![選擇群集訪問組 HDIUser 組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. 完成群集配置的其他步驟，並驗證**群集摘要**上的詳細資訊。 選取 [建立]****。

1. 登錄到 在 中`https://CLUSTERNAME.azurehdinsight.net`新創建的群集的 Ambari UI。 使用管理員使用者名`hdiadmin@hdifabrikam.com`及其密碼。

    ![阿帕奇 Ambari UI 登錄視窗](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. 在群集儀表板中，選擇 **"角色**"。
1. 在 **"角色"** 頁上，**在"將角色指派給這些**角色"下，在**群集管理員**角色旁邊輸入組*hdiuser 組*。 

    ![將群集管理員角色分配給 hdiuser 組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. 打開安全外殼 （SSH） 用戶端並登錄到群集。 使用您在本地活動目錄實例中創建的**hdiuser。**

    ![使用 SSH 用戶端登錄到群集](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

如果可以使用此帳戶登錄，則已正確配置 ESP 群集以與本地活動目錄實例同步。

## <a name="next-steps"></a>後續步驟

閱讀[一本關於使用 ESP 的 Apache Hadoop 安全性的介紹](hdinsight-security-overview.md)。
