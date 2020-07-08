---
title: 安全性：將內部部署 Apache Hadoop 遷移至 Azure HDInsight
description: 了解將內部部署 Hadoop 叢集遷移至 Azure HDInsight 的安全性和 DevOps 最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 4ceefcbbbb53e3ae13f8ced930ae8417fb00965f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75974412"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>將內部部署 Apache Hadoop 叢集遷移到 Azure HDInsight - 安全性和 DevOps 最佳做法

本文提供在 Azure HDInsight 系統中的安全性和 DevOps 建議。 本文是系列文章中的一篇，提供有助於將內部部署 Apache Hadoop 系統移轉至 Azure HDInsight 的最佳做法。

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>使用企業安全性套件保護與管理叢集

企業安全性套件 (ESP) 支援 Active Directory 型驗證、多使用者支援和角色型存取控制。 使用所選的 ESP 選項，HDInsight 叢集會加入到 Active Directory 網域，且企業管理員可以使用 Apache Ranger 針對 Apache Hive 安全性設定角色型存取控制 (RBAC)。 系統管理員也可以稽核由員工存取的資料，以及對存取控制原則所做的任何變更。

ESP 適用於下列叢集類型：Apache Hadoop、Apache Spark、Apache HBase、Apache Kafka 和互動式查詢 (Hive LLAP)。

您可以使用下列步驟來部署已加入網域的 HDInsight 叢集：

- 藉由傳遞網域名稱來部署 Azure Active Directory (AAD)。
- 部署 Azure Active Directory Domain Services (AAD DS)。
- 建立必要的虛擬網路和子網路。
- 在虛擬網路中部署 VM 以管理 AAD DS。
- 將 VM 加入網域。
- 安裝 AD 和 DNS 工具。
- 讓 AAD DS 系統管理員建立組織單位 (OU)。
- 為 LDAPS 啟用 AAD DS。
- 在 Azure Active Directory 中建立有委派讀取與寫入 OU 的權限，讓它能。 此服務帳戶接著可以將機器加入網域，並且在 OU 中置入機器原則。 它也可以在叢集建立期間，於您指定的 OU 中建立服務主體。

    > [!Note]
    > 服務帳戶不需要是 AD 網域系統管理員帳戶。

- 藉由設定下列參數來部署 HDInsight ESP 叢集：

    |參數 |說明 |
    |---|---|
    |網域名稱|與 Azure AD DS 相關聯的網域名稱。|
    |網域使用者名稱|上一節在受 Azure AD DS DC 管理的網域中建立的服務帳戶，例如：`hdiadmin@contoso.onmicrosoft.com`。 此網域使用者將是這個 HDInsight 叢集的系統管理員。|
    |網域密碼|服務帳戶的密碼。|
    |組織單位|HDInsight 叢集要搭配使用的 OU 的辨別名稱，例如：`OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`。 如果此 OU 不存在，HDInsight 叢集會嘗試使用服務帳戶的許可權來建立 OU。|
    |LDAPS URL|例如， `ldaps://contoso.onmicrosoft.com:636` 。|
    |存取使用者群組|您要將其使用者同步至叢集的安全性群組，例如：`HiveUsers`。 如果要指定多個使用者群組，請以分號 (;) 來分隔。 群組必須存在於目錄中，才會建立 ESP 叢集。|

如需詳細資訊，請參閱下列文章：

- [已加入網域之 HDInsight 叢集的 Apache Hadoop 安全性簡介](../domain-joined/hdinsight-security-overview.md)
- [規劃 HDInsight 中已加入網域的 Azure Apache Hadoop 叢集](../domain-joined/apache-domain-joined-architecture.md)
- [使用 Azure Active Directory Domain Services 來設定已加入網域的 HDInsight 叢集](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [將 Azure Active Directory 使用者同步至 HDInsight 叢集](../hdinsight-sync-aad-users-to-cluster.md)
- [在已加入網域的 HDInsight 中設定 Apache Hive 原則](../domain-joined/apache-domain-joined-run-hive.md)
- [在已加入網域的 HDInsight Hadoop 叢集中執行 Apache Oozie](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>實作端對端企業安全性

端對端企業安全性可以藉由使用下列控管來達成：

**私人和受保護的資料管線 (周邊層級安全性)**
    - 周邊層級安全性可以透過 Azure 虛擬網路、網路安全性群組和閘道服務來達成。

**資料存取的驗證和授權**
    - 使用 Azure Active Directory Domain Services 建立已加入網域的 HDInsight 叢集。 (企業安全性套件)。
    - 使用 Ambari 來為 AD 使用者提供對叢集資源的角色型存取。
    - 使用 Apache Ranger 來為 Hive 設定對資料表 / 資料行 / 資料列層級的存取控制原則。
    - 對叢集的 SSH 存取可以僅限於系統管理員。

**稽核**
    - 檢視和報告所有對 HDInsight 叢集資源和資料的存取。
    - 檢視和報告所有存取控制原則的變更。

**加密**
    - 「透明的伺服器端」加密使用受 Microsoft 管理的金鑰或受客戶管理的金鑰。
    - 在使用用戶端加密、HTTPs 和 TLS 的傳輸加密中。

如需詳細資訊，請參閱下列文章：

- [Azure 虛擬網路概觀](../../virtual-network/virtual-networks-overview.md)
- [Azure 網路安全性群組概觀](../../virtual-network/security-overview.md)
- [Azure 虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)
- [Azure 儲存體安全性指南](../../storage/blobs/security-recommendations.md)
- [Azure 儲存體服務待用加密](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>使用監視與警示

如需詳細資訊，請參閱文章：

[Azure 監視器總覽](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>升級叢集

定期升級為最新的 HDInsight 版本，以利用最新的功能。 下列步驟可用來將叢集升級為最新版本：

1. 使用最新可用的 HDInsight 版本，來建立新的測試 HDInsight 叢集。
1. 在新叢集上測試，以確定作業和工作負載如預期般運作。
1. 視需要修改作業、應用程式或工作負載。
1. 備份所有儲存在本機叢集節點上的暫時性資料。
1. 刪除現有的叢集。
1. 使用與先前叢集相同的預設資料和中繼存放區，在相同的虛擬網路子網中建立最新 HDInsight 版本的叢集。
1. 匯入任何已備份的暫時性資料。
1. 使用新叢集啟動工作/繼續處理。

如需詳細資訊，請參閱文章：將[HDInsight 叢集升級至新版本](../hdinsight-upgrade-cluster.md)。

## <a name="patch-cluster-operating-systems"></a>修補叢集作業系統

HDInsight 作為受控 Hadoop 服務，會負責修補 HDInsight 叢集所使用之 VM 的 OS。

如需詳細資訊，請參閱文章： [HDInsight 的作業系統修補](../hdinsight-os-patching.md)。

## <a name="post-migration"></a>移轉後

1. **補救應用程式** - 反覆地對作業、處理序和指令碼進行必要的變更。
2. **執行測試** - 反覆地執行功能和效能測試。
3. **最佳化** - 根據上述的測試結果，解決任何效能問題，然後重新測試以確認效能改進。

## <a name="next-steps"></a>後續步驟

深入了解 [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)。
