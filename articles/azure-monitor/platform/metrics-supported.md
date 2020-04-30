---
title: Azure 監視器支援的資源類型計量
description: AAzure 監視器每一個資源類型的可用計量清單。
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: f2e3c03ba599128cc4552f64637ebd63efcb4578
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128456"
---
# <a name="supported-metrics-with-azure-monitor"></a>支援 Azure 監視器的計量

> [!NOTE]
> 這份清單主要是從 Azure 監視器計量 REST API 自動產生。 透過 Github 對這份清單所做的任何修改都可以不發出警告的方式撰寫。 如需如何進行永久更新的詳細資訊，請洽詢這篇文章的作者。

Azure 監視器提供數種與計量進行互動的方式，包括在入口網站中製作計量圖表、透過 REST API 存取計量，或使用 PowerShell 或 CLI 查詢計量。 

本文是 Azure 監視器的匯總計量管線目前提供的所有平臺（也就是自動收集）度量的完整清單。 清單上次更新日期為2020年3月27日。 在此日期之後變更或新增的計量可能不會出現在下方。 若要以程式設計方式查詢和存取計量清單，請使用[2018-01-01 api 版本](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

其他計量可在入口網站中或使用舊版 API 提供。 在 Azure 虛擬機器、Service Fabric 和雲端服務中執行的客體作業系統（來賓 os）計量**不**會列在此處。 這些必須透過一或多個在作業系統上執行或作為一部分的代理程式來收集。 您可以使用[自訂計量](metrics-custom-overview.md)API （目前處於公開預覽狀態），將代理程式計量傳送到平臺計量資料庫。 然後，您可以圖表、警示，並以其他方式使用來賓 os 計量，例如平臺計量。 如需詳細資訊，請參閱[監視代理程式總覽](agents-overview.md)。    

計量是依命名空間來組織。 如需服務及其所屬命名空間的清單，請參閱[Azure 服務的資源提供者](../../azure-resource-manager/management/azure-services-resource-providers.md)。 

> [!NOTE]
> 目前不支援透過診斷設定傳送多維度計量。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。
>
> 「例如」**：可以在每個佇列層級瀏覽並繪製事件中樞上的「內送郵件」計量。 不過，當您透過診斷設定匯出時，計量將會呈現為事件中樞內所有佇列的所有內送郵件。
>
> 如需可透過診斷設定匯出的平臺計量清單，請參閱[這篇文章](metrics-supported-export-diagnostic-settings.md)。


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|qpu_metric|QPU|計數|Average|QPU。 範圍 0-100 (S1)、0-200 (S2) 和 0-400 (S4)|ServerResourceType|
|memory_metric|記憶體|位元組|Average|記憶體。 範圍 0-25 GB (S1)、0-50 GB (S2) 和 0-100 GB (S4)|ServerResourceType|
|private_bytes_metric|私用位元組|位元組|Average|私用位元組。|ServerResourceType|
|virtual_bytes_metric|虛擬位元組|位元組|Average|虛擬位元組。|ServerResourceType|
|TotalConnectionRequests|連線要求的總計|計數|Average|連線要求的總計。 這些是達到的。|ServerResourceType|
|SuccessfullConnectionsPerSec|每秒連線成功的次數|每秒計數|Average|成功完成連線的速率。|ServerResourceType|
|TotalConnectionFailures|連線失敗的總計|計數|Average|連線嘗試失敗的總計。|ServerResourceType|
|CurrentUserSessions|目前的使用者工作階段|計數|Average|目前建立的使用者工作階段數目。|ServerResourceType|
|QueryPoolBusyThreads|查詢集區忙碌執行緒|計數|Average|查詢執行緒集區中的忙碌執行緒數目。|ServerResourceType|
|CommandPoolJobQueueLength|命令集區作業佇列長度|計數|Average|命令執行緒集區佇列中的作業數目。|ServerResourceType|
|ProcessingPoolJobQueueLength|處理集區作業佇列長度|計數|Average|處理執行緒集區佇列中的非 I/O 作業數目。|ServerResourceType|
|CurrentConnections|連接：目前的連接|計數|Average|目前已建立的用戶端連接數目。|ServerResourceType|
|CleanerCurrentPrice|記憶體︰清除工具目前價格|計數|Average|記憶體目前的價格 ($/位元組/時間)，並正規化為 1000。|ServerResourceType|
|CleanerMemoryShrinkable|記憶體︰可壓縮的清除器記憶體|位元組|Average|背景清除器將清除的記憶體數量，以 KB 為單位。|ServerResourceType|
|CleanerMemoryNonshrinkable|記憶體︰不可壓縮的清除器記憶體|位元組|Average|背景清除器將不會清除的記憶體數量，以 KB 為單位。|ServerResourceType|
|MemoryUsage|記憶體：記憶體使用量|位元組|Average|伺服器處理序用於計算清除器記憶體價格的記憶體使用量。 等於計數器 Process\PrivateBytes 加上記憶體對應的資料大小，忽略 xVelocity 記憶體內部分析引擎 (VertiPaq) 在超出 xVelocity 引擎記憶體限制外對應或配置的任何記憶體。|ServerResourceType|
|MemoryLimitHard|記憶體︰固定記憶體限制|位元組|Average|組態檔中的固定記憶體限制。|ServerResourceType|
|MemoryLimitHigh|記憶體︰記憶體上限|位元組|Average|來自組態檔的記憶體上限。|ServerResourceType|
|MemoryLimitLow|記憶體︰記憶體下限|位元組|Average|來自組態檔的記憶體下限。|ServerResourceType|
|MemoryLimitVertiPaq|記憶體︰記憶體限制 VertiPaq|位元組|Average|來自組態檔的記憶體內部限制。|ServerResourceType|
|Quota|記憶體︰配額|位元組|Average|目前的記憶體配額，以位元組為單位。 記憶體配額也就是指授與使用的記憶體，或是保留的記憶體。|ServerResourceType|
|QuotaBlocked|記憶體︰封鎖的配額|計數|Average|在釋放其他記憶體配額之前，目前已封鎖的配額要求數目。|ServerResourceType|
|VertiPaqNonpaged|記憶體︰未分頁的 VertiPaq|位元組|Average|工作集中已封鎖來供記憶體內部引擎使用的記憶體位元組。|ServerResourceType|
|VertiPaqPaged|記憶體︰分頁的 VertiPaq|位元組|Average|可供記憶體內部資料使用的分頁記憶體位元組。|ServerResourceType|
|RowsReadPerSec|處理︰每秒讀取的資料列|每秒計數|Average|從所有關聯式資料庫讀取資料列的速率。|ServerResourceType|
|RowsConvertedPerSec|處理︰每秒轉換的資料列|每秒計數|Average|處理期間資料列轉換的速率。|ServerResourceType|
|RowsWrittenPerSec|處理︰每秒寫入的資料列|每秒計數|Average|處理期間資料列寫入的速率。|ServerResourceType|
|CommandPoolBusyThreads|執行緒︰命令集區的忙碌執行緒數|計數|Average|命令執行緒集區中的忙碌執行緒數。|ServerResourceType|
|CommandPoolIdleThreads|執行緒︰命令集區的閒置執行緒數|計數|Average|命令執行緒集區中的閒置執行緒數。|ServerResourceType|
|LongParsingBusyThreads|執行緒︰完整剖析的忙碌執行緒數|計數|Average|完整剖析執行緒集區中的忙碌執行緒數目。|ServerResourceType|
|LongParsingIdleThreads|執行緒︰完整剖析的閒置執行緒數|計數|Average|完整剖析執行緒集區中的閒置執行緒數目。|ServerResourceType|
|LongParsingJobQueueLength|執行緒︰完整剖析的作業佇列長度|計數|Average|完整剖析執行緒集區佇列中的作業數目。|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|執行緒︰處理集區的忙碌 I/O 作業執行緒數|計數|Average|處理執行緒集區中執行 I/O 作業的執行緒數目。|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|執行緒︰處理集區的忙碌非 I/O 執行緒數|計數|Average|處理執行緒集區中執行非 I/O 作業的執行緒數目。|ServerResourceType|
|ProcessingPoolIOJobQueueLength|執行緒：處理集區 I/O 作業佇列長度|計數|Average|處理執行緒集區佇列中的 I/O 作業數目。|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|執行緒︰處理集區的閒置 I/O 作業執行緒數|計數|Average|處理執行緒集區中 I/O 作業的閒置執行緒數目。|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|執行緒︰處理集區的閒置非 I/O 執行緒數|計數|Average|處理執行緒集區中專供非 I/O 作業使用的閒置執行緒數目。|ServerResourceType|
|QueryPoolIdleThreads|執行緒︰查詢集區的閒置執行緒數|計數|Average|處理執行緒集區中 I/O 作業的閒置執行緒數目。|ServerResourceType|
|QueryPoolJobQueueLength|執行緒︰查詢集區的作業佇列長度|計數|Average|查詢執行緒集區佇列中的作業數目。|ServerResourceType|
|ShortParsingBusyThreads|執行緒︰簡短剖析的忙碌執行緒數|計數|Average|簡短剖析執行緒集區中的忙碌執行緒數目。|ServerResourceType|
|ShortParsingIdleThreads|執行緒︰簡短剖析的閒置執行緒數|計數|Average|簡短剖析執行緒集區中的閒置執行緒數目。|ServerResourceType|
|ShortParsingJobQueueLength|執行緒︰簡短剖析的作業佇列長度|計數|Average|簡短剖析執行緒集區佇列中的作業數目。|ServerResourceType|
|memory_thrashing_metric|記憶體猛移|百分比|Average|記憶體猛移的平均值。|ServerResourceType|
|mashup_engine_qpu_metric|M 引擎 QPU|計數|Average|混搭引擎處理序的 QPU 使用量|ServerResourceType|
|mashup_engine_memory_metric|M 引擎記憶體|位元組|Average|混搭引擎處理序的記憶體使用量|ServerResourceType|
|mashup_engine_private_bytes_metric|M 引擎私用位元組|位元組|Average|混搭引擎進程的私用位元組使用量。|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M 引擎虛擬位元組|位元組|Average|混搭引擎進程的虛擬位元組使用量。|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TotalRequests|閘道要求總數（已淘汰）|計數|總計|閘道要求數目-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|Location、Hostname|
|SuccessfulRequests|成功的閘道要求（已淘汰）|計數|總計|成功的閘道要求數目-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|Location、Hostname|
|UnauthorizedRequests|未經授權的閘道要求（已淘汰）|計數|總計|未授權的閘道要求數目-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|Location、Hostname|
|FailedRequests|失敗的閘道要求（已淘汰）|計數|總計|閘道要求中的失敗次數-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|Location、Hostname|
|OtherRequests|其他閘道要求（已淘汰）|計數|總計|其他閘道要求數目-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|Location、Hostname|
|Duration|閘道要求的整體持續期間|毫秒|Average|閘道要求的整體持續期間，以毫秒為單位|Location、Hostname|
|BackendDuration|後端要求的持續時間|毫秒|Average|後端要求的持續時間（以毫秒為單位）|Location、Hostname|
|Capacity|Capacity|百分比|Average|ApiManagement 服務的使用率衡量標準|位置|
|EventHubTotalEvents|EventHub 事件總數|計數|總計|傳送至 EventHub 的事件數目|位置|
|EventHubSuccessfulEvents|成功的 EventHub 事件|計數|總計|成功的 EventHub 事件數目|位置|
|EventHubTotalFailedEvents|失敗的 EventHub 事件|計數|總計|失敗的 EventHub 事件數目|位置|
|EventHubRejectedEvents|已拒絕 EventHub 事件|計數|總計|已拒絕的 EventHub 事件數（錯誤設定或未經授權）|位置|
|EventHubThrottledEvents|已節流的 EventHub 事件|計數|總計|已節流的 EventHub 事件數目|位置|
|EventHubTimedoutEvents|已超時的 EventHub 事件|計數|總計|已超時的 EventHub 事件數|位置|
|EventHubDroppedEvents|已卸載 EventHub 事件|計數|總計|因為達到佇列大小上限而略過的事件數目|位置|
|EventHubTotalBytesSent|EventHub 事件的大小|位元組|總計|EventHub 事件的總大小（以位元組為單位）|位置|
|Requests|Requests|計數|總計|具有多個維度的閘道要求計量|Location、Hostname、LastErrorReason、BackendResponseCode、GatewayResponseCode、BackendResponseCodeCategory、GatewayResponseCodeCategory|
|NetworkConnectivity|資源的網路線上狀態（預覽）|計數|總計|來自 API 管理服務之相依資源類型的網路線上狀態|Location、ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>AppConfiguration/configurationStores

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|計數|計數|傳入的 HTTP 要求總數。|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|計數|Average|Http 要求的延遲。|StatusCode|


## <a name="microsoftappplatformspring"></a>AppPlatform/春季

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|系統 CPU 使用量百分比|百分比|Average|整個系統的最近 cpu 使用量|AppName、Pod|
|AppCpuUsagePercentage|應用程式 CPU 使用量百分比|百分比|Average|應用程式 JVM CPU 使用量百分比|AppName、Pod|
|AppMemoryCommitted|已指派應用程式記憶體|位元組|Average|指派給 JVM 的記憶體（位元組）|AppName、Pod|
|AppMemoryUsed|使用的應用程式記憶體|位元組|Average|使用的應用程式記憶體（位元組）|AppName、Pod|
|AppMemoryMax|應用程式記憶體最大值|位元組|最大值|可用於記憶體管理的最大記憶體數量（以位元組為單位）|AppName、Pod|
|MaxOldGenMemoryPoolBytes|可用的舊世代資料大小上限|位元組|Average|舊版世代記憶體集區的大小上限|AppName、Pod|
|OldGenMemoryPoolBytes|舊的產生資料大小|位元組|Average|完整 GC 之後的舊層代記憶體集區大小|AppName、Pod|
|OldGenPromotedBytes|升級為舊的產生資料大小|位元組|最大值|在 GC 到 GC 之後，舊的世代記憶體集區大小正增加的計數|AppName、Pod|
|YoungGenPromotedBytes|升階為年輕產生資料大小|位元組|最大值|在一個 GC 到下一個之前，會增加年輕世代記憶體集區的大小|AppName、Pod|
|GCPauseTotalCount|GC 暫停計數|計數|總計|GC 暫停計數|AppName、Pod|
|GCPauseTotalTime|GC 暫停總時間|毫秒|總計|GC 暫停總時間|AppName、Pod|
|TomcatSentBytes|Tomcat 總傳送位元組數|位元組|總計|Tomcat 總傳送位元組數|AppName、Pod|
|TomcatReceivedBytes|Tomcat 接收的位元組總數|位元組|總計|Tomcat 接收的位元組總數|AppName、Pod|
|TomcatRequestTotalTime|Tomcat 要求總次數|毫秒|總計|Tomcat 要求總次數|AppName、Pod|
|TomcatRequestTotalCount|Tomcat 要求總計數|計數|總計|Tomcat 要求總計數|AppName、Pod|
|TomcatResponseAvgTime|Tomcat 要求平均時間|毫秒|Average|Tomcat 要求平均時間|AppName、Pod|
|TomcatRequestMaxTime|Tomcat 要求最大時間|毫秒|最大值|Tomcat 要求最大時間|AppName、Pod|
|TomcatErrorCount|Tomcat 全域錯誤|計數|總計|Tomcat 全域錯誤|AppName、Pod|
|TomcatSessionActiveMaxCount|Tomcat 會話最大使用中計數|計數|總計|Tomcat 會話最大使用中計數|AppName、Pod|
|TomcatSessionAliveMaxTime|Tomcat 會話最大的存留時間|毫秒|最大值|Tomcat 會話最大的存留時間|AppName、Pod|
|TomcatSessionActiveCurrentCount|Tomcat 會話的生存計數|計數|總計|Tomcat 會話的生存計數|AppName、Pod|
|TomcatSessionCreatedCount|Tomcat 會話已建立計數|計數|總計|Tomcat 會話已建立計數|AppName、Pod|
|TomcatSessionExpiredCount|Tomcat 會話過期計數|計數|總計|Tomcat 會話過期計數|AppName、Pod|
|TomcatSessionRejectedCount|Tomcat 會話已拒絕計數|計數|總計|Tomcat 會話已拒絕計數|AppName、Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TotalJob|工作總數|計數|總計|工作總數|Runbook，狀態|
|TotalUpdateDeploymentRuns|更新部署執行總計|計數|總計|軟體更新部署執行總計|SoftwareUpdateConfigurationName，狀態|
|TotalUpdateDeploymentMachineRuns|更新部署機器執行總計|計數|總計|在軟體更新部署執行中執行的軟體更新部署電腦總數|SoftwareUpdateConfigurationName、Status、TargetComputer、SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CoreCount|專用核心計數|計數|總計|Batch 帳戶中的專用核心總數|無|
|TotalNodeCount|專用節點計數|計數|總計|Batch 帳戶中的專用節點總數|無|
|LowPriorityCoreCount|LowPriority 核心計數|計數|總計|Batch 帳戶中的低優先順序核心總數|無|
|TotalLowPriorityNodeCount|低優先順序節點計數|計數|總計|Batch 帳戶中的低優先順序節點總數|無|
|CreatingNodeCount|建立節點計數|計數|總計|正在建立的節點數目|無|
|StartingNodeCount|啟動節點計數|計數|總計|啟動的節點數目|無|
|WaitingForStartTaskNodeCount|等候啟動工作節點計數|計數|總計|等待啟動工作完成的節點數目|無|
|StartTaskFailedNodeCount|啟動工作失敗的節點計數|計數|總計|啟動工作失敗的節點數目|無|
|IdleNodeCount|閒置的節點計數|計數|總計|閒置節點的數目|無|
|OfflineNodeCount|離線節點計數|計數|總計|離線節點的數目|無|
|RebootingNodeCount|重新啟動節點計數|計數|總計|重新啟動節點的數目|無|
|RebootingNodeCount|重新安裝映像節點計數|計數|總計|重新安裝映像的節點數目|無|
|RunningNodeCount|執行節點計數|計數|總計|執行節點的數目|無|
|LeavingPoolNodeCount|離開集區節點計數|計數|總計|離開集區的節點數目|無|
|UnusableNodeCount|無法使用的節點計數|計數|總計|無法使用的節點數目|無|
|PreemptedNodeCount|先占節點計數|計數|總計|先占節點的數量|無|
|TaskStartEvent|工作啟動的事件|計數|總計|已啟動的工作總次數|poolId，jobId|
|TaskCompleteEvent|工作完成事件|計數|總計|已完成的工作總次數|poolId，jobId|
|TaskFailEvent|工作失敗事件|計數|總計|失敗狀態中已完成的工作總數|poolId，jobId|
|PoolCreateEvent|集區建立事件|計數|總計|已建立集區的總數|poolId|
|PoolResizeStartEvent|調整集區大小啟動事件|計數|總計|已啟動的調整集區大小總次數|poolId|
|PoolResizeCompleteEvent|調整集區大小完成事件|計數|總計|已完成的調整集區大小總次數|poolId|
|PoolDeleteStartEvent|集區刪除啟動事件|計數|總計|已啟動的集區刪除總次數|poolId|
|PoolDeleteCompleteEvent|集區刪除完成事件|計數|總計|已完成的集區刪除總次數|poolId|
|JobDeleteCompleteEvent|作業刪除完成事件|計數|總計|已成功刪除的作業總數。|jobId|
|JobDeleteStartEvent|作業刪除啟動事件|計數|總計|已要求要刪除的作業總數。|jobId|
|JobDisableCompleteEvent|作業停用完成事件|計數|總計|已成功停用的作業總數。|jobId|
|JobDisableStartEvent|作業停用啟動事件|計數|總計|已要求要停用的作業總數。|jobId|
|JobStartEvent|作業啟動事件|計數|總計|已成功啟動的作業總數。|jobId|
|JobTerminateCompleteEvent|作業終止完成事件|計數|總計|已成功終止的作業總數。|jobId|
|JobTerminateCompleteEvent|作業終止啟動事件|計數|總計|已要求要終止的作業總數。|jobId|


## <a name="microsoftbatchaiworkspaces"></a>BatchAI/工作區

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|已提交作業|已提交作業|計數|總計|已提交的作業數目|案例，ClusterName|
|工作已完成|工作已完成|計數|總計|已完成的作業數目|案例，ClusterName，ResultType|
|節點總計|節點總計|計數|Average|總節點數|案例，ClusterName|
|現用節點|現用節點|計數|Average|執行節點的數目|案例，ClusterName|
|閒置節點|閒置節點|計數|Average|閒置節點的數目|案例，ClusterName|
|無法使用的節點|無法使用的節點|計數|Average|無法使用的節點數目|案例，ClusterName|
|搶先節點|搶先節點|計數|Average|先占節點的數量|案例，ClusterName|
|離開節點|離開節點|計數|Average|離開節點的數目|案例，ClusterName|
|核心總數|核心總數|計數|Average|核心總數|案例，ClusterName|
|現用核心|現用核心|計數|Average|使用中核心數目|案例，ClusterName|
|閒置核心|閒置核心|計數|Average|閒置核心數|案例，ClusterName|
|無法使用的核心|無法使用的核心|計數|Average|無法使用的核心數目|案例，ClusterName|
|搶先核心|搶先核心|計數|Average|搶先核心數|案例，ClusterName|
|離開核心|離開核心|計數|Average|離開核心數|案例，ClusterName|
|配額使用量百分比|配額使用量百分比|計數|Average|使用的配額百分比|案例，ClusterName，VmFamilyName，VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>區塊鏈/blockchainMembers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|CPU 使用量百分比|百分比|最大值|CPU 使用量百分比|節點|
|MemoryUsage|記憶體使用量|位元組|Average|記憶體使用量|節點|
|MemoryLimit|記憶體限制|位元組|Average|記憶體限制|節點|
|MemoryUsagePercentageInDouble|記憶體使用量百分比|百分比|Average|記憶體使用量百分比|節點|
|StorageUsage|存放裝置使用量|位元組|Average|存放裝置使用量|節點|
|IOReadBytes|IO 讀取位元組|位元組|總計|IO 讀取位元組|節點|
|IOWriteBytes|IO 寫入位元組|位元組|總計|IO 寫入位元組|節點|
|ConnectionAccepted|接受的連接|計數|總計|接受的連接|節點|
|ConnectionHandled|已處理的連接|計數|總計|已處理的連接|節點|
|ConnectionActive|作用中的連線|計數|Average|作用中的連線|節點|
|RequestHandled|已處理的要求|計數|總計|已處理的要求|節點|
|ProcessedBlocks|已處理的區塊|計數|總計|已處理的區塊|節點|
|ProcessedTransactions|已處理的交易|計數|總計|已處理的交易|節點|
|擱置交易|暫止交易|計數|Average|暫止交易|節點|
|QueuedTransactions|佇列交易|計數|Average|佇列交易|節點|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|connectedclients|連線的用戶端|計數|最大值||ShardId|
|totalcommandsprocessed|總作業數|計數|總計||ShardId|
|cachehits|快取點擊|計數|總計||ShardId|
|cachemisses|快取遺漏|計數|總計||ShardId|
|cachemissrate|快取遺漏率|百分比|cachemissrate||ShardId|
|getcommands|取得|計數|總計||ShardId|
|setcommands|集合|計數|總計||ShardId|
|operationsPerSecond|每秒的作業數|計數|最大值||ShardId|
|evictedkeys|收回的金鑰|計數|總計||ShardId|
|totalkeys|索引鍵總計|計數|最大值||ShardId|
|expiredkeys|到期的金鑰|計數|總計||ShardId|
|usedmemory|已使用的記憶體|位元組|最大值||ShardId|
|usedmemorypercentage|已用的記憶體百分比|百分比|最大值||ShardId|
|usedmemoryRss|已用的記憶體 RSS|位元組|最大值||ShardId|
|serverLoad|伺服器負載|百分比|最大值||ShardId|
|cacheWrite|快取寫入|每秒位元組|最大值||ShardId|
|cacheRead|快取讀取|每秒位元組|最大值||ShardId|
|percentProcessorTime|CPU|百分比|最大值||ShardId|
|cacheLatency|快取延遲 (毫秒) (預覽)|計數|Average||ShardId|
|錯誤|Errors|計數|最大值||ShardId、ErrorType|
|connectedclients0|連線的用戶端 (分區 0)|計數|最大值||無|
|totalcommandsprocessed0|總作業數 (分區 0)|計數|總計||無|
|cachehits0|快取點擊 (分區 0)|計數|總計||無|
|cachemisses0|快取遺漏 (分區 0)|計數|總計||無|
|getcommands0|取得 (分區 0)|計數|總計||無|
|setcommands0|設定 (分區 0)|計數|總計||無|
|operationsPerSecond0|每秒的作業數 (分區 0)|計數|最大值||無|
|evictedkeys0|收回的金鑰 (分區 0)|計數|總計||無|
|totalkeys0|金鑰總數 (分區 0)|計數|最大值||無|
|expiredkeys0|到期的金鑰 (分區 0)|計數|總計||無|
|usedmemory0|已用的記憶體 (分區 0)|位元組|最大值||無|
|usedmemoryRss0|已用的記憶體 RSS (分區 0)|位元組|最大值||無|
|serverLoad0|伺服器負載 (分區 0)|百分比|最大值||無|
|cacheWrite0|快取寫入 (分區 0)|每秒位元組|最大值||無|
|cacheRead0|快取讀取 (分區 0)|每秒位元組|最大值||無|
|percentProcessorTime0|CPU (分區 0)|百分比|最大值||無|
|connectedclients1|連線的用戶端 (分區 1)|計數|最大值||無|
|totalcommandsprocessed1|總作業數 (分區 1)|計數|總計||無|
|cachehits1|快取點擊 (分區 1)|計數|總計||無|
|cachemisses1|快取遺漏 (分區 1)|計數|總計||無|
|getcommands1|取得 (分區 1)|計數|總計||無|
|setcommands1|設定 (分區 1)|計數|總計||無|
|operationsPerSecond1|每秒的作業數 (分區 1)|計數|最大值||無|
|evictedkeys1|收回的金鑰 (分區 1)|計數|總計||無|
|totalkeys1|金鑰總數 (分區 1)|計數|最大值||無|
|expiredkeys1|到期的金鑰 (分區 1)|計數|總計||無|
|usedmemory1|已用的記憶體 (分區 1)|位元組|最大值||無|
|usedmemoryRss1|已用的記憶體 RSS (分區 1)|位元組|最大值||無|
|serverLoad1|伺服器負載 (分區 1)|百分比|最大值||無|
|cacheWrite1|快取寫入 (分區 1)|每秒位元組|最大值||無|
|cacheRead1|快取讀取 (分區 1)|每秒位元組|最大值||無|
|percentProcessorTime1|CPU (分區 1)|百分比|最大值||無|
|connectedclients2|連線的用戶端 (分區 2)|計數|最大值||無|
|totalcommandsprocessed2|總作業數 (分區 2)|計數|總計||無|
|cachehits2|快取點擊 (分區 2)|計數|總計||無|
|cachemisses2|快取遺漏 (分區 2)|計數|總計||無|
|getcommands2|取得 (分區 2)|計數|總計||無|
|setcommands2|設定 (分區 2)|計數|總計||無|
|operationsPerSecond2|每秒的作業數 (分區 2)|計數|最大值||無|
|evictedkeys2|收回的金鑰 (分區 2)|計數|總計||無|
|totalkeys2|金鑰總數 (分區 2)|計數|最大值||無|
|expiredkeys2|到期的金鑰 (分區 2)|計數|總計||無|
|usedmemory2|已用的記憶體 (分區 2)|位元組|最大值||無|
|usedmemoryRss2|已用的記憶體 RSS (分區 2)|位元組|最大值||無|
|serverLoad2|伺服器負載 (分區 2)|百分比|最大值||無|
|cacheWrite2|快取寫入 (分區 2)|每秒位元組|最大值||無|
|cacheRead2|快取讀取 (分區 2)|每秒位元組|最大值||無|
|percentProcessorTime2|CPU (分區 2)|百分比|最大值||無|
|connectedclients3|連線的用戶端 (分區 3)|計數|最大值||無|
|totalcommandsprocessed3|總作業數 (分區 3)|計數|總計||無|
|cachehits3|快取點擊 (分區 3)|計數|總計||無|
|cachemisses3|快取遺漏 (分區 3)|計數|總計||無|
|getcommands3|取得 (分區 3)|計數|總計||無|
|setcommands3|設定 (分區 3)|計數|總計||無|
|operationsPerSecond3|每秒的作業數 (分區 3)|計數|最大值||無|
|evictedkeys3|收回的金鑰 (分區 3)|計數|總計||無|
|totalkeys3|金鑰總數 (分區 3)|計數|最大值||無|
|expiredkeys3|到期的金鑰 (分區 3)|計數|總計||無|
|usedmemory3|已用的記憶體 (分區 3)|位元組|最大值||無|
|usedmemoryRss3|已用的記憶體 RSS (分區 3)|位元組|最大值||無|
|serverLoad3|伺服器負載 (分區 3)|百分比|最大值||無|
|cacheWrite3|快取寫入 (分區 3)|每秒位元組|最大值||無|
|cacheRead3|快取讀取 (分區 3)|每秒位元組|最大值||無|
|percentProcessorTime3|CPU (分區 3)|百分比|最大值||無|
|connectedclients4|連線的用戶端 (分區 4)|計數|最大值||無|
|totalcommandsprocessed4|總作業數 (分區 4)|計數|總計||無|
|cachehits4|快取點擊 (分區 4)|計數|總計||無|
|cachemisses4|快取遺漏 (分區 4)|計數|總計||無|
|getcommands4|取得 (分區 4)|計數|總計||無|
|setcommands4|設定 (分區 4)|計數|總計||無|
|operationsPerSecond4|每秒的作業數 (分區 4)|計數|最大值||無|
|evictedkeys4|收回的金鑰 (分區 4)|計數|總計||無|
|totalkeys4|金鑰總數 (分區 4)|計數|最大值||無|
|expiredkeys4|到期的金鑰 (分區 4)|計數|總計||無|
|usedmemory4|已用的記憶體 (分區 4)|位元組|最大值||無|
|usedmemoryRss4|已用的記憶體 RSS (分區 4)|位元組|最大值||無|
|serverLoad4|伺服器負載 (分區 4)|百分比|最大值||無|
|cacheWrite4|快取寫入 (分區 4)|每秒位元組|最大值||無|
|cacheRead4|快取讀取 (分區 4)|每秒位元組|最大值||無|
|percentProcessorTime4|CPU (分區 4)|百分比|最大值||無|
|connectedclients5|連線的用戶端 (分區 5)|計數|最大值||無|
|totalcommandsprocessed5|總作業數 (分區 5)|計數|總計||無|
|cachehits5|快取點擊 (分區 5)|計數|總計||無|
|cachemisses5|快取遺漏 (分區 5)|計數|總計||無|
|getcommands5|取得 (分區 5)|計數|總計||無|
|setcommands5|設定 (分區 5)|計數|總計||無|
|operationsPerSecond5|每秒的作業數 (分區 5)|計數|最大值||無|
|evictedkeys5|收回的金鑰 (分區 5)|計數|總計||無|
|totalkeys5|金鑰總數 (分區 5)|計數|最大值||無|
|expiredkeys5|到期的金鑰 (分區 5)|計數|總計||無|
|usedmemory5|已用的記憶體 (分區 5)|位元組|最大值||無|
|usedmemoryRss5|已用的記憶體 RSS (分區 5)|位元組|最大值||無|
|serverLoad5|伺服器負載 (分區 5)|百分比|最大值||無|
|cacheWrite5|快取寫入 (分區 5)|每秒位元組|最大值||無|
|cacheRead5|快取讀取 (分區 5)|每秒位元組|最大值||無|
|percentProcessorTime5|CPU (分區 5)|百分比|最大值||無|
|connectedclients6|連線的用戶端 (分區 6)|計數|最大值||無|
|totalcommandsprocessed6|總作業數 (分區 6)|計數|總計||無|
|cachehits6|快取點擊 (分區 6)|計數|總計||無|
|cachemisses6|快取遺漏 (分區 6)|計數|總計||無|
|getcommands6|取得 (分區 6)|計數|總計||無|
|setcommands6|設定 (分區 6)|計數|總計||無|
|operationsPerSecond6|每秒的作業數 (分區 6)|計數|最大值||無|
|evictedkeys6|收回的金鑰 (分區 6)|計數|總計||無|
|totalkeys6|金鑰總數 (分區 6)|計數|最大值||無|
|expiredkeys6|到期的金鑰 (分區 6)|計數|總計||無|
|usedmemory6|已用的記憶體 (分區 6)|位元組|最大值||無|
|usedmemoryRss6|已用的記憶體 RSS (分區 6)|位元組|最大值||無|
|serverLoad6|伺服器負載 (分區 6)|百分比|最大值||無|
|cacheWrite6|快取寫入 (分區 6)|每秒位元組|最大值||無|
|cacheRead6|快取讀取 (分區 6)|每秒位元組|最大值||無|
|percentProcessorTime6|CPU (分區 6)|百分比|最大值||無|
|connectedclients7|連線的用戶端 (分區 7)|計數|最大值||無|
|totalcommandsprocessed7|總作業數 (分區 7)|計數|總計||無|
|cachehits7|快取點擊 (分區 7)|計數|總計||無|
|cachemisses7|快取遺漏 (分區 7)|計數|總計||無|
|getcommands7|取得 (分區 7)|計數|總計||無|
|setcommands7|設定 (分區 7)|計數|總計||無|
|operationsPerSecond7|每秒的作業數 (分區 7)|計數|最大值||無|
|evictedkeys7|收回的金鑰 (分區 7)|計數|總計||無|
|totalkeys7|金鑰總數 (分區 7)|計數|最大值||無|
|expiredkeys7|到期的金鑰 (分區 7)|計數|總計||無|
|usedmemory7|已用的記憶體 (分區 7)|位元組|最大值||無|
|usedmemoryRss7|已用的記憶體 RSS (分區 7)|位元組|最大值||無|
|serverLoad7|伺服器負載 (分區 7)|百分比|最大值||無|
|cacheWrite7|快取寫入 (分區 7)|每秒位元組|最大值||無|
|cacheRead7|快取讀取 (分區 7)|每秒位元組|最大值||無|
|percentProcessorTime7|CPU (分區 7)|百分比|最大值||無|
|connectedclients8|連線的用戶端 (分區 8)|計數|最大值||無|
|totalcommandsprocessed8|總作業數 (分區 8)|計數|總計||無|
|cachehits8|快取點擊 (分區 8)|計數|總計||無|
|cachemisses8|快取遺漏 (分區 8)|計數|總計||無|
|getcommands8|取得 (分區 8)|計數|總計||無|
|setcommands8|設定 (分區 8)|計數|總計||無|
|operationsPerSecond8|每秒的作業數 (分區 8)|計數|最大值||無|
|evictedkeys8|收回的金鑰 (分區 8)|計數|總計||無|
|totalkeys8|金鑰總數 (分區 8)|計數|最大值||無|
|expiredkeys8|到期的金鑰 (分區 8)|計數|總計||無|
|usedmemory8|已用的記憶體 (分區 8)|位元組|最大值||無|
|usedmemoryRss8|已用的記憶體 RSS (分區 8)|位元組|最大值||無|
|serverLoad8|伺服器負載 (分區 8)|百分比|最大值||無|
|cacheWrite8|快取寫入 (分區 8)|每秒位元組|最大值||無|
|cacheRead8|快取讀取 (分區 8)|每秒位元組|最大值||無|
|percentProcessorTime8|CPU (分區 8)|百分比|最大值||無|
|connectedclients9|連線的用戶端 (分區 9)|計數|最大值||無|
|totalcommandsprocessed9|總作業數 (分區 9)|計數|總計||無|
|cachehits9|快取點擊 (分區 9)|計數|總計||無|
|cachemisses9|快取遺漏 (分區 9)|計數|總計||無|
|getcommands9|取得 (分區 9)|計數|總計||無|
|setcommands9|設定 (分區 9)|計數|總計||無|
|operationsPerSecond9|每秒的作業數 (分區 9)|計數|最大值||無|
|evictedkeys9|收回的金鑰 (分區 9)|計數|總計||無|
|totalkeys9|金鑰總數 (分區 9)|計數|最大值||無|
|expiredkeys9|到期的金鑰 (分區 9)|計數|總計||無|
|usedmemory9|已用的記憶體 (分區 9)|位元組|最大值||無|
|usedmemoryRss9|已用的記憶體 RSS (分區 9)|位元組|最大值||無|
|serverLoad9|伺服器負載 (分區 9)|百分比|最大值||無|
|cacheWrite9|快取寫入 (分區 9)|每秒位元組|最大值||無|
|cacheRead9|快取讀取 (分區 9)|每秒位元組|最大值||無|
|percentProcessorTime9|CPU (分區 9)|百分比|最大值||無|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft Cdn/cdnwebapplicationfirewallpolicies

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Web 應用程式防火牆要求計數|計數|總計|Web 應用程式防火牆處理的用戶端要求數|PolicyName、RuleName、Action|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|Average|虛擬機器目前使用的配置計算單位百分比。|無|
|Network In|Network In|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目。|無|
|Network Out|Network Out|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目。|無|
|Disk Read Bytes/Sec|磁碟讀取|每秒位元組|Average|監視期間從磁碟讀取的平均位元組。|無|
|Disk Write Bytes/Sec|磁碟寫入|每秒位元組|Average|監視期間寫入磁碟的平均位元組。|無|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS。|無|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS。|無|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|Average|虛擬機器目前使用的配置計算單位百分比。|RoleInstanceId|
|Network In|Network In|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目。|RoleInstanceId|
|Network Out|Network Out|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目。|RoleInstanceId|
|Disk Read Bytes/Sec|磁碟讀取|每秒位元組|Average|監視期間從磁碟讀取的平均位元組。|RoleInstanceId|
|Disk Write Bytes/Sec|磁碟寫入|每秒位元組|Average|監視期間寫入磁碟的平均位元組。|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS。|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS。|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|UsedCapacity|已使用容量|位元組|Average|帳戶使用的容量|無|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.classicstorage/storageAccounts/blobServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BlobCapacity|Blob 容量|位元組|Average|儲存體帳戶 Blob 服務所使用的儲存體量 (位元組)。|BlobType，層|
|BlobCount|Blob 計數|計數|Average|儲存體帳戶 Blob 服務中的 Blob 數目。|BlobType，層|
|ContainerCount|Blob 容器計數|計數|Average|儲存體帳戶 Blob 服務中的容器數目。|無|
|IndexCapacity|索引容量|位元組|Average|ADLS Gen2 （階層）索引所使用的儲存體數量（以位元組為單位）。|無|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.classicstorage/storageAccounts/tableServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TableCapacity|資料表容量|位元組|Average|儲存體帳戶資料表服務所使用的儲存體量 (位元組)。|無|
|TableCount|資料表計數|計數|Average|儲存體帳戶資料表服務中的資料表數目。|無|
|TableEntityCount|資料表實體計數|計數|Average|儲存體帳戶資料表服務中的資料表實體數目。|無|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.classicstorage/storageAccounts/fileServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|FileCapacity|檔案容量|位元組|Average|儲存體帳戶的檔案服務所使用的儲存體數量（以位元組為單位）。|FileShare|
|FileCount|檔案計數|計數|Average|儲存體帳戶之檔案服務中的檔案數目。|FileShare|
|FileShareCount|檔案共用計數|計數|Average|儲存體帳戶之檔案服務中的檔案共用數目。|無|
|FileShareSnapshotCount|檔案共用快照集計數|計數|Average|儲存體帳戶之檔案服務中的共用上存在的快照集數目。|FileShare|
|FileShareSnapshotSize|檔案共用快照集大小|位元組|Average|儲存體帳戶之檔案服務中的快照集所使用的儲存體數量（以位元組為單位）。|FileShare|
|FileShareQuota|檔案共用配額大小|位元組|Average|Azure 檔案儲存體服務可使用的儲存體數量上限（以位元組為單位）。|FileShare|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication、檔案共用|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication、檔案共用|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication、檔案共用|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication、檔案共用|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication、檔案共用|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication、檔案共用|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.classicstorage/storageAccounts/queueServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueueCapacity|佇列容量|位元組|Average|儲存體帳戶佇列服務所使用的儲存體量 (位元組)。|無|
|QueueCount|佇列計數|計數|Average|儲存體帳戶佇列服務中的佇列數目。|無|
|QueueMessageCount|佇列訊息計數|計數|Average|儲存體帳戶佇列服務中的佇列訊息大約數目。|無|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TotalCalls|呼叫總數|計數|總計|呼叫總數。|ApiName、OperationName、Region|
|SuccessfulCalls|成功的呼叫|計數|總計|成功的呼叫數。|ApiName、OperationName、Region|
|TotalErrors|錯誤總數|計數|總計|具有錯誤回應 (HTTP 回應碼 4xx 或 5xx) 的呼叫總數。|ApiName、OperationName、Region|
|BlockedCalls|已封鎖的呼叫|計數|總計|超過速率或配額限制的呼叫數目。|ApiName、OperationName、Region|
|ServerErrors|伺服器錯誤|計數|總計|具有服務內部錯誤 (HTTP 回應碼 5xx) 的呼叫數目。|ApiName、OperationName、Region|
|ClientErrors|用戶端錯誤|計數|總計|具有用戶端錯誤 (HTTP 回應碼 4xx) 的呼叫數目。|ApiName、OperationName、Region|
|DataIn|資料輸入|位元組|總計|內送資料大小，以位元組為單位。|ApiName、OperationName、Region|
|DataOut|資料輸出|位元組|總計|輸出資料大小，以位元組為單位。|ApiName、OperationName、Region|
|Latency|Latency|毫秒|Average|延遲，以毫秒為單位。|ApiName、OperationName、Region|
|TotalTokenCalls|呼叫權杖總計|計數|總計|呼叫權杖次數總計。|ApiName、OperationName、Region|
|CharactersTranslated|轉譯字元數|計數|總計|傳入文字要求中的字元總數。|ApiName、OperationName、Region|
|CharactersTrained|已定型的字元|計數|總計|已定型的字元總數。|ApiName、OperationName、Region|
|SpeechSessionDuration|語音工作階段持續時間|秒|總計|語音工作階段的總持續時間 (秒)。|ApiName、OperationName、Region|
|TotalTransactions|交易數總計|計數|總計|交易總數。|無|
|ProcessedImages|已處理的影像|計數|總計| 影像處理的交易數目。|ApiName，功能區，頻道，地區|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|Average|目前虛擬機器正在使用中的已配置計算單位百分比|無|
|Network In|計費中的網路（已淘汰）|位元組|總計|虛擬機器在所有網路介面上接收的可計費位元組數（連入流量）（已淘汰）|無|
|Network Out|網路輸出計費（已淘汰）|位元組|總計|虛擬機器（連出流量）在所有網路介面上傳出的可計費位元組數（已淘汰）|無|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|在監視期間從磁片讀取的位元組數|無|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|在監視期間寫入磁片的位元組數|無|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS|無|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS|無|
|剩餘 CPU 信用額度|剩餘 CPU 信用額度|計數|Average|可用於高載的信用額度總數|無|
|CPU Credits Consumed|CPU Credits Consumed|計數|Average|由虛擬機器取用的信用額度總數|無|
|每一磁碟讀取位元組/秒|資料磁片讀取位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|SlotId|
|每一磁碟寫入位元組/秒|資料磁片寫入位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|SlotId|
|每一磁碟讀取作業/秒|資料磁片讀取作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|SlotId|
|每一磁碟寫入作業/秒|資料磁片寫入作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|SlotId|
|每一磁碟 QD|資料磁片 QD [（已淘汰）](portal-disk-metrics-deprecation.md)] （portal-disk-metrics-deprecation.md）|計數|Average|資料磁碟佇列深度 (或佇列長度)|SlotId|
|OS 每一磁碟讀取位元組/秒|OS 磁片讀取位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無|
|OS 每一磁碟寫入位元組/秒|OS 磁片寫入位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無|
|OS 每一磁碟讀取作業/秒|OS 磁片讀取作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無|
|OS 每一磁碟寫入作業/秒|OS 磁片寫入作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無|
|OS 每一磁碟 QD|OS 磁片 QD [（已淘汰）](portal-disk-metrics-deprecation.md)|計數|Average|OS 磁碟佇列深度 (或佇列長度)|無|
|資料磁片讀取位元組數/秒|資料磁碟讀取位元組/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|LUN|
|資料磁片寫入位元組數/秒|資料磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|LUN|
|資料磁片讀取作業/秒|資料磁碟讀取作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|LUN|
|資料磁片寫入作業/秒|資料磁碟寫入作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|LUN|
|資料磁碟佇列深度|資料磁片佇列深度（預覽）|計數|Average|資料磁碟佇列深度 (或佇列長度)|LUN|
|OS 磁片讀取位元組數/秒|OS 磁碟讀取位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無|
|OS 磁片寫入位元組數/秒|OS 磁碟寫入位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無|
|OS 磁片讀取作業數/秒|OS 磁碟讀取作業/秒 (預覽)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無|
|OS 磁片寫入作業/秒|OS 磁碟寫入作業/秒 (預覽)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無|
|OS 磁碟佇列深度|OS 磁片佇列深度（預覽）|計數|Average|OS 磁碟佇列深度 (或佇列長度)|無|
|連入流量|連入流量|計數|Average|輸入流量是傳入方向的目前流量數（進入 VM 的流量）|無|
|輸出流程|輸出流程|計數|Average|輸出流程是輸出方向的目前流量數（從 VM 傳出的流量）|無|
|輸入流量最大建立速率|輸入流量最大建立速率|每秒計數|Average|輸入流量的最大建立速率（進入 VM 的流量）|無|
|輸出流量最大建立速率|輸出流量最大建立速率|每秒計數|Average|輸出流量的最大建立速率（從 VM 傳出的流量）|無|
|Premium 資料磁碟快取讀取命中|Premium 資料磁碟快取讀取命中（預覽）|百分比|Average|Premium 資料磁碟快取讀取命中|LUN|
|Premium 資料磁碟快取讀取遺漏|Premium 資料磁碟快取讀取遺漏（預覽）|百分比|Average|Premium 資料磁碟快取讀取遺漏|LUN|
|Premium OS 磁碟快取讀取命中|Premium OS 磁碟快取讀取命中（預覽）|百分比|Average|Premium OS 磁碟快取讀取命中|無|
|Premium OS 磁碟快取讀取遺漏|Premium OS 磁碟快取讀取遺漏（預覽）|百分比|Average|Premium OS 磁碟快取讀取遺漏|無|
|網路總計|網路總計|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|無|
|網路輸出總計|網路輸出總計|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|無|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|Average|目前虛擬機器正在使用中的已配置計算單位百分比|VMName|
|Network In|計費中的網路（已淘汰）|位元組|總計|虛擬機器在所有網路介面上接收的可計費位元組數（連入流量）（已淘汰）|VMName|
|Network Out|網路輸出計費（已淘汰）|位元組|總計|虛擬機器（連出流量）在所有網路介面上傳出的可計費位元組數（已淘汰）|VMName|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|在監視期間從磁片讀取的位元組數|VMName|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|在監視期間寫入磁片的位元組數|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS|VMName|
|剩餘 CPU 信用額度|剩餘 CPU 信用額度|計數|Average|可用於高載的信用額度總數|無|
|CPU Credits Consumed|CPU Credits Consumed|計數|Average|由虛擬機器取用的信用額度總數|無|
|每一磁碟讀取位元組/秒|資料磁片讀取位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|SlotId|
|每一磁碟寫入位元組/秒|資料磁片寫入位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|SlotId|
|每一磁碟讀取作業/秒|資料磁片讀取作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|SlotId|
|每一磁碟寫入作業/秒|資料磁片寫入作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|SlotId|
|每一磁碟 QD|資料磁片 QD [（已淘汰）](portal-disk-metrics-deprecation.md)|計數|Average|資料磁碟佇列深度 (或佇列長度)|SlotId|
|OS 每一磁碟讀取位元組/秒|OS 磁片讀取位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無|
|OS 每一磁碟寫入位元組/秒|OS 磁片寫入位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無|
|OS 每一磁碟讀取作業/秒|OS 磁片讀取作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無|
|OS 每一磁碟寫入作業/秒|OS 磁片寫入作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無|
|OS 每一磁碟 QD|OS 磁片 QD [（已淘汰）](portal-disk-metrics-deprecation.md)|計數|Average|OS 磁碟佇列深度 (或佇列長度)|無|
|資料磁片讀取位元組數/秒|資料磁碟讀取位元組/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|LUN，VMName|
|資料磁片寫入位元組數/秒|資料磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|LUN，VMName|
|資料磁片讀取作業/秒|資料磁碟讀取作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|LUN，VMName|
|資料磁片寫入作業/秒|資料磁碟寫入作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|LUN，VMName|
|資料磁碟佇列深度|資料磁片佇列深度（預覽）|計數|Average|資料磁碟佇列深度 (或佇列長度)|LUN，VMName|
|OS 磁片讀取位元組數/秒|OS 磁碟讀取位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|VMName|
|OS 磁片寫入位元組數/秒|OS 磁碟寫入位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|VMName|
|OS 磁片讀取作業數/秒|OS 磁碟讀取作業/秒 (預覽)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|VMName|
|OS 磁片寫入作業/秒|OS 磁碟寫入作業/秒 (預覽)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|VMName|
|OS 磁碟佇列深度|OS 磁片佇列深度（預覽）|計數|Average|OS 磁碟佇列深度 (或佇列長度)|VMName|
|連入流量|連入流量|計數|Average|輸入流量是傳入方向的目前流量數（進入 VM 的流量）|VMName|
|輸出流程|輸出流程|計數|Average|輸出流程是輸出方向的目前流量數（從 VM 傳出的流量）|VMName|
|輸入流量最大建立速率|輸入流量最大建立速率|每秒計數|Average|輸入流量的最大建立速率（進入 VM 的流量）|VMName|
|輸出流量最大建立速率|輸出流量最大建立速率|每秒計數|Average|輸出流量的最大建立速率（從 VM 傳出的流量）|VMName|
|Premium 資料磁碟快取讀取命中|Premium 資料磁碟快取讀取命中（預覽）|百分比|Average|Premium 資料磁碟快取讀取命中|LUN，VMName|
|Premium 資料磁碟快取讀取遺漏|Premium 資料磁碟快取讀取遺漏（預覽）|百分比|Average|Premium 資料磁碟快取讀取遺漏|LUN，VMName|
|Premium OS 磁碟快取讀取命中|Premium OS 磁碟快取讀取命中（預覽）|百分比|Average|Premium OS 磁碟快取讀取命中|VMName|
|Premium OS 磁碟快取讀取遺漏|Premium OS 磁碟快取讀取遺漏（預覽）|百分比|Average|Premium OS 磁碟快取讀取遺漏|VMName|
|網路總計|網路總計|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|VMName|
|網路輸出總計|網路輸出總計|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|Average|目前虛擬機器正在使用中的已配置計算單位百分比|無|
|Network In|計費中的網路（已淘汰）|位元組|總計|虛擬機器在所有網路介面上接收的可計費位元組數（連入流量）（已淘汰）|無|
|Network Out|網路輸出計費（已淘汰）|位元組|總計|虛擬機器（連出流量）在所有網路介面上傳出的可計費位元組數（已淘汰）|無|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|在監視期間從磁片讀取的位元組數|無|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|在監視期間寫入磁片的位元組數|無|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS|無|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS|無|
|剩餘 CPU 信用額度|剩餘 CPU 信用額度|計數|Average|可用於高載的信用額度總數|無|
|CPU Credits Consumed|CPU Credits Consumed|計數|Average|由虛擬機器取用的信用額度總數|無|
|每一磁碟讀取位元組/秒|資料磁片讀取位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|SlotId|
|每一磁碟寫入位元組/秒|資料磁片寫入位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|SlotId|
|每一磁碟讀取作業/秒|資料磁片讀取作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|SlotId|
|每一磁碟寫入作業/秒|資料磁片寫入作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|SlotId|
|每一磁碟 QD|資料磁片 QD [（已淘汰）](portal-disk-metrics-deprecation.md)|計數|Average|資料磁碟佇列深度 (或佇列長度)|SlotId|
|OS 每一磁碟讀取位元組/秒|OS 磁片讀取位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無|
|OS 每一磁碟寫入位元組/秒|OS 磁片寫入位元組數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無|
|OS 每一磁碟讀取作業/秒|OS 磁片讀取作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無|
|OS 每一磁碟寫入作業/秒|OS 磁片寫入作業數/秒[（已淘汰）](portal-disk-metrics-deprecation.md)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無|
|OS 每一磁碟 QD|OS 磁片 QD [（已淘汰）](portal-disk-metrics-deprecation.md)|計數|Average|OS 磁碟佇列深度 (或佇列長度)|無|
|資料磁片讀取位元組數/秒|資料磁碟讀取位元組/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|LUN|
|資料磁片寫入位元組數/秒|資料磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|LUN|
|資料磁片讀取作業/秒|資料磁碟讀取作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|LUN|
|資料磁片寫入作業/秒|資料磁碟寫入作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|LUN|
|資料磁碟佇列深度|資料磁片佇列深度（預覽）|計數|Average|資料磁碟佇列深度 (或佇列長度)|LUN|
|OS 磁片讀取位元組數/秒|OS 磁碟讀取位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無|
|OS 磁片寫入位元組數/秒|OS 磁碟寫入位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無|
|OS 磁片讀取作業數/秒|OS 磁碟讀取作業/秒 (預覽)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無|
|OS 磁片寫入作業/秒|OS 磁碟寫入作業/秒 (預覽)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無|
|OS 磁碟佇列深度|OS 磁片佇列深度（預覽）|計數|Average|OS 磁碟佇列深度 (或佇列長度)|無|
|連入流量|連入流量|計數|Average|輸入流量是傳入方向的目前流量數（進入 VM 的流量）|無|
|輸出流程|輸出流程|計數|Average|輸出流程是輸出方向的目前流量數（從 VM 傳出的流量）|無|
|輸入流量最大建立速率|輸入流量最大建立速率|每秒計數|Average|輸入流量的最大建立速率（進入 VM 的流量）|無|
|輸出流量最大建立速率|輸出流量最大建立速率|每秒計數|Average|輸出流量的最大建立速率（從 VM 傳出的流量）|無|
|Premium 資料磁碟快取讀取命中|Premium 資料磁碟快取讀取命中（預覽）|百分比|Average|Premium 資料磁碟快取讀取命中|LUN|
|Premium 資料磁碟快取讀取遺漏|Premium 資料磁碟快取讀取遺漏（預覽）|百分比|Average|Premium 資料磁碟快取讀取遺漏|LUN|
|Premium OS 磁碟快取讀取命中|Premium OS 磁碟快取讀取命中（預覽）|百分比|Average|Premium OS 磁碟快取讀取命中|無|
|Premium OS 磁碟快取讀取遺漏|Premium OS 磁碟快取讀取遺漏（預覽）|百分比|Average|Premium OS 磁碟快取讀取遺漏|無|
|網路總計|網路總計|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|無|
|網路輸出總計|網路輸出總計|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|無|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CpuUsage|CPU 使用率|計數|Average|所有核心的 CPU 使用率 (以 millicores 為單位)。|containerName|
|MemoryUsage|記憶體使用量|位元組|Average|總記憶體使用率 (位元組)。|containerName|
|NetworkBytesReceivedPerSecond|每秒接收的網路位元組數|位元組|Average|每秒接收的網路位元組數。|無|
|NetworkBytesReceivedPerSecond|每秒傳輸的網路位元組數|位元組|Average|每秒傳輸的網路位元組數。|無|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TotalPullCount|總提取計數|計數|Average|總映射提取次數|無|
|SuccessfulPullCount|成功的提取計數|計數|Average|成功的映射提取次數|無|
|TotalPushCount|總推送計數|計數|Average|映射推播總數總計|無|
|SuccessfulPushCount|成功推送計數|計數|Average|成功的映射推送數目|無|
|RunDuration|執行持續期間|毫秒|總計|執行持續時間（毫秒）|無|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|受控叢集中可用的 cpu 核心總數|計數|Average|受控叢集中可用的 cpu 核心總數|無|
|kube_node_status_allocatable_memory_bytes|受控叢集中可用的記憶體總量|位元組|Average|受控叢集中可用的記憶體總量|無|
|kube_pod_status_ready|處於就緒狀態的 Pod 數目|計數|Average|處於就緒狀態的 Pod 數目|命名空間、pod|
|kube_node_status_condition|各種節點條件的狀態|計數|Average|各種節點條件的狀態|條件、狀態、status2、節點|
|kube_pod_status_phase|各階段的 Pod 數目|計數|Average|各階段的 Pod 數目|階段、命名空間、pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>CustomProviders/resourceproviders

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SuccessfullRequests|成功的要求|計數|總計|自訂提供者提出的成功要求|HttpMethod、CallPath、StatusCode|
|FailedRequests|失敗的要求|計數|總計|取得自訂資源提供者的可用記錄|HttpMethod、CallPath、StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>DataBoxEdge/dataBoxEdgeDevices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|NICReadThroughput|讀取輸送量（網路）|每秒位元組|Average|閘道中所有磁片區的報告期間，裝置上網路介面的讀取輸送量。|InstanceName|
|NICWriteThroughput|寫入輸送量（網路）|每秒位元組|Average|閘道中所有磁片區的報告期間，裝置上網路介面的寫入輸送量。|InstanceName|
|CloudReadThroughputPerShare|雲端下載輸送量（共用）|每秒位元組|Average|報告期間從共用到 Azure 的下載輸送量。|共用|
|CloudUploadThroughputPerShare|雲端上傳輸送量（共用）|每秒位元組|Average|報告期間從共用到 Azure 的上傳輸送量。|共用|
|BytesUploadedToCloudPerShare|已上傳的雲端位元組數（共用）|位元組|Average|報告期間從共用上傳至 Azure 的位元組總數。|共用|
|TotalCapacity|總容量|位元組|Average|總容量|無|
|AvailableCapacity|可用容量|位元組|Average|報告期間的可用容量（以位元組為單位）。|無|
|CloudUploadThroughput|雲端上傳輸送量|每秒位元組|Average|在報告期間，將雲端上傳至 Azure 的輸送量。|無|
|CloudReadThroughput|雲端下載輸送量|每秒位元組|Average|在報告期間，將雲端下載至 Azure 的輸送量。|無|
|BytesUploadedToCloud|已上傳的雲端位元組數（裝置）|位元組|Average|報告期間從裝置上傳至 Azure 的位元組總數。|無|
|HyperVVirtualProcessorUtilization|Edge 計算-CPU 百分比|百分比|Average|CPU 使用量百分比|InstanceName|
|HyperVMemoryUtilization|Edge 計算-記憶體使用量|百分比|Average|使用中的 RAM 數量|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.datacatalog/datacatalogs

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AssetDistributionByClassification|依分類的資產散發|計數|總計|指出已指派特定分類的資產數目，也就是使用該標籤來分類。|分類，來源|
|AssetDistributionByStorageType|依儲存體類型的資產散發|計數|總計|指出具有特定儲存體類型的資產數目。|StorageType|
|NumberOfAssetsWithClassifications|具有至少一個分類的資產數目|計數|Average|指出至少有一個標記分類的資產數目。|無|
|ScanCancelled|掃描已取消|計數|總計|表示已取消的掃描數目。|無|
|ScanCompleted|掃描完成|計數|總計|指出已成功完成的掃描數目。|無|
|ScanFailed|掃描失敗|計數|總計|指出失敗的掃描次數。|無|
|ScanTimeTaken|掃描花費時間|秒|總計|指出掃描時間總計（以秒為單位）。|無|
|CatalogActiveUsers|每日作用中使用者|計數|總計|每天的作用中使用者數目|無|
|CatalogUsage|依作業的使用量散發|計數|總計|指出使用者對目錄進行的作業數目，亦即存取、搜尋、詞彙。|作業|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|FailedRuns|失敗的執行|計數|總計||pipelineName、activityName|
|SuccessfulRuns|成功的執行|計數|總計||pipelineName、activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PipelineFailedRuns|失敗的管線執行計量|計數|總計||FailureType、Name|
|PipelineSucceededRuns|成功的管線執行計量|計數|總計||FailureType、Name|
|PipelineCancelledRuns|已取消的管線執行計量|計數|總計||FailureType、Name|
|ActivityFailedRuns|失敗的活動執行計量|計數|總計||ActivityType、PipelineName、FailureType、Name|
|ActivitySucceededRuns|成功的活動執行計量|計數|總計||ActivityType、PipelineName、FailureType、Name|
|ActivityCancelledRuns|已取消的活動執行計量|計數|總計||ActivityType、PipelineName、FailureType、Name|
|TriggerFailedRuns|失敗的觸發程序執行計量|計數|總計||Name、FailureType|
|TriggerSucceededRuns|成功的觸發程序執行計量|計數|總計||Name、FailureType|
|TriggerCancelledRuns|已取消的觸發程式會執行計量|計數|總計||Name、FailureType|
|IntegrationRuntimeCpuPercentage|整合執行階段 CPU 使用率|百分比|Average||IntegrationRuntimeName，NodeName|
|IntegrationRuntimeAvailableMemory|整合執行階段可用記憶體|位元組|Average||IntegrationRuntimeName，NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Integration runtime 佇列持續時間|秒|Average||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|整合執行時間佇列長度|計數|Average||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|整合執行時間可用節點計數|計數|Average||IntegrationRuntimeName|
|MaxAllowedResourceCount|允許的實體數目上限|計數|最大值||無|
|MaxAllowedFactorySizeInGbUnits|允許的 factory 大小上限（GB 單位）|計數|最大值||無|
|ResourceCount|總實體數|計數|最大值||無|
|FactorySizeInGbUnits|總廠大小（GB 單位）|計數|最大值||無|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|JobEndedSuccess|成功的作業|計數|總計|成功作業計數。|無|
|JobEndedFailure|失敗的作業|計數|總計|失敗作業計數。|無|
|JobEndedCancelled|取消的作業|計數|總計|取消作業計數。|無|
|JobAUEndedSuccess|成功 AU 時間|秒|總計|成功作業的 AU 時間總計。|無|
|JobAUEndedFailure|失敗 AU 時間|秒|總計|失敗作業的 AU 時間總計。|無|
|JobAUEndedCancelled|取消的 AU 時間|秒|總計|取消作業的 AU 時間總計。|無|
|JobStage|階段中的工作|計數|總計|每個階段中的工作數目。|無|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TotalStorage|儲存體總計|位元組|最大值|儲存在帳戶中的總資料量。|無|
|DataWritten|寫入的資料|位元組|總計|寫入帳戶的總資料量。|無|
|DataRead|讀取的資料|位元組|總計|從帳戶讀取的總資料量。|無|
|WriteRequests|寫入要求|計數|總計|帳戶的資料寫入要求計數。|無|
|ReadRequests|讀取要求|計數|總計|帳戶的資料讀取要求計數。|無|


