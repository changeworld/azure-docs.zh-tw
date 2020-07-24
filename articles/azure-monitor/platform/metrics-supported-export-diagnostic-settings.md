---
title: 具有 Null 和零值的計量匯出行為
description: 匯出度量和無法匯出的計量清單的指標時，會討論 Null 和零值。
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.subservice: metrics
ms.openlocfilehash: ca6acb97e52123a0663d988b3f217d305bce2c4b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131679"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>可透過診斷設定匯出的 Azure 監視器平台計量

Azure 監視器依預設會提供未設定的[平台計量](data-platform-metrics.md)。 其中提供數種與平台計量進行互動的方式，包括在入口網站中製作計量圖表、透過 REST API 存取計量，或使用 PowerShell 或 CLI 查詢計量。 請參閱[支援的計量](metrics-supported.md)，以了解目前可供 Azure 監視器的合併計量管線使用的所有平台計量。 若要查詢及存取這些計量，請使用 [2018-01-01 api-version](/rest/api/monitor/metricdefinitions)。 其他計量可在入口網站中或使用舊版 API 提供。

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>無法透過診斷設定匯出計量

用在此位置的內容已移至[支援的 Azure 監視器計量清單](metrics-supported.md#exporting-platform-metrics-to-other-locations)。

透過診斷設定匯出計量時，會有一些限制。 所有計量都可使用 REST API 進行匯出。 

## <a name="exported-zero-vs-null-values"></a>已匯出零與 Null 值 

處理0或 Null 值時，計量會有不同的行為。  某些計量會在未取得任何資料時回報0，例如 HTTP 失敗的計量。 當沒有資料取得時，其他計量會儲存 Null，因為它可能表示資源已離線。 您可以在以[虛線](metrics-troubleshoot.md#chart-shows-dashed-line)顯示具有 Null 值的這些計量圖表時，看到差異。 

當平臺計量可以透過診斷設定匯出時，它們會符合度量的行為。 也就是說，當資源不傳送任何資料時，它們會匯出 Null。  它們只會匯出 ' 0 '，但只有當基礎資源確實發出它們時。 

如果您刪除資源群組或特定資源，受影響資源的計量資料就不會再傳送到診斷設定匯出目的地。 也就是說，它不會再出現在事件中樞、Azure 儲存體帳戶和 Log Analytics 工作區中。

### <a name="metrics-that-used-to-export-zero-for-null"></a>用來匯出零為 Null 的計量

在2020年6月1日前，下列計量**用來**在沒有資料時發出 ' 0 '。 然後，這些零可以匯出到下游系統（例如 Log Analytics 和 Azure 儲存體）。  這種行為會造成實際 ' 0 ' （由資源所發出）與轉譯 ' 0 ' （Null）之間的混淆，因此行為已變更為符合上一節中所述的基礎度量。 

變更發生在所有公用和私人雲端。

這項變更並不會影響下列任何經驗的行為： 
   - 透過診斷設定匯出的平臺資源記錄
   - 計量瀏覽器中的計量圖表
   - 平台計量的警示
 
以下是其行為已變更的計量清單。 

| ResourceType                    | 計量               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  未經授權的閘道要求 (已淘汰)  | 
| Microsoft.ApiManagement/service | TotalRequests |  閘道要求總數 (已淘汰)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  成功的閘道要求 (已淘汰)  | 
| Microsoft.ApiManagement/service | Requests |  Requests  | 
| Microsoft.ApiManagement/service | OtherRequests |  其他閘道要求 (已淘汰)  | 
| Microsoft.ApiManagement/service | FailedRequests |  失敗的閘道要求 (已淘汰)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  失敗的 EventHub 事件  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  EventHub 事件總數  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  EventHub 事件的大小  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  已逾時的 EventHub 事件  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  已節流的 EventHub 事件  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  成功的 EventHub 事件  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  已拒絕的 EventHub 事件  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  已卸除的 EventHub 事件  | 
| Microsoft.ApiManagement/service | Duration |  閘道要求的整體持續期間  | 
| Microsoft.ApiManagement/service | BackendDuration |  後端要求的持續時間  | 
| Microsoft.DBforMariaDB/servers | storage_used |  已使用儲存體  | 
| Microsoft.DBforMariaDB/servers | storage_percent |  儲存體百分比  | 
| Microsoft.DBforMariaDB/servers | storage_limit |  儲存體限制  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_usage |  使用的伺服器記錄儲存體  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_percent |  伺服器記錄儲存體百分比  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_limit |  伺服器記錄儲存體限制  | 
| Microsoft.DBforMariaDB/servers | seconds_behind_master |  複寫延遲 (秒)  | 
| Microsoft.DBforMariaDB/servers | network_bytes_ingress |  Network In  | 
| Microsoft.DBforMariaDB/servers | network_bytes_egress |  Network Out  | 
| Microsoft.DBforMariaDB/servers | memory_percent |  記憶體百分比  | 
| Microsoft.DBforMariaDB/servers | io_consumption_percent |  IO 百分比  | 
| Microsoft.DBforMariaDB/servers | cpu_percent |  CPU 百分比  | 
| Microsoft.DBforMariaDB/servers | connections_failed |  失敗的連線  | 
| Microsoft.DBforMariaDB/servers | backup_storage_used |  已使用的備份儲存體  | 
| Microsoft.DBforMariaDB/servers | active_connections |  作用中的連線  | 
| Microsoft.DBforMySQL/servers | storage_used |  已使用儲存體  | 
| Microsoft.DBforMySQL/servers | storage_percent |  儲存體百分比  | 
| Microsoft.DBforMySQL/servers | storage_limit |  儲存體限制  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_usage |  使用的伺服器記錄儲存體  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_percent |  伺服器記錄儲存體百分比  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_limit |  伺服器記錄儲存體限制  | 
| Microsoft.DBforMySQL/servers | seconds_behind_master |  複寫延遲 (秒)  | 
| Microsoft.DBforMySQL/servers | network_bytes_ingress |  Network In  | 
| Microsoft.DBforMySQL/servers | network_bytes_egress |  Network Out  | 
| Microsoft.DBforMySQL/servers | memory_percent |  記憶體百分比  | 
| Microsoft.DBforMySQL/servers | io_consumption_percent |  IO 百分比  | 
| Microsoft.DBforMySQL/servers | cpu_percent |  CPU 百分比  | 
| Microsoft.DBforMySQL/servers | connections_failed |  失敗的連線  | 
| Microsoft.DBforMySQL/servers | backup_storage_used |  已使用的備份儲存體  | 
| Microsoft.DBforMySQL/servers | active_connections |  作用中的連線  | 
| Microsoft.Devices/Account | digitaltwins.telemetry.nodes |  Digital Twins 節點遙測預留位置  | 
| Microsoft.Devices/IotHubs | twinQueries.success |  成功對應項查詢  | 
| Microsoft.Devices/IotHubs | twinQueries.resultSize |  對應項查詢結果大小  | 
| Microsoft.Devices/IotHubs | twinQueries.failure |  失敗對應項查詢  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.success |  成功作業查詢  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.failure |  失敗作業查詢  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.success |  成功呼叫列出作業  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.failure |  呼叫列出作業失敗  | 
| Microsoft.Devices/IotHubs | jobs.failed |  失敗作業  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success |  成功建立的對應項更新作業  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure |  建立失敗的對應項更新作業  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success |  成功建立的方法叫用作業  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure |  建立失敗的方法叫用作業  | 
| Microsoft.Devices/IotHubs | jobs.completed |  已完成的工作  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.success |  成功取消作業  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.failure |  取消作業失敗  | 
| Microsoft.Devices/IotHubs | EventGridLatency |  事件方格延遲 (預覽)  | 
| Microsoft.Devices/IotHubs | EventGridDeliveries |  事件方格傳遞 (預覽)  | 
| Microsoft.Devices/IotHubs | devices.totalDevices |  裝置總計 (已淘汰)  | 
| Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol |  連接的裝置 (已淘汰)   | 
| Microsoft.Devices/IotHubs | deviceDataUsageV2 |  裝置資料使用量總計 (預覽)  | 
| Microsoft.Devices/IotHubs | deviceDataUsage |  裝置資料使用量總計  | 
| Microsoft.Devices/IotHubs | dailyMessageQuotaUsed |  已使用的訊息總數  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.success |  裝置的成功對應項更新  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.size |  裝置的對應項更新大小  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.failure |  裝置的失敗對應項更新  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.success |  裝置的成功對應項讀取  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.size |  裝置的對應項讀取回應大小  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.failure |  裝置的失敗對應項讀取  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success |  已傳送的遙測訊息  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle |  節流錯誤數目  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol |  遙測訊息傳送嘗試  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.success |  路由：已傳遞的遙測訊息  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned |  路由：已遺棄的遙測訊息   | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid |  路由：不相容的遙測訊息  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback |  路由：傳遞至後援的訊息  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped |  路由：已捨棄的遙測訊息   | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage |  路由：儲存體的訊息延遲  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics |  路由：服務匯流排主題的訊息延遲  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues |  路由：服務匯流排佇列的訊息延遲  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs |  路由：事件中樞的訊息延遲  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events |  路由：訊息/事件的訊息延遲  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes |  路由：傳遞至儲存體的資料  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs |  路由：傳遞至儲存體的 BLOB  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage |  路由：傳遞至儲存體的訊息  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics |  路由：傳遞至服務匯流排主題的訊息  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues |  路由：傳遞至服務匯流排佇列的訊息  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs |  路由：傳遞至事件中樞的訊息  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events |  路由：傳遞至訊息/事件的訊息  | 
| Microsoft.Devices/IotHubs | 組態 |  設定計量  | 
| Microsoft.Devices/IotHubs | C2DMessagesExpired |  已過期的 C2D 訊息 (預覽)  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.success |  後端的成功對應項更新  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.size |  後端的對應項更新大小  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.failure |  後端的失敗對應項更新  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.success |  後端的成功對應項讀取  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.size |  後端的對應項讀取回應大小  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.failure |  後端的失敗對應項讀取  | 
| Microsoft.Devices/IotHubs | c2d.methods.success |  成功直接方法叫用  | 
| Microsoft.Devices/IotHubs | c2d.methods.responseSize |  直接方法叫用的回應大小  | 
| Microsoft.Devices/IotHubs | c2d.methods.requestSize |  直接方法叫用的要求大小  | 
| Microsoft.Devices/IotHubs | c2d.methods.failure |  失敗直接方法叫用  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success |  已拒絕的 C2D 訊息  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success |  已完成的 C2D 訊息傳遞  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success |  已放棄的 C2D 訊息  | 
| Microsoft.Devices/provisioningServices | RegistrationAttempts |  註冊嘗試數  | 
| Microsoft.Devices/provisioningServices | DeviceAssignments |  已指派的裝置數  | 
| Microsoft.Devices/provisioningServices | AttestationAttempts |  證明嘗試數  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  要求單位總計  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  要求總數  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Mongo 要求  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Mongo 要求收費  | 
| Microsoft.EventGrid/domains | PublishSuccessLatencyInMs |  發行成功延遲  | 
| Microsoft.EventGrid/domains | PublishSuccessCount |  已發佈的事件  | 
| Microsoft.EventGrid/domains | PublishFailCount |  發行失敗的事件  | 
| Microsoft.EventGrid/domains | MatchedEventCount |  相符的事件  | 
| Microsoft.EventGrid/domains | DroppedEventCount |  卸除的事件  | 
| Microsoft.EventGrid/domains | DeliverySuccessCount |  已傳遞的事件  | 
| Microsoft.EventGrid/domains | DeadLetteredCount |  失效信件事件  | 
| Microsoft.EventGrid/eventSubscriptions | MatchedEventCount |  相符的事件  | 
| Microsoft.EventGrid/eventSubscriptions | DroppedEventCount |  卸除的事件  | 
| Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount |  已傳遞的事件  | 
| Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount |  失效信件事件  | 
| Microsoft.EventGrid/extensionTopics | UnmatchedEventCount |  不相符的事件  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs |  發行成功延遲  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessCount |  已發佈的事件  | 
| Microsoft.EventGrid/extensionTopics | PublishFailCount |  發行失敗的事件  | 
| Microsoft.EventGrid/topics | UnmatchedEventCount |  不相符的事件  | 
| Microsoft.EventGrid/topics | PublishSuccessLatencyInMs |  發行成功延遲  | 
| Microsoft.EventGrid/topics | PublishSuccessCount |  已發佈的事件  | 
| Microsoft.EventGrid/topics | PublishFailCount |  發行失敗的事件  | 
| Microsoft.EventHub/clusters | OutgoingMessages |  外送訊息  | 
| Microsoft.EventHub/clusters | OutgoingBytes |  傳出位元組數。  | 
| Microsoft.EventHub/clusters | IncomingRequests |  傳入的要求  | 
| Microsoft.EventHub/clusters | IncomingMessages |  傳入訊息  | 
| Microsoft.EventHub/clusters | IncomingBytes |  傳入位元組數。  | 
| Microsoft.EventHub/namespaces | SVRBSY |  伺服器忙線錯誤 (已淘汰)  | 
| Microsoft.EventHub/namespaces | SUCCREQ |  成功的要求 (已淘汰)  | 
| Microsoft.EventHub/namespaces | OUTMSGS |  傳出的訊息 (過時) (已淘汰)  | 
| Microsoft.EventHub/namespaces | OutgoingMessages |  外送訊息  | 
| Microsoft.EventHub/namespaces | OutgoingBytes |  傳出位元組數。  | 
| Microsoft.EventHub/namespaces | MISCERR |  其他錯誤 (已淘汰)  | 
| Microsoft.EventHub/namespaces | INTERR |  內部伺服器錯誤 (已淘汰)  | 
| Microsoft.EventHub/namespaces | INREQS |  傳入的要求 (已淘汰)  | 
| Microsoft.EventHub/namespaces | INMSGS |  傳入的訊息 (過時) (已淘汰)  | 
| Microsoft.EventHub/namespaces | IncomingRequests |  傳入的要求  | 
| Microsoft.EventHub/namespaces | IncomingMessages |  傳入訊息  | 
| Microsoft.EventHub/namespaces | IncomingBytes |  傳入位元組數。  | 
| Microsoft.EventHub/namespaces | FAILREQ |  失敗的要求 (已淘汰)  | 
| Microsoft.EventHub/namespaces | EHOUTMSGS |  傳出訊息數 (已過時)  | 
| Microsoft.EventHub/namespaces | EHOUTMBS |  傳出位元組數 (過時) (已淘汰)  | 
| Microsoft.EventHub/namespaces | EHOUTBYTES |  傳出位元組數 (已過時)  | 
| Microsoft.EventHub/namespaces | EHINMSGS |  連入訊息數 (已過時)  | 
| Microsoft.EventHub/namespaces | EHINMBS |  傳入位元組數 (過時) (已淘汰)  | 
| Microsoft.EventHub/namespaces | EHINBYTES |  傳入位元組數 (已過時)  | 
| Microsoft.EventHub/namespaces | EHAMSGS |  封存訊息 (已淘汰)  | 
| Microsoft.EventHub/namespaces | EHAMBS |  封存訊息輸送量 (已淘汰)  | 
| Microsoft.EventHub/namespaces | EHABL |  封存待處理項目訊息 (已淘汰)  | 
| Microsoft.HDInsight/clusters | NumActiveWorkers |  作用中背景工作數目  | 
| Microsoft.HDInsight/clusters | GatewayRequests |  閘道要求數  | 
| Microsoft.HDInsight/clusters | CategorizedGatewayRequests |  已分類的閘道要求數  | 
| Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated |  已起始的調整規模動作數  | 
| Microsoft.Insights/Components | traces/count |  追蹤  | 
| Microsoft.Insights/Components | performanceCounters/requestsPerSecond |  HTTP 要求率  | 
| Microsoft.Insights/Components | performanceCounters/requestsInQueue |  應用程式佇列中的 HTTP 要求數  | 
| Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond |  例外狀況比率  | 
| Microsoft.Insights/Components | pageViews/count |  頁面檢視  | 
| Microsoft.Insights/Components | exceptions/count |  例外狀況  | 
| Microsoft.Kusto/Clusters | StreamingIngestResults |  串流內嵌結果  | 
| Microsoft.Kusto/Clusters | StreamingIngestDuration |  串流內嵌持續時間  | 
| Microsoft.Kusto/Clusters | StreamingIngestDataRate |  串流內嵌資料速率  | 
| Microsoft.Kusto/Clusters | SteamingIngestRequestRate |  串流內嵌要求率  | 
| Microsoft.Kusto/Clusters | QueryDuration |  查詢持續時間  | 
| Microsoft.Kusto/Clusters | KeepAlive |  保持運作  | 
| Microsoft.Kusto/Clusters | IngestionVolumeInMB |  擷取量 (以 MB 為單位)  | 
| Microsoft.Kusto/Clusters | IngestionUtilization |  擷取使用率  | 
| Microsoft.Kusto/Clusters | IngestionResult |  擷取結果  | 
| Microsoft.Kusto/Clusters | IngestionLatencyInSeconds |  擷取延遲 (以秒為單位)  | 
| Microsoft.Kusto/Clusters | ExportUtilization |  匯出使用率  | 
| Microsoft.Kusto/Clusters | EventsProcessedForEventHubs |  已處理的事件 (針對事件/IoT 中樞)  | 
| Microsoft.Kusto/Clusters | CPU |  CPU  | 
| Microsoft.Kusto/Clusters | ContinuousExportResult |  連續匯出結果  | 
| Microsoft.Kusto/Clusters | ContinuousExportPendingCount |  連續匯出擱置計數  | 
| Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported |  連續匯出 - 已匯出的記錄數  | 
| Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes |  連續匯出最大延遲分鐘數  | 
| Microsoft.Kusto/Clusters | CacheUtilization |  快取使用率  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents |  觸發程序節流的事件  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded |  成功的觸發程序   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersStarted |  啟動的觸發程序   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped |  略過的觸發程序  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFired |  引發的觸發程序   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFailed |  失敗的觸發程序   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted |  完成的觸發程序   | 
| Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents |  執行節流的事件  | 
| Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents |  執行開始節流處理事件  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded |  已成功的執行  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsStarted |  執行已啟動  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsFailed |  失敗的執行  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCompleted |  完成的執行  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCancelled |  已取消的執行  | 
| Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage |  執行失敗百分比  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents |  動作節流的事件  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded |  成功的動作   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsStarted |  啟動的動作   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped |  略過的動作   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsFailed |  動作的失敗   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted |  完成的動作   | 
| Microsoft.Logic/workflows | TriggerThrottledEvents |  觸發程序節流的事件  | 
| Microsoft.Logic/workflows | TriggersSucceeded |  成功的觸發程序   | 
| Microsoft.Logic/workflows | TriggersStarted |  啟動的觸發程序   | 
| Microsoft.Logic/workflows | TriggersSkipped |  略過的觸發程序  | 
| Microsoft.Logic/workflows | TriggersFired |  引發的觸發程序   | 
| Microsoft.Logic/workflows | TriggersFailed |  失敗的觸發程序   | 
| Microsoft.Logic/workflows | TriggersCompleted |  完成的觸發程序   | 
| Microsoft.Logic/workflows | TotalBillableExecutions |  可計費的總執行次數  | 
| Microsoft.Logic/workflows | RunThrottledEvents |  執行節流的事件  | 
| Microsoft.Logic/workflows | RunStartThrottledEvents |  執行開始節流處理事件  | 
| Microsoft.Logic/workflows | RunsSucceeded |  已成功的執行  | 
| Microsoft.Logic/workflows | RunsStarted |  執行已啟動  | 
| Microsoft.Logic/workflows | RunsFailed |  失敗的執行  | 
| Microsoft.Logic/workflows | RunsCompleted |  完成的執行  | 
| Microsoft.Logic/workflows | RunsCancelled |  已取消的執行  | 
| Microsoft.Logic/workflows | RunFailurePercentage |  執行失敗百分比  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  為使用儲存體使用量執行計費  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  為使用儲存體使用量執行計費  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  為使用標準連接器執行計費  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  為使用標準連接器執行計費  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  為使用原生作業執行計費  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  為使用原生作業執行計費  | 
| Microsoft.Logic/workflows | BillableTriggerExecutions |  可計費的觸發程序執行  | 
| Microsoft.Logic/workflows | BillableActionExecutions |  可計費的動作執行  | 
| Microsoft.Logic/workflows | ActionThrottledEvents |  動作節流的事件  | 
| Microsoft.Logic/workflows | ActionsSucceeded |  成功的動作   | 
| Microsoft.Logic/workflows | ActionsStarted |  啟動的動作   | 
| Microsoft.Logic/workflows | ActionsSkipped |  略過的動作   | 
| Microsoft.Logic/workflows | ActionsFailed |  動作的失敗   | 
| Microsoft.Logic/workflows | ActionsCompleted |  完成的動作   | 
| Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount |  Web 應用程式防火牆要求計數  | 
| Microsoft.Network/frontdoors | TotalLatency |  延遲總計  | 
| Microsoft.Network/frontdoors | ResponseSize |  回應大小  | 
| Microsoft.Network/frontdoors | RequestSize |  要求大小  | 
| Microsoft.Network/frontdoors | RequestCount |  要求計數  | 
| Microsoft.Network/frontdoors | BillableResponseSize |  可計費的回應大小  | 
| Microsoft.Network/frontdoors | BackendRequestLatency |  後端要求延遲  | 
| Microsoft.Network/frontdoors | BackendRequestCount |  後端要求計數  | 
| Microsoft.Network/frontdoors | BackendHealthPercentage |  後端健康情況百分比  | 
| Microsoft.Network/trafficManagerProfiles | QpsByEndpoint |  傳回的依端點查詢數  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending |  暫止已排程的通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update |  註冊更新作業  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get |  註冊讀取作業  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete |  註冊刪除作業  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create |  註冊建立作業  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all |  註冊作業數  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken |  WNS 授權錯誤 (錯誤權杖)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable |  WNS 授權錯誤 (無法連線)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled |  WNS 節流通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success |  WNS 成功通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror |  WNS 錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken |  WNS 授權錯誤 (無效權杖)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize |  WNS 無效通知大小錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat |  WNS 無效通知格式  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials |  WNS 授權錯誤 (無效認證)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel |  WNS 過期通道錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped |  WNS 已捨棄通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled |  WNS 通道已節流處理  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected |  WNS 通道已中斷連線  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel |  WNS 不正確的通道錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror |  WNS 驗證錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled |  MPNS 已將通知節流處理  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success |  MPNS 成功通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror |  MPNS 錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat |  MPNS 無效通知格式  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials |  MPNS 無效認證  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped |  MPNS 已捨棄通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected |  MPNS 通道已中斷連線  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel |  MPNS 不正確的通道錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror |  MPNS 驗證錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel |  GCM 不正確的通道錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled |  GCM 已將通知節流處理  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success |  GCM 成功通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror |  GCM 錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize |  GCM 無效通知大小錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat |  GCM 無效通知格式  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials |  GCM 授權錯誤 (無效認證)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel |  GCM 過期通道錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel |  GCM 不正確的通道錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror |  GCM 驗證錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success |  APNS 成功通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror |  APNS 錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize |  APNS 無效通知大小錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials |  APNS 授權錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel |  APNS 過期通道錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel |  APNS 不正確的通道錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success |  成功通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror |  外部通知系統錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload |  承載錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror |  通道錯誤  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes |  所有外寄通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert |  建立或更新安裝作業  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch |  修補安裝作業  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get |  取得安裝作業  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete |  刪除安裝作業  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all |  安裝管理作業  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel |  取消已排程的推播通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled |  傳送已排程的推播通知  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests |  所有傳入要求  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests |  所有傳入的失敗要求  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming |  傳入訊息  | 
| Microsoft.OperationalInsights/workspaces | Heartbeat |  Heartbeat  | 
| Microsoft.Relay/namespaces | BytesTransferred |  BytesTransferred  | 
| Microsoft.ServiceBus/namespaces | OutgoingMessages |  外送訊息  | 
| Microsoft.ServiceBus/namespaces | IncomingRequests |  傳入的要求  | 
| Microsoft.ServiceBus/namespaces | IncomingMessages |  傳入訊息  | 
| Microsoft.SignalRService/SignalR | UserErrors |  使用者錯誤  | 
| Microsoft.SignalRService/SignalR | SystemErrors |  系統錯誤  | 
| Microsoft.SignalRService/SignalR | OutboundTraffic |  輸出流量  | 
| Microsoft.SignalRService/SignalR | MessageCount |  訊息計數  | 
| Microsoft.SignalRService/SignalR | InboundTraffic |  輸入流量  | 
| Microsoft.SignalRService/SignalR | ConnectionCount |  連線計數  | 
| Microsoft.Sql/managedInstances | avg_cpu_percent |  CPU 百分比平均  | 
| Microsoft.Sql/servers | dtu_used |  已使用 DTU  | 
| Microsoft.Sql/servers | dtu_consumption_percent |  DTU 百分比  | 
| Microsoft.Sql/servers/databases | xtp_storage_percent |  記憶體內部 OLTP 儲存體百分比  | 
| Microsoft.Sql/servers/databases | workers_percent |  背景工作角色百分比  | 
| Microsoft.Sql/servers/databases | sessions_percent |  工作階段百分比  | 
| Microsoft.Sql/servers/databases | physical_data_read_percent |  資料 IO 百分比  | 
| Microsoft.Sql/servers/databases | log_write_percent |  記錄 IO 百分比  | 
| Microsoft.Sql/servers/databases | dwu_used |  已使用 DWU  | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent |  DWU 百分比  | 
| Microsoft.Sql/servers/databases | dtu_used |  已使用 DTU  | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent |  DTU 百分比  | 
| Microsoft.Sql/servers/databases | deadlock |  死結  | 
| Microsoft.Sql/servers/databases | cpu_used |  已使用的 CPU  | 
| Microsoft.Sql/servers/databases | cpu_percent |  CPU 百分比  | 
| Microsoft.Sql/servers/databases | connection_successful |  成功的連線  | 
| Microsoft.Sql/servers/databases | connection_failed |  失敗的連線  | 
| Microsoft.Sql/servers/databases | cache_hit_percent |  快取命中的百分比  | 
| Microsoft.Sql/servers/databases | blocked_by_firewall |  遭到防火牆封鎖  | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent |  記憶體內部 OLTP 儲存體百分比  | 
| Microsoft.Sql/servers/elasticPools | workers_percent |  背景工作角色百分比  | 
| Microsoft.Sql/servers/elasticPools | sessions_percent |  工作階段百分比  | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent |  資料 IO 百分比  | 
| Microsoft.Sql/servers/elasticPools | log_write_percent |  記錄 IO 百分比  | 
| Microsoft.Sql/servers/elasticPools | eDTU_used |  已使用 eDTU  | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent |  DTU 百分比  | 
| Microsoft.Sql/servers/elasticPools | cpu_percent |  CPU 百分比  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  前端總數  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  小型 App Service 方案背景工作角色  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Requests |  Requests  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage |  記憶體百分比  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  中型 App Service 方案背景工作角色  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  大型 App Service 方案背景工作角色  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Http 佇列長度  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx |  Http 伺服器錯誤  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx |  Http 4xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http404 |  Http 404  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http403 |  Http 403  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http401 |  Http 401  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx |  Http 3xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx |  Http 2xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength |  磁碟佇列長度  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage |  CPU 百分比  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent |  資料輸出  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived |  資料輸入  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime |  平均回應時間  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests |  使用中的要求  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed |  已使用的背景工作角色  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal |  背景工作角色總數  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable |  可用的背景工作角色  | 
| Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage |  記憶體百分比  | 
| Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage |  CPU 百分比  | 
| Microsoft.Web/serverfarms | TcpTimeWait |  TCP 等候時間  | 
| Microsoft.Web/serverfarms | TcpSynSent |  已傳送的 TCP 同步  | 
| Microsoft.Web/serverfarms | TcpSynReceived |  已接收的 TCP 同步  | 
| Microsoft.Web/serverfarms | TcpLastAck |  TCP 上次認可  | 
| Microsoft.Web/serverfarms | TcpFinWait2 |  TCP 完成等候 2  | 
| Microsoft.Web/serverfarms | TcpFinWait1 |  TCP 完成等候 1  | 
| Microsoft.Web/serverfarms | TcpEstablished |  已建立的 TCP  | 
| Microsoft.Web/serverfarms | TcpClosing |  正在關閉的 TCP  | 
| Microsoft.Web/serverfarms | TcpCloseWait |  等候關閉的 TCP  | 
| Microsoft.Web/serverfarms | MemoryPercentage |  記憶體百分比  | 
| Microsoft.Web/serverfarms | HttpQueueLength |  Http 佇列長度  | 
| Microsoft.Web/serverfarms | DiskQueueLength |  磁碟佇列長度  | 
| Microsoft.Web/serverfarms | CpuPercentage |  CPU 百分比  | 
| Microsoft.Web/serverfarms | BytesSent |  資料輸出  | 
| Microsoft.Web/serverfarms | BytesReceived |  資料輸入  | 
| Microsoft.Web/sites | TotalAppDomainsUnloaded |  已卸載的應用程式網域總計  | 
| Microsoft.Web/sites | TotalAppDomains |  應用程式網域總計  | 
| Microsoft.Web/sites | Threads |  執行緒計數  | 
| Microsoft.Web/sites | RequestsInApplicationQueue |  應用程式佇列中的要求數  | 
| Microsoft.Web/sites | Requests |  Requests  | 
| Microsoft.Web/sites | PrivateBytes |  私用位元組  | 
| Microsoft.Web/sites | MemoryWorkingSet |  記憶體工作集  | 
| Microsoft.Web/sites | IoWriteOperationsPerSecond |  每秒的 IO 寫入作業數  | 
| Microsoft.Web/sites | IoWriteBytesPerSecond |  每秒的 IO 寫入位元組數  | 
| Microsoft.Web/sites | IoReadOperationsPerSecond |  每秒的 IO 讀取作業數  | 
| Microsoft.Web/sites | IoReadBytesPerSecond |  每秒的 IO 讀取位元組數  | 
| Microsoft.Web/sites | IoOtherOperationsPerSecond |  每秒的 IO 其他作業數  | 
| Microsoft.Web/sites | IoOtherBytesPerSecond |  每秒的 IO 其他位元組數  | 
| Microsoft.Web/sites | HttpResponseTime |  回應時間  | 
| Microsoft.Web/sites | Http5xx |  Http 伺服器錯誤  | 
| Microsoft.Web/sites | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites | Http406 |  Http 406  | 
| Microsoft.Web/sites | Http404 |  Http 404  | 
| Microsoft.Web/sites | Http403 |  Http 403  | 
| Microsoft.Web/sites | Http401 |  Http 401  | 
| Microsoft.Web/sites | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites | Http101 |  Http 101  | 
| Microsoft.Web/sites | HealthCheckStatus |  健康情況檢查狀態  | 
| Microsoft.Web/sites | 處理 |  控制代碼計數  | 
| Microsoft.Web/sites | Gen2Collections |  Gen 2 記憶體回收  | 
| Microsoft.Web/sites | Gen1Collections |  Gen 1 記憶體回收  | 
| Microsoft.Web/sites | Gen0Collections |  Gen 0 記憶體回收  | 
| Microsoft.Web/sites | FunctionExecutionUnits |  函式執行單位  | 
| Microsoft.Web/sites | FunctionExecutionCount |  函式執行計數  | 
| Microsoft.Web/sites | CurrentAssemblies |  目前的組件  | 
| Microsoft.Web/sites | CpuTime |  CPU 時間  | 
| Microsoft.Web/sites | BytesSent |  資料輸出  | 
| Microsoft.Web/sites | BytesReceived |  資料輸入  | 
| Microsoft.Web/sites | AverageResponseTime |  平均回應時間  | 
| Microsoft.Web/sites | AverageMemoryWorkingSet |  平均記憶體工作集  | 
| Microsoft.Web/sites | AppConnections |  連接  | 
| Microsoft.Web/sites/slots | TotalAppDomainsUnloaded |  已卸載的應用程式網域總計  | 
| Microsoft.Web/sites/slots | TotalAppDomains |  應用程式網域總計  | 
| Microsoft.Web/sites/slots | Threads |  執行緒計數  | 
| Microsoft.Web/sites/slots | RequestsInApplicationQueue |  應用程式佇列中的要求數  | 
| Microsoft.Web/sites/slots | Requests |  Requests  | 
| Microsoft.Web/sites/slots | PrivateBytes |  私用位元組  | 
| Microsoft.Web/sites/slots | MemoryWorkingSet |  記憶體工作集  | 
| Microsoft.Web/sites/slots | IoWriteOperationsPerSecond |  每秒的 IO 寫入作業數  | 
| Microsoft.Web/sites/slots | IoWriteBytesPerSecond |  每秒的 IO 寫入位元組數  | 
| Microsoft.Web/sites/slots | IoReadOperationsPerSecond |  每秒的 IO 讀取作業數  | 
| Microsoft.Web/sites/slots | IoReadBytesPerSecond |  每秒的 IO 讀取位元組數  | 
| Microsoft.Web/sites/slots | IoOtherOperationsPerSecond |  每秒的 IO 其他作業數  | 
| Microsoft.Web/sites/slots | IoOtherBytesPerSecond |  每秒的 IO 其他位元組數  | 
| Microsoft.Web/sites/slots | HttpResponseTime |  回應時間  | 
| Microsoft.Web/sites/slots | Http5xx |  Http 伺服器錯誤  | 
| Microsoft.Web/sites/slots | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites/slots | Http406 |  Http 406  | 
| Microsoft.Web/sites/slots | Http404 |  Http 404  | 
| Microsoft.Web/sites/slots | Http403 |  Http 403  | 
| Microsoft.Web/sites/slots | Http401 |  Http 401  | 
| Microsoft.Web/sites/slots | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites/slots | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites/slots | Http101 |  Http 101  | 
| Microsoft.Web/sites/slots | HealthCheckStatus |  健康情況檢查狀態  | 
| Microsoft.Web/sites/slots | 處理 |  控制代碼計數  | 
| Microsoft.Web/sites/slots | Gen2Collections |  Gen 2 記憶體回收  | 
| Microsoft.Web/sites/slots | Gen1Collections |  Gen 1 記憶體回收  | 
| Microsoft.Web/sites/slots | Gen0Collections |  Gen 0 記憶體回收  | 
| Microsoft.Web/sites/slots | FunctionExecutionUnits |  函式執行單位  | 
| Microsoft.Web/sites/slots | FunctionExecutionCount |  函式執行計數  | 
| Microsoft.Web/sites/slots | CurrentAssemblies |  目前的組件  | 
| Microsoft.Web/sites/slots | CpuTime |  CPU 時間  | 
| Microsoft.Web/sites/slots | BytesSent |  資料輸出  | 
| Microsoft.Web/sites/slots | BytesReceived |  資料輸入  | 
| Microsoft.Web/sites/slots | AverageResponseTime |  平均回應時間  | 
| Microsoft.Web/sites/slots | AverageMemoryWorkingSet |  平均記憶體工作集  | 
| Microsoft.Web/sites/slots | AppConnections |  連接  | 
| Microsoft.Sql/servers/databases | cpu_percent | CPU 百分比 | 
| Microsoft.Sql/servers/databases | physical_data_read_percent | 資料 IO 百分比 | 
| Microsoft.Sql/servers/databases | log_write_percent | 記錄 IO 百分比 | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU 百分比 | 
| Microsoft.Sql/servers/databases | connection_successful | 成功的連線 | 
| Microsoft.Sql/servers/databases | connection_failed | 失敗的連線 | 
| Microsoft.Sql/servers/databases | blocked_by_firewall | 遭到防火牆封鎖 | 
| Microsoft.Sql/servers/databases | deadlock | 死結 | 
| Microsoft.Sql/servers/databases | xtp_storage_percent | 記憶體內部 OLTP 儲存體百分比 | 
| Microsoft.Sql/servers/databases | workers_percent | 背景工作角色百分比 | 
| Microsoft.Sql/servers/databases | sessions_percent | 工作階段百分比 | 
| Microsoft.Sql/servers/databases | dtu_used | 已使用 DTU | 
| Microsoft.Sql/servers/databases | cpu_used | 已使用的 CPU | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU 百分比 | 
| Microsoft.Sql/servers/databases | dwu_used | 已使用 DWU | 
| Microsoft.Sql/servers/databases | cache_hit_percent | 快取命中的百分比 | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_system_percent | 依系統百分比配置的工作負載群組 | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_wlg_effective_cap_percent | 依最大資源百分比配置的工作負載群組 | 
| Microsoft.Sql/servers/databases | wlg_active_queries | 工作負載群組使用中查詢 | 
| Microsoft.Sql/servers/databases | wlg_queued_queries | 已排入佇列的工作負載群組查詢 | 
| Microsoft.Sql/servers/databases | active_queries | 現用查詢 | 
| Microsoft.Sql/servers/databases | queued_queries | 佇列查詢 | 
| Microsoft.Sql/servers/databases | wlg_active_queries_timeouts | 工作負載群組查詢逾時 | 
| Microsoft.Sql/servers/databases | wlg_queued_queries_timeouts | 工作負載群組佇列查詢超時 | 
| Microsoft.Sql/servers/databases | wlg_effective_min_resource_percent | 有效的最低資源百分比 | 
| Microsoft.Sql/servers/databases | wlg_effective_cap_resource_percent | 有效的容量資源百分比 | 
| Microsoft.Sql/servers/elasticPools | cpu_percent | CPU 百分比 | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent | 資料 IO 百分比 | 
| Microsoft.Sql/servers/elasticPools | log_write_percent | 記錄 IO 百分比 | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU 百分比 | 
| Microsoft.Sql/servers/elasticPools | workers_percent | 背景工作角色百分比 | 
| Microsoft.Sql/servers/elasticPools | sessions_percent | 工作階段百分比 | 
| Microsoft.Sql/servers/elasticPools | eDTU_used | 已使用 eDTU | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent | 記憶體內部 OLTP 儲存體百分比 | 
| Microsoft.Sql/servers | dtu_consumption_percent | DTU 百分比 | 
| Microsoft.Sql/servers | dtu_used | 已使用 DTU | 
| Microsoft.Sql/managedInstances | avg_cpu_percent | CPU 百分比平均 | 

