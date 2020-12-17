---
title: 設定 Azure Active Directory 整合的叢集
titleSuffix: Azure HDInsight
description: 瞭解如何使用 Azure Active Directory Domain Services 和企業安全性套件功能，設定與 Active Directory 整合的 HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020, contperf-fy21q2
ms.date: 10/30/2020
ms.openlocfilehash: c5c5db892f417f2e2ef3fde3535d806d39342327
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631545"
---
# <a name="configure-hdinsight-clusters-for-azure-active-directory-integration-with-enterprise-security-package"></a>設定 HDInsight 叢集以 Azure Active Directory 與企業安全性套件整合

本文提供建立和設定與 Azure Active Directory 整合之 HDInsight 叢集的程式摘要和總覽。 這項整合依賴稱為企業安全性套件 (ESP) 的 HDInsight 功能，Azure Active Directory Domain Services (Azure AD DS) 和既有的內部部署 Active Directory。

如需詳細的逐步教學課程，說明如何在 Azure 中設定網域並建立啟用 ESP 的叢集，然後同步內部部署使用者，請參閱 [在 Azure HDInsight 中建立和設定企業安全性套件](apache-domain-joined-create-configure-enterprise-security-cluster.md)叢集。

## <a name="background"></a>背景

企業安全性套件 (ESP) 提供 Azure HDInsight 的 Active Directory 整合。 這種整合可讓網域使用者使用其網域認證來向 HDInsight 叢集進行驗證，並執行 big data 作業。

> [!NOTE]  
> ESP 已在 HDInsight 3.6 和4.0 中正式推出，適用于下列叢集類型： Apache Spark、互動式、Hadoop 和 HBase。 Apache Kafka 叢集類型的 ESP 處於預覽狀態，僅提供最佳支援。 在 ESP GA 日期之前建立的 ESP 叢集 (2018 年10月1日) 不受支援。

## <a name="prerequisites"></a>必要條件

您必須先完成幾個必要條件，才能建立已啟用 ESP 的 HDInsight 叢集：

- 現有的內部部署 Active Directory 和 Azure Active Directory。
- 啟用 Azure AD DS。
- 檢查 Azure AD DS 健全狀況狀態，以確保同步處理已完成。
- 建立及授權受控識別。
- 完成 DNS 和相關問題的網路設定。

以下將詳細討論每一個專案。 如需完成上述所有步驟的逐步解說，請參閱 [建立及設定 Azure HDInsight 中的企業安全性套件](apache-domain-joined-create-configure-enterprise-security-cluster.md)叢集。

### <a name="enable-azure-ad-ds"></a>啟用 Azure AD DS

您必須先啟用 Azure AD DS，才能建立具有 ESP 的 HDInsight 叢集。 如需詳細資訊，請參閱 [使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md)。

啟用 Azure AD DS 時，所有使用者和物件預設會從 Azure Active Directory (Azure AD) 開始同步處理至 Azure AD DS。 同步作業的所需時間取決於 Azure AD 中的物件數目。 針對數十萬個物件，同步處理可能需要幾天的時間。

您搭配 Azure AD DS 使用的功能變數名稱必須為39個字元或更少，才能與 HDInsight 搭配使用。

您可以選擇只同步需要存取 HDInsight 叢集的群組。 這個僅同步特定群組的選項，稱為 *特定範圍同步處理*。 如需相關指示，請參閱 [設定從 Azure AD 到受控網域的限域同步](../../active-directory-domain-services/scoped-synchronization.md)處理。

當您啟用安全 LDAP 時，請將功能變數名稱放在主體名稱中。 和憑證中的主體替代名稱。 如果您的功能變數名稱是 *contoso100.onmicrosoft.com*，請確定您的憑證主體名稱和主體別名中有正確的名稱。 如需詳細資訊，請參閱[針對 Azure AD DS 受控網域設定安全的 LDAP](../../active-directory-domain-services/tutorial-configure-ldaps.md)。

下列範例會建立自我簽署憑證。 功能變數名稱 *contoso100.onmicrosoft.com* 同時位於 `Subject` (主體名稱) 和 `DnsName` (主體替代名稱) 。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