## <a name="microsoftdatashareaccounts"></a>DataShare/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ShareCount|已傳送的共用|計數|最大值|帳戶中已傳送的共用數|ShareName|
|ShareSubscriptionCount|已接收的共用|計數|最大值|帳戶中接收的共用數|ShareSubscriptionName|
|SucceededShareSynchronizations|已傳送共用成功快照集|計數|計數|帳戶中已傳送的共用成功快照集數目|無|
|FailedShareSynchronizations|傳送的共用失敗快照集|計數|計數|帳戶中的已傳送共用失敗快照集數目|無|
|SucceededShareSubscriptionSynchronizations|已接收共用成功快照集|計數|計數|帳戶中已接收的共用成功快照集數目|無|
|FailedShareSubscriptionSynchronizations|收到共用失敗快照集|計數|計數|帳戶中接收的共用失敗快照集數目|無|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|無|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|無|
|io_consumption_percent|IO 百分比|百分比|Average|IO 百分比|無|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|無|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|無|
|storage_limit|儲存體限制|位元組|最大值|儲存體限制|無|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|Average|伺服器記錄儲存體百分比|無|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|Average|使用的伺服器記錄儲存體|無|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|Average|伺服器記錄儲存體限制|無|
|active_connections|作用中的連線|計數|Average|作用中的連線|無|
|connections_failed|失敗的連線|計數|總計|失敗的連線|無|
|seconds_behind_master|複寫延遲 (秒)|計數|最大值|複寫延遲 (秒)|無|
|backup_storage_used|已使用的備份儲存體|位元組|Average|已使用的備份儲存體|無|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|無|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|無|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|無|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|無|
|io_consumption_percent|IO 百分比|百分比|Average|IO 百分比|無|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|無|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|無|
|storage_limit|儲存體限制|位元組|最大值|儲存體限制|無|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|Average|伺服器記錄儲存體百分比|無|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|Average|使用的伺服器記錄儲存體|無|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|最大值|伺服器記錄儲存體限制|無|
|active_connections|作用中的連線|計數|Average|作用中的連線|無|
|connections_failed|失敗的連線|計數|總計|失敗的連線|無|
|seconds_behind_master|複寫延遲 (秒)|計數|最大值|複寫延遲 (秒)|無|
|backup_storage_used|已使用的備份儲存體|位元組|Average|已使用的備份儲存體|無|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|無|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|無|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|無|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|無|
|io_consumption_percent|IO 百分比|百分比|Average|IO 百分比|無|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|無|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|無|
|storage_limit|儲存體限制|位元組|最大值|儲存體限制|無|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|Average|伺服器記錄儲存體百分比|無|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|Average|使用的伺服器記錄儲存體|無|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|最大值|伺服器記錄儲存體限制|無|
|active_connections|作用中的連線|計數|Average|作用中的連線|無|
|connections_failed|失敗的連線|計數|總計|失敗的連線|無|
|backup_storage_used|已使用的備份儲存體|位元組|Average|已使用的備份儲存體|無|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|無|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|無|
|pg_replica_log_delay_in_seconds|複本延隔時間|秒|最大值|複本延遲（秒）|無|
|pg_replica_log_delay_in_bytes|複本之間的最大延隔時間|位元組|最大值|最延遲複本的延遲（以位元組為單位）|無|


