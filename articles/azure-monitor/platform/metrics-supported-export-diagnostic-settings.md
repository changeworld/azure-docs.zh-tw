---
title: 透過診斷設定可匯出 Azure 監視器平臺計量
description: AAzure 監視器每一個資源類型的可用計量清單。
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: dcf5276393400be864e738d89bc5713f5aac242b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963473"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>透過診斷設定可匯出 Azure 監視器平臺計量

Azure 監視器預設會提供[平臺計量](data-platform-metrics.md)，而且沒有任何設定。 它提供數種與平臺計量互動的方式，包括在入口網站中繪製圖表、透過 REST API 存取，或使用 PowerShell 或 CLI 來查詢它們。 如需目前可透過 Azure 監視器的匯總計量管線取得之平臺度量的完整清單，請參閱[計量-支援](metrics-supported.md)。 若要查詢並存取這些計量，請使用[2018-01-01 api 版本](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)。 其他計量可在入口網站中或使用舊版 API 提供。

您可以透過兩種方式的其中一種，將平臺計量從 Azure 監視器管線匯出至其他位置。
1. 使用[診斷設定](diagnostic-settings.md)來傳送至 Log Analytics，事件中樞或 Azure 儲存體。
2. 使用[計量 REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

由於 Azure 監視器後端的複雜性，並非所有計量都可以使用診斷設定匯出。 下表列出哪些可以使用診斷設定來匯出。

資料表包含下列資料行。 
- 可透過診斷設定匯出嗎？ 
- ResourceType 
- 計量 
- MetricDisplayName
- 單位 
- AggregationType


> [!NOTE]
> 下表在底部可能會有水準捲軸。 如果您認為遺失資訊，請檢查捲軸是否為左側的方向。  


可透過診斷設定匯出嗎？ | ResourceType | 計量 | MetricDisplayName | 單位 | AggregationType
|----|-----|------|----|----|-----|
是 | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | 記憶體︰清除工具目前價格 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | 記憶體︰不可壓縮的清除器記憶體 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | 記憶體︰可壓縮的清除器記憶體 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | 執行緒︰命令集區的忙碌執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | 執行緒︰命令集區的閒置執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | 命令集區作業佇列長度 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | CurrentConnections | 連接：目前的連接 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | CurrentUserSessions | 目前的使用者工作階段 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | 執行緒︰完整剖析的忙碌執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | 執行緒︰完整剖析的閒置執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | 執行緒︰完整剖析的作業佇列長度 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | M 引擎記憶體 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | M 引擎私用位元組 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | M 引擎 QPU | 計數 | Average
是 | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | M 引擎虛擬位元組 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | memory_metric | 記憶體 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | memory_thrashing_metric | 記憶體猛移 | 百分比 | Average
是 | Microsoft.AnalysisServices/servers | MemoryLimitHard | 記憶體︰固定記憶體限制 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | MemoryLimitHigh | 記憶體︰記憶體上限 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | MemoryLimitLow | 記憶體︰記憶體下限 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | 記憶體︰記憶體限制 VertiPaq | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | MemoryUsage | 記憶體：記憶體使用量 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | private_bytes_metric | 私用位元組 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | 執行緒︰處理集區的忙碌 I/O 作業執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | 執行緒︰處理集區的忙碌非 I/O 執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | 執行緒︰處理集區的閒置 I/O 作業執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | 執行緒︰處理集區的閒置非 I/O 執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | 執行緒：處理集區 I/O 作業佇列長度 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | 處理集區作業佇列長度 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | qpu_metric | QPU | 計數 | Average
是 | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | 查詢集區忙碌執行緒 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | 執行緒︰查詢集區的閒置執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | 執行緒︰查詢集區的作業佇列長度 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | 配額 | 記憶體︰配額 | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | QuotaBlocked | 記憶體︰封鎖的配額 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | 處理︰每秒轉換的資料列 | 每秒計數 | Average
是 | Microsoft.AnalysisServices/servers | RowsReadPerSec | 處理︰每秒讀取的資料列 | 每秒計數 | Average
是 | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | 處理︰每秒寫入的資料列 | 每秒計數 | Average
是 | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | 執行緒︰簡短剖析的忙碌執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | 執行緒︰簡短剖析的閒置執行緒數 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | 執行緒︰簡短剖析的作業佇列長度 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | 每秒連線成功的次數 | 每秒計數 | Average
是 | Microsoft.AnalysisServices/servers | TotalConnectionFailures | 連線失敗的總計 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | TotalConnectionRequests | 連線要求的總計 | 計數 | Average
是 | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | 記憶體︰未分頁的 VertiPaq | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | VertiPaqPaged | 記憶體︰分頁的 VertiPaq | 位元組 | Average
是 | Microsoft.AnalysisServices/servers | virtual_bytes_metric | 虛擬位元組 | 位元組 | Average
是 | Microsoft.ApiManagement/service | BackendDuration | 後端要求的持續時間 | 毫秒 | Average
是 | Microsoft.ApiManagement/service | 容量 | 容量 | 百分比 | Average
是 | Microsoft.ApiManagement/service | 課程時間 | 閘道要求的整體持續期間 | 毫秒 | Average
是 | Microsoft.ApiManagement/service | EventHubDroppedEvents | 已卸載 EventHub 事件 | 計數 | 總計
是 | Microsoft.ApiManagement/service | EventHubRejectedEvents | 已拒絕 EventHub 事件 | 計數 | 總計
是 | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | 成功的 EventHub 事件 | 計數 | 總計
是 | Microsoft.ApiManagement/service | EventHubThrottledEvents | 已節流的 EventHub 事件 | 計數 | 總計
是 | Microsoft.ApiManagement/service | EventHubTimedoutEvents | 已超時的 EventHub 事件 | 計數 | 總計
是 | Microsoft.ApiManagement/service | EventHubTotalBytesSent | EventHub 事件的大小 | 位元組 | 總計
是 | Microsoft.ApiManagement/service | EventHubTotalEvents | EventHub 事件總數 | 計數 | 總計
是 | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | 失敗的 EventHub 事件 | 計數 | 總計
是 | Microsoft.ApiManagement/service | FailedRequests | 失敗的閘道要求（已淘汰） | 計數 | 總計
是 | Microsoft.ApiManagement/service | OtherRequests | 其他閘道要求（已淘汰） | 計數 | 總計
是 | Microsoft.ApiManagement/service | 要求 | 要求 | 計數 | 總計
是 | Microsoft.ApiManagement/service | SuccessfulRequests | 成功的閘道要求（已淘汰） | 計數 | 總計
是 | Microsoft.ApiManagement/service | TotalRequests | 閘道要求總數（已淘汰） | 計數 | 總計
是 | Microsoft.ApiManagement/service | UnauthorizedRequests | 未經授權的閘道要求（已淘汰） | 計數 | 總計
是 | AppPlatform/春季 | AppCpuUsagePercentage | 應用程式 CPU 使用量百分比 | 百分比 | Average
是 | AppPlatform/春季 | AppMemoryCommitted | 已指派應用程式記憶體 | 位元組 | Average
是 | AppPlatform/春季 | AppMemoryMax | 應用程式記憶體最大值 | 位元組 | 最大值
是 | AppPlatform/春季 | AppMemoryUsed | 使用的應用程式記憶體 | 位元組 | Average
是 | AppPlatform/春季 | GCPauseTotalCount | GC 暫停計數 | 計數 | 總計
是 | AppPlatform/春季 | GCPauseTotalTime | GC 暫停總時間 | 毫秒 | 總計
是 | AppPlatform/春季 | MaxOldGenMemoryPoolBytes | 可用的舊世代資料大小上限 | 位元組 | Average
是 | AppPlatform/春季 | OldGenMemoryPoolBytes | 舊的產生資料大小 | 位元組 | Average
是 | AppPlatform/春季 | OldGenPromotedBytes | 升級為舊的產生資料大小 | 位元組 | 最大值
是 | AppPlatform/春季 | SystemCpuUsagePercentage | 系統 CPU 使用量百分比 | 百分比 | Average
是 | AppPlatform/春季 | TomcatErrorCount | Tomcat 全域錯誤 | 計數 | 總計
是 | AppPlatform/春季 | TomcatReceivedBytes | Tomcat 接收的位元組總數 | 位元組 | 總計
是 | AppPlatform/春季 | TomcatRequestMaxTime | Tomcat 要求最大時間 | 毫秒 | 最大值
是 | AppPlatform/春季 | TomcatRequestTotalCount | Tomcat 要求總計數 | 計數 | 總計
是 | AppPlatform/春季 | TomcatRequestTotalTime | Tomcat 要求總次數 | 毫秒 | 總計
是 | AppPlatform/春季 | TomcatResponseAvgTime | Tomcat 要求平均時間 | 毫秒 | Average
是 | AppPlatform/春季 | TomcatSentBytes | Tomcat 總傳送位元組數 | 位元組 | 總計
是 | AppPlatform/春季 | TomcatSessionActiveCurrentCount | Tomcat 會話的生存計數 | 計數 | 總計
是 | AppPlatform/春季 | TomcatSessionActiveMaxCount | Tomcat 會話最大使用中計數 | 計數 | 總計
是 | AppPlatform/春季 | TomcatSessionAliveMaxTime | Tomcat 會話最大的存留時間 | 毫秒 | 最大值
是 | AppPlatform/春季 | TomcatSessionCreatedCount | Tomcat 會話已建立計數 | 計數 | 總計
是 | AppPlatform/春季 | TomcatSessionExpiredCount | Tomcat 會話過期計數 | 計數 | 總計
是 | AppPlatform/春季 | TomcatSessionRejectedCount | Tomcat 會話已拒絕計數 | 計數 | 總計
是 | AppPlatform/春季 | YoungGenPromotedBytes | 升階為年輕產生資料大小 | 位元組 | 最大值
是 | Microsoft.Automation/automationAccounts | TotalJob | 工作總數 | 計數 | 總計
是 | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | 更新部署電腦的執行總計 | 計數 | 總計
是 | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentRuns | 更新部署執行總計 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | CoreCount | 專用核心計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | CreatingNodeCount | 建立節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | IdleNodeCount | 閒置的節點計數 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | 作業刪除完成事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | 作業刪除啟動事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | 作業停用完成事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | JobDisableStartEvent | 作業停用啟動事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | JobStartEvent | 作業啟動事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | 作業終止完成事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | 作業終止啟動事件 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | 離開集區節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | LowPriority 核心計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | OfflineNodeCount | 離線節點計數 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | PoolCreateEvent | 集區建立事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | 集區刪除完成事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | 集區刪除啟動事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | 調整集區大小完成事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | 調整集區大小啟動事件 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | PreemptedNodeCount | 先占節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | RebootingNodeCount | 重新啟動節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | RebootingNodeCount | 重新安裝映像節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | RunningNodeCount | 執行節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | StartingNodeCount | 啟動節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | 啟動工作失敗的節點計數 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | TaskCompleteEvent | 工作完成事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | TaskFailEvent | 工作失敗事件 | 計數 | 總計
是 | Microsoft.Batch/batchAccounts | TaskStartEvent | 工作啟動的事件 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | 低優先順序節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | TotalNodeCount | 專用節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | UnusableNodeCount | 無法使用的節點計數 | 計數 | 總計
否 | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | 等候啟動工作節點計數 | 計數 | 總計
是 | BatchAI/工作區 | 現用核心 | 現用核心 | 計數 | Average
是 | BatchAI/工作區 | 現用節點 | 現用節點 | 計數 | Average
是 | BatchAI/工作區 | 閒置核心 | 閒置核心 | 計數 | Average
是 | BatchAI/工作區 | 閒置節點 | 閒置節點 | 計數 | Average
是 | BatchAI/工作區 | 工作已完成 | 工作已完成 | 計數 | 總計
是 | BatchAI/工作區 | 已提交作業 | 已提交作業 | 計數 | 總計
是 | BatchAI/工作區 | 離開核心 | 離開核心 | 計數 | Average
是 | BatchAI/工作區 | 離開節點 | 離開節點 | 計數 | Average
是 | BatchAI/工作區 | 搶先核心 | 搶先核心 | 計數 | Average
是 | BatchAI/工作區 | 搶先節點 | 搶先節點 | 計數 | Average
是 | BatchAI/工作區 | 配額使用量百分比 | 配額使用量百分比 | 計數 | Average
是 | BatchAI/工作區 | 核心總數 | 核心總數 | 計數 | Average
是 | BatchAI/工作區 | 節點總計 | 節點總計 | 計數 | Average
是 | BatchAI/工作區 | 無法使用的核心 | 無法使用的核心 | 計數 | Average
是 | BatchAI/工作區 | 無法使用的節點 | 無法使用的節點 | 計數 | Average
是 | Microsoft.Blockchain/blockchainMembers | ConnectionAccepted | 接受的連接 | 計數 | 總計
是 | Microsoft.Blockchain/blockchainMembers | ConnectionActive | 作用中的連線 | 計數 | Average
是 | Microsoft.Blockchain/blockchainMembers | ConnectionHandled | 已處理的連接 | 計數 | 總計
是 | Microsoft.Blockchain/blockchainMembers | CpuUsagePercentageInDouble | CPU 使用量百分比 | 百分比 | 最大值
是 | Microsoft.Blockchain/blockchainMembers | IOReadBytes | IO 讀取位元組 | 位元組 | 總計
是 | Microsoft.Blockchain/blockchainMembers | IOWriteBytes | IO 寫入位元組 | 位元組 | 總計
是 | Microsoft.Blockchain/blockchainMembers | MemoryLimit | 記憶體限制 | 位元組 | Average
是 | Microsoft.Blockchain/blockchainMembers | MemoryUsage | 記憶體使用量 | 位元組 | Average
是 | Microsoft.Blockchain/blockchainMembers | MemoryUsagePercentageInDouble | 記憶體使用量百分比 | 百分比 | Average
是 | Microsoft.Blockchain/blockchainMembers | 擱置交易 | 暫止交易 | 計數 | Average
是 | Microsoft.Blockchain/blockchainMembers | ProcessedBlocks | 已處理的區塊 | 計數 | 總計
是 | Microsoft.Blockchain/blockchainMembers | ProcessedTransactions | 已處理的交易 | 計數 | 總計
是 | Microsoft.Blockchain/blockchainMembers | QueuedTransactions | 佇列交易 | 計數 | Average
是 | Microsoft.Blockchain/blockchainMembers | RequestHandled | 已處理的要求 | 計數 | 總計
是 | Microsoft.Blockchain/blockchainMembers | StorageUsage | 存放裝置使用量 | 位元組 | Average
是 | Microsoft.Cache/redis | cachehits | 快取點擊 | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits0 | 快取點擊 (分區 0) | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits1 | 快取點擊 (分區 1) | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits2 | 快取點擊 (分區 2) | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits3 | 快取點擊 (分區 3) | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits4 | 快取點擊 (分區 4) | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits5 | 快取點擊 (分區 5) | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits6 | 快取點擊 (分區 6) | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits7 | 快取點擊 (分區 7) | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits8 | 快取點擊 (分區 8) | 計數 | 總計
是 | Microsoft.Cache/redis | cachehits9 | 快取點擊 (分區 9) | 計數 | 總計
是 | Microsoft.Cache/redis | cacheLatency | 快取延遲 (毫秒) (預覽) | 計數 | Average
是 | Microsoft.Cache/redis | cachemisses | 快取遺漏 | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses0 | 快取遺漏 (分區 0) | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses1 | 快取遺漏 (分區 1) | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses2 | 快取遺漏 (分區 2) | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses3 | 快取遺漏 (分區 3) | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses4 | 快取遺漏 (分區 4) | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses5 | 快取遺漏 (分區 5) | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses6 | 快取遺漏 (分區 6) | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses7 | 快取遺漏 (分區 7) | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses8 | 快取遺漏 (分區 8) | 計數 | 總計
是 | Microsoft.Cache/redis | cachemisses9 | 快取遺漏 (分區 9) | 計數 | 總計
是 | Microsoft.Cache/redis | cacheRead | 快取讀取 | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead0 | 快取讀取 (分區 0) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead1 | 快取讀取 (分區 1) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead2 | 快取讀取 (分區 2) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead3 | 快取讀取 (分區 3) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead4 | 快取讀取 (分區 4) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead5 | 快取讀取 (分區 5) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead6 | 快取讀取 (分區 6) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead7 | 快取讀取 (分區 7) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead8 | 快取讀取 (分區 8) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheRead9 | 快取讀取 (分區 9) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite | 快取寫入 | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite0 | 快取寫入 (分區 0) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite1 | 快取寫入 (分區 1) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite2 | 快取寫入 (分區 2) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite3 | 快取寫入 (分區 3) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite4 | 快取寫入 (分區 4) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite5 | 快取寫入 (分區 5) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite6 | 快取寫入 (分區 6) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite7 | 快取寫入 (分區 7) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite8 | 快取寫入 (分區 8) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | cacheWrite9 | 快取寫入 (分區 9) | 每秒位元組 | 最大值
是 | Microsoft.Cache/redis | connectedclients | 連線的用戶端 | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients0 | 連線的用戶端 (分區 0) | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients1 | 連線的用戶端 (分區 1) | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients2 | 連線的用戶端 (分區 2) | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients3 | 連線的用戶端 (分區 3) | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients4 | 連線的用戶端 (分區 4) | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients5 | 連線的用戶端 (分區 5) | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients6 | 連線的用戶端 (分區 6) | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients7 | 連線的用戶端 (分區 7) | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients8 | 連線的用戶端 (分區 8) | 計數 | 最大值
是 | Microsoft.Cache/redis | connectedclients9 | 連線的用戶端 (分區 9) | 計數 | 最大值
是 | Microsoft.Cache/redis | 錯誤 | Errors | 計數 | 最大值
是 | Microsoft.Cache/redis | evictedkeys | 收回的金鑰 | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys0 | 收回的金鑰 (分區 0) | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys1 | 收回的金鑰 (分區 1) | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys2 | 收回的金鑰 (分區 2) | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys3 | 收回的金鑰 (分區 3) | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys4 | 收回的金鑰 (分區 4) | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys5 | 收回的金鑰 (分區 5) | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys6 | 收回的金鑰 (分區 6) | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys7 | 收回的金鑰 (分區 7) | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys8 | 收回的金鑰 (分區 8) | 計數 | 總計
是 | Microsoft.Cache/redis | evictedkeys9 | 收回的金鑰 (分區 9) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys | 到期的金鑰 | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys0 | 到期的金鑰 (分區 0) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys1 | 到期的金鑰 (分區 1) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys2 | 到期的金鑰 (分區 2) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys3 | 到期的金鑰 (分區 3) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys4 | 到期的金鑰 (分區 4) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys5 | 到期的金鑰 (分區 5) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys6 | 到期的金鑰 (分區 6) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys7 | 到期的金鑰 (分區 7) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys8 | 到期的金鑰 (分區 8) | 計數 | 總計
是 | Microsoft.Cache/redis | expiredkeys9 | 到期的金鑰 (分區 9) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands | 取得 | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands0 | 取得 (分區 0) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands1 | 取得 (分區 1) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands2 | 取得 (分區 2) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands3 | 取得 (分區 3) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands4 | 取得 (分區 4) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands5 | 取得 (分區 5) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands6 | 取得 (分區 6) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands7 | 取得 (分區 7) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands8 | 取得 (分區 8) | 計數 | 總計
是 | Microsoft.Cache/redis | getcommands9 | 取得 (分區 9) | 計數 | 總計
是 | Microsoft.Cache/redis | operationsPerSecond | 每秒的作業數 | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond0 | 每秒的作業數 (分區 0) | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond1 | 每秒的作業數 (分區 1) | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond2 | 每秒的作業數 (分區 2) | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond3 | 每秒的作業數 (分區 3) | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond4 | 每秒的作業數 (分區 4) | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond5 | 每秒的作業數 (分區 5) | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond6 | 每秒的作業數 (分區 6) | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond7 | 每秒的作業數 (分區 7) | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond8 | 每秒的作業數 (分區 8) | 計數 | 最大值
是 | Microsoft.Cache/redis | operationsPerSecond9 | 每秒的作業數 (分區 9) | 計數 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime | CPU | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime0 | CPU (分區 0) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime1 | CPU (分區 1) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime2 | CPU (分區 2) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime3 | CPU (分區 3) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime4 | CPU (分區 4) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime5 | CPU (分區 5) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime6 | CPU (分區 6) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime7 | CPU (分區 7) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime8 | CPU (分區 8) | 百分比 | 最大值
是 | Microsoft.Cache/redis | percentProcessorTime9 | CPU (分區 9) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad | 伺服器負載 | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad0 | 伺服器負載 (分區 0) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad1 | 伺服器負載 (分區 1) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad2 | 伺服器負載 (分區 2) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad3 | 伺服器負載 (分區 3) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad4 | 伺服器負載 (分區 4) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad5 | 伺服器負載 (分區 5) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad6 | 伺服器負載 (分區 6) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad7 | 伺服器負載 (分區 7) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad8 | 伺服器負載 (分區 8) | 百分比 | 最大值
是 | Microsoft.Cache/redis | serverLoad9 | 伺服器負載 (分區 9) | 百分比 | 最大值
是 | Microsoft.Cache/redis | setcommands | 集合 | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands0 | 設定 (分區 0) | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands1 | 設定 (分區 1) | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands2 | 設定 (分區 2) | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands3 | 設定 (分區 3) | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands4 | 設定 (分區 4) | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands5 | 設定 (分區 5) | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands6 | 設定 (分區 6) | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands7 | 設定 (分區 7) | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands8 | 設定 (分區 8) | 計數 | 總計
是 | Microsoft.Cache/redis | setcommands9 | 設定 (分區 9) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed | 總作業數 | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed0 | 總作業數 (分區 0) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed1 | 總作業數 (分區 1) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed2 | 總作業數 (分區 2) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed3 | 總作業數 (分區 3) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed4 | 總作業數 (分區 4) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed5 | 總作業數 (分區 5) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed6 | 總作業數 (分區 6) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed7 | 總作業數 (分區 7) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed8 | 總作業數 (分區 8) | 計數 | 總計
是 | Microsoft.Cache/redis | totalcommandsprocessed9 | 總作業數 (分區 9) | 計數 | 總計
是 | Microsoft.Cache/redis | totalkeys | 索引鍵總計 | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys0 | 金鑰總數 (分區 0) | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys1 | 金鑰總數 (分區 1) | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys2 | 金鑰總數 (分區 2) | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys3 | 金鑰總數 (分區 3) | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys4 | 金鑰總數 (分區 4) | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys5 | 金鑰總數 (分區 5) | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys6 | 金鑰總數 (分區 6) | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys7 | 金鑰總數 (分區 7) | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys8 | 金鑰總數 (分區 8) | 計數 | 最大值
是 | Microsoft.Cache/redis | totalkeys9 | 金鑰總數 (分區 9) | 計數 | 最大值
是 | Microsoft.Cache/redis | usedmemory | 已使用的記憶體 | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory0 | 已用的記憶體 (分區 0) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory1 | 已用的記憶體 (分區 1) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory2 | 已用的記憶體 (分區 2) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory3 | 已用的記憶體 (分區 3) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory4 | 已用的記憶體 (分區 4) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory5 | 已用的記憶體 (分區 5) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory6 | 已用的記憶體 (分區 6) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory7 | 已用的記憶體 (分區 7) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory8 | 已用的記憶體 (分區 8) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemory9 | 已用的記憶體 (分區 9) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemorypercentage | 已用的記憶體百分比 | 百分比 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss | 已用的記憶體 RSS | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss0 | 已用的記憶體 RSS (分區 0) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss1 | 已用的記憶體 RSS (分區 1) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss2 | 已用的記憶體 RSS (分區 2) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss3 | 已用的記憶體 RSS (分區 3) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss4 | 已用的記憶體 RSS (分區 4) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss5 | 已用的記憶體 RSS (分區 5) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss6 | 已用的記憶體 RSS (分區 6) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss7 | 已用的記憶體 RSS (分區 7) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss8 | 已用的記憶體 RSS (分區 8) | 位元組 | 最大值
是 | Microsoft.Cache/redis | usedmemoryRss9 | 已用的記憶體 RSS (分區 9) | 位元組 | 最大值
否 | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Read Bytes/Sec | 磁碟讀取 | 每秒位元組 | Average
是 | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Read Operations/Sec | Disk Read Operations/Sec | 每秒計數 | Average
否 | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Write Bytes/Sec | 磁碟寫入 | 每秒位元組 | Average
是 | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Write Operations/Sec | Disk Write Operations/Sec | 每秒計數 | Average
是 | Microsoft.ClassicCompute/domainNames/slots/roles | Network In | Network In | 位元組 | 總計
是 | Microsoft.ClassicCompute/domainNames/slots/roles | Network Out | Network Out | 位元組 | 總計
是 | Microsoft.ClassicCompute/domainNames/slots/roles | Percentage CPU | Percentage CPU | 百分比 | Average
否 | Microsoft.ClassicCompute/virtualMachines | Disk Read Bytes/Sec | 磁碟讀取 | 每秒位元組 | Average
是 | Microsoft.ClassicCompute/virtualMachines | Disk Read Operations/Sec | Disk Read Operations/Sec | 每秒計數 | Average
否 | Microsoft.ClassicCompute/virtualMachines | Disk Write Bytes/Sec | 磁碟寫入 | 每秒位元組 | Average
是 | Microsoft.ClassicCompute/virtualMachines | Disk Write Operations/Sec | Disk Write Operations/Sec | 每秒計數 | Average
是 | Microsoft.ClassicCompute/virtualMachines | Network In | Network In | 位元組 | 總計
是 | Microsoft.ClassicCompute/virtualMachines | Network Out | Network Out | 位元組 | 總計
是 | Microsoft.ClassicCompute/virtualMachines | Percentage CPU | Percentage CPU | 百分比 | Average
是 | Microsoft.ClassicStorage/storageAccounts | 可用性 | 可用性 | 百分比 | Average
是 | Microsoft.ClassicStorage/storageAccounts | 輸出 | 輸出 | 位元組 | 總計
是 | Microsoft.ClassicStorage/storageAccounts | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.ClassicStorage/storageAccounts | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.ClassicStorage/storageAccounts | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.ClassicStorage/storageAccounts | 交易 | 交易 | 計數 | 總計
否 | Microsoft.ClassicStorage/storageAccounts | UsedCapacity | 已使用容量 | 位元組 | Average
是 | Microsoft.classicstorage/storageAccounts/blobServices | 可用性 | 可用性 | 百分比 | Average
否 | Microsoft.classicstorage/storageAccounts/blobServices | BlobCapacity | Blob 容量 | 位元組 | Average
否 | Microsoft.classicstorage/storageAccounts/blobServices | BlobCount | Blob 計數 | 計數 | Average
是 | Microsoft.classicstorage/storageAccounts/blobServices | ContainerCount | Blob 容器計數 | 計數 | Average
是 | Microsoft.classicstorage/storageAccounts/blobServices | 輸出 | 輸出 | 位元組 | 總計
否 | Microsoft.classicstorage/storageAccounts/blobServices | IndexCapacity | 索引容量 | 位元組 | Average
是 | Microsoft.classicstorage/storageAccounts/blobServices | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.classicstorage/storageAccounts/blobServices | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.classicstorage/storageAccounts/blobServices | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.classicstorage/storageAccounts/blobServices | 交易 | 交易 | 計數 | 總計
是 | Microsoft.classicstorage/storageAccounts/fileServices | 可用性 | 可用性 | 百分比 | Average
是 | Microsoft.classicstorage/storageAccounts/fileServices | 輸出 | 輸出 | 位元組 | 總計
否 | Microsoft.classicstorage/storageAccounts/fileServices | FileCapacity | 檔案容量 | 位元組 | Average
否 | Microsoft.classicstorage/storageAccounts/fileServices | FileCount | 檔案計數 | 計數 | Average
否 | Microsoft.classicstorage/storageAccounts/fileServices | FileShareCount | 檔案共用計數 | 計數 | Average
否 | Microsoft.classicstorage/storageAccounts/fileServices | FileShareQuota | 檔案共用配額大小 | 位元組 | Average
否 | Microsoft.classicstorage/storageAccounts/fileServices | FileShareSnapshotCount | 檔案共用快照集計數 | 計數 | Average
否 | Microsoft.classicstorage/storageAccounts/fileServices | FileShareSnapshotSize | 檔案共用快照集大小 | 位元組 | Average
是 | Microsoft.classicstorage/storageAccounts/fileServices | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.classicstorage/storageAccounts/fileServices | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.classicstorage/storageAccounts/fileServices | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.classicstorage/storageAccounts/fileServices | 交易 | 交易 | 計數 | 總計
是 | Microsoft.classicstorage/storageAccounts/queueServices | 可用性 | 可用性 | 百分比 | Average
是 | Microsoft.classicstorage/storageAccounts/queueServices | 輸出 | 輸出 | 位元組 | 總計
是 | Microsoft.classicstorage/storageAccounts/queueServices | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.classicstorage/storageAccounts/queueServices | QueueCapacity | 佇列容量 | 位元組 | Average
是 | Microsoft.classicstorage/storageAccounts/queueServices | QueueCount | 佇列計數 | 計數 | Average
是 | Microsoft.classicstorage/storageAccounts/queueServices | QueueMessageCount | 佇列訊息計數 | 計數 | Average
是 | Microsoft.classicstorage/storageAccounts/queueServices | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.classicstorage/storageAccounts/queueServices | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.classicstorage/storageAccounts/queueServices | 交易 | 交易 | 計數 | 總計
是 | Microsoft.classicstorage/storageAccounts/tableServices | 可用性 | 可用性 | 百分比 | Average
是 | Microsoft.classicstorage/storageAccounts/tableServices | 輸出 | 輸出 | 位元組 | 總計
是 | Microsoft.classicstorage/storageAccounts/tableServices | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.classicstorage/storageAccounts/tableServices | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.classicstorage/storageAccounts/tableServices | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.classicstorage/storageAccounts/tableServices | TableCapacity | 資料表容量 | 位元組 | Average
是 | Microsoft.classicstorage/storageAccounts/tableServices | TableCount | 資料表計數 | 計數 | Average
是 | Microsoft.classicstorage/storageAccounts/tableServices | TableEntityCount | 資料表實體計數 | 計數 | Average
是 | Microsoft.classicstorage/storageAccounts/tableServices | 交易 | 交易 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | BlockedCalls | 已封鎖的呼叫 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | CharactersTrained | 已定型的字元 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | CharactersTranslated | 轉譯字元數 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | ClientErrors | 用戶端錯誤 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | DataIn | 資料輸入 | 位元組 | 總計
是 | Microsoft.CognitiveServices/accounts | DataOut | 資料輸出 | 位元組 | 總計
是 | Microsoft.CognitiveServices/accounts | 延遲 | 延遲 | 毫秒 | Average
是 | Microsoft.CognitiveServices/accounts | ServerErrors | 伺服器錯誤 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | 語音工作階段持續時間 | 秒 | 總計
是 | Microsoft.CognitiveServices/accounts | SuccessfulCalls | 成功的呼叫 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | TotalCalls | 呼叫總數 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | TotalErrors | 錯誤總數 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | TotalTokenCalls | 呼叫權杖總計 | 計數 | 總計
是 | Microsoft.CognitiveServices/accounts | TotalTransactions | 交易數總計 | 計數 | 總計
是 | Microsoft.Compute/virtualMachines | CPU Credits Consumed | CPU Credits Consumed | 計數 | Average
是 | Microsoft.Compute/virtualMachines | 剩餘 CPU 信用額度 | 剩餘 CPU 信用額度 | 計數 | Average
是 | Microsoft.Compute/virtualMachines | 資料磁片佇列深度 | 資料磁片佇列深度（預覽） | 計數 | Average
是 | Microsoft.Compute/virtualMachines | 資料磁片讀取位元組數/秒 | 資料磁碟讀取位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | 資料磁片讀取作業/秒 | 資料磁碟讀取作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | 資料磁片寫入位元組數/秒 | 資料磁碟寫入位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | 資料磁片寫入作業/秒 | 資料磁碟寫入作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | Disk Read Bytes | Disk Read Bytes | 位元組 | 總計
是 | Microsoft.Compute/virtualMachines | Disk Read Operations/Sec | Disk Read Operations/Sec | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | Disk Write Bytes | Disk Write Bytes | 位元組 | 總計
是 | Microsoft.Compute/virtualMachines | Disk Write Operations/Sec | Disk Write Operations/Sec | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | 輸入流量 | 輸入流量 | 計數 | Average
是 | Microsoft.Compute/virtualMachines | 輸入流量最大建立速率 | 輸入流量最大建立速率（預覽） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | Network In | 計費中的網路（已淘汰） | 位元組 | 總計
是 | Microsoft.Compute/virtualMachines | 網路總計 | 網路總計 | 位元組 | 總計
是 | Microsoft.Compute/virtualMachines | Network Out | 網路輸出計費（已淘汰） | 位元組 | 總計
是 | Microsoft.Compute/virtualMachines | 網路輸出總計 | 網路輸出總計 | 位元組 | 總計
是 | Microsoft.Compute/virtualMachines | OS 磁片佇列深度 | OS 磁片佇列深度（預覽） | 計數 | Average
是 | Microsoft.Compute/virtualMachines | OS 磁片讀取位元組數/秒 | OS 磁碟讀取位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | OS 磁片讀取作業數/秒 | OS 磁碟讀取作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | OS 磁片寫入位元組數/秒 | OS 磁碟寫入位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | OS 磁片寫入作業/秒 | OS 磁碟寫入作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | OS 每一磁碟 QD | OS 磁片 QD （已淘汰） | 計數 | Average
是 | Microsoft.Compute/virtualMachines | OS 每一磁碟讀取位元組/秒 | OS 磁片讀取位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | OS 每一磁碟讀取作業/秒 | OS 磁片讀取作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | OS 每一磁碟寫入位元組/秒 | OS 磁片寫入位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | OS 每一磁碟寫入作業/秒 | OS 磁片寫入作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | 輸出流程 | 輸出流程 | 計數 | Average
是 | Microsoft.Compute/virtualMachines | 輸出流量最大建立速率 | 輸出流量最大建立速率（預覽） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | 每一磁碟 QD | 資料磁片 QD （已淘汰） | 計數 | Average
是 | Microsoft.Compute/virtualMachines | 每一磁碟讀取位元組/秒 | 資料磁片讀取位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | 每一磁碟讀取作業/秒 | 資料磁片讀取作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | 每一磁碟寫入位元組/秒 | 資料磁片寫入位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | 每一磁碟寫入作業/秒 | 資料磁片寫入作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachines | Percentage CPU | Percentage CPU | 百分比 | Average
是 | Microsoft.Compute/virtualMachines | Premium 資料磁碟快取讀取命中 | Premium 資料磁碟快取讀取命中（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachines | Premium 資料磁碟快取讀取遺漏 | Premium 資料磁碟快取讀取遺漏（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachines | Premium OS 磁碟快取讀取命中 | Premium OS 磁碟快取讀取命中（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachines | Premium OS 磁碟快取讀取遺漏 | Premium OS 磁碟快取讀取遺漏（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | CPU Credits Consumed | CPU Credits Consumed | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 剩餘 CPU 信用額度 | 剩餘 CPU 信用額度 | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 資料磁片佇列深度 | 資料磁片佇列深度（預覽） | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 資料磁片讀取位元組數/秒 | 資料磁碟讀取位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 資料磁片讀取作業/秒 | 資料磁碟讀取作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 資料磁片寫入位元組數/秒 | 資料磁碟寫入位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 資料磁片寫入作業/秒 | 資料磁碟寫入作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | Disk Read Bytes | Disk Read Bytes | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets | Disk Read Operations/Sec | Disk Read Operations/Sec | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | Disk Write Bytes | Disk Write Bytes | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets | Disk Write Operations/Sec | Disk Write Operations/Sec | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 輸入流量 | 輸入流量 | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 輸入流量最大建立速率 | 輸入流量最大建立速率（預覽） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | Network In | 計費中的網路（已淘汰） | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets | 網路總計 | 網路總計 | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets | Network Out | 網路輸出計費（已淘汰） | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets | 網路輸出總計 | 網路輸出總計 | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁片佇列深度 | OS 磁片佇列深度（預覽） | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁片讀取位元組數/秒 | OS 磁碟讀取位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁片讀取作業數/秒 | OS 磁碟讀取作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁片寫入位元組數/秒 | OS 磁碟寫入位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | OS 磁片寫入作業/秒 | OS 磁碟寫入作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每一磁碟 QD | OS 磁片 QD （已淘汰） | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每一磁碟讀取位元組/秒 | OS 磁片讀取位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每一磁碟讀取作業/秒 | OS 磁片讀取作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每一磁碟寫入位元組/秒 | OS 磁片寫入位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | OS 每一磁碟寫入作業/秒 | OS 磁片寫入作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 輸出流程 | 輸出流程 | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 輸出流量最大建立速率 | 輸出流量最大建立速率（預覽） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 每一磁碟 QD | 資料磁片 QD （已淘汰） | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 每一磁碟讀取位元組/秒 | 資料磁片讀取位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 每一磁碟讀取作業/秒 | 資料磁片讀取作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 每一磁碟寫入位元組/秒 | 資料磁片寫入位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | 每一磁碟寫入作業/秒 | 資料磁片寫入作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | Percentage CPU | Percentage CPU | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | Premium 資料磁碟快取讀取命中 | Premium 資料磁碟快取讀取命中（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | Premium 資料磁碟快取讀取遺漏 | Premium 資料磁碟快取讀取遺漏（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | Premium OS 磁碟快取讀取命中 | Premium OS 磁碟快取讀取命中（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets | Premium OS 磁碟快取讀取遺漏 | Premium OS 磁碟快取讀取遺漏（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | CPU Credits Consumed | CPU Credits Consumed | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 剩餘 CPU 信用額度 | 剩餘 CPU 信用額度 | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 資料磁片佇列深度 | 資料磁片佇列深度（預覽） | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 資料磁片讀取位元組數/秒 | 資料磁碟讀取位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 資料磁片讀取作業/秒 | 資料磁碟讀取作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 資料磁片寫入位元組數/秒 | 資料磁碟寫入位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 資料磁片寫入作業/秒 | 資料磁碟寫入作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Disk Read Bytes | Disk Read Bytes | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Disk Read Operations/Sec | Disk Read Operations/Sec | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Disk Write Bytes | Disk Write Bytes | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Disk Write Operations/Sec | Disk Write Operations/Sec | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 輸入流量 | 輸入流量 | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 輸入流量最大建立速率 | 輸入流量最大建立速率（預覽） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Network In | 計費中的網路（已淘汰） | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 網路總計 | 網路總計 | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Network Out | 網路輸出計費（已淘汰） | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 網路輸出總計 | 網路輸出總計 | 位元組 | 總計
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁片佇列深度 | OS 磁片佇列深度（預覽） | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁片讀取位元組數/秒 | OS 磁碟讀取位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁片讀取作業數/秒 | OS 磁碟讀取作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁片寫入位元組數/秒 | OS 磁碟寫入位元組/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 磁片寫入作業/秒 | OS 磁碟寫入作業/秒 (預覽) | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每一磁碟 QD | OS 磁片 QD （已淘汰） | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每一磁碟讀取位元組/秒 | OS 磁片讀取位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每一磁碟讀取作業/秒 | OS 磁片讀取作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每一磁碟寫入位元組/秒 | OS 磁片寫入位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 每一磁碟寫入作業/秒 | OS 磁片寫入作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 輸出流程 | 輸出流程 | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 輸出流量最大建立速率 | 輸出流量最大建立速率（預覽） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每一磁碟 QD | 資料磁片 QD （已淘汰） | 計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每一磁碟讀取位元組/秒 | 資料磁片讀取位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每一磁碟讀取作業/秒 | 資料磁片讀取作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每一磁碟寫入位元組/秒 | 資料磁片寫入位元組數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 每一磁碟寫入作業/秒 | 資料磁片寫入作業數/秒（已淘汰） | 每秒計數 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Percentage CPU | Percentage CPU | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Premium 資料磁碟快取讀取命中 | Premium 資料磁碟快取讀取命中（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Premium 資料磁碟快取讀取遺漏 | Premium 資料磁碟快取讀取遺漏（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Premium OS 磁碟快取讀取命中 | Premium OS 磁碟快取讀取命中（預覽） | 百分比 | Average
是 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Premium OS 磁碟快取讀取遺漏 | Premium OS 磁碟快取讀取遺漏（預覽） | 百分比 | Average
是 | Microsoft.ContainerInstance/containerGroups | CpuUsage | CPU 使用率 | 計數 | Average
是 | Microsoft.ContainerInstance/containerGroups | MemoryUsage | 記憶體使用量 | 位元組 | Average
是 | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | 每秒接收的網路位元組數 | 位元組 | Average
是 | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | 每秒傳輸的網路位元組數 | 位元組 | Average
是 | Microsoft.ContainerRegistry/registries | RunDuration | 執行持續時間 | 毫秒 | 總計
是 | Microsoft.ContainerRegistry/registries | SuccessfulPullCount | 成功的提取計數 | 計數 | Average
是 | Microsoft.ContainerRegistry/registries | SuccessfulPushCount | 成功推送計數 | 計數 | Average
是 | Microsoft.ContainerRegistry/registries | TotalPullCount | 總提取計數 | 計數 | Average
是 | Microsoft.ContainerRegistry/registries | TotalPushCount | 總推送計數 | 計數 | Average
否 | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | 受控叢集中可用的 cpu 核心總數 | 計數 | Average
否 | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | 受控叢集中可用的記憶體總量 | 位元組 | Average
否 | Microsoft.ContainerService/managedClusters | kube_node_status_condition | 各種節點條件的狀態 | 計數 | Average
否 | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | 各階段的 Pod 數目 | 計數 | Average
否 | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | 處於就緒狀態的 Pod 數目 | 計數 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | AvailableCapacity | 可用容量 | 位元組 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | 已上傳的雲端位元組數（裝置） | 位元組 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | 已上傳的雲端位元組數（共用） | 位元組 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | 雲端下載輸送量 | 每秒位元組 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | 雲端下載輸送量（共用） | 每秒位元組 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | 雲端上傳輸送量 | 每秒位元組 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | 雲端上傳輸送量（共用） | 每秒位元組 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | Edge 計算-記憶體使用量 | 百分比 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | Edge 計算-CPU 百分比 | 百分比 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | 讀取輸送量（網路） | 每秒位元組 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | 寫入輸送量（網路） | 每秒位元組 | Average
是 | DataBoxEdge/dataBoxEdgeDevices | TotalCapacity | 總容量 | 位元組 | Average
是 | Microsoft.DataFactory/datafactories | FailedRuns | 失敗的執行 | 計數 | 總計
是 | Microsoft.DataFactory/datafactories | SuccessfulRuns | 成功的執行 | 計數 | 總計
是 | Microsoft.DataFactory/factories | ActivityCancelledRuns | 已取消的活動執行計量 | 計數 | 總計
是 | Microsoft.DataFactory/factories | ActivityFailedRuns | 失敗的活動執行計量 | 計數 | 總計
是 | Microsoft.DataFactory/factories | ActivitySucceededRuns | 成功的活動執行計量 | 計數 | 總計
是 | Microsoft.DataFactory/factories | FactorySizeInGbUnits | 總廠大小（GB 單位） | 計數 | 最大值
是 | Microsoft.DataFactory/factories | IntegrationRuntimeAvailableMemory | 整合執行階段可用記憶體 | 位元組 | Average
是 | Microsoft.DataFactory/factories | IntegrationRuntimeAverageTaskPickupDelay | Integration runtime 佇列持續時間 | 秒 | Average
是 | Microsoft.DataFactory/factories | IntegrationRuntimeCpuPercentage | 整合執行階段 CPU 使用率 | 百分比 | Average
是 | Microsoft.DataFactory/factories | IntegrationRuntimeQueueLength | 整合執行時間佇列長度 | 計數 | Average
是 | Microsoft.DataFactory/factories | MaxAllowedFactorySizeInGbUnits | 允許的 factory 大小上限（GB 單位） | 計數 | 最大值
是 | Microsoft.DataFactory/factories | MaxAllowedResourceCount | 允許的實體數目上限 | 計數 | 最大值
是 | Microsoft.DataFactory/factories | PipelineCancelledRuns | 已取消的管線執行計量 | 計數 | 總計
是 | Microsoft.DataFactory/factories | PipelineFailedRuns | 失敗的管線執行計量 | 計數 | 總計
是 | Microsoft.DataFactory/factories | PipelineSucceededRuns | 成功的管線執行計量 | 計數 | 總計
是 | Microsoft.DataFactory/factories | ResourceCount | 總實體數 | 計數 | 最大值
是 | Microsoft.DataFactory/factories | TriggerCancelledRuns | 已取消的觸發程式會執行計量 | 計數 | 總計
是 | Microsoft.DataFactory/factories | TriggerFailedRuns | 失敗的觸發程序執行計量 | 計數 | 總計
是 | Microsoft.DataFactory/factories | TriggerSucceededRuns | 成功的觸發程序執行計量 | 計數 | 總計
是 | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | 取消的 AU 時間 | 秒 | 總計
是 | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | 失敗 AU 時間 | 秒 | 總計
是 | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | 成功 AU 時間 | 秒 | 總計
是 | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | 取消的作業 | 計數 | 總計
是 | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | 失敗的作業 | 計數 | 總計
是 | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | 成功的作業 | 計數 | 總計
是 | Microsoft.DataLakeStore/accounts | DataRead | 讀取的資料 | 位元組 | 總計
是 | Microsoft.DataLakeStore/accounts | DataWritten | 寫入的資料 | 位元組 | 總計
是 | Microsoft.DataLakeStore/accounts | ReadRequests | 讀取要求 | 計數 | 總計
是 | Microsoft.DataLakeStore/accounts | TotalStorage | 儲存體總計 | 位元組 | 最大值
是 | Microsoft.DataLakeStore/accounts | WriteRequests | 寫入要求 | 計數 | 總計
是 | Microsoft.DBforMariaDB/servers | active_connections | 作用中的連線 | 計數 | Average
是 | Microsoft.DBforMariaDB/servers | backup_storage_used | 已使用的備份儲存體 | 位元組 | Average
是 | Microsoft.DBforMariaDB/servers | connections_failed | 失敗的連線 | 計數 | 總計
是 | Microsoft.DBforMariaDB/servers | cpu_percent | CPU 百分比 | 百分比 | Average
是 | Microsoft.DBforMariaDB/servers | io_consumption_percent | IO 百分比 | 百分比 | Average
是 | Microsoft.DBforMariaDB/servers | memory_percent | 記憶體百分比 | 百分比 | Average
是 | Microsoft.DBforMariaDB/servers | network_bytes_egress | Network Out | 位元組 | 總計
是 | Microsoft.DBforMariaDB/servers | network_bytes_ingress | Network In | 位元組 | 總計
是 | Microsoft.DBforMariaDB/servers | seconds_behind_master | 複寫延遲 (秒) | 計數 | 最大值
是 | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | 伺服器記錄儲存體限制 | 位元組 | Average
是 | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | 伺服器記錄儲存體百分比 | 百分比 | Average
是 | Microsoft.DBforMariaDB/servers | serverlog_storage_usage | 使用的伺服器記錄儲存體 | 位元組 | Average
是 | Microsoft.DBforMariaDB/servers | storage_limit | 儲存體限制 | 位元組 | 最大值
是 | Microsoft.DBforMariaDB/servers | storage_percent | 儲存體百分比 | 百分比 | Average
是 | Microsoft.DBforMariaDB/servers | storage_used | 使用的儲存空間 | 位元組 | Average
是 | Microsoft.DBforMySQL/servers | active_connections | 作用中的連線 | 計數 | Average
是 | Microsoft.DBforMySQL/servers | backup_storage_used | 已使用的備份儲存體 | 位元組 | Average
是 | Microsoft.DBforMySQL/servers | connections_failed | 失敗的連線 | 計數 | 總計
是 | Microsoft.DBforMySQL/servers | cpu_percent | CPU 百分比 | 百分比 | Average
是 | Microsoft.DBforMySQL/servers | io_consumption_percent | IO 百分比 | 百分比 | Average
是 | Microsoft.DBforMySQL/servers | memory_percent | 記憶體百分比 | 百分比 | Average
是 | Microsoft.DBforMySQL/servers | network_bytes_egress | Network Out | 位元組 | 總計
是 | Microsoft.DBforMySQL/servers | network_bytes_ingress | Network In | 位元組 | 總計
是 | Microsoft.DBforMySQL/servers | seconds_behind_master | 複寫延遲 (秒) | 計數 | 最大值
是 | Microsoft.DBforMySQL/servers | serverlog_storage_limit | 伺服器記錄儲存體限制 | 位元組 | 最大值
是 | Microsoft.DBforMySQL/servers | serverlog_storage_percent | 伺服器記錄儲存體百分比 | 百分比 | Average
是 | Microsoft.DBforMySQL/servers | serverlog_storage_usage | 使用的伺服器記錄儲存體 | 位元組 | Average
是 | Microsoft.DBforMySQL/servers | storage_limit | 儲存體限制 | 位元組 | 最大值
是 | Microsoft.DBforMySQL/servers | storage_percent | 儲存體百分比 | 百分比 | Average
是 | Microsoft.DBforMySQL/servers | storage_used | 使用的儲存空間 | 位元組 | Average
是 | Microsoft.DBforPostgreSQL/servers | active_connections | 作用中的連線 | 計數 | Average
是 | Microsoft.DBforPostgreSQL/servers | backup_storage_used | 已使用的備份儲存體 | 位元組 | Average
是 | Microsoft.DBforPostgreSQL/servers | connections_failed | 失敗的連線 | 計數 | 總計
是 | Microsoft.DBforPostgreSQL/servers | cpu_percent | CPU 百分比 | 百分比 | Average
是 | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | IO 百分比 | 百分比 | Average
是 | Microsoft.DBforPostgreSQL/servers | memory_percent | 記憶體百分比 | 百分比 | Average
是 | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | Network Out | 位元組 | 總計
是 | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | Network In | 位元組 | 總計
是 | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | 複本之間的最大延隔時間 | 位元組 | 最大值
是 | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | 複本延隔時間 | 秒 | 最大值
是 | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | 伺服器記錄儲存體限制 | 位元組 | 最大值
是 | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | 伺服器記錄儲存體百分比 | 百分比 | Average
是 | Microsoft.DBforPostgreSQL/servers | serverlog_storage_usage | 使用的伺服器記錄儲存體 | 位元組 | Average
是 | Microsoft.DBforPostgreSQL/servers | storage_limit | 儲存體限制 | 位元組 | 最大值
是 | Microsoft.DBforPostgreSQL/servers | storage_percent | 儲存體百分比 | 百分比 | Average
是 | Microsoft.DBforPostgreSQL/servers | storage_used | 使用的儲存空間 | 位元組 | Average
是 | DBforPostgreSQL/serversv2 | active_connections | 作用中的連線 | 計數 | Average
是 | DBforPostgreSQL/serversv2 | cpu_percent | CPU 百分比 | 百分比 | Average
是 | DBforPostgreSQL/serversv2 | 功耗 | IOPS | 計數 | Average
是 | DBforPostgreSQL/serversv2 | memory_percent | 記憶體百分比 | 百分比 | Average
是 | DBforPostgreSQL/serversv2 | network_bytes_egress | Network Out | 位元組 | 總計
是 | DBforPostgreSQL/serversv2 | network_bytes_ingress | Network In | 位元組 | 總計
是 | DBforPostgreSQL/serversv2 | storage_percent | 儲存體百分比 | 百分比 | Average
是 | DBforPostgreSQL/serversv2 | storage_used | 使用的儲存空間 | 位元組 | Average
是 | Microsoft. 裝置/帳戶 | 選取。節點 | 數位 Twins 節點遙測預留位置 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | 已放棄的 C2D 訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success | 已完成 C2D 訊息傳遞 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | 拒絕的 C2D 訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | c2d.methods.failure | 失敗直接方法叫用 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | c2d.methods.requestSize | 直接方法叫用的要求大小 | 位元組 | Average
是 | Microsoft.Devices/IotHubs | c2d.methods.responseSize | 直接方法叫用的回應大小 | 位元組 | Average
是 | Microsoft.Devices/IotHubs | c2d.methods.success | 成功直接方法叫用 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | c2d.twin.read.failure | 後端的失敗對應項讀取 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | c2d.twin.read.size | 後端的對應項讀取回應大小 | 位元組 | Average
是 | Microsoft.Devices/IotHubs | c2d.twin.read.success | 後端的成功對應項讀取 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | c2d.twin.update.failure | 後端的失敗對應項更新 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | c2d.twin.update.size | 後端的對應項更新大小 | 位元組 | Average
是 | Microsoft.Devices/IotHubs | c2d.twin.update.success | 後端的成功對應項更新 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | C2DMessagesExpired | C2D 郵件已過期（預覽） | 計數 | 總計
是 | Microsoft.Devices/IotHubs | 組態 | 設定計量 | 計數 | 總計
否 | Microsoft.Devices/IotHubs | connectedDeviceCount | 連接的裝置 (預覽) | 計數 | Average
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events | 路由：傳遞至訊息/事件的訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | 路由：傳遞至事件中樞的訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | 路由：傳遞至服務匯流排佇列的訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | 路由：傳遞至服務匯流排主題的訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage | 路由：傳遞至儲存體的訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs | 路由：傳遞至儲存體的 BLOB | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes | 路由：傳遞至儲存體的資料 | 位元組 | 總計
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events | 路由：訊息/事件的訊息延遲 | 毫秒 | Average
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | 路由：事件中樞的訊息延遲 | 毫秒 | Average
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | 路由：服務匯流排佇列的訊息延遲 | 毫秒 | Average
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | 路由：服務匯流排主題的訊息延遲 | 毫秒 | Average
是 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage | 路由：儲存體的訊息延遲 | 毫秒 | Average
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | 路由：已捨棄的遙測訊息  | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | 路由：傳遞至後援的訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | 路由：不相容的遙測訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | 路由：已遺棄的遙測訊息  | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | 路由：已傳遞的遙測訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | 遙測訊息傳送嘗試 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | 節流錯誤數目 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | 已傳送的遙測訊息 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.twin.read.failure | 裝置的失敗對應項讀取 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.twin.read.size | 裝置的對應項讀取回應大小 | 位元組 | Average
是 | Microsoft.Devices/IotHubs | d2c.twin.read.success | 裝置的成功對應項讀取 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.twin.update.failure | 裝置的失敗對應項更新 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | d2c.twin.update.size | 裝置的對應項更新大小 | 位元組 | Average
是 | Microsoft.Devices/IotHubs | d2c.twin.update.success | 裝置的成功對應項更新 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | 已使用的訊息總數 | 計數 | Average
是 | Microsoft.Devices/IotHubs | deviceDataUsage | 裝置資料使用量總計 | 位元組 | 總計
是 | Microsoft.Devices/IotHubs | deviceDataUsageV2 | 裝置資料使用量總計 (預覽) | 位元組 | 總計
是 | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | 連接的裝置 (已淘汰)  | 計數 | 總計
是 | Microsoft.Devices/IotHubs | devices.totalDevices | 裝置總計 (已淘汰) | 計數 | 總計
是 | Microsoft.Devices/IotHubs | EventGridDeliveries | 事件方格傳遞（預覽） | 計數 | 總計
是 | Microsoft.Devices/IotHubs | EventGridLatency | 事件方格延遲（預覽） | 毫秒 | Average
是 | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | 取消作業失敗 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.cancelJob.success | 成功取消作業 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.completed | 已完成的工作 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | 建立失敗的方法叫用作業 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | 成功建立的方法叫用作業 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | 建立失敗的對應項更新作業 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | 成功建立的對應項更新作業 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.failed | 失敗作業 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.listJobs.failure | 呼叫列出作業失敗 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.listJobs.success | 成功呼叫列出作業 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | 失敗作業查詢 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | jobs.queryJobs.success | 成功作業查詢 | 計數 | 總計
否 | Microsoft.Devices/IotHubs | totalDeviceCount | 裝置總計 (預覽) | 計數 | Average
是 | Microsoft.Devices/IotHubs | twinQueries.failure | 失敗對應項查詢 | 計數 | 總計
是 | Microsoft.Devices/IotHubs | twinQueries.resultSize | 對應項查詢結果大小 | 位元組 | Average
是 | Microsoft.Devices/IotHubs | twinQueries.success | 成功對應項查詢 | 計數 | 總計
是 | Microsoft.Devices/provisioningServices | AttestationAttempts | 證明嘗試數 | 計數 | 總計
是 | Microsoft.Devices/provisioningServices | DeviceAssignments | 已指派的裝置數 | 計數 | 總計
是 | Microsoft.Devices/provisioningServices | RegistrationAttempts | 註冊嘗試數 | 計數 | 總計
否 | Microsoft.DocumentDB/databaseAccounts | AvailableStorage | 可用的儲存體 | 位元組 | 總計
否 | Microsoft.DocumentDB/databaseAccounts | CassandraConnectionClosures | Cassandra 連線終止 | 計數 | 總計
否 | Microsoft.DocumentDB/databaseAccounts | CassandraRequestCharges | Cassandra 要求費用 | 計數 | 總計
否 | Microsoft.DocumentDB/databaseAccounts | CassandraRequests | Cassandra 要求 | 計數 | 計數
否 | Microsoft.DocumentDB/databaseAccounts | DataUsage | 資料使用量 | 位元組 | 總計
是 | Microsoft.DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | 計數 | 計數
否 | Microsoft.DocumentDB/databaseAccounts | DocumentCount | 文件計數 | 計數 | 總計
否 | Microsoft.DocumentDB/databaseAccounts | DocumentQuota | 文件配額 | 位元組 | 總計
否 | Microsoft.DocumentDB/databaseAccounts | IndexUsage | 索引使用量 | 位元組 | 總計
否 | Microsoft.DocumentDB/databaseAccounts | MetadataRequests | 中繼資料要求 | 計數 | 計數
是 | Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge | Mongo 要求收費 | 計數 | 總計
是 | Microsoft.DocumentDB/databaseAccounts | MongoRequests | Mongo 要求 | 計數 | 計數
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsCount | Mongo 要求率 | 每秒計數 | Average
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsDelete | Mongo 刪除要求率 | 每秒計數 | Average
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsInsert | Mongo 插入要求速率 | 每秒計數 | Average
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsQuery | Mongo 查詢要求率 | 每秒計數 | Average
否 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsUpdate | Mongo 更新要求率 | 每秒計數 | Average
否 | Microsoft.DocumentDB/databaseAccounts | ProvisionedThroughput | 佈建輸送量 | 計數 | 最大值
是 | Microsoft.DocumentDB/databaseAccounts | ReplicationLatency | P99 複寫延遲 | 毫秒 | Average
否 | Microsoft.DocumentDB/databaseAccounts | ServiceAvailability | 服務可用性 | 百分比 | Average
是 | Microsoft.DocumentDB/databaseAccounts | TotalRequests | 要求總數 | 計數 | 計數
是 | Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits | 要求單位總計 | 計數 | 總計
否 | EnterpriseKnowledgeGraph/服務 | FailureCount | 失敗計數 | 計數 | 計數
否 | EnterpriseKnowledgeGraph/服務 | SuccessCount | 成功計數 | 計數 | 計數
否 | EnterpriseKnowledgeGraph/服務 | SuccessLatency | 成功延遲 | 毫秒 | Average
否 | EnterpriseKnowledgeGraph/服務 | TransactionCount | 交易計數 | 計數 | 計數
是 | Microsoft.EventGrid/domains | DeadLetteredCount | 失效信件事件 | 計數 | 總計
否 | Microsoft.EventGrid/domains | DeliveryAttemptFailCount | 傳遞失敗的事件 | 計數 | 總計
是 | Microsoft.EventGrid/domains | DeliverySuccessCount | 已傳遞的事件 | 計數 | 總計
否 | Microsoft.EventGrid/domains | DestinationProcessingDurationInMs | 目的端處理持續期間 | 毫秒 | Average
是 | Microsoft.EventGrid/domains | DroppedEventCount | 卸除的事件 | 計數 | 總計
是 | Microsoft.EventGrid/domains | MatchedEventCount | 相符的事件 | 計數 | 總計
是 | Microsoft.EventGrid/domains | PublishFailCount | 發行失敗的事件 | 計數 | 總計
是 | Microsoft.EventGrid/domains | PublishSuccessCount | 已發佈的事件 | 計數 | 總計
是 | Microsoft.EventGrid/domains | PublishSuccessLatencyInMs | 發佈成功延遲 | 計數 | 總計
是 | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | 失效信件事件 | 計數 | 總計
否 | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | 傳遞失敗的事件 | 計數 | 總計
是 | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | 已傳遞的事件 | 計數 | 總計
否 | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | 目的端處理持續期間 | 毫秒 | Average
是 | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | 卸除的事件 | 計數 | 總計
是 | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | 相符的事件 | 計數 | 總計
是 | Microsoft.EventGrid/extensionTopics | PublishFailCount | 發行失敗的事件 | 計數 | 總計
是 | Microsoft.EventGrid/extensionTopics | PublishSuccessCount | 已發佈的事件 | 計數 | 總計
是 | Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs | 發佈成功延遲 | 計數 | 總計
是 | Microsoft.EventGrid/extensionTopics | UnmatchedEventCount | 不相符的事件 | 計數 | 總計
是 | Microsoft.EventGrid/topics | PublishFailCount | 發行失敗的事件 | 計數 | 總計
是 | Microsoft.EventGrid/topics | PublishSuccessCount | 已發佈的事件 | 計數 | 總計
是 | Microsoft.EventGrid/topics | PublishSuccessLatencyInMs | 發佈成功延遲 | 計數 | 總計
是 | Microsoft.EventGrid/topics | UnmatchedEventCount | 不相符的事件 | 計數 | 總計
否 | Microsoft.EventHub/clusters | ActiveConnections | ActiveConnections | 計數 | Average
否 | Microsoft.EventHub/clusters | AvailableMemory | 可用的記憶體 | 百分比 | 最大值
否 | Microsoft.EventHub/clusters | CaptureBacklog | 擷取待辦項目。 | 計數 | 總計
否 | Microsoft.EventHub/clusters | CapturedBytes | 已擷取的位元組。 | 位元組 | 總計
否 | Microsoft.EventHub/clusters | CapturedMessages | 已擷取的訊息。 | 計數 | 總計
否 | Microsoft.EventHub/clusters | ConnectionsClosed | 已關閉的連線。 | 計數 | Average
否 | Microsoft.EventHub/clusters | ConnectionsOpened | 已開啟的連線。 | 計數 | Average
否 | Microsoft.EventHub/clusters | CPU | CPU | 百分比 | 最大值
是 | Microsoft.EventHub/clusters | IncomingBytes | 傳入位元組數。 | 位元組 | 總計
是 | Microsoft.EventHub/clusters | IncomingMessages | 傳入訊息 | 計數 | 總計
是 | Microsoft.EventHub/clusters | IncomingRequests | 傳入的要求 | 計數 | 總計
是 | Microsoft.EventHub/clusters | OutgoingBytes | 傳出位元組數。 | 位元組 | 總計
是 | Microsoft.EventHub/clusters | OutgoingMessages | 外送訊息 | 計數 | 總計
否 | Microsoft.EventHub/clusters | QuotaExceededErrors | 超出配額的錯誤。 | 計數 | 總計
否 | Microsoft.EventHub/clusters | ServerErrors | 伺服器錯誤。 | 計數 | 總計
否 | Microsoft.EventHub/clusters | SuccessfulRequests | 成功的要求 | 計數 | 總計
否 | Microsoft.EventHub/clusters | ThrottledRequests | 節流的要求。 | 計數 | 總計
否 | Microsoft.EventHub/clusters | UserErrors | 使用者錯誤。 | 計數 | 總計
否 | Microsoft.EventHub/namespaces | ActiveConnections | ActiveConnections | 計數 | Average
否 | Microsoft.EventHub/namespaces | CaptureBacklog | 擷取待辦項目。 | 計數 | 總計
否 | Microsoft.EventHub/namespaces | CapturedBytes | 已擷取的位元組。 | 位元組 | 總計
否 | Microsoft.EventHub/namespaces | CapturedMessages | 已擷取的訊息。 | 計數 | 總計
否 | Microsoft.EventHub/namespaces | ConnectionsClosed | 已關閉的連線。 | 計數 | Average
否 | Microsoft.EventHub/namespaces | ConnectionsOpened | 已開啟的連線。 | 計數 | Average
是 | Microsoft.EventHub/namespaces | EHABL | 封存待處理專案訊息（已淘汰） | 計數 | 總計
是 | Microsoft.EventHub/namespaces | EHAMBS | 封存訊息輸送量（已淘汰） | 位元組 | 總計
是 | Microsoft.EventHub/namespaces | EHAMSGS | 封存訊息（已淘汰） | 計數 | 總計
是 | Microsoft.EventHub/namespaces | EHINBYTES | 傳入位元組數 (已過時) | 位元組 | 總計
是 | Microsoft.EventHub/namespaces | EHINMBS | 傳入位元組數（已過時）（已被取代） | 位元組 | 總計
是 | Microsoft.EventHub/namespaces | EHINMSGS | 連入訊息數 (已過時) | 計數 | 總計
是 | Microsoft.EventHub/namespaces | EHOUTBYTES | 傳出位元組數 (已過時) | 位元組 | 總計
是 | Microsoft.EventHub/namespaces | EHOUTMBS | 傳出位元組數（已過時）（已被取代） | 位元組 | 總計
是 | Microsoft.EventHub/namespaces | EHOUTMSGS | 傳出訊息數 (已過時) | 計數 | 總計
是 | Microsoft.EventHub/namespaces | FAILREQ | 失敗的要求（已淘汰） | 計數 | 總計
是 | Microsoft.EventHub/namespaces | IncomingBytes | 傳入位元組數。 | 位元組 | 總計
是 | Microsoft.EventHub/namespaces | IncomingMessages | 傳入訊息 | 計數 | 總計
是 | Microsoft.EventHub/namespaces | IncomingRequests | 傳入的要求 | 計數 | 總計
是 | Microsoft.EventHub/namespaces | INMSGS | 傳入訊息（已過時）（已淘汰） | 計數 | 總計
是 | Microsoft.EventHub/namespaces | INREQS | 傳入要求（已淘汰） | 計數 | 總計
是 | Microsoft.EventHub/namespaces | INTERR | 內部伺服器錯誤（已淘汰） | 計數 | 總計
是 | Microsoft.EventHub/namespaces | MISCERR | 其他錯誤（已淘汰） | 計數 | 總計
是 | Microsoft.EventHub/namespaces | OutgoingBytes | 傳出位元組數。 | 位元組 | 總計
是 | Microsoft.EventHub/namespaces | OutgoingMessages | 外送訊息 | 計數 | 總計
是 | Microsoft.EventHub/namespaces | OUTMSGS | 外寄訊息（已過時）（已被取代） | 計數 | 總計
否 | Microsoft.EventHub/namespaces | QuotaExceededErrors | 超出配額的錯誤。 | 計數 | 總計
否 | Microsoft.EventHub/namespaces | ServerErrors | 伺服器錯誤。 | 計數 | 總計
否 | Microsoft.EventHub/namespaces | 大小 | 大小 | 位元組 | Average
否 | Microsoft.EventHub/namespaces | SuccessfulRequests | 成功的要求 | 計數 | 總計
是 | Microsoft.EventHub/namespaces | SUCCREQ | 成功的要求（已淘汰） | 計數 | 總計
是 | Microsoft.EventHub/namespaces | SVRBSY | 伺服器忙碌錯誤（已淘汰） | 計數 | 總計
否 | Microsoft.EventHub/namespaces | ThrottledRequests | 節流的要求。 | 計數 | 總計
否 | Microsoft.EventHub/namespaces | UserErrors | 使用者錯誤。 | 計數 | 總計
是 | Microsoft.HDInsight/clusters | CategorizedGatewayRequests | 已分類的閘道要求數 | 計數 | 總計
是 | Microsoft.HDInsight/clusters | GatewayRequests | 閘道要求數 | 計數 | 總計
是 | Microsoft.HDInsight/clusters | NumActiveWorkers | 作用中背景工作角色的數目 | 計數 | 最大值
是 | Microsoft.HDInsight/clusters | ScalingRequests | 調整要求 | 計數 | 最大值
是 | Microsoft.Insights/AutoscaleSettings | MetricThreshold | 計量閾值 | 計數 | Average
是 | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | 觀察的容量 | 計數 | Average
是 | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | 觀察的計量值 | 計數 | Average
是 | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | 已起始的調整規模動作數 | 計數 | 總計
是 | Microsoft.Insights/Components | availabilityResults/availabilityPercentage | 可用性 | 百分比 | Average
否 | Microsoft.Insights/Components | availabilityResults/計數 | 可用性集合 | 計數 | 計數
是 | Microsoft.Insights/Components | availabilityResults/duration | 可用性測試持續時間 | 毫秒 | Average
是 | Microsoft.Insights/Components | browserTimings/networkDuration | 頁面載入網路連線時間 | 毫秒 | Average
是 | Microsoft.Insights/Components | browserTimings/processingDuration | 用戶端處理時間 | 毫秒 | Average
是 | Microsoft.Insights/Components | browserTimings/receiveDuration | 接收回應時間 | 毫秒 | Average
是 | Microsoft.Insights/Components | browserTimings/sendDuration | 傳送要求時間 | 毫秒 | Average
是 | Microsoft.Insights/Components | browserTimings/totalDuration | 瀏覽器頁面載入時間 | 毫秒 | Average
否 | Microsoft.Insights/Components | dependencies/count | 相依性呼叫 | 計數 | 計數
是 | Microsoft.Insights/Components | dependencies/duration | 相依性持續時間 | 毫秒 | Average
否 | Microsoft.Insights/Components | dependencies/failed | 相依性呼叫失敗 | 計數 | 計數
否 | Microsoft.Insights/Components | exceptions/browser | 瀏覽器例外狀況 | 計數 | 計數
是 | Microsoft.Insights/Components | exceptions/count | 例外狀況 | 計數 | 計數
否 | Microsoft.Insights/Components | exceptions/server | 伺服器例外狀況 | 計數 | 計數
是 | Microsoft.Insights/Components | pageViews/count | 頁面檢視 | 計數 | 計數
是 | Microsoft.Insights/Components | pageViews/duration | 頁面檢視載入時間 | 毫秒 | Average
是 | Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond | 例外狀況比率 | 每秒計數 | Average
是 | Microsoft.Insights/Components | performanceCounters/memoryAvailableBytes | 可用的記憶體 | 位元組 | Average
是 | Microsoft.Insights/Components | performanceCounters/processCpuPercentage | 處理程序 CPU | 百分比 | Average
是 | Microsoft.Insights/Components | performanceCounters/processIOBytesPerSecond | 處理程序 IO 速率 | 每秒位元組 | Average
是 | Microsoft.Insights/Components | performanceCounters/processorCpuPercentage | 處理器時間 | 百分比 | Average
是 | Microsoft.Insights/Components | performanceCounters/processPrivateBytes | 處理程序私人位元組 | 位元組 | Average
是 | Microsoft.Insights/Components | performanceCounters/requestExecutionTime | HTTP 要求執行時間 | 毫秒 | Average
是 | Microsoft.Insights/Components | performanceCounters/requestsInQueue | 應用程式佇列中的 HTTP 要求數 | 計數 | Average
是 | Microsoft.Insights/Components | performanceCounters/requestsPerSecond | HTTP 要求率 | 每秒計數 | Average
否 | Microsoft.Insights/Components | requests/count | 伺服器要求 | 計數 | 計數
是 | Microsoft.Insights/Components | requests/duration | 伺服器回應時間 | 毫秒 | Average
否 | Microsoft.Insights/Components | requests/failed | 失敗的要求 | 計數 | 計數
否 | Microsoft.Insights/Components | 要求/速率 | 伺服器要求速率 | 每秒計數 | Average
是 | Microsoft.Insights/Components | traces/count | 追蹤 | 計數 | 計數
是 | Microsoft.KeyVault/vaults | ServiceApiHit | 服務 API 點擊次數總計 | 計數 | 計數
是 | Microsoft.KeyVault/vaults | ServiceApiLatency | 整體服務 API 延遲 | 毫秒 | Average
是 | Microsoft.KeyVault/vaults | ServiceApiResult | 服務 API 結果總計 | 計數 | 計數
是 | Microsoft.Kusto/Clusters | CacheUtilization | 快取使用率 | 百分比 | Average
是 | Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes | 連續匯出最大延遲分鐘數 | 計數 | 最大值
是 | Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported | 連續匯出-匯出記錄的 num | 計數 | 總計
是 | Microsoft.Kusto/Clusters | ContinuousExportPendingCount | 連續匯出暫止計數 | 計數 | 最大值
是 | Microsoft.Kusto/Clusters | ContinuousExportResult | 連續匯出結果 | 計數 | 計數
是 | Microsoft.Kusto/Clusters | CPU | CPU | 百分比 | Average
是 | Microsoft.Kusto/Clusters | EventsProcessedForEventHubs | 已處理的事件（適用于事件/IoT 中樞） | 計數 | 總計
是 | Microsoft.Kusto/Clusters | ExportUtilization | 匯出使用率 | 百分比 | 最大值
是 | Microsoft.Kusto/Clusters | IngestionLatencyInSeconds | 內嵌延遲（以秒為單位） | 秒 | Average
是 | Microsoft.Kusto/Clusters | IngestionResult | 內嵌結果 | 計數 | 計數
是 | Microsoft.Kusto/Clusters | IngestionUtilization | 內嵌使用率 | 百分比 | Average
是 | Microsoft.Kusto/Clusters | IngestionVolumeInMB | 內嵌磁片區（以 MB 為單位） | 計數 | 總計
是 | Microsoft.Kusto/Clusters | 保 | 保持運作 | 計數 | Average
是 | Microsoft.Kusto/Clusters | QueryDuration | 查詢持續時間 | 毫秒 | Average
是 | Microsoft.Kusto/Clusters | SteamingIngestRequestRate | 串流內嵌要求速率 | 計數 | RateRequestsPerSecond
是 | Microsoft.Kusto/Clusters | StreamingIngestDataRate | 串流內嵌資料速率 | 計數 | Average
是 | Microsoft.Kusto/Clusters | StreamingIngestDuration | 串流內嵌持續時間 | 毫秒 | Average
是 | Microsoft.Kusto/Clusters | StreamingIngestResults | 串流內嵌結果 | 計數 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | ActionLatency | 動作延遲  | 秒 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | ActionsCompleted | 完成的動作  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | ActionsFailed | 動作的失敗  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | ActionsSkipped | 略過的動作  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | ActionsStarted | 啟動的動作  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | ActionsSucceeded | 成功的動作  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | ActionSuccessLatency | 動作成功延遲  | 秒 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | ActionThrottledEvents | 動作節流的事件 | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | 整合服務環境的連接器記憶體使用量 | 百分比 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | 整合服務環境的連接器處理器使用量 | 百分比 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | 整合服務環境的工作流程記憶體使用量 | 百分比 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | 整合服務環境的工作流程處理器使用量 | 百分比 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunFailurePercentage | 執行失敗百分比 | 百分比 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunLatency | 執行延遲 | 秒 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunsCancelled | 已取消執行 | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunsCompleted | 完成的執行 | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunsFailed | 失敗的執行 | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunsStarted | 執行已啟動 | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunsSucceeded | 已成功的執行 | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunStartThrottledEvents | 執行啟動節流事件 | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunSuccessLatency | 執行成功的延遲 | 秒 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | RunThrottledEvents | 執行節流的事件 | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggerFireLatency | 觸發程序引發延遲  | 秒 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggerLatency | 觸發程序延遲  | 秒 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggersCompleted | 完成的觸發程序  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggersFailed | 失敗的觸發程序  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggersFired | 引發的觸發程序  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggersSkipped | 略過的觸發程序 | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggersStarted | 啟動的觸發程序  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggersSucceeded | 成功的觸發程序  | 計數 | 總計
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggerSuccessLatency | 觸發程序成功延遲  | 秒 | Average
是 | Microsoft. 邏輯/integrationServiceEnvironments | TriggerThrottledEvents | 觸發程序節流的事件 | 計數 | 總計
是 | Microsoft.Logic/workflows | ActionLatency | 動作延遲  | 秒 | Average
是 | Microsoft.Logic/workflows | ActionsCompleted | 完成的動作  | 計數 | 總計
是 | Microsoft.Logic/workflows | ActionsFailed | 動作的失敗  | 計數 | 總計
是 | Microsoft.Logic/workflows | ActionsSkipped | 略過的動作  | 計數 | 總計
是 | Microsoft.Logic/workflows | ActionsStarted | 啟動的動作  | 計數 | 總計
是 | Microsoft.Logic/workflows | ActionsSucceeded | 成功的動作  | 計數 | 總計
是 | Microsoft.Logic/workflows | ActionSuccessLatency | 動作成功延遲  | 秒 | Average
是 | Microsoft.Logic/workflows | ActionThrottledEvents | 動作節流的事件 | 計數 | 總計
是 | Microsoft.Logic/workflows | BillableActionExecutions | 可計費的動作執行 | 計數 | 總計
是 | Microsoft.Logic/workflows | BillableTriggerExecutions | 可計費的觸發程序執行 | 計數 | 總計
是 | Microsoft.Logic/workflows | BillingUsageNativeOperation | 為使用原生作業執行計費 | 計數 | 總計
是 | Microsoft.Logic/workflows | BillingUsageNativeOperation | 為使用原生作業執行計費 | 計數 | 總計
是 | Microsoft.Logic/workflows | BillingUsageStandardConnector | 為使用標準連接器執行計費 | 計數 | 總計
是 | Microsoft.Logic/workflows | BillingUsageStandardConnector | 為使用標準連接器執行計費 | 計數 | 總計
是 | Microsoft.Logic/workflows | BillingUsageStorageConsumption | 為使用儲存體使用量執行計費 | 計數 | 總計
是 | Microsoft.Logic/workflows | BillingUsageStorageConsumption | 為使用儲存體使用量執行計費 | 計數 | 總計
是 | Microsoft.Logic/workflows | RunFailurePercentage | 執行失敗百分比 | 百分比 | 總計
是 | Microsoft.Logic/workflows | RunLatency | 執行延遲 | 秒 | Average
是 | Microsoft.Logic/workflows | RunsCancelled | 已取消執行 | 計數 | 總計
是 | Microsoft.Logic/workflows | RunsCompleted | 完成的執行 | 計數 | 總計
是 | Microsoft.Logic/workflows | RunsFailed | 失敗的執行 | 計數 | 總計
是 | Microsoft.Logic/workflows | RunsStarted | 執行已啟動 | 計數 | 總計
是 | Microsoft.Logic/workflows | RunsSucceeded | 已成功的執行 | 計數 | 總計
是 | Microsoft.Logic/workflows | RunStartThrottledEvents | 執行啟動節流事件 | 計數 | 總計
是 | Microsoft.Logic/workflows | RunSuccessLatency | 執行成功的延遲 | 秒 | Average
是 | Microsoft.Logic/workflows | RunThrottledEvents | 執行節流的事件 | 計數 | 總計
是 | Microsoft.Logic/workflows | TotalBillableExecutions | 可計費的總執行次數 | 計數 | 總計
是 | Microsoft.Logic/workflows | TriggerFireLatency | 觸發程序引發延遲  | 秒 | Average
是 | Microsoft.Logic/workflows | TriggerLatency | 觸發程序延遲  | 秒 | Average
是 | Microsoft.Logic/workflows | TriggersCompleted | 完成的觸發程序  | 計數 | 總計
是 | Microsoft.Logic/workflows | TriggersFailed | 失敗的觸發程序  | 計數 | 總計
是 | Microsoft.Logic/workflows | TriggersFired | 引發的觸發程序  | 計數 | 總計
是 | Microsoft.Logic/workflows | TriggersSkipped | 略過的觸發程序 | 計數 | 總計
是 | Microsoft.Logic/workflows | TriggersStarted | 啟動的觸發程序  | 計數 | 總計
是 | Microsoft.Logic/workflows | TriggersSucceeded | 成功的觸發程序  | 計數 | 總計
是 | Microsoft.Logic/workflows | TriggerSuccessLatency | 觸發程序成功延遲  | 秒 | Average
是 | Microsoft.Logic/workflows | TriggerThrottledEvents | 觸發程序節流的事件 | 計數 | 總計
是 | Microsoft.MachineLearningServices/workspaces | 現用核心 | 現用核心 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 現用節點 | 現用節點 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 已完成的執行 | 已完成的執行 | 計數 | 總計
是 | Microsoft.MachineLearningServices/workspaces | 失敗的執行 | 失敗的執行 | 計數 | 總計
是 | Microsoft.MachineLearningServices/workspaces | 閒置核心 | 閒置核心 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 閒置節點 | 閒置節點 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 離開核心 | 離開核心 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 離開節點 | 離開節點 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 模型部署失敗 | 模型部署失敗 | 計數 | 總計
是 | Microsoft.MachineLearningServices/workspaces | 模型部署已開始 | 模型部署已開始 | 計數 | 總計
是 | Microsoft.MachineLearningServices/workspaces | 模型部署成功 | 模型部署成功 | 計數 | 總計
是 | Microsoft.MachineLearningServices/workspaces | 模型註冊失敗 | 模型註冊失敗 | 計數 | 總計
是 | Microsoft.MachineLearningServices/workspaces | 模型註冊成功 | 模型註冊成功 | 計數 | 總計
是 | Microsoft.MachineLearningServices/workspaces | 搶先核心 | 搶先核心 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 搶先節點 | 搶先節點 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 配額使用量百分比 | 配額使用量百分比 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 開始執行 | 開始執行 | 計數 | 總計
是 | Microsoft.MachineLearningServices/workspaces | 核心總數 | 核心總數 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 節點總計 | 節點總計 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 無法使用的核心 | 無法使用的核心 | 計數 | Average
是 | Microsoft.MachineLearningServices/workspaces | 無法使用的節點 | 無法使用的節點 | 計數 | Average
是 | Microsoft.Maps/accounts | 可用性 | 可用性 | 百分比 | Average
否 | Microsoft.Maps/accounts | 用量 | 用量 | 計數 | 計數
是 | Microsoft Media/windowsazure.mediaservices.extensions | AssetCount | 資產計數 | 計數 | Average
是 | Microsoft Media/windowsazure.mediaservices.extensions | AssetQuota | 資產配額 | 計數 | Average
是 | Microsoft Media/windowsazure.mediaservices.extensions | AssetQuotaUsedPercentage | 已使用的資產配額百分比 | 百分比 | Average
是 | Microsoft Media/windowsazure.mediaservices.extensions | ContentKeyPolicyCount | 內容金鑰原則計數 | 計數 | Average
是 | Microsoft Media/windowsazure.mediaservices.extensions | ContentKeyPolicyQuota | 內容金鑰原則配額 | 計數 | Average
是 | Microsoft Media/windowsazure.mediaservices.extensions | ContentKeyPolicyQuotaUsedPercentage | 已使用的內容金鑰原則配額百分比 | 百分比 | Average
是 | Microsoft Media/windowsazure.mediaservices.extensions | StreamingPolicyCount | 串流原則計數 | 計數 | Average
是 | Microsoft Media/windowsazure.mediaservices.extensions | StreamingPolicyQuota | 串流原則配額 | 計數 | Average
是 | Microsoft Media/windowsazure.mediaservices.extensions | StreamingPolicyQuotaUsedPercentage | 已使用的串流原則配額百分比 | 百分比 | Average
是 | Microsoft Media/windowsazure.mediaservices.extensions/Streamingendpoint | 輸出 | 輸出 | 位元組 | 總計
是 | Microsoft Media/windowsazure.mediaservices.extensions/Streamingendpoint | 要求 | 要求 | 計數 | 總計
是 | Microsoft Media/windowsazure.mediaservices.extensions/Streamingendpoint | SuccessE2ELatency | 成功的端對端延遲 | 毫秒 | Average
是 | Microservices4Spring/appClusters | GCPauseTotalCount | GC 暫停計數 | 計數 | 總計
是 | Microservices4Spring/appClusters | GCPauseTotalTime | GC 暫停總時間 | 毫秒 | 總計
是 | Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | 可用的舊世代資料大小上限 | 位元組 | Average
是 | Microservices4Spring/appClusters | OldGenMemoryPoolBytes | 舊的產生資料大小 | 位元組 | Average
是 | Microservices4Spring/appClusters | OldGenPromotedBytes | 升級為舊的產生資料大小 | 位元組 | 最大值
是 | Microservices4Spring/appClusters | ServiceCpuUsagePercentage | 服務 CPU 使用量百分比 | 百分比 | Average
是 | Microservices4Spring/appClusters | ServiceMemoryCommitted | 已指派服務記憶體 | 位元組 | Average
是 | Microservices4Spring/appClusters | ServiceMemoryMax | 服務記憶體最大值 | 位元組 | 最大值
是 | Microservices4Spring/appClusters | ServiceMemoryUsed | 使用的服務記憶體 | 位元組 | Average
是 | Microservices4Spring/appClusters | SystemCpuUsagePercentage | 系統 CPU 使用量百分比 | 百分比 | Average
是 | Microservices4Spring/appClusters | TomcatErrorCount | Tomcat 全域錯誤 | 計數 | 總計
是 | Microservices4Spring/appClusters | TomcatReceivedBytes | Tomcat 接收的位元組總數 | 位元組 | 總計
是 | Microservices4Spring/appClusters | TomcatRequestMaxTime | Tomcat 要求最大時間 | 毫秒 | 最大值
是 | Microservices4Spring/appClusters | TomcatRequestTotalCount | Tomcat 要求總計數 | 計數 | 總計
是 | Microservices4Spring/appClusters | TomcatRequestTotalTime | Tomcat 要求總次數 | 毫秒 | 總計
是 | Microservices4Spring/appClusters | TomcatResponseAvgTime | Tomcat 要求平均時間 | 毫秒 | Average
是 | Microservices4Spring/appClusters | TomcatSentBytes | Tomcat 總傳送位元組數 | 位元組 | 總計
是 | Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Tomcat 會話的生存計數 | 計數 | 總計
是 | Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Tomcat 會話最大使用中計數 | 計數 | 總計
是 | Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Tomcat 會話最大的存留時間 | 毫秒 | 最大值
是 | Microservices4Spring/appClusters | TomcatSessionCreatedCount | Tomcat 會話已建立計數 | 計數 | 總計
是 | Microservices4Spring/appClusters | TomcatSessionExpiredCount | Tomcat 會話過期計數 | 計數 | 總計
是 | Microservices4Spring/appClusters | TomcatSessionRejectedCount | Tomcat 會話已拒絕計數 | 計數 | 總計
是 | Microservices4Spring/appClusters | YoungGenPromotedBytes | 升階為年輕產生資料大小 | 位元組 | 最大值
是 | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | 已使用的磁碟區集區配置 | 位元組 | Average
是 | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | 磁碟區集區邏輯大小總計 | 位元組 | Average
是 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區 | AverageReadLatency | 讀取延遲的平均值 | 毫秒 | Average
是 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區 | AverageWriteLatency | 寫入延遲的平均值 | 毫秒 | Average
是 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區 | ReadIops | 讀取 IOPS | 每秒計數 | Average
是 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區 | VolumeLogicalSize | 磁碟區邏輯大小 | 位元組 | Average
是 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區 | VolumeSnapshotSize | 磁碟區快照集大小 | 位元組 | Average
是 | Microsoft NetApp/netAppAccounts/capacityPools/磁片區 | WriteIops | 寫入 IOPS | 每秒計數 | Average
否 | Microsoft.Network/applicationGateways | ApplicationGatewayTotalTime | 應用程式閘道總時間 | 毫秒 | Average
否 | Microsoft.Network/applicationGateways | AvgRequestCountPerHealthyHost | 每部狀況良好主機的每分鐘要求數 | 計數 | Average
否 | Microsoft.Network/applicationGateways | BackendConnectTime | 後端連線時間 | 毫秒 | Average
否 | Microsoft.Network/applicationGateways | BackendFirstByteResponseTime | 後端第一個位元組回應時間 | 毫秒 | Average
否 | Microsoft.Network/applicationGateways | BackendLastByteResponseTime | 後端上次位元組回應時間 | 毫秒 | Average
是 | Microsoft.Network/applicationGateways | BackendResponseStatus | 後端回應狀態 | 計數 | 總計
是 | Microsoft.Network/applicationGateways | BlockedCount | Web 應用程式防火牆封鎖要求規則散發 | 計數 | 總計
是 | Microsoft.Network/applicationGateways | BlockedReqCount | Web 應用程式防火牆封鎖的要求計數 | 計數 | 總計
是 | Microsoft.Network/applicationGateways | BytesReceived | 接收的位元組數 | 位元組 | 總計
是 | Microsoft.Network/applicationGateways | BytesSent | 傳送的位元組數 | 位元組 | 總計
否 | Microsoft.Network/applicationGateways | CapacityUnits | 目前的容量單位 | 計數 | Average
否 | Microsoft.Network/applicationGateways | ClientRtt | 用戶端 RTT | 毫秒 | Average
否 | Microsoft.Network/applicationGateways | ComputeUnits | 目前的計算單位 | 計數 | Average
是 | Microsoft.Network/applicationGateways | CurrentConnections | 目前的連線數 | 計數 | 總計
是 | Microsoft.Network/applicationGateways | FailedRequests | 失敗的要求 | 計數 | 總計
是 | Microsoft.Network/applicationGateways | HealthyHostCount | 狀況良好的主機計數 | 計數 | Average
是 | Microsoft.Network/applicationGateways | MatchedCount | Web 應用程式防火牆的規則散發總數 | 計數 | 總計
是 | Microsoft.Network/applicationGateways | ResponseStatus | 回應狀態 | 計數 | 總計
否 | Microsoft.Network/applicationGateways | 輸送量 | 輸送量 | 每秒位元組 | Average
是 | Microsoft.Network/applicationGateways | TlsProtocol | 用戶端 TLS 通訊協定 | 計數 | 總計
是 | Microsoft.Network/applicationGateways | TotalRequests | 要求總數 | 計數 | 總計
是 | Microsoft.Network/applicationGateways | UnhealthyHostCount | 狀況不良的主機計數 | 計數 | Average
是 | Microsoft.Network/azurefirewalls | ApplicationRuleHit | 應用程式規則計數 | 計數 | 總計
是 | Microsoft.Network/azurefirewalls | DataProcessed | 已處理的資料量 | 位元組 | 總計
是 | Microsoft.Network/azurefirewalls | FirewallHealth | 防火牆健全狀況狀態 | 百分比 | Average
是 | Microsoft.Network/azurefirewalls | NetworkRuleHit | 網路規則計數 | 計數 | 總計
是 | Microsoft.Network/azurefirewalls | SNATPortUtilization | SNAT 埠使用率 | 百分比 | Average
是 | Microsoft.Network/connections | BitsInPerSecond | BitsInPerSecond | 每秒計數 | Average
是 | Microsoft.Network/connections | BitsOutPerSecond | BitsOutPerSecond | 每秒計數 | Average
是 | Microsoft.Network/dnszones | QueryVolume | 查詢磁碟區 | 計數 | 總計
否 | Microsoft.Network/dnszones | RecordSetCapacityUtilization | 記錄集容量使用率 | 百分比 | 最大值
是 | Microsoft.Network/dnszones | RecordSetCount | 記錄集計數 | 計數 | 最大值
是 | Microsoft.Network/expressRouteCircuits | ArpAvailability | Arp 可用性 | 百分比 | Average
是 | Microsoft.Network/expressRouteCircuits | BgpAvailability | Bgp 可用性 | 百分比 | Average
否 | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | 每秒計數 | Average
否 | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | 每秒計數 | Average
否 | Microsoft.Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | 每秒計數 | Average
否 | Microsoft.Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | 每秒計數 | Average
否 | Microsoft.Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | 每秒計數 | Average
否 | Microsoft.Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | 每秒計數 | Average
是 | Microsoft.Network/expressRouteCircuits/peerings | BitsInPerSecond | BitsInPerSecond | 每秒計數 | Average
是 | Microsoft.Network/expressRouteCircuits/peerings | BitsOutPerSecond | BitsOutPerSecond | 每秒計數 | Average
否 | Microsoft 網路/expressRouteGateways | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | 每秒計數 | Average
否 | Microsoft 網路/expressRouteGateways | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | 每秒計數 | Average
是 | Microsoft 網路/expressRoutePorts | AdminState | AdminState | 計數 | Average
是 | Microsoft 網路/expressRoutePorts | LineProtocol | LineProtocol | 計數 | Average
是 | Microsoft 網路/expressRoutePorts | PortBitsInPerSecond | BitsInPerSecond | 每秒計數 | Average
是 | Microsoft 網路/expressRoutePorts | PortBitsOutPerSecond | BitsOutPerSecond | 每秒計數 | Average
是 | Microsoft 網路/expressRoutePorts | RxLightLevel | RxLightLevel | 計數 | Average
是 | Microsoft 網路/expressRoutePorts | TxLightLevel | TxLightLevel | 計數 | Average
是 | Microsoft.Network/frontdoors | BackendHealthPercentage | 後端健康情況百分比 | 百分比 | Average
是 | Microsoft.Network/frontdoors | BackendRequestCount | 後端要求計數 | 計數 | 總計
是 | Microsoft.Network/frontdoors | BackendRequestLatency | 後端要求延遲 | 毫秒 | Average
是 | Microsoft.Network/frontdoors | BillableResponseSize | 可計費回應大小 | 位元組 | 總計
是 | Microsoft.Network/frontdoors | RequestCount | 要求計數 | 計數 | 總計
是 | Microsoft.Network/frontdoors | RequestSize | 要求大小 | 位元組 | 總計
是 | Microsoft.Network/frontdoors | ResponseSize | 回應大小 | 位元組 | 總計
是 | Microsoft.Network/frontdoors | TotalLatency | 延遲總計 | 毫秒 | Average
是 | Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount | Web 應用程式防火牆要求計數 | 計數 | 總計
否 | Microsoft.Network/loadBalancers | AllocatedSnatPorts | 配置的 SNAT 連接埠 (預覽) | 計數 | 總計
是 | Microsoft.Network/loadBalancers | ByteCount | 位元組計數 | 計數 | 總計
是 | Microsoft.Network/loadBalancers | DipAvailability | 健全狀況探查狀態 | 計數 | Average
是 | Microsoft.Network/loadBalancers | PacketCount | 封包計數 | 計數 | 總計
是 | Microsoft.Network/loadBalancers | SnatConnectionCount | SNAT 連線計數 | 計數 | 總計
是 | Microsoft.Network/loadBalancers | SYNCount | SYN 計數 | 計數 | 總計
否 | Microsoft.Network/loadBalancers | UsedSnatPorts | 使用的 SNAT 連接埠 (預覽) | 計數 | 總計
是 | Microsoft.Network/loadBalancers | VipAvailability | 資料路徑可用性 | 計數 | Average
是 | Microsoft.Network/networkInterfaces | BytesReceivedRate | 接收的位元組數 | 位元組 | 總計
是 | Microsoft.Network/networkInterfaces | BytesSentRate | 傳送的位元組數 | 位元組 | 總計
是 | Microsoft.Network/networkInterfaces | PacketsReceivedRate | 接收的封包 | 計數 | 總計
是 | Microsoft.Network/networkInterfaces | PacketsSentRate | 傳送的封包 | 計數 | 總計
是 | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | 平均來回時間（毫秒） | 毫秒 | Average
是 | Microsoft.Network/networkWatchers/connectionMonitors | ChecksFailedPercent | 檢查失敗百分比（預覽） | 百分比 | Average
是 | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | 失敗的探查百分比 | 百分比 | Average
是 | Microsoft.Network/networkWatchers/connectionMonitors | RoundTripTimeMs | 來回時間（毫秒）（預覽） | 毫秒 | Average
是 | Microsoft.Network/publicIPAddresses | ByteCount | 位元組計數 | 計數 | 總計
是 | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | 傳入位元組數捨棄 DDoS | 每秒位元組 | 最大值
是 | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | 傳入位元組數轉送 DDoS | 每秒位元組 | 最大值
是 | Microsoft.Network/publicIPAddresses | BytesInDDoS | 傳入位元組數 DDoS | 每秒位元組 | 最大值
是 | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | 用以觸發 DDoS 風險降低措施的輸入 SYN 封包數 | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | 傳入 TCP 封包數以觸發 DDoS 緩和 | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | 傳入 UDP 封包數以觸發 DDoS 緩和 | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | 是否正遭受 DDoS 攻擊 | 計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | PacketCount | 封包計數 | 計數 | 總計
是 | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | 傳入封包捨棄 DDoS | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | 傳入封包轉寄 DDoS | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | PacketsInDDoS | 傳入封包 DDoS | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | SynCount | SYN 計數 | 計數 | 總計
是 | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | 傳入 TCP 位元組數捨棄 DDoS | 每秒位元組 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | 傳入 TCP 位元組數轉送 DDoS | 每秒位元組 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | 傳入 TCP 位元組數 DDoS | 每秒位元組 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | 傳入 TCP 封包捨棄 DDoS | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | 傳入 TCP 封包轉送 DDoS | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | 傳入 TCP 封包 DDoS | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | 傳入 UDP 位元組數捨棄 DDoS | 每秒位元組 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | 傳入 UDP 位元組數轉送 DDoS | 每秒位元組 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | 傳入 UDP 位元組數 DDoS | 每秒位元組 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | 傳入 UDP 封包捨棄 DDoS | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | 傳入 UDP 封包轉送 DDoS | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | 傳入 UDP 封包 DDoS | 每秒計數 | 最大值
是 | Microsoft.Network/publicIPAddresses | VipAvailability | 資料路徑可用性 | 計數 | Average
是 | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | 依端點的端點狀態 | 計數 | 最大值
是 | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | 傳回的依端點查詢數 | 計數 | 總計
是 | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | 閘道 S2S 頻寬 | 每秒位元組 | Average
是 | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | 閘道 S2S 頻寬 | 每秒位元組 | Average
是 | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | P2S 連線計數 | 計數 | 最大值
是 | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | 通道頻寬 | 每秒位元組 | Average
是 | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | 通道輸出位元組數 | 位元組 | 總計
是 | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | 通道輸出 TS 不相符封包捨棄 | 計數 | 總計
是 | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | 通道輸出封包數 | 計數 | 總計
是 | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | 通道輸入位元組數 | 位元組 | 總計
是 | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | 通道輸入 TS 不符合封包捨棄 | 計數 | 總計
是 | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | 通道輸入封包數 | 計數 | 總計
是 | Microsoft.Network/virtualNetworks | PingMeshAverageRoundtripMs | Ping 至 VM 的來回時間 | 毫秒 | Average
是 | Microsoft.Network/virtualNetworks | PingMeshProbesFailedPercent | Ping 至 VM 失敗 | 百分比 | Average
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming | 傳入訊息 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests | 所有傳入的失敗要求 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests | 所有傳入要求 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled | 傳送已排程的推播通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel | 取消已排程的推播通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all | 安裝管理作業 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete | 刪除安裝作業 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get | 取得安裝作業 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch | 修補安裝作業 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert | 建立或更新安裝作業 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes | 所有外寄通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.badorexpiredchannel | 錯誤或過期的通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror | 通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload | 承載錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror | 外部通知系統錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success | 成功通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel | APNS 不正確的通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel | APNS 過期通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials | APNS 授權錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize | APNS 無效通知大小錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror | APNS 錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success | APNS 成功通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror | GCM 驗證錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel | GCM 不正確的通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel | GCM 過期通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials | GCM 授權錯誤 (無效認證) | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat | GCM 無效通知格式 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize | GCM 無效通知大小錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror | GCM 錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success | GCM 成功通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled | GCM 已將通知節流處理 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel | GCM 不正確的通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror | MPNS 驗證錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel | MPNS 不正確的通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected | MPNS 通道已中斷連線 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped | MPNS 已捨棄通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials | MPNS 無效認證 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat | MPNS 無效通知格式 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror | MPNS 錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success | MPNS 成功通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled | MPNS 已將通知節流處理 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror | WNS 驗證錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel | WNS 不正確的通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected | WNS 通道已中斷連線 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled | WNS 通道已節流處理 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped | WNS 已捨棄通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel | WNS 過期通道錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials | WNS 授權錯誤 (無效認證) | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat | WNS 無效通知格式 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize | WNS 無效通知大小錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken | WNS 授權錯誤 (無效權杖) | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror | WNS 錯誤 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success | WNS 成功通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled | WNS 節流通知 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable | WNS 授權錯誤 (無法連線) | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken | WNS 授權錯誤 (錯誤權杖) | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all | 註冊作業數 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create | 註冊建立作業 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete | 註冊刪除作業 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get | 註冊讀取作業 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update | 註冊更新作業 | 計數 | 總計
是 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending | 暫止已排程的通知 | 計數 | 總計
是 | Microsoft.OperationalInsights/workspaces | Average_% Available Memory | % Available Memory | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Available Swap Space | % Available Swap Space | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Committed Bytes In Use | % Committed Bytes In Use | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% DPC Time | % DPC Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Free Inodes | % Free Inodes | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Free Space | % Free Space | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Free Space | % Free Space | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Idle Time | % Idle Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Interrupt Time | % Interrupt Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% IO Wait Time | % IO Wait Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Nice Time | % Nice Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Privileged Time | % Privileged Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | % Processor Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | % Processor Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Used Inodes | % Used Inodes | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Used Memory | % Used Memory | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Used Space | % Used Space | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% Used Swap Space | % Used Swap Space | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_% User Time | % User Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Available MBytes | 可用的 MB | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Memory | Available MBytes Memory | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Swap | Available MBytes Swap | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Read | Avg. Disk sec/Read | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Read | Avg. Disk sec/Read | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Transfer | Avg. Disk sec/Transfer | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Write | Avg. Disk sec/Write | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Write | Avg. Disk sec/Write | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Bytes Received/sec | Bytes Received/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Bytes Sent/sec | Bytes Sent/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Bytes Total/sec | Bytes Total/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Current Disk Queue Length | Current Disk Queue Length | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Read Bytes/sec | Disk Read Bytes/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | Disk Reads/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | Disk Reads/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | Disk Transfers/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | Disk Transfers/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Write Bytes/sec | Disk Write Bytes/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | Disk Writes/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | Disk Writes/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Free Megabytes | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Free Megabytes | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Free Physical Memory | Free Physical Memory | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Free Space in Paging Files | Free Space in Paging Files | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Free Virtual Memory | Free Virtual Memory | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Logical Disk Bytes/sec | Logical Disk Bytes/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Page Reads/sec | Page Reads/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Page Writes/sec | Page Writes/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Pages/sec | Pages/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Pct Privileged Time | Pct Privileged Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Pct User Time | Pct User Time | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Physical Disk Bytes/sec | Physical Disk Bytes/sec | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Processes | 程序 | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Processor Queue Length | 處理器佇列長度 | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Size Stored In Paging Files | Size Stored In Paging Files | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Total Bytes | Total Bytes | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Received | Total Bytes Received | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Transmitted | Total Bytes Transmitted | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Total Collisions | Total Collisions | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Total Packets Received | Total Packets Received | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Total Packets Transmitted | Total Packets Transmitted | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Total Rx Errors | Total Rx Errors | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Total Tx Errors | Total Tx Errors | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Uptime | Uptime | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Used MBytes Swap Space | Used MBytes Swap Space | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Used Memory kBytes | Used Memory kBytes | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Used Memory MBytes | Used Memory MBytes | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Users | 使用者人數 | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Average_Virtual Shared Memory | Virtual Shared Memory | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | 活動 | 活動 | 計數 | Average
是 | Microsoft.OperationalInsights/workspaces | Heartbeat | Heartbeat | 計數 | 總計
是 | Microsoft.OperationalInsights/workspaces | 更新 | 更新 | 計數 | Average
是 | Microsoft.PowerBIDedicated/capacities | memory_metric | 記憶體 | 位元組 | Average
是 | Microsoft.PowerBIDedicated/capacities | memory_thrashing_metric | 記憶體抖動（資料集） | 百分比 | Average
是 | Microsoft.PowerBIDedicated/capacities | qpu_high_utilization_metric | QPU 高使用率 | 計數 | 總計
是 | Microsoft.PowerBIDedicated/capacities | QueryDuration | 查詢持續時間（資料集） | 毫秒 | Average
是 | Microsoft.PowerBIDedicated/capacities | QueryPoolJobQueueLength | 查詢集區作業佇列長度（資料集） | 計數 | Average
否 | Microsoft.Relay/namespaces | ActiveConnections | ActiveConnections | 計數 | 總計
否 | Microsoft.Relay/namespaces | ActiveListeners | ActiveListeners | 計數 | 總計
是 | Microsoft.Relay/namespaces | BytesTransferred | BytesTransferred | 計數 | 總計
否 | Microsoft.Relay/namespaces | ListenerConnections-ClientError | ListenerConnections-ClientError | 計數 | 總計
否 | Microsoft.Relay/namespaces | ListenerConnections-ServerError | ListenerConnections-ServerError | 計數 | 總計
否 | Microsoft.Relay/namespaces | ListenerConnections-Success | ListenerConnections-Success | 計數 | 總計
否 | Microsoft.Relay/namespaces | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | 計數 | 總計
否 | Microsoft.Relay/namespaces | ListenerDisconnects | ListenerDisconnects | 計數 | 總計
否 | Microsoft.Relay/namespaces | SenderConnections-ClientError | SenderConnections-ClientError | 計數 | 總計
否 | Microsoft.Relay/namespaces | SenderConnections-ServerError | SenderConnections-ServerError | 計數 | 總計
否 | Microsoft.Relay/namespaces | SenderConnections-Success | SenderConnections-Success | 計數 | 總計
否 | Microsoft.Relay/namespaces | SenderConnections-TotalRequests | SenderConnections-TotalRequests | 計數 | 總計
否 | Microsoft.Relay/namespaces | SenderDisconnects | SenderDisconnects | 計數 | 總計
是 | Microsoft.Search/searchServices | SearchLatency | 搜尋延遲 | 秒 | Average
是 | Microsoft.Search/searchServices | SearchQueriesPerSecond | 每秒搜尋查詢 | 每秒計數 | Average
是 | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | 節流的搜尋查詢百分比 | 百分比 | Average
否 | Microsoft.ServiceBus/namespaces | ActiveConnections | ActiveConnections | 計數 | 總計
否 | Microsoft.ServiceBus/namespaces | ActiveMessages | 佇列/主題中的作用中訊息計數。 | 計數 | Average
否 | Microsoft.ServiceBus/namespaces | ConnectionsClosed | 已關閉的連線。 | 計數 | Average
否 | Microsoft.ServiceBus/namespaces | ConnectionsOpened | 已開啟的連線。 | 計數 | Average
否 | Microsoft.ServiceBus/namespaces | CPUXNS | CPU （已淘汰） | 百分比 | 最大值
否 | Microsoft.ServiceBus/namespaces | DeadletteredMessages | 佇列/主題中的無效字母訊息計數。 | 計數 | Average
是 | Microsoft.ServiceBus/namespaces | IncomingMessages | 傳入訊息 | 計數 | 總計
是 | Microsoft.ServiceBus/namespaces | IncomingRequests | 傳入的要求 | 計數 | 總計
否 | Microsoft.ServiceBus/namespaces | 訊息 | 佇列/主題中的訊息計數。 | 計數 | Average
否 | Microsoft.ServiceBus/namespaces | NamespaceCpuUsage | CPU | 百分比 | 最大值
否 | Microsoft.ServiceBus/namespaces | NamespaceMemoryUsage | 記憶體使用量 | 百分比 | 最大值
是 | Microsoft.ServiceBus/namespaces | OutgoingMessages | 外送訊息 | 計數 | 總計
否 | Microsoft.ServiceBus/namespaces | ScheduledMessages | 佇列/主題中已排程的訊息計數。 | 計數 | Average
否 | Microsoft.ServiceBus/namespaces | ServerErrors | 伺服器錯誤。 | 計數 | 總計
否 | Microsoft.ServiceBus/namespaces | 大小 | 大小 | 位元組 | Average
否 | Microsoft.ServiceBus/namespaces | SuccessfulRequests | 成功的要求 | 計數 | 總計
否 | Microsoft.ServiceBus/namespaces | ThrottledRequests | 節流的要求。 | 計數 | 總計
否 | Microsoft.ServiceBus/namespaces | UserErrors | 使用者錯誤。 | 計數 | 總計
否 | Microsoft.ServiceBus/namespaces | WSXNS | 記憶體使用量（已淘汰） | 百分比 | 最大值
否 | ServiceFabricMesh/應用程式 | ActualCpu | ActualCpu | 計數 | Average
否 | ServiceFabricMesh/應用程式 | ActualMemory | ActualMemory | 位元組 | Average
否 | ServiceFabricMesh/應用程式 | AllocatedCpu | AllocatedCpu | 計數 | Average
否 | ServiceFabricMesh/應用程式 | AllocatedMemory | AllocatedMemory | 位元組 | Average
否 | ServiceFabricMesh/應用程式 | ApplicationStatus | ApplicationStatus | 計數 | Average
否 | ServiceFabricMesh/應用程式 | ContainerStatus | ContainerStatus | 計數 | Average
否 | ServiceFabricMesh/應用程式 | CpuUtilization | CpuUtilization | 百分比 | Average
否 | ServiceFabricMesh/應用程式 | MemoryUtilization | MemoryUtilization | 百分比 | Average
否 | ServiceFabricMesh/應用程式 | RestartCount | RestartCount | 計數 | Average
否 | ServiceFabricMesh/應用程式 | ServiceReplicaStatus | ServiceReplicaStatus | 計數 | Average
否 | ServiceFabricMesh/應用程式 | ServiceStatus | ServiceStatus | 計數 | Average
是 | Microsoft.SignalRService/SignalR | ConnectionCount | 連線計數 | 計數 | 最大值
是 | Microsoft.SignalRService/SignalR | InboundTraffic | 輸入流量 | 位元組 | 總計
是 | Microsoft.SignalRService/SignalR | MessageCount | 訊息計數 | 計數 | 總計
是 | Microsoft.SignalRService/SignalR | OutboundTraffic | 輸出流量 | 位元組 | 總計
是 | Microsoft.SignalRService/SignalR | SystemErrors | 系統錯誤 | 百分比 | 最大值
是 | Microsoft.SignalRService/SignalR | UserErrors | 使用者錯誤 | 百分比 | 最大值
是 | Microsoft.Sql/managedInstances | avg_cpu_percent | CPU 百分比平均 | 百分比 | Average
是 | Microsoft.Sql/managedInstances | io_bytes_read | 讀取的 IO 位元組 | 位元組 | Average
是 | Microsoft.Sql/managedInstances | io_bytes_written | 寫入的 IO 位元組 | 位元組 | Average
是 | Microsoft.Sql/managedInstances | io_requests | IO 要求計數 | 計數 | Average
是 | Microsoft.Sql/managedInstances | reserved_storage_mb | 保留的儲存體空間 | 計數 | Average
是 | Microsoft.Sql/managedInstances | storage_space_used_mb | 使用的儲存體空間 | 計數 | Average
是 | Microsoft.Sql/managedInstances | virtual_core_count | 虛擬核心計數 | 計數 | Average
否 | Microsoft.Sql/servers | database_dtu_consumption_percent | DTU 百分比 | 百分比 | Average
否 | Microsoft.Sql/servers | database_storage_used | 使用的資料空間 | 位元組 | Average
是 | Microsoft.Sql/servers | dtu_consumption_percent | DTU 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers | dtu_used | 已使用 DTU | 計數 | Average
是 | Microsoft.Sql/servers | storage_used | 使用的資料空間 | 位元組 | Average
是 | Microsoft.Sql/servers/databases | allocated_data_storage | 已配置的資料空間 | 位元組 | Average
是 | Microsoft.Sql/servers/databases | app_cpu_billed | 應用程式 CPU 計費 | 計數 | 總計
是 | Microsoft.Sql/servers/databases | app_cpu_percent | 應用程式 CPU 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/databases | app_memory_percent | 應用程式記憶體百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/databases | blocked_by_firewall | 遭到防火牆封鎖 | 計數 | 總計
是 | Microsoft.Sql/servers/databases | cache_hit_percent | 快取命中的百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | cache_used_percent | 已用快取的百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | connection_failed | 失敗的連線 | 計數 | 總計
是 | Microsoft.Sql/servers/databases | connection_successful | 成功的連線 | 計數 | 總計
是 | Microsoft.Sql/servers/databases | cpu_limit | CPU 限制 | 計數 | Average
是 | Microsoft.Sql/servers/databases | cpu_percent | CPU 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/databases | cpu_used | 使用的 CPU | 計數 | Average
是 | Microsoft.Sql/servers/databases | deadlock | 死結 | 計數 | 總計
是 | Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/databases | dtu_limit | DTU 限制 | 計數 | Average
是 | Microsoft.Sql/servers/databases | dtu_used | 已使用 DTU | 計數 | Average
是 | Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU 百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | dwu_limit | DWU 限制 | 計數 | 最大值
是 | Microsoft.Sql/servers/databases | dwu_used | 已使用 DWU | 計數 | 最大值
是 | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | 本機 tempdb 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/databases | log_write_percent | 記錄 IO 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/databases | memory_usage_percent | 記憶體百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | physical_data_read_percent | 資料 IO 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/databases | sessions_percent | 工作階段百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/databases | 儲存體 | 使用的資料空間 | 位元組 | 最大值
是 | Microsoft.Sql/servers/databases | storage_percent | 使用的資料空間百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | tempdb_data_size | Tempdb 資料檔案大小 Kb | 計數 | 最大值
是 | Microsoft.Sql/servers/databases | tempdb_log_size | Tempdb 記錄檔大小 Kb | 計數 | 最大值
是 | Microsoft.Sql/servers/databases | tempdb_log_used_percent | 使用的 Tempdb 百分比記錄 | 百分比 | 最大值
是 | Microsoft.Sql/servers/databases | workers_percent | 背景工作角色百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/databases | xtp_storage_percent | 記憶體內部 OLTP 儲存體百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/elasticPools | allocated_data_storage | 已配置的資料空間 | 位元組 | Average
是 | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | 已配置的資料空間百分比 | 百分比 | 最大值
是 | Microsoft.Sql/servers/elasticPools | cpu_limit | CPU 限制 | 計數 | Average
是 | Microsoft.Sql/servers/elasticPools | cpu_percent | CPU 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/elasticPools | cpu_used | 使用的 CPU | 計數 | Average
否 | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | 已配置的資料空間 | 位元組 | Average
否 | Microsoft.Sql/servers/elasticPools | database_cpu_limit | CPU 限制 | 計數 | Average
否 | Microsoft.Sql/servers/elasticPools | database_cpu_percent | CPU 百分比 | 百分比 | Average
否 | Microsoft.Sql/servers/elasticPools | database_cpu_used | 使用的 CPU | 計數 | Average
否 | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | DTU 百分比 | 百分比 | Average
否 | Microsoft.Sql/servers/elasticPools | database_eDTU_used | 已使用 eDTU | 計數 | Average
否 | Microsoft.Sql/servers/elasticPools | database_log_write_percent | 記錄 IO 百分比 | 百分比 | Average
否 | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | 資料 IO 百分比 | 百分比 | Average
否 | Microsoft.Sql/servers/elasticPools | database_sessions_percent | 工作階段百分比 | 百分比 | Average
否 | Microsoft.Sql/servers/elasticPools | database_storage_used | 使用的資料空間 | 位元組 | Average
否 | Microsoft.Sql/servers/elasticPools | database_workers_percent | 背景工作角色百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/elasticPools | eDTU_limit | eDTU 限制 | 計數 | Average
是 | Microsoft.Sql/servers/elasticPools | eDTU_used | 已使用 eDTU | 計數 | Average
是 | Microsoft.Sql/servers/elasticPools | log_write_percent | 記錄 IO 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | 資料 IO 百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/elasticPools | sessions_percent | 工作階段百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/elasticPools | storage_limit | 資料大小上限 | 位元組 | Average
是 | Microsoft.Sql/servers/elasticPools | storage_percent | 使用的資料空間百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/elasticPools | storage_used | 使用的資料空間 | 位元組 | Average
是 | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Tempdb 資料檔案大小 Kb | 計數 | 最大值
是 | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Tempdb 記錄檔大小 Kb | 計數 | 最大值
是 | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | 使用的 Tempdb 百分比記錄 | 百分比 | 最大值
是 | Microsoft.Sql/servers/elasticPools | workers_percent | 背景工作角色百分比 | 百分比 | Average
是 | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | 記憶體內部 OLTP 儲存體百分比 | 百分比 | Average
是 | Microsoft.Storage/storageAccounts | 可用性 | 可用性 | 百分比 | Average
是 | Microsoft.Storage/storageAccounts | 輸出 | 輸出 | 位元組 | 總計
是 | Microsoft.Storage/storageAccounts | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.Storage/storageAccounts | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts | 交易 | 交易 | 計數 | 總計
否 | Microsoft.Storage/storageAccounts | UsedCapacity | 已使用容量 | 位元組 | Average
是 | Microsoft.Storage/storageAccounts/blobServices | 可用性 | 可用性 | 百分比 | Average
否 | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Blob 容量 | 位元組 | Average
否 | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Blob 計數 | 計數 | Average
是 | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Blob 容器計數 | 計數 | Average
是 | Microsoft.Storage/storageAccounts/blobServices | 輸出 | 輸出 | 位元組 | 總計
否 | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | 索引容量 | 位元組 | Average
是 | Microsoft.Storage/storageAccounts/blobServices | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts/blobServices | 交易 | 交易 | 計數 | 總計
是 | Microsoft.Storage/storageAccounts/fileServices | 可用性 | 可用性 | 百分比 | Average
是 | Microsoft.Storage/storageAccounts/fileServices | 輸出 | 輸出 | 位元組 | 總計
否 | Microsoft.Storage/storageAccounts/fileServices | FileCapacity | 檔案容量 | 位元組 | Average
否 | Microsoft.Storage/storageAccounts/fileServices | FileCount | 檔案計數 | 計數 | Average
否 | Microsoft.Storage/storageAccounts/fileServices | FileShareCount | 檔案共用計數 | 計數 | Average
否 | Microsoft.Storage/storageAccounts/fileServices | FileShareQuota | 檔案共用配額大小 | 位元組 | Average
否 | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotCount | 檔案共用快照集計數 | 計數 | Average
否 | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotSize | 檔案共用快照集大小 | 位元組 | Average
是 | Microsoft.Storage/storageAccounts/fileServices | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.Storage/storageAccounts/fileServices | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts/fileServices | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts/fileServices | 交易 | 交易 | 計數 | 總計
是 | Microsoft.Storage/storageAccounts/queueServices | 可用性 | 可用性 | 百分比 | Average
是 | Microsoft.Storage/storageAccounts/queueServices | 輸出 | 輸出 | 位元組 | 總計
是 | Microsoft.Storage/storageAccounts/queueServices | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | 佇列容量 | 位元組 | Average
是 | Microsoft.Storage/storageAccounts/queueServices | QueueCount | 佇列計數 | 計數 | Average
是 | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | 佇列訊息計數 | 計數 | Average
是 | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts/queueServices | 交易 | 交易 | 計數 | 總計
是 | Microsoft.Storage/storageAccounts/tableServices | 可用性 | 可用性 | 百分比 | Average
是 | Microsoft.Storage/storageAccounts/tableServices | 輸出 | 輸出 | 位元組 | 總計
是 | Microsoft.Storage/storageAccounts/tableServices | 輸入 | 輸入 | 位元組 | 總計
是 | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | 成功 E2E 延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | 成功伺服器延遲 | 毫秒 | Average
是 | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | 資料表容量 | 位元組 | Average
是 | Microsoft.Storage/storageAccounts/tableServices | TableCount | 資料表計數 | 計數 | Average
是 | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | 資料表實體計數 | 計數 | Average
是 | Microsoft.Storage/storageAccounts/tableServices | 交易 | 交易 | 計數 | 總計
是 | StorageCache/快取 | ClientIOPS | 用戶端 IOPS 總計 | 計數 | Average
是 | StorageCache/快取 | ClientLatency | 平均用戶端延遲 | 毫秒 | Average
是 | StorageCache/快取 | ClientLockIOPS | 用戶端鎖定 IOPS | 每秒計數 | Average
是 | StorageCache/快取 | ClientMetadataReadIOPS | 用戶端中繼資料讀取 IOPS | 每秒計數 | Average
是 | StorageCache/快取 | ClientMetadataWriteIOPS | 用戶端中繼資料寫入 IOPS | 每秒計數 | Average
是 | StorageCache/快取 | ClientReadIOPS | 用戶端讀取 IOPS | 每秒計數 | Average
是 | StorageCache/快取 | ClientReadThroughput | 平均快取讀取輸送量 | 每秒位元組 | Average
是 | StorageCache/快取 | ClientWriteIOPS | 用戶端寫入 IOPS | 每秒計數 | Average
是 | StorageCache/快取 | ClientWriteThroughput | 平均快取寫入輸送量 | 每秒位元組 | Average
是 | StorageCache/快取 | StorageTargetAsyncWriteThroughput | StorageTarget 非同步寫入輸送量 | 每秒位元組 | Average
是 | StorageCache/快取 | StorageTargetFillThroughput | StorageTarget 填滿輸送量 | 每秒位元組 | Average
是 | StorageCache/快取 | StorageTargetHealth | 儲存體目標健全狀況 | 計數 | Average
是 | StorageCache/快取 | StorageTargetIOPS | 總 StorageTarget IOPS | 計數 | Average
是 | StorageCache/快取 | StorageTargetLatency | StorageTarget 延遲 | 毫秒 | Average
是 | StorageCache/快取 | StorageTargetMetadataReadIOPS | StorageTarget 中繼資料讀取 IOPS | 每秒計數 | Average
是 | StorageCache/快取 | StorageTargetMetadataWriteIOPS | StorageTarget 中繼資料寫入 IOPS | 每秒計數 | Average
是 | StorageCache/快取 | StorageTargetReadAheadThroughput | StorageTarget 讀取前輸送量 | 每秒位元組 | Average
是 | StorageCache/快取 | StorageTargetReadIOPS | StorageTarget 讀取 IOPS | 每秒計數 | Average
是 | StorageCache/快取 | StorageTargetSyncWriteThroughput | StorageTarget 同步寫入輸送量 | 每秒位元組 | Average
是 | StorageCache/快取 | StorageTargetTotalReadThroughput | StorageTarget 讀取輸送量總計 | 每秒位元組 | Average
是 | StorageCache/快取 | StorageTargetTotalWriteThroughput | StorageTarget 總寫入輸送量 | 每秒位元組 | Average
是 | StorageCache/快取 | StorageTargetWriteIOPS | StorageTarget 寫入 IOPS | 計數 | Average
是 | StorageCache/快取 | Uptime | Uptime | 計數 | Average
是 | microsoft.storagesync/storageSyncServices | ServerSyncSessionResult | 同步會話結果 | 計數 | Average
是 | microsoft.storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | 同步的位元組 | 位元組 | 總計
是 | microsoft.storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | 雲端階層處理重新叫用應用程式的大小 | 位元組 | 總計
是 | microsoft.storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | 雲端階層處理重新叫用大小 | 位元組 | 總計
是 | microsoft.storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | 雲端階層處理重新叫用 | 位元組 | 總計
是 | microsoft.storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | 雲端階層處理重新叫用輸送量 | 每秒位元組 | Average
是 | microsoft.storagesync/storageSyncServices | StorageSyncServerHeartbeat | 伺服器線上狀態 | 計數 | 最大值
是 | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | 檔案已同步 | 計數 | 總計
是 | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | 檔案無法同步 | 計數 | 總計
是 | microsoft.storagesync/storageSyncServices/registeredServers | ServerHeartbeat | 伺服器線上狀態 | 計數 | 最大值
是 | microsoft.storagesync/storageSyncServices/registeredServers | ServerRecallIOTotalSizeBytes | 雲端階層處理重新叫用 | 位元組 | 總計
是 | microsoft.storagesync/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | 同步的位元組 | 位元組 | 總計
是 | microsoft.storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | 檔案已同步 | 計數 | 總計
是 | microsoft.storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | 檔案無法同步 | 計數 | 總計
是 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | 同步的位元組 | 位元組 | 總計
是 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | 檔案已同步 | 計數 | 總計
是 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | 檔案無法同步 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | 失敗的函式要求 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | 函式事件 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | 函式要求 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | 資料轉換錯誤 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | 輸入還原序列化錯誤 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | 次序錯誤事件 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | 早期輸入事件 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | Errors | 執行階段錯誤 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | 輸入事件位元組 | 位元組 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | InputEvents | 輸入事件 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | 待處理輸入事件數 | 計數 | 最大值
是 | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | 收到的輸入來源數 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | 延遲輸入事件 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | 輸出事件 | 計數 | 總計
是 | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | 浮水印延遲秒數 | 秒 | 最大值
是 | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | SU % 使用率 | 百分比 | 最大值
是 | VMwareCloudSimple/virtualMachines | Disk Read Bytes | Disk Read Bytes | 位元組 | 總計
是 | VMwareCloudSimple/virtualMachines | Disk Read Operations/Sec | Disk Read Operations/Sec | 每秒計數 | Average
是 | VMwareCloudSimple/virtualMachines | Disk Write Bytes | Disk Write Bytes | 位元組 | 總計
是 | VMwareCloudSimple/virtualMachines | Disk Write Operations/Sec | Disk Write Operations/Sec | 每秒計數 | Average
是 | VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | Disk Read Bytes/Sec | 每秒位元組 | Average
是 | VMwareCloudSimple/virtualMachines | DiskReadLatency | 磁碟讀取延遲 | 毫秒 | Average
是 | VMwareCloudSimple/virtualMachines | DiskReadOperations | 磁片讀取作業 | 計數 | 總計
是 | VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | Disk Write Bytes/Sec | 每秒位元組 | Average
是 | VMwareCloudSimple/virtualMachines | DiskWriteLatency | 磁碟寫入延遲 | 毫秒 | Average
是 | VMwareCloudSimple/virtualMachines | DiskWriteOperations | 磁片寫入作業 | 計數 | 總計
是 | VMwareCloudSimple/virtualMachines | MemoryActive | 記憶體使用中 | 位元組 | Average
是 | VMwareCloudSimple/virtualMachines | MemoryGranted | 授與的記憶體 | 位元組 | Average
是 | VMwareCloudSimple/virtualMachines | MemoryUsed | 使用的記憶體 | 位元組 | Average
是 | VMwareCloudSimple/virtualMachines | Network In | Network In | 位元組 | 總計
是 | VMwareCloudSimple/virtualMachines | Network Out | Network Out | 位元組 | 總計
是 | VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | 網路位元組/秒 | 每秒位元組 | Average
是 | VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | 網路輸出位元組數/秒 | 每秒位元組 | Average
是 | VMwareCloudSimple/virtualMachines | Percentage CPU | Percentage CPU | 百分比 | Average
是 | VMwareCloudSimple/virtualMachines | PercentageCpuReady | CPU 已就緒百分比 | 毫秒 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | 使用中的要求 | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | 平均回應時間 | 秒 | Average
是 | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | 資料輸入 | 位元組 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | 資料輸出 | 位元組 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | CPU 百分比 | 百分比 | Average
是 | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | 磁碟佇列長度 | 計數 | Average
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | Http 2xx | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | Http 3xx | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | Http 401 | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | Http 403 | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | Http 404 | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | Http 4xx | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Http 伺服器錯誤 | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Http 佇列長度 | 計數 | Average
是 | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | 大型 App Service 方案背景工作角色 | 計數 | Average
是 | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | 中型 App Service 方案背景工作角色 | 計數 | Average
是 | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | 記憶體百分比 | 百分比 | Average
是 | Microsoft.Web/hostingEnvironments/multiRolePools | 要求 | 要求 | 計數 | 總計
是 | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | 小型 App Service 方案背景工作角色 | 計數 | Average
是 | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | 前端總數 | 計數 | Average
是 | Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage | CPU 百分比 | 百分比 | Average
是 | Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage | 記憶體百分比 | 百分比 | Average
是 | Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable | 可用的背景工作角色 | 計數 | Average
是 | Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal | 背景工作角色總數 | 計數 | Average
是 | Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed | 已使用的背景工作角色 | 計數 | Average
是 | Microsoft.Web/serverfarms | BytesReceived | 資料輸入 | 位元組 | 總計
是 | Microsoft.Web/serverfarms | BytesSent | 資料輸出 | 位元組 | 總計
是 | Microsoft.Web/serverfarms | CpuPercentage | CPU 百分比 | 百分比 | Average
是 | Microsoft.Web/serverfarms | DiskQueueLength | 磁碟佇列長度 | 計數 | Average
是 | Microsoft.Web/serverfarms | HttpQueueLength | Http 佇列長度 | 計數 | Average
是 | Microsoft.Web/serverfarms | MemoryPercentage | 記憶體百分比 | 百分比 | Average
是 | Microsoft.Web/serverfarms | TcpCloseWait | TCP 關閉等候 | 計數 | Average
是 | Microsoft.Web/serverfarms | TcpClosing | TCP 關閉 | 計數 | Average
是 | Microsoft.Web/serverfarms | TcpEstablished | TCP 已建立 | 計數 | Average
是 | Microsoft.Web/serverfarms | TcpFinWait1 | TCP Fin 等候1 | 計數 | Average
是 | Microsoft.Web/serverfarms | TcpFinWait2 | TCP Fin 等待2 | 計數 | Average
是 | Microsoft.Web/serverfarms | TcpLastAck | TCP 最後 Ack | 計數 | Average
是 | Microsoft.Web/serverfarms | TcpSynReceived | 已接收 TCP Syn | 計數 | Average
是 | Microsoft.Web/serverfarms | TcpSynSent | TCP Syn 已傳送 | 計數 | Average
是 | Microsoft.Web/serverfarms | TcpTimeWait | TCP 等候時間 | 計數 | Average
是 | Microsoft.Web/sites | AppConnections | 連線 | 計數 | Average
是 | Microsoft.Web/sites | AverageMemoryWorkingSet | 平均記憶體工作集 | 位元組 | Average
是 | Microsoft.Web/sites | AverageResponseTime | 平均回應時間 | 秒 | Average
是 | Microsoft.Web/sites | BytesReceived | 資料輸入 | 位元組 | 總計
是 | Microsoft.Web/sites | BytesSent | 資料輸出 | 位元組 | 總計
是 | Microsoft.Web/sites | CpuTime | CPU 時間 | 秒 | 總計
是 | Microsoft.Web/sites | CurrentAssemblies | 目前的組件 | 計數 | Average
是 | Microsoft.Web/sites | FunctionExecutionCount | 函式執行計數 | 計數 | 總計
是 | Microsoft.Web/sites | FunctionExecutionUnits | 函式執行單位 | 計數 | 總計
是 | Microsoft.Web/sites | Gen0Collections | Gen 0 記憶體回收 | 計數 | 總計
是 | Microsoft.Web/sites | Gen1Collections | Gen 1 記憶體回收 | 計數 | 總計
是 | Microsoft.Web/sites | Gen2Collections | Gen 2 記憶體回收 | 計數 | 總計
是 | Microsoft.Web/sites | 處理 | 控制代碼計數 | 計數 | Average
是 | Microsoft.Web/sites | HealthCheckStatus | 健全狀況檢查狀態 | 計數 | Average
是 | Microsoft.Web/sites | Http101 | Http 101 | 計數 | 總計
是 | Microsoft.Web/sites | Http2xx | Http 2xx | 計數 | 總計
是 | Microsoft.Web/sites | Http3xx | Http 3xx | 計數 | 總計
是 | Microsoft.Web/sites | Http401 | Http 401 | 計數 | 總計
是 | Microsoft.Web/sites | Http403 | Http 403 | 計數 | 總計
是 | Microsoft.Web/sites | Http404 | Http 404 | 計數 | 總計
是 | Microsoft.Web/sites | Http406 | Http 406 | 計數 | 總計
是 | Microsoft.Web/sites | Http4xx | Http 4xx | 計數 | 總計
是 | Microsoft.Web/sites | Http5xx | Http 伺服器錯誤 | 計數 | 總計
是 | Microsoft.Web/sites | HttpResponseTime | 回應時間 | 秒 | Average
是 | Microsoft.Web/sites | IoOtherBytesPerSecond | 每秒的 IO 其他位元組數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites | IoOtherOperationsPerSecond | 每秒的 IO 其他作業數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites | IoReadBytesPerSecond | 每秒的 IO 讀取位元組數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites | IoReadOperationsPerSecond | 每秒的 IO 讀取作業數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites | IoWriteBytesPerSecond | 每秒的 IO 寫入位元組數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites | IoWriteOperationsPerSecond | 每秒的 IO 寫入作業數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites | MemoryWorkingSet | 記憶體工作集 | 位元組 | Average
是 | Microsoft.Web/sites | PrivateBytes | 私用位元組 | 位元組 | Average
是 | Microsoft.Web/sites | 要求 | 要求 | 計數 | 總計
是 | Microsoft.Web/sites | RequestsInApplicationQueue | 應用程式佇列中的要求數 | 計數 | Average
是 | Microsoft.Web/sites | Threads | 執行緒計數 | 計數 | Average
是 | Microsoft.Web/sites | TotalAppDomains | 應用程式網域總計 | 計數 | Average
是 | Microsoft.Web/sites | TotalAppDomainsUnloaded | 已卸載的應用程式網域總計 | 計數 | Average
是 | Microsoft.Web/sites/slots | AppConnections | 連線 | 計數 | Average
是 | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | 平均記憶體工作集 | 位元組 | Average
是 | Microsoft.Web/sites/slots | AverageResponseTime | 平均回應時間 | 秒 | Average
是 | Microsoft.Web/sites/slots | BytesReceived | 資料輸入 | 位元組 | 總計
是 | Microsoft.Web/sites/slots | BytesSent | 資料輸出 | 位元組 | 總計
是 | Microsoft.Web/sites/slots | CpuTime | CPU 時間 | 秒 | 總計
是 | Microsoft.Web/sites/slots | CurrentAssemblies | 目前的組件 | 計數 | Average
是 | Microsoft.Web/sites/slots | FunctionExecutionCount | 函式執行計數 | 計數 | 總計
是 | Microsoft.Web/sites/slots | FunctionExecutionUnits | 函式執行單位 | 計數 | 總計
是 | Microsoft.Web/sites/slots | Gen0Collections | Gen 0 記憶體回收 | 計數 | 總計
是 | Microsoft.Web/sites/slots | Gen1Collections | Gen 1 記憶體回收 | 計數 | 總計
是 | Microsoft.Web/sites/slots | Gen2Collections | Gen 2 記憶體回收 | 計數 | 總計
是 | Microsoft.Web/sites/slots | 處理 | 控制代碼計數 | 計數 | Average
是 | Microsoft.Web/sites/slots | HealthCheckStatus | 健全狀況檢查狀態 | 計數 | Average
是 | Microsoft.Web/sites/slots | Http101 | Http 101 | 計數 | 總計
是 | Microsoft.Web/sites/slots | Http2xx | Http 2xx | 計數 | 總計
是 | Microsoft.Web/sites/slots | Http3xx | Http 3xx | 計數 | 總計
是 | Microsoft.Web/sites/slots | Http401 | Http 401 | 計數 | 總計
是 | Microsoft.Web/sites/slots | Http403 | Http 403 | 計數 | 總計
是 | Microsoft.Web/sites/slots | Http404 | Http 404 | 計數 | 總計
是 | Microsoft.Web/sites/slots | Http406 | Http 406 | 計數 | 總計
是 | Microsoft.Web/sites/slots | Http4xx | Http 4xx | 計數 | 總計
是 | Microsoft.Web/sites/slots | Http5xx | Http 伺服器錯誤 | 計數 | 總計
是 | Microsoft.Web/sites/slots | HttpResponseTime | 回應時間 | 秒 | Average
是 | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | 每秒的 IO 其他位元組數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | 每秒的 IO 其他作業數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites/slots | IoReadBytesPerSecond | 每秒的 IO 讀取位元組數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | 每秒的 IO 讀取作業數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | 每秒的 IO 寫入位元組數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | 每秒的 IO 寫入作業數 | 每秒位元組 | 總計
是 | Microsoft.Web/sites/slots | MemoryWorkingSet | 記憶體工作集 | 位元組 | Average
是 | Microsoft.Web/sites/slots | PrivateBytes | 私用位元組 | 位元組 | Average
是 | Microsoft.Web/sites/slots | 要求 | 要求 | 計數 | 總計
是 | Microsoft.Web/sites/slots | RequestsInApplicationQueue | 應用程式佇列中的要求數 | 計數 | Average
是 | Microsoft.Web/sites/slots | Threads | 執行緒計數 | 計數 | Average
是 | Microsoft.Web/sites/slots | TotalAppDomains | 應用程式網域總計 | 計數 | Average
是 | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | 已卸載的應用程式網域總計 | 計數 | Average
