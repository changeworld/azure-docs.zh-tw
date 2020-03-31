---
title: 群集管理最佳實踐 - Azure HDInsight
description: 瞭解管理 HDInsight 群集的最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782017"
---
# <a name="hdinsight-cluster-management-best-practices"></a>HDInsight 集群管理最佳實踐

瞭解管理 HDInsight 群集的最佳做法。

## <a name="how-do-i-create-hdinsight-clusters"></a>如何創建 HDInsight 群集？

| 選項 | 文件 |
|---|---|
| Azure Data Factory | [使用 Azure Data Factory 在 HDInsight 中建立隨選 Apache Hadoop 叢集](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| 自訂資源管理器範本 | [使用 Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| 快速入門範本 | [HDInsight 快速入門範本](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure 示例 | [HDInsight Azure 示例](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Azure 入口網站 | [使用 Azure 入口網站在 HDInsight 中建立 Linux 型叢集](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [使用 Azure CLI 建立 HDInsight 叢集](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [使用 Azure PowerShell 在 HDInsight 中建立以 Linux 為基礎的叢集](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [使用 Azure REST API 建立 Apache Hadoop 叢集](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK（.NET、Python、JAVA） | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)， [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)， [JAVA](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)，[去](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> 如果要創建群集並從以前創建的群集重新使用群集名稱，請等到上一個群集刪除完成後，再創建群集。

## <a name="how-do-i-customize-hdinsight-clusters"></a>如何自訂 HDInsight 群集？

| 選項 | 文件 |
|---|---|
| 指令碼動作 | [使用腳本操作自訂 Azure HDInsight 群集](./hdinsight-hadoop-customize-cluster-linux.md) |
| 啟動程序 | [使用 Bootstrap 自訂 HDInsight 叢集](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| 外部元存儲 | [在 Azure HDInsight 中使用外部中繼資料存放區](./hdinsight-use-external-metadata-stores.md) |
| 自訂 Ambari DB | [使用自訂 Ambari DB 設置 HDInsight 群集](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>創建群集時可能會遇到哪些錯誤？

| 錯誤 | 詳細資訊 |
|---|---|
| 無配額 | 每個區域的訂閱上可以創建的配額數有配額。 有關詳細資訊，請參閱[容量規劃：配額](./hdinsight-capacity-planning.md)。 |
| 沒有更多的可用 IP 位址 | 每個 VNet 的 IP 位址數量有限。 創建 HDInsight 群集時，每個節點（包括動物園管理員和閘道節點）使用其中分配的 IP 位址。 當所有 IP 位址都在使用中時，您會遇到此錯誤。  |
| 網路安全性群組 （NSG） 規則不允許與 HDInsight 資源供應商通信 | 如果使用 NSG 或使用者定義的路由 （UDR） 來控制到 HDInsight 群集的入站流量，則必須確保群集可以與關鍵的 Azure 運行狀況和管理服務進行通信。 有關詳細資訊，請參閱[Azure HDInsight 的網路安全性群組 （NSG） 服務標記](./hdinsight-service-tags.md) |
| 重用群集名稱 | 使用以前使用過的群集名稱時，需要等待 X 分鐘才能重新創建群集。 否則，您將看到一條消息，指出資源已存在。 |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>如何管理正在運行的 HDInsight 群集？

| 選項 | 文件 |
|---|---|
| Autoscale | [自動調整 Azure HDInsight 叢集規模](./hdinsight-autoscale-clusters.md) |
| 手動調整 | [縮放 Azure HDInsight 群集](./hdinsight-scaling-best-practices.md) |
| 與安巴里一起監測| [在 Azure HDInsight 中監視群集性能](./hdinsight-key-scenarios-to-monitor.md) |
| 使用 Azure 監視器日誌進行監視 | [使用 Azure 監視器記錄來監視 HDInsight 叢集](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>如何檢查已刪除的 HDInsight 群集？

### <a name="azure-monitor-logs"></a>Azure 監視器記錄

可以將以下查詢與 Azure 監視器日誌一起監視已刪除的群集。

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>後續步驟

* [HDInsight 叢集的容量規劃](./hdinsight-capacity-planning.md)
* [Azure HDInsight 的預設和推薦的節點配置是什麼？](./hdinsight-supported-node-configuration.md)