## <a name="microsoftdbforpostgresqlserversv2"></a>DBforPostgreSQL/serversv2

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|無|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|無|
|功耗|IOPS|計數|Average|每秒的 IO 作業數|無|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|無|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|無|
|active_connections|作用中的連線|計數|Average|作用中的連線|無|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|無|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|無|


## <a name="microsoftdbforpostgresqlsingleservers"></a>DBforPostgreSQL/singleservers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|無|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|無|
|功耗|IOPS|計數|Average|每秒的 IO 作業數|無|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|無|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|無|
|active_connections|作用中的連線|計數|Average|作用中的連線|無|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|無|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|無|
|connections_failed|失敗的連線|計數|總計|失敗的連線|無|
|connections_succeeded|成功的連接|計數|總計|成功的連接|無|
|maximum_used_transactionIDs|使用的交易識別碼上限|計數|Average|使用的交易識別碼上限|無|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遙測訊息傳送嘗試|計數|總計|要嘗試傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無|
|d2c.telemetry.ingress.success|已傳送的遙測訊息|計數|總計|成功傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無|
|c2d.commands.egress.complete.success|已完成 C2D 訊息傳遞|計數|總計|裝置已成功完成的雲端到裝置訊息傳遞數|無|
|c2d.commands.egress.abandon.success|已放棄的 C2D 訊息|計數|總計|裝置放棄的雲端到裝置訊息數|無|
|c2d.commands.egress.reject.success|拒絕的 C2D 訊息|計數|總計|裝置拒絕的雲端到裝置訊息數目|無|
|C2DMessagesExpired|C2D 郵件已過期（預覽）|計數|總計|已過期的雲端到裝置訊息數目|無|
|devices.totalDevices|裝置總計 (已淘汰)|計數|總計|向 IoT 中樞註冊的裝置數目|無|
|devices.connectedDevices.allProtocol|連接的裝置 (已淘汰) |計數|總計|連接至 IoT 中樞的裝置數目|無|
|d2c.telemetry.egress.success|路由：已傳遞的遙測訊息|計數|總計|使用 IoT 中樞路由成功地將訊息傳遞到所有端點的次數。 如果將訊息路由至多個端點，這個值會為每一次成功傳遞加 1。 如果將訊息多次傳遞到同一個端點，這個值會為每一次成功傳遞加 1。|無|
|d2c.telemetry.egress.dropped|路由：已捨棄的遙測訊息 |計數|總計|IoT 中樞路由因為端點無效而捨棄訊息的次數。 這個值不會計算傳遞到後援路由的訊息，因為捨棄的訊息不會傳遞到那裡。|無|
|d2c.telemetry.egress.orphaned|路由：已遺棄的遙測訊息 |計數|總計|IoT 中樞路由因為訊息不符合任何路由規則 (包括後援規則) 而遺棄訊息的次數。 |無|
|d2c.telemetry.egress.invalid|路由：不相容的遙測訊息|計數|總計|IoT 中樞路由因為與端點不相容而無法傳遞訊息的次數。 這個值不包含重試次數。|無|
|d2c.telemetry.egress.fallback|路由：傳遞至後援的訊息|計數|總計|IoT 中樞路由傳遞訊息至與後援路由相關聯之端點的次數。|無|
|d2c.endpoints.egress.eventHubs|路由：傳遞至事件中樞的訊息|計數|總計|IoT 中樞路由成功將訊息傳遞至事件中樞端點的次數。|無|
|d2c.endpoints.latency.eventHubs|路由：事件中樞的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到事件中樞端點之間的平均延遲 (毫秒)。|無|
|d2c.endpoints.egress.serviceBusQueues|路由：傳遞至服務匯流排佇列的訊息|計數|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排佇列端點的次數。|無|
|d2c.endpoints.latency.serviceBusQueues|路由：服務匯流排佇列的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒)。|無|
|d2c.endpoints.egress.serviceBusTopics|路由：傳遞至服務匯流排主題的訊息|計數|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排主題端點的次數。|無|
|d2c.endpoints.latency.serviceBusTopics|路由：服務匯流排主題的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒)。|無|
|d2c.endpoints.egress.builtIn.events|路由：傳遞至訊息/事件的訊息|計數|總計|IoT 中樞路由成功將訊息傳遞至內建端點 (訊息/事件) 的次數。|無|
|d2c.endpoints.latency.builtIn.events|路由：訊息/事件的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到內建端點 (訊息/事件) 之間的平均延遲 (毫秒)。|無|
|d2c.endpoints.egress.storage|路由：傳遞至儲存體的訊息|計數|總計|IoT 中樞路由成功將訊息傳遞至儲存體端點的次數。|無|
|d2c.endpoints.latency.storage|路由：儲存體的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到儲存體端點之間的平均延遲 (毫秒)。|無|
|d2c.endpoints.egress.storage.bytes|路由：傳遞至儲存體的資料|位元組|總計|IoT 中樞路由傳遞至儲存體端點的資料量 (位元組)。|無|
|d2c.endpoints.egress.storage.blobs|路由：傳遞至儲存體的 BLOB|計數|總計|IoT 中樞路由將 BLOB 傳遞至儲存體端點的次數。|無|
|EventGridDeliveries|事件方格傳遞（預覽）|計數|總計|已發佈至事件方格的 IoT 中樞事件數目。 使用 [結果] 維度來取得成功和失敗的要求數目。 [事件種類] 維度會顯示事件https://aka.ms/ioteventgrid)的類型（。|ResourceId、結果、事件識別碼|
|EventGridLatency|事件方格延遲（預覽）|毫秒|Average|當事件發佈至事件方格時，從 Iot 中樞事件產生的平均延遲（毫秒）。 這個數位是所有事件種類之間的平均值。 使用 [事件種類] 維度來查看特定事件種類的延遲。|ResourceId，事件識別碼|
|RoutingDeliveries|路由傳遞（預覽）|毫秒|總計|IoT 中樞嘗試使用路由將訊息傳遞至所有端點的次數。 若要查看成功或失敗的嘗試次數，請使用 [結果] 維度。 若要查看失敗的原因，例如 [無效]、[已捨棄] 或 [孤立]，請使用 [FailureReasonCategory] 維度。 您也可以使用端點和 EndpointType 維度來瞭解有多少訊息傳遞至不同的端點。 計量值會針對每個傳遞嘗試增加一個，包括訊息是否傳遞至多個端點，或訊息是否多次傳遞至相同的端點。|ResourceId、EndpointType、端點、FailureReasonCategory、Result、RoutingSource|
|RoutingDeliveryLatency|路由傳遞延遲（預覽）|毫秒|Average|訊息輸入到 IoT 中樞的平均延遲（毫秒）和遙測訊息輸入到端點。 您可以使用端點和 EndpointType 維度來瞭解不同端點的延遲。|ResourceId、EndpointType、終結點、RoutingSource|
|d2c.twin.read.success|裝置的成功對應項讀取|計數|總計|裝置起始的所有成功對應項讀取的計數。|無|
|d2c.twin.read.failure|裝置的失敗對應項讀取|計數|總計|裝置起始的所有失敗對應項讀取的計數。|無|
|d2c.twin.read.size|裝置的對應項讀取回應大小|位元組|Average|裝置起始的所有成功對應項讀取的平均值、最小值和最大值。|無|
|d2c.twin.update.success|裝置的成功對應項更新|計數|總計|裝置起始的所有成功對應項更新的計數。|無|
|d2c.twin.update.failure|裝置的失敗對應項更新|計數|總計|裝置起始的所有失敗對應項更新的計數。|無|
|d2c.twin.update.size|裝置的對應項更新大小|位元組|Average|裝置起始的所有成功對應項更新的大小平均值、最小值和最大值。|無|
|c2d.methods.success|成功直接方法叫用|計數|總計|所有成功直接方法呼叫的計數。|無|
|c2d.methods.failure|失敗直接方法叫用|計數|總計|所有失敗直接方法呼叫的計數。|無|
|c2d.methods.requestSize|直接方法叫用的要求大小|位元組|Average|所有成功直接方法要求的平均值、最小值和最大值。|無|
|c2d.methods.responseSize|直接方法叫用的回應大小|位元組|Average|所有成功直接方法回應的平均值、最小值和最大值。|無|
|c2d.twin.read.success|後端的成功對應項讀取|計數|總計|後端起始的所有成功對應項讀取的計數。|無|
|c2d.twin.read.failure|後端的失敗對應項讀取|計數|總計|後端起始的所有失敗對應項讀取的計數。|無|
|c2d.twin.read.size|後端的對應項讀取回應大小|位元組|Average|後端起始的所有成功對應項讀取的平均值、最小值和最大值。|無|
|c2d.twin.update.success|後端的成功對應項更新|計數|總計|後端起始的所有成功對應項更新的計數。|無|
|c2d.twin.update.failure|後端的失敗對應項更新|計數|總計|後端起始的所有失敗對應項更新的計數。|無|
|c2d.twin.update.size|後端的對應項更新大小|位元組|Average|後端起始的所有成功對應項更新的大小平均值、最小值和最大值。|無|
|twinQueries.success|成功對應項查詢|計數|總計|所有成功對應項查詢的計數。|無|
|twinQueries.failure|失敗對應項查詢|計數|總計|所有失敗對應項查詢的計數。|無|
|twinQueries.resultSize|對應項查詢結果大小|位元組|Average|所有成功對應項查詢的結果大小平均值、最小值和最大值。|無|
|jobs.createTwinUpdateJob.success|成功建立的對應項更新作業|計數|總計|所有成功建立的對應項更新作業計數。|無|
|jobs.createTwinUpdateJob.failure|建立失敗的對應項更新作業|計數|總計|所有建立失敗的對應項更新作業計數。|無|
|jobs.createDirectMethodJob.success|成功建立的方法叫用作業|計數|總計|所有成功建立的直接方法叫用作業計數。|無|
|jobs.createDirectMethodJob.failure|建立失敗的方法叫用作業|計數|總計|所有建立失敗的直接方法叫用作業計數。|無|
|jobs.listJobs.success|成功呼叫列出作業|計數|總計|所有成功呼叫列出作業的計數。|無|
|jobs.listJobs.failure|呼叫列出作業失敗|計數|總計|所有呼叫列出作業失敗的計數。|無|
|jobs.cancelJob.success|成功取消作業|計數|總計|所有成功呼叫取消作業的計數。|無|
|jobs.cancelJob.failure|取消作業失敗|計數|總計|所有呼叫取消作業失敗的計數。|無|
|jobs.queryJobs.success|成功作業查詢|計數|總計|所有成功呼叫查詢作業的計數。|無|
|jobs.queryJobs.failure|失敗作業查詢|計數|總計|所有呼叫查詢作業失敗的計數。|無|
|jobs.completed|已完成的工作|計數|總計|所有已完成的作業計數。|無|
|jobs.failed|失敗作業|計數|總計|所有失敗作業計數。|無|
|d2c.telemetry.ingress.sendThrottle|節流錯誤數目|計數|總計|因裝置輸送量節流而導致的節流錯誤數目|無|
|dailyMessageQuotaUsed|已使用的訊息總數|計數|Average|今日已使用的總訊息數|無|
|deviceDataUsage|裝置資料使用量總計|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|無|
|deviceDataUsageV2|裝置資料使用量總計 (預覽)|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|無|
|totalDeviceCount|裝置總計 (預覽)|計數|Average|向 IoT 中樞註冊的裝置數目|無|
|connectedDeviceCount|連接的裝置 (預覽)|計數|Average|連接至 IoT 中樞的裝置數目|無|
|組態|設定計量|計數|總計|設定作業的計量|無|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RegistrationAttempts|註冊嘗試數|計數|總計|嘗試的裝置註冊數|ProvisioningServiceName、IotHubName、Status|
|DeviceAssignments|已指派的裝置數|計數|總計|已指派到 IoT 中樞的裝置數|ProvisioningServiceName、IotHubName|
|AttestationAttempts|證明嘗試數|計數|總計|嘗試的裝置證明數|ProvisioningServiceName，狀態，通訊協定|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AddRegion|已新增區域|計數|計數|已新增區域|區域|
|AvailableStorage|可用的儲存體|位元組|總計|在5分鐘資料細微性報告的可用儲存空間總計|CollectionName、DatabaseName、Region|
|CassandraConnectionClosures|Cassandra 連線終止|計數|總計|已關閉的 Cassandra 連接數，以1分鐘的資料細微性回報|APIType、Region、ClosureReason|
|CassandraKeyspaceDelete|已刪除 Cassandra Keyspace|計數|計數|已刪除 Cassandra Keyspace|ApiKind、ApiKindResourceType、OperationType|
|CassandraKeyspaceThroughputUpdate|已更新 Cassandra Keyspace 輸送量|計數|計數|已更新 Cassandra Keyspace 輸送量|ApiKind、ApiKindResourceType、IsThroughputRequest|
|CassandraKeyspaceUpdate|已更新 Cassandra Keyspace|計數|計數|已更新 Cassandra Keyspace|ApiKind、ApiKindResourceType、IsThroughputRequest|
|CassandraRequestCharges|Cassandra 要求費用|計數|總計|針對提出的 Cassandra 要求所耗用的 ru|APIType、DatabaseName、CollectionName、Region、OperationType、ResourceType|
|CassandraRequests|Cassandra 要求|計數|計數|提出的 Cassandra 要求數目|APIType、DatabaseName、CollectionName、Region、OperationType、ResourceType、ErrorCode|
|CassandraTableDelete|已刪除 Cassandra 資料表|計數|計數|已刪除 Cassandra 資料表|ChildResourceName、ApiKind、ApiKindResourceType、OperationType|
|CassandraTableThroughputUpdate|已更新 Cassandra 資料表輸送量|計數|計數|已更新 Cassandra 資料表輸送量|ChildResourceName、ApiKind、ApiKindResourceType、IsThroughputRequest|
|CassandraTableUpdate|Cassandra 資料表已更新|計數|計數|Cassandra 資料表已更新|ChildResourceName、ApiKind、ApiKindResourceType、IsThroughputRequest|
|CreateAccount|帳戶已建立|計數|計數|帳戶已建立|無|
|DataUsage|資料使用量|位元組|總計|5分鐘資料細微性報告的總數據使用量|CollectionName、DatabaseName、Region|
|DeleteAccount|帳戶已刪除|計數|計數|帳戶已刪除|無|
|DocumentCount|文件計數|計數|總計|在5分鐘資料細微性報告的檔計數總計|CollectionName、DatabaseName、Region|
|DocumentQuota|文件配額|位元組|總計|5分鐘資料細微性報告的總儲存體配額|CollectionName、DatabaseName、Region|
|GremlinDatabaseDelete|已刪除 Gremlin 資料庫|計數|計數|已刪除 Gremlin 資料庫|ApiKind、ApiKindResourceType、OperationType|
|GremlinDatabaseThroughputUpdate|已更新 Gremlin 資料庫輸送量|計數|計數|已更新 Gremlin 資料庫輸送量|ApiKind、ApiKindResourceType、IsThroughputRequest|
|GremlinDatabaseUpdate|已更新 Gremlin 資料庫|計數|計數|已更新 Gremlin 資料庫|ApiKind、ApiKindResourceType、IsThroughputRequest|
|GremlinGraphDelete|已刪除 Gremlin 圖形|計數|計數|已刪除 Gremlin 圖形|ChildResourceName、ApiKind、ApiKindResourceType、OperationType|
|GremlinGraphThroughputUpdate|已更新 Gremlin 圖形輸送量|計數|計數|已更新 Gremlin 圖形輸送量|ChildResourceName、ApiKind、ApiKindResourceType、IsThroughputRequest|
|GremlinGraphUpdate|Gremlin 圖已更新|計數|計數|Gremlin 圖已更新|ChildResourceName、ApiKind、ApiKindResourceType、IsThroughputRequest|
|IndexUsage|索引使用量|位元組|總計|5分鐘資料細微性報告的索引使用量總計|CollectionName、DatabaseName、Region|
|MetadataRequests|中繼資料要求|計數|計數|中繼資料要求計數。 Cosmos DB 會維護每個帳戶的系統中繼資料集合，可讓您免費列舉集合、資料庫等及其設定。|DatabaseName、CollectionName、Region、StatusCode、Role|
|MongoCollectionDelete|已刪除 Mongo 集合|計數|計數|已刪除 Mongo 集合|ChildResourceName、ApiKind、ApiKindResourceType、OperationType|
|MongoCollectionThroughputUpdate|已更新 Mongo 集合輸送量|計數|計數|已更新 Mongo 集合輸送量|ChildResourceName、ApiKind、ApiKindResourceType、IsThroughputRequest|
|MongoCollectionUpdate|已更新 Mongo 集合|計數|計數|已更新 Mongo 集合|ChildResourceName、ApiKind、ApiKindResourceType、IsThroughputRequest|
|MongoDBDatabaseUpdate|已更新 Mongo 資料庫|計數|計數|已更新 Mongo 資料庫|ApiKind、ApiKindResourceType、IsThroughputRequest|
|MongoDatabaseDelete|已刪除 Mongo 資料庫|計數|計數|已刪除 Mongo 資料庫|ApiKind、ApiKindResourceType、OperationType|
|MongoDatabaseThroughputUpdate|已更新 Mongo 資料庫輸送量|計數|計數|已更新 Mongo 資料庫輸送量|ApiKind、ApiKindResourceType、IsThroughputRequest|
|MongoRequestCharge|Mongo 要求收費|計數|總計|已使用的 Mongo 要求單位|DatabaseName、CollectionName、Region、CommandName、ErrorCode、Status|
|MongoRequests|Mongo 要求|計數|計數|已提出的 Mongo 要求數目|DatabaseName、CollectionName、Region、CommandName、ErrorCode、Status|
|MongoRequestsCount|Mongo 要求率|每秒計數|Average|每秒的 Mongo 要求計數|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequestsDelete|Mongo 刪除要求率|每秒計數|Average|每秒的 Mongo 刪除要求數|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequestsInsert|Mongo 插入要求速率|每秒計數|Average|每秒的 Mongo 插入計數|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequestsQuery|Mongo 查詢要求率|每秒計數|Average|每秒的 Mongo 查詢要求數|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|MongoRequestsUpdate|Mongo 更新要求率|每秒計數|Average|每秒的 Mongo 更新要求數|DatabaseName、CollectionName、Region、CommandName、ErrorCode|
|NormalizedRUConsumption|正規化 RU 耗用量|百分比|最大值|每分鐘的最大 RU 耗用量百分比|CollectionName、DatabaseName、Region|
|ProvisionedThroughput|佈建的輸送量|計數|最大值|佈建的輸送量|DatabaseName、CollectionName|
|RegionFailover|區域已故障切換|計數|計數|區域已故障切換|無|
|RemoveRegion|區域已移除|計數|計數|區域已移除|區域|
|ReplicationLatency|P99 複寫延遲|毫秒|Average|異地複寫啟用的帳戶其跨來源和目標區域的 P99 複寫延遲|SourceRegion,TargetRegion|
|ServerSideLatency|伺服器端延遲|毫秒|Average|伺服器端延遲|DatabaseName、CollectionName、Region、ConnectionMode、OperationType、PublicAPIType|
|ServiceAvailability|服務可用性|百分比|Average|帳戶要求可用性為一小時、一天或每月的資料細微性|無|
|SqlContainerDelete|Sql 容器已刪除|計數|計數|Sql 容器已刪除|ChildResourceName、ApiKind、ApiKindResourceType、OperationType|
|SqlContainerThroughputUpdate|Sql 容器輸送量已更新|計數|計數|Sql 容器輸送量已更新|ChildResourceName、ApiKind、ApiKindResourceType、IsThroughputRequest|
|SqlContainerUpdate|已更新 Sql 容器|計數|計數|已更新 Sql 容器|ChildResourceName、ApiKind、ApiKindResourceType、IsThroughputRequest|
|SqlDatabaseDelete|Sql Database 已刪除|計數|計數|Sql Database 已刪除|ApiKind、ApiKindResourceType、OperationType|
|SqlDatabaseThroughputUpdate|Sql 資料庫輸送量已更新|計數|計數|Sql 資料庫輸送量已更新|ApiKind、ApiKindResourceType、IsThroughputRequest|
|SqlDatabaseUpdate|Sql Database 已更新|計數|計數|Sql Database 已更新|ApiKind、ApiKindResourceType、IsThroughputRequest|
|TableTableDelete|已刪除 AzureTable 資料表|計數|計數|已刪除 AzureTable 資料表|ApiKind、ApiKindResourceType、OperationType|
|TableTableThroughputUpdate|已更新 AzureTable 資料表輸送量|計數|計數|已更新 AzureTable 資料表輸送量|ApiKind、ApiKindResourceType、IsThroughputRequest|
|TableTableUpdate|AzureTable 資料表已更新|計數|計數|AzureTable 資料表已更新|ApiKind、ApiKindResourceType、IsThroughputRequest|
|TotalRequestUnits|要求單位總計|計數|總計|已使用的要求單位|DatabaseName、CollectionName、Region、StatusCode、OperationType、Status|
|TotalRequests|要求總數|計數|計數|進行的要求數目|DatabaseName、CollectionName、Region、StatusCode、OperationType、Status|
|UpdateAccountKeys|帳戶金鑰已更新|計數|計數|帳戶金鑰已更新|KeyType|
|UpdateAccountNetworkSettings|已更新帳戶網路設定|計數|計數|已更新帳戶網路設定|無|
|UpdateAccountReplicationSettings|已更新帳戶複寫設定|計數|計數|已更新帳戶複寫設定|無|
|UpdateDiagnosticsSettings|已更新帳戶診斷設定|計數|計數|已更新帳戶診斷設定|DiagnosticSettingsName、ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>EnterpriseKnowledgeGraph/服務

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TransactionCount|交易計數|計數|計數|總交易計數|TransactionCount|
|SuccessCount|成功計數|計數|計數|成功的交易計數|SuccessCount|
|FailureCount|失敗計數|計數|計數|失敗的交易計數|FailureCount|
|SuccessLatency|成功延遲|毫秒|Average|成功交易的延遲|SuccessCount|

