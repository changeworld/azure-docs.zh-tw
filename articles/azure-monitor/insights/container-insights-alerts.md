---
title: 建立容器 Azure 監視器的效能警示 |Microsoft Docs
description: 本文說明如何根據容器 Azure 監視器的記憶體和 CPU 使用量記錄查詢來建立自訂警示。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 1041b8bfe299a0daac633e478c906c746759d50f
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607306"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>如何在容器的 Azure 監視器中設定效能問題的警示

容器的 Azure 監視器會監視部署至 Azure 容器實例的容器工作負載效能，或監視裝載于 Azure Kubernetes Service (AKS) 上的受控 Kubernetes 叢集。

本文說明如何在下列情況下啟用警示：

- 當叢集節點上的 CPU 或記憶體使用量超過閾值時
- 如果控制器內任何容器的 CPU 或記憶體使用量超過閾值（相較于對應資源上設定的限制）
- *NotReady* 狀態節點計數
- *失敗*、*暫*止、*未知*、執行或*成功**的 pod*階段計數
- 叢集節點上的可用磁碟空間超過閾值時 

若要警示高 CPU 或記憶體使用率，或叢集節點上的可用磁碟空間不足，請使用提供的查詢來建立計量警示或計量測量警示。 計量警示的延遲低於記錄警示。 但是記錄警示可提供先進的查詢和更複雜的查詢。 記錄警示查詢會使用 *now* 運算子來比較 datetime 與目前的結果，並返回一小時。 容器的 (Azure 監視器會以國際標準時間 (UTC) 格式來儲存所有日期。 ) 

如果您不熟悉 Azure 監視器警示，請在開始之前參閱 [Microsoft Azure 中的警示總覽](../platform/alerts-overview.md) 。 若要深入瞭解使用記錄查詢的警示，請參閱 [Azure 監視器中的記錄警示](../platform/alerts-unified-log.md)。 如需計量警示的詳細資訊，請參閱 [Azure 監視器中](../platform/alerts-metric-overview.md)的計量警示。

## <a name="resource-utilization-log-search-queries"></a>資源使用率記錄搜尋查詢

