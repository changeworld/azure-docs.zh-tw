---
title: 如何查詢容器 Azure 監視器的記錄 |Microsoft Docs
description: 適用于容器的 Azure 監視器會收集計量和記錄資料，而本文將說明記錄並包含範例查詢。
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: a540ae828b14f231b3b327b714876550cc744a7e
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994024"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>如何查詢容器 Azure 監視器的記錄

容器的 Azure 監視器會從容器主機和容器收集效能計量、清查資料和健全狀況狀態資訊。 資料會每三分鐘收集一次，並轉送至 Azure 監視器中的 Log Analytics 工作區。 這項資料可用於 Azure 監視器中的 [查詢](../log-query/log-query-overview.md) 。 您可以將此資料套用至各種案例，包括移轉規劃、容量分析、探索和隨選效能疑難排解。

## <a name="container-records"></a>容器資料列

在下表中，會提供容器 Azure 監視器所收集之記錄的詳細資料。 

| 資料 | 資料來源 | 資料類型 | 欄位 |
|------|-------------|-----------|--------|
| 容器清查 | Kubelet | `ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器記錄檔 | Docker | `ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器節點清查 | Kube API | `ContainerNodeInventory`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| 清查 Kubernetes 叢集中的 Pod | Kube API | `KubePodInventory` | TimeGenerated、Computer、ClusterId、ContainerCreationTimeStamp、PodUid、PodCreationTimeStamp、ContainerRestartCount、PodRestartCount、PodStartTime、ContainerStartTime、ServiceName、ControllerKind、ControllerName、ContainerStatus、ContainerStatusReason、ContainerID、容器名稱、Name、PodLabel、Namespace、PodStatus、ClusterName、PodIp、SourceSystem |
| 清查 Kubernetes 叢集中的節點部分 | Kube API | `KubeNodeInventory` | TimeGenerated、Computer、ClusterName、ClusterId、LastTransitionTimeReady、Labels、Status、KubeletVersion、KubeProxyVersion、CreationTimeStamp、SourceSystem | 
| Kubernetes 事件 | Kube API | `KubeEvents` | TimeGenerated、Computer、ClusterId_s、FirstSeen_t、LastSeen_t、Count_d、ObjectKind_s、Namespace_s、Name_s、Reason_s、Type_s、TimeGenerated_s、SourceComponent_s、ClusterName_s、Message、SourceSystem | 
| Kubernetes 叢集中的服務 | Kube API | `KubeServices` | TimeGenerated、ServiceName_s、Namespace_s、SelectorLabels_s、ClusterId_s、ClusterName_s、ClusterIP_s、ServiceType_s、SourceSystem | 
| Kubernetes 叢集節點部分的效能計量 | 使用計量是從 Kube api 的 cAdvisor 和限制取得 | 效能 &#124; 其中 ObjectName = = "K8SNode" | Computer、ObjectName、CounterName &#40;cpuAllocatableNanoCores、memoryAllocatableBytes、cpuCapacityNanoCores、memoryCapacityBytes、memoryRssBytes、cpuUsageNanoCores、memoryWorkingsetBytes、restartTimeEpoch&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| Kubernetes 叢集容器部分的效能計量 | 使用計量是從 Kube api 的 cAdvisor 和限制取得 | 效能 &#124; 其中 ObjectName = = "K8SContainer" | CounterName &#40; cpuRequestNanoCores、memoryRequestBytes、cpuLimitNanoCores、memoryWorkingSetBytes、restartTimeEpoch、cpuUsageNanoCores、memoryRssBytes&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| 自訂計量 ||`InsightsMetrics` | Computer、Name、Namespace、原點、SourceSystem、Tag<sup>1</sup>、TimeGenerated、Type、Va _ResourceId | 

<sup>1</sup> *標記* 屬性代表對應度量的 [多個維度](../platform/data-platform-metrics.md#multi-dimensional-metrics) 。 如需有關在資料表中收集和儲存的計量 `InsightsMetrics` 以及記錄屬性描述的詳細資訊，請參閱 [InsightsMetrics 總覽](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)。

## <a name="search-logs-to-analyze-data"></a>搜尋記錄來分析資料

Azure 監視器記錄可協助您尋找趨勢、診斷瓶頸、預測或關聯資料，以協助您判斷目前的叢集設定是否能以最佳方式執行。 已為您提供可立即開始使用，或自訂以您想要的方式傳回資訊的預先定義記錄搜尋。

您可以從 [**分析**] 下拉式清單中的 [預覽] 窗格中，選取 [ **view Kubernetes event logs** ] 或 [ **view container logs** ] 選項，以執行工作區中資料的互動式分析。 [記錄搜尋]**** 頁面會出現在您所在 Azure 入口網站頁面的右邊。

![在 Log Analytics 中分析資料](./media/container-insights-analyze/container-health-log-search-example.png)

轉送至您工作區的容器記錄輸出為 STDOUT 和 STDERR。 因為 Azure 監視器會監視 Azure 受控 Kubernetes (AKS)，所以，由於所產生的資料量很大，因此今天不會收集 Kube 系統。 

### <a name="example-log-search-queries"></a>範例記錄檔搜尋查詢

從一或兩個範例開始建置查詢，然後加以修改以滿足您的需求，通常很實用。 若要取得建置更進階查詢的協助，您可以試驗下列範例查詢：

| 查詢 | 描述 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出所有容器的生命週期資訊| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清查 | 
| **選取折線圖顯示選項**：<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| **選取折線圖顯示選項**：<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器記憶體 |
| InsightsMetrics<br> &#124;，其中 Name = = "requests_count"<br> &#124; 總結 Val = (Val) 依 TimeGenerated = bin (TimeGenerated，1m) <br> 依 TimeGenerated asc 排序的 &#124;<br> &#124; 專案 RequestsPerMinute = Val-上一個 (Val) ，TimeGenerated <br> &#124; 轉譯 barchart  | 使用自訂計量的每分鐘要求數 |

## <a name="query-prometheus-metrics-data"></a>查詢 Prometheus 計量資料

下列範例是 Prometheus 計量查詢，顯示每個節點每個磁片每秒的磁片讀取次數。

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

若要查看依 Azure 監視器依命名空間篩選而剪輯的 Prometheus 計量，請指定 "Prometheus"。 以下是從 kubernetes 命名空間中查看 Prometheus 計量的範例查詢 `default` 。

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus 資料也可以直接依名稱查詢。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>查詢設定或抓取錯誤

若要調查任何設定或抓取錯誤，下列查詢範例會傳回資料表中的資訊事件 `KubeMonAgentEvents` 。

```
KubeMonAgentEvents | where Level != "Info" 
```

輸出會顯示類似下列範例的結果：

![從代理程式記錄資訊事件的查詢結果](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>接下來的步驟

容器的 Azure 監視器不包含一組預先定義的警示。 請參閱 [容器的 Azure 監視器建立效能警示](./container-insights-log-alerts.md) ，以瞭解如何建立高 CPU 和記憶體使用量的建議警示，以支援您的 DevOps 或操作程式和程式。