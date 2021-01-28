---
title: 建立、設定企業安全性套件叢集-Azure
description: 瞭解如何在 Azure HDInsight 中建立及設定企業安全性套件叢集
services: hdinsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/10/2019
ms.openlocfilehash: 914acfab3935bc81e7d8382163ca9283c7f71a53
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934035"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>在 Azure HDInsight 中建立及設定企業安全性套件叢集

適用于 Azure HDInsight 的企業安全性套件 (ESP) 可讓您存取 Azure 中 Apache Hadoop 叢集的 Active Directory 型驗證、多使用者支援和角色型存取控制。 HDInsight ESP 叢集可讓符合嚴格公司安全性原則的組織安全地處理敏感性資料。

本指南說明如何建立啟用 ESP 的 Azure HDInsight 叢集。 它也會示範如何建立 Windows IaaS VM，以在其上啟用 Active Directory 和網域名稱系統 (DNS) 。 您可以使用本指南來設定必要的資源，以允許內部部署使用者登入已啟用 ESP 的 HDInsight 叢集。

您所建立的伺服器將作為 *實際* 內部部署環境的取代。 您將使用它來進行設定和設定步驟。 稍後您會在自己的環境中重複這些步驟。

本指南也將協助您使用密碼雜湊同步處理搭配 Azure Active Directory (Azure AD) 來建立混合式身分識別環境。 本指南補充了 [在 HDInsight 中使用 ESP](apache-domain-joined-architecture.md)的功能。

在您自己的環境中使用此進程之前：

* 設定 Active Directory 和 DNS。
* 啟用 Azure AD。
* 將內部部署使用者帳戶同步至 Azure AD。

