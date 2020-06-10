---
title: Azure HDInsight 中的企業安全性概觀
description: 了解各種方法以確保 Azure HDInsight 中的企業安全性。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 94823445e4f8e3f8d40a219dc23b40f8a5c267c6
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996307"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Azure HDInsight 中的企業安全性概觀

Azure HDInsight 提供多種方法來滿足您的企業安全性需求。 根據預設，這些解決方案大多未啟用。 此彈性可讓您選擇對您而言最重要的安全性功能。 並協助您避免支付不想要的功能。 此彈性也表示您必須負責確保已針對您的設定和環境啟用正確的解決方案。

本文藉由將安全性解決方案劃分為四個傳統安全性要素，來探討安全性解決方案：周邊安全性、驗證、授權和加密。

本文也介紹 **Azure HDInsight 企業安全性套件 (ESP)** ，其提供以 Active Directory 為基礎的驗證、多使用者支援，以及 HDInsight 叢集的角色型存取控制。

## <a name="enterprise-security-pillars"></a>企業安全性要素

探討企業安全性的其中一種方式，就是根據控制項的類型將安全性解決方案分成四個主要群組。 這些群組也稱為安全性要素，類型如下：周邊安全性、驗證、授權和加密。

### <a name="perimeter-security"></a>周邊安全性

透過[虛擬網路](../hdinsight-plan-virtual-network-deployment.md)可達成 HDInsight 中的周邊安全性。 企業系統管理員可以在虛擬網路 (VNET) 內部建立叢集，並使用網路安全性群組 (NSG) 限制對虛擬網路的存取。 只有輸入 NSG 規則中允許的 IP 位址能夠與 HDInsight 叢集通訊。 此設定可提供周邊安全性。

部署在 VNET 中的所有叢集也會有私人端點。 端點會解析為 VNET 內的私人 IP，以便對叢集閘道進行私人的 HTTP 存取。

### <a name="authentication"></a>驗證

HDInsight 的[企業安全性套件](apache-domain-joined-architecture.md)提供 Active Directory 型驗證、多使用者支援和角色型存取控制。 透過使用 [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) 即可達成 Active Directory 整合。 利用這些功能，您可以建立已加入 Active Directory 網域的 HDInsight 叢集。 接著，設定企業員工清單，指出可向叢集進行驗證的員工。

企業員工可以藉由這項設定，便能使用其網域認證來登入叢集節點。 他們也可以使用其網域認證向其他已核准的端點進行驗證。 像是可與叢集互動的 Apache Ambari Views、ODBC、JDBC、PowerShell 和 REST API。

### <a name="authorization"></a>授權

大多數企業所依循的最佳做法，就是確保並非每一位員工都能完全存取所有的企業資源。 同樣地，系統管理員可以針對叢集資源定義角色型存取控制原則。 此動作僅適用於 ESP 叢集。

Hadoop 系統管理員可以設定角色型存取控制 (RBAC)。 這些設定使用 Apache Ranger 外掛程式來保護 Apache [Hive](apache-domain-joined-run-hive.md)、[HBase](apache-domain-joined-run-hbase.md) 和 [Kafka](apache-domain-joined-run-kafka.md)。 設定 RBAC 原則可讓您將權限與組織中的角色產生關聯。 這一層的抽象概念可讓您更輕鬆地確保人員只擁有執行其職責所需的權限。 Ranger 也可讓您稽核員工的存取資料以及對存取控制原則所做的任何變更。

