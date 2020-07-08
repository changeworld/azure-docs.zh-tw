---
title: 叢集管理最佳做法-Azure HDInsight
description: 瞭解管理 HDInsight 叢集的最佳作法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74782017"
---
# <a name="hdinsight-cluster-management-best-practices"></a>HDInsight 叢集管理最佳作法

瞭解管理 HDInsight 叢集的最佳作法。

## <a name="how-do-i-create-hdinsight-clusters"></a>如何? 建立 HDInsight 叢集？

| 選項 | 文件 |
|---|---|
| Azure Data Factory | [使用 Azure Data Factory 在 HDInsight 中建立隨選 Apache Hadoop 叢集](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| 自訂 Resource Manager 範本 | [使用 Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| 快速入門範本 | [HDInsight 快速入門範本](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure 範例 | [HDInsight Azure 範例](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Azure 入口網站 | [使用 Azure 入口網站在 HDInsight 中建立 Linux 型叢集](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [使用 Azure CLI 建立 HDInsight 叢集](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [使用 Azure PowerShell 在 HDInsight 中建立以 Linux 為基礎的叢集](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [使用 Azure REST API 建立 Apache Hadoop 叢集](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| Sdk （.NET、Python、JAVA） | [.Net](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)、 [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)、 [JAVA](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)、 [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> 如果您要建立叢集，並從先前建立的叢集重新使用叢集名稱，請等到先前的叢集刪除作業完成後，再建立您的叢集。

## <a name="how-do-i-customize-hdinsight-clusters"></a>如何? 自訂 HDInsight 叢集？

| 選項 | 文件 |
|---|---|
| 指令碼動作 | [使用指令碼動作來自訂 Azure HDInsight 叢集](./hdinsight-hadoop-customize-cluster-linux.md) |
| 啟動程序 | [使用 Bootstrap 自訂 HDInsight 叢集](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| 外部中繼存放區 | [在 Azure HDInsight 中使用外部中繼資料存放區](./hdinsight-use-external-metadata-stores.md) |
| 自訂 Ambari DB | [使用自訂 Ambari DB 設定 HDInsight 叢集](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>建立叢集時，我可能會面臨哪些錯誤？

| 錯誤 | 更多資訊 |
|---|---|
| 沒有配額 | 您可以在每個區域的訂用帳戶上建立配額數目配額。 如需詳細資訊，請參閱[容量規劃：配額](./hdinsight-capacity-planning.md)。 |
| 沒有其他可用的 IP 位址 | 每個 VNet 都有有限數目的 IP 位址。 當您建立 HDInsight 叢集時，每個節點（包括 zookeeper 和閘道節點）都會使用其中一些已分配的 IP 位址。 當所有的 IP 位址都在使用中時，您將會遇到此錯誤。  |
| 網路安全性群組（NSG）規則不允許與 HDInsight 資源提供者進行通訊 | 如果您使用 Nsg 或使用者定義的路由（Udr）來控制 HDInsight 叢集的輸入流量，您必須確定您的叢集可以與重要的 Azure 健康狀態和管理服務進行通訊。 如需詳細資訊，請參閱[Azure HDInsight 的網路安全性群組（NSG）服務標記](./hdinsight-service-tags.md) |
| 重複使用叢集名稱 | 當您使用先前使用過的叢集名稱時，您必須等候 X 分鐘數，再重新建立叢集。 否則，您會看到資源已經存在的訊息。 |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>如何? 管理執行中的 HDInsight 叢集？

| 選項 | 文件 |
|---|---|
| Autoscale | [自動調整 Azure HDInsight 叢集規模](./hdinsight-autoscale-clusters.md) |
| 手動調整 | [調整 Azure HDInsight 叢集](./hdinsight-scaling-best-practices.md) |
| 使用 Ambari 監視| [監視 Azure HDInsight 中的叢集效能](./hdinsight-key-scenarios-to-monitor.md) |
| 使用 Azure 監視器記錄監視 | [使用 Azure 監視器記錄來監視 HDInsight 叢集](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>如何? 在已刪除的 HDInsight 叢集上進行檢查嗎？

### <a name="azure-monitor-logs"></a>Azure 監視器記錄

您可以使用下列查詢搭配 Azure 監視器記錄來監視已刪除的叢集。

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>後續步驟

* [HDInsight 叢集的容量規劃](./hdinsight-capacity-planning.md)
* [Azure HDInsight 的預設和建議節點設定為何？](./hdinsight-supported-node-configuration.md)