---
title: 使用 Azure 監視器日誌監視 Azure HDInsight 群集
description: 瞭解如何使用 Azure 監視器日誌監視在 HDInsight 群集中運行的作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162781"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>使用 Azure 監視器記錄來監視 HDInsight 叢集

瞭解如何啟用 Azure 監視器日誌以監視 HDInsight 中的 Hadoop 群集操作，以及如何添加 HDInsight 監視解決方案。

[Azure 監視器日誌](../log-analytics/log-analytics-overview.md)是 Azure 監視器中的一項服務，用於監視雲和本地環境，以保持其可用性和性能。 它會收集您的雲端和內部部署環境中的資源所產生的資料，以及從其他監視工具提供橫跨多個來源的分析。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

如果沒有 Azure 訂閱，請先[創建一個免費帳戶](https://azure.microsoft.com/free/)。"

## <a name="prerequisites"></a>Prerequisites

* Log Analytics 工作區。 您可以將此工作區視為具有自己的資料存儲庫、資料來源和解決方案的唯一 Azure 監視器日誌環境。 如需指示，請參閱[建立 Log Analytics 工作區](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)。

* Azure HDInsight 叢集。 目前，您可以將 Azure 監視器日誌與以下 HDInsight 群集類型一起使用：

  * Hadoop
  * hbase
  * 互動式查詢
  * Kafka
  * Spark
  * Storm

  如需如何建立 HDInsight 叢集的指示，請參閱[開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)。  

* Azure 電源殼 Az 模組。  請參閱[介紹新的 Azure PowerShell Az 模組](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)。 確保您擁有最新版本。 如有必要，運行`Update-Module -Name Az`。

> [!NOTE]  
> 建議您將 HDInsight 叢集和 Log Analytics 工作區放在相同的區域中，以提升效能。 Azure 監視器日誌並非在所有 Azure 區域都可用。

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>使用門戶啟用 Azure 監視器日誌

在本節中，您會設定現有 HDInsight Hadoop 叢集，以使用 Azure Log Analytics 工作區來監視作業、偵錯記錄等等。

1. 從[Azure 門戶](https://portal.azure.com/)中選擇群集。  請參閱[列出和顯示叢集](./hdinsight-administer-use-portal-linux.md#showClusters)以取得指示。 群集在新門戶頁中打開。

1. 從左側，在 **"監視"** 下，選擇**Azure 監視器**。

1. 從主視圖，在**Azure 監視器集成**下，選擇**啟用**。

1. 從 [選取工作區]**** 下拉式清單中，選取現有的 Log Analytics 工作區。

1. 選取 [儲存]****。  需要一些時間來儲存設定。

    ![啟用對 HDInsight 群集的監控](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "啟用對 HDInsight 群集的監控")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>使用 Azure PowerShell 啟用 Azure 監視器日誌

您可以使用 Azure PowerShell Az 模組[啟用-AzHDInsight 監視](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring)Cmdlet 啟用 Azure 監視器日誌。

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

要禁用，請使用[禁用-AzHDInsight 監控](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)Cmdlet：

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>安裝 HDInsight 叢集管理解決方案

HDInsight 提供了特定于群集的管理解決方案，您可以為 Azure 監視器日誌添加這些解決方案。 [管理解決方案](../log-analytics/log-analytics-add-solutions.md)向 Azure 監視器日誌添加功能，提供其他資料和分析工具。 這些解決方案會從您的 HDInsight 叢集收集重要效能計量，並且提供工具以搜尋計量。 這些解決方案也會針對 HDInsight 中支援的大部分叢集類型，提供視覺效果和儀表板。 藉由使用您以解決方案收集的計量，您可以建立自訂的監視規則和警示。

可用的 HDInsight 解決方案如下：

* HDInsight Hadoop 監視
* HDInsight HBase 監視
* HDInsight 互動式查詢監視
* HDInsight Kafka 監視
* HDInsight Spark 監視
* HDInsight Storm 監視

如需安裝管理解決方案的指示，請參閱[在 Azure 中的管理解決方案](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)。 要進行試驗，請安裝 HDInsight Hadoop 監控解決方案。 完成後，您將看到**摘要**下列出的**HDInsightHadoop**磁貼。 選取 **HDInsightHadoop** 圖格。 HDInsightHadoop 解決方案顯示如下：

![HDInsight 監視解決方案檢視](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

由於叢集是全新的叢集，因此報表不會顯示任何活動。

## <a name="configuring-performance-counters"></a>配置效能計數器

Azure 監視器還支援收集和分析群集中節點的性能指標。 有關啟用和配置此功能的詳細資訊，請參閱[Azure 監視器 中的 Linux 效能資料源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters)。

## <a name="cluster-auditing"></a>群集審核

HDInsight 通過導入以下類型的日誌來支援使用 Azure 監視器日誌進行群集審核：

* `log_gateway_audit_CL`- 此表提供來自群集閘道節點的稽核記錄，這些節點顯示成功的登錄嘗試和失敗的登錄嘗試。
* `log_auth_CL`- 此表為 SSH 日誌提供成功且失敗的登錄嘗試。
* `log_ambari_audit_CL`- 此表提供來自 Ambari 的稽核記錄。
* `log_ranger_audti_CL`- 此表在 ESP 群集上提供來自 Apache Ranger 的稽核記錄。

## <a name="next-steps"></a>後續步驟

* [查詢 Azure 監視器日誌以監視 HDInsight 群集](hdinsight-hadoop-oms-log-analytics-use-queries.md)