> [!NOTE]  
> 只有租使用者系統管理員具有啟用 Azure AD DS 的許可權。 如果叢集存放裝置是 Azure Data Lake Storage Gen1 或 Gen2，您必須針對需要使用基本 Kerberos 驗證存取叢集的使用者停用 Azure AD Multi-Factor Authentication。
>
> 您可以使用 [信任的 ip](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) 或 [條件式存取](../../active-directory/conditional-access/overview.md) ，只在特定使用者存取 HDInsight 叢集虛擬網路的 IP 範圍時， *才* 停用 Multi-Factor Authentication。 如果您是使用條件式存取，請確定已在 HDInsight 虛擬網路上啟用 Active Directory 服務端點。
>
> 如果叢集儲存體是 Azure Blob 儲存體，請勿停用 Multi-Factor Authentication。

### <a name="check-azure-ad-ds-health-status"></a>檢查 Azure AD DS 健全狀況狀態

選取 [**管理**] 類別中的 [**健全** 狀況]，以查看 Azure Active Directory Domain Services 的健全狀況狀態。 確定 Azure AD DS 的狀態為綠色 (執行) 且同步處理已完成。

![Azure AD DS 健全狀況](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

### <a name="create-and-authorize-a-managed-identity"></a>建立及授權受控識別

使用 *使用者指派的受控識別* 來簡化網域服務作業的安全。 當您將 **HDInsight 網域服務參與者** 角色指派給受控識別時，它可以讀取、建立、修改和刪除網域服務作業。

HDInsight 企業安全性套件需要某些網域服務作業，例如建立 Ou 和服務主體。 您可以在任何訂用帳戶中建立受控識別。 如需一般受控識別的詳細資訊，請參閱 [適用于 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/overview.md)識別。 如需有關受控識別如何在 Azure HDInsight 中運作的詳細資訊，請參閱 [Azure HDInsight 中的受控](../hdinsight-managed-identities.md)識別。

若要設定 ESP 叢集，請建立使用者指派的受控識別（如果您還沒有的話）。 請參閱 [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

接下來，將 **HDInsight 網域服務參與者** 角色指派給 Azure AD DS 之 **存取控制** 中的受控識別。 您需要 Azure AD DS 系統管理員許可權，才能進行此角色指派。

![Azure Active Directory Domain Services 存取控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

指派 **HDInsight 網域服務參與者** 角色，可確保此身分識別具有適當的 (`on behalf of`) 存取 Azure AD DS 網域上的網域服務作業。 這些作業包括建立和刪除 Ou。

將角色授與受控識別之後，Azure AD DS 系統管理員會管理使用它的人員。 首先，系統管理員會在入口網站中選取受控識別。 然後選取 **[總覽**] **(IAM) 的 [存取控制**]。 系統管理員將 **受控識別操作員** 角色指派給想要建立 ESP 叢集的使用者或群組。

例如，Azure AD DS 系統管理員可以將此角色指派給 **sjmsi** 受控識別的 **MarketingTeam** 群組。 範例如下圖所示。 此指派可確保組織中適當的人員可以使用受控識別來建立 ESP 叢集。

![HDInsight 受控識別操作員角色指派](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

### <a name="network-configuration"></a>網路組態

> [!NOTE]  
> Azure AD DS 必須部署在以 Azure Resource Manager 為基礎的虛擬網路中。 Azure AD DS 不支援傳統虛擬網路。 如需詳細資訊，請參閱 [使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)。

啟用 Azure AD DS。 然後，本機網域名稱系統 (DNS) server 會在 Active Directory 的虛擬機器上執行)  (Vm。 設定您的 Azure AD DS 虛擬網路，以使用這些自訂 DNS 伺服器。 若要找出正確的 IP 位址，請選取 [**管理**] 類別 **中的 [** 內容]，然後查看 [**虛擬網路上的 IP 位址**]。

![找出本機 DNS 伺服器的 IP 位址](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

變更 Azure AD DS 虛擬網路中的 DNS 伺服器設定。 若要使用這些自訂 Ip，請選取 [**設定**] 類別中的 [ **DNS 伺服器**]。 然後選取 [ **自訂** ] 選項，並在文字方塊中輸入第一個 IP 位址，然後選取 [ **儲存**]。 使用相同的步驟來新增更多 IP 位址。

![正在更新虛擬網路 DNS 設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

將 Azure AD DS 執行個體和 HDInsight 叢集放在相同的 Azure 虛擬網路中比較容易。 如果您打算使用不同的虛擬網路，您必須將這些虛擬網路對等互連，才能讓 HDInsight Vm 看到網域控制站。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。

對等互連虛擬網路之後，將 HDInsight 虛擬網路設定為使用自訂的 DNS 伺服器。 並輸入 Azure AD DS 私人 Ip 作為 DNS 伺服器位址。 當兩個虛擬網路都使用相同的 DNS 伺服器時，您的自訂功能變數名稱將解析為正確的 IP，且將可從 HDInsight 連線。 例如，如果您的功能變數名稱是 `contoso.com` ，則在這個步驟之後， `ping contoso.com` 應解析為右邊的 AZURE AD DS IP。

![設定對等互連虛擬網路的自訂 DNS 伺服器](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

如果您使用網路安全性群組 (在您的 HDInsight 子網中 NSG) 規則，則應該允許輸入和輸出流量 [所需的 ip](../hdinsight-management-ip-addresses.md) 。

若要測試您的網路設定，請將 Windows VM 加入至 HDInsight 虛擬網路/子網，並偵測功能變數名稱。  (應解析為 IP。 ) 執行 **ldp.exe** 以存取 Azure AD DS 網域。 然後將此 Windows VM 加入網域，以確認用戶端與伺服器之間的所有必要 RPC 呼叫都會成功。

使用 **nslookup** 來確認對儲存體帳戶的網路存取。 或者，您可能使用的任何外部資料庫 (例如，外部 Hive 中繼存放區或 Ranger DB) 。 如果 NSG 保護 Azure AD DS，請確定 Azure AD DS 子網的 NSG 規則中，允許 [必要的埠](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) 。 如果此 Windows VM 的網域加入成功，則您可以繼續進行下一個步驟，並建立 ESP 叢集。

## <a name="create-an-hdinsight-cluster-with-esp"></a>建立具有 ESP 的 HDInsight 叢集

在您正確設定上述步驟後，下一個步驟是建立已啟用 ESP 的 HDInsight 叢集。 當您建立 HDInsight 叢集時，您可以在 [ **安全性 + 網路** ] 索引標籤上啟用企業安全性套件。如需部署的 Azure Resource Manager 範本，請使用入口網站體驗一次。 然後，在 [ **審核 + 建立** ] 頁面上下載預先填入的範本，以供未來重複使用。

您也可以在叢集建立期間啟用 [HDINSIGHT 識別碼訊息代理](identity-broker.md) 程式功能。 識別碼訊息代理程式功能可讓您使用 Multi-Factor Authentication 登入 Ambari，並取得所需的 Kerberos 票證，而不需要 Azure AD DS 中的密碼雜湊。

> [!NOTE]  
> ESP 叢集名稱的前六個字元在您的環境中必須是唯一的。 例如，如果您在不同的虛擬網路中有多個 ESP 叢集，請選擇一個命名慣例，以確保叢集名稱的前六個字元是唯一的。

![Azure HDInsight 企業安全性套件的網域驗證](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

啟用 ESP 之後，會自動偵測並驗證與 Azure AD DS 相關的一般錯誤配置。 修正這些錯誤之後，您可以繼續進行下一個步驟。

![Azure HDInsight 企業安全性套件網域驗證失敗](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

當您建立具有 ESP 的 HDInsight 叢集時，您必須提供下列參數：

* 叢集系統 **管理員使用者**：從已同步的 Azure AD DS 實例中，為您的叢集選擇系統管理員。 此網域帳戶必須已同步，而且可在 Azure AD DS 中使用。

* 叢集 **存取群組**：您要同步其使用者並具有叢集存取權的安全性群組，應可在 Azure AD DS 中使用。 例如，HiveUsers 群組。 如需詳細資訊，請參閱[在 Azure Active Directory 中建立群組和新增使用者](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

* **LDAPS URL**：範例為 `ldaps://contoso.com:636` 。

當您建立新的叢集時，可以從 [ **使用者指派的受控識別** ] 下拉式清單中選擇您所建立的受控識別。

![Azure HDInsight ESP Active Directory Domain Services 受控識別](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>後續步驟

* 如需了解如何設定 Hive 原則及執行 Hive 查詢，請參閱[為具有 ESP 的 HDInsight 叢集設定 Apache Hive 原則](apache-domain-joined-run-hive.md)。
* 如需了解如何使用 SSH 來連線到具有 ESP 的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 使用 SSH 搭配 Linux 型 HDInsight 上的 Apache Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)。
