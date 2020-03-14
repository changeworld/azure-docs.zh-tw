---
title: Azure AD DS Azure HDInsight 的企業安全性
description: 瞭解如何使用 Azure Active Directory Domain Services 安裝和設定 HDInsight 企業安全性套件叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272834"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>在 HDInsight 中使用 Azure Active Directory Domain Services 的企業安全性套件設定

企業安全性套件（ESP）叢集會在 Azure HDInsight 叢集上提供多使用者存取。 具有 ESP 的 HDInsight 叢集會與網域連線，讓網域使用者可以使用其網域認證來驗證叢集並執行巨量資料作業。

在本文中，您將瞭解如何使用 Azure Active Directory Domain Services （Azure AD DS）來設定具有 ESP 的 HDInsight 叢集。

> [!NOTE]  
> ESP 在 HDInsight 3.6 和4.0 中已正式推出，適用于這些叢集類型： Apache Spark、互動式、Hadoop 和 HBase。 Apache Kafka 叢集類型的 ESP 僅供預覽，僅提供最佳的支援。 不支援在 ESP GA 日期之前建立的 ESP 叢集（2018年10月1日）。

## <a name="enable-azure-ad-ds"></a>啟用 Azure AD DS

> [!NOTE]  
> 只有租使用者系統管理員具有啟用 Azure AD DS 的許可權。 如果叢集儲存體是 Azure Data Lake Storage Gen1 或 Gen2，您必須僅針對需要使用基本 Kerberos 驗證來存取叢集的使用者停用 Azure 多重要素驗證。 
>
> 只有當特定使用者正在存取 HDInsight 叢集的虛擬網路的 IP 範圍時，您*才*可以使用[受信任的 ip](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips)或[條件式存取](../../active-directory/conditional-access/overview.md)來停用其多重要素驗證。 如果您使用的是條件式存取，請確定已在 HDInsight 虛擬網路上啟用 Active Directory 服務端點。
>
> 如果叢集儲存體是 Azure Blob 儲存體，請勿停用多重要素驗證。

在您可以建立具有 ESP 的 HDInsight 叢集之前，啟用 Azure AD DS 是必要條件。 如需詳細資訊，請參閱[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md)。 

啟用 Azure AD DS 時，所有使用者和物件預設會開始從 Azure Active Directory （Azure AD）同步處理至 Azure AD DS。 同步作業的所需時間取決於 Azure AD 中的物件數目。 對數十萬個物件進行同步處理可能需要幾天的時間。 

您搭配 Azure AD DS 使用的功能變數名稱必須是39個字元或更少，才能使用 HDInsight。

您可以選擇只同步需要存取 HDInsight 叢集的群組。 這個僅同步特定群組的選項，稱為*特定範圍同步處理*。 如需指示，請參閱[設定從 Azure AD 到受控網域的範圍同步](../../active-directory-domain-services/scoped-synchronization.md)處理。

當您啟用安全 LDAP 時，請在憑證的主體名稱和主體替代名稱中放入功能變數名稱。 例如，如果您的網域名稱是 *contoso100.onmicrosoft.com*，請確定相同的名稱存在於您的憑證主體名稱與主體替代名稱中。 如需詳細資訊，請參閱[針對 Azure AD DS 受控網域設定安全的 LDAP](../../active-directory-domain-services/tutorial-configure-ldaps.md)。 

下列範例會建立自我簽署憑證。 功能變數名稱*contoso100.onmicrosoft.com*同時為 `Subject` （主體名稱）和 `DnsName` （主體替代名稱）。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>檢查 Azure AD DS 健全狀況狀態
選取 [**管理**] 類別中的 [**健康**情況]，以查看 Azure Active Directory Domain Services 的健全狀況狀態。 請確定 Azure AD DS 的狀態為綠色（執行中），且同步處理已完成。

