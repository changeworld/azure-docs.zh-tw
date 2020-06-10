---
title: 在 Azure 中監視 Service Fabric 叢集
description: 在本教學課程中，您將了解如何藉由檢視 Service Fabric 事件、查詢 EventStore API、監視效能計數器，以及檢視健康情況報告，來監視叢集。
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc
ms.openlocfilehash: 338ebc584cc718fd9d3b587d17ec0895483dfb0c
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560445"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>教學課程：在 Azure 中監視 Service Fabric 叢集

不論任何雲端環境，針對工作負載的開發、測試及部署進行監視和診斷都極為重要。 本教學課程為一個系列中的第二部分，示範如何使用事件、效能計數器和健康情況報表來監視和診斷 Service Fabric 叢集。   如需詳細資訊，請參閱關於[叢集監控](service-fabric-diagnostics-overview.md#platform-cluster-monitoring)和[基礎結構監視](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring)的概觀。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢視 Service Fabric 事件
> * 針對叢集事件查詢 EventStore API
> * 監視基礎結構/收集效能計數器
> * 檢視叢集健康情況報告

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * 在 Azure 上使用範本建立安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
> * 監視叢集
> * [將叢集相應縮小或相應放大](service-fabric-tutorial-scale-cluster.md)
> * [升級叢集的執行階段](service-fabric-tutorial-upgrade-cluster.md)
> * [刪除叢集](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 安裝 [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) 或 [Azure CLI](/cli/azure/install-azure-cli)。
* 建立安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 
* 設定叢集的[診斷收集](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor)
* 在叢集中啟用 [EventStore 服務](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor)
* 針對叢集設定 [Azure 監視器記錄和 Log Analytics 代理程式](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor)

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>使用 Azure 監視器記錄檢視 Service Fabric 事件

Azure 監視器記錄會從裝載於雲端的應用程式和服務收集和分析遙測，並提供分析工具來協助您將其可用性和效能最大化。 您可以在 Azure 監視器記錄中執行查詢，以深入了解叢集中發生什麼狀況並進行疑難排解。

若要存取 Service Fabric 分析解決方案，移至 [Azure 入口網站](https://portal.azure.com)，然後選取您建立 Service Fabric 分析解決方案所在的資源群組。

選取資源 **ServiceFabric(mysfomsworkspace)** 。

在 [概觀]  中，您會看到每個已啟用解決方案的圖格均以圖形形式顯示，其中包括一個適用於 Service Fabric 的圖格。 按一下 [Service Fabric]  圖形以繼續進行「Service Fabric 分析」解決方案。

![Service Fabric 解決方案](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

下圖顯示 Service Fabric 分析解決方案的首頁。 這個首頁提供叢集中目前情況的快照集檢視。

![Service Fabric 解決方案](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 如果您建立叢集的時候啟用了分析功能，就可以看到以下各項的事件 

* [Service Fabric 叢集事件](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors 程式設計模型事件](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services 程式設計模型事件](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>除了現有的 Service Fabric 事件外，[更新分析延伸模組的設定](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)可以收集更詳細的系統事件。

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>檢視 Service Fabric 事件，包括節點上的動作

在 Service Fabric 分析頁面上，按一下**叢集事件**的圖形。  隨即顯示已收集的所有系統事件記錄。 為了方便參考，這些記錄均來自 Azure 儲存體帳戶中的 **WADServiceFabricSystemEventsTable**，而且同樣地，您接下來看到的 Reliable Service 和 Actor 事件均分別來自那些表格。
    
![查詢操作通道](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

此查詢會使用 Kusto 查詢語言，您可以進行修改以調整您要尋找的內容。 例如，如果想尋找針對叢集節點所採取的動作，您可以使用下列查詢。 下面使用的事件識別碼，可以在[操作通道事件參考](service-fabric-diagnostics-event-generation-operational.md)中找到。

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Kusto 查詢語言功能很強大。 以下是一些其他有用的查詢。

藉由將查詢儲存為具有 ServiceFabricEvent 別名的函式，來建立 *ServiceFabricEvent* 查閱資料表作為使用者定義函式：

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

傳回過去一小時所記錄的操作事件：
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

傳回 EventId == 18604 且 EventName == 'NodeDownOperational' 的操作事件：
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

傳回 EventId == 18604 且 EventName == 'NodeUpOperational' 的操作事件：
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 

傳回 HealthState == 3 (錯誤) 的健康情況報告，並從 EventMessage 欄位擷取其他屬性：

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

傳回 EventId != 17523 之事件的時間圖表：

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

使用取得特定服務和節點來彙總的 Service Fabric 操作事件：

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

使用跨資源查詢，依 EventId / EventName 轉譯 Service Fabric 事件的計數：

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>檢視 Service Fabric 應用程式事件

您可以檢視部署於叢集上之 Reliable Services 和 Reliable Actors 應用程式的事件。  在 Service Fabric 分析頁面上，按一下**應用程式事件**的圖形。

執行下列查詢，以檢視來自 Reliable Services 應用程式的事件：
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

啟動及完成 runasync 服務時，您可以看到不同的事件，它們通常是在部署和升級發生的。

![Service Fabric 解決方案 Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

您也可以尋找 ServiceName == "fabric:/Watchdog/WatchdogService" 之可靠服務的事件：

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
以類似的方式，就可以檢視 Reliable Actor 事件：

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
若要為 Reliable Actors 設定更詳細的事件，您可以在叢集範本內，適用於診斷擴充功能的設定中變更 `scheduledTransferKeywordFilter`。 [Reliable Actor 事件參考](service-fabric-reliable-actors-diagnostics.md#keywords)會有這些事件值的詳細資料。

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>使用 Azure 監視器記錄檢視效能計數器
若要檢視效能計數器，請依序移至 [Azure 入口網站](https://portal.azure.com)及您建立 Service Fabric 分析解決方案所在的資源群組。 

依序選取資源 **ServiceFabric(mysfomsworkspace)** 、[Log Analytics 工作區]  及 [進階設定]  。

按一下 [資料]  ，然後按一下 [Windows 效能計數器]  。 有一個預設計數器清單，您可以從中選擇啟用，也可以設定收集間隔。 您也可以新增想收集的[其他效能計數器](service-fabric-diagnostics-event-generation-perf.md)。 [本文](/windows/desktop/PerfCtrs/specifying-a-counter-path)參照適當的格式。 按一下 [儲存]  ，然後按一下 [確定]  。

關閉 [進階設定] 刀鋒視窗，然後選取 [一般]  標題下方的 [工作區摘要]  。 針對每個啟用的解決方案，會有一個圖形化圖格，其中包括一個適用於 Service Fabric 的圖格。 按一下 [Service Fabric]  圖形以繼續進行「Service Fabric 分析」解決方案。

有適用於操作通道和 Reliable Services 事件的圖形化圖格。 針對您所選取計數器流入的資料圖形表示將顯示於 [節點計量]  底下。 

選取 [容器計量]  圖形，以查看其他詳細資料。 您也可以使用 Kusto 查詢語言來查詢效能計數器資料 (與節點上的叢集事件和篩選條件類似)、效能計數器名稱以及值。

## <a name="query-the-eventstore-service"></a>查詢 EventStore 服務
[EventStore 服務](service-fabric-diagnostics-eventstore.md)可讓您了解叢集或工作負載在指定時間點的狀態。 EventStore 是具狀態的 Service Fabric 服務，可維護叢集中的事件。 事件會透過 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)、REST 和 API 公開。 EventStore 會直接查詢叢集，以取得叢集中有關所有實體的診斷資料。若要查看 EventStore 中可用的事件完整清單，請參閱 [Service Fabric 事件](service-fabric-diagnostics-event-generation-operational.md)。

EventStore API 可以使用 [Service Fabric 用戶端程式庫](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)，以程式設計方式進行查詢。

以下為透過 GetClusterEventListAsync 函式，針對介於 2018-04-03T18:00:00Z 和 2018-04-04T18:00:00Z 之間的所有叢集事件所提出的範例要求。

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

以下是另一個範例，該範例會查詢叢集健康情況以及 2018 年 9 月的所有節點事件，並將其列印出來。

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>監視叢集健康情況
Service Fabric 導入了由健康情況實體組成的[健康情況模型](service-fabric-health-introduction.md)，系統元件和看門狗可以在其上報告所監視的本機情況。 [健康情況存放區](service-fabric-health-introduction.md#health-store) 會彙總所有健康情況資料，以判斷實體是否狀況良好。

叢集會自動填入系統元件所傳送的健康情況報告。 請參閱 [使用系統健康狀態報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)。

Service Fabric 會針對每種支援的 [實體類型](service-fabric-health-introduction.md#health-entities-and-hierarchy)公開健康情況查詢。 您可透過 API (在 [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet) 上使用方法)、PowerShell Cmdlet 和 REST 來存取查詢。 這些查詢會傳回實體的完整健康情況資訊：彙總健康情況、實體健康事件、子系健康情況 (如果適用)、狀況不佳評估 (當實體狀況不佳時)，以及子系健康情況統計資料 (如果適用)。

### <a name="get-cluster-health"></a>取得叢集健康情況
[Get-ServiceFabricClusterHealth Cmdlet](/powershell/module/servicefabric/get-servicefabricclusterhealth) 會傳回叢集實體的健康情況，並包含應用程式和節點 (叢集子系) 的健全狀態。  首先使用 [Connect-ServiceFabricCluster Cmdlet](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) 連線到叢集。

叢集的狀態是 11 個節點、系統應用程式，以及依所述設定的 fabric:/Voting。

以下範例會使用預設的健康情況原則，來取得叢集健康情況。 11 個節點均狀況良好，但叢集彙總健全狀態為「錯誤」，因為 fabric:/Voting 應用程式處於「錯誤」狀態。 請注意健康情況不佳的評估，會如何提供觸發彙總健康情況的條件的詳細資料。

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

以下範例會使用自訂的應用程式原則，來取得叢集的健康情況。 它會篩選結果，只取得發生錯誤或警告的應用程式和節點。 在此範例中，不會傳回任何節點，因為它們全部都狀況良好。 只有 fabric:/Voting 應用程式符合應用程式篩選。 由於自訂原則會針對 fabric:/Voting 應用程式指定將警告視為錯誤，因此會將應用程式及叢集評估為處於錯誤狀態。

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>取得節點的健康情況
[Get-ServiceFabricNodeHealth Cmdlet](/powershell/module/servicefabric/get-servicefabricnodehealth) 會傳回節點實體的健康情況，並包含節點上報告的健康情況事件。 首先使用 [Connect-ServiceFabricCluster Cmdlet](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) 連線到叢集。 下列範例會使用預設的健康情況原則，來取得特定節點的健康情況：

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

下列範例會取得叢集中所有節點的健康情況：
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>取得系統服務健康情況 

取得系統服務的彙總健康情況：

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 檢視 Service Fabric 事件
> * 針對叢集事件查詢 EventStore API
> * 監視基礎結構/收集效能計數器
> * 檢視叢集健康情況報告

接下來，前進到下列教學課程，以了解如何調整叢集。
> [!div class="nextstepaction"]
> [調整叢集](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
