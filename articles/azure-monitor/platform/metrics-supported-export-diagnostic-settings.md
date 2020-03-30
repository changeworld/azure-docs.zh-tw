---
title: Azure 監視器平臺指標可通過診斷設置匯出
description: AAzure 監視器每一個資源類型的可用計量清單。
services: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
ms.subservice: metrics
ms.openlocfilehash: 7a75655d1707dd2491065974ed8addc4c2da1a6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661357"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure 監視器平臺指標可通過診斷設置匯出

預設情況下，Azure 監視器提供[平臺指標](data-platform-metrics.md)，無需配置。 它提供了幾種與平臺指標進行交互的方法，包括在門戶中繪製圖表、通過 REST API 訪問它們或使用 PowerShell 或 CLI 查詢它們。 有關 Azure 監視器的整合指標管道當前可用的平臺指標的完整清單，請參閱[支援指標](metrics-supported.md)。 要查詢和訪問這些指標，請使用[2018-01-01 api 版本](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)。 其他計量可在入口網站中或使用舊版 API 提供。

您可以通過以下兩種方式之一將平臺指標從 Azure 監視器管道匯出到其他位置。
1. 使用[診斷設置](diagnostic-settings.md)發送到日誌分析、事件中心或 Azure 存儲。
2. 使用[指標 REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

由於 Azure 監視器後端的複雜性，並非所有指標都可以使用診斷設置進行匯出。 下表列出了可以使用診斷設置匯出和不能匯出的內容。

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>更改為 NUL 和零值的行為 
 
對於可通過診斷設置匯出的平臺指標，Azure 監視器將"0s"解釋為"Nulls"的一些指標。 這在實際的"0"（由資源發出）和解釋"0"（Nulls）之間造成了一些混淆。 從**2020 年 4 月 1**日起，通過診斷設置匯出的平臺指標將不再匯出"0s"，除非它們確實由基礎資源發出。 請注意：

1.  如果刪除資源組或特定資源，則來自受影響資源的指標資料將不再發送到診斷設置匯出目標。 也就是說，它將不再出現在事件中心、存儲帳戶和日誌分析工作區中。
2.  此改進將在所有公共雲和私有雲中提供。
3.  此更改不會影響以下任何體驗的行為： 
   - 通過診斷設置匯出的平臺資源日誌
   - 指標資源管理器中的指標圖表
   - 在平臺指標上發出警報
 
## <a name="metrics-exportable-table"></a>指標可匯出表 

此資料表包含下列欄位。 
- 可通過診斷設置匯出？ 
- 受 Null / 0 影響 
- ResourceType 
- 計量 
- 指標顯示名稱
- 單位 
- AggregationType


> [!NOTE]
> 下表底部可能有一個水準捲軸。 如果您認為缺少資訊，請檢查捲軸是否一直向左。  


| 可通過診斷設置匯出？  | 發射 NUL |  ResourceType  |  計量  |  指標顯示名稱  |  單位  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| 是]  | 否 |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  記憶體︰清除工具目前價格  |  Count  |  Average | 
| 是]  | 否 |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  記憶體︰不可壓縮的清除器記憶體  |  位元組  |  Average | 
| 是]  | 否 |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  記憶體︰可壓縮的清除器記憶體  |  位元組  |  Average | 
| 是]  | 否 |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  執行緒︰命令集區的忙碌執行緒數  |  Count  |  Average | 
| 是]  | 否 |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  執行緒︰命令集區的閒置執行緒數  |  Count  |  Average | 
| 是]  | 否 |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  命令集區作業佇列長度  |  Count  |  Average | 
| 是]  | 否 |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  連接：目前的連接  |  Count  |  Average | 
| 是]  | 否 |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  目前的使用者工作階段  |  Count  |  Average | 
| 是]  | 否 |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  執行緒︰完整剖析的忙碌執行緒數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  執行緒︰完整剖析的閒置執行緒數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  執行緒︰完整剖析的作業佇列長度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  M 引擎記憶體  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  M 引擎專用位元組  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  M 引擎 QPU  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  M 引擎虛擬位元組  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  memory_metric  |  記憶體  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  記憶體猛移  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  記憶體︰固定記憶體限制  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  記憶體︰記憶體上限  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  記憶體︰記憶體下限  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  記憶體︰記憶體限制 VertiPaq  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  記憶體：記憶體使用量  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  私用位元組  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  執行緒︰處理集區的忙碌 I/O 作業執行緒數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  執行緒︰處理集區的忙碌非 I/O 執行緒數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  執行緒︰處理集區的閒置 I/O 作業執行緒數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  執行緒︰處理集區的閒置非 I/O 執行緒數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  執行緒：處理集區 I/O 作業佇列長度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  處理集區作業佇列長度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  查詢集區忙碌執行緒  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  執行緒︰查詢集區的閒置執行緒數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  執行緒︰查詢集區的作業佇列長度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  Quota  |  記憶體︰配額  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  記憶體︰封鎖的配額  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  處理︰每秒轉換的資料列  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  處理︰每秒讀取的資料列  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  處理︰每秒寫入的資料列  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  執行緒︰簡短剖析的忙碌執行緒數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  執行緒︰簡短剖析的閒置執行緒數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  執行緒︰簡短剖析的作業佇列長度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  每秒連線成功的次數  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  連線失敗的總計  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  連線要求的總計  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  記憶體︰未分頁的 VertiPaq  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  記憶體︰分頁的 VertiPaq  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  虛擬位元組  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  後端持續時間  |  後端請求的持續時間  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.ApiManagement/service  |  Capacity  |  Capacity  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  Duration  |  閘道要求的整體持續期間  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  事件Hub放棄事件  |  丟棄的事件中心事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  事件Hub拒絕事件  |  已拒絕的事件中心事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  活動中心成功活動  |  成功事件中心事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  事件HubHubd事件  |  限制事件中心事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  事件HubTimedout事件  |  超時事件中心事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  事件中心總位元組已發送  |  事件中心事件的大小  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  活動中心總活動  |  事件中心事件總數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  事件Hub完全失敗事件  |  失敗的事件中心事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  FailedRequests  |  失敗的閘道請求（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  OtherRequests  |  其他閘道請求（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  Requests  |  Requests  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  成功的閘道請求（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  TotalRequests  |  閘道請求總數（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  未經授權的閘道請求（已棄用）  |  Count  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  應用Cpu使用百分比  |  應用 CPU 使用率  |  百分比  |  Average | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  應用記憶體已提交  |  應用記憶體分配  |  位元組  |  Average | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  應用程式記憶體最大值  |  應用記憶體最大值  |  位元組  |  最大值 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  應用記憶體已使用  |  使用的應用程式記憶體  |  位元組  |  Average | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  GC暫停總計數  |  GC 暫停計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  GC暫停總時間  |  GC 暫停總時間  |  毫秒  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  最大記憶池位元組  |  最大可用舊代資料大小  |  位元組  |  Average | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  舊記憶體池位元組  |  舊代資料大小  |  位元組  |  Average | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  舊基因提升位元組  |  提升為舊代資料大小  |  位元組  |  最大值 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  系統 Cpu 使用百分比  |  系統 CPU 使用率  |  百分比  |  Average | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特錯誤計數  |  湯姆卡特全域錯誤  |  Count  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特接收位元組  |  湯姆卡特總接收位元組  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特請求最大時間  |  湯姆卡特請求最大時間  |  毫秒  |  最大值 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特請求總數  |  湯姆卡特請求總數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特請求總時間  |  湯姆卡特請求總時間  |  毫秒  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特回應Avg時間  |  湯姆卡特請求平均時間  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  托姆卡特森特斯  |  湯姆卡特總傳送的位元組  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特會話活動電流Count  |  Tomcat 會話活動計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特會話活動最大值Count  |  Tomcat 會話 最大活動計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特會話活動最大時間  |  Tomcat 會話最大啟用時間  |  毫秒  |  最大值 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特會話創建計數  |  Tomcat 會話創建計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特會話過期計數  |  Tomcat 會話過期計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  湯姆卡特會話拒絕計數  |  Tomcat 會話已拒絕計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.AppPlatform/春天  |  楊根升點位元組  |  提升為年輕一代資料大小  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Automation/automationAccounts  |  TotalJob  |  工作總數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Automation/automationAccounts  |  總更新部署電腦運行  |  更新部署機器執行總計  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Automation/automationAccounts  |  總更新部署運行  |  更新部署執行總計  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  CoreCount  |  專用核心計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  建立節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  閒置的節點計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  作業刪除完成事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  作業刪除啟動事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  作業停用完成事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  作業停用啟動事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  作業啟動事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  作業終止完成事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  作業終止啟動事件  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  離開集區節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority 核心計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  離線節點計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  集區建立事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  集區刪除完成事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  集區刪除啟動事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  調整集區大小完成事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  調整集區大小啟動事件  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  先占節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  重新啟動節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  重新安裝映像節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  執行節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  啟動節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  啟動工作失敗的節點計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  工作完成事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  工作失敗事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  工作啟動的事件  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  低優先順序節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  專用節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  無法使用的節點計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  等候啟動工作節點計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  活動核心  |  活動核心  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  活動節點  |  活動節點  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  空閒核心  |  空閒核心  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  空閒節點  |  空閒節點  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  已完成作業  |  已完成作業  |  Count  |  總計 | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  提交作業  |  提交作業  |  Count  |  總計 | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  離開核心  |  離開核心  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  離開節點  |  離開節點  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  搶佔型核心  |  搶佔型核心  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  搶佔節點  |  搶佔節點  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  配額利用率百分比  |  配額利用率百分比  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  核心總數  |  核心總數  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  節點總數  |  節點總數  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  不可用的內核  |  不可用的內核  |  Count  |  Average | 
| **是**  | 否 |  微軟.BatchAI/工作區  |  無法使用的節點  |  無法使用的節點  |  Count  |  Average | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  連接已接受  |  已接受連接  |  Count  |  總計 | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  連接活動  |  作用中的連線  |  Count  |  Average | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  連接處理  |  已處理的連接  |  Count  |  總計 | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  Cpu 使用百分比雙  |  CPU 使用率  |  百分比  |  最大值 | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  IORead 位元組  |  IO 讀取位元組  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  IOWrite 位元組  |  IO 寫入位元組  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  MemoryLimit  |  記憶體限制  |  位元組  |  Average | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  MemoryUsage  |  記憶體使用量  |  位元組  |  Average | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  記憶體使用百分比雙  |  記憶體使用百分比  |  百分比  |  Average | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  待定交易  |  待處理事務  |  Count  |  Average | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  已處理的塊  |  已處理的塊  |  Count  |  總計 | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  已處理的交易  |  已處理的交易  |  Count  |  總計 | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  排隊的事務  |  排隊的事務  |  Count  |  Average | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  請求處理  |  已處理的請求  |  Count  |  總計 | 
| **是**  | 否 |  微軟.區塊鏈/區塊鏈成員  |  存儲使用  |  存儲使用方式  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits  |  快取點擊  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits0  |  快取點擊 (分區 0)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits1  |  快取點擊 (分區 1)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits2  |  快取點擊 (分區 2)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits3  |  快取點擊 (分區 3)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits4  |  快取點擊 (分區 4)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits5  |  快取點擊 (分區 5)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits6  |  快取點擊 (分區 6)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits7  |  快取點擊 (分區 7)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits8  |  快取點擊 (分區 8)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachehits9  |  快取點擊 (分區 9)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheLatency  |  快取延遲 (毫秒) (預覽)  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses  |  快取遺漏  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses0  |  快取遺漏 (分區 0)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses1  |  快取遺漏 (分區 1)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses2  |  快取遺漏 (分區 2)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses3  |  快取遺漏 (分區 3)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses4  |  快取遺漏 (分區 4)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses5  |  快取遺漏 (分區 5)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses6  |  快取遺漏 (分區 6)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses7  |  快取遺漏 (分區 7)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses8  |  快取遺漏 (分區 8)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cachemisses9  |  快取遺漏 (分區 9)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead  |  快取讀取  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead0  |  快取讀取 (分區 0)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead1  |  快取讀取 (分區 1)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead2  |  快取讀取 (分區 2)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead3  |  快取讀取 (分區 3)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead4  |  快取讀取 (分區 4)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead5  |  快取讀取 (分區 5)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead6  |  快取讀取 (分區 6)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead7  |  快取讀取 (分區 7)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead8  |  快取讀取 (分區 8)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheRead9  |  快取讀取 (分區 9)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite  |  快取寫入  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite0  |  快取寫入 (分區 0)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite1  |  快取寫入 (分區 1)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite2  |  快取寫入 (分區 2)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite3  |  快取寫入 (分區 3)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite4  |  快取寫入 (分區 4)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite5  |  快取寫入 (分區 5)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite6  |  快取寫入 (分區 6)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite7  |  快取寫入 (分區 7)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite8  |  快取寫入 (分區 8)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  cacheWrite9  |  快取寫入 (分區 9)  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients  |  連線的用戶端  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients0  |  連線的用戶端 (分區 0)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients1  |  連線的用戶端 (分區 1)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients2  |  連線的用戶端 (分區 2)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients3  |  連線的用戶端 (分區 3)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients4  |  連線的用戶端 (分區 4)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients5  |  連線的用戶端 (分區 5)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients6  |  連線的用戶端 (分區 6)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients7  |  連線的用戶端 (分區 7)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients8  |  連線的用戶端 (分區 8)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  connectedclients9  |  連線的用戶端 (分區 9)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  錯誤  |  Errors  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys  |  收回的金鑰  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys0  |  收回的金鑰 (分區 0)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys1  |  收回的金鑰 (分區 1)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys2  |  收回的金鑰 (分區 2)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys3  |  收回的金鑰 (分區 3)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys4  |  收回的金鑰 (分區 4)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys5  |  收回的金鑰 (分區 5)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys6  |  收回的金鑰 (分區 6)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys7  |  收回的金鑰 (分區 7)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys8  |  收回的金鑰 (分區 8)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  evictedkeys9  |  收回的金鑰 (分區 9)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys  |  到期的金鑰  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys0  |  到期的金鑰 (分區 0)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys1  |  到期的金鑰 (分區 1)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys2  |  到期的金鑰 (分區 2)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys3  |  到期的金鑰 (分區 3)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys4  |  到期的金鑰 (分區 4)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys5  |  到期的金鑰 (分區 5)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys6  |  到期的金鑰 (分區 6)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys7  |  到期的金鑰 (分區 7)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys8  |  到期的金鑰 (分區 8)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  expiredkeys9  |  到期的金鑰 (分區 9)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands  |  取得  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands0  |  取得 (分區 0)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands1  |  取得 (分區 1)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands2  |  取得 (分區 2)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands3  |  取得 (分區 3)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands4  |  取得 (分區 4)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands5  |  取得 (分區 5)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands6  |  取得 (分區 6)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands7  |  取得 (分區 7)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands8  |  取得 (分區 8)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  getcommands9  |  取得 (分區 9)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond  |  每秒的作業數  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond0  |  每秒的作業數 (分區 0)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond1  |  每秒的作業數 (分區 1)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond2  |  每秒的作業數 (分區 2)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond3  |  每秒的作業數 (分區 3)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond4  |  每秒的作業數 (分區 4)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond5  |  每秒的作業數 (分區 5)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond6  |  每秒的作業數 (分區 6)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond7  |  每秒的作業數 (分區 7)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond8  |  每秒的作業數 (分區 8)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  operationsPerSecond9  |  每秒的作業數 (分區 9)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime  |  CPU  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (分區 0)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU (分區 1)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU (分區 2)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU (分區 3)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU (分區 4)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU (分區 5)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU (分區 6)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU (分區 7)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU (分區 8)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU (分區 9)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad  |  伺服器負載  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad0  |  伺服器負載 (分區 0)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad1  |  伺服器負載 (分區 1)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad2  |  伺服器負載 (分區 2)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad3  |  伺服器負載 (分區 3)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad4  |  伺服器負載 (分區 4)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad5  |  伺服器負載 (分區 5)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad6  |  伺服器負載 (分區 6)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad7  |  伺服器負載 (分區 7)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad8  |  伺服器負載 (分區 8)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  serverLoad9  |  伺服器負載 (分區 9)  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands  |  集合  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands0  |  設定 (分區 0)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands1  |  設定 (分區 1)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands2  |  設定 (分區 2)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands3  |  設定 (分區 3)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands4  |  設定 (分區 4)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands5  |  設定 (分區 5)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands6  |  設定 (分區 6)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands7  |  設定 (分區 7)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands8  |  設定 (分區 8)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  setcommands9  |  設定 (分區 9)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  總作業數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  總作業數 (分區 0)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  總作業數 (分區 1)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  總作業數 (分區 2)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  總作業數 (分區 3)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  總作業數 (分區 4)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  總作業數 (分區 5)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  總作業數 (分區 6)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  總作業數 (分區 7)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  總作業數 (分區 8)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  總作業數 (分區 9)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys  |  索引鍵總計  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys0  |  金鑰總數 (分區 0)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys1  |  金鑰總數 (分區 1)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys2  |  金鑰總數 (分區 2)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys3  |  金鑰總數 (分區 3)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys4  |  金鑰總數 (分區 4)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys5  |  金鑰總數 (分區 5)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys6  |  金鑰總數 (分區 6)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys7  |  金鑰總數 (分區 7)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys8  |  金鑰總數 (分區 8)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  totalkeys9  |  金鑰總數 (分區 9)  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory  |  已使用的記憶體  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory0  |  已用的記憶體 (分區 0)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory1  |  已用的記憶體 (分區 1)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory2  |  已用的記憶體 (分區 2)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory3  |  已用的記憶體 (分區 3)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory4  |  已用的記憶體 (分區 4)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory5  |  已用的記憶體 (分區 5)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory6  |  已用的記憶體 (分區 6)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory7  |  已用的記憶體 (分區 7)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory8  |  已用的記憶體 (分區 8)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemory9  |  已用的記憶體 (分區 9)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemorypercentage  |  已用的記憶體百分比  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss  |  已用的記憶體 RSS  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss0  |  已用的記憶體 RSS (分區 0)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss1  |  已用的記憶體 RSS (分區 1)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss2  |  已用的記憶體 RSS (分區 2)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss3  |  已用的記憶體 RSS (分區 3)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss4  |  已用的記憶體 RSS (分區 4)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss5  |  已用的記憶體 RSS (分區 5)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss6  |  已用的記憶體 RSS (分區 6)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss7  |  已用的記憶體 RSS (分區 7)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss8  |  已用的記憶體 RSS (分區 8)  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Cache/redis  |  usedmemoryRss9  |  已用的記憶體 RSS (分區 9)  |  位元組  |  最大值 | 
| 否  | 否 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Bytes/Sec  |  磁碟讀取  |  每秒位元組  |  Average | 
| **是**  | 否 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Bytes/Sec  |  磁碟寫入  |  每秒位元組  |  Average | 
| **是**  | 否 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Network In  |  Network In  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Network Out  |  Network Out  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Percentage CPU  |  Percentage CPU  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.ClassicCompute/virtualMachines  |  Disk Read Bytes/Sec  |  磁碟讀取  |  每秒位元組  |  Average | 
| **是**  | 否 |  Microsoft.ClassicCompute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.ClassicCompute/virtualMachines  |  Disk Write Bytes/Sec  |  磁碟寫入  |  每秒位元組  |  Average | 
| **是**  | 否 |  Microsoft.ClassicCompute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.ClassicCompute/virtualMachines  |  Network In  |  Network In  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.ClassicCompute/virtualMachines  |  Network Out  |  Network Out  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.ClassicCompute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.ClassicStorage/storageAccounts  |  可用性  |  可用性  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.ClassicStorage/storageAccounts  |  輸出  |  輸出  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.ClassicStorage/storageAccounts  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.ClassicStorage/storageAccounts  |  交易  |  交易  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  已使用容量  |  位元組  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  可用性  |  可用性  |  百分比  |  Average | 
| 否  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  BlobCapacity  |  Blob 容量  |  位元組  |  Average | 
| 否  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  BlobCount  |  Blob 計數  |  Count  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  ContainerCount  |  Blob 容器計數  |  Count  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  輸出  |  輸出  |  位元組  |  總計 | 
| 否  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  IndexCapacity  |  索引容量  |  位元組  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/blob服務  |  交易  |  交易  |  Count  |  總計 | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  可用性  |  可用性  |  百分比  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  輸出  |  輸出  |  位元組  |  總計 | 
| 否  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  FileCapacity  |  檔案容量  |  位元組  |  Average | 
| 否  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  FileCount  |  檔案計數  |  Count  |  Average | 
| 否  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  FileShareCount  |  檔案共用計數  |  Count  |  Average | 
| 否  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  檔共用配額  |  檔共用配額大小  |  位元組  |  Average | 
| 否  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  檔共用快照計數  |  檔共用快照計數  |  Count  |  Average | 
| 否  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  檔共用快照大小  |  檔共用快照大小  |  位元組  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/檔服務  |  交易  |  交易  |  Count  |  總計 | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/佇列服務  |  可用性  |  可用性  |  百分比  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/佇列服務  |  輸出  |  輸出  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/佇列服務  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/佇列服務  |  QueueCapacity  |  佇列容量  |  位元組  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/佇列服務  |  QueueCount  |  佇列計數  |  Count  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/佇列服務  |  QueueMessageCount  |  佇列訊息計數  |  Count  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/佇列服務  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/佇列服務  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/佇列服務  |  交易  |  交易  |  Count  |  總計 | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/表服務  |  可用性  |  可用性  |  百分比  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/表服務  |  輸出  |  輸出  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/表服務  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/表服務  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/表服務  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/表服務  |  TableCapacity  |  資料表容量  |  位元組  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/表服務  |  TableCount  |  資料表計數  |  Count  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/表服務  |  TableEntityCount  |  資料表實體計數  |  Count  |  Average | 
| **是**  | 否 |  微軟.經典存儲/存儲帳戶/表服務  |  交易  |  交易  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  已封鎖的呼叫  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  字元已訓練  |  已訓練字元  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  轉譯字元數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  用戶端錯誤  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  DataIn  |  資料輸入  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  DataOut  |  資料輸出  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  Latency  |  Latency  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  伺服器錯誤  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  語音工作階段持續時間  |  秒  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  成功的呼叫  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  呼叫總數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  錯誤總數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  呼叫權杖總計  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  交易數總計  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  CPU Credits Consumed  |  CPU Credits Consumed  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  剩餘 CPU 信用額度  |  剩餘 CPU 信用額度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  資料磁碟佇列深度  |  資料磁片佇列深度（預覽）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  資料磁片讀取位元組/秒  |  資料磁碟讀取位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  資料磁片讀取操作/秒  |  資料磁碟讀取作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  資料磁片寫入位元組/秒  |  資料磁碟寫入位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  資料磁片寫入操作/秒  |  資料磁碟寫入作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  Disk Read Bytes  |  Disk Read Bytes  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  連入流量  |  連入流量  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  入站流最大創建速率  |  入站流最大創建速率（預覽）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  Network In  |  網路在計費（已棄用）  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  網路總數  |  網路總數  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  Network Out  |  網路出計費（已棄用）  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  網路出出總計  |  網路出出總計  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  OS 磁碟佇列深度  |  作業系統磁片佇列深度（預覽）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  OS 磁片讀取位元組/秒  |  OS 磁碟讀取位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  作業系統磁片讀取操作/秒  |  OS 磁碟讀取作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  作業系統磁片寫入位元組/秒  |  OS 磁碟寫入位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  作業系統磁片寫入操作/秒  |  OS 磁碟寫入作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  OS 每一磁碟 QD  |  OS 磁片 QD（已棄用）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  OS 每一磁碟讀取位元組/秒  |  OS 磁片讀取位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  OS 每一磁碟讀取作業/秒  |  OS 磁片讀取操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  OS 每一磁碟寫入位元組/秒  |  OS 磁片寫入位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  OS 每一磁碟寫入作業/秒  |  OS 磁片寫入操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  出站流  |  出站流  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  出站流最大創建速率  |  出站流最大創建速率（預覽）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  每一磁碟 QD  |  資料磁片 QD（已棄用）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  每一磁碟讀取位元組/秒  |  資料磁片讀取位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  每一磁碟讀取作業/秒  |  資料磁片讀取操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  每一磁碟寫入位元組/秒  |  資料磁片寫入位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  每一磁碟寫入作業/秒  |  資料磁片寫入操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  高級資料磁片緩存讀取命中  |  高級資料磁片緩存讀取命中（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  高級資料磁片緩存讀取誤區  |  高級資料磁片緩存讀取誤讀（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  高級作業系統磁片緩存讀取命中  |  高級作業系統磁片緩存讀取命中（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachines  |  高級 OS 磁片緩存讀取誤區  |  高級作業系統磁片緩存讀取誤讀（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  CPU Credits Consumed  |  CPU Credits Consumed  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  剩餘 CPU 信用額度  |  剩餘 CPU 信用額度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  資料磁碟佇列深度  |  資料磁片佇列深度（預覽）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  資料磁片讀取位元組/秒  |  資料磁碟讀取位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  資料磁片讀取操作/秒  |  資料磁碟讀取作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  資料磁片寫入位元組/秒  |  資料磁碟寫入位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  資料磁片寫入操作/秒  |  資料磁碟寫入作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Read Bytes  |  Disk Read Bytes  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Write Bytes  |  Disk Write Bytes  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  連入流量  |  連入流量  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  入站流最大創建速率  |  入站流最大創建速率（預覽）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  Network In  |  網路在計費（已棄用）  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  網路總數  |  網路總數  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  Network Out  |  網路出計費（已棄用）  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  網路出出總計  |  網路出出總計  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 磁碟佇列深度  |  作業系統磁片佇列深度（預覽）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 磁片讀取位元組/秒  |  OS 磁碟讀取位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  作業系統磁片讀取操作/秒  |  OS 磁碟讀取作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  作業系統磁片寫入位元組/秒  |  OS 磁碟寫入位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  作業系統磁片寫入操作/秒  |  OS 磁碟寫入作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 每一磁碟 QD  |  OS 磁片 QD（已棄用）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 每一磁碟讀取位元組/秒  |  OS 磁片讀取位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 每一磁碟讀取作業/秒  |  OS 磁片讀取操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 每一磁碟寫入位元組/秒  |  OS 磁片寫入位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  OS 每一磁碟寫入作業/秒  |  OS 磁片寫入操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  出站流  |  出站流  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  出站流最大創建速率  |  出站流最大創建速率（預覽）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  每一磁碟 QD  |  資料磁片 QD（已棄用）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  每一磁碟讀取位元組/秒  |  資料磁片讀取位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  每一磁碟讀取作業/秒  |  資料磁片讀取操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  每一磁碟寫入位元組/秒  |  資料磁片寫入位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  每一磁碟寫入作業/秒  |  資料磁片寫入操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  Percentage CPU  |  Percentage CPU  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  高級資料磁片緩存讀取命中  |  高級資料磁片緩存讀取命中（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  高級資料磁片緩存讀取誤區  |  高級資料磁片緩存讀取誤讀（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  高級作業系統磁片緩存讀取命中  |  高級作業系統磁片緩存讀取命中（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets  |  高級 OS 磁片緩存讀取誤區  |  高級作業系統磁片緩存讀取誤讀（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  CPU Credits Consumed  |  CPU Credits Consumed  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  剩餘 CPU 信用額度  |  剩餘 CPU 信用額度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  資料磁碟佇列深度  |  資料磁片佇列深度（預覽）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  資料磁片讀取位元組/秒  |  資料磁碟讀取位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  資料磁片讀取操作/秒  |  資料磁碟讀取作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  資料磁片寫入位元組/秒  |  資料磁碟寫入位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  資料磁片寫入操作/秒  |  資料磁碟寫入作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Bytes  |  Disk Read Bytes  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  連入流量  |  連入流量  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  入站流最大創建速率  |  入站流最大創建速率（預覽）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Network In  |  網路在計費（已棄用）  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  網路總數  |  網路總數  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Network Out  |  網路出計費（已棄用）  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  網路出出總計  |  網路出出總計  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 磁碟佇列深度  |  作業系統磁片佇列深度（預覽）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 磁片讀取位元組/秒  |  OS 磁碟讀取位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  作業系統磁片讀取操作/秒  |  OS 磁碟讀取作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  作業系統磁片寫入位元組/秒  |  OS 磁碟寫入位元組/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  作業系統磁片寫入操作/秒  |  OS 磁碟寫入作業/秒 (預覽)  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 每一磁碟 QD  |  OS 磁片 QD（已棄用）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 每一磁碟讀取位元組/秒  |  OS 磁片讀取位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 每一磁碟讀取作業/秒  |  OS 磁片讀取操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 每一磁碟寫入位元組/秒  |  OS 磁片寫入位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS 每一磁碟寫入作業/秒  |  OS 磁片寫入操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  出站流  |  出站流  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  出站流最大創建速率  |  出站流最大創建速率（預覽）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  每一磁碟 QD  |  資料磁片 QD（已棄用）  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  每一磁碟讀取位元組/秒  |  資料磁片讀取位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  每一磁碟讀取作業/秒  |  資料磁片讀取操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  每一磁碟寫入位元組/秒  |  資料磁片寫入位元組/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  每一磁碟寫入作業/秒  |  資料磁片寫入操作/秒（已棄用）  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  高級資料磁片緩存讀取命中  |  高級資料磁片緩存讀取命中（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  高級資料磁片緩存讀取誤區  |  高級資料磁片緩存讀取誤讀（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  高級作業系統磁片緩存讀取命中  |  高級作業系統磁片緩存讀取命中（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  高級 OS 磁片緩存讀取誤區  |  高級作業系統磁片緩存讀取誤讀（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  CPU 使用率  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  記憶體使用量  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  每秒接收的網路位元組數  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  每秒傳輸的網路位元組數  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.ContainerRegistry/registries  |  運行持續時間  |  執行持續期間  |  毫秒  |  總計 | 
| **是**  | 否 |  Microsoft.ContainerRegistry/registries  |  成功拉計數  |  成功拉取計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.ContainerRegistry/registries  |  成功推送計數  |  成功推送計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.ContainerRegistry/registries  |  總拉計數  |  總拉取計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.ContainerRegistry/registries  |  總推送計數  |  總推送計數  |  Count  |  Average | 
| 否  | 否 |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  受控叢集中可用的 cpu 核心總數  |  Count  |  Average | 
| 否  | 否 |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  受控叢集中可用的記憶體總量  |  位元組  |  Average | 
| 否  | 否 |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  各種節點條件的狀態  |  Count  |  Average | 
| 否  | 否 |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  各階段的 Pod 數目  |  Count  |  Average | 
| 否  | 否 |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  處於就緒狀態的 Pod 數目  |  Count  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  可用容量  |  可用容量  |  位元組  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  位元組上傳到雲  |  上傳雲位元組（設備）  |  位元組  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  位元組上傳到雲佩爾共用  |  上傳雲位元組（共用）  |  位元組  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  雲讀取輸送量  |  雲下載輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  雲讀取輸送量PerShare  |  雲下載輸送量（共用）  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  雲上傳輸送量  |  雲上傳輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  雲上傳輸送量PerShare  |  雲上傳輸送量（共用）  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  超V記憶體利用  |  邊緣計算 - 記憶體使用  |  百分比  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  超V虛擬處理器利用  |  邊緣計算 - CPU 百分比  |  百分比  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  NICRead輸送量  |  讀取輸送量（網路）  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  NICWrite輸送量  |  寫入輸送量（網路）  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.DataBoxEdge/DataBox邊緣設備  |  總容量  |  總容量  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.DataFactory/datafactories  |  FailedRuns  |  失敗的執行  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns  |  成功的執行  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  活動已取消運行  |  已取消的活動運行指標  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  ActivityFailedRuns  |  失敗的活動執行計量  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  ActivitySucceededRuns  |  成功的活動執行計量  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  工廠尺寸單位  |  工廠總規模（GB 單位）  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAvailableMemory  |  整合執行階段可用記憶體  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  集成運行時平均任務提取延遲  |  集成運行時佇列持續時間  |  秒  |  Average | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  IntegrationRuntimeCpuPercentage  |  整合執行階段 CPU 使用率  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  集成運行時排隊長度  |  集成運行時佇列長度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  最大允許工廠尺寸單位  |  允許的最大出廠規模（GB 單位）  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  最大允許資源計數  |  允許的最大實體計數  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  管道取消運行  |  已取消的管道運行指標  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  PipelineFailedRuns  |  失敗的管線執行計量  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  PipelineSucceededRuns  |  成功的管線執行計量  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  資源計數  |  實體計數總數  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  觸發器取消運行  |  已取消的觸發器運行指標  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  TriggerFailedRuns  |  失敗的觸發程序執行計量  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataFactory/factories  |  TriggerSucceededRuns  |  成功的觸發程序執行計量  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  取消的 AU 時間  |  秒  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  失敗 AU 時間  |  秒  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  成功 AU 時間  |  秒  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  取消的作業  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  失敗的作業  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  成功的作業  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeStore/accounts  |  DataRead  |  讀取的資料  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  寫入的資料  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  讀取要求  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  儲存體總計  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  寫入要求  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  作用中的連線  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  使用的備份存儲  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  失敗的連線  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  CPU 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  IO 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  記憶體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Network Out  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Network In  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  複寫延遲 (秒)  |  Count  |  最大值 | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  伺服器記錄儲存體限制  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  伺服器記錄儲存體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  使用的伺服器記錄儲存體  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  儲存體限制  |  位元組  |  最大值 | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  儲存體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  已使用儲存體  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  active_connections  |  作用中的連線  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  使用的備份存儲  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  失敗的連線  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  CPU 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  IO 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  記憶體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Network Out  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Network In  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  複寫延遲 (秒)  |  Count  |  最大值 | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  伺服器記錄儲存體限制  |  位元組  |  最大值 | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  伺服器記錄儲存體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  使用的伺服器記錄儲存體  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  儲存體限制  |  位元組  |  最大值 | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  儲存體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DBforMySQL/servers  |  storage_used  |  已使用儲存體  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  作用中的連線  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  使用的備份存儲  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  失敗的連線  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  CPU 百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  IO 百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  記憶體百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Network Out  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Network In  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  複本之間的最大延隔時間  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  複本延隔時間  |  秒  |  最大值 | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  伺服器記錄儲存體限制  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  伺服器記錄儲存體百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  使用的伺服器記錄儲存體  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  儲存體限制  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  儲存體百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  已使用儲存體  |  位元組  |  Average | 
| **是**  | 否 |  微軟.DBforPostgreSQL/伺服器2  |  active_connections  |  作用中的連線  |  Count  |  Average | 
| **是**  | 否 |  微軟.DBforPostgreSQL/伺服器2  |  cpu_percent  |  CPU 百分比  |  百分比  |  Average | 
| **是**  | 否 |  微軟.DBforPostgreSQL/伺服器2  |  伊奧普斯  |  IOPS  |  Count  |  Average | 
| **是**  | 否 |  微軟.DBforPostgreSQL/伺服器2  |  memory_percent  |  記憶體百分比  |  百分比  |  Average | 
| **是**  | 否 |  微軟.DBforPostgreSQL/伺服器2  |  network_bytes_egress  |  Network Out  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.DBforPostgreSQL/伺服器2  |  network_bytes_ingress  |  Network In  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.DBforPostgreSQL/伺服器2  |  storage_percent  |  儲存體百分比  |  百分比  |  Average | 
| **是**  | 否 |  微軟.DBforPostgreSQL/伺服器2  |  storage_used  |  已使用儲存體  |  位元組  |  Average | 
| **是**  | **是** |  微軟.設備/帳戶  |  數位雙胞胎.遙測.節點  |  數位雙胞胎節點遙測預留位置  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  C2D 消息已放棄  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  已完成 C2D 消息傳遞  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  C2D 郵件被拒絕  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  失敗直接方法叫用  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  直接方法叫用的要求大小  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  直接方法叫用的回應大小  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  成功直接方法叫用  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  後端的失敗對應項讀取  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  後端的對應項讀取回應大小  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  後端的成功對應項讀取  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  後端的失敗對應項更新  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  後端的對應項更新大小  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  後端的成功對應項更新  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  C2D 消息過期  |  C2D 郵件已過期（預覽）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  組態  |  設定計量  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  連接的裝置 (預覽)  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  路由：傳遞至訊息/事件的訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  路由：傳遞至事件中樞的訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  路由：傳遞至服務匯流排佇列的訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  路由：傳遞至服務匯流排主題的訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  路由：傳遞至儲存體的訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  路由：傳遞至儲存體的 BLOB  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  路由：傳遞至儲存體的資料  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  路由：訊息/事件的訊息延遲  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  路由：事件中樞的訊息延遲  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  路由：服務匯流排佇列的訊息延遲  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  路由：服務匯流排主題的訊息延遲  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  路由：儲存體的訊息延遲  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  路由：已捨棄的遙測訊息   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  路由：傳遞至後援的訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  路由：不相容的遙測訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  路由：已遺棄的遙測訊息   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  路由：已傳遞的遙測訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  遙測訊息傳送嘗試  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  節流錯誤數目  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  已傳送的遙測訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  裝置的失敗對應項讀取  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  裝置的對應項讀取回應大小  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  裝置的成功對應項讀取  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  裝置的失敗對應項更新  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  裝置的對應項更新大小  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  裝置的成功對應項更新  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  已使用的訊息總數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  設備資料使用總量  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  裝置資料使用量總計 (預覽)  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  連接的裝置 (已淘汰)   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  裝置總計 (已淘汰)  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  事件網格交付  |  事件網格交付（預覽）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  事件網格延遲  |  事件網格延遲（預覽）  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  取消作業失敗  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  成功取消作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.completed  |  已完成的工作  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  建立失敗的方法叫用作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  成功建立的方法叫用作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  建立失敗的對應項更新作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  成功建立的對應項更新作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  失敗作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  呼叫列出作業失敗  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  成功呼叫列出作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  失敗作業查詢  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  成功作業查詢  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  裝置總計 (預覽)  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  失敗對應項查詢  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  對應項查詢結果大小  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  成功對應項查詢  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  證明嘗試數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  已指派的裝置數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  註冊嘗試數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  可用的儲存體  |  位元組  |  總計 | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Cassandra 連線終止  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Cassandra 要求費用  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Cassandra 要求  |  Count  |  Count | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  資料使用量  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  刪除虛擬網路  |  刪除虛擬網路  |  Count  |  Count | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  文件計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  文件配額  |  位元組  |  總計 | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  索引使用量  |  位元組  |  總計 | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  中繼資料要求  |  Count  |  Count | 
| **是**  | **是** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Mongo 要求收費  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Mongo 要求  |  Count  |  Count | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  蒙戈請求計數  |  蒙戈請求率  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  蒙戈請求刪除  |  蒙戈刪除請求率  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  蒙戈請求插入  |  蒙戈插入請求率  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  蒙戈請求查詢  |  蒙戈查詢請求率  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  蒙戈請求更新  |  蒙戈更新要求率  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  佈建的輸送量  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  P99 複寫延遲  |  毫秒  |  Average | 
| 否  | 否 |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  服務可用性  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  要求總數  |  Count  |  Count | 
| **是**  | **是** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  要求單位總計  |  Count  |  總計 | 
| 否  | 否 |  微軟.企業知識圖/服務  |  故障計數  |  失敗計數  |  Count  |  Count | 
| 否  | 否 |  微軟.企業知識圖/服務  |  SuccessCount  |  成功計數  |  Count  |  Count | 
| 否  | 否 |  微軟.企業知識圖/服務  |  成功延遲  |  成功延遲  |  毫秒  |  Average | 
| 否  | 否 |  微軟.企業知識圖/服務  |  事務計數  |  事務計數  |  Count  |  Count | 
| **是**  | **是** |  微軟.事件網格/域  |  DeadLetteredCount  |  失效信件事件  |  Count  |  總計 | 
| 否  | 否 |  微軟.事件網格/域  |  DeliveryAttemptFailCount  |  傳遞失敗的事件  |  Count  |  總計 | 
| **是**  | **是** |  微軟.事件網格/域  |  DeliverySuccessCount  |  已傳遞的事件  |  Count  |  總計 | 
| 否  | 否 |  微軟.事件網格/域  |  DestinationProcessingDurationInMs  |  目的端處理持續期間  |  毫秒  |  Average | 
| **是**  | **是** |  微軟.事件網格/域  |  DroppedEventCount  |  卸除的事件  |  Count  |  總計 | 
| **是**  | **是** |  微軟.事件網格/域  |  MatchedEventCount  |  相符的事件  |  Count  |  總計 | 
| **是**  | **是** |  微軟.事件網格/域  |  PublishFailCount  |  發佈失敗事件  |  Count  |  總計 | 
| **是**  | **是** |  微軟.事件網格/域  |  PublishSuccessCount  |  已發佈的事件  |  Count  |  總計 | 
| **是**  | **是** |  微軟.事件網格/域  |  發佈成功延遲在M中  |  發佈成功延遲  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  失效信件事件  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  傳遞失敗的事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  已傳遞的事件  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  目的端處理持續期間  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  卸除的事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  相符的事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  發佈失敗事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  已發佈的事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/extensionTopics  |  發佈成功延遲在M中  |  發佈成功延遲  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  不相符的事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  發佈失敗事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  已發佈的事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/topics  |  發佈成功延遲在M中  |  發佈成功延遲  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventGrid/topics  |  UnmatchedEventCount  |  不相符的事件  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Count  |  Average | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  AvailableMemory  |  可用的記憶體  |  百分比  |  最大值 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  擷取待辦項目。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  CapturedBytes  |  已擷取的位元組。  |  位元組  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  CapturedMessages  |  已擷取的訊息。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  ConnectionsClosed  |  已關閉的連線。  |  Count  |  Average | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  已開啟的連線。  |  Count  |  Average | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  CPU  |  CPU  |  百分比  |  最大值 | 
| **是**  | **是** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  傳入位元組數。  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  傳入訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  傳入的要求  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  傳出位元組數。  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  外送訊息  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  超出配額的錯誤。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  ServerErrors  |  伺服器錯誤。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  成功的要求  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  節流的要求。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/clusters  |  UserErrors  |  使用者錯誤。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Average | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  擷取待辦項目。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  已擷取的位元組。  |  位元組  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  已擷取的訊息。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  ConnectionsClosed  |  已關閉的連線。  |  Count  |  Average | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  已開啟的連線。  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  EHABL  |  存檔積壓工作郵件（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  存檔消息輸送量（已棄用）  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  存檔消息（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  傳入位元組數 (已過時)  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  傳入位元組（已過時）（已棄用）  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  連入訊息數 (已過時)  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  傳出位元組數 (已過時)  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  傳出位元組（過時）（已棄用）  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  傳出訊息數 (已過時)  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  失敗的請求（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  傳入位元組數。  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  傳入訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  傳入的要求  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  INMSGS  |  傳入消息（已過時）（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  INREQS  |  傳入請求（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  INTERR  |  內部伺服器錯誤（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  MISCERR  |  其他錯誤（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  傳出位元組數。  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  外送訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  傳出消息（已過時）（已棄用）  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  超出配額的錯誤。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  ServerErrors  |  伺服器錯誤。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  大小  |  大小  |  位元組  |  Average | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  成功的要求  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  成功請求（已棄用）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  伺服器忙錯誤（已棄用）  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  節流的要求。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.EventHub/namespaces  |  UserErrors  |  使用者錯誤。  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  已分類的閘道要求數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  閘道要求數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.HDInsight/clusters  |  數位活動工作人員  |  活動工作人員數  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.HDInsight/clusters  |  縮放請求  |  縮放請求  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  計量閾值  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  觀察的容量  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  觀察的計量值  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  已起始的調整規模動作數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Insights/Components  |  可用性 結果/可用性百分比  |  可用性  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Insights/Components  |  可用性 結果/計數  |  可用性集合  |  Count  |  Count | 
| **是**  | 否 |  Microsoft.Insights/Components  |  availabilityResults/duration  |  可用性測試持續時間  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  頁面載入網路連線時間  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  用戶端處理時間  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  接收回應時間  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  傳送要求時間  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  瀏覽器頁面載入時間  |  毫秒  |  Average | 
| 否  | 否 |  Microsoft.Insights/Components  |  dependencies/count  |  相依性呼叫  |  Count  |  Count | 
| **是**  | 否 |  Microsoft.Insights/Components  |  dependencies/duration  |  相依性持續時間  |  毫秒  |  Average | 
| 否  | 否 |  Microsoft.Insights/Components  |  dependencies/failed  |  依賴項調用失敗  |  Count  |  Count | 
| 否  | 否 |  Microsoft.Insights/Components  |  exceptions/browser  |  瀏覽器例外狀況  |  Count  |  Count | 
| **是**  | **是** |  Microsoft.Insights/Components  |  exceptions/count  |  例外狀況  |  Count  |  Count | 
| 否  | 否 |  Microsoft.Insights/Components  |  exceptions/server  |  伺服器例外狀況  |  Count  |  Count | 
| **是**  | **是** |  Microsoft.Insights/Components  |  pageViews/count  |  頁面檢視  |  Count  |  Count | 
| **是**  | 否 |  Microsoft.Insights/Components  |  pageViews/duration  |  頁面檢視載入時間  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  例外狀況比率  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  可用的記憶體  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  處理程序 CPU  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  處理程序 IO 速率  |  每秒位元組  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  處理器時間  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  處理程序私人位元組  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  HTTP 要求執行時間  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  應用程式佇列中的 HTTP 要求數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  HTTP 要求率  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.Insights/Components  |  requests/count  |  伺服器要求  |  Count  |  Count | 
| **是**  | 否 |  Microsoft.Insights/Components  |  requests/duration  |  伺服器回應時間  |  毫秒  |  Average | 
| 否  | 否 |  Microsoft.Insights/Components  |  requests/failed  |  失敗的要求  |  Count  |  Count | 
| 否  | 否 |  Microsoft.Insights/Components  |  請求/速率  |  伺服器請求率  |  每秒計數  |  Average | 
| **是**  | **是** |  Microsoft.Insights/Components  |  traces/count  |  追蹤  |  Count  |  Count | 
| **是**  | 否 |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  服務 API 點擊次數總計  |  Count  |  Count | 
| **是**  | 否 |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  整體服務 API 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  服務 API 結果總計  |  Count  |  Count | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  緩存利用率  |  緩存利用率  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  連續出口最大完成分鐘數  |  連續匯出最大延遲分鐘數  |  Count  |  最大值 | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  連續匯出記錄  |  連續匯出 - 匯出記錄數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  連續匯出掛起計數  |  連續匯出掛起計數  |  Count  |  最大值 | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  連續匯出結果  |  連續匯出結果  |  Count  |  Count | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  CPU  |  CPU  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  為事件中心處理的事件  |  處理的事件（對於事件/IoT 中心）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  匯出利用率  |  出口利用率  |  百分比  |  最大值 | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  攝入延遲以秒為單位  |  攝入延遲（以秒為單位）  |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  攝取結果  |  Count  |  Count | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  攝入利用率  |  攝入利用率  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  攝入體積  |  攝入體積（以 MB）  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  保持活力  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  查詢持續時間  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  蒸化請求率  |  資料流引入請求速率  |  Count  |  速率請求每秒 | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  資料流資料速率  |  資料流引入資料速率  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  流式處理持續時間  |  資料流引入持續時間  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Kusto/Clusters  |  流式處理結果  |  資料流引入結果  |  Count  |  Average | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  ActionLatency  |  動作延遲   |  秒  |  Average | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  ActionsCompleted  |  完成的動作   |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  ActionsFailed  |  動作的失敗   |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  ActionsSkipped  |  略過的動作   |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  ActionsStarted  |  啟動的動作   |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  ActionsSucceeded  |  成功的動作   |  Count  |  總計 | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  ActionSuccessLatency  |  動作成功延遲   |  秒  |  Average | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  ActionThrottledEvents  |  動作節流的事件  |  Count  |  總計 | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  整合服務環境連接器記憶體使用  |  整合服務環境的連接器記憶體使用方式  |  百分比  |  Average | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  整合服務環境連接器處理器使用  |  整合服務環境的連接器處理器使用方式  |  百分比  |  Average | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  整合服務環境工作流記憶體使用  |  整合服務環境的工作流記憶體使用方式  |  百分比  |  Average | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  整合服務環境工作流處理器使用  |  整合服務環境的工作流處理器使用方式  |  百分比  |  Average | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  RunFailurePercentage  |  執行失敗百分比  |  百分比  |  總計 | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  RunLatency  |  執行延遲  |  秒  |  Average | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  RunsCancelled  |  運行已取消  |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  RunsCompleted  |  完成的執行  |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  RunsFailed  |  失敗的執行  |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  RunsStarted  |  執行已啟動  |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  RunsSucceeded  |  已成功的執行  |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  運行啟動限制事件  |  運行啟動限制事件  |  Count  |  總計 | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  RunSuccessLatency  |  執行成功的延遲  |  秒  |  Average | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  RunThrottledEvents  |  執行節流的事件  |  Count  |  總計 | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  TriggerFireLatency  |  觸發程序引發延遲   |  秒  |  Average | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  TriggerLatency  |  觸發程序延遲   |  秒  |  Average | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  TriggersCompleted  |  完成的觸發程序   |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  TriggersFailed  |  失敗的觸發程序   |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  TriggersFired  |  引發的觸發程序   |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  TriggersSkipped  |  略過的觸發程序  |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  TriggersStarted  |  啟動的觸發程序   |  Count  |  總計 | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  TriggersSucceeded  |  成功的觸發程序   |  Count  |  總計 | 
| **是**  | 否 |  微軟.邏輯/整合服務環境  |  TriggerSuccessLatency  |  觸發程序成功延遲   |  秒  |  Average | 
| **是**  | **是** |  微軟.邏輯/整合服務環境  |  TriggerThrottledEvents  |  觸發程序節流的事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Logic/workflows  |  ActionLatency  |  動作延遲   |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  完成的動作   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  ActionsFailed  |  動作的失敗   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  略過的動作   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  ActionsStarted  |  啟動的動作   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  成功的動作   |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  動作成功延遲   |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  動作節流的事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  可計費的動作執行  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  可計費的觸發程序執行  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  為使用原生作業執行計費  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  為使用原生作業執行計費  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  為使用標準連接器執行計費  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  為使用標準連接器執行計費  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  為使用儲存體使用量執行計費  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  為使用儲存體使用量執行計費  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  執行失敗百分比  |  百分比  |  總計 | 
| **是**  | 否 |  Microsoft.Logic/workflows  |  RunLatency  |  執行延遲  |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  RunsCancelled  |  運行已取消  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  RunsCompleted  |  完成的執行  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  RunsFailed  |  失敗的執行  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  RunsStarted  |  執行已啟動  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  已成功的執行  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  運行啟動限制事件  |  運行啟動限制事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  執行成功的延遲  |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  執行節流的事件  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  可計費的總執行次數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  觸發程序引發延遲   |  秒  |  Average | 
| **是**  | 否 |  Microsoft.Logic/workflows  |  TriggerLatency  |  觸發程序延遲   |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  完成的觸發程序   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  TriggersFailed  |  失敗的觸發程序   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  TriggersFired  |  引發的觸發程序   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  略過的觸發程序  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  TriggersStarted  |  啟動的觸發程序   |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  成功的觸發程序   |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  觸發程序成功延遲   |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  觸發程序節流的事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  活動核心  |  活動核心  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  活動節點  |  活動節點  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  已完成運行  |  已完成運行  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  失敗的執行  |  失敗的執行  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  空閒核心  |  空閒核心  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  空閒節點  |  空閒節點  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  離開核心  |  離開核心  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  離開節點  |  離開節點  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  模型部署失敗  |  模型部署失敗  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  模型部署已啟動  |  模型部署已啟動  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  模型部署成功  |  模型部署成功  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  模型註冊失敗  |  模型註冊失敗  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  模型註冊成功  |  模型註冊成功  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  搶佔型核心  |  搶佔型核心  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  搶佔節點  |  搶佔節點  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  配額利用率百分比  |  配額利用率百分比  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  已啟動運行  |  已啟動運行  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  核心總數  |  核心總數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  節點總數  |  節點總數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  不可用的內核  |  不可用的內核  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.MachineLearningServices/workspaces  |  無法使用的節點  |  無法使用的節點  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Maps/accounts  |  可用性  |  可用性  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Maps/accounts  |  使用量  |  使用量  |  Count  |  Count | 
| **是**  | 否 |  微軟.媒體/媒體服務  |  資產計數  |  資產計數  |  Count  |  Average | 
| **是**  | 否 |  微軟.媒體/媒體服務  |  資產配額  |  資產配額  |  Count  |  Average | 
| **是**  | 否 |  微軟.媒體/媒體服務  |  資產配額使用百分比  |  資產配額使用百分比  |  百分比  |  Average | 
| **是**  | 否 |  微軟.媒體/媒體服務  |  內容關鍵策略計數  |  內容金鑰策略計數  |  Count  |  Average | 
| **是**  | 否 |  微軟.媒體/媒體服務  |  內容關鍵策略配額  |  內容關鍵策略配額  |  Count  |  Average | 
| **是**  | 否 |  微軟.媒體/媒體服務  |  內容關鍵策略配額使用百分比  |  內容 關鍵策略配額使用百分比  |  百分比  |  Average | 
| **是**  | 否 |  微軟.媒體/媒體服務  |  流式處理策略計數  |  流策略計數  |  Count  |  Average | 
| **是**  | 否 |  微軟.媒體/媒體服務  |  流式處理策略配額  |  流式處理策略配額  |  Count  |  Average | 
| **是**  | 否 |  微軟.媒體/媒體服務  |  流式處理策略配額使用百分比  |  流策略配額使用百分比  |  百分比  |  Average | 
| **是**  | 否 |  微軟.媒體/媒體服務/流媒體終端  |  輸出  |  輸出  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.媒體/媒體服務/流媒體終端  |  Requests  |  Requests  |  Count  |  總計 | 
| **是**  | 否 |  微軟.媒體/媒體服務/流媒體終端  |  SuccessE2ELatency  |  成功端到端延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  GC暫停總計數  |  GC 暫停計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  GC暫停總時間  |  GC 暫停總時間  |  毫秒  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  最大記憶池位元組  |  最大可用舊代資料大小  |  位元組  |  Average | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  舊記憶體池位元組  |  舊代資料大小  |  位元組  |  Average | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  舊基因提升位元組  |  提升為舊代資料大小  |  位元組  |  最大值 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  服務Cpu使用百分比  |  服務 CPU 使用率  |  百分比  |  Average | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  服務記憶體已提交  |  服務記憶體分配  |  位元組  |  Average | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  服務記憶體最大值  |  服務記憶體最大值  |  位元組  |  最大值 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  服務記憶體已使用  |  使用的服務記憶體  |  位元組  |  Average | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  系統 Cpu 使用百分比  |  系統 CPU 使用率  |  百分比  |  Average | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特錯誤計數  |  湯姆卡特全域錯誤  |  Count  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特接收位元組  |  湯姆卡特總接收位元組  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特請求最大時間  |  湯姆卡特請求最大時間  |  毫秒  |  最大值 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特請求總數  |  湯姆卡特請求總數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特請求總時間  |  湯姆卡特請求總時間  |  毫秒  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特回應Avg時間  |  湯姆卡特請求平均時間  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  托姆卡特森特斯  |  湯姆卡特總傳送的位元組  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特會話活動電流Count  |  Tomcat 會話活動計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特會話活動最大值Count  |  Tomcat 會話 最大活動計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特會話活動最大時間  |  Tomcat 會話最大啟用時間  |  毫秒  |  最大值 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特會話創建計數  |  Tomcat 會話創建計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特會話過期計數  |  Tomcat 會話過期計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  湯姆卡特會話拒絕計數  |  Tomcat 會話已拒絕計數  |  Count  |  總計 | 
| **是**  | 否 |  微軟.微服務4春天/應用程式集群  |  楊根升點位元組  |  提升為年輕一代資料大小  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  已使用的磁碟區集區配置  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  磁碟區集區邏輯大小總計  |  位元組  |  Average | 
| **是**  | 否 |  微軟.NetApp/NetApp帳戶/容量池/卷  |  AverageReadLatency  |  讀取延遲的平均值  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.NetApp/NetApp帳戶/容量池/卷  |  AverageWriteLatency  |  寫入延遲的平均值  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.NetApp/NetApp帳戶/容量池/卷  |  ReadIops  |  讀取 IOPS  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.NetApp/NetApp帳戶/容量池/卷  |  VolumeLogicalSize  |  磁碟區邏輯大小  |  位元組  |  Average | 
| **是**  | 否 |  微軟.NetApp/NetApp帳戶/容量池/卷  |  VolumeSnapshotSize  |  磁碟區快照集大小  |  位元組  |  Average | 
| **是**  | 否 |  微軟.NetApp/NetApp帳戶/容量池/卷  |  WriteIops  |  寫入 IOPS  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.Network/applicationGateways  |  應用程式閘道總時間  |  應用程式閘道總時間  |  毫秒  |  Average | 
| 否  | 否 |  Microsoft.Network/applicationGateways  |  平均請求計數Per健康主機  |  每個健康主機每分鐘的請求  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Network/applicationGateways  |  後端連線時間  |  後端連線時間  |  毫秒  |  Average | 
| 否  | 否 |  Microsoft.Network/applicationGateways  |  後端第一位元組回應時間  |  後端第一位元組回應時間  |  毫秒  |  Average | 
| 否  | 否 |  Microsoft.Network/applicationGateways  |  後端上位元組回應時間  |  後端上一個位元組回應時間  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  後端回應狀態  |  後端回應狀態  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  阻止計數  |  Web 應用程式防火牆阻止請求規則分發  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  已阻止的 ReqCount  |  Web 應用程式防火牆阻止的請求計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  BytesReceived  |  接收的位元組數  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  BytesSent  |  傳送的位元組數  |  位元組  |  總計 | 
| 否  | 否 |  Microsoft.Network/applicationGateways  |  容量單位  |  當前容量單位  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Network/applicationGateways  |  用戶端Rtt  |  用戶端 RTT  |  毫秒  |  Average | 
| 否  | 否 |  Microsoft.Network/applicationGateways  |  計算單位  |  當前計算單位  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  目前的連線數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  FailedRequests  |  失敗的要求  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  狀況良好的主機計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  匹配計數  |  Web 應用程式防火牆總規則分發  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  回應狀態  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Network/applicationGateways  |  Throughput  |  Throughput  |  每秒位元組  |  Average | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  特爾斯協定  |  用戶端 TLS 協定  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  TotalRequests  |  要求總數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  狀況不良的主機計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Network/azurefirewalls  |  應用程式規則Hit  |  應用程式規則命中計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/azurefirewalls  |  資料處理  |  已處理的資料量  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Network/azurefirewalls  |  防火牆運行狀況  |  防火牆運行狀況狀態  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Network/azurefirewalls  |  網路規則Hit  |  網路規則命中計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/azurefirewalls  |  SNATPort 利用  |  SNAT 埠利用率  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Network/dnszones  |  QueryVolume  |  查詢磁碟區  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  記錄集容量使用率  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/dnszones  |  RecordSetCount  |  記錄集計數  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/expressRouteCircuits  |  arp可用性  |  Arp 可用性  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Network/expressRouteCircuits  |  Bgp可用性  |  Bgp 可用性  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.Network/expressRouteCircuits  |  全域覆蓋位秒  |  全域覆蓋位秒  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.Network/expressRouteCircuits  |  全域覆蓋位出每秒  |  全域覆蓋位出每秒  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.Network/expressRouteCircuits  |  QosDropBitper秒  |  落點每秒  |  每秒計數  |  Average | 
| 否  | 否 |  Microsoft.Network/expressRouteCircuits  |  QosDropBitOutPer秒  |  已退出的位數每秒  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  每秒計數  |  Average | 
| 否  | 否 |  微軟.網路/快速路由閘道  |  ErGateway 連接位位秒  |  BitsInPerSecond  |  每秒計數  |  Average | 
| 否  | 否 |  微軟.網路/快速路由閘道  |  ErGateway連接位出每秒  |  BitsOutPerSecond  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.網路/快速路由埠  |  管理狀態  |  管理狀態  |  Count  |  Average | 
| **是**  | 否 |  微軟.網路/快速路由埠  |  線協定  |  線協定  |  Count  |  Average | 
| **是**  | 否 |  微軟.網路/快速路由埠  |  波特比辛第二  |  BitsInPerSecond  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.網路/快速路由埠  |  埠出點秒  |  BitsOutPerSecond  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.網路/快速路由埠  |  RxLight 級別  |  RxLight 級別  |  Count  |  Average | 
| **是**  | 否 |  微軟.網路/快速路由埠  |  TxLight 級別  |  TxLight 級別  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  後端健康情況百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  後端要求計數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  後端要求延遲  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Network/frontdoors  |  計費回應大小  |  計費回應大小  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Network/frontdoors  |  RequestCount  |  要求計數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Network/frontdoors  |  RequestSize  |  要求大小  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Network/frontdoors  |  ResponseSize  |  回應大小  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Network/frontdoors  |  TotalLatency  |  延遲總計  |  毫秒  |  Average | 
| **是**  | **是** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Web 應用程式防火牆要求計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  配置的 SNAT 連接埠 (預覽)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/loadBalancers  |  ByteCount  |  位元組計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/loadBalancers  |  DipAvailability  |  健全狀況探查狀態  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Network/loadBalancers  |  PacketCount  |  封包計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  SNAT 連線計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/loadBalancers  |  SYNCount  |  SYN 計數  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  使用的 SNAT 連接埠 (預覽)  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/loadBalancers  |  VipAvailability  |  資料路徑可用性  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  接收的位元組數  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  傳送的位元組數  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  接收的封包  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  傳送的封包  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  平均往返時間（毫秒）  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Network/networkWatchers/connectionMonitors  |  檢查失敗百分比  |  檢查失敗百分比（預覽）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  失敗的探查百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Network/networkWatchers/connectionMonitors  |  往返時間  |  往返時間 （ms） （預覽）  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  位元組計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  傳入位元組數捨棄 DDoS  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  傳入位元組數轉送 DDoS  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  傳入位元組數 DDoS  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  用以觸發 DDoS 風險降低措施的輸入 SYN 封包數  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  傳入 TCP 封包數以觸發 DDoS 緩和  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  傳入 UDP 封包數以觸發 DDoS 緩和  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  是否正遭受 DDoS 攻擊  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  封包計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  傳入封包捨棄 DDoS  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  傳入封包轉寄 DDoS  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  傳入封包 DDoS  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  SynCount  |  SYN 計數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  傳入 TCP 位元組數捨棄 DDoS  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  傳入 TCP 位元組數轉送 DDoS  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  傳入 TCP 位元組數 DDoS  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  傳入 TCP 封包捨棄 DDoS  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  傳入 TCP 封包轉送 DDoS  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  傳入 TCP 封包 DDoS  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  傳入 UDP 位元組數捨棄 DDoS  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  傳入 UDP 位元組數轉送 DDoS  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  傳入 UDP 位元組數 DDoS  |  每秒位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  傳入 UDP 封包捨棄 DDoS  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  傳入 UDP 封包轉送 DDoS  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  傳入 UDP 封包 DDoS  |  每秒計數  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  資料路徑可用性  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  依端點的端點狀態  |  Count  |  最大值 | 
| **是**  | **是** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  傳回的依端點查詢數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  閘道 S2S 頻寬  |  每秒位元組  |  Average | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  閘道 S2S 頻寬  |  每秒位元組  |  Average | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  P2S 連線計數  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  通道頻寬  |  每秒位元組  |  Average | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  通道輸出位元組數  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  通道輸出 TS 不相符封包捨棄  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  通道輸出封包數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  通道輸入位元組數  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  通道輸入 TS 不符合封包捨棄  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  通道輸入封包數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Network/virtualNetworks  |  PingMesh平均往返  |  Ping 到 VM 的往返時間  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Network/virtualNetworks  |  PingMeshProbe 失敗百分比  |  對 VM 的故障 Ping  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming  |  傳入訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.failedrequests  |  所有傳入的失敗要求  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  所有傳入要求  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  傳送已排程的推播通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  取消已排程的推播通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  安裝管理作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  刪除安裝作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  取得安裝作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  修補安裝作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  建立或更新安裝作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  所有外寄通知  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  錯誤或過期的通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  承載錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  外部通知系統錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  成功通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  APNS 不正確的通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  APNS 過期通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  APNS 授權錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  APNS 無效通知大小錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  APNS 錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  APNS 成功通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  GCM 驗證錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  GCM 不正確的通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  GCM 過期通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  GCM 授權錯誤 (無效認證)  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  GCM 無效通知格式  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  GCM 無效通知大小錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  GCM 錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  GCM 成功通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  GCM 已將通知節流處理  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  GCM 不正確的通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  MPNS 驗證錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  MPNS 不正確的通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  MPNS 通道已中斷連線  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  MPNS 已捨棄通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  MPNS 無效認證  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  MPNS 無效通知格式  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  MPNS 錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  MPNS 成功通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  MPNS 已將通知節流處理  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  WNS 驗證錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  WNS 不正確的通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  WNS 通道已中斷連線  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  WNS 通道已節流處理  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  WNS 已捨棄通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  WNS 過期通道錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  WNS 授權錯誤 (無效認證)  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  WNS 無效通知格式  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  WNS 無效通知大小錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  WNS 授權錯誤 (無效權杖)  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  WNS 錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  WNS 成功通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  WNS 節流通知  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  WNS 授權錯誤 (無法連線)  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  WNS 授權錯誤 (錯誤權杖)  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.all  |  註冊作業數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  註冊建立作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  註冊刪除作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  註冊讀取作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  註冊更新作業  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  暫止已排程的通知  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Memory  |  % Available Memory  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Swap Space  |  % Available Swap Space  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Committed Bytes In Use  |  % Committed Bytes In Use  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% DPC Time  |  % DPC Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Inodes  |  % Free Inodes  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % Free Space  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % Free Space  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Idle Time  |  % Idle Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Interrupt Time  |  % Interrupt Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Wait Time  |  % IO Wait Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Nice Time  |  % Nice Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Privileged Time  |  % Privileged Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % Processor Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % Processor Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Inodes  |  % Used Inodes  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Memory  |  % Used Memory  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Space  |  % Used Space  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Swap Space  |  % Used Swap Space  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_% User Time  |  % User Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  可用的 MB  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Memory  |  Available MBytes Memory  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Swap  |  Available MBytes Swap  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Read  |  平均磁片秒/讀取  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Read  |  平均磁片秒/讀取  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Transfer  |  平均磁片秒/傳輸  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Write  |  平均磁片秒/寫入  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Write  |  平均磁片秒/寫入  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Bytes Received/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Bytes Sent/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Bytes Total/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Current Disk Queue Length  |  Current Disk Queue Length  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Read Bytes/sec  |  Disk Read Bytes/sec   |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Disk Reads/sec   |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Disk Reads/sec   |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Disk Transfers/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Disk Transfers/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Write Bytes/sec  |  Disk Write Bytes/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Disk Writes/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Disk Writes/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Free Megabytes  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Free Megabytes  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Physical Memory  |  Free Physical Memory  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Space in Paging Files  |  Free Space in Paging Files  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Free Virtual Memory  |  Free Virtual Memory  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Disk Bytes/sec  |  Logical Disk Bytes/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Page Reads/sec  |  Page Reads/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Page Writes/sec  |  Page Writes/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec  |  Pages/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Privileged Time  |  Pct Privileged Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Pct User Time  |  Pct User Time  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Disk Bytes/sec  |  Physical Disk Bytes/sec  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  處理序  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Processor Queue Length  |  處理器佇列長度  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Size Stored In Paging Files  |  Size Stored In Paging Files  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  Total Bytes  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  Total Bytes Received  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Transmitted  |  Total Bytes Transmitted  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Collisions  |  Total Collisions  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Received  |  Total Packets Received  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Transmitted  |  Total Packets Transmitted  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  Total Rx Errors  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Total Tx Errors  |  Total Tx Errors  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Uptime  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  Used MBytes Swap Space  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory kBytes  |  Used Memory kBytes  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  Used Memory MBytes  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  使用者  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Shared Memory  |  Virtual Shared Memory  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  事件  |  事件  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.OperationalInsights/workspaces  |  Heartbeat  |  Heartbeat  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.OperationalInsights/workspaces  |  更新  |  更新  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  記憶體  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  記憶體抖動（資料集）  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  QPU 高使用率  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  查詢持續時間（資料集）  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  查詢池作業佇列長度（資料集）  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Search/searchServices  |  SearchLatency  |  搜尋延遲  |  秒  |  Average | 
| **是**  | 否 |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  每秒搜尋查詢  |  每秒計數  |  Average | 
| **是**  | 否 |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  節流的搜尋查詢百分比  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  佇列/主題中的作用中訊息計數。  |  Count  |  Average | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  ConnectionsClosed  |  已關閉的連線。  |  Count  |  Average | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  已開啟的連線。  |  Count  |  Average | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  CPU（已棄用）  |  百分比  |  最大值 | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  死信  |  佇列/主題中死字母消息的計數。  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  傳入訊息  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  傳入的要求  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  訊息  |  佇列/主題中的訊息計數。  |  Count  |  Average | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  命名空間Cpu使用  |  CPU  |  百分比  |  最大值 | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  命名空間記憶體使用  |  記憶體使用量  |  百分比  |  最大值 | 
| **是**  | **是** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  外送訊息  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  計畫消息  |  佇列/主題中計畫的消息計數。  |  Count  |  Average | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  伺服器錯誤。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  大小  |  大小  |  位元組  |  Average | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  成功的要求  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  節流的要求。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  使用者錯誤。  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  記憶體使用（已棄用）  |  百分比  |  最大值 | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  實際Cpu  |  實際Cpu  |  Count  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  實際記憶體  |  實際記憶體  |  位元組  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  已分配 Cpu  |  已分配 Cpu  |  Count  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  已分配記憶體  |  已分配記憶體  |  位元組  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  應用程式狀態  |  應用程式狀態  |  Count  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  容器狀態  |  容器狀態  |  Count  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  Cpu 利用  |  Cpu 利用  |  百分比  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  記憶體利用  |  記憶體利用  |  百分比  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  重新開機計數  |  重新開機計數  |  Count  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  服務複製狀態  |  服務複製狀態  |  Count  |  Average | 
| 否  | 否 |  微軟.服務布藝網格/應用程式  |  ServiceStatus  |  ServiceStatus  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  連線計數  |  Count  |  最大值 | 
| **是**  | **是** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  輸入流量  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  訊息計數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  輸出流量  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  系統錯誤  |  百分比  |  最大值 | 
| **是**  | **是** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  使用者錯誤  |  百分比  |  最大值 | 
| **是**  | **是** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  CPU 百分比平均  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  讀取的 IO 位元組  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  寫入的 IO 位元組  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Sql/managedInstances  |  io_requests  |  IO 要求計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  保留的儲存體空間  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  使用的儲存體空間  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  虛擬核心計數  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  DTU 百分比  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers  |  database_storage_used  |  使用的資料空間  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  DTU 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers  |  dtu_used  |  已使用 DTU  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers  |  storage_used  |  使用的資料空間  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  已配置的資料空間  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  應用程式 CPU 計費  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  應用 CPU 百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  應用記憶體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  遭到防火牆封鎖  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  快取命中的百分比  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  已用快取的百分比  |  百分比  |  最大值 | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  connection_failed  |  失敗的連線  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  connection_successful  |  成功的連線  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  cpu_limit  |  CPU 限制  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  CPU 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  cpu_used  |  使用的 CPU  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  deadlock  |  死結  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  DTU 百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  dtu_limit  |  DTU 限制  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  dtu_used  |  已使用 DTU  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU 百分比  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  dwu_limit  |  DWU 限制  |  Count  |  最大值 | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  dwu_used  |  已使用 DWU  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  本機 tempdb 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  記錄 IO 百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  記憶體百分比  |  百分比  |  最大值 | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  資料 IO 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  工作階段百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  儲存  |  使用的資料空間  |  位元組  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  storage_percent  |  使用的資料空間百分比  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Tempdb 資料檔案大小 千位元組  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Tempdb 日誌檔案大小 千位元組  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  使用的 Tempdb 百分比日誌  |  百分比  |  最大值 | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  workers_percent  |  背景工作角色百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  記憶體內部 OLTP 儲存體百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  已配置的資料空間  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  分配的資料空間百分比  |  百分比  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  CPU 限制  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  CPU 百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  使用的 CPU  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  已配置的資料空間  |  位元組  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  CPU 限制  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  CPU 百分比  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  使用的 CPU  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  DTU 百分比  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  已使用 eDTU  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  記錄 IO 百分比  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  資料 IO 百分比  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  工作階段百分比  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  使用的資料空間  |  位元組  |  Average | 
| 否  | 否 |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  背景工作角色百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  DTU 百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU 限制  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  已使用 eDTU  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  記錄 IO 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  資料 IO 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  工作階段百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  資料大小上限  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  使用的資料空間百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  使用的資料空間  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Tempdb 資料檔案大小 千位元組  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Tempdb 日誌檔案大小 千位元組  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  使用的 Tempdb 百分比日誌  |  百分比  |  最大值 | 
| **是**  | **是** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  背景工作角色百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  記憶體內部 OLTP 儲存體百分比  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts  |  可用性  |  可用性  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts  |  輸出  |  輸出  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts  |  交易  |  交易  |  Count  |  總計 | 
| 否  | 否 |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  已使用容量  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  可用性  |  可用性  |  百分比  |  Average | 
| 否  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Blob 容量  |  位元組  |  Average | 
| 否  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Blob 計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Blob 容器計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  輸出  |  輸出  |  位元組  |  總計 | 
| 否  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  索引容量  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/blobServices  |  交易  |  交易  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  可用性  |  可用性  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  輸出  |  輸出  |  位元組  |  總計 | 
| 否  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  檔案容量  |  位元組  |  Average | 
| 否  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  檔案計數  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  檔案共用計數  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  檔共用配額  |  檔共用配額大小  |  位元組  |  Average | 
| 否  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  檔共用快照計數  |  檔共用快照計數  |  Count  |  Average | 
| 否  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  檔共用快照大小  |  檔共用快照大小  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/fileServices  |  交易  |  交易  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/queueServices  |  可用性  |  可用性  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/queueServices  |  輸出  |  輸出  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/queueServices  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  佇列容量  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  佇列計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  佇列訊息計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/queueServices  |  交易  |  交易  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/tableServices  |  可用性  |  可用性  |  百分比  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/tableServices  |  輸出  |  輸出  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/tableServices  |  輸入  |  輸入  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  成功 E2E 延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  成功伺服器延遲  |  毫秒  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  資料表容量  |  位元組  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  資料表計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  資料表實體計數  |  Count  |  Average | 
| **是**  | 否 |  Microsoft.Storage/storageAccounts/tableServices  |  交易  |  交易  |  Count  |  總計 | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  用戶端  |  客戶 IOPS 總數  |  Count  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  用戶端延遲  |  平均用戶端延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  用戶端鎖定  |  用戶端鎖定 IOPS  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  用戶端中繼資料讀取  |  用戶端中繼資料讀取 IOPS  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  用戶端中繼資料寫入  |  用戶端中繼資料寫入 IOPS  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  用戶端閱讀  |  用戶端讀取 IOPS  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  用戶端讀取輸送量  |  平均緩存讀取輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  用戶端寫入  |  用戶端寫入 IOPS  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  用戶端寫入輸送量  |  平均緩存寫入輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標同步寫入輸送量  |  存儲目標非同步寫入輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標填充輸送量  |  存儲目標填充輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標運行狀況  |  存儲目標運行狀況  |  Count  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標  |  總存儲目標 IOPS  |  Count  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標延遲  |  存儲目標延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標中繼資料讀取IOPS  |  存儲目標中繼資料讀取 IOPS  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標中繼資料寫入IOPS  |  存儲目標中繼資料寫入 IOPS  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標讀取前通量  |  存儲目標提前讀取輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標讀取  |  存儲目標讀取 IOPS  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標同步寫入輸送量  |  存儲目標同步寫入輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標總讀取輸送量  |  存儲目標總讀取輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標總寫入輸送量  |  存儲目標總寫入輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  存儲目標寫入IOPS  |  存儲目標寫入 IOPS  |  Count  |  Average | 
| **是**  | 否 |  微軟.存儲緩存/緩存  |  Uptime  |  Uptime  |  Count  |  Average | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務  |  伺服器同步會話結果  |  同步會話結果  |  Count  |  Average | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務  |  存儲同步批次轉移檔位元組  |  同步的位元組  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務  |  存儲同步調用網路位元組應用程式  |  按應用程式劃分的雲分層召回大小  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務  |  存儲同步已調用的總網路位元組  |  雲分層召回大小  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務  |  存儲同步召回總大小位元組  |  雲端階層處理重新叫用  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務  |  存儲同步存儲輸送量位元組每秒  |  雲分層召回輸送量  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務  |  存儲同步伺服器活動訊號  |  伺服器線上狀態  |  Count  |  最大值 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務  |  存儲同步會話應用檔計數  |  檔已同步  |  Count  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務  |  存儲同步會話Per專案錯誤計數  |  檔案無法同步  |  Count  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務/註冊伺服器  |  伺服器檢測  |  伺服器線上狀態  |  Count  |  最大值 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務/註冊伺服器  |  伺服器撤回總大小位元組  |  雲端階層處理重新叫用  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務/同步組  |  同步組批次轉移檔位元組  |  同步的位元組  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務/同步組  |  同步組同步應用檔計數  |  檔已同步  |  Count  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務/同步組  |  同步組會話項錯誤計數  |  檔案無法同步  |  Count  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務/同步組/伺服器終結點  |  伺服器終結點批量傳輸檔位元組  |  同步的位元組  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務/同步組/伺服器終結點  |  伺服器端點同步應用檔計數  |  檔已同步  |  Count  |  總計 | 
| **是**  | 否 |  微軟.存儲同步/存儲同步服務/同步組/伺服器終結點  |  伺服器端點同步會話項錯誤計數  |  檔案無法同步  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  失敗的函式要求  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  函式事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  函式要求  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  資料轉換錯誤  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  輸入還原序列化錯誤  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  次序錯誤事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  早期輸入事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  Errors  |  執行階段錯誤  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  輸入事件位元組  |  位元組  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  輸入事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  輸入事件源回記錄  |  待處理輸入事件數  |  Count  |  最大值 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  輸入事件源每秒  |  收到的輸入來源數  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  延遲輸入事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  輸出事件  |  Count  |  總計 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  浮水印延遲秒數  |  秒  |  最大值 | 
| **是**  | 否 |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  SU % 使用率  |  百分比  |  最大值 | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  Disk Read Bytes  |  Disk Read Bytes  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  Disk Write Bytes  |  Disk Write Bytes  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  每秒計數  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  磁片讀取位元組秒  |  Disk Read Bytes/Sec  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  磁片讀取延遲  |  磁碟讀取延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  磁片讀取操作  |  磁片讀取操作  |  Count  |  總計 | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  磁片寫入位元組秒數  |  Disk Write Bytes/Sec  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  磁片寫入延遲  |  磁碟寫入延遲  |  毫秒  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  磁片寫入操作  |  磁片寫入操作  |  Count  |  總計 | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  記憶體活動  |  記憶體活動  |  位元組  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  記憶體授予  |  記憶體已授予  |  位元組  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  記憶體已使用  |  使用的記憶體  |  位元組  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  Network In  |  Network In  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  Network Out  |  Network Out  |  位元組  |  總計 | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  網路位元組秒  |  以位元組/秒為單位的網路  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  網路外位元組秒  |  網路出位元組/秒  |  每秒位元組  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  Percentage CPU  |  Percentage CPU  |  百分比  |  Average | 
| **是**  | 否 |  微軟.VMware雲簡單/虛擬機器  |  百分比Cpu就緒  |  CPU 就緒百分比  |  毫秒  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  使用中的要求  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  平均回應時間  |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  資料輸入  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  資料輸出  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  CPU 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  磁碟佇列長度  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http 2xx  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http 3xx  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  Http 401  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Http 伺服器錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Http 佇列長度  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  大型 App Service 方案背景工作角色  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  中型 App Service 方案背景工作角色  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  記憶體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Requests  |  Requests  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  小型 App Service 方案背景工作角色  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  前端總數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  CPU 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  記憶體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  可用的背景工作角色  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  背景工作角色總數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  已使用的背景工作角色  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  BytesReceived  |  資料輸入  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  BytesSent  |  資料輸出  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  CPU 百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  磁碟佇列長度  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Http 佇列長度  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  記憶體百分比  |  百分比  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  TcpClose等待  |  TCP 關閉等待  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  Tcp 關閉  |  TCP 關閉  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  Tcp 已建立  |  TCP 成立  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP 鰭等待 1  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP 鰭等待 2  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  TcpLastack  |  TCP 最後一個確認  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  TcpSyn接收  |  收到的 TCP 合成  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  TcpSynsent  |  TCP 合成發送  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/serverfarms  |  TcpTime等待  |  TCP 時間等待  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  AppConnections  |  連接  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  平均記憶體工作集  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  AverageResponseTime  |  平均回應時間  |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  BytesReceived  |  資料輸入  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  BytesSent  |  資料輸出  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  CpuTime  |  CPU 時間  |  秒  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  CurrentAssemblies  |  目前的組件  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  函式執行計數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  函式執行單位  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Gen0Collections  |  Gen 0 記憶體回收  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Gen1Collections  |  Gen 1 記憶體回收  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Gen2Collections  |  Gen 2 記憶體回收  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  處理  |  控制代碼計數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  運行狀況檢查狀態  |  運行狀況檢查狀態  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http2xx  |  Http 2xx  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http3xx  |  Http 3xx  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http401  |  Http 401  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http403  |  Http 403  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http4xx  |  Http 4xx  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http5xx  |  Http 伺服器錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  Http回應時間  |  回應時間  |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  每秒的 IO 其他位元組數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  每秒的 IO 其他作業數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  每秒的 IO 讀取位元組數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  每秒的 IO 讀取作業數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  每秒的 IO 寫入位元組數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  每秒的 IO 寫入作業數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  記憶體工作集  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  PrivateBytes  |  私用位元組  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  Requests  |  Requests  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  應用程式佇列中的要求數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  Threads  |  執行緒計數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  TotalAppDomains  |  應用程式網域總計  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  已卸載的應用程式網域總計  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  AppConnections  |  連接  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  平均記憶體工作集  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  平均回應時間  |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  BytesReceived  |  資料輸入  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  BytesSent  |  資料輸出  |  位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  CpuTime  |  CPU 時間  |  秒  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  目前的組件  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  函式執行計數  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  函式執行單位  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Gen 0 記憶體回收  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Gen 1 記憶體回收  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Gen 2 記憶體回收  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  處理  |  控制代碼計數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  運行狀況檢查狀態  |  運行狀況檢查狀態  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http 2xx  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http3xx  |  Http 3xx  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http 4xx  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http5xx  |  Http 伺服器錯誤  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Http回應時間  |  回應時間  |  秒  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  每秒的 IO 其他位元組數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  每秒的 IO 其他作業數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  每秒的 IO 讀取位元組數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  每秒的 IO 讀取作業數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  每秒的 IO 寫入位元組數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  每秒的 IO 寫入作業數  |  每秒位元組  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  記憶體工作集  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  私用位元組  |  位元組  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Requests  |  Requests  |  Count  |  總計 | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  應用程式佇列中的要求數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  Threads  |  執行緒計數  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  應用程式網域總計  |  Count  |  Average | 
| **是**  | **是** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  已卸載的應用程式網域總計  |  Count  |  Average | 
