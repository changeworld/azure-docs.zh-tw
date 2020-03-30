---
title: 為容器創建 Azure 監視器的性能警報 |微軟文檔
description: 本文介紹如何基於日誌查詢創建自訂警報，以便從 Azure 監視器為容器創建記憶體和 CPU 利用率。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730920"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>如何在容器的 Azure 監視器中設定效能問題的警示

容器的 Azure 監視器監視部署到 Azure 容器實例或託管在 Azure 庫伯奈斯服務 （AKS） 上託管的庫伯奈斯群集的容器工作負荷的性能。

本文介紹如何為以下情況啟用警報：

- 當叢集節點上的 CPU 或記憶體利用率超過閾值時
- 當控制器內任何容器上的 CPU 或記憶體利用率超過閾值時，與在相應資源上設置的限制相比
- *未就緒*狀態節點計數
- *失敗*、*掛起*、*未知*、*正在運行*或*成功*pod 階段計數
- 當叢集節點上的可用磁碟空間超過閾值時 

要警報 CPU 或記憶體利用率高或叢集節點上的可用磁碟空間不足，請使用提供的查詢創建指標警報或指標度量警報。 指標警報的延遲低於日誌警報。 但是日誌警報提供了高級查詢和更高的複雜性。 日誌警報查詢通過使用*現在*運算子並將日期時間與目前時間進行比較，然後返回一小時。 （容器的 Azure 監視器以協調通用時間 （UTC） 格式存儲所有日期。

如果您不熟悉 Azure 監視器警報，請參閱在開始之前在[Microsoft Azure 中查看警報概述](../platform/alerts-overview.md)。 要瞭解有關使用日誌查詢的警報的更多，請參閱 Azure[監視器 中的日誌警報](../platform/alerts-unified-log.md)。 有關指標警報的更多，請參閱[Azure 監視器 中的指標警報](../platform/alerts-metric-overview.md)。

## <a name="resource-utilization-log-search-queries"></a>資源利用率日誌搜索查詢

本節中的查詢支援每個警報方案。 它們用於本文[創建警報](#create-an-alert-rule)部分的步驟 7。

以下查詢將平均 CPU 利用率計算為成員節點每分鐘 CPU 利用率的平均值。  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

以下查詢將平均記憶體利用率計算為每分鐘成員節點的記憶體利用率平均值。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>以下查詢使用預留位置值\<群集名稱>和\<控制器名稱>來表示群集和控制器。 設置警報時，將它們替換為特定于環境的值。

以下查詢計算控制器中所有容器的平均 CPU 利用率，作為控制器中每分鐘每個容器實例的 CPU 利用率平均值。 測量是為容器設置的限制的百分比。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

以下查詢計算控制器中所有容器的平均記憶體利用率，作為控制器中每分鐘每個容器實例的記憶體利用率平均值。 測量是為容器設置的限制的百分比。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

以下查詢返回狀態為 *"就緒*"和"*未就緒*"的所有節點和計數。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
以下查詢基於所有階段返回 pod 階段計數：*失敗*、*掛起*、*未知*、*正在運行*或*成功*。  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>要在某些 Pod 階段（如*掛起*、*失敗*或*未知）* 發出警報，請修改查詢的最後一行。 例如，要在*故障計數*使用時發出警報： <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

以下查詢返回超過 90% 可用空間的叢集節點磁片。 要獲取群集 ID，請首先運行以下查詢並從 屬性`ClusterId`複製該值：

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>建立警示規則

按照以下步驟使用前面提供的日誌搜索規則之一，在 Azure 監視器中創建日誌警報。 要使用 ARM 範本創建，請參閱[使用 Azure 資源範本創建示例日誌警報](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template)。

>[!NOTE]
>以下為容器資源利用率創建警報規則的過程要求您切換到新的日誌警報 API，如[日誌警報的 Switch API 首選項](../platform/alerts-log-api-switch.md)中所述。
>

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在 Azure 門戶中，搜索並選擇**日誌分析工作區**。
3. 在日誌分析工作區清單中，選擇支援容器 Azure 監視器的工作區。 
4. 在左側的窗格中，選擇 **"日誌"** 以打開 Azure 監視器日誌頁。 使用此頁面編寫和執行 Azure 日誌分析查詢。
5. 在 **"日誌"** 頁上，將前面提供的[查詢](#resource-utilization-log-search-queries)之一粘貼到 **"搜索"查詢**欄位中，然後選擇 **"運行"** 以驗證結果。 如果不執行此步驟，則無法選擇 **_New 警報**選項。
6. 選擇 **"新建警報**"以創建日誌警報。
7. 在 **"條件"** 部分中，選擇 **"每當自訂日誌搜索\<邏輯未定義>** 預定義的自訂日誌條件時。 **自訂日誌搜索**信號類型會自動選擇，因為我們直接從 Azure 監視器日誌頁創建警報規則。  
8. 將前面提供的[查詢](#resource-utilization-log-search-queries)之一粘貼到 **"搜索"查詢**欄位中。
9. 按如下方式配置警報：

    1. 從 **"基於**下拉清單"中，選擇 **"指標度量**"。 指標度量為查詢中的每個物件創建一個警報，該物件的值高於我們指定的閾值。
    1. 對於**條件**，選擇**大於**，然後輸入**75**作為 CPU 和記憶體利用率警報的初始基線**閾值**。 對於磁碟空間不足警報，輸入**90**。 或者輸入符合條件的不同值。
    1. 在 **"基於觸發警報"** 部分中，選擇 **"連續違規**"。 從下拉清單中，選擇 **"大於**"，然後輸入**2**。
    1. 要配置容器 CPU 或記憶體利用率的警報，請在 **"聚合"下**選擇**容器名稱**。 要配置叢集節點低磁片警報，請選擇**群集 ID**。
    1. 在 **"基於"部分的評估"** 中，將 **"期間"** 值設置為**60 分鐘**。 該規則將每 5 分鐘運行一次，並返回在目前時間最後一小時內創建的記錄。 將時間段設置為寬視窗，考慮潛在的資料延遲。 它還確保查詢返回資料以避免警報從不觸發的假負數。

10. 選擇 **"完成"** 以完成警報規則。
11. 在 **"警報規則名稱"** 欄位中輸入名稱。 指定提供有關警報的詳細資訊**的說明**。 並從提供的選項中選擇適當的嚴重性級別。
12. 要立即啟動警報規則，請在創建 時接受**啟用規則的**預設值。
13. 選擇現有**操作組**或創建新組。 此步驟可確保每次觸發警報時都執行相同的操作。 根據 IT 或 DevOps 運營團隊管理事件的方式進行配置。
14. 選擇 **"創建警報規則**"以完成警報規則。 此警示規則會立即開始執行。

## <a name="next-steps"></a>後續步驟

- 查看[日誌查詢示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看預定義的查詢和示例，以評估或自訂以用於警報、視覺化或分析群集。
- 要瞭解有關 Azure 監視器以及如何監視庫伯奈斯群集的其他方面，請參閱[查看庫伯奈斯群集性能](container-insights-analyze.md)和[查看庫伯奈斯群集運行狀況](container-insights-health.md)。
