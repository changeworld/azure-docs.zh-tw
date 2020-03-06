---
title: 基礎結構：內部部署 Apache Hadoop 至 Azure HDInsight
description: 了解將內部部署 Hadoop 叢集遷移到 Azure HDInsight 的基礎結構最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: d7ee8ae121e3cbb9760a87c95d12109a9b05e0c5
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951508"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>將內部部署 Apache Hadoop 叢集遷移到 Azure HDInsight - 基礎結構最佳做法

本文提供管理 Azure HDInsight 叢集基礎結構的建議。 本文是系列文章中的一篇，提供有助於將內部部署 Apache Hadoop 系統移轉至 Azure HDInsight 的最佳做法。

## <a name="plan-for-hdinsight-cluster-capacity"></a>HDInsight 叢集容量的規劃

針對 HDInsight 叢集容量進行規劃的重要選擇如下：

**區域**  
Azure 區域會決定叢集實際布建的位置。 若要將讀取和寫入的延遲降至最低，叢集應該與資料位於相同區域。

**儲存位置和大小**  
預設儲存體必須與叢集位於相同的區域。 若是48節點叢集，建議使用4到8個儲存體帳戶。 雖然可能已經有足夠的儲存體總計，每個儲存體帳戶都會提供額外的網路頻寬供計算節點使用。 當有多個儲存體帳戶時，請對每個儲存體帳戶使用隨機名稱，不含前置詞。 隨機命名的目的在於減少儲存體瓶頸 (節流) 或所有帳戶發生一般模式失敗的機會。 為提升效能，每個儲存體帳戶僅使用一個容器。

**VM 大小和類型（現在支援 G 系列）**  
每個叢集類型都具有一組節點類型，且每個節點類型都有其 VM 大小和類型的特定選項。 VM 大小與類型是由 CPU 處理能力、RAM 大小和網路延遲所決定。 模擬工作負載可用來決定每個節點類型適用的最佳 VM 大小和類型。

**背景工作節點數目**  
您可以使用模擬的工作負載來判斷背景工作節點的初始數目。 稍後可新增更多背景工作節點來調整叢集，以符合尖峰負載需求。 稍後當不需要額外的背景工作節點時，叢集可以相應放大。

如需詳細資訊，請參閱 [HDInsight 叢集的容量規劃](../hdinsight-capacity-planning.md)一文。

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>使用針對叢集建議的虛擬機器類型

如需每種 HDInsight 叢集類型適用的建議虛擬機器類型，請參閱[適用於叢集的預設節點設定和虛擬機器大小](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)。

## <a name="check-hadoop-components-availability-in-hdinsight"></a>在 HDInsight 中檢查 Hadoop 元件可用性

每個 HDInsight 版本都是一組 Hadoop 生態系統元件的雲端發佈。 如需所有 HDInsight 元件與其最新版本的詳細資訊，請參閱 [HDInsight 元件版本設定](../hdinsight-component-versioning.md)。

您也可以使用 Apache Ambari UI 或 Ambari REST API ，在 HDInsight 中檢查 Hadoop 元件和版本。

在內部部署叢集中提供但不屬於 HDInsight 叢集的應用程式或元件，可以新增至邊緣節點或與 HDInsight 叢集位於相同 VNet 中的 VM 上。 協力廠商 Hadoop 應用程式無法在 Azure HDInsight 上使用，但可以在 HDInsight 叢集中使用「應用程式」選項安裝。 自訂 Hadoop 應用程式可使用「指令碼動作」在 HDInsight 叢集上安裝。 下表列出一些常見應用程式及其 HDInsight 整合選項：

|**應用程式**|**整合**
|---|---|
|氣流|IaaS 或 HDInsight 邊緣節點
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|無 (僅 HDP)
|Datameer|HDInsight 邊緣節點
|Datastax (Cassandra)|IaaS (Azure 上的替代方案為 CosmosDB)
|DataTorrent|IaaS 
|深入探詢|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (Azure 上的替代方案為 CosmosDB)
|NiFi|IaaS 
|Presto|IaaS 或 HDInsight 邊緣節點
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (Azure 上的替代方案為 SQLDW)
|Tableau|IaaS 
|Waterline|HDInsight 邊緣節點
|StreamSets|HDInsight edge 
|Palantir|IaaS 
|Sailpoint|Iaas 

如需詳細資訊，請參閱[可以搭配不同 HDInsight 版本使用的 Apache Hadoop 元件](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)一文

## <a name="customize-hdinsight-clusters-using-script-actions"></a>使用指令碼動作自訂 HDInsight 叢集

HDInsight 提供稱為**指令碼動作**的叢集設定方法。 指令碼動作是 Bash 指令碼，可在 HDInsight 叢集中的節點上執行，而且可用來安裝其他元件和變更組態設定。

指令碼動作必須儲存在可從 HDInsight 叢集存取的 URI 上。 您可以在建立叢集期間或建立叢集之後使用它們，也可以限制它們只在特定節點類型上執行。

指令碼可以是持續性或是一次性執行。 持續性指令碼可用來自訂透過調整規模作業新增至叢集的新背景工作節點。 持續性指令碼也可以在進行調整規模作業時，將變更套用至另一個節點類型，例如前端節點。

HDInsight 提供預先撰寫的指令碼以在 HDInsight 叢集上安裝下列元件：

- 新增 Azure 儲存體帳戶
- 安裝 Hue
- 安裝 Presto
- 安裝 Solr
- 安裝 Giraph
- 預先載入 Hive 程式庫
- 安裝或更新 Mono

> [!Note]  
> HDInsight 不提供對於自訂 Hadoop 元件或使用指令碼動作安裝之元件的直接支援。