本節中的查詢支援每個警示案例。 這些專案是在本文的「 [建立警示](#create-an-alert-rule) 」一節的步驟7中使用。

下列查詢會將平均 CPU 使用率計算為每分鐘成員節點 CPU 使用率的平均 CPU 使用率。  

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

下列查詢會計算平均記憶體使用率，以平均每分鐘成員節點的記憶體使用率。

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
>下列查詢會使用預留位置值 \<your-cluster-name> ，並 \<your-controller-name> 代表您的叢集和控制器。 當您設定警示時，請將其取代為您環境的特定值。

下列查詢會將控制器中所有容器的平均 CPU 使用率，計算為控制器中每個容器實例的平均 CPU 使用率（以每分鐘為單位）。 測量是針對容器設定的限制百分比。

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

下列查詢會計算控制器中所有容器的平均記憶體使用量，作為控制器中每個容器實例每分鐘的平均記憶體使用率。 測量是針對容器設定的限制百分比。

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

下列查詢會傳回狀態為 [ *就緒* ] 和 [ *NotReady*] 的所有節點和計數。

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
下列查詢會根據所有階段（*失敗*、*暫*止、*不明*、執行中或成功 *）來*傳回*Succeeded*pod 階段計數。  

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
>若要對某些 pod 階段發出警示（例如 [ *擱置*]、[ *失敗*] 或 [ *未知*]），請修改查詢的最後一行。 例如，若要在 *failedcount 個* 上發出警示，請使用： <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

下列查詢會傳回超過90% 可用空間的叢集節點磁片。 若要取得叢集識別碼，請先執行下列查詢，並從屬性複製值 `ClusterId` ：

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

依照下列步驟，使用稍早提供的其中一個記錄搜尋規則，在 Azure 監視器中建立記錄警示。 若要使用 ARM 範本建立，請參閱 [使用 Azure 資源範本建立記錄警示的範例](../platform/alerts-log-create-templates.md)。

>[!NOTE]
>若要建立容器資源使用率的警示規則，您必須切換至新的記錄警示 API，如「 [記錄警示的參數 API 喜好](../platform/alerts-log-api-switch.md)設定」中所述。
>

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站中，搜尋並選取 **Log Analytics 工作區**。
3. 在您的 Log Analytics 工作區清單中，選取支援容器 Azure 監視器的工作區。 
4. 在左側窗格中，選取 [ **記錄** ] 以開啟 [Azure 監視器記錄] 頁面。 您可以使用此頁面來撰寫及執行 Azure Log Analytics 查詢。
5. 在 [ **記錄** 檔] 頁面上，將稍早提供的其中一個 [查詢](#resource-utilization-log-search-queries) 貼到 **搜尋查詢** 欄位中，然後選取 [ **執行** ] 以驗證結果。 如果您未執行此步驟，則不能選取 [ **+ 新增警示** ] 選項。
6. 選取 [ **+ 新增警示** ] 以建立記錄警示。
7. 在 [**條件**] 區段中，選取 [**每當自訂記錄 \<logic undefined> 搜尋是**預先定義的自訂記錄條件時]。 系統會自動選取 **自訂記錄搜尋** 信號類型，因為我們會直接從 Azure 監視器記錄] 頁面建立警示規則。  
8. 將稍早提供的其中一個 [查詢](#resource-utilization-log-search-queries) 貼到 **搜尋查詢** 欄位中。
9. 設定警示，如下所示：

    1. 從 [依據] 下拉式清單中，選取 [計量測量]。 計量測量會針對查詢中值超過指定閾值的每個物件建立警示。
    1. 針對 [ **條件**]，選取 [ **大於**]，然後輸入 **75** 作為 CPU 和記憶體使用量警示的初始基準 **閾值** 。 針對 [磁碟空間不足] 警示，輸入 **90**。 或輸入符合準則的其他值。
    1. 在 [ **觸發程式警示依據** ] 區段中，選取 [ **連續違規**]。 從下拉式清單中選取 [ **大於**]，然後輸入 **2**。
    1. 若要設定容器 CPU 或記憶體使用量的警示，請在 [ **匯總依據**] 底下選取 [ **容器容器**]。 若要設定叢集節點磁碟空間不足的警示，請選取 [ **ClusterId**]。
    1. 在 [ **評估依據** ] 區段中，將 [ **期間** ] 值設定為 **60 分鐘**。 此規則會每隔5分鐘執行一次，並傳回在目前時間從過去一小時內建立的記錄。 將時間週期設定為寬範圍的帳戶，以取得潛在的資料延遲。 它也可確保查詢會傳回資料，以避免警示永遠不會引發的錯誤負。

10. 選取 [ **完成** ] 以完成警示規則。
11. 在 [ **警示規則名稱** ] 欄位中輸入名稱。 指定提供警示詳細資料的 **描述** 。 並從提供的選項中選取適當的嚴重性層級。
12. 若要立即啟用警示規則，請接受 [ **建立時啟用規則**] 的預設值。
13. 選取現有的 **動作群組** 或建立新的群組。 此步驟可確保在每次觸發警示時都會採取相同的動作。 根據 IT 或 DevOps 作業小組管理事件的方式進行設定。
14. 選取 [ **建立警示規則** ] 以完成警示規則。 此警示規則會立即開始執行。

## <a name="next-steps"></a>後續步驟

- 查看 [記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data) 以查看預先定義的查詢和範例，以評估或自訂警示、視覺化或分析您的叢集。
- 若要深入瞭解 Azure 監視器以及如何監視 Kubernetes 叢集的其他層面，請參閱 [View Kubernetes cluster performance](container-insights-analyze.md) And [view Kubernetes cluster health](container-insights-health.md)。