例如，系統管理員可以設定 [Apache Ranger](https://ranger.apache.org/) 以設定 Hive 的存取控制原則。 此功能可確保執行資料列層級和資料行層級的篩選 (資料遮罩)。 並且篩選未經授權使用者的敏感性資料。

### <a name="auditing"></a>稽核

您需要稽核叢集資源的存取，才能追蹤未經授權或意外的資源存取。 請務必防止叢集資源遭到未經授權的存取。

系統管理員可以檢視和報告所有對於 HDInsight 叢集資源和資料的存取。 系統管理員可檢視和報告存取控制原則的變更。

若要存取 Apache Ranger 和 Ambari 稽核記錄以及 ssh 存取記錄，[請啟用 Azure 監視器](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing)。 並檢視提供稽核記錄的資料表。

### <a name="encryption"></a>加密

若要符合組織安全性和合規性需求，保護資料就非常重要。 除了限制未經授權的員工存取資料之外，您還應該將資料加密。

Azure 儲存體和 Azure Data Lake Storage Gen1/Gen2 都支援透明伺服器端的待用[資料加密](../../storage/common/storage-service-encryption.md)。 安全的 HDInsight 叢集會完美地與伺服器端待用資料加密配合運作。

### <a name="compliance"></a>法規遵循

Azure 合規性供應項目是以各種類型的保證為基礎，包括正式認證。 此外也包括證明、驗證和授權。 獨立第三方稽核公司所產生的評量。 Microsoft 所產生的合約修訂、自我評量及客戶指導文件。 如需 HDInsight 合規性資訊，請參閱 [Microsoft 信任中心](https://www.microsoft.com/trust-center)和 [Microsoft Azure 合規性的概觀](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。

## <a name="shared-responsibility-model"></a>共同責任模式

下圖摘要說明主要系統安全性區域，以及您可在各區域中使用的安全性解決方案。 其中也會強調哪些安全性區域是您身為客戶的責任。 而哪些區域是 HDInsight 作為服務提供者的責任。

![HDInsight 共用責任圖表](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

下表提供每種安全性解決方案的資源連結。

| 安全性區域 | 可用的解決方案 | 負責方 |
|---|---|---|
| 資料存取安全性 | 設定 Azure Data Lake Storage Gen1 和 Gen2 的[存取控制清單 (ACL)](../../storage/blobs/data-lake-storage-access-control.md)  | 客戶 |
|  | 在儲存體帳戶上啟用 [[需要安全傳輸]](../../storage/common/storage-require-secure-transfer.md) 屬性。 | 客戶 |
|  | 設定 [Azure 儲存體防火牆](../../storage/common/storage-network-security.md)和虛擬網路 | 客戶 |
|  | 設定 Cosmos DB 和 [Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) 的 [Azure 虛擬網路服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) | 客戶 |
|  | 確定已針對傳輸中的資料啟用 [TLS 加密](../../storage/common/storage-security-tls.md)。 | 客戶 |
|  | 設定[客戶管理的金鑰](../../storage/common/storage-encryption-keys-portal.md)以進行 Azure 儲存體加密 | 客戶 |
| 應用程式和中介軟體安全性 | 與 AAD-DS 整合並[設定驗證](apache-domain-joined-configure-using-azure-adds.md) | 客戶 |
|  | 設定 [Apache Ranger 授權](apache-domain-joined-run-hive.md)原則 | 客戶 |
|  | 使用 [Azure 監視器記錄](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | 客戶 |
| 作業系統安全性 | 使用最新安全基底映射建立叢集 | 客戶 |
|  | 確保定期進行 [OS 修補](../hdinsight-os-patching.md) | 客戶 |
| 網路安全性 | 設定[虛擬網路](../hdinsight-plan-virtual-network-deployment.md) |
|  | 設定[輸入網路安全性群組 (NSG) 規則](../control-network-traffic.md) | 客戶 |
|  | 使用防火牆來設定[輸出流量限制](../hdinsight-restrict-outbound-traffic.md) | 客戶 |
| 虛擬化基礎結構 | N/A | HDInsight (雲端提供者) |
| 實體基礎結構安全性 | N/A | HDInsight (雲端提供者) |

## <a name="next-steps"></a>後續步驟

* [規劃具有 ESP 的 HDInsight 叢集](apache-domain-joined-architecture.md)
* [設定具有 ESP 的 HDInsight 叢集](apache-domain-joined-configure.md)
* [管理具有 ESP 的 HDInsight 叢集](apache-domain-joined-manage.md)