指令碼動作也可以發佈到 Azure Marketplace 做為 HDInsight 應用程式。

如需詳細資訊，請參閱下列文章。

- [在 HDInsight 上安裝協力廠商 Apache Hadoop 應用程式](../hdinsight-apps-install-applications.md)
- [使用指令碼動作自訂 HDInsight 叢集](../hdinsight-hadoop-customize-cluster-linux.md)
- [將 HDInsight 應用程式發佈到 Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>使用 Bootstrap 自訂 HDInsight 設定

您可以使用 Bootstrap 針對設定檔 (例如 `core-site.xml`、`hive-site.xml` 和 `oozie-env.xml`) 中的設定進行變更。 下列腳本是使用 Powershell [AZ module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) Cmdlet [AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)的範例：

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

如需詳細資訊，請參閱 [使用 Bootstrap 自訂 HDInsight 叢集](../hdinsight-hadoop-customize-cluster-bootstrap.md)一文。  另請參閱[使用 Apache Ambari 管理 HDInsight 叢集 REST API](../hdinsight-hadoop-manage-ambari-rest-api.md)。

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>從 HDInsight Hadoop 叢集邊緣節點存取用戶端工具

空白邊緣節點是一部 Linux 虛擬機器，其中已安裝及設定和前端節點相同的用戶端工具，但未執行任何 Hadoop 服務。 邊緣端點的用途如下：

- 存取叢集
- 測試用戶端應用程式
- 裝載用戶端應用程式

邊緣節點可透過 Azure 入口網站來建立和刪除，也可以用在叢集建立期間或建立之後。 建立邊緣節點之後，您可以使用 SSH 連線到邊緣節點，並執行用戶端工具來存取 HDInsight 中的 Hadoop 叢集。 邊緣節點 SSH 端點為 `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`。


如需詳細資訊，請參閱[在 HDInsight 中的 Apache Hadoop 叢集上使用空白邊緣節點](../hdinsight-apps-use-edge-node.md)一文。


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>使用叢集的相應增加和相應減少功能

HDInsight 具有彈性，可讓您選擇擴大和縮小叢集中的背景工作節點數。 此功能可讓您在下班時間或是週末縮小叢集，並於業務需求達到高峰的期間擴大叢集。 如需詳細資訊，請參閱

* [調整 HDInsight](../hdinsight-scaling-best-practices.md)叢集。
* [調整叢集規模](../hdinsight-administer-use-portal-linux.md#scale-clusters)。

## <a name="use-hdinsight-with-azure-virtual-network"></a>搭配使用 HDInsight 與 Azure 虛擬網路

Azure 虛擬網路透過篩選和路由傳送網路流量，讓 Azure 資源 (例如 Azure 虛擬機器) 安全地彼此通訊，以及與網際網路和內部部署網路通訊。

使用 Azure 虛擬網路搭配 HDInsight 可實現下列案例：

- 從內部部署網路直接連線至 HDInsight。
- 將 HDInsight 連線至 Azure 虛擬網路中的資料存放區。
- 直接存取無法透過網際網路公開使用的 Hadoop 服務。 例如，Kafka API 或 HBase Java API。

HDInsight 可以新增到新的或現有的 Azure 虛擬網路。 如果 HDInsight 已新增到現有的虛擬網路，則需要更新現有的網路安全性群組和使用者定義的路由，以允許對 Azure 資料中心內的[數個 IP 位址](../hdinsight-management-ip-addresses.md)進行不受限制的存取。 此外，請確定不會封鎖對 HDInsight 服務所使用之[埠](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports)的流量。

> [!Note]  
> HDInsight 目前不支援強制通道。 強制通道是一種子網路設定，可強制裝置的輸出網際網路流量以進行檢查和記錄。 先移除強制通道，再將 HDInsight 安裝至子網路，或建立 HDInsight 的新子網路。 HDInsight 也不支援限制輸出網路連線。

如需詳細資訊，請參閱下列文章。

- [Azure 虛擬網路概觀](../../virtual-network/virtual-networks-overview.md)
- [使用 Azure 虛擬網路延伸 Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>使用 Azure 虛擬網路服務端點安全地連線到 Azure 服務

HDInsight 支援[虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)，可讓您安全地連接到 Azure Blob 儲存體、Azure Data Lake Storage Gen2、COSMOS DB 和 SQL 資料庫。 藉由啟用 Azure HDInsight 的服務端點，來自 Azure 資料中心內的流量就可流經受保護的路由。 透過此網路層的增強式安全性等級，您可以將巨量資料儲存體帳戶鎖定至其指定的虛擬網路 (VNET)，並繼續使用 HDInsight 叢集順暢地存取和處理其資料。

如需詳細資訊，請參閱下列文章。

- [虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [使用服務端點增強 HDInsight 安全性](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>將 HDInsight 連線至內部部署網路

HDInsight 可透過使用 Azure 虛擬網路和 VPN 閘道，連線到內部部署網路。 您可以使用下列步驟來建立連線：

- 在連線至內部部署網路的 Azure 虛擬網路中使用 HDInsight。
- 設定虛擬網路與內部部署網路之間的 DNS 名稱解析。
- 設定網路安全性群組或使用者定義路由 (UDR) 來控制網路流量。

如需詳細資訊，請參閱[將 HDInsight 連線至內部部署網路](../connect-on-premises-network.md)一文

## <a name="next-steps"></a>後續步驟

閱讀這一系列的下一篇文章：內部[部署到 Azure HDInsight Hadoop 遷移的儲存最佳作法](apache-hadoop-on-premises-migration-best-practices-storage.md)。