## <a name="microsofteventgriddomains"></a>EventGrid/網域

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|計數|總計|發佈至本主題的事件總數|主題|
|PublishFailCount|發行失敗的事件|計數|總計|發佈至本主題失敗的事件總數|主題、ErrorType、錯誤|
|PublishSuccessLatencyInMs|發佈成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無|
|MatchedEventCount|相符的事件|計數|總計|與此事件訂閱相符的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DeliveryAttemptFailCount|傳遞失敗的事件|計數|總計|傳遞到此事件訂閱失敗的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、Error、ErrorType|
|DeliverySuccessCount|已傳遞的事件|計數|總計|已傳遞到此事件訂閱的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DroppedEventCount|卸除的事件|計數|總計|與此事件訂閱相符的已卸除事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DropReason|
|DeadLetteredCount|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|計數|總計|發佈至本主題的事件總數|無|
|PublishFailCount|發行失敗的事件|計數|總計|發佈至本主題失敗的事件總數|ErrorType，錯誤|
|UnmatchedEventCount|不相符的事件|計數|總計|不符合本主題中任何事件訂閱的事件總數|無|
|PublishSuccessLatencyInMs|發佈成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無|
|MatchedEventCount|相符的事件|計數|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|DeliveryAttemptFailCount|傳遞失敗的事件|計數|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|已傳遞的事件|計數|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|卸除的事件|計數|總計|與此事件訂閱相符的已卸除事件總數|DropReason,EventSubscriptionName|
|DeadLetteredCount|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>EventGrid/systemTopics

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|計數|總計|發佈至本主題的事件總數|無|
|PublishFailCount|發行失敗的事件|計數|總計|發佈至本主題失敗的事件總數|ErrorType，錯誤|
|UnmatchedEventCount|不相符的事件|計數|總計|不符合本主題中任何事件訂閱的事件總數|無|
|PublishSuccessLatencyInMs|發佈成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無|
|MatchedEventCount|相符的事件|計數|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|DeliveryAttemptFailCount|傳遞失敗的事件|計數|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|已傳遞的事件|計數|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|卸除的事件|計數|總計|與此事件訂閱相符的已卸除事件總數|DropReason,EventSubscriptionName|
|DeadLetteredCount|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|MatchedEventCount|相符的事件|計數|總計|與此事件訂閱相符的事件總數|無|
|DeliveryAttemptFailCount|傳遞失敗的事件|計數|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType|
|DeliverySuccessCount|已傳遞的事件|計數|總計|已傳遞到此事件訂閱的事件總數|無|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|無|
|DroppedEventCount|卸除的事件|計數|總計|與此事件訂閱相符的已卸除事件總數|DropReason|
|DeadLetteredCount|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|計數|總計|發佈至本主題的事件總數|無|
|PublishFailCount|發行失敗的事件|計數|總計|發佈至本主題失敗的事件總數|ErrorType，錯誤|
|UnmatchedEventCount|不相符的事件|計數|總計|不符合本主題中任何事件訂閱的事件總數|無|
|PublishSuccessLatencyInMs|發佈成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的要求|計數|總計|Microsoft.EventHub 的成功要求。|EntityName、OperationResult|
|ServerErrors|伺服器錯誤。|計數|總計|Microsoft.EventHub 的伺服器錯誤。|EntityName、OperationResult|
|UserErrors|使用者錯誤。|計數|總計|Microsoft.EventHub 的使用者錯誤。|EntityName、OperationResult|
|QuotaExceededErrors|超出配額的錯誤。|計數|總計|Microsoft.EventHub 超出配額的錯誤。|EntityName、OperationResult|
|ThrottledRequests|節流的要求。|計數|總計|Microsoft.EventHub 的節流要求。|EntityName、OperationResult|
|IncomingRequests|傳入的要求|計數|總計|Microsoft.EventHub 的傳入要求。|EntityName|
|IncomingMessages|傳入訊息|計數|總計|Microsoft.EventHub 的傳入訊息。|EntityName|
|OutgoingMessages|外送訊息|計數|總計|Microsoft.EventHub 的傳出訊息。|EntityName|
|IncomingBytes|傳入位元組數。|位元組|總計|Microsoft.EventHub 的傳入位元組數。|EntityName|
|OutgoingBytes|傳出位元組數。|位元組|總計|Microsoft.EventHub 的傳出位元組數。|EntityName|
|ActiveConnections|ActiveConnections|計數|Average|Microsoft.EventHub 的使用中連線總數。|無|
|ConnectionsOpened|已開啟的連線。|計數|Average|Microsoft.EventHub 的已開啟連線。|EntityName|
|ConnectionsClosed|已關閉的連線。|計數|Average|Microsoft.EventHub 的已關閉連線。|EntityName|
|CaptureBacklog|擷取待辦項目。|計數|總計|Microsoft.EventHub 的擷取待辦項目。|EntityName|
|CapturedMessages|已擷取的訊息。|計數|總計|Microsoft.EventHub 的已擷取訊息。|EntityName|
|CapturedBytes|已擷取的位元組。|位元組|總計|Microsoft.EventHub 的已擷取位元組。|EntityName|
|Size|Size|位元組|Average|EventHub 的大小 (以位元組為單位)。|EntityName|
|INREQS|傳入要求（已淘汰）|計數|總計|命名空間的連入傳送要求總數（已淘汰）|無|
|SUCCREQ|成功的要求（已淘汰）|計數|總計|命名空間的成功要求總數（已淘汰）|無|
|FAILREQ|失敗的要求（已淘汰）|計數|總計|命名空間的失敗要求總計（已淘汰）|無|
|SVRBSY|伺服器忙碌錯誤（已淘汰）|計數|總計|命名空間的伺服器忙碌錯誤總計（已淘汰）|無|
|INTERR|內部伺服器錯誤（已淘汰）|計數|總計|命名空間的內部伺服器錯誤總數（已淘汰）|無|
|MISCERR|其他錯誤（已淘汰）|計數|總計|命名空間的失敗要求總計（已淘汰）|無|
|INMSGS|傳入訊息（已過時）（已淘汰）|計數|總計|命名空間的傳入訊息總數。 此計量已過時。 請改用傳入訊息計量（已淘汰）|無|
|EHINMSGS|連入訊息數 (已過時)|計數|總計|命名空間的傳入訊息總計（已淘汰）|無|
|OUTMSGS|外寄訊息（已過時）（已被取代）|計數|總計|命名空間的傳出訊息總數。 此計量已過時。 請改用外寄訊息度量（已淘汰）|無|
|EHOUTMSGS|傳出訊息數 (已過時)|計數|總計|命名空間的傳出訊息總計（已淘汰）|無|
|EHINMBS|傳入位元組數（已過時）（已被取代）|位元組|總計|命名空間的事件中樞傳入訊息輸送量。 此計量已過時。 請改用傳入位元組度量（已淘汰）|無|
|EHINBYTES|傳入位元組數 (已過時)|位元組|總計|命名空間的事件中樞傳入訊息輸送量（已淘汰）|無|
|EHOUTMBS|傳出位元組數（已過時）（已被取代）|位元組|總計|命名空間的事件中樞傳出訊息輸送量。 此計量已過時。 請改用傳出位元組計量（已淘汰）|無|
|EHOUTBYTES|傳出位元組數 (已過時)|位元組|總計|命名空間的事件中樞傳出訊息輸送量（已淘汰）|無|
|EHABL|封存待處理專案訊息（已淘汰）|計數|總計|命名空間的待處理專案中的事件中樞封存訊息（已淘汰）|無|
|EHAMSGS|封存訊息（已淘汰）|計數|總計|命名空間中的事件中樞封存訊息（已淘汰）|無|
|EHAMBS|封存訊息輸送量（已淘汰）|位元組|總計|命名空間中的事件中樞封存訊息輸送量（已淘汰）|無|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的要求|計數|總計|Microsoft.EventHub 的成功要求。|OperationResult|
|ServerErrors|伺服器錯誤。|計數|總計|Microsoft.EventHub 的伺服器錯誤。|OperationResult|
|UserErrors|使用者錯誤。|計數|總計|Microsoft.EventHub 的使用者錯誤。|OperationResult|
|QuotaExceededErrors|超出配額的錯誤。|計數|總計|Microsoft.EventHub 超出配額的錯誤。|OperationResult|
|ThrottledRequests|節流的要求。|計數|總計|Microsoft.EventHub 的節流要求。|OperationResult|
|IncomingRequests|傳入的要求|計數|總計|Microsoft.EventHub 的傳入要求。|無|
|IncomingMessages|傳入訊息|計數|總計|Microsoft.EventHub 的傳入訊息。|無|
|OutgoingMessages|外送訊息|計數|總計|Microsoft.EventHub 的傳出訊息。|無|
|IncomingBytes|傳入位元組數。|位元組|總計|Microsoft.EventHub 的傳入位元組數。|無|
|OutgoingBytes|傳出位元組數。|位元組|總計|Microsoft.EventHub 的傳出位元組數。|無|
|ActiveConnections|ActiveConnections|計數|Average|Microsoft.EventHub 的使用中連線總數。|無|
|ConnectionsOpened|已開啟的連線。|計數|Average|Microsoft.EventHub 的已開啟連線。|無|
|ConnectionsClosed|已關閉的連線。|計數|Average|Microsoft.EventHub 的已關閉連線。|無|
|CaptureBacklog|擷取待辦項目。|計數|總計|Microsoft.EventHub 的擷取待辦項目。|無|
|CapturedMessages|已擷取的訊息。|計數|總計|Microsoft.EventHub 的已擷取訊息。|無|
|CapturedBytes|已擷取的位元組。|位元組|總計|Microsoft.EventHub 的已擷取位元組。|無|
|CPU|CPU|百分比|最大值|事件中樞叢集的 CPU 使用率 (百分比)|[角色]|
|AvailableMemory|可用的記憶體|百分比|最大值|事件中樞叢集的可用記憶體（以總記憶體的百分比表示）。|[角色]|
|Size|EventHub 的大小 (以位元組為單位)。|位元組|Average|EventHub 的大小 (以位元組為單位)。|[角色]|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|GatewayRequests|閘道要求數|計數|總計|閘道器要求數目|HttpStatus|
|CategorizedGatewayRequests|已分類的閘道要求數|計數|總計|依類別 (1xx/2xx/3xx/4xx/5xx) 區分的閘道要求數目|HttpStatus|
|NumActiveWorkers|作用中背景工作角色的數目|計數|最大值|作用中背景工作角色的數目|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ObservedMetricValue|觀察的計量值|計數|Average|執行時由自動調整規模計算的值|MetricTriggerSource|
|MetricThreshold|計量閾值|計數|Average|當自動調整規模執行時設定的自動調整規模閾值。|MetricTriggerRule|
|ObservedCapacity|觀察的容量|計數|Average|執行時向自動調整規模回報的容量。|無|
|ScaleActionsInitiated|已起始的調整規模動作數|計數|總計|調整規模作業的方向。|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|可用性|百分比|Average|已成功完成可用性測試的百分比|availabilityResult/name、availabilityResult/location|
|availabilityResults/計數|可用性集合|計數|計數|可用性測試計數|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|availabilityResults/duration|可用性測試持續時間|毫秒|Average|可用性測試持續時間|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|browserTimings/networkDuration|頁面載入網路連線時間|毫秒|Average|介於使用者要求和網路連線之間的時間。 包含 DNS 查閱和傳輸連接。|無|
|browserTimings/processingDuration|用戶端處理時間|毫秒|Average|從接收上個文件位元組直至載入 DOM 的經過時間。 系統可能仍在處理非同步要求。|無|
|browserTimings/receiveDuration|接收回應時間|毫秒|Average|介於第一個位元組和最後一個位元組 (或直到中斷連線) 之間的時間。|無|
|browserTimings/sendDuration|傳送要求時間|毫秒|Average|介於網路連線和接收第一個位元組之間的時間。|無|
|browserTimings/totalDuration|瀏覽器頁面載入時間|毫秒|Average|從使用者要求直至載入 DOM、樣式表、指令碼和影像的經過時間。|無|
|dependencies/count|相依性呼叫|計數|計數|應用程式對外部資源的呼叫計數。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、dependency/resultCode、operation/綜合、雲端/roleInstance、雲端/擁有項|
|dependencies/duration|相依性持續時間|毫秒|Average|應用程式對外部資源的呼叫持續時間。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、dependency/resultCode、operation/綜合、雲端/roleInstance、雲端/擁有項|
|dependencies/failed|相依性呼叫失敗|計數|計數|應用程式對外部資源進行相依性呼叫的失敗計數。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、dependency/resultCode、operation/綜合、雲端/roleInstance、雲端/擁有項|
|pageViews/count|頁面檢視|計數|計數|頁面檢視計數。|作業/綜合、雲端/擁有|
|pageViews/duration|頁面檢視載入時間|毫秒|Average|頁面檢視載入時間|作業/綜合、雲端/擁有|
|performanceCounters/requestExecutionTime|HTTP 要求執行時間|毫秒|Average|最近要求的執行時間。|cloud/roleInstance|
|performanceCounters/requestsInQueue|應用程式佇列中的 HTTP 要求數|計數|Average|應用程式要求佇列的長度。|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP 要求率|每秒計數|Average|每秒從 ASP.NET 發出所有應用程式要求的速率。|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|例外狀況比率|每秒計數|Average|回報給 Windows 的已處理與未處理例外狀況計數，包括 .NET 例外狀況以及轉換成 .NET 例外狀況的非受控例外狀況。|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|處理程序 IO 速率|每秒位元組|Average|每秒讀取與寫入檔案、 網路和裝置的總位元組數。|cloud/roleInstance|
|performanceCounters/processCpuPercentage|處理程序 CPU|百分比|Average|所有處理序執行緒使用處理器執行指令已耗用時間的百分比。 差異可介於 0 到 100 之間。 這個計量只表示 w3wp 處理序的效能。|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|處理器時間|百分比|Average|處理器針對非閒置執行緒所耗費時間的百分比。|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|可用的記憶體|位元組|Average|可立即用於配置給處理序或系統使用的實體記憶體。|cloud/roleInstance|
|performanceCounters/processPrivateBytes|處理程序私人位元組|位元組|Average|以獨佔方式指派至監視應用程式處理程序的記憶體。|cloud/roleInstance|
|requests/duration|伺服器回應時間|毫秒|Average|從接收 HTTP 要求直至完成傳送回應的經過時間。|request/performanceBucket、request/resultCode、operation/綜合、雲端/roleInstance、要求/成功、雲端/擁有|
|requests/count|伺服器要求|計數|計數|已完成 HTTP 要求的計數。|request/performanceBucket、request/resultCode、operation/綜合、雲端/roleInstance、要求/成功、雲端/擁有|
|requests/failed|失敗的要求|計數|計數|標示為失敗的 HTTP 要求計數。 在大多數情況下，這些要求的回應碼會 >= 400，且不等於 401。|request/performanceBucket、request/resultCode、request/success、operation/綜合、cloud/roleInstance、雲端/擁有|
|要求/速率|伺服器要求速率|每秒計數|Average|每秒伺服器要求的速率|request/performanceBucket、request/resultCode、operation/綜合、雲端/roleInstance、要求/成功、雲端/擁有|
|exceptions/count|例外狀況|計數|計數|所有未攔截的例外狀況合併計數。|雲端/擁有的雲端/roleInstance，用戶端/類型|
|exceptions/browser|瀏覽器例外狀況|計數|計數|在瀏覽器中擲回的未攔截例外狀況計數。|用戶端/isServer、雲端/擁有|
|exceptions/server|伺服器例外狀況|計數|計數|伺服器應用程式中擲回但未攔截的例外狀況計數。|用戶端/isServer、雲端/擁有群、雲端/roleInstance|
|traces/count|追蹤|計數|計數|追蹤文件計數|追蹤/severityLevel、作業/綜合、雲端/擁有的雲端/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>IoTCentral/IoTApps

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|connectedDeviceCount|已連線的裝置總數|計數|Average|連線到 IoT Central 的裝置數目|無|
|c2d。屬性. 讀取. 成功|成功的裝置屬性從 IoT Central 讀取|計數|總計|從 IoT Central 起始的所有成功屬性讀取計數|無|
|c2d. 屬性。讀取失敗|失敗的裝置屬性從 IoT Central 讀取|計數|總計|從 IoT Central 起始的所有失敗屬性讀取計數|無|
|d2c。屬性. 讀取. 成功|裝置的成功屬性讀取|計數|總計|從裝置起始的所有成功屬性讀取計數|無|
|d2c. 屬性。讀取失敗|裝置的裝置屬性讀取失敗|計數|總計|從裝置起始的所有失敗屬性讀取計數|無|
|c2d。屬性. update. success|從 IoT Central 成功的裝置屬性更新|計數|總計|從 IoT Central 起始的所有成功屬性更新計數|無|
|c2d. 屬性. update. 失敗|IoT Central 的裝置屬性更新失敗|計數|總計|從 IoT Central 起始的所有失敗屬性更新計數|無|
|d2c。屬性. update. success|裝置的成功屬性更新|計數|總計|從裝置起始的所有成功屬性更新計數|無|
|d2c. 屬性. update. 失敗|裝置的裝置屬性更新失敗|計數|總計|從裝置起始的所有失敗屬性更新計數|無|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ServiceApiHit|服務 API 點擊次數總計|計數|計數|服務 API 點擊次數總數|ActivityType、ActivityName|
|ServiceApiLatency|整體服務 API 延遲|毫秒|Average|服務 API 要求的整體延遲|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|ServiceApiResult|服務 API 結果總計|計數|計數|服務 API 結果總數|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|SaturationShoebox|整體保存庫飽和度|百分比|Average|使用的保存庫容量|ActivityType、ActivityName、TransactionType|
|可用性|整體保存庫可用性|百分比|Average|保存庫要求可用性|ActivityType、ActivityName、StatusCode、StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CacheUtilization|快取使用率|百分比|Average|在叢集範圍的使用率等級|無|
|QueryDuration|查詢持續時間|毫秒|Average|查詢的持續時間（秒）|QueryStatus|
|IngestionUtilization|內嵌使用率|百分比|Average|在叢集中使用的擷取位置的比率|無|
|KeepAlive|保持運作|計數|Average|例行性檢查指出對查詢的叢集回應|無|
|IngestionVolumeInMB|內嵌磁片區（以 MB 為單位）|計數|總計|擷取到叢集的資料總量 (以 MB 為單位)|資料庫|
|IngestionLatencyInSeconds|內嵌延遲（以秒為單位）|秒|Average|從來源 (例如，訊息位於 EventHub 中) 擷取到叢集的擷取時間，以秒為單位|無|
|EventsProcessedForEventHubs|已處理的事件（適用于事件/IoT 中樞）|計數|總計|從事件/IoT 中樞內嵌時，由叢集處理的事件數目|EventStatus|
|IngestionResult|內嵌結果|計數|計數|擷取作業數|IngestionResultDetails|
|CPU|CPU|百分比|Average|CPU 使用率等級|無|
|ContinuousExportNumOfRecordsExported|連續匯出–匯出記錄的數目|計數|總計|針對匯出作業期間寫入的每個儲存體成品所匯出的記錄數目|ContinuousExportName，資料庫|
|ExportUtilization|匯出使用率|百分比|最大值|匯出使用率|無|
|ContinuousExportPendingCount|連續匯出暫止計數|計數|最大值|準備好執行的暫止連續匯出作業數|無|
|ContinuousExportMaxLatenessMinutes|連續匯出最大延遲|計數|最大值|叢集中的連續匯出作業所報告的延遲（以分鐘為單位）|無|
|ContinuousExportResult|連續匯出結果|計數|計數|指出連續匯出是否成功或失敗|ContinuousExportName，結果，資料庫|
|StreamingIngestDuration|串流內嵌持續時間|毫秒|Average|串流內嵌持續時間（以毫秒為單位）|無|
|StreamingIngestDataRate|串流內嵌資料速率|計數|Average|串流內嵌資料速率（每秒 MB）|無|
|SteamingIngestRequestRate|串流內嵌要求速率|計數|RateRequestsPerSecond|串流內嵌要求速率（每秒的要求數）|無|
|StreamingIngestResults|串流內嵌結果|計數|Average|串流內嵌結果|結果|
|TotalNumberOfConcurrentQueries|並行查詢的總數|計數|總計|並行查詢的總數|無|
|TotalNumberOfThrottledQueries|已節流的查詢總數|計數|總計|已節流的查詢總數|無|
|TotalNumberOfThrottledCommands|已節流的命令總數|計數|總計|已節流的命令總數|CommandType|
|TotalNumberOfExtents|範圍總數|計數|總計|資料範圍總數|無|
|InstanceCount|執行個體計數|計數|Average|總實例計數|無|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RunsStarted|執行已啟動|計數|總計|已啟動的工作流程執行數目。|無|
|RunsCompleted|完成的執行|計數|總計|已完成的工作流程執行數目。|無|
|RunsSucceeded|已成功的執行|計數|總計|已成功的工作流程執行數目。|無|
|RunsFailed|失敗的執行|計數|總計|已失敗的工作流程執行數目。|無|
|RunsCancelled|取消的執行|計數|總計|已取消的工作流程執行數目。|無|
|RunLatency|執行延遲|秒|Average|已完成的工作流程執行延遲。|無|
|RunSuccessLatency|執行成功的延遲|秒|Average|已成功的工作流程執行延遲。|無|
|RunThrottledEvents|執行節流的事件|計數|總計|工作流程動作或觸發節流的事件數目。|無|
|RunStartThrottledEvents|執行啟動節流事件|計數|總計|工作流程執行開始節流事件的數目。|無|
|RunFailurePercentage|執行失敗百分比|百分比|總計|已失敗的工作流程執行百分比。|無|
|ActionsStarted|啟動的動作 |計數|總計|已啟動的工作流程動作數目。|無|
|ActionsCompleted|完成的動作 |計數|總計|已完成的工作流程動作數目。|無|
|ActionsSucceeded|成功的動作 |計數|總計|已成功的工作流程動作數目。|無|
|ActionsFailed|動作的失敗 |計數|總計|已失敗的工作流程動作數目。|無|
|ActionsSkipped|略過的動作 |計數|總計|已略過的工作流程動作數目。|無|
|ActionLatency|動作延遲 |秒|Average|已完成的工作流程動作延遲。|無|
|ActionSuccessLatency|動作成功延遲 |秒|Average|已成功的工作流程動作延遲。|無|
|ActionThrottledEvents|動作節流的事件|計數|總計|工作流程動作已節流的事件數目。|無|
|TriggersStarted|啟動的觸發程序 |計數|總計|已啟動的工作流程觸發程序數目。|無|
|TriggersCompleted|完成的觸發程序 |計數|總計|已完成的工作流程觸發程序數目。|無|
|TriggersSucceeded|成功的觸發程序 |計數|總計|已成功的工作流程觸發程序數目。|無|
|TriggersFailed|失敗的觸發程序 |計數|總計|已失敗的工作流程觸發程序數目。|無|
|TriggersSkipped|略過的觸發程序|計數|總計|已略過的工作流程觸發程序數目。|無|
|TriggersFired|引發的觸發程序 |計數|總計|已引發的工作流程觸發程序數目。|無|
|TriggerLatency|觸發程序延遲 |秒|Average|已完成的工作流程觸發程序延遲。|無|
|TriggerFireLatency|觸發程序引發延遲 |秒|Average|已引發的工作流程觸發程序延遲。|無|
|TriggerSuccessLatency|觸發程序成功延遲 |秒|Average|已成功的工作流程觸發程序延遲。|無|
|TriggerThrottledEvents|觸發程序節流的事件|計數|總計|工作流程觸發程序節流的事件數目。|無|
|BillableActionExecutions|可計費的動作執行|計數|總計|計費的工作流程動作執行數目。|無|
|BillableTriggerExecutions|可計費的觸發程序執行|計數|總計|計費的工作流程觸發程序執行數目。|無|
|TotalBillableExecutions|可計費的總執行次數|計數|總計|計費的工作流程執行數目。|無|
|BillingUsageNativeOperation|為使用原生作業執行計費|計數|總計|要計費的原生作業執行數。|無|
|BillingUsageStandardConnector|為使用標準連接器執行計費|計數|總計|要計費的標準連接器執行數。|無|
|BillingUsageStorageConsumption|為使用儲存體使用量執行計費|計數|總計|要計費的儲存體使用量執行數。|無|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. 邏輯/integrationServiceEnvironments

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RunsStarted|執行已啟動|計數|總計|已啟動的工作流程執行數目。|無|
|RunsCompleted|完成的執行|計數|總計|已完成的工作流程執行數目。|無|
|RunsSucceeded|已成功的執行|計數|總計|已成功的工作流程執行數目。|無|
|RunsFailed|失敗的執行|計數|總計|已失敗的工作流程執行數目。|無|
|RunsCancelled|取消的執行|計數|總計|已取消的工作流程執行數目。|無|
|RunLatency|執行延遲|秒|Average|已完成的工作流程執行延遲。|無|
|RunSuccessLatency|執行成功的延遲|秒|Average|已成功的工作流程執行延遲。|無|
|RunThrottledEvents|執行節流的事件|計數|總計|工作流程動作或觸發節流的事件數目。|無|
|RunStartThrottledEvents|執行啟動節流事件|計數|總計|工作流程執行開始節流事件的數目。|無|
|RunFailurePercentage|執行失敗百分比|百分比|總計|已失敗的工作流程執行百分比。|無|
|ActionsStarted|啟動的動作 |計數|總計|已啟動的工作流程動作數目。|無|
|ActionsCompleted|完成的動作 |計數|總計|已完成的工作流程動作數目。|無|
|ActionsSucceeded|成功的動作 |計數|總計|已成功的工作流程動作數目。|無|
|ActionsFailed|動作的失敗 |計數|總計|已失敗的工作流程動作數目。|無|
|ActionsSkipped|略過的動作 |計數|總計|已略過的工作流程動作數目。|無|
|ActionLatency|動作延遲 |秒|Average|已完成的工作流程動作延遲。|無|
|ActionSuccessLatency|動作成功延遲 |秒|Average|已成功的工作流程動作延遲。|無|
|ActionThrottledEvents|動作節流的事件|計數|總計|工作流程動作已節流的事件數目。|無|
|TriggersStarted|啟動的觸發程序 |計數|總計|已啟動的工作流程觸發程序數目。|無|
|TriggersCompleted|完成的觸發程序 |計數|總計|已完成的工作流程觸發程序數目。|無|
|TriggersSucceeded|成功的觸發程序 |計數|總計|已成功的工作流程觸發程序數目。|無|
|TriggersFailed|失敗的觸發程序 |計數|總計|已失敗的工作流程觸發程序數目。|無|
|TriggersSkipped|略過的觸發程序|計數|總計|已略過的工作流程觸發程序數目。|無|
|TriggersFired|引發的觸發程序 |計數|總計|已引發的工作流程觸發程序數目。|無|
|TriggerLatency|觸發程序延遲 |秒|Average|已完成的工作流程觸發程序延遲。|無|
|TriggerFireLatency|觸發程序引發延遲 |秒|Average|已引發的工作流程觸發程序延遲。|無|
|TriggerSuccessLatency|觸發程序成功延遲 |秒|Average|已成功的工作流程觸發程序延遲。|無|
|TriggerThrottledEvents|觸發程序節流的事件|計數|總計|工作流程觸發程序節流的事件數目。|無|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|整合服務環境的工作流程處理器使用量|百分比|Average|整合服務環境的工作流程處理器使用量。|無|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|整合服務環境的工作流程記憶體使用量|百分比|Average|整合服務環境的工作流程記憶體使用量。|無|
|IntegrationServiceEnvironmentConnectorProcessorUsage|整合服務環境的連接器處理器使用量|百分比|Average|整合服務環境的連接器處理器使用量。|無|
|IntegrationServiceEnvironmentConnectorMemoryUsage|整合服務環境的連接器記憶體使用量|百分比|Average|整合服務環境的連接器記憶體使用量。|無|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|取消的執行|取消的執行|計數|總計|此工作區的執行數已取消|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|取消要求的執行|取消要求的執行|計數|總計|針對此工作區要求取消的執行次數|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|已完成的執行|已完成的執行|計數|總計|已成功完成此工作區的執行數目|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|失敗的執行|失敗的執行|計數|總計|此工作區的執行失敗次數|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|正在完成執行|正在完成執行|計數|總計|此工作區的進入完成狀態的執行次數|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|未回應執行|未回應執行|計數|總計|此工作區的執行沒有回應的次數|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|未開始執行|未開始執行|計數|總計|此工作區的執行數目未啟動狀態|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|準備執行|準備執行|計數|總計|為此工作區準備的執行數目|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|布建執行|布建執行|計數|總計|為此工作區布建的執行數目|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|佇列的執行|佇列的執行|計數|總計|為此工作區排入佇列的執行數|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|開始執行|開始執行|計數|總計|此工作區已開始執行的數目|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|開始執行|開始執行|計數|總計|此工作區已開始執行的數目|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|Errors|Errors|計數|總計|此工作區中的執行錯誤數目|案例|
|警告|警告|計數|總計|此工作區中的執行警告數目|案例|
|模型註冊成功|模型註冊成功|計數|總計|在此工作區中成功的模型註冊數目|案例|
|模型註冊失敗|模型註冊失敗|計數|總計|在此工作區中失敗的模型註冊數目|案例，StatusCode|
|模型部署已開始|模型部署已開始|計數|總計|在此工作區中啟動的模型部署數目|案例|
|模型部署成功|模型部署成功|計數|總計|在此工作區中成功的模型部署數|案例|
|模型部署失敗|模型部署失敗|計數|總計|在此工作區中失敗的模型部署數|案例，StatusCode|
|節點總計|節點總計|計數|Average|總節點數。 此總計包括一些作用中節點、閒置節點、無法使用的節點、Premepted 節點、離開節點|案例，ClusterName|
|現用節點|現用節點|計數|Average|Acitve 節點的數目。 這些是目前正在執行作業的節點。|案例，ClusterName|
|閒置節點|閒置節點|計數|Average|閒置節點的數目。 閒置節點是指未執行任何作業的節點，但可以接受新作業（如果有的話）。|案例，ClusterName|
|無法使用的節點|無法使用的節點|計數|Average|無法使用的節點數目。 無法使用的節點無法運作，因為有一些無法解析的問題。 Azure 將會回收這些節點。|案例，ClusterName|
|搶先節點|搶先節點|計數|Average|已搶先節點的數目。 這些節點是從可用節點集區中取出的低優先順序節點。|案例，ClusterName|
|離開節點|離開節點|計數|Average|離開節點的數目。 離開節點是剛完成作業並將進入閒置狀態的節點。|案例，ClusterName|
|核心總數|核心總數|計數|Average|核心總數|案例，ClusterName|
|現用核心|現用核心|計數|Average|使用中核心數目|案例，ClusterName|
|閒置核心|閒置核心|計數|Average|閒置核心數|案例，ClusterName|
|無法使用的核心|無法使用的核心|計數|Average|無法使用的核心數目|案例，ClusterName|
|搶先核心|搶先核心|計數|Average|搶先核心數|案例，ClusterName|
|離開核心|離開核心|計數|Average|離開核心數|案例，ClusterName|
|配額使用量百分比|配額使用量百分比|計數|Average|使用的配額百分比|案例，ClusterName，VmFamilyName，VmPriority|
|CpuUtilization|CpuUtilization|計數|Average|CPU (預覽)|案例，runId，，CreatedTime|
|GpuUtilization|GpuUtilization|計數|Average|GPU （預覽）|案例，runId，，CreatedTime，DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|使用方式|使用方式|計數|計數|API 呼叫的計數|ApiCategory、ApiName、ResultType、ResponseCode|
|可用性|可用性|百分比|Average|Api 的可用性|ApiCategory、ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft Media/windowsazure.mediaservices.extensions/Streamingendpoint

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|輸出|輸出|位元組|總計|輸出資料量（以位元組為單位）。|OutputFormat|
|SuccessE2ELatency|成功的端對端延遲|毫秒|Average|成功要求的平均延遲（以毫秒為單位）。|OutputFormat|
|Requests|Requests|計數|總計|對串流端點的要求。|OutputFormat、HttpStatusCode、ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft Media/windowsazure.mediaservices.extensions

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AssetQuota|資產配額|計數|Average|目前的媒體服務帳戶允許多少個資產|無|
|AssetCount|資產計數|計數|Average|目前的媒體服務帳戶中已建立多少個資產|無|
|AssetQuotaUsedPercentage|已使用的資產配額百分比|百分比|Average|目前媒體服務帳戶中的資產使用百分比|無|
|ContentKeyPolicyQuota|內容金鑰原則配額|計數|Average|目前的媒體服務帳戶允許多少內容金鑰原則|無|
|ContentKeyPolicyCount|內容金鑰原則計數|計數|Average|目前媒體服務帳戶中已建立的內容金鑰原則數目|無|
|ContentKeyPolicyQuotaUsedPercentage|已使用的內容金鑰原則配額百分比|百分比|Average|目前媒體服務帳戶中的內容金鑰原則使用百分比|無|
|StreamingPolicyQuota|串流原則配額|計數|Average|目前媒體服務帳戶允許的串流原則數目|無|
|StreamingPolicyCount|串流原則計數|計數|Average|目前媒體服務帳戶中已建立的串流原則數目|無|
|StreamingPolicyQuotaUsedPercentage|已使用的串流原則配額百分比|百分比|Average|目前媒體服務帳戶中的串流原則使用百分比|無|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>MixedReality/remoteRenderingAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AssetsConverted|已轉換的資產|計數|總計|已轉換的資產總數|AppId、ResourceId、SDKVersion|
|ActiveRenderingSessions|現用轉譯會話|計數|總計|作用中的轉譯會話總數|AppId、ResourceId、SessionType、SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft NetApp/netAppAccounts/capacityPools/磁片區

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AverageReadLatency|讀取延遲的平均值|毫秒|Average|每個作業讀取延遲的平均值 (毫秒)|無|
|AverageWriteLatency|寫入延遲的平均值|毫秒|Average|每個作業寫入延遲的平均值 (毫秒)|無|
|VolumeLogicalSize|磁片區已耗用大小|位元組|Average|磁碟區的邏輯大小 (已使用的位元組)|無|
|VolumeSnapshotSize|磁碟區快照集大小|位元組|Average|磁碟區所有快照集的大小|無|
|ReadIops|讀取 IOPS|每秒計數|Average|每秒的讀取 I/O 作業|無|
|WriteIops|寫入 IOPS|每秒計數|Average|每秒的寫入 I/O 作業|無|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|配置給磁片區大小的集區|位元組|Average|集區的已使用配置大小|無|
|VolumePoolTotalLogicalSize|已耗用的集區大小|位元組|Average|屬於集區之所有磁碟區的邏輯大小的總和|無|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BytesSentRate|傳送的位元組數|位元組|總計|網路介面傳送的位元組數|無|
|BytesReceivedRate|接收的位元組數|位元組|總計|網路介面接收的位元組數|無|
|PacketsSentRate|傳送的封包|計數|總計|網路介面傳送的封包數|無|
|PacketsReceivedRate|接收的封包|計數|總計|網路介面接收的封包數|無|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|VipAvailability|資料路徑可用性|計數|Average|每個持續時間 Load Balancer 資料路徑可用性的平均值|FrontendIPAddress、FrontendPort|
|DipAvailability|健全狀況探查狀態|計數|Average|每個持續時間 Load Balancer 健全狀況探查狀態的平均值|ProtocolType、BackendPort、FrontendIPAddress、FrontendPort、BackendIPAddress|
|ByteCount|位元組計數|計數|總計|在期間內傳輸的位元組總數|FrontendIPAddress、FrontendPort、Direction|
|PacketCount|封包計數|計數|總計|在期間內傳輸的封包總數|FrontendIPAddress、FrontendPort、Direction|
|SYNCount|SYN 計數|計數|總計|在期間內傳輸的 SYN 封包總數|FrontendIPAddress、FrontendPort、Direction|
|SnatConnectionCount|SNAT 連線計數|計數|總計|在期間內建立的新 SNAT 連線總數|FrontendIPAddress、BackendIPAddress、ConnectionState|
|AllocatedSnatPorts|配置的 SNAT 連接埠 (預覽)|計數|總計|在期間內配置的 SNAT 連接埠總數|FrontendIPAddress、BackendIPAddress、ProtocolType、IsAwaitingRemoval|
|UsedSnatPorts|使用的 SNAT 連接埠 (預覽)|計數|總計|在期間內使用的 SNAT 連接埠總數|FrontendIPAddress、BackendIPAddress、ProtocolType、IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueryVolume|查詢磁碟區|計數|總計|為 DNS 區域儲存的查詢數目|無|
|RecordSetCount|記錄集計數|計數|最大值|DNS 區域中的記錄集數目|無|
|RecordSetCapacityUtilization|記錄集容量使用率|百分比|最大值|DNS 區域所使用的記錄集容量百分比|無|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PacketsInDDoS|傳入封包 DDoS|每秒計數|最大值|傳入封包 DDoS|無|
|PacketsDroppedDDoS|傳入封包捨棄 DDoS|每秒計數|最大值|傳入封包捨棄 DDoS|無|
|PacketsForwardedDDoS|傳入封包轉寄 DDoS|每秒計數|最大值|傳入封包轉寄 DDoS|無|
|TCPPacketsInDDoS|傳入 TCP 封包 DDoS|每秒計數|最大值|傳入 TCP 封包 DDoS|無|
|TCPPacketsDroppedDDoS|傳入 TCP 封包捨棄 DDoS|每秒計數|最大值|傳入 TCP 封包捨棄 DDoS|無|
|TCPPacketsForwardedDDoS|傳入 TCP 封包轉送 DDoS|每秒計數|最大值|傳入 TCP 封包轉送 DDoS|無|
|UDPPacketsInDDoS|傳入 UDP 封包 DDoS|每秒計數|最大值|傳入 UDP 封包 DDoS|無|
|UDPPacketsDroppedDDoS|傳入 UDP 封包捨棄 DDoS|每秒計數|最大值|傳入 UDP 封包捨棄 DDoS|無|
|UDPPacketsForwardedDDoS|傳入 UDP 封包轉送 DDoS|每秒計數|最大值|傳入 UDP 封包轉送 DDoS|無|
|BytesInDDoS|傳入位元組數 DDoS|每秒位元組|最大值|傳入位元組數 DDoS|無|
|BytesDroppedDDoS|傳入位元組數捨棄 DDoS|每秒位元組|最大值|傳入位元組數捨棄 DDoS|無|
|BytesForwardedDDoS|傳入位元組數轉送 DDoS|每秒位元組|最大值|傳入位元組數轉送 DDoS|無|
|TCPBytesInDDoS|傳入 TCP 位元組數 DDoS|每秒位元組|最大值|傳入 TCP 位元組數 DDoS|無|
|TCPBytesDroppedDDoS|傳入 TCP 位元組數捨棄 DDoS|每秒位元組|最大值|傳入 TCP 位元組數捨棄 DDoS|無|
|TCPBytesForwardedDDoS|傳入 TCP 位元組數轉送 DDoS|每秒位元組|最大值|傳入 TCP 位元組數轉送 DDoS|無|
|UDPBytesInDDoS|傳入 UDP 位元組數 DDoS|每秒位元組|最大值|傳入 UDP 位元組數 DDoS|無|
|UDPBytesDroppedDDoS|傳入 UDP 位元組數捨棄 DDoS|每秒位元組|最大值|傳入 UDP 位元組數捨棄 DDoS|無|
|UDPBytesForwardedDDoS|傳入 UDP 位元組數轉送 DDoS|每秒位元組|最大值|傳入 UDP 位元組數轉送 DDoS|無|
|IfUnderDDoSAttack|是否正遭受 DDoS 攻擊|計數|最大值|是否正遭受 DDoS 攻擊|無|
|DDoSTriggerTCPPackets|傳入 TCP 封包數以觸發 DDoS 緩和|每秒計數|最大值|傳入 TCP 封包數以觸發 DDoS 緩和|無|
|DDoSTriggerUDPPackets|傳入 UDP 封包數以觸發 DDoS 緩和|每秒計數|最大值|傳入 UDP 封包數以觸發 DDoS 緩和|無|
|DDoSTriggerSYNPackets|用以觸發 DDoS 風險降低措施的輸入 SYN 封包數|每秒計數|最大值|用以觸發 DDoS 風險降低措施的輸入 SYN 封包數|無|
|VipAvailability|資料路徑可用性|計數|Average|每個持續時間 IP 位址可用性的平均值|連接埠|
|ByteCount|位元組計數|計數|總計|在期間內傳輸的位元組總數|埠，方向|
|PacketCount|封包計數|計數|總計|在期間內傳輸的封包總數|埠，方向|
|SynCount|SYN 計數|計數|總計|在期間內傳輸的 SYN 封包總數|埠，方向|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Ping 至 VM 的來回時間|毫秒|Average|傳送至目的地 VM 之 Ping 的來回時間|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Ping 至 VM 失敗|百分比|Average|對目的地 VM 的已傳送 Ping 總數的失敗次數百分比|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ApplicationRuleHit|應用程式規則計數|計數|總計|遇到應用程式規則的次數|狀態、原因、通訊協定|
|NetworkRuleHit|網路規則計數|計數|總計|點擊網路規則的次數|狀態、原因、通訊協定|
|FirewallHealth|防火牆健全狀況狀態|百分比|Average|防火牆健全狀況狀態|狀態、原因|
|DataProcessed|已處理的資料量|位元組|總計|防火牆處理的總數據量|無|
|SNATPortUtilization|SNAT 埠使用率|百分比|Average|SNAT 埠使用率|無|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Throughput|Throughput|每秒位元組|Average|應用程式閘道每秒已服務的位元組數目|無|
|UnhealthyHostCount|狀況不良的主機計數|計數|Average|狀況不良的後端主機數目|BackendSettingsPool|
|HealthyHostCount|狀況良好的主機計數|計數|Average|狀況良好的後端主機數目|BackendSettingsPool|
|TotalRequests|要求總數|計數|總計|應用程式閘道已服務的成功要求計數|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|每部狀況良好主機的每分鐘要求數|計數|Average|集區中每個狀況良好後端主機每分鐘的平均要求計數|BackendSettingsPool|
|FailedRequests|失敗的要求|計數|總計|應用程式閘道已服務的失敗要求計數|BackendSettingsPool|
|ResponseStatus|回應狀態|計數|總計|應用程式閘道傳回的 Http 回應狀態|HttpStatusGroup|
|CurrentConnections|目前的連線數|計數|總計|目前與應用程式閘道建立的連線計數|無|
|NewConnectionsPerSecond|每秒的新連線數|每秒計數|Average|以應用程式閘道建立的每秒新連線數|無|
|CpuUtilization|CPU 使用率|百分比|Average|應用程式閘道目前的 CPU 使用率|無|
|CapacityUnits|目前的容量單位|計數|Average|耗用的容量單位|無|
|FixedBillableCapacityUnits|固定計費容量單位|計數|Average|將收取的最小容量單位|無|
|EstimatedBilledCapacityUnits|預估的計費容量單位|計數|Average|預估的容量單位將收取費用|無|
|ComputeUnits|目前的計算單位|計數|Average|耗用的計算單位|無|
|BackendResponseStatus|後端回應狀態|計數|總計|後端成員所產生的 HTTP 回應碼數目。 這不包含任何由應用程式閘道產生的回應碼。|BackendServer、BackendPool、BackendHttpSetting、HttpStatusGroup|
|TlsProtocol|用戶端 TLS 通訊協定|計數|總計|與應用程式閘道建立連線的用戶端所起始的 TLS 和非 TLS 要求數目。 若要查看 TLS 通訊協定散發，請依維度 TLS 通訊協定進行篩選。|接聽程式，TlsProtocol|
|BytesSent|傳送的位元組數|位元組|總計|應用程式閘道傳送給用戶端的位元組總數|接聽程式|
|BytesReceived|接收的位元組數|位元組|總計|應用程式閘道從用戶端接收的位元組總數|接聽程式|
|ClientRtt|用戶端 RTT|毫秒|Average|用戶端與應用程式閘道之間的平均來回行程時間。 此計量指出建立連線和傳回通知所需的時間|接聽程式|
|ApplicationGatewayTotalTime|應用程式閘道總時間|毫秒|Average|處理要求所花費的平均時間，以及要傳送的回應。 這是以從應用程式閘道接收 HTTP 要求的第一個位元組到回應傳送作業完成時的時間間隔平均值來計算。 請務必注意，這通常包括應用程式閘道處理時間、要求和回應封包在網路上的傳輸時間，以及後端伺服器回應的時間。|接聽程式|
|BackendConnectTime|後端連線時間|毫秒|Average|建立與後端伺服器連接所花費的時間|接聽程式，BackendServer，BackendPool，BackendHttpSetting|
|BackendFirstByteResponseTime|後端第一個位元組回應時間|毫秒|Average|開始建立與後端伺服器之間的連線，以及接收回應標頭的第一個位元組、將逼近後端伺服器的處理時間，兩者之間的時間間隔|接聽程式，BackendServer，BackendPool，BackendHttpSetting|
|BackendLastByteResponseTime|後端上次位元組回應時間|毫秒|Average|開始建立與後端伺服器之間的連線，以及接收回應主體最後一個位元組之間的時間間隔|接聽程式，BackendServer，BackendPool，BackendHttpSetting|
|MatchedCount|Web 應用程式防火牆 v1 規則散發總數|計數|總計|連入流量的 Web 應用程式防火牆 v1 總規則分佈|RuleGroup、RuleId|
|BlockedCount|Web 應用程式防火牆 v1 已封鎖要求規則散發|計數|總計|Web 應用程式防火牆 v1 已封鎖要求規則散發|RuleGroup、RuleId|
|BlockedReqCount|Web 應用程式防火牆 v1 已封鎖的要求計數|計數|總計|Web 應用程式防火牆 v1 已封鎖的要求計數|無|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AverageBandwidth|閘道 S2S 頻寬|每秒位元組|Average|每秒閘道中站對站的平均頻寬 (位元組)|無|
|P2SBandwidth|閘道 S2S 頻寬|每秒位元組|Average|每秒閘道中點對站的平均頻寬 (位元組)|無|
|P2SConnectionCount|P2S 連線計數|計數|最大值|閘道的點對站連線計數|通訊協定|
|TunnelAverageBandwidth|通道頻寬|每秒位元組|Average|通道的平均頻寬 (位元組/秒)|ConnectionName、RemoteIP|
|TunnelEgressBytes|通道輸出位元組數|位元組|總計|通道的傳出位元組數|ConnectionName、RemoteIP|
|TunnelIngressBytes|通道輸入位元組數|位元組|總計|通道的傳入位元組數|ConnectionName、RemoteIP|
|TunnelEgressPackets|通道輸出封包數|計數|總計|通道的傳出封包計數|ConnectionName、RemoteIP|
|TunnelIngressPackets|通道輸入封包數|計數|總計|通道的傳入封包計數|ConnectionName、RemoteIP|
|TunnelEgressPacketDropTSMismatch|通道輸出 TS 不相符封包捨棄|計數|總計|因與通道不符而從流量選取器捨棄的傳出封包計數|ConnectionName、RemoteIP|
|TunnelIngressPacketDropTSMismatch|通道輸入 TS 不符合封包捨棄|計數|總計|因與通道不符而從流量選取器捨棄的傳入封包計數|ConnectionName、RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft 網路/expressRoutePorts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|計數|Average|Rx 光線層級（dBm）|連結，Lane|
|TxLightLevel|TxLightLevel|計數|Average|以 dBm 為 Tx 的淺層級|連結，Lane|
|AdminState|AdminState|計數|Average|埠的系統管理狀態|連結|
|LineProtocol|LineProtocol|計數|Average|埠的線路通訊協定狀態|連結|
|PortBitsInPerSecond|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|連結|
|PortBitsOutPerSecond|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|連結|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|PeeredCircuitSKey|
|BgpAvailability|Bgp 可用性|百分比|Average|從 MSEE 到所有對等的 BGP 可用性。|PeeringType、對等|
|ArpAvailability|Arp 可用性|百分比|Average|從 MSEE 到所有對等的 ARP 可用性。|PeeringType、對等|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|每秒計數|Average|每秒捨棄的輸入資料位|無|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|每秒計數|Average|每秒捨棄的輸出資料位|無|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|無|
|BitsOutPerSecond|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|無|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|無|
|BitsOutPerSecond|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|無|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft 網路/expressRouteGateways

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QpsByEndpoint|傳回的依端點查詢數|計數|總計|在指定時間範圍內傳回 Traffic Manager 端點的次數|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|依端點的端點狀態|計數|最大值|若端點探查狀態為「已啟用」則為 1，否則為 0。|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ProbesFailedPercent|失敗的探查百分比|百分比|Average|失敗的連線能力監視探查百分比|無|
|AverageRoundtripMs|平均來回時間（毫秒）|毫秒|Average|在來源與目的地之間所傳送連線能力監視探查的平均網路來回時間 (毫秒)|無|
|ChecksFailedPercent|檢查失敗百分比（預覽）|百分比|Average|% 的連線能力監視檢查失敗|SourceAddress、SourceResourceId、SourceType、Protocol、DestinationAddress、DestinationName、DestinationResourceId、DestinationType、DestinationPort、TestGroupName、TestConfigurationName|
|RoundTripTimeMs|來回時間（毫秒）（預覽）|毫秒|Average|連線能力監視檢查的來回行程時間（以毫秒為單位）|SourceAddress、SourceResourceId、SourceType、Protocol、DestinationAddress、DestinationName、DestinationResourceId、DestinationType、DestinationPort、TestGroupName、TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RequestCount|要求計數|計數|總計|HTTP/S Proxy 服務的用戶端要求數|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|RequestSize|要求大小|位元組|總計|從用戶端傳送到 HTTP/S Proxy 的要求的位元組數|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|ResponseSize|回應大小|位元組|總計|從 HTTP/S Proxy 傳送到用戶端的回應的位元組數|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|BillableResponseSize|可計費回應大小|位元組|總計|當做回應從 HTTP/S proxy 傳送至用戶端的可計費位元組數（每個要求最少的2KB）。|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|BackendRequestCount|後端要求計數|計數|總計|從 HTTP/S Proxy 傳送至後端的要求數|HttpStatus、HttpStatusGroup、後端|
|BackendRequestLatency|後端要求延遲|毫秒|Average|從 HTTP/S Proxy 傳送要求到後端一直到 HTTP/S Proxy 從後端接收最後回應位元組之間算出的時間|後端|
|TotalLatency|延遲總計|毫秒|Average|從 HTTP/S Proxy 接收用戶端要求一直到用戶端認可來自 HTTP/S Proxy 的最後回應位元組之間算出的時間|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|BackendHealthPercentage|後端健康情況百分比|百分比|Average|從 HTTP/S Proxy 到後端的健全狀況探查成功百分比|後端，BackendPool|
|WebApplicationFirewallRequestCount|Web 應用程式防火牆要求計數|計數|總計|Web 應用程式防火牆處理的用戶端要求數|PolicyName、RuleName、Action|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft 網路/privateDnsZones

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueryVolume|查詢磁碟區|計數|總計|為私人 DNS 區域提供服務的查詢數目|無|
|RecordSetCount|記錄集計數|計數|最大值|私人 DNS 區域中的記錄集數目|無|
|RecordSetCapacityUtilization|記錄集容量使用率|百分比|最大值|私人 DNS 區域所使用的記錄集容量百分比|無|
|VirtualNetworkLinkCount|虛擬網路連結計數|計數|最大值|連結至私人 DNS 區域的虛擬網路數目|無|
|VirtualNetworkLinkCapacityUtilization|虛擬網路連結容量使用率|百分比|最大值|私人 DNS 區域使用的虛擬網路連結容量百分比|無|
|VirtualNetworkWithRegistrationLinkCount|虛擬網路註冊連結計數|計數|最大值|已啟用自動註冊且連結至私人 DNS 區域的虛擬網路數目|無|
|VirtualNetworkWithRegistrationCapacityUtilization|虛擬網路註冊連結容量使用率|百分比|最大值|私人 DNS 區域使用的自動註冊容量虛擬網路連結的百分比|無|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|registration.all|註冊作業數|計數|總計|所有成功註冊作業 (建立、更新、查詢和刪除) 的計數。 |無|
|registration.create|註冊建立作業|計數|總計|所有成功註冊建立的計數。|無|
|registration.update|註冊更新作業|計數|總計|所有成功註冊更新的計數。|無|
|registration.get|註冊讀取作業|計數|總計|所有成功註冊查詢的計數。|無|
|registration.delete|註冊刪除作業|計數|總計|所有成功註冊刪除的計數。|無|
|incoming|傳入訊息|計數|總計|所有成功傳送 API 呼叫的計數。 |無|
|incoming.scheduled|傳送已排程的推播通知|計數|總計|取消已排程的推播通知|無|
|incoming.scheduled.cancel|取消已排程的推播通知|計數|總計|取消已排程的推播通知|無|
|scheduled.pending|暫止已排程的通知|計數|總計|暫止已排程的通知|無|
|installation.all|安裝管理作業|計數|總計|安裝管理作業|無|
|installation.get|取得安裝作業|計數|總計|取得安裝作業|無|
|installation.upsert|建立或更新安裝作業|計數|總計|建立或更新安裝作業|無|
|installation.patch|修補安裝作業|計數|總計|修補安裝作業|無|
|installation.delete|刪除安裝作業|計數|總計|刪除安裝作業|無|
|outgoing.allpns.success|成功通知|計數|總計|所有成功通知的計數。|無|
|outgoing.allpns.invalidpayload|承載錯誤|計數|總計|因為 PNS 傳回錯誤承載錯誤而失敗的推播計數。|無|
|outgoing.allpns.pnserror|外部通知系統錯誤|計數|總計|因為與 PNS 通訊發生問題 (不包括驗證問題) 而失敗的推播計數。|無|
|outgoing.allpns.channelerror|通道錯誤|計數|總計|因為通道無效、未與正確的應用程式相關聯、已節流處理或過期，導致失敗的推播計數。|無|
|outgoing.allpns.badorexpiredchannel|錯誤或過期的通道錯誤|計數|總計|因為註冊中的通道/權杖/registrationId 過期或無效，導致失敗的推播計數。|無|
|outgoing.wns.success|WNS 成功通知|計數|總計|所有成功通知的計數。|無|
|outgoing.wns.invalidcredentials|WNS 授權錯誤 (無效認證)|計數|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。 (Windows Live 無法識別認證)。|無|
|outgoing.wns.badchannel|WNS 不正確的通道錯誤|計數|總計|因為無法識別註冊中的 ChannelURI (WNS 狀態︰404 找不到)，導致失敗的推播計數。|無|
|outgoing.wns.expiredchannel|WNS 過期通道錯誤|計數|總計|因為 ChannelURIi 過期 (WNS 狀態︰ 410 不存在) 而失敗的推播計數。|無|
|outgoing.wns.throttled|WNS 節流通知|計數|總計|因為 WNS 正在節流此應用程式，導致失敗的推播計數 (WNS 狀態︰406 無法接受)。|無|
|outgoing.wns.tokenproviderunreachable|WNS 授權錯誤 (無法連線)|計數|總計|無法連線到 Windows Live。|無|
|outgoing.wns.invalidtoken|WNS 授權錯誤 (無效權杖)|計數|總計|提供給 WNS 的權杖無效 (WNS 狀態︰ 401 未經授權)。|無|
|outgoing.wns.wrongtoken|WNS 授權錯誤 (錯誤權杖)|計數|總計|提供給 WNS 的權杖有效，但卻是給另一個應用程式 (WNS 狀態︰403 禁止)。 如果註冊中的 ChannelURI 與另一個應用程式相關聯，可能會發生這種情形。 請檢查用戶端應用程式相關聯的應用程式，就是認證位於通知中樞的同一個應用程式。|無|
|outgoing.wns.invalidnotificationformat|WNS 無效通知格式|計數|總計|通知的格式無效 (WNS 狀態︰400)。 請注意，WNS 不會拒絕所有無效承載。|無|
|outgoing.wns.invalidnotificationsize|WNS 無效通知大小錯誤|計數|總計|通知承載太大 (WNS 狀態︰413)。|無|
|outgoing.wns.channelthrottled|WNS 通道已節流處理|計數|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (WNS 回應標頭︰X-WNS-NotificationStatus:channelThrottled)。|無|
|outgoing.wns.channeldisconnected|WNS 通道已中斷連線|計數|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (WNS 回應標頭︰X-WNS-DeviceConnectionStatus: disconnected)。|無|
|outgoing.wns.dropped|WNS 已捨棄通知|計數|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (X-WNS-NotificationStatus: dropped 但不是 X-WNS-DeviceConnectionStatus: disconnected)。|無|
|outgoing.wns.pnserror|WNS 錯誤|計數|總計|因為與 WNS 通訊錯誤而未傳遞通知。|無|
|outgoing.wns.authenticationerror|WNS 驗證錯誤|計數|總計|因為與 Windows Live 通訊錯誤、無效認證或不正確的權杖，導致未傳遞通知。|無|
|outgoing.apns.success|APNS 成功通知|計數|總計|所有成功通知的計數。|無|
|outgoing.apns.invalidcredentials|APNS 授權錯誤|計數|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無|
|outgoing.apns.badchannel|APNS 不正確的通道錯誤|計數|總計|因為權杖無效而失敗的推播計數 (APNS 狀態碼︰8)。|無|
|outgoing.apns.expiredchannel|APNS 過期通道錯誤|計數|總計|由 APNS 意見反應通道宣告失效的權杖計數。|無|
|outgoing.apns.invalidnotificationsize|APNS 無效通知大小錯誤|計數|總計|因為承載太大而失敗的推播計數 (APNS 狀態碼︰7)。|無|
|outgoing.apns.pnserror|APNS 錯誤|計數|總計|因為與 APNS 通訊錯誤而失敗的推播計數。|無|
|outgoing.gcm.success|GCM 成功通知|計數|總計|所有成功通知的計數。|無|
|outgoing.gcm.invalidcredentials|GCM 授權錯誤 (無效認證)|計數|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無|
|outgoing.gcm.badchannel|GCM 不正確的通道錯誤|計數|總計|因為無法識別註冊中的 registrationId 而失敗的推播計數 (GCM 結果：無效註冊)。|無|
|outgoing.gcm.expiredchannel|GCM 過期通道錯誤|計數|總計|因為註冊中的 registrationId 過期而失敗的推播計數 (GCM 結果：NotRegistered)。|無|
|outgoing.gcm.throttled|GCM 已將通知節流處理|計數|總計|因為 GCM 已將此應用程式節流處理而失敗的推播計數 (GCM 狀態碼︰501-599 或結果：Unavailable)。|無|
|outgoing.gcm.invalidnotificationformat|GCM 無效通知格式|計數|總計|因為承載未正確格式化而失敗的推播計數 (GCM 結果︰InvalidDataKey 或 InvalidTtl)。|無|
|outgoing.gcm.invalidnotificationsize|GCM 無效通知大小錯誤|計數|總計|因為承載太大而失敗的推播計數 (GCM 結果︰MessageTooBig)。|無|
|outgoing.gcm.wrongchannel|GCM 不正確的通道錯誤|計數|總計|因為註冊中的 registrationId 未與目前的應用程式相關聯，導致失敗的推播計數 (GCM 結果：InvalidPackageName)。|無|
|outgoing.gcm.pnserror|GCM 錯誤|計數|總計|因為與 GCM 通訊錯誤而失敗的推播計數。|無|
|outgoing.gcm.authenticationerror|GCM 驗證錯誤|計數|總計|因為 PNS 不接受提供的認證、已封鎖認證，或應用程式中未正確設定 SenderId，導致失敗的推播計數 (GCM 結果︰MismatchedSenderId)。|無|
|outgoing.mpns.success|MPNS 成功通知|計數|總計|所有成功通知的計數。|無|
|outgoing.mpns.invalidcredentials|MPNS 無效認證|計數|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無|
|outgoing.mpns.badchannel|MPNS 不正確的通道錯誤|計數|總計|因為無法識別註冊中的 ChannelURI 而失敗的推播計數 (MPNS 狀態︰404 找不到)。|無|
|outgoing.mpns.throttled|MPNS 已將通知節流處理|計數|總計|因為 MPNS 正在節流此應用程式，導致失敗的推播計數 (MPNS 狀態︰406 無法接受)。|無|
|outgoing.mpns.invalidnotificationformat|MPNS 無效通知格式|計數|總計|因為通知的承載太大而失敗的推播計數。|無|
|outgoing.mpns.channeldisconnected|MPNS 通道已中斷連線|計數|總計|因為註冊中的 ChannelURI 中斷連線而失敗的推播計數 (MPNS 狀態︰412 找不到)。|無|
|outgoing.mpns.dropped|MPNS 已捨棄通知|計數|總計|MPNS 已捨棄的推播計數 (MPNS 回應標頭︰X-NotificationStatus: QueueFull 或 Suppressed)。|無|
|outgoing.mpns.pnserror|MPNS 錯誤|計數|總計|因為與 MPNS 通訊錯誤而失敗的推播計數。|無|
|outgoing.mpns.authenticationerror|MPNS 驗證錯誤|計數|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無|
|notificationhub.pushes|所有外寄通知|計數|總計|通知中樞的所有外寄通知|無|
|incoming.all.requests|所有傳入要求|計數|總計|針對通知中樞傳入的要求總數|無|
|incoming.all.failedrequests|所有傳入的失敗要求|計數|總計|針對通知中樞傳入的失敗要求總數|無|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|計數|Average|Average_% Free Inodes|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Free Space|% Free Space|計數|Average|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Used Inodes|% Used Inodes|計數|Average|Average_% Used Inodes|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Used Space|% Used Space|計數|Average|Average_% Used Space|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec |計數|Average|Average_Disk Read Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Disk Reads/sec|Disk Reads/sec |計數|Average|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|計數|Average|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|計數|Average|Average_Disk Write Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Disk Writes/sec|Disk Writes/sec|計數|Average|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Free Megabytes|Free Megabytes|計數|Average|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|計數|Average|Average_Logical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Available Memory|% Available Memory|計數|Average|Average_% Available Memory|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Available Swap Space|% Available Swap Space|計數|Average|Average_% Available Swap Space|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Used Memory|% Used Memory|計數|Average|Average_% Used Memory|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Used Swap Space|% Used Swap Space|計數|Average|Average_% Used Swap Space|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Available MBytes Memory|Available MBytes Memory|計數|Average|Average_Available MBytes Memory|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Available MBytes Swap|Available MBytes Swap|計數|Average|Average_Available MBytes Swap|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Page Reads/sec|Page Reads/sec|計數|Average|Average_Page Reads/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Page Writes/sec|Page Writes/sec|計數|Average|Average_Page Writes/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Pages/sec|Pages/sec|計數|Average|Average_Pages/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|計數|Average|Average_Used MBytes Swap Space|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Used Memory MBytes|Used Memory MBytes|計數|Average|Average_Used Memory MBytes|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|計數|Average|Average_Total Bytes Transmitted|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Total Bytes Received|Total Bytes Received|計數|Average|Average_Total Bytes Received|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Total Bytes|Total Bytes|計數|Average|Average_Total Bytes|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|計數|Average|Average_Total Packets Transmitted|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Total Packets Received|Total Packets Received|計數|Average|Average_Total Packets Received|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Total Rx Errors|Total Rx Errors|計數|Average|Average_Total Rx Errors|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Total Tx Errors|Total Tx Errors|計數|Average|Average_Total Tx Errors|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Total Collisions|Total Collisions|計數|Average|Average_Total Collisions|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|計數|Average|Average_Avg. Disk sec/Read|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|計數|Average|Average_Avg. Disk sec/Transfer|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|計數|Average|Average_Avg. Disk sec/Write|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|計數|Average|Average_Physical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Pct Privileged Time|Pct Privileged Time|計數|Average|Average_Pct Privileged Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Pct User Time|Pct User Time|計數|Average|Average_Pct User Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Used Memory kBytes|Used Memory kBytes|計數|Average|Average_Used Memory kBytes|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|計數|Average|Average_Virtual Shared Memory|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% DPC Time|% DPC Time|計數|Average|Average_% DPC Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Idle Time|% Idle Time|計數|Average|Average_% Idle Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Interrupt Time|% Interrupt Time|計數|Average|Average_% Interrupt Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% IO Wait Time|% IO Wait Time|計數|Average|Average_% IO Wait Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Nice Time|% Nice Time|計數|Average|Average_% Nice Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Privileged Time|% Privileged Time|計數|Average|Average_% Privileged Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Processor Time|% Processor Time|計數|Average|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% User Time|% User Time|計數|Average|Average_% User Time|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Free Physical Memory|Free Physical Memory|計數|Average|Average_Free Physical Memory|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|計數|Average|Average_Free Space in Paging Files|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Free Virtual Memory|Free Virtual Memory|計數|Average|Average_Free Virtual Memory|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Processes|處理序|計數|Average|Average_Processes|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|計數|Average|Average_Size Stored In Paging Files|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Uptime|Uptime|計數|Average|Average_Uptime|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Users|使用者|計數|Average|Average_Users|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|計數|Average|Average_Current Disk Queue Length|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Available MBytes|可用的 MB|計數|Average|Average_Available MBytes|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|計數|Average|Average_% Committed Bytes In Use|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Bytes Received/sec|Bytes Received/sec|計數|Average|Average_Bytes Received/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|計數|Average|Average_Bytes Sent/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Bytes Total/sec|Bytes Total/sec|計數|Average|Average_Bytes Total/sec|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Average_Processor Queue Length|處理器佇列長度|計數|Average|Average_Processor Queue Length|Computer、ObjectName、InstanceName、CounterPath、及 sourcesystem|
|Heartbeat|Heartbeat|計數|總計|Heartbeat|Computer、OSType、Version、SourceComputerId|
|更新|更新|計數|Average|更新|電腦、產品、分類、UpdateState、選擇性、已核准|
|事件|事件|計數|Average|事件|Source、EventLog、Computer、EventCategory、EventLevel、EventLevelName、EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft。對等互連/peeringServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PrefixLatency|前置延遲|毫秒|Average|中間值前置延遲|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft。對等互連/對等互連

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SessionAvailabilityV4|會話可用性 V4|百分比|Average|V4 會話的可用性|ConnectionId|
|SessionAvailabilityV6|會話可用性 V6|百分比|Average|V6 會話的可用性|ConnectionId|
|IngressTrafficRate|輸入流量率|BitsPerSecond|Average|輸入流量速率（以每秒位數為單位）|ConnectionId|
|EgressTrafficRate|輸出流量率|BitsPerSecond|Average|輸出流量速率（以每秒位數為單位）|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueryDuration|查詢持續時間|毫秒|Average|最後一個間隔中的 DAX 查詢持續時間|無維度|
|QueryPoolJobQueueLength|執行緒︰查詢集區的作業佇列長度|計數|Average|查詢執行緒集區佇列中的作業數目。|無維度|
|qpu_high_utilization_metric|QPU 高使用率|計數|總計|前一分鐘的 QPU 高使用率，1 代表 QPU 高使用率，否則為 0|無維度|
|memory_metric|記憶體|位元組|Average|記憶體。 範圍：A1 為 0-3 GB、A2 為 0-5 GB、A3 為 0-10 GB、A4 為 0-25 GB、A5 為 0-50 GB、A6 為 0-100 GB|無維度|
|memory_thrashing_metric|記憶體猛移|百分比|Average|記憶體猛移的平均值。|無維度|


