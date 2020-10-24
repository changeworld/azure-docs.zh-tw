---
title: 使用 Azure 監視器記錄來監視 Azure HDInsight 叢集
description: 了解如何使用 Azure 監視器記錄來監視在 HDInsight 叢集中執行的作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 5524c7625678d3bacc5fdbe3c295d8392da2280f
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490997"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>使用 Azure 監視器記錄來監視 HDInsight 叢集

了解如何啟用 Azure 監視器記錄來監視 HDInsight 中的 Hadoop 叢集作業。 以及如何新增 HDInsight 監視解決方案。

[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)是監視您的雲端和內部部署環境的 Azure 監視器服務。 監視是要維護其可用性和效能。 其會收集您的雲端、內部部署環境，以及從其他監視工具中的資源所產生的資料。 該資料可用來提供跨多個來源的分析。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

* Log Analytics 工作區。 您可以將此工作區視為唯一的 Azure 監視器記錄環境，有其自己的資料存放庫、資料來源和方案。 如需指示，請參閱[建立 Log Analytics 工作區](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)。

* Azure HDInsight 叢集。 目前，您可以使用具有以下 HDInsight 叢集類型的 Azure 監視器記錄：

  * Hadoop
  * hbase
  * 互動式查詢
  * Kafka
  * Spark
  * Storm

  如需如何建立 HDInsight 叢集的指示，請參閱[開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)。  

* 如果使用 PowerShell，您將需要 [Az 模組](https://docs.microsoft.com/powershell/azure/) \(部分機器翻譯\)。 確認您擁有最新版本。 如有必要，請執行 `Update-Module -Name Az`。

* 如果您想要使用 Azure CLI，但尚未安裝，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

> [!NOTE]  
> 建議您將 HDInsight 叢集和 Log Analytics 工作區放在相同的區域中，以提升效能。 Azure 監視器記錄並非適用於所有 Azure 區域。

## <a name="enable-azure-monitor-using-the-portal"></a>使用入口網站啟用 Azure 監視器

在本節中，您會設定現有 HDInsight Hadoop 叢集，以使用 Azure Log Analytics 工作區來監視作業、偵錯記錄等等。

1. 從 [Azure 入口網站](https://portal.azure.com/)中，選取您的叢集。 叢集會在新的入口網站分頁中開啟。

1. 從左側的 [監視] 底下，選取 [Azure 監視器]。

1. 從主要檢視的 [Azure 監視器整合] 底下，選取 [啟用]。

1. 從 [選取工作區] 下拉式清單中，選取現有的 Log Analytics 工作區。

1. 選取 [儲存]。  需要一些時間來儲存設定。

    ![啟用 HDInsight 叢集的監視](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "啟用 HDInsight 叢集的監視")

## <a name="enable-azure-monitor-using-azure-powershell"></a>使用 Azure PowerShell 啟用 Azure 監視器

您可以使用 Azure PowerShell Az 模組 [Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) Cmdlet 來啟用 Azure 監視器記錄。

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

若要停用，請使用 [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) Cmdlet：

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>使用 Azure CLI 啟用 Azure 監視器

您可以使用 Azure CLI `[az hdinsight monitor enable` ] (/cli/azure/hdinsight/monitor # az-hdinsight-enable) 命令來啟用 Azure 監視器記錄。

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

若要停用，請使用 [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az-hdinsight-monitor-disable) 命令。

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>安裝 HDInsight 叢集管理解決方案

HDInsight 提供叢集特定的管理解決方案，您可以為 Azure 監視器記錄新增。 [管理解決方案](../log-analytics/log-analytics-add-solutions.md)可將功能新增至 Azure 監視器記錄，以提供其他資料和分析工具。 這些解決方案會從您的 HDInsight 叢集收集重要效能計量。 並且提供工具以搜尋計量。 這些解決方案也會針對 HDInsight 中支援的大部分叢集類型，提供視覺效果和儀表板。 藉由使用您以解決方案收集的計量，您可以建立自訂的監視規則和警示。

可用的 HDInsight 解決方案：

* HDInsight Hadoop 監視
* HDInsight HBase 監視
* HDInsight 互動式查詢監視
* HDInsight Kafka 監視
* HDInsight Spark 監視
* HDInsight Storm 監視

如需管理解決方案的指示，請參閱[在 Azure 中的管理解決方案](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)。 若要進行實驗，請安裝 HDInsight Hadoop 監視解決方案。 完成後，您會看到 **HDInsightHadoop** 圖格列示於 [摘要] 下方。 選取 **HDInsightHadoop** 圖格。 HDInsightHadoop 解決方案顯示如下：

![HDInsight 監視解決方案檢視](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

由於叢集是全新的叢集，因此報表不會顯示任何活動。

## <a name="configuring-performance-counters"></a>設定效能計數器

Azure 監視器支援收集和分析叢集中節點的效能計量。 如需詳細資訊，請參閱 [Azure 監視器中的 Linux 效能資料來源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters) \(部分機器翻譯\)。

## <a name="cluster-auditing"></a>叢集稽核

藉由匯入下列類型的記錄，HDInsight 支援使用 Azure 監視器記錄進行叢集稽核：

* `log_gateway_audit_CL` - 此資料表提供來自叢集閘道節點的稽核記錄，以顯示成功和失敗的登入嘗試。
* `log_auth_CL` - 此資料表提供 SSH 記錄，其中包含成功和失敗的登入嘗試。
* `log_ambari_audit_CL` - 此資料表提供來自 Ambari 的稽核記錄。
* `log_ranger_audti_CL` - 此資料表提供 ESP 叢集上來自 Apache Ranger 的稽核記錄。

## <a name="next-steps"></a>後續步驟

* [查詢 Azure 監視器記錄來監視 HDInsight 叢集](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [如何使用 Apache Ambari 和 Azure 監視器記錄監視叢集可用性](./hdinsight-cluster-availability.md)
