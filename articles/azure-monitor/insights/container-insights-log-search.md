---
title: 如何從 Azure 監視器查詢容器日誌 |微軟文檔
description: 容器的 Azure 監視器收集指標和日誌資料，本文介紹記錄並包含依例查詢。
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333468"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>如何查詢來自 Azure 監視器的容器日誌

容器的 Azure 監視器從容器主機和容器收集性能指標、清單資料和運行狀況狀態資訊，並將其轉發到 Azure 監視器中的日誌分析工作區。 每隔三分鐘會收集一次資料。 此資料可用於 Azure 監視器中的[查詢](../../azure-monitor/log-query/log-query-overview.md)。 您可以將此資料套用至各種案例，包括移轉規劃、容量分析、探索和隨選效能疑難排解。

## <a name="container-records"></a>容器資料列

下表顯示適用於容器的 Azure 監視器所收集的記錄範例，以及記錄搜尋結果中所顯示的資料類型：

| 資料類型 | 記錄檔搜尋中的資料類型 | 欄位 |
| --- | --- | --- |
| 主機和容器的效能 | `Perf` | Computer、ObjectName、CounterName &#40;%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器清查 | `ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器記錄檔 | `ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器節點清查 | `ContainerNodeInventory`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| 清查 Kubernetes 叢集中的 Pod | `KubePodInventory` | 時間生成，電腦，群集 Id，容器創建時間戳記，PodUid，Pod創造時間戳記，容器重新開機計數，PodrestartCount，PodStarttime，容器開始時間，服務名稱，控制器金德，控制器名稱，容器狀態， 容器狀態原因、容器 ID、容器名稱、名稱、PodLabel、命名空間、Pod 狀態、群集名稱、PodIp、源系統 |
| 清查 Kubernetes 叢集中的節點部分 | `KubeNodeInventory` | TimeGenerated、Computer、ClusterName、ClusterId、LastTransitionTimeReady、Labels、Status、KubeletVersion、KubeProxyVersion、CreationTimeStamp、SourceSystem | 
| Kubernetes 事件 | `KubeEvents` | TimeGenerated、Computer、ClusterId_s、FirstSeen_t、LastSeen_t、Count_d、ObjectKind_s、Namespace_s、Name_s、Reason_s、Type_s、TimeGenerated_s、SourceComponent_s、ClusterName_s、Message、SourceSystem | 
| Kubernetes 叢集中的服務 | `KubeServices` | TimeGenerated、ServiceName_s、Namespace_s、SelectorLabels_s、ClusterId_s、ClusterName_s、ClusterIP_s、ServiceType_s、SourceSystem | 
| Kubernetes 叢集節點部分的效能計量 | 在物件名稱 = "K8Snode"的 &#124; | 電腦、 物件名稱、 計數器名稱&#40;cpuAllocaab、 記憶體可無價位元組、 cpu容量納米、 記憶體容量位元組、 記憶體位元組、 cpuUsageNanocores、 記憶體工作集位元組、 重新開機時間時間&#41;、 計數器值、 時間生成、 反路徑、 源系統 | 
| Kubernetes 叢集容器部分的效能計量 | 在物件名稱 = "K8S 容器"的位置，perf &#124; | 計數器名稱&#40; cpuRequestNanocores、記憶體請求位元組、cpuLimitNanocores、記憶體工作集位元組、重新開機TimeEpoch、cpuUsageNanocores、記憶體&#41;、計數器值、時間生成、反路徑、源系統 | 
| 自訂計量 |`InsightsMetrics` | 電腦， 名稱， 命名空間， 原點， 源系統， 標籤<sup>1</sup>， 時間生成， 類型， Va， _ResourceId | 

<sup>1</sup> *標記*屬性工作表示相應指標的[多個維度](../platform/data-platform-metrics.md#multi-dimensional-metrics)。 有關在`InsightsMetrics`表中收集和存儲的指標的其他資訊以及記錄屬性的說明，請參閱[InsightsMetrics 概述](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)。

## <a name="search-logs-to-analyze-data"></a>搜尋記錄來分析資料

Azure 監視器日誌可説明您查找趨勢、診斷瓶頸、預測或關聯資料，這些資料可説明您確定當前群集配置是否以最佳方式執行。 已為您提供可立即開始使用，或自訂以您想要的方式傳回資訊的預先定義記錄搜尋。

您可以通過在"在分析下拉清單中**的視圖"** 下拉清單中的"**視圖庫伯奈斯"事件日誌**或"查看容器日誌"窗格中選擇"查看 Kubernetes 事件日誌"或 **"查看容器日誌"** 選項，對工作區中的資料執行互動式分析。 [記錄搜尋]**** 頁面會出現在您所在 Azure 入口網站頁面的右邊。

![在 Log Analytics 中分析資料](./media/container-insights-analyze/container-health-log-search-example.png)

轉發到工作區的容器日誌輸出是 STDOUT 和 STDERR。 因為 Azure 監視器會監視 Azure 受控 Kubernetes (AKS)，所以，由於所產生的資料量很大，因此今天不會收集 Kube 系統。 

### <a name="example-log-search-queries"></a>範例記錄檔搜尋查詢

從一或兩個範例開始建置查詢，然後加以修改以滿足您的需求，通常很實用。 若要取得建置更進階查詢的協助，您可以試驗下列範例查詢：

| 查詢 | 描述 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出所有容器的生命週期資訊| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清查 | 
| **選取折線圖顯示選項**：<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| **選取折線圖顯示選項**：<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器記憶體 |
| 見解指標<br> 名稱 = "requests_count"&#124;<br> &#124;按時間生成\bin匯總 Val_any（Val）（時間生成，1m）<br> 按時間生成 asc 排序&#124;<br> &#124;專案請求PerMine = Val - 前（Val），時間生成 <br> &#124;渲染條圖  | 使用自訂指標每分鐘請求 |

## <a name="query-prometheus-metrics-data"></a>查詢普羅米塞烏斯指標資料

下面的示例是 Prometheus 指標查詢，顯示每個節點每磁片每秒讀取磁片。

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

要查看由按命名空間篩選的 Azure 監視器刮擦的 Prometheus 指標，請指定"原型"。 下面是一個依例查詢，用於查看來自庫伯奈斯命名空間`default`的 Prometheus 指標。

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

普羅米古斯資料也可以按名稱直接查詢。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>查詢配置或刮擦錯誤

要調查任何配置或刮擦錯誤，以下依例查詢將從`KubeMonAgentEvents`表中返回資訊事件。

```
KubeMonAgentEvents | where Level != "Info" 
```

輸出將顯示類似于以下內容的結果：

![從代理記錄資訊事件的查詢結果](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>後續步驟

容器的 Azure 監視器不包括預定義的警報集。 查看[使用 Azure 監視器為容器創建性能警報](container-insights-alerts.md)，瞭解如何為高 CPU 和記憶體利用率創建建議警報以支援 DevOps 或操作過程。 