## <a name="microsoftprojectbabylonaccounts"></a>ProjectBabylon/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AssetDistributionByClassification|依分類的資產散發|計數|總計|指出已指派特定分類的資產數目，也就是使用該標籤來分類。|分類、來源、ResourceId|
|AssetDistributionByStorageType|依儲存體類型的資產散發|計數|總計|指出具有特定儲存體類型的資產數目。|StorageType，ResourceId|
|CatalogActiveUsers|每日作用中使用者|計數|總計|每天的作用中使用者數目|ResourceId|
|CatalogUsage|依作業的使用量散發|計數|總計|指出使用者對目錄進行的作業數目，亦即存取、搜尋、詞彙。|Operation，ResourceId|
|NumberOfAssetsWithClassifications|具有至少一個分類的資產數目|計數|Average|指出至少有一個標記分類的資產數目。|ResourceId|
|ScanCancelled|掃描已取消|計數|總計|表示已取消的掃描數目。|ResourceId|
|ScanCompleted|掃描完成|計數|總計|指出已成功完成的掃描數目。|ResourceId|
|ScanFailed|掃描失敗|計數|總計|指出失敗的掃描次數。|ResourceId|
|ScanTimeTaken|掃描花費時間|秒|總計|指出掃描時間總計（以秒為單位）。|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|計數|總計|Microsoft.Relay 的成功 ListenerConnections。|EntityName、OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|計數|總計|Microsoft.Relay 的 ListenerConnections 上發生的 ClientError。|EntityName、OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|計數|總計|Microsoft.Relay 的 ListenerConnections 上發生的 ServerError。|EntityName、OperationResult|
|SenderConnections-Success|SenderConnections-Success|計數|總計|Microsoft.Relay 的成功 SenderConnections。|EntityName、OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|計數|總計|Microsoft.Relay 的 SenderConnections 上發生的 ClientError。|EntityName、OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|計數|總計|Microsoft.Relay 的 SenderConnections 上發生的 ServerError。|EntityName、OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|計數|總計|Microsoft.Relay 的 ListenerConnections 總計。|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|計數|總計|Microsoft.Relay 的 SenderConnections 要求總計。|EntityName|
|ActiveConnections|ActiveConnections|計數|總計|Microsoft.Relay 的 ActiveConnections 總計。|EntityName|
|ActiveListeners|ActiveListeners|計數|總計|Microsoft.Relay 的 ActiveListeners 總計。|EntityName|
|BytesTransferred|BytesTransferred|計數|總計|Microsoft.Relay 的 BytesTransferred 總計。|EntityName|
|ListenerDisconnects|ListenerDisconnects|計數|總計|Microsoft.Relay 的 ListenerDisconnects 總計。|EntityName|
|SenderDisconnects|SenderDisconnects|計數|總計|Microsoft.Relay 的 SenderDisconnects 總計。|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SearchLatency|搜尋延遲|秒|Average|搜尋服務的平均搜尋延遲|無|
|SearchQueriesPerSecond|每秒搜尋查詢|每秒計數|Average|搜尋服務的每秒搜尋查詢|無|
|ThrottledSearchQueriesPercentage|節流的搜尋查詢百分比|百分比|Average|已針對搜尋查詢進行節流的搜尋服務百分比|無|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的要求|計數|總計|命名空間的成功要求總數|EntityName、OperationResult|
|ServerErrors|伺服器錯誤。|計數|總計|Microsoft.ServiceBus 的伺服器錯誤。|EntityName、OperationResult|
|UserErrors|使用者錯誤。|計數|總計|Microsoft.ServiceBus 的使用者錯誤。|EntityName、OperationResult|
|ThrottledRequests|節流的要求。|計數|總計|Microsoft.ServiceBus 的節流要求。|EntityName、OperationResult|
|IncomingRequests|傳入的要求|計數|總計|Microsoft.ServiceBus 的傳入要求。|EntityName|
|IncomingMessages|傳入訊息|計數|總計|Microsoft.ServiceBus 的傳入訊息。|EntityName|
|OutgoingMessages|外送訊息|計數|總計|Microsoft.ServiceBus 的傳出訊息。|EntityName|
|ActiveConnections|ActiveConnections|計數|總計|Microsoft.ServiceBus 的使用中連線總數。|無|
|ConnectionsOpened|已開啟的連線。|計數|Average|Microsoft.ServiceBus 的已開啟連線。|EntityName|
|ConnectionsClosed|已關閉的連線。|計數|Average|Microsoft.ServiceBus 的已關閉連線。|EntityName|
|Size|Size|位元組|Average|佇列/主題的大小 (以位元組為單位)。|EntityName|
|訊息|佇列/主題中的訊息計數。|計數|Average|佇列/主題中的訊息計數。|EntityName|
|ActiveMessages|佇列/主題中的作用中訊息計數。|計數|Average|佇列/主題中的作用中訊息計數。|EntityName|
|DeadletteredMessages|佇列/主題中的無效字母訊息計數。|計數|Average|佇列/主題中的無效字母訊息計數。|EntityName|
|ScheduledMessages|佇列/主題中已排程的訊息計數。|計數|Average|佇列/主題中已排程的訊息計數。|EntityName|
|NamespaceCpuUsage|CPU|百分比|最大值|服務匯流排 premium 命名空間 CPU 使用量計量。|複本|
|NamespaceMemoryUsage|記憶體使用量|百分比|最大值|服務匯流排 premium 命名空間記憶體使用量計量。|複本|
|CPUXNS|CPU （已淘汰）|百分比|最大值|服務匯流排 premium 命名空間 CPU 使用量計量。 此度量為淘汰。 請改用 CPU 度量（NamespaceCpuUsage）。|複本|
|WSXNS|記憶體使用量（已淘汰）|百分比|最大值|服務匯流排 premium 命名空間記憶體使用量計量。 此計量已過時。 請改用記憶體使用量（NamespaceMemoryUsage）度量。|複本|


## <a name="microsoftservicefabricmeshapplications"></a>ServiceFabricMesh/應用程式

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|計數|Average|在 milli> 核心中配置給此容器的 Cpu|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|位元組|Average|配置給此容器的記憶體（MB）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualCpu|ActualCpu|計數|Average|Milli> 核心中的實際 CPU 使用量|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualMemory|ActualMemory|位元組|Average|實際記憶體使用量（MB）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|CpuUtilization|CpuUtilization|百分比|Average|此容器的 CPU 使用率（以 AllocatedCpu 的百分比表示）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|百分比|Average|此容器的 CPU 使用率（以 AllocatedCpu 的百分比表示）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|計數|Average|Service Fabric 網狀應用程式的狀態|ApplicationName，狀態|
|ServiceStatus|ServiceStatus|計數|Average|Service Fabric 網狀應用程式中服務的健全狀況狀態|ApplicationName、Status、ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|計數|Average|Service Fabric 網狀應用程式中服務複本的健全狀況狀態|ApplicationName、Status、ServiceName、ServiceReplicaName|
|ContainerStatus|ContainerStatus|計數|Average|Service Fabric 網狀應用程式中的容器狀態|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName、Status|
|RestartCount|RestartCount|計數|Average|Service Fabric 網狀應用程式中的容器重新開機計數|ApplicationName、Status、ServiceName、ServiceReplicaName、CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ConnectionCount|連線計數|計數|最大值|使用者連線數量。|端點|
|MessageCount|訊息計數|計數|總計|訊息總量|無|
|InboundTraffic|輸入流量|位元組|總計|服務的輸入流量|無|
|OutboundTraffic|輸出流量|位元組|總計|服務的輸出流量|無|
|UserErrors|使用者錯誤|百分比|最大值|使用者錯誤的百分比|無|
|SystemErrors|系統錯誤|百分比|最大值|系統錯誤的百分比|無|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|無|
|physical_data_read_percent|資料 IO 百分比|百分比|Average|資料 IO 百分比|無|
|log_write_percent|記錄 IO 百分比|百分比|Average|記錄 IO 百分比。 不適用於資料倉儲。|無|
|dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比。 適用于以 DTU 為基礎的資料庫。|無|
|儲存|使用的資料空間|位元組|最大值|使用的資料空間。 不適用於資料倉儲。|無|
|connection_successful|成功的連線|計數|總計|成功的連線|無|
|connection_failed|失敗的連線|計數|總計|失敗的連線|無|
|blocked_by_firewall|遭到防火牆封鎖|計數|總計|遭到防火牆封鎖|無|
|deadlock|死結|計數|總計|發生. 不適用於資料倉儲。|無|
|storage_percent|使用的資料空間百分比|百分比|最大值|已使用的資料空間百分比。 不適用於資料倉儲或超大規模資料庫資料庫。|無|
|xtp_storage_percent|記憶體內部 OLTP 儲存體百分比|百分比|Average|記憶體內部 OLTP 儲存體百分比。 不適用於資料倉儲。|無|
|workers_percent|背景工作角色百分比|百分比|Average|背景工作百分比。 不適用於資料倉儲。|無|
|sessions_percent|工作階段百分比|百分比|Average|會話百分比。 不適用於資料倉儲。|無|
|dtu_limit|DTU 限制|計數|Average|DTU 限制。 適用于以 DTU 為基礎的資料庫。|無|
|dtu_used|已使用 DTU|計數|Average|使用的 DTU。 適用于以 DTU 為基礎的資料庫。|無|
|cpu_limit|CPU 限制|計數|Average|CPU 限制。 適用于以 vCore 為基礎的資料庫。|無|
|cpu_used|使用的 CPU|計數|Average|使用的 CPU。 適用于以 vCore 為基礎的資料庫。|無|
|dwu_limit|DWU 限制|計數|最大值|DWU 限制。 僅適用于資料倉儲。|無|
|dwu_consumption_percent|DWU 百分比|百分比|最大值|DWU 百分比。 僅適用于資料倉儲。|無|
|dwu_used|已使用 DWU|計數|最大值|已使用 DWU。 僅適用于資料倉儲。|無|
|cache_hit_percent|快取命中的百分比|百分比|最大值|快取命中百分比。 僅適用于資料倉儲。|無|
|cache_used_percent|已用快取的百分比|百分比|最大值|快取已使用百分比。 僅適用于資料倉儲。|無|
|sqlserver_process_core_percent<sup>1</sup> |SQL Server 處理常式核心百分比|百分比|最大值|SQL Server 進程的 CPU 使用量百分比（由作業系統測量）。|無|
|sqlserver_process_memory_percent<sup>1</sup> |SQL Server 處理常式記憶體百分比|百分比|最大值|SQL Server 進程的記憶體使用量百分比（由作業系統測量）。|無|
|tempdb_data_size<sup>2</sup> |Tempdb 資料檔案大小 Kb|計數|最大值|Tempdb 資料檔案大小（Kb）。|無|
|tempdb_log_size<sup>2</sup> |Tempdb 記錄檔大小 Kb|計數|最大值|Tempdb 記錄檔大小（Kb）。|無|
|tempdb_log_used_percent<sup>2</sup> |使用的 Tempdb 百分比記錄|百分比|最大值|使用的 Tempdb 百分比記錄。|無|
|local_tempdb_usage_percent|本機 tempdb 百分比|百分比|Average|本機 tempdb 百分比。 僅適用于資料倉儲。|無|
|app_cpu_billed|應用程式 CPU 計費|計數|總計|應用程式 CPU 計費。 適用于無伺服器資料庫。|無|
|app_cpu_percent|應用程式 CPU 百分比|百分比|Average|應用程式 CPU 百分比。 適用于無伺服器資料庫。|無|
|app_memory_percent|應用程式記憶體百分比|百分比|Average|應用程式記憶體百分比。 適用于無伺服器資料庫。|無|
|allocated_data_storage|已配置的資料空間|位元組|Average|已配置的資料儲存體。 不適用於資料倉儲。|無|
|memory_usage_percent|記憶體百分比|百分比|最大值|記憶體百分比。 僅適用于資料倉儲。|無|
|dw_backup_size_gb|資料儲存體大小|計數|總計|資料儲存體大小是由您的資料和交易記錄檔的大小所組成。 度量會計入帳單的「儲存體」部分。 僅適用于資料倉儲。|無|
|dw_snapshot_size_gb|快照集儲存體大小|計數|總計|快照集儲存體大小是快照集所捕獲的累加變更大小，以建立使用者定義和自動還原點。 度量會計入帳單的「儲存體」部分。 僅適用于資料倉儲。|無|
|dw_geosnapshot_size_gb|嚴重損壞修復儲存體大小|計數|總計|在您的帳單中，嚴重損壞修復儲存體大小會反映為「嚴重損壞修復儲存體」。 僅適用于資料倉儲。|無|
|wlg_allocation_relative_to_system_percent|依系統百分比配置的工作負載群組|百分比|最大值|配置的資源百分比，相對於每個工作負載群組的整個系統。 僅適用于資料倉儲。|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|依 cap 資源百分比配置的工作負載群組|百分比|最大值|針對每個工作負載群組，配置的資源百分比相對於指定的上限資源。 僅適用于資料倉儲。|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|工作負載群組使用中查詢|計數|總計|工作負載群組內的使用中查詢。 僅適用于資料倉儲。|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|工作負載群組佇列查詢|計數|總計|工作負載群組內已排入佇列的查詢。 僅適用于資料倉儲。|WorkloadGroupName,IsUserDefined|
|active_queries|現用查詢|計數|總計|跨所有工作負載群組的作用中查詢。 僅適用于資料倉儲。|無|
|queued_queries|佇列查詢|計數|總計|跨所有工作負載群組的佇列查詢。 僅適用于資料倉儲。|無|
|wlg_active_queries_timeouts|工作負載群組查詢超時|計數|總計|工作負載群組已超時的查詢。 僅適用于資料倉儲。|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|有效的最低資源百分比|百分比|最大值|針對工作負載群組保留和隔離的最小資源百分比，將服務層級的最小值納入考慮。 僅適用于資料倉儲。|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|有效的 cap 資源百分比|百分比|最大值|針對工作負載群組所允許的資源百分比固定限制，將配置給其他工作負載群組的有效最小資源百分比納入考慮。 僅適用于資料倉儲。|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|完整備份儲存體大小|位元組|最大值|累計完整備份儲存體大小。 適用于以 vCore 為基礎的資料庫。 不適用於超大規模資料庫資料庫。|無|
|diff_backup_size_bytes|差異備份儲存體大小|位元組|最大值|累計差異備份儲存體大小。 適用于以 vCore 為基礎的資料庫。 不適用於超大規模資料庫資料庫。|無|
|log_backup_size_bytes|記錄備份儲存體大小|位元組|最大值|累計記錄備份儲存體大小。 適用于以 vCore 為基礎的和超大規模資料庫資料庫。|無|
|snapshot_backup_size_bytes|快照集備份儲存體大小|位元組|最大值|累計快照集備份儲存體大小。 適用于超大規模資料庫資料庫。|無|
|base_blob_size_bytes|基底 blob 儲存體大小|位元組|最大值|基底 blob 儲存體大小。 適用于超大規模資料庫資料庫。|無|