![Azure AD 架構圖表](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>建立內部部署環境

在本節中，您將使用 Azure 快速入門部署範本來建立新的 Vm、設定 DNS，並新增 Active Directory 樹系。

1. 移至快速入門部署範本，以 [使用新的 Active Directory 樹系建立 AZURE VM](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)。

1. 選取 [ **部署至 Azure**]。
1. 登入 Azure 訂用帳戶。
1. 在 [ **使用新的 AD 樹系建立 AZURE VM** ] 頁面上，提供下列資訊：

    |屬性 | 值 |
    |---|---|
    |訂用帳戶|選取您要部署資源的訂用帳戶。|
    |資源群組|選取 [ **建立新** 的]，然後輸入名稱 `OnPremADVRG`|
    |Location|選取位置。|
    |管理員使用者名稱|`HDIFabrikamAdmin`|
    |管理員密碼|輸入密碼。|
    |網域名稱|`HDIFabrikam.com`|
    |Dns 首碼|`hdifabrikam`|

    保留其餘預設值。

    ![用來建立具有新 Azure AD 樹系之 Azure VM 的範本](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. 檢查 **條款及條件**，然後選取 [ **我同意上方所述的條款及條件**]。
1. 選取 [ **購買**]，並監視部署並等候它完成。 部署需要大約30分鐘才能完成。

## <a name="configure-users-and-groups-for-cluster-access"></a>設定用於叢集存取的使用者和群組

在本節中，您將會在本指南結束時建立可存取 HDInsight 叢集的使用者。

1. 使用遠端桌面連線到網域控制站。
    1. 在 Azure 入口網站中，流覽至 [**資源群組**  >  ]**OnPremADVRG**  >  **adVM**  >  **Connect]**。
    1. 從 [ **IP 位址** ] 下拉式清單中，選取公用 IP 位址。
    1. 選取 [ **下載 RDP** 檔案]，然後開啟檔案。
    1. 使用 `HDIFabrikam\HDIFabrikamAdmin` 做為使用者名稱。
    1. 輸入您為系統管理員帳戶選擇的密碼。
    1. 選取 [確定]。

1. 從網域控制站 **伺服器管理員** 儀表板，流覽至 [**工具**]  >  **Active Directory 消費者和電腦**。

    ![在伺服器管理員儀表板上，開啟 [Active Directory 管理]](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. 建立兩個新使用者： **HDIAdmin** 和 **HDIUser**。 這兩個使用者將會登入 HDInsight 叢集。

    1. 在 [ **Active Directory 消費者和電腦**] 頁面中，以滑鼠右鍵按一下 `HDIFabrikam.com` ，然後流覽至 [**新增**  >  **使用者**]。

        ![建立新的 Active Directory 使用者](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. 在 [ **新增物件-使用者** ] 頁面上，輸入 `HDIUser` **名字** 和 **使用者登入名稱**。 其他欄位將會自動填入。 然後選取 [下一步]。

        ![建立第一個系統管理使用者物件](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. 在出現的快顯視窗中，輸入新帳戶的密碼。 選取 [ **密碼永久** 有效]，然後在快顯訊息中選取 **[確定]** 。
    1. 選取 **[下一步**]，然後 **完成** 建立新的帳戶。
    1. 重複上述步驟以建立使用者 `HDIAdmin` 。

        ![建立第二個系統管理使用者物件](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. 建立全域安全性群組。

    1. 從 **Active Directory 消費者和電腦** 中，以滑鼠右鍵按一下 `HDIFabrikam.com` ，然後流覽至 [**新增**  >  **群組**]。

    1. `HDIUserGroup`在 [**組名**] 文字方塊中輸入。

    1. 選取 [確定]。

    ![建立新的 Active Directory 群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![建立新的物件](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. 將成員新增至 **HDIUserGroup**。

    1. 以滑鼠右鍵按一下 **HDIUser** ，然後選取 [ **加入群組**]。
    1. 在 [ **輸入要選取的物件名稱** ] 文字方塊中，輸入 `HDIUserGroup` 。 然後選取 **[確定]**，然後在快顯視窗中再次 **確定** 。
    1. 針對 **HDIAdmin** 帳戶重複上述步驟。

        ![將成員 HDIUser 新增至群組 HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

您現在已建立 Active Directory 環境。 您已新增可存取 HDInsight 叢集的兩個使用者和使用者群組。

使用者會與 Azure AD 同步處理。

### <a name="create-an-azure-ad-directory"></a>建立 Azure AD 目錄

1. 登入 Azure 入口網站。
1. 選取 [ **建立資源** ] 和 [類型] `directory` 。 選取 **Azure Active Directory**  >  **建立**]。
1. 在 [ **組織名稱**] 下，輸入 `HDIFabrikam` 。
1. 在 [ **初始功能變數名稱**] 下，輸入 `HDIFabrikamoutlook` 。
1. 選取 [建立]  。

    ![建立 Azure AD 目錄](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>建立自訂網域

1. 從新的 **Azure Active Directory** 的 [ **管理**] 底下，選取 [ **自訂功能變數名稱**]。
1. 選取 [+ 新增自訂網域]。
1. 在 [ **自訂功能變數名稱**] 下，輸入 `HDIFabrikam.com` ，然後選取 [ **新增網域**]。
1. 然後完成 [將您的 DNS 資訊新增至網域註冊機構](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)。

![建立自訂網域](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>建立群組

1. 從新的 **Azure Active Directory** 的 [ **管理**] 底下，選取 [ **群組**]。
1. 選取 [ **+ 新增群組**]。
1. 在 [ **組名** ] 文字方塊中，輸入 `AAD DC Administrators` 。
1. 選取 [建立]  。

## <a name="configure-your-azure-ad-tenant"></a>設定您的 Azure AD 租使用者

現在您將設定 Azure AD 租使用者，讓您可以將內部部署 Active Directory 實例的使用者和群組同步至雲端。

建立 Active Directory 租使用者系統管理員。

1. 登入 Azure 入口網站，然後選取您的 Azure AD 租使用者 **HDIFabrikam**。

1. 流覽以 **管理**  >  **使用者**  >  的 **新使用者**。

1. 輸入新使用者的下列詳細資料：

    **身分識別**

    |屬性 |描述 |
    |---|---|
    |使用者名稱|在文字方塊中輸入 `fabrikamazureadmin`。 從 [功能變數名稱] 下拉式清單中，選取 `hdifabrikam.com`|
    |名稱| 輸入 `fabrikamazureadmin`。|

    **密碼**
    1. 選取 **[讓我建立密碼**]。
    1. 輸入您選擇的安全密碼。

    **群組和角色**
    1. 選取 [已 **選取0個群組**]。
    1. 選取 [ **AAD DC 系統管理員**]，然後 **選取**[]。

    ![Azure AD 群組] 對話方塊](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. 選取 [ **使用者**]。
    1. 選取 [ **全域管理員**]，然後 **選取**[]。

    ![[Azure AD 角色] 對話方塊](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. 選取 [建立]  。

1. 然後讓新的使用者登入 Azure 入口網站會提示您變更密碼。 您必須先完成此動作，才能設定 Microsoft Azure Active Directory Connect。

## <a name="sync-on-premises-users-to-azure-ad"></a>將內部部署使用者同步至 Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>設定 Microsoft Azure Active Directory Connect

1. 從網域控制站下載 [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

1. 開啟您所下載的可執行檔，並同意授權條款。 選取 [繼續]。

1. 選取 [ **使用快速設定**]。

1. 在 [ **連接到 Azure AD]** 頁面上，輸入 Azure AD 全域管理員的使用者名稱和密碼。 使用您在 `fabrikamazureadmin@hdifabrikam.com` 設定 Active Directory 租使用者時所建立的使用者名稱。 然後選取 [下一步]。

    ![[連接到 Azure A D] 頁面。](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. 在 [ **連接到 Active Directory Domain Services]** 頁面上，輸入企業系統管理員帳戶的使用者名稱和密碼。 使用您稍 `HDIFabrikam\HDIFabrikamAdmin` 早建立的使用者名稱和密碼。 然後選取 [下一步]。

   ![[連接到 D D S] 頁面。](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. 在 [ **Azure AD 登入** 設定] 頁面上，選取 **[下一步]**。
   ![[Azure AD 登入設定] 頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. 在 [ **準備設定** ] 頁面上，選取 [ **安裝**]。

   ![[準備設定] 頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. 在 [設定完成] 頁面上，選取 [結束]。
   ![[設定完成] 頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. 同步完成之後，請確認您在 IaaS 目錄上建立的使用者已同步處理至 Azure AD。
   1. 登入 Azure 入口網站。
   1. 選取 **Azure Active Directory**  >  **HDIFabrikam**  >  **使用者**]。

### <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

建立使用者指派的受控識別，您可用來設定 Azure AD Domain Services (Azure AD DS) 。 如需詳細資訊，請參閱 [使用 Azure 入口網站建立、列出、刪除或指派角色給使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 登入 Azure 入口網站。
1. 選取 [ **建立資源** ] 和 [類型] `managed identity` 。 選取 **使用者指派的受控識別**  >  **建立**。
1. 在 [ **資源名稱**] 中，輸入 `HDIFabrikamManagedIdentity` 。
1. 選取您的訂用帳戶。
1. 在 [ **資源群組**] 底下，選取 [ **建立新** 的]，然後輸入 `HDIFabrikam-CentralUS` 。
1. 在 [ **位置**] 底下，選取 [ **美國中部**]。
1. 選取 [建立]  。

![建立新的使用者指派受控識別](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>啟用 Azure AD DS

遵循下列步驟來啟用 Azure AD DS。 如需詳細資訊，請參閱 [使用 Azure 入口網站啟用 AZURE AD DS](../../active-directory-domain-services/tutorial-create-instance.md)。

1. 建立要裝載 Azure AD DS 的虛擬網路。 執行下列 PowerShell 程式碼。

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
1. 選取 [**建立資源**]，輸入 `Domain services` ，然後選取 [ **Azure AD Domain Services**  >  **建立**]。
1. 在 [ **基本** ] 頁面上：
    1. 在 [ **目錄名稱**] 底下，選取您建立的 Azure AD 目錄： **HDIFabrikam**。
    1. 針對 [ **DNS 功能變數名稱**]，輸入 *HDIFabrikam.com*。
    1. 選取您的訂用帳戶。
    1. 指定資源群組 **HDIFabrikam-CentralUS**。 針對 [ **位置**]，選取 [ **美國中部**]。

        ![Azure AD DS 基本詳細資料](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. 在 [ **網路** ] 頁面上，選取 (**HDIFabrikam-VNET**) 的網路，以及您使用 PowerShell 腳本建立的子網 (**AADDS-子網**) 。 或選擇 [ **建立新** 的]，立即建立虛擬網路。

    ![「建立虛擬網路」步驟](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. 在 [ **系統管理員群組** ] 頁面上，您應該會看到已建立名為「 **AAD DC 系統管理員** 」的群組來管理此群組的通知。 您可以視需要修改此群組的成員資格，但在此情況下，您不需要加以變更。 選取 [確定]。

    ![查看 Azure AD 系統管理員群組](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. 在 [**同步** 處理] 頁面上 **，選取**  >  **[確定]** 以啟用完成同步處理。

    ![啟用 Azure AD DS 同步處理](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. 在 [ **摘要** ] 頁面上，確認 Azure AD DS 的詳細資料，然後選取 **[確定]**。

    ![「啟用 Azure AD Domain Services」的摘要](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

啟用 Azure AD DS 之後，本機 DNS 伺服器會在 Azure AD Vm 上執行。

### <a name="configure-your-azure-ad-ds-virtual-network"></a>設定您的 Azure AD DS 虛擬網路

使用下列步驟來設定您的 Azure AD DS 虛擬網路 (**HDIFABRIKAM AADDSVNET**) 以使用您的自訂 DNS 伺服器。

1. 找出您的自訂 DNS 伺服器的 IP 位址。
    1. 選取 `HDIFabrikam.com` AZURE AD DS 資源。
    1. 在 [管理] 底下，選取 [屬性]。
    1. 在 [虛擬網路] 的 [ **ip 位址**] 下找出 ip 位址。

    ![找出 Azure AD DS 的自訂 DNS IP 位址](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. 設定 **HDIFabrikam-AADDSVNET** 使用自訂 IP 位址10.0.0.4 和10.0.0.5。

    1. 在 [ **設定**] 底下，選取 [ **DNS 伺服器**]。
    1. 選取 [自訂]。
    1. 在文字方塊中，輸入 (*10.0.0.4*) 的第一個 IP 位址。
    1. 選取 [儲存]。
    1. 重複步驟，將其他 IP 位址新增 (*10.0.0.5*) 。

在我們的案例中，我們將 Azure AD DS 設定為使用 IP 位址10.0.0.4 和10.0.0.5，並在 Azure AD DS 虛擬網路上設定相同的 IP 位址：

![自訂 DNS 伺服器頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>保護 LDAP 流量

輕量型目錄存取協定 (LDAP) 用來讀取和寫入 Azure Active Directory。 您可以使用安全通訊端層 (SSL) 或傳輸層安全性 (TLS) 技術，讓 LDAP 流量成為機密和安全。 您可以藉由安裝格式正確的憑證來啟用 LDAP over SSL (LDAPS) 。

如需安全 LDAP 的詳細資訊，請參閱為 [AZURE AD DS 受控網域設定 LDAPS](../../active-directory-domain-services/tutorial-configure-ldaps.md)。

在本節中，您會建立自我簽署憑證、下載憑證，以及為 **HDIFabrikam** Azure AD DS 受控網域設定 LDAPS。

下列腳本會建立 **HDIFabrikam** 的憑證。 憑證會儲存在 *LocalMachine* 路徑中。

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> 任何建立有效公開金鑰加密標準 (PKCS) 10 要求的公用程式或應用程式， \# 都可以用來形成 TLS/SSL 憑證要求。

確認憑證已安裝在電腦的 **個人** 存放區中：

1.  (MMC) 開始 Microsoft Management Console。
1. 新增可管理本機電腦憑證的 [ **憑證** ] 嵌入式管理單元。
1. 展開 [憑證 (本機電腦)] > [個人] > [憑證]。 新的憑證應該存在於 **個人** 存放區中。 此憑證會發行至完整的主機名稱。

    ![確認本機憑證建立](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. 在右側窗格中，以滑鼠右鍵按一下您建立的憑證。 指向 [ **所有** 工作]，然後選取 [ **匯出**]。

1. 在 [ **匯出私密金鑰** ] 頁面上，選取 [ **是，匯出私密金鑰]**。 將匯入金鑰的電腦需要私密金鑰才能讀取加密的訊息。

    ![憑證匯出 Wizard 的 [匯出私密金鑰] 頁面](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. 在 [ **匯出檔案格式** ] 頁面上，保留預設設定，然後選取 **[下一步]**。
1. 在 [ **密碼** ] 頁面上，輸入私密金鑰的密碼。 針對 **加密**，請選取 [ **TripleDES-SHA1**]。 然後選取 [下一步]。
1. 在 [ **要匯出** 的檔案] 頁面上，輸入所匯出憑證檔案的路徑和名稱，然後選取 **[下一步]**。 檔案名的副檔名必須為 .pfx。 此檔案會在 Azure 入口網站中設定，以建立安全的連接。
1. 針對 Azure AD DS 受控網域啟用 LDAPS。
    1. 從 Azure 入口網站選取網域 `HDIFabrikam.com` 。
    1. 在 [ **管理**] 底下，選取 [ **安全 LDAP**]。
    1. 在 [ **安全 LDAP** ] 頁面的 [ **安全 LDAP**] 底下，選取 [ **啟用**]。
    1. 流覽您在電腦上匯出的 .pfx 憑證檔案。
    1. 輸入憑證密碼。

    ![啟用安全 LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. 現在您已啟用 LDAPS，請確定可透過啟用埠636來連線。
    1. 在 [ **HDIFabrikam-CentralUS** ] 資源群組中，選取 [網路安全性群組] **AADDS-HDIFabrikam.com-NSG**。
    1. 在 [**設定**] 底下，選取 [**輸入安全性規則**  >  **新增**]。
    1. 在 [ **新增輸入安全性規則** ] 頁面上，輸入下列屬性，然後選取 [ **新增**]：

        | 屬性 | 值 |
        |---|---|
        | 來源 | 任意 |
        | 來源連接埠範圍 | * |
        | Destination | 任意 |
        | 目的地連接埠範圍 | 636 |
        | 通訊協定 | 任意 |
        | 動作 | Allow |
        | 優先順序 | \<Desired number> |
        | 名稱 | Port_LDAP_636 |

    ![[新增輸入安全性規則] 對話方塊](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** 是使用者指派的受控識別。 系統會為受控識別啟用 HDInsight 網域服務參與者角色，以允許此身分識別讀取、建立、修改和刪除網域服務作業。

![建立使用者指派的受控識別](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>建立已啟用 ESP 的 HDInsight 叢集

此步驟需要下列必要條件：

1. 在「**美國西部**」位置建立新的資源群組 *HDIFabrikam-WestUS* 。
1. 建立將裝載已啟用 ESP 之 HDInsight 叢集的虛擬網路。

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. 在裝載 Azure AD DS () 的虛擬網路 `HDIFabrikam-AADDSVNET` ，以及將裝載已啟用 ESP 的 HDInsight 叢集 () 的虛擬網路之間建立對等關聯性 `HDIFabrikam-HDIVNet` 。 使用下列 PowerShell 程式碼將兩個虛擬網路對等互連。

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. 建立名為 **Hdigen2store** 的新 Azure Data Lake Storage Gen2 帳戶。 使用使用者管理的身分識別 **HDIFabrikamManagedIdentity** 來設定帳戶。 如需詳細資訊，請參閱 [搭配使用 Azure Data Lake Storage Gen2 與 Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)叢集。

1. 在 **HDIFabrikam-AADDSVNET** 虛擬網路上設定自訂 DNS。
    1. 移至 Azure 入口網站 >**資源群組**  >  **OnPremADVRG**  >  **HDIFabrikam-AADDSVNET**  >  **DNS 伺服器**。
    1. 選取 [ **自訂** ]，然後輸入 *10.0.0.4* 和 *10.0.0.5*。
    1. 選取 [儲存]。

        ![儲存虛擬網路的自訂 DNS 設定](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. 建立啟用 ESP 的新 HDInsight Spark 叢集。
    1. 選取 **自訂 (大小、設定、應用程式)**。
    1. 輸入 **基本** (第1節) 的詳細資料。 確定叢集 **類型** 為 **SPARK 2.3 (HDI 3.6)**。 確定 **資源群組** 是 **HDIFabrikam-CentralUS**。

    1. 針對 **安全性 + 網路** (第2節) ，請填入下列詳細資料：
        * 在 [ **企業安全性套件**] 下，選取 [ **已啟用**]。
        * 選取 [叢集 **管理使用者** ]，然後選取以內部部署系統管理員使用者身分建立的 **HDIAdmin** 帳戶。 按一下 [選取]。
        * 選取 [叢集 **存取群組**  >  **HDIUserGroup**]。 您未來新增至此群組的任何使用者都可以存取 HDInsight 叢集。

            ![選取叢集存取群組 HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. 完成叢集設定的其他步驟，並確認叢集 **摘要** 的詳細資料。 選取 [建立]  。

1. 登入新建立之叢集的 Ambari UI `https://CLUSTERNAME.azurehdinsight.net` 。 使用您的系統管理員使用者名稱 `hdiadmin@hdifabrikam.com` 和密碼。

    ![Apache Ambari UI 登入視窗](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. 從叢集儀表板中選取 [ **角色**]。
1. 在 [ **角色** ] 頁面的 [ **將角色指派給這些** 角色] 底下的 [叢集 **管理員** ] 角色旁，輸入群組 *hdiusergroup*。 

    ![將叢集系統管理員角色指派給 hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. 開啟安全殼層 (SSH) 用戶端，然後登入叢集。 使用您在內部部署 Active Directory 實例中建立的 **hdiuser** 。

    ![使用 SSH 用戶端登入叢集](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

如果您可以使用此帳戶登入，則您已正確設定 ESP 叢集，以與內部部署 Active Directory 實例進行同步處理。

## <a name="next-steps"></a>後續步驟

閱讀 [使用 ESP 的 Apache Hadoop 安全性簡介](hdinsight-security-overview.md)。