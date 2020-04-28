---
title: 建立容器 Azure 監視器的效能警示 |Microsoft Docs
description: 本文說明如何根據 Azure 監視器容器的記憶體和 CPU 使用率，建立以記錄查詢為基礎的自訂警示。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75730920"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>如何在容器的 Azure 監視器中設定效能問題的警示

適用于容器的 Azure 監視器會監視部署至 Azure 容器實例或裝載于 Azure Kubernetes Service （AKS）之受管理 Kubernetes 叢集的容器工作負載效能。

本文說明如何在下列情況下啟用警示：

- 當叢集節點上的 CPU 或記憶體使用量超過閾值時
- 當控制器內任何容器的 CPU 或記憶體使用量超過閾值（相較于對應資源上所設定的限制）時
- *NotReady*狀態節點計數
- *失敗*、*暫*止、*不明*、*執行中或**成功*的 pod-階段計數
- 當叢集節點上的可用磁碟空間超過閾值時 

若要警示叢集節點上的高 CPU 或記憶體使用率，或可用磁碟空間不足，請使用所提供的查詢來建立計量警示或度量的度量警示。 計量警示的延遲低於記錄警示。 但是記錄警示提供先進的查詢和更複雜的。 「記錄警示」查詢會使用*now*運算子並返回一小時，比較日期時間與目前的狀況。 （Azure 監視器容器會以國際標準時間（UTC）格式儲存所有日期）。

如果您不熟悉 Azure 監視器警示，請先參閱[Microsoft Azure 中的警示總覽](../platform/alerts-overview.md)，再開始進行。 若要深入瞭解使用記錄查詢的警示，請參閱[Azure 監視器中的記錄警示](../platform/alerts-unified-log.md)。 如需計量警示的詳細資訊，請參閱[Azure 監視器中的計量警示](../platform/alerts-metric-overview.md)。

## <a name="resource-utilization-log-search-queries"></a>資源使用率記錄搜尋查詢

本節中的查詢支援每個警示案例。 它們是在本文的[建立警示](#create-an-alert-rule)一節的步驟7中使用。

下列查詢會以每分鐘成員節點的 CPU 使用率平均值，計算平均 CPU 使用率。  

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

下列查詢會計算每分鐘成員節點記憶體使用率的平均記憶體使用率。

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
>下列查詢會使用> 的預留位置\<值，以及\<您的-控制器名稱> 來代表您的叢集和控制器。 當您設定警示時，請以您環境特定的值取代它們。

下列查詢會計算控制器中所有容器的平均 CPU 使用率，做為控制器中每個容器實例的平均 CPU 使用率（每分鐘）。 量測是為容器設定的限制百分比。

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

下列查詢會計算控制器中所有容器的平均記憶體使用率，做為控制器中每一分鐘的每個容器實例的平均記憶體使用率。 量測是為容器設定的限制百分比。

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

下列查詢會傳回狀態為 [*就緒*] 和 [ *NotReady*] 的所有節點和計數。

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
下列查詢會根據所有階段傳回 pod 階段計數： [*失敗*]、[*擱置*] *、[**未知*]、[執行中] 或 [*成功*]。  

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
>若要針對特定 pod 階段（例如 [*擱置*]、[*失敗*] 或 [*未知*]）發出警示，請修改查詢的最後一行。 例如，若要在*failedcount 個*時發出警示，請使用： <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

下列查詢傳回的叢集節點磁片超過90% 的可用空間。 若要取得叢集識別碼，請先執行下列查詢，並從`ClusterId`屬性複製值：

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

請遵循下列步驟，使用稍早提供的其中一個記錄搜尋規則，在 Azure 監視器中建立記錄警示。 若要使用 ARM 範本建立，請參閱[使用 Azure 資源範本建立記錄警示的範例](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template)。

>[!NOTE]
>下列為容器資源使用量建立警示規則的程式，需要您切換到新的記錄警示 API，如[記錄警示的切換 API 喜好](../platform/alerts-log-api-switch.md)設定中所述。
>

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Azure 入口網站中，搜尋並選取 [ **Log Analytics 工作區**]。
3. 在您的 Log Analytics 工作區清單中，選取支援容器 Azure 監視器的工作區。 
4. 在左側窗格中，選取 [**記錄**] 以開啟 [Azure 監視器記錄] 頁面。 您可以使用此頁面來撰寫及執行 Azure Log Analytics 查詢。
5. 在 [**記錄**] 頁面上，將先前提供的其中一個[查詢](#resource-utilization-log-search-queries)貼入 [**搜尋查詢**] 欄位，然後選取 [**執行**] 來驗證結果。 如果您未執行此步驟，則無法選取 [ **+ 新增警示**] 選項。
6. 選取 [ **+ 新增警示**] 來建立記錄警示。
7. 在 [**條件**] 區段中，選取 [**每當自訂記錄\<搜尋是未定義的邏輯>** 預先定義的自訂記錄條件]。 系統會自動選取**自訂記錄搜尋**信號類型，因為我們會直接從 [Azure 監視器記錄] 頁面建立警示規則。  
8. 將稍早提供的其中一個[查詢](#resource-utilization-log-search-queries)貼入 [**搜尋查詢**] 欄位中。
9. 設定警示，如下所示：

    1. 從 [**根據**] 下拉式清單中，選取 [**度量度量**]。 計量測量會針對查詢中的每個物件建立警示，其值高於指定的閾值。
    1. 針對 [**條件**]，選取 [**大於**]，然後輸入**75**做為 CPU 和記憶體使用量警示的初始基準**閾值**。 針對 [磁碟空間不足] 警示，輸入**90**。 或者輸入不同的值，以符合您的準則。
    1. 在 [**觸發警示依據**] 區段中，選取 [**連續違規**]。 從下拉式清單選取 [**大於**]，然後輸入**2**。
    1. 若要設定容器 CPU 或記憶體使用率的警示，請在 [**匯總開啟**] 底下選取 [**容器**]。 若要設定叢集節點的低磁片警示，請選取 [ **ClusterId**]。
    1. 在 [**評估依據**] 區段中，將 [**期間**] 值設為**60 分鐘**。 此規則會每隔5分鐘執行一次，並傳回從目前時間開始的過去一小時內所建立的記錄。 將時間週期設定為寬視窗帳戶，以取得潛在的資料延遲。 它也可確保查詢會傳回資料，以避免不會引發警示的錯誤否定。

10. 選取 [**完成**] 以完成警示規則。
11. 在 [**警示規則名稱**] 欄位中輸入名稱。 指定提供警示詳細資料的**描述**。 然後從提供的選項中選取適當的嚴重性層級。
12. 若要立即啟用警示規則，請接受 [**在建立時啟用規則**] 的預設值。
13. 選取現有的**動作群組**或建立新的群組。 此步驟可確保每次觸發警示時，都會採取相同的動作。 根據 IT 或 DevOps 作業小組管理事件的方式進行設定。
14. 選取 [**建立警示規則**] 以完成警示規則。 此警示規則會立即開始執行。

## <a name="next-steps"></a>後續步驟

- 查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看預先定義的查詢和範例，以評估或自訂警示、視覺化或分析您的叢集。
- 若要深入瞭解 Azure 監視器以及如何監視 Kubernetes 叢集的其他層面，請參閱[View Kubernetes cluster performance](container-insights-analyze.md)和[view Kubernetes cluster health](container-insights-health.md)。