<sup>1</sup>此計量適用于使用具有2虛擬核心和更高版本的 vCore 購買模型，或 200 dtu 和更新版本（適用于以 DTU 為基礎的購買模型）的資料庫。 

<sup>2</sup>此計量適用于使用具有2虛擬核心和更高版本的 vCore 購買模型，或 200 dtu 和更新版本（適用于以 DTU 為基礎的購買模型）的資料庫。 此度量目前不適用於超大規模資料庫資料庫或資料倉儲。

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|無|
|database_cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|DatabaseResourceId|
|physical_data_read_percent|資料 IO 百分比|百分比|Average|資料 IO 百分比|無|
|database_physical_data_read_percent|資料 IO 百分比|百分比|Average|資料 IO 百分比|DatabaseResourceId|
|log_write_percent|記錄 IO 百分比|百分比|Average|記錄 IO 百分比|無|
|database_log_write_percent|記錄 IO 百分比|百分比|Average|記錄 IO 百分比|DatabaseResourceId|
|dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比。 適用于以 DTU 為基礎的彈性集區。|無|
|database_dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比|DatabaseResourceId|
|storage_percent|使用的資料空間百分比|百分比|Average|使用的資料空間百分比|無|
|workers_percent|背景工作角色百分比|百分比|Average|背景工作角色百分比|無|
|database_workers_percent|背景工作角色百分比|百分比|Average|背景工作角色百分比|DatabaseResourceId|
|sessions_percent|工作階段百分比|百分比|Average|工作階段百分比|無|
|database_sessions_percent|工作階段百分比|百分比|Average|工作階段百分比|DatabaseResourceId|
|eDTU_limit|eDTU 限制|計數|Average|eDTU 限制。 適用于以 DTU 為基礎的彈性集區。|無|
|storage_limit|資料大小上限|位元組|Average|資料大小上限|無|
|eDTU_used|已使用 eDTU|計數|Average|使用的 eDTU。 適用于以 DTU 為基礎的彈性集區。|無|
|database_eDTU_used|已使用 eDTU|計數|Average|已使用 eDTU|DatabaseResourceId|
|storage_used|使用的資料空間|位元組|Average|使用的資料空間|無|
|database_storage_used|使用的資料空間|位元組|Average|使用的資料空間|DatabaseResourceId|
|xtp_storage_percent|記憶體內部 OLTP 儲存體百分比|百分比|Average|記憶體內部 OLTP 儲存體百分比|無|
|cpu_limit|CPU 限制|計數|Average|CPU 限制。 適用于以 vCore 為基礎的彈性集區。|無|
|database_cpu_limit|CPU 限制|計數|Average|CPU 限制|DatabaseResourceId|
|cpu_used|使用的 CPU|計數|Average|使用的 CPU。 適用于以 vCore 為基礎的彈性集區。|無|
|database_cpu_used|使用的 CPU|計數|Average|使用的 CPU|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|SQL Server 處理常式核心百分比|百分比|最大值|SQL Server 進程的 CPU 使用量百分比（由作業系統測量）。 適用于彈性集區。 |無|
|sqlserver_process_memory_percent<sup>1</sup>|SQL Server 處理常式記憶體百分比|百分比|最大值|SQL Server 進程的記憶體使用量百分比（由作業系統測量）。 適用于彈性集區。 |無|
|tempdb_data_size<sup>2</sup>|Tempdb 資料檔案大小 Kb|計數|最大值|Tempdb 資料檔案大小（Kb）。|無|
|tempdb_log_size<sup>2</sup>|Tempdb 記錄檔大小 Kb|計數|最大值|Tempdb 記錄檔大小（Kb）。 |無|
|tempdb_log_used_percent<sup>2</sup>|使用的 Tempdb 百分比記錄|百分比|最大值|使用的 Tempdb 百分比記錄。|無|
|allocated_data_storage|已配置的資料空間|位元組|Average|已配置的資料空間|無|
|database_allocated_data_storage|已配置的資料空間|位元組|Average|已配置的資料空間|DatabaseResourceId|
|allocated_data_storage_percent|已配置的資料空間百分比|百分比|最大值|已配置的資料空間百分比|無|

<sup>1</sup>此計量適用于使用具有2虛擬核心和更高版本的 vCore 購買模型，或 200 dtu 和更新版本（適用于以 DTU 為基礎的購買模型）的資料庫。 

<sup>2</sup>此計量適用于使用具有2虛擬核心和更高版本的 vCore 購買模型，或 200 dtu 和更新版本（適用于以 DTU 為基礎的購買模型）的資料庫。 此度量目前不適用於超大規模資料庫資料庫。


## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|使用的資料空間|位元組|Average|使用的資料空間|ElasticPoolResourceId|
|database_storage_used|使用的資料空間|位元組|Average|使用的資料空間|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|已使用 DTU|計數|Average|已使用 DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|virtual_core_count|虛擬核心計數|計數|Average|虛擬核心計數|無|
|avg_cpu_percent|CPU 百分比平均|百分比|Average|CPU 百分比平均|無|
|reserved_storage_mb|保留的儲存體空間|計數|Average|保留的儲存體空間|無|
|storage_space_used_mb|使用的儲存體空間|計數|Average|使用的儲存體空間|無|
|io_requests|IO 要求計數|計數|Average|IO 要求計數|無|
|io_bytes_read|讀取的 IO 位元組|位元組|Average|讀取的 IO 位元組|無|
|io_bytes_written|寫入的 IO 位元組|位元組|Average|寫入的 IO 位元組|無|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|UsedCapacity|已使用容量|位元組|Average|帳戶使用的容量|無|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BlobCapacity|Blob 容量|位元組|Average|儲存體帳戶 Blob 服務所使用的儲存體量 (位元組)。|BlobType，層|
|BlobCount|Blob 計數|計數|Average|儲存體帳戶 Blob 服務中的 Blob 數目。|BlobType，層|
|ContainerCount|Blob 容器計數|計數|Average|儲存體帳戶 Blob 服務中的容器數目。|無|
|IndexCapacity|索引容量|位元組|Average|ADLS Gen2 （階層）索引所使用的儲存體數量（以位元組為單位）。|無|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TableCapacity|資料表容量|位元組|Average|儲存體帳戶資料表服務所使用的儲存體量 (位元組)。|無|
|TableCount|資料表計數|計數|Average|儲存體帳戶資料表服務中的資料表數目。|無|
|TableEntityCount|資料表實體計數|計數|Average|儲存體帳戶資料表服務中的資料表實體數目。|無|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|FileCapacity|檔案容量|位元組|Average|儲存體帳戶的檔案服務所使用的儲存體數量（以位元組為單位）。|FileShare|
|FileCount|檔案計數|計數|Average|儲存體帳戶之檔案服務中的檔案數目。|FileShare|
|FileShareCount|檔案共用計數|計數|Average|儲存體帳戶之檔案服務中的檔案共用數目。|無|
|FileShareSnapshotCount|檔案共用快照集計數|計數|Average|儲存體帳戶之檔案服務中的共用上存在的快照集數目。|FileShare|
|FileShareSnapshotSize|檔案共用快照集大小|位元組|Average|儲存體帳戶之檔案服務中的快照集所使用的儲存體數量（以位元組為單位）。|FileShare|
|FileShareQuota|檔案共用配額大小|位元組|Average|Azure 檔案儲存體服務可使用的儲存體數量上限（以位元組為單位）。|FileShare|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication、檔案共用|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication、檔案共用|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication、檔案共用|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication、檔案共用|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication、檔案共用|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication、檔案共用|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueueCapacity|佇列容量|位元組|Average|儲存體帳戶佇列服務所使用的儲存體量 (位元組)。|無|
|QueueCount|佇列計數|計數|Average|儲存體帳戶佇列服務中的佇列數目。|無|
|QueueMessageCount|佇列訊息計數|計數|Average|儲存體帳戶佇列服務中的佇列訊息大約數目。|無|
|交易|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|





## <a name="microsoftstoragecachecaches"></a>StorageCache/快取

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ClientIOPS|用戶端 IOPS 總計|計數|Average|快取處理的用戶端檔案作業的速率。|無|
|ClientLatency|平均用戶端延遲|毫秒|Average|用戶端檔案作業對儲存體快取的平均延遲。|無|
|ClientReadIOPS|用戶端讀取 IOPS|每秒計數|Average|每秒的用戶端讀取作業。|無|
|ClientReadThroughput|平均快取讀取輸送量|每秒位元組|Average|用戶端讀取資料傳送速率。|無|
|ClientWriteIOPS|用戶端寫入 IOPS|每秒計數|Average|每秒的用戶端寫入作業。|無|
|ClientWriteThroughput|平均快取寫入輸送量|每秒位元組|Average|用戶端寫入資料傳送速率。|無|
|ClientMetadataReadIOPS|用戶端中繼資料讀取 IOPS|每秒計數|Average|傳送至快取的用戶端檔案作業的速率（不包括資料讀取），不會修改持續性狀態。|無|
|ClientMetadataWriteIOPS|用戶端中繼資料寫入 IOPS|每秒計數|Average|傳送至快取的用戶端檔案作業的速率（不包括資料寫入），這會修改持續性狀態。|無|
|ClientLockIOPS|用戶端鎖定 IOPS|每秒計數|Average|每秒的用戶端檔案鎖定作業數。|無|
|StorageTargetHealth|儲存體目標健全狀況|計數|Average|快取和儲存體目標之間的連線能力測試布林值結果。|無|
|Uptime|Uptime|計數|Average|快取和監視系統之間的連線能力測試布林值結果。|無|
|StorageTargetIOPS|總 StorageTarget IOPS|計數|Average|快取傳送至特定 StorageTarget 之所有檔案作業的速率。|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget 寫入 IOPS|計數|Average|快取傳送至特定 StorageTarget 之檔案寫入作業的速率。|StorageTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget 非同步寫入輸送量|每秒位元組|Average|快取以非同步方式將資料寫入特定 StorageTarget 的速率。 這些是不會導致用戶端封鎖的無機會寫入。|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget 同步寫入輸送量|每秒位元組|Average|快取以同步方式將資料寫入特定 StorageTarget 的速率。 這些是會導致用戶端封鎖的寫入。|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget 總寫入輸送量|每秒位元組|Average|快取將資料寫入特定 StorageTarget 的總速率。|StorageTarget|
|StorageTargetLatency|StorageTarget 延遲|毫秒|Average|快取傳送至 partricular StorageTarget 之所有檔案作業的平均反復行程延遲。|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget 中繼資料讀取 IOPS|每秒計數|Average|快取傳送至特定 StorageTarget 之檔案作業的速率，不會修改持續性狀態，也不會排除讀取作業。|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget 中繼資料寫入 IOPS|每秒計數|Average|此快取會傳送至特定 StorageTarget 之檔案作業的速率，會修改持續性狀態，並排除寫入作業。|StorageTarget|
|StorageTargetReadIOPS|StorageTarget 讀取 IOPS|每秒計數|Average|快取傳送至特定 StorageTarget 的檔案讀取作業速率。|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget 讀取前輸送量|每秒位元組|Average|快取 opportunisticly 從 StorageTarget 讀取資料的速率。|StorageTarget|
|StorageTargetFillThroughput|StorageTarget 填滿輸送量|每秒位元組|Average|快取從 StorageTarget 讀取資料以處理快取遺漏的速率。|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget 讀取輸送量總計|每秒位元組|Average|快取從特定 StorageTarget 讀取資料的總速率。|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ServerSyncSessionResult|同步會話結果|計數|Average|每次伺服器端點成功完成與雲端端點的同步會話時，會將值記錄為1的度量|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|檔案已同步|計數|總計|已同步處理的檔案計數|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|檔案無法同步|計數|總計|無法同步的檔案計數|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncBatchTransferredFileBytes|同步的位元組|位元組|總計|同步會話的已傳輸檔案大小總計|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncServerHeartbeat|伺服器線上狀態|計數|最大值|每次 resigtered 伺服器順利記錄與雲端端點的心跳時，其值會記錄1的度量|ServerName|
|StorageSyncRecallIOTotalSizeBytes|雲端階層處理重新叫用|位元組|總計|伺服器回收的資料大小總計|ServerName|
|StorageSyncRecalledTotalNetworkBytes|雲端階層處理重新叫用大小|位元組|總計|重新叫用的資料大小|SyncGroupName、ServerName|
|StorageSyncRecallThroughputBytesPerSecond|雲端階層處理重新叫用輸送量|每秒位元組|Average|資料重新叫用輸送量的大小|SyncGroupName、ServerName|
|StorageSyncRecalledNetworkBytesByApplication|雲端階層處理重新叫用應用程式的大小|位元組|總計|應用程式重新叫用的資料大小|SyncGroupName、ServerName、ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|檔案已同步|計數|總計|已同步處理的檔案計數|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|檔案無法同步|計數|總計|無法同步的檔案計數|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupBatchTransferredFileBytes|同步的位元組|位元組|總計|同步會話的已傳輸檔案大小總計|SyncGroupName、ServerEndpointName、SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|檔案已同步|計數|總計|已同步處理的檔案計數|ServerEndpointName、SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|檔案無法同步|計數|總計|無法同步的檔案計數|ServerEndpointName、SyncDirection|
|ServerEndpointBatchTransferredFileBytes|同步的位元組|位元組|總計|同步會話的已傳輸檔案大小總計|ServerEndpointName、SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ServerHeartbeat|伺服器線上狀態|計數|最大值|每次 resigtered 伺服器順利記錄與雲端端點的心跳時，其值會記錄1的度量|ServerResourceId、ServerName|
|ServerRecallIOTotalSizeBytes|雲端階層處理重新叫用|位元組|總計|伺服器回收的資料大小總計|ServerResourceId、ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 使用率|百分比|最大值|SU % 使用率|LogicalName、PartitionId|
|InputEvents|輸入事件|計數|總計|輸入事件|LogicalName、PartitionId|
|InputEventBytes|輸入事件位元組|位元組|總計|輸入事件位元組|LogicalName、PartitionId|
|LateInputEvents|延遲輸入事件|計數|總計|延遲輸入事件|LogicalName、PartitionId|
|OutputEvents|輸出事件|計數|總計|輸出事件|LogicalName、PartitionId|
|ConversionErrors|資料轉換錯誤|計數|總計|資料轉換錯誤|LogicalName、PartitionId|
|Errors|執行階段錯誤|計數|總計|執行階段錯誤|LogicalName、PartitionId|
|DroppedOrAdjustedEvents|次序錯誤事件|計數|總計|次序錯誤事件|LogicalName、PartitionId|
|AMLCalloutRequests|函式要求|計數|總計|函式要求|LogicalName、PartitionId|
|AMLCalloutFailedRequests|失敗的函式要求|計數|總計|失敗的函式要求|LogicalName、PartitionId|
|AMLCalloutInputEvents|函式事件|計數|總計|函式事件|LogicalName、PartitionId|
|DeserializationError|輸入還原序列化錯誤|計數|總計|輸入還原序列化錯誤|LogicalName、PartitionId|
|EarlyInputEvents|早期輸入事件|計數|總計|早期輸入事件|LogicalName、PartitionId|
|OutputWatermarkDelaySeconds|浮水印延遲秒數|秒|最大值|浮水印延遲秒數|LogicalName、PartitionId|
|InputEventsSourcesBacklogged|待處理輸入事件數|計數|最大值|待處理輸入事件數|LogicalName、PartitionId|
|InputEventsSourcesPerSecond|收到的輸入來源數|計數|總計|收到的輸入來源數|LogicalName、PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Synapse/工作區

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|管線執行結束|計數|總計|成功、失敗或已取消之協調流程管線執行的計數|結果、FailureType、管線|
|OrchestrationActivityRunsEnded|活動執行結束|計數|總計|成功、失敗或已取消之協調流程活動的計數|Result、FailureType、Activity、ActivityType、Pipeline|
|OrchestrationTriggersEnded|觸發程式已結束|計數|總計|成功、失敗或已取消之協調流程觸發程式的計數|Result、FailureType、Trigger|
|SQLOnDemandLoginAttempts|登入嘗試|計數|總計|成功或失敗的登入嘗試計數|結果|
|SQLOnDemandQueriesEnded|查詢已結束|計數|總計|成功、失敗或已取消的查詢計數|結果|
|SQLOnDemandQueryProcessedBytes|已處理的資料量|位元組|總計|查詢所處理的資料量|無|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Synapse/workspace/bigDataPools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SparkJobsEnded|結束的應用程式|計數|總計|已結束的應用程式計數|JobType、Jobresult.txt|
|CoresCapacity|核心容量|計數|最大值|核心容量|無|
|MemoryCapacityGB|記憶體容量（GB）|計數|最大值|記憶體容量（GB）|無|