![Azure AD DS 健全狀況](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>建立及授權受控識別

您可以使用*使用者指派的受控識別*，來簡化及協助保護網域服務作業。 當您將**HDInsight 網域服務參與者**角色指派給受控識別時，它可以讀取、建立、修改和刪除網域服務作業。 

HDInsight 企業安全性套件需要特定的網域服務作業，例如建立 Ou 和服務主體。 您可以在任何訂用帳戶中建立受控識別。 如需有關受控識別的一般詳細資訊，請參閱[適用于 Azure 資源的受控](../../active-directory/managed-identities-azure-resources/overview.md)識別。 如需如何在 Azure HDInsight 中使用受控識別的詳細資訊，請參閱[Azure HDInsight 中的受控](../hdinsight-managed-identities.md)識別。

若要設定 ESP 叢集，請建立使用者指派的受控識別（如果您還沒有的話）。 如需指示，請參閱[使用 Azure 入口網站建立、列出、刪除或指派角色給使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

接下來，在 Azure AD DS 的**存取控制**中，將**HDInsight 網域服務參與者**角色指派給受控識別。 您需要 Azure AD DS 系統管理員許可權，才能進行此角色指派。

![Azure Active Directory Domain Services 存取控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

指派**HDInsight 網域服務參與者**角色可確保此身分識別具有適當（代表）存取權，以在 Azure AD DS 網域上執行網域服務作業。 這些作業包括建立和刪除 Ou。

建立受控識別並指定正確的角色之後，Azure AD DS 系統管理員可以設定誰可以使用此受控識別。 首先，系統管理員在入口網站中選取受控識別，然後選取 **[總覽**] 底下的 [**存取控制（IAM）** ]。 然後，系統管理員會在右側將**受控識別操作員**角色指派給想要建立 HDInsight ESP 叢集的使用者或群組。 

例如，Azure AD DS 系統管理員可以將此角色指派給**sjmsi**受控識別的**MarketingTeam**群組，如下圖所示。 此指派可確保組織中的適當人員可以使用受控識別來建立 ESP 叢集。

![HDInsight 受控識別操作員角色指派](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>網路考量事項

> [!NOTE]  
> Azure AD DS 必須部署在以 Azure Resource Manager 為基礎的虛擬網路中。 Azure AD DS 不支援傳統虛擬網路。 如需詳細資訊，請參閱[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)。

啟用 Azure AD DS 之後，本機網域名稱系統（DNS）伺服器會在 Active Directory 的虛擬機器（Vm）上執行。 將您的 Azure AD DS 虛擬網路設定為使用這些自訂 DNS 伺服器。 若要找出正確的 IP 位址，請在 [**管理**] 類別中選取 [**屬性**]，並查看 [**虛擬網路上的 IP 位址**]。

![尋找本機 DNS 伺服器的 IP 位址](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

在 [設定] 類別中選取 [ **dns 伺服器**]，將 Azure AD DS 虛擬網路中的 Dns 伺服器**設定**變更為使用這些自訂 ip。 然後選取 [**自訂**] 選項，在文字方塊中輸入第一個 IP 位址，然後選取 [**儲存**]。 使用相同的步驟來新增更多 IP 位址。

![正在更新虛擬網路 DNS 設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

將 Azure AD DS 執行個體和 HDInsight 叢集放在相同的 Azure 虛擬網路中比較容易。 如果您打算使用不同的虛擬網路，您必須將這些虛擬網路對等互連，讓 HDInsight Vm 可以看見網域控制站。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。 

對等互連虛擬網路之後，請將 HDInsight 虛擬網路設定為使用自訂 DNS 伺服器，並輸入 Azure AD DS 私人 Ip 作為 DNS 伺服器位址。 當兩個虛擬網路都使用相同的 DNS 伺服器時，您的自訂功能變數名稱會解析為正確的 IP，並可從 HDInsight 連線。 例如，如果您的功能變數名稱為 `contoso.com`，則在此步驟之後，`ping contoso.com` 應該會解析為正確的 Azure AD DS IP。

![設定對等互連虛擬網路的自訂 DNS 伺服器](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

如果您在 HDInsight 子網中使用網路安全性群組（NSG）規則，您應該允許輸入和輸出流量所[需的 ip](../hdinsight-management-ip-addresses.md) 。

若要測試您的網路是否已正確設定，請將 Windows VM 加入 HDInsight 虛擬網路/子網，並 ping 功能變數名稱。 （它應該會解析為 IP）。執行**ldp.exe**以存取 Azure AD DS 網域。 然後將此 Windows VM 加入網域，以確認用戶端與伺服器之間的所有必要 RPC 呼叫都成功。 

您也可以使用**nslookup**來確認您的儲存體帳戶或任何您可能使用之外部資料庫的網路存取（例如，external Hive 中繼存放區或 Ranger DB）。 如果 NSG 有助於保護 Azure AD DS，請確定 Azure AD DS 子網的 NSG 規則中，允許所有[必要的埠](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)。 如果加入此 Windows VM 的網域成功，您可以繼續進行下一個步驟，並建立 ESP 叢集。

## <a name="create-an-hdinsight-cluster-with-esp"></a>建立具有 ESP 的 HDInsight 叢集

正確設定上述步驟之後，下一步就是建立已啟用 ESP 的 HDInsight 叢集。 當您建立 HDInsight 叢集時，您可以在 [**安全性 + 網路**] 索引標籤上啟用企業安全性套件。如果您想要使用 Azure Resource Manager 範本進行部署，請使用入口網站體驗一次，並在 [**審查 + 建立**] 頁面上下載預先填入的範本，以供日後重複使用。 

您也可以在叢集建立期間啟用[HDINSIGHT 識別碼訊息代理](identity-broker.md)程式功能。 識別碼訊息代理程式功能可讓您使用多重要素驗證來登入 Ambari，並取得所需的 Kerberos 票證，而不需要 Azure AD DS 中的密碼雜湊。

> [!NOTE]  
> ESP 叢集名稱的前六個字元在您的環境中必須是唯一的。 例如，如果您在不同的虛擬網路中有多個 ESP 叢集，請選擇一種命名慣例，以確保叢集名稱的前六個字元是唯一的。

![Azure HDInsight 企業安全性套件的網域驗證](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

啟用 ESP 之後，系統會自動偵測及驗證與 Azure AD DS 相關的常見錯誤配置。 修正這些錯誤之後，您可以繼續進行下一個步驟。

![Azure HDInsight 企業安全性套件網域驗證失敗](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

當您建立具有 ESP 的 HDInsight 叢集時，您必須提供下列參數：

- 叢集**管理使用者**：從同步處理的 Azure AD DS 實例中，選擇叢集的系統管理員。 此網域帳戶必須已同步處理，並可在 Azure AD DS 中使用。

- 叢集**存取群組**：您要同步其使用者並具有叢集存取權的安全性群組，應可在 Azure AD DS 中取得。 例如，HiveUsers 群組。 如需詳細資訊，請參閱[在 Azure Active Directory 中建立群組和新增使用者](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

- **LDAPS URL**： `ldaps://contoso.com:636`的範例。

當您建立新的叢集時，可以從 [**使用者指派的受控識別**] 下拉式清單中選擇您所建立的受控識別。

![Azure HDInsight ESP Active Directory Domain Services 受控識別](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png)。

## <a name="next-steps"></a>後續步驟

* 如需了解如何設定 Hive 原則及執行 Hive 查詢，請參閱[為具有 ESP 的 HDInsight 叢集設定 Apache Hive 原則](apache-domain-joined-run-hive.md)。
* 如需了解如何使用 SSH 來連線到具有 ESP 的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 使用 SSH 搭配 Linux 型 HDInsight 上的 Apache Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)。