## <a name="microsoftsynapseworkspacessqlpools"></a>Synapse/workspace/sqlPools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|DWULimit|DWU 限制|計數|最大值|SQL 集區的服務等級目標|無|
|DWUUsed|已使用 DWU|計數|最大值|表示跨 SQL 集區使用方式的高階標記法。 測量依據 DWU 限制 * DWU 百分比|無|
|DWUUsedPercent|DWU 已使用百分比|百分比|最大值|表示跨 SQL 集區使用方式的高階標記法。 以 CPU 百分比與資料 IO 百分比之間的最大值來測量|無|
|ConnectionsBlockedByFirewall|防火牆封鎖的連線|計數|總計|防火牆規則封鎖的連線計數。 若計數很高，請重新流覽 SQL 集區的存取控制原則，並監視這些連接|無|
|AdaptiveCacheHitPercent|調適型快取命中百分比|百分比|最大值|測量工作負載使用適應性快取的程度。 將此計量與快取點擊百分比計量搭配使用，以判斷是否要調整額外容量，或重新執行工作負載以序列化快取|無|
|AdaptiveCacheUsedPercent|調適型快取已使用百分比|百分比|最大值|測量工作負載使用適應性快取的程度。 使用此計量搭配 [快取使用百分比] 計量來決定是否要調整額外容量，或重新執行工作負載以序列化快取|無|
|LocalTempDBUsedPercent|本機 tempdb 使用百分比|百分比|最大值|跨所有計算節點的本機 tempdb 使用率-值會每五分鐘發出一次|無|
|MemoryUsedPercent|記憶體使用百分比|百分比|最大值|SQL 集區中所有節點的記憶體使用率|無|
|連接|連接|計數|總計|SQL 集區的登入總數計數|結果|
|WLGActiveQueries|工作負載群組使用中查詢|計數|總計|工作負載群組內的使用中查詢。 使用未篩選的此計量和解除會顯示在系統上執行的所有使用中查詢|IsUserDefined、WorkloadGroup|
|WLGActiveQueriesTimeouts|工作負載群組查詢超時|計數|總計|查詢已超時的工作負載群組。此計量所報告的查詢超時，只有在查詢開始執行之後（不包括因鎖定或資源等候而造成的等候時間）|IsUserDefined、WorkloadGroup|
|WLGAllocationBySystemPercent|依系統百分比配置的工作負載群組|百分比|最大值|相對於整個系統的資源配置百分比|IsUserDefined、WorkloadGroup|
|WLGAllocationByMaxResourcePercent|依最大資源百分比配置的工作負載群組|百分比|最大值|針對每個工作負載群組，顯示相對於有效 cap 資源百分比的資源配置百分比。 此度量可提供工作負載群組的有效使用率|IsUserDefined、WorkloadGroup|
|WLGEffectiveCapResourcePercent|有效的 cap 資源百分比|百分比|最大值|工作負載群組的有效上限資源百分比。 如果有其他 min_percentage_resource > 0 的工作負載群組，effective_cap_percentage_resource 會按比例降低|IsUserDefined、WorkloadGroup|
|wlg_effective_min_resource_percent|有效的最低資源百分比|百分比|最小值|允許考慮服務等級和工作負載群組設定的有效最小資源百分比設定。 有效的 min_percentage_resource 可以在較低的服務層級上調整|IsUserDefined、WorkloadGroup|
|WLGQueuedQueries|工作負載群組佇列查詢|計數|總計|達到最大並行限制之後，佇列的要求累計計數|IsUserDefined、WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|IngressReceivedMessages|輸入接收的訊息|計數|總計|從所有事件中樞或 IoT 中樞事件來源讀取的訊息計數|無|
|IngressReceivedInvalidMessages|輸入接收的無效訊息|計數|總計|從所有事件中樞或 IoT 中樞事件來源讀取的無效訊息計數|無|
|IngressReceivedBytes|輸入接收的位元組|位元組|總計|從所有事件來源讀取的位元組計數|無|
|IngressStoredBytes|輸入儲存的位元組|位元組|總計|已成功處理且可供查詢的事件總大小|無|
|IngressStoredEvents|輸入儲存的事件|計數|總計|已成功處理且可供查詢的壓平合併事件計數|無|
|IngressReceivedMessagesTimeLag|輸入接收的訊息時間延遲|秒|最大值|訊息在事件來源中加入佇列的時間與在輸入中處理的時間之間的差異|無|
|IngressReceivedMessagesCountLag|輸入接收的訊息計數延遲|計數|Average|事件來源資料分割中最後排入佇列之訊息的序號與輸入中所處理訊息的序號之間的差異|無|
|WarmStorageMaxProperties|暖儲存空間上限屬性|計數|最大值|適用于 S1/S2 SKU 的環境所允許的屬性數目上限，以及 PAYG SKU 的暖存放區所允許的最大屬性數目|無|
|WarmStorageUsedProperties|暖儲存體使用的屬性 |計數|最大值|適用于 S1/S2 SKU 的環境所使用的屬性數目，以及 PAYG SKU 的暖存放區所使用的屬性數目|無|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|IngressReceivedMessages|輸入接收的訊息|計數|總計|從事件來源讀取的訊息計數|無|
|IngressReceivedInvalidMessages|輸入接收的無效訊息|計數|總計|從事件來源讀取的無效訊息計數|無|
|IngressReceivedBytes|輸入接收的位元組|位元組|總計|從事件來源讀取的位元組計數|無|
|IngressStoredBytes|輸入儲存的位元組|位元組|總計|已成功處理且可供查詢的事件總大小|無|
|IngressStoredEvents|輸入儲存的事件|計數|總計|已成功處理且可供查詢的壓平合併事件計數|無|
|IngressReceivedMessagesTimeLag|輸入接收的訊息時間延遲|秒|最大值|訊息在事件來源中加入佇列的時間與在輸入中處理的時間之間的差異|無|
|IngressReceivedMessagesCountLag|輸入接收的訊息計數延遲|計數|Average|事件來源資料分割中最後排入佇列之訊息的序號與輸入中所處理訊息的序號之間的差異|無|
|WarmStorageMaxProperties|暖儲存空間上限屬性|計數|最大值|適用于 S1/S2 SKU 的環境所允許的屬性數目上限，以及 PAYG SKU 的暖存放區所允許的最大屬性數目|無|
|WarmStorageUsedProperties|暖儲存體使用的屬性 |計數|最大值|適用于 S1/S2 SKU 的環境所使用的屬性數目，以及 PAYG SKU 的暖存放區所使用的屬性數目|無|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>VMwareCloudSimple/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|每秒位元組|Average|在取樣期間讀取作業所造成的平均磁片輸送量。|無|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|每秒位元組|Average|因取樣期間的寫入作業而造成的平均磁片輸送量。|無|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|由於在取樣期間讀取作業的總磁片輸送量。|無|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|因取樣期間的寫入作業而造成的總磁片輸送量。|無|
|DiskReadOperations|磁片讀取作業|計數|總計|上一個範例期間的 IO 讀取作業數目。 請注意，這些作業可能是可變大小的。|無|
|DiskWriteOperations|磁片寫入作業|計數|總計|上一個範例期間的 IO 寫入作業數目。 請注意，這些作業可能是可變大小的。|無|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|上一個取樣期間內 IO 讀取作業的平均數目。 請注意，這些作業可能是可變大小的。|無|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|上一個範例期間的 IO 寫入作業平均數目。 請注意，這些作業可能是可變大小的。|無|
|DiskReadLatency|磁碟讀取延遲|毫秒|Average|總讀取延遲。 裝置和核心讀取延遲的總和。|無|
|DiskWriteLatency|磁碟寫入延遲|毫秒|Average|總寫入延遲。 裝置和核心寫入延遲的總和。|無|
|NetworkInBytesPerSecond|網路位元組/秒|每秒位元組|Average|已接收流量的平均網路輸送量。|無|
|NetworkOutBytesPerSecond|網路輸出位元組數/秒|每秒位元組|Average|傳輸流量的平均網路輸送量。|無|
|Network In|Network In|位元組|總計|已接收流量的網路輸送量總計。|無|
|Network Out|Network Out|位元組|總計|傳輸流量的網路輸送量總計。|無|
|MemoryUsed|使用的記憶體|位元組|Average|VM 使用的電腦記憶體數量。|無|
|MemoryGranted|授與的記憶體|位元組|Average|主機授與 VM 的記憶體數量。 記憶體在觸及一次之後才會授與給主機，而且如果 VMkernel 需要記憶體，則會交換或膨脹。|無|
|MemoryActive|記憶體使用中|位元組|Average|VM 在過去小型時間範圍內使用的記憶體數量。 這是 VM 目前所需記憶體數量的 "true"。 其他、未使用的記憶體可能會交換或膨脹，而不會影響到來賓的效能。|無|
|Percentage CPU|Percentage CPU|百分比|Average|CPU 使用率。 此值是以100% 回報，代表系統上的所有處理器核心。 例如，使用四核心系統50% 的雙向 VM，完全使用兩個核心。|無|
|PercentageCpuReady|CPU 已就緒百分比|毫秒|總計|「就緒時間」是等待 CPU 在過去的更新間隔中變成可用的時間。|無|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CpuPercentage|CPU 百分比|百分比|Average|CPU 百分比|執行個體|
|MemoryPercentage|記憶體百分比|百分比|Average|記憶體百分比|執行個體|
|DiskQueueLength|磁碟佇列長度|計數|Average|磁碟佇列長度|執行個體|
|HttpQueueLength|Http 佇列長度|計數|Average|Http 佇列長度|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|TcpSynSent|TCP Syn 已傳送|計數|Average|TCP Syn 已傳送|執行個體|
|TcpSynReceived|已接收 TCP Syn|計數|Average|已接收 TCP Syn|執行個體|
|TcpEstablished|TCP 已建立|計數|Average|TCP 已建立|執行個體|
|TcpFinWait1|TCP Fin 等候1|計數|Average|TCP Fin 等候1|執行個體|
|TcpFinWait2|TCP Fin 等待2|計數|Average|TCP Fin 等待2|執行個體|
|TcpClosing|TCP 關閉|計數|Average|TCP 關閉|執行個體|
|TcpCloseWait|TCP 關閉等候|計數|Average|TCP 關閉等候|執行個體|
|TcpLastAck|TCP 最後 Ack|計數|Average|TCP 最後 Ack|執行個體|
|TcpTimeWait|TCP 等候時間|計數|Average|TCP 等候時間|執行個體|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (不包括函式) 

> [!NOTE]
> **檔案系統使用**方式是全域推出的新計量，除非您已列入個人預覽版的白名單中，否則不會有任何資料。

> [!IMPORTANT]
> **平均回應時間**將會被取代，以避免與計量匯總產生混淆。 使用**回應時間**做為取代。

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|秒|總計|CPU 時間|執行個體|
|Requests|Requests|計數|總計|Requests|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http101|Http 101|計數|總計|Http 101|執行個體|
|Http2xx|Http 2xx|計數|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|計數|總計|Http 3xx|執行個體|
|Http401|Http 401|計數|總計|Http 401|執行個體|
|Http403|Http 403|計數|總計|Http 403|執行個體|
|Http404|Http 404|計數|總計|Http 404|執行個體|
|Http406|Http 406|計數|總計|Http 406|執行個體|
|Http4xx|Http 4xx|計數|總計|Http 4xx|執行個體|
|Http5xx|Http 伺服器錯誤|計數|總計|Http 伺服器錯誤|執行個體|
|MemoryWorkingSet|記憶體工作集|位元組|Average|記憶體工作集|執行個體|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|Average|平均記憶體工作集|執行個體|
|HttpResponseTime|回應時間|秒|總計|回應時間|執行個體|
|AverageResponseTime|平均回應時間（已淘汰）|秒|Average|平均回應時間|執行個體|
|AppConnections|連接|計數|Average|連接|執行個體|
|處理|控制代碼計數|計數|Average|控制代碼計數|執行個體|
|執行緒|執行緒計數|計數|Average|執行緒計數|執行個體|
|PrivateBytes|私用位元組|位元組|Average|私用位元組|執行個體|
|IoReadBytesPerSecond|每秒的 IO 讀取位元組數|每秒位元組|總計|每秒的 IO 讀取位元組數|執行個體|
|IoWriteBytesPerSecond|每秒的 IO 寫入位元組數|每秒位元組|總計|每秒的 IO 寫入位元組數|執行個體|
|IoOtherBytesPerSecond|每秒的 IO 其他位元組數|每秒位元組|總計|每秒的 IO 其他位元組數|執行個體|
|IoReadOperationsPerSecond|每秒的 IO 讀取作業數|每秒位元組|總計|每秒的 IO 讀取作業數|執行個體|
|IoWriteOperationsPerSecond|每秒的 IO 寫入作業數|每秒位元組|總計|每秒的 IO 寫入作業數|執行個體|
|IoOtherOperationsPerSecond|每秒的 IO 其他作業數|每秒位元組|總計|每秒的 IO 其他作業數|執行個體|
|RequestsInApplicationQueue|應用程式佇列中的要求數|計數|Average|應用程式佇列中的要求數|執行個體|
|CurrentAssemblies|目前的組件|計數|Average|目前的組件|執行個體|
|TotalAppDomains|應用程式網域總計|計數|Average|應用程式網域總計|執行個體|
|TotalAppDomainsUnloaded|已卸載的應用程式網域總計|計數|Average|已卸載的應用程式網域總計|執行個體|
|Gen0Collections|Gen 0 記憶體回收|計數|總計|Gen 0 記憶體回收|執行個體|
|Gen1Collections|Gen 1 記憶體回收|計數|總計|Gen 1 記憶體回收|執行個體|
|Gen2Collections|Gen 2 記憶體回收|計數|總計|Gen 2 記憶體回收|執行個體|
|HealthCheckStatus|健全狀況檢查狀態|計數|Average|健全狀況檢查狀態|執行個體|
|FileSystemUsage|檔案系統使用方式|位元組|Average|檔案系統使用方式|無|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (函式)

> [!NOTE]
> **檔案系統使用**方式是全域推出的新計量，除非您已列入個人預覽版的白名單中，否則不會有任何資料。

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http5xx|Http 伺服器錯誤|計數|總計|Http 伺服器錯誤|執行個體|
|MemoryWorkingSet|記憶體工作集|位元組|Average|記憶體工作集|執行個體|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|Average|平均記憶體工作集|執行個體|
|FunctionExecutionUnits|函式執行單位|MB/毫秒|總計|[函式執行單位](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|執行個體|
|FunctionExecutionCount|函式執行計數|計數|總計|函式執行計數|執行個體|
|PrivateBytes|私用位元組|位元組|Average|私用位元組|執行個體|
|IoReadBytesPerSecond|每秒的 IO 讀取位元組數|每秒位元組|總計|每秒的 IO 讀取位元組數|執行個體|
|IoWriteBytesPerSecond|每秒的 IO 寫入位元組數|每秒位元組|總計|每秒的 IO 寫入位元組數|執行個體|
|IoOtherBytesPerSecond|每秒的 IO 其他位元組數|每秒位元組|總計|每秒的 IO 其他位元組數|執行個體|
|IoReadOperationsPerSecond|每秒的 IO 讀取作業數|每秒位元組|總計|每秒的 IO 讀取作業數|執行個體|
|IoWriteOperationsPerSecond|每秒的 IO 寫入作業數|每秒位元組|總計|每秒的 IO 寫入作業數|執行個體|
|IoOtherOperationsPerSecond|每秒的 IO 其他作業數|每秒位元組|總計|每秒的 IO 其他作業數|執行個體|
|RequestsInApplicationQueue|應用程式佇列中的要求數|計數|Average|應用程式佇列中的要求數|執行個體|
|CurrentAssemblies|目前的組件|計數|Average|目前的組件|執行個體|
|TotalAppDomains|應用程式網域總計|計數|Average|應用程式網域總計|執行個體|
|TotalAppDomainsUnloaded|已卸載的應用程式網域總計|計數|Average|已卸載的應用程式網域總計|執行個體|
|Gen0Collections|Gen 0 記憶體回收|計數|總計|Gen 0 記憶體回收|執行個體|
|Gen1Collections|Gen 1 記憶體回收|計數|總計|Gen 1 記憶體回收|執行個體|
|Gen2Collections|Gen 2 記憶體回收|計數|總計|Gen 2 記憶體回收|執行個體|
|HealthCheckStatus|健全狀況檢查狀態|計數|Average|健全狀況檢查狀態|執行個體|
|FileSystemUsage|檔案系統使用方式|位元組|Average|檔案系統使用方式|無|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|秒|總計|CPU 時間|執行個體|
|Requests|Requests|計數|總計|Requests|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http101|Http 101|計數|總計|Http 101|執行個體|
|Http2xx|Http 2xx|計數|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|計數|總計|Http 3xx|執行個體|
|Http401|Http 401|計數|總計|Http 401|執行個體|
|Http403|Http 403|計數|總計|Http 403|執行個體|
|Http404|Http 404|計數|總計|Http 404|執行個體|
|Http406|Http 406|計數|總計|Http 406|執行個體|
|Http4xx|Http 4xx|計數|總計|Http 4xx|執行個體|
|Http5xx|Http 伺服器錯誤|計數|總計|Http 伺服器錯誤|執行個體|
|MemoryWorkingSet|記憶體工作集|位元組|Average|記憶體工作集|執行個體|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|Average|平均記憶體工作集|執行個體|
|AverageResponseTime|平均回應時間|秒|Average|平均回應時間|執行個體|
|HttpResponseTime|回應時間|秒|Average|回應時間|執行個體|
|FunctionExecutionUnits|函式執行單位|計數|總計|函式執行單位|執行個體|
|FunctionExecutionCount|函式執行計數|計數|總計|函式執行計數|執行個體|
|AppConnections|連接|計數|Average|連接|執行個體|
|處理|控制代碼計數|計數|Average|控制代碼計數|執行個體|
|執行緒|執行緒計數|計數|Average|執行緒計數|執行個體|
|PrivateBytes|私用位元組|位元組|Average|私用位元組|執行個體|
|IoReadBytesPerSecond|每秒的 IO 讀取位元組數|每秒位元組|總計|每秒的 IO 讀取位元組數|執行個體|
|IoWriteBytesPerSecond|每秒的 IO 寫入位元組數|每秒位元組|總計|每秒的 IO 寫入位元組數|執行個體|
|IoOtherBytesPerSecond|每秒的 IO 其他位元組數|每秒位元組|總計|每秒的 IO 其他位元組數|執行個體|
|IoReadOperationsPerSecond|每秒的 IO 讀取作業數|每秒位元組|總計|每秒的 IO 讀取作業數|執行個體|
|IoWriteOperationsPerSecond|每秒的 IO 寫入作業數|每秒位元組|總計|每秒的 IO 寫入作業數|執行個體|
|IoOtherOperationsPerSecond|每秒的 IO 其他作業數|每秒位元組|總計|每秒的 IO 其他作業數|執行個體|
|RequestsInApplicationQueue|應用程式佇列中的要求數|計數|Average|應用程式佇列中的要求數|執行個體|
|CurrentAssemblies|目前的組件|計數|Average|目前的組件|執行個體|
|TotalAppDomains|應用程式網域總計|計數|Average|應用程式網域總計|執行個體|
|TotalAppDomainsUnloaded|已卸載的應用程式網域總計|計數|Average|已卸載的應用程式網域總計|執行個體|
|Gen0Collections|Gen 0 記憶體回收|計數|總計|Gen 0 記憶體回收|執行個體|
|Gen1Collections|Gen 1 記憶體回收|計數|總計|Gen 1 記憶體回收|執行個體|
|Gen2Collections|Gen 2 記憶體回收|計數|總計|Gen 2 記憶體回收|執行個體|
|HealthCheckStatus|健全狀況檢查狀態|計數|Average|健全狀況檢查狀態|執行個體|
|FileSystemUsage|檔案系統使用方式|位元組|Average|檔案系統使用方式|無|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Requests|Requests|計數|總計|Requests|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http101|Http 101|計數|總計|Http 101|執行個體|
|Http2xx|Http 2xx|計數|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|計數|總計|Http 3xx|執行個體|
|Http401|Http 401|計數|總計|Http 401|執行個體|
|Http403|Http 403|計數|總計|Http 403|執行個體|
|Http404|Http 404|計數|總計|Http 404|執行個體|
|Http406|Http 406|計數|總計|Http 406|執行個體|
|Http4xx|Http 4xx|計數|總計|Http 4xx|執行個體|
|Http5xx|Http 伺服器錯誤|計數|總計|Http 伺服器錯誤|執行個體|
|AverageResponseTime|平均回應時間|秒|Average|平均回應時間|執行個體|
|CpuPercentage|CPU 百分比|百分比|Average|CPU 百分比|執行個體|
|MemoryPercentage|記憶體百分比|百分比|Average|記憶體百分比|執行個體|
|DiskQueueLength|磁碟佇列長度|計數|Average|磁碟佇列長度|執行個體|
|HttpQueueLength|Http 佇列長度|計數|Average|Http 佇列長度|執行個體|
|ActiveRequests|使用中的要求|計數|總計|使用中的要求|執行個體|
|TotalFrontEnds|前端總數|計數|Average|前端總數|無|
|SmallAppServicePlanInstances|小型 App Service 方案背景工作角色|計數|Average|小型 App Service 方案背景工作角色|無|
|MediumAppServicePlanInstances|中型 App Service 方案背景工作角色|計數|Average|中型 App Service 方案背景工作角色|無|
|LargeAppServicePlanInstances|大型 App Service 方案背景工作角色|計數|Average|大型 App Service 方案背景工作角色|無|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|WorkersTotal|背景工作角色總數|計數|Average|背景工作角色總數|無|
|WorkersAvailable|可用的背景工作角色|計數|Average|可用的背景工作角色|無|
|WorkersUsed|已使用的背景工作角色|計數|Average|已使用的背景工作角色|無|
|CpuPercentage|CPU 百分比|百分比|Average|CPU 百分比|執行個體|
|MemoryPercentage|記憶體百分比|百分比|Average|記憶體百分比|執行個體|
## <a name="next-steps"></a>後續步驟
* [了解 Azure 監視器中的計量](data-platform.md)
* [建立計量警示](alerts-overview.md)
* [將計量匯出至儲存體、事件中樞或 Log Analytics](platform-logs-overview.md)

