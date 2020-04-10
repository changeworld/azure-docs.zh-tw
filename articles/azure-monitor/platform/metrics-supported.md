---
title: Azure 監視器支援的資源類型計量
description: AAzure 監視器每一個資源類型的可用計量清單。
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 80e38893d764f35511793d8b31f596dcbf16dd42
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991887"
---
# <a name="supported-metrics-with-azure-monitor"></a>支援 Azure 監視器的計量

> [!NOTE]
> 此清單大部分是從 Azure 監視器指標 REST API 自動生成的。 通過 Github 對此清單所做的任何修改都可以在沒有警告的情況下寫入。 有關如何進行永久更新的詳細資訊,請與本文的作者聯繫。

Azure 監視器提供數種與計量進行互動的方式，包括在入口網站中製作計量圖表、透過 REST API 存取計量，或使用 PowerShell 或 CLI 查詢計量。 

本文是 Azure 監視器的整合指標管道當前可用的所有平臺(即自動收集)指標的完整清單。 該清單上次更新於 2020 年 3 月 27 日。 在此日期之後更改或添加的指標可能不會顯示在下面。 要以程式設計查詢和存取指標清單,請使用[2018-01-01 api 版本](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

其他計量可在入口網站中或使用舊版 API 提供。 此處**未**列出在 Azure 虛擬機器、服務交換矩陣和雲端服務中運行的來賓作業系統(來賓作業系統)的指標。 這些必須通過運行在作業系統或作為作業系統的一個或多個代理進行收集。 您可以使用目前處於公共預覽的[自訂指標](metrics-custom-overview.md)API 將代理指標發送到平臺指標資料庫。 然後,您可以繪製圖表、警報,並以其他方式使用來賓 os 指標(如平臺指標)。 有關詳細資訊,請參閱[監視代理概述](agents-overview.md)。    

指標按命名空間組織。 有關屬於它們的服務和命名空間的清單,請參閱[Azure 服務的資源提供者](../../azure-resource-manager/management/azure-services-resource-providers.md)。 

> [!NOTE]
> 目前不支援透過診斷設定傳送多維度計量。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。
>
> 「例如」**：可以在每個佇列層級瀏覽並繪製事件中樞上的「內送郵件」計量。 不過，當您透過診斷設定匯出時，計量將會呈現為事件中樞內所有佇列的所有內送郵件。
>
> 有關可通過診斷設定匯出的平台指標清單,請參閱[本文](metrics-supported-export-diagnostic-settings.md)。


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU。 範圍 0-100 (S1)、0-200 (S2) 和 0-400 (S4)|ServerResourceType|
|memory_metric|記憶體|位元組|Average|記憶體。 範圍 0-25 GB (S1)、0-50 GB (S2) 和 0-100 GB (S4)|ServerResourceType|
|private_bytes_metric|私用位元組|位元組|Average|私有位元組。|ServerResourceType|
|virtual_bytes_metric|虛擬位元組|位元組|Average|虛擬位元組。|ServerResourceType|
|TotalConnectionRequests|連線要求的總計|Count|Average|連線要求的總計。 這些是達到的。|ServerResourceType|
|SuccessfullConnectionsPerSec|每秒連線成功的次數|每秒計數|Average|成功完成連線的速率。|ServerResourceType|
|TotalConnectionFailures|連線失敗的總計|Count|Average|連線嘗試失敗的總計。|ServerResourceType|
|CurrentUserSessions|目前的使用者工作階段|Count|Average|目前建立的使用者工作階段數目。|ServerResourceType|
|QueryPoolBusyThreads|查詢集區忙碌執行緒|Count|Average|查詢執行緒集區中的忙碌執行緒數目。|ServerResourceType|
|CommandPoolJobQueueLength|命令集區作業佇列長度|Count|Average|命令執行緒集區佇列中的作業數目。|ServerResourceType|
|ProcessingPoolJobQueueLength|處理集區作業佇列長度|Count|Average|處理執行緒集區佇列中的非 I/O 作業數目。|ServerResourceType|
|CurrentConnections|連接：目前的連接|Count|Average|目前已建立的用戶端連接數目。|ServerResourceType|
|CleanerCurrentPrice|記憶體︰清除工具目前價格|Count|Average|記憶體目前的價格 ($/位元組/時間)，並正規化為 1000。|ServerResourceType|
|CleanerMemoryShrinkable|記憶體︰可壓縮的清除器記憶體|位元組|Average|背景清除器將清除的記憶體數量，以 KB 為單位。|ServerResourceType|
|CleanerMemoryNonshrinkable|記憶體︰不可壓縮的清除器記憶體|位元組|Average|背景清除器將不會清除的記憶體數量，以 KB 為單位。|ServerResourceType|
|MemoryUsage|記憶體：記憶體使用量|位元組|Average|伺服器處理序用於計算清除器記憶體價格的記憶體使用量。 等於計數器 Process\PrivateBytes 加上記憶體對應的資料大小，忽略 xVelocity 記憶體內部分析引擎 (VertiPaq) 在超出 xVelocity 引擎記憶體限制外對應或配置的任何記憶體。|ServerResourceType|
|MemoryLimitHard|記憶體︰固定記憶體限制|位元組|Average|組態檔中的固定記憶體限制。|ServerResourceType|
|MemoryLimitHigh|記憶體︰記憶體上限|位元組|Average|來自組態檔的記憶體上限。|ServerResourceType|
|MemoryLimitLow|記憶體︰記憶體下限|位元組|Average|來自組態檔的記憶體下限。|ServerResourceType|
|MemoryLimitVertiPaq|記憶體︰記憶體限制 VertiPaq|位元組|Average|來自組態檔的記憶體內部限制。|ServerResourceType|
|Quota|記憶體︰配額|位元組|Average|目前的記憶體配額，以位元組為單位。 記憶體配額也就是指授與使用的記憶體，或是保留的記憶體。|ServerResourceType|
|QuotaBlocked|記憶體︰封鎖的配額|Count|Average|在釋放其他記憶體配額之前，目前已封鎖的配額要求數目。|ServerResourceType|
|VertiPaqNonpaged|記憶體︰未分頁的 VertiPaq|位元組|Average|工作集中已封鎖來供記憶體內部引擎使用的記憶體位元組。|ServerResourceType|
|VertiPaqPaged|記憶體︰分頁的 VertiPaq|位元組|Average|可供記憶體內部資料使用的分頁記憶體位元組。|ServerResourceType|
|RowsReadPerSec|處理︰每秒讀取的資料列|每秒計數|Average|從所有關聯式資料庫讀取資料列的速率。|ServerResourceType|
|RowsConvertedPerSec|處理︰每秒轉換的資料列|每秒計數|Average|處理期間資料列轉換的速率。|ServerResourceType|
|RowsWrittenPerSec|處理︰每秒寫入的資料列|每秒計數|Average|處理期間資料列寫入的速率。|ServerResourceType|
|CommandPoolBusyThreads|執行緒︰命令集區的忙碌執行緒數|Count|Average|命令執行緒集區中的忙碌執行緒數。|ServerResourceType|
|CommandPoolIdleThreads|執行緒︰命令集區的閒置執行緒數|Count|Average|命令執行緒集區中的閒置執行緒數。|ServerResourceType|
|LongParsingBusyThreads|執行緒︰完整剖析的忙碌執行緒數|Count|Average|完整剖析執行緒集區中的忙碌執行緒數目。|ServerResourceType|
|LongParsingIdleThreads|執行緒︰完整剖析的閒置執行緒數|Count|Average|完整剖析執行緒集區中的閒置執行緒數目。|ServerResourceType|
|LongParsingJobQueueLength|執行緒︰完整剖析的作業佇列長度|Count|Average|完整剖析執行緒集區佇列中的作業數目。|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|執行緒︰處理集區的忙碌 I/O 作業執行緒數|Count|Average|處理執行緒集區中執行 I/O 作業的執行緒數目。|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|執行緒︰處理集區的忙碌非 I/O 執行緒數|Count|Average|處理執行緒集區中執行非 I/O 作業的執行緒數目。|ServerResourceType|
|ProcessingPoolIOJobQueueLength|執行緒：處理集區 I/O 作業佇列長度|Count|Average|處理執行緒集區佇列中的 I/O 作業數目。|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|執行緒︰處理集區的閒置 I/O 作業執行緒數|Count|Average|處理執行緒集區中 I/O 作業的閒置執行緒數目。|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|執行緒︰處理集區的閒置非 I/O 執行緒數|Count|Average|處理執行緒集區中專供非 I/O 作業使用的閒置執行緒數目。|ServerResourceType|
|QueryPoolIdleThreads|執行緒︰查詢集區的閒置執行緒數|Count|Average|處理執行緒集區中 I/O 作業的閒置執行緒數目。|ServerResourceType|
|QueryPoolJobQueueLength|執行緒︰查詢集區的作業佇列長度|Count|Average|查詢執行緒集區佇列中的作業數目。|ServerResourceType|
|ShortParsingBusyThreads|執行緒︰簡短剖析的忙碌執行緒數|Count|Average|簡短剖析執行緒集區中的忙碌執行緒數目。|ServerResourceType|
|ShortParsingIdleThreads|執行緒︰簡短剖析的閒置執行緒數|Count|Average|簡短剖析執行緒集區中的閒置執行緒數目。|ServerResourceType|
|ShortParsingJobQueueLength|執行緒︰簡短剖析的作業佇列長度|Count|Average|簡短剖析執行緒集區佇列中的作業數目。|ServerResourceType|
|memory_thrashing_metric|記憶體猛移|百分比|Average|記憶體猛移的平均值。|ServerResourceType|
|mashup_engine_qpu_metric|M 引擎 QPU|Count|Average|混搭引擎處理序的 QPU 使用量|ServerResourceType|
|mashup_engine_memory_metric|M 引擎記憶體|位元組|Average|混搭引擎處理序的記憶體使用量|ServerResourceType|
|mashup_engine_private_bytes_metric|M 引擎專用位元組|位元組|Average|由 mashup 引擎程序使用專用位元組。|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M 引擎虛擬位元組|位元組|Average|按 mashup 引擎程序進行的虛擬位元組使用。|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TotalRequests|閘道要求總數(已棄用)|Count|總計|閘道要求數 - 使用多維請求指標,改為使用閘道回應代碼類別維度|位置,主機名|
|SuccessfulRequests|成功的閘道要求(已棄用)|Count|總計|成功閘道要求數 - 使用多維請求指標,改為使用閘道回應代碼類別維度|位置,主機名|
|UnauthorizedRequests|未經授權的閘道要求(已棄用)|Count|總計|未經授權的閘道請求數 - 使用多維請求指標,改為使用閘道回應代碼類別維度|位置,主機名|
|FailedRequests|失敗的閘道要求(已棄用)|Count|總計|閘道要求中的故障數 - 使用多維請求指標,改為使用閘道回應代碼類別維度|位置,主機名|
|OtherRequests|其他閘道要求(已棄用)|Count|總計|其他閘道要求數 - 使用多維請求指標,改為使用閘道回應代碼類別維度|位置,主機名|
|Duration|閘道要求的整體持續期間|毫秒|Average|閘道要求的整體持續期間，以毫秒為單位|位置,主機名|
|後端持續時間|後端介面要求的持續時間|毫秒|Average|後端介面要求的持續時間(以毫秒為單位)|位置,主機名|
|Capacity|Capacity|百分比|Average|ApiManagement 服務的使用率衡量標準|Location|
|活動中心總活動|事件中心事件總數|Count|總計|傳送事件 Hub 的事件|Location|
|活動中心成功活動|成功事件中心事件|Count|總計|成功事件中心事件數|Location|
|事件Hub完全失敗事件|失敗的事件中心事件|Count|總計|失敗的事件Hub事件|Location|
|事件Hub拒絕事件|已拒絕的事件中心事件|Count|總計|拒絕的事件事件數(設定錯誤或未授權)|Location|
|事件HubHubd事件|限制事件中心事件|Count|總計|受限制的事件Hub 事件數|Location|
|事件HubTimedout事件|逾時事件中心事件|Count|總計|逾時事件 Hub 事件數|Location|
|事件Hub放棄事件|丟棄的事件中心事件|Count|總計|由於已達到佇列大小限制而跳過的事件數|Location|
|事件中心總位元組已送出|事件中心事件的大小|位元組|總計|事件中心事件的總大小(以位元組為單位)|Location|
|Requests|Requests|Count|總計|具有多個維度的閘道要求指標|位置、主機名稱、LastError、後端介面、閘道回應代碼、後端介面類別、閘道回應代碼類別|
|網路連線|資源網路連線狀態(預覽)|Count|總計|API 管理服務中從資源類型的網路連線狀態|位置,資源類型|


## <a name="microsoftappconfigurationconfigurationstores"></a>微軟.應用程式配置/配置商店

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|HTTP 傳輸要求計數|HTTP 傳輸要求計數|Count|Count|傳入 HTTP 請求的總數。|StatusCode|
|HTTP 傳入要求持續時間|HTTP 傳入要求持續時間|Count|Average|HTTP 請求上的延遲。|StatusCode|


## <a name="microsoftappplatformspring"></a>微軟.AppPlatform/春天

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|系統 Cpu 使用百分比|系統 CPU 使用量|百分比|Average|整個系統最近的 cpu 使用方式|應用程式名稱,Pod|
|套用Cpu使用百分比|套用 CPU 使用量|百分比|Average|套用 JVM CPU 使用率|應用程式名稱,Pod|
|套用記憶體已提交|套用記憶體配置|位元組|Average|配置給 JVM 的記憶體(以位元組為單位)|應用程式名稱,Pod|
|套用記憶體已使用|使用的應用程式記憶體|位元組|Average|以位元組為單位使用的應用記憶體|應用程式名稱,Pod|
|應用程式記憶體最大值|套用記憶體最大值|位元組|最大值|可用於記憶體管理的最大記憶體(以位元組為單位)|應用程式名稱,Pod|
|最大記憶池位元組|最大可用舊代資料大小|位元組|Average|舊代記憶體池的最大大小|應用程式名稱,Pod|
|舊記憶體池位元組|舊代資料大小|位元組|Average|完整 GC 後舊代記憶體池的大小|應用程式名稱,Pod|
|舊基因提升位元組|提高舊代資料大小|位元組|最大值|GC 之前舊代記憶體池的大小正增長計數到 GC 之後|應用程式名稱,Pod|
|楊根升點位元組|提高為年輕一代資料大小|位元組|最大值|增加一個 GC 後年輕代記憶體池的大小,以在下一個 GC 之前增加|應用程式名稱,Pod|
|GC 暫停總計數|GC 暫停計數|Count|總計|GC 暫停計數|應用程式名稱,Pod|
|GC 暫停總時間|GC 暫停總時間|毫秒|總計|GC 暫停總時間|應用程式名稱,Pod|
|托姆卡特森特斯|湯姆卡特總發送位元組|位元組|總計|湯姆卡特總發送位元組|應用程式名稱,Pod|
|湯姆卡特接收位元組|湯姆卡特總接收位元組|位元組|總計|湯姆卡特總接收位元組|應用程式名稱,Pod|
|湯姆卡特請求總時間|湯姆卡特請求總時間|毫秒|總計|湯姆卡特請求總時間|應用程式名稱,Pod|
|湯姆卡特請求總數|湯姆卡特請求總數|Count|總計|湯姆卡特請求總數|應用程式名稱,Pod|
|湯姆卡特回應Avg時間|湯姆卡特請求平均時間|毫秒|Average|湯姆卡特請求平均時間|應用程式名稱,Pod|
|湯姆卡特要求最大時間|湯姆卡特要求最大時間|毫秒|最大值|湯姆卡特要求最大時間|應用程式名稱,Pod|
|湯姆卡特錯誤計數|湯姆卡特全域錯誤|Count|總計|湯姆卡特全域錯誤|應用程式名稱,Pod|
|湯姆卡特會話活動最大值Count|Tomcat 工作階段 最大活動計數|Count|總計|Tomcat 工作階段 最大活動計數|應用程式名稱,Pod|
|湯姆卡特會話活動最大時間|Tomcat 工作階段最大活動時間|毫秒|最大值|Tomcat 工作階段最大活動時間|應用程式名稱,Pod|
|湯姆卡特會話活動電流Count|Tomcat 工作階段活動計數|Count|總計|Tomcat 工作階段活動計數|應用程式名稱,Pod|
|湯姆卡特會話創建計數|Tomcat 工作階段建立計數|Count|總計|Tomcat 工作階段建立計數|應用程式名稱,Pod|
|湯姆卡特會話過期計數|Tomcat 工作階段過期計數|Count|總計|Tomcat 工作階段過期計數|應用程式名稱,Pod|
|湯姆卡特會話拒絕計數|Tomcat 工作階段已拒絕計數|Count|總計|Tomcat 工作階段已拒絕計數|應用程式名稱,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TotalJob|工作總數|Count|總計|工作總數|執行簿,狀態|
|總更新部署執行|更新部署執行總計|Count|總計|軟體更新部署執行總數|軟體更新設定名稱、狀態|
|總更新部署電腦執行|更新部署機器執行總計|Count|總計|軟體更新部署電腦在軟體更新部署執行中執行的總|軟體更新設定名稱、狀態、目標電腦、軟體更新設定RunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CoreCount|專用核心計數|Count|總計|Batch 帳戶中的專用核心總數|None|
|TotalNodeCount|專用節點計數|Count|總計|Batch 帳戶中的專用節點總數|None|
|LowPriorityCoreCount|LowPriority 核心計數|Count|總計|Batch 帳戶中的低優先順序核心總數|None|
|TotalLowPriorityNodeCount|低優先順序節點計數|Count|總計|Batch 帳戶中的低優先順序節點總數|None|
|CreatingNodeCount|建立節點計數|Count|總計|正在建立的節點數目|None|
|StartingNodeCount|啟動節點計數|Count|總計|啟動的節點數目|None|
|WaitingForStartTaskNodeCount|等候啟動工作節點計數|Count|總計|等待啟動工作完成的節點數目|None|
|StartTaskFailedNodeCount|啟動工作失敗的節點計數|Count|總計|啟動工作失敗的節點數目|None|
|IdleNodeCount|閒置的節點計數|Count|總計|閒置節點的數目|None|
|OfflineNodeCount|離線節點計數|Count|總計|離線節點的數目|None|
|RebootingNodeCount|重新啟動節點計數|Count|總計|重新啟動節點的數目|None|
|RebootingNodeCount|重新安裝映像節點計數|Count|總計|重新安裝映像的節點數目|None|
|RunningNodeCount|執行節點計數|Count|總計|執行節點的數目|None|
|LeavingPoolNodeCount|離開集區節點計數|Count|總計|離開集區的節點數目|None|
|UnusableNodeCount|無法使用的節點計數|Count|總計|無法使用的節點數目|None|
|PreemptedNodeCount|先占節點計數|Count|總計|先占節點的數量|None|
|TaskStartEvent|工作啟動的事件|Count|總計|已啟動的工作總次數|池代碼,工作代碼|
|TaskCompleteEvent|工作完成事件|Count|總計|已完成的工作總次數|池代碼,工作代碼|
|TaskFailEvent|工作失敗事件|Count|總計|失敗狀態中已完成的工作總數|池代碼,工作代碼|
|PoolCreateEvent|集區建立事件|Count|總計|已建立集區的總數|poolId|
|PoolResizeStartEvent|調整集區大小啟動事件|Count|總計|已啟動的調整集區大小總次數|poolId|
|PoolResizeCompleteEvent|調整集區大小完成事件|Count|總計|已完成的調整集區大小總次數|poolId|
|PoolDeleteStartEvent|集區刪除啟動事件|Count|總計|已啟動的集區刪除總次數|poolId|
|PoolDeleteCompleteEvent|集區刪除完成事件|Count|總計|已完成的集區刪除總次數|poolId|
|JobDeleteCompleteEvent|作業刪除完成事件|Count|總計|已成功刪除的作業總數。|jobId|
|JobDeleteStartEvent|作業刪除啟動事件|Count|總計|已要求要刪除的作業總數。|jobId|
|JobDisableCompleteEvent|作業停用完成事件|Count|總計|已成功停用的作業總數。|jobId|
|JobDisableStartEvent|作業停用啟動事件|Count|總計|已要求要停用的作業總數。|jobId|
|JobStartEvent|作業啟動事件|Count|總計|已成功啟動的作業總數。|jobId|
|JobTerminateCompleteEvent|作業終止完成事件|Count|總計|已成功終止的作業總數。|jobId|
|JobTerminateCompleteEvent|作業終止啟動事件|Count|總計|已要求要終止的作業總數。|jobId|


## <a name="microsoftbatchaiworkspaces"></a>微軟.BatchAI/工作區

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|提交工作|提交工作|Count|總計|提交的工作數|方案,叢集名稱|
|已完成工作|已完成工作|Count|總計|已完成的工作數|專案、叢集名稱、結果類型|
|節點總數|節點總數|Count|Average|總節點數|方案,叢集名稱|
|活動節點|活動節點|Count|Average|執行節點的數目|方案,叢集名稱|
|閒置節點|閒置節點|Count|Average|閒置節點的數目|方案,叢集名稱|
|無法使用的節點|無法使用的節點|Count|Average|無法使用的節點數目|方案,叢集名稱|
|搶佔節點|搶佔節點|Count|Average|先占節點的數量|方案,叢集名稱|
|離開節點|離開節點|Count|Average|離開節點數|方案,叢集名稱|
|核心總數|核心總數|Count|Average|總核心數|方案,叢集名稱|
|活動核心|活動核心|Count|Average|活動核心數|方案,叢集名稱|
|閒置核心|閒置核心|Count|Average|閒置核心數|方案,叢集名稱|
|無法使用的核心|無法使用的核心|Count|Average|無法使用核心數|方案,叢集名稱|
|搶佔型核心|搶佔型核心|Count|Average|搶佔型內核數|方案,叢集名稱|
|離開核心|離開核心|Count|Average|離開核心的數量|方案,叢集名稱|
|配額利用率百分比|配額利用率百分比|Count|Average|使用配額百分比|方案,叢集名稱,Vm 家族名稱,Vm 優先|

## <a name="microsoftblockchainblockchainmembers"></a>微軟.區塊鏈/區塊鏈成員

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Cpu 使用百分比雙|CPU 使用率|百分比|最大值|CPU 使用率|節點|
|MemoryUsage|記憶體使用量|位元組|Average|記憶體使用量|節點|
|MemoryLimit|記憶體限制|位元組|Average|記憶體限制|節點|
|記憶體使用百分比雙|記憶體使用百分比|百分比|Average|記憶體使用百分比|節點|
|儲存使用|儲存使用方式|位元組|Average|儲存使用方式|節點|
|IORead 位元組|IO 讀取位元組|位元組|總計|IO 讀取位元組|節點|
|IOWrite 位元組|IO 寫入位元組|位元組|總計|IO 寫入位元組|節點|
|連線已接受|已接受連線|Count|總計|已接受連線|節點|
|連線處理|已處理的連線|Count|總計|已處理的連線|節點|
|連線活動|作用中的連線|Count|Average|作用中的連線|節點|
|要求處理|已處理的要求|Count|總計|已處理的要求|節點|
|已處理的區塊|已處理的區塊|Count|總計|已處理的區塊|節點|
|已處理的交易|已處理的交易|Count|總計|已處理的交易|節點|
|待定交易|待處理交易|Count|Average|待處理交易|節點|
|排隊的事務|排隊的事務|Count|Average|排隊的事務|節點|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|connectedclients|連線的用戶端|Count|最大值||ShardId|
|totalcommandsprocessed|總作業數|Count|總計||ShardId|
|cachehits|快取點擊|Count|總計||ShardId|
|cachemisses|快取遺漏|Count|總計||ShardId|
|快取率|快取錯過率|百分比|快取率||ShardId|
|getcommands|取得|Count|總計||ShardId|
|setcommands|集合|Count|總計||ShardId|
|operationsPerSecond|每秒的作業數|Count|最大值||ShardId|
|evictedkeys|收回的金鑰|Count|總計||ShardId|
|totalkeys|索引鍵總計|Count|最大值||ShardId|
|expiredkeys|到期的金鑰|Count|總計||ShardId|
|usedmemory|已使用的記憶體|位元組|最大值||ShardId|
|usedmemorypercentage|已用的記憶體百分比|百分比|最大值||ShardId|
|usedmemoryRss|已用的記憶體 RSS|位元組|最大值||ShardId|
|serverLoad|伺服器負載|百分比|最大值||ShardId|
|cacheWrite|快取寫入|每秒位元組|最大值||ShardId|
|cacheRead|快取讀取|每秒位元組|最大值||ShardId|
|percentProcessorTime|CPU|百分比|最大值||ShardId|
|cacheLatency|快取延遲 (毫秒) (預覽)|Count|Average||ShardId|
|錯誤|Errors|Count|最大值||磁碟,錯誤型別|
|connectedclients0|連線的用戶端 (分區 0)|Count|最大值||None|
|totalcommandsprocessed0|總作業數 (分區 0)|Count|總計||None|
|cachehits0|快取點擊 (分區 0)|Count|總計||None|
|cachemisses0|快取遺漏 (分區 0)|Count|總計||None|
|getcommands0|取得 (分區 0)|Count|總計||None|
|setcommands0|設定 (分區 0)|Count|總計||None|
|operationsPerSecond0|每秒的作業數 (分區 0)|Count|最大值||None|
|evictedkeys0|收回的金鑰 (分區 0)|Count|總計||None|
|totalkeys0|金鑰總數 (分區 0)|Count|最大值||None|
|expiredkeys0|到期的金鑰 (分區 0)|Count|總計||None|
|usedmemory0|已用的記憶體 (分區 0)|位元組|最大值||None|
|usedmemoryRss0|已用的記憶體 RSS (分區 0)|位元組|最大值||None|
|serverLoad0|伺服器負載 (分區 0)|百分比|最大值||None|
|cacheWrite0|快取寫入 (分區 0)|每秒位元組|最大值||None|
|cacheRead0|快取讀取 (分區 0)|每秒位元組|最大值||None|
|percentProcessorTime0|CPU (分區 0)|百分比|最大值||None|
|connectedclients1|連線的用戶端 (分區 1)|Count|最大值||None|
|totalcommandsprocessed1|總作業數 (分區 1)|Count|總計||None|
|cachehits1|快取點擊 (分區 1)|Count|總計||None|
|cachemisses1|快取遺漏 (分區 1)|Count|總計||None|
|getcommands1|取得 (分區 1)|Count|總計||None|
|setcommands1|設定 (分區 1)|Count|總計||None|
|operationsPerSecond1|每秒的作業數 (分區 1)|Count|最大值||None|
|evictedkeys1|收回的金鑰 (分區 1)|Count|總計||None|
|totalkeys1|金鑰總數 (分區 1)|Count|最大值||None|
|expiredkeys1|到期的金鑰 (分區 1)|Count|總計||None|
|usedmemory1|已用的記憶體 (分區 1)|位元組|最大值||None|
|usedmemoryRss1|已用的記憶體 RSS (分區 1)|位元組|最大值||None|
|serverLoad1|伺服器負載 (分區 1)|百分比|最大值||None|
|cacheWrite1|快取寫入 (分區 1)|每秒位元組|最大值||None|
|cacheRead1|快取讀取 (分區 1)|每秒位元組|最大值||None|
|percentProcessorTime1|CPU (分區 1)|百分比|最大值||None|
|connectedclients2|連線的用戶端 (分區 2)|Count|最大值||None|
|totalcommandsprocessed2|總作業數 (分區 2)|Count|總計||None|
|cachehits2|快取點擊 (分區 2)|Count|總計||None|
|cachemisses2|快取遺漏 (分區 2)|Count|總計||None|
|getcommands2|取得 (分區 2)|Count|總計||None|
|setcommands2|設定 (分區 2)|Count|總計||None|
|operationsPerSecond2|每秒的作業數 (分區 2)|Count|最大值||None|
|evictedkeys2|收回的金鑰 (分區 2)|Count|總計||None|
|totalkeys2|金鑰總數 (分區 2)|Count|最大值||None|
|expiredkeys2|到期的金鑰 (分區 2)|Count|總計||None|
|usedmemory2|已用的記憶體 (分區 2)|位元組|最大值||None|
|usedmemoryRss2|已用的記憶體 RSS (分區 2)|位元組|最大值||None|
|serverLoad2|伺服器負載 (分區 2)|百分比|最大值||None|
|cacheWrite2|快取寫入 (分區 2)|每秒位元組|最大值||None|
|cacheRead2|快取讀取 (分區 2)|每秒位元組|最大值||None|
|percentProcessorTime2|CPU (分區 2)|百分比|最大值||None|
|connectedclients3|連線的用戶端 (分區 3)|Count|最大值||None|
|totalcommandsprocessed3|總作業數 (分區 3)|Count|總計||None|
|cachehits3|快取點擊 (分區 3)|Count|總計||None|
|cachemisses3|快取遺漏 (分區 3)|Count|總計||None|
|getcommands3|取得 (分區 3)|Count|總計||None|
|setcommands3|設定 (分區 3)|Count|總計||None|
|operationsPerSecond3|每秒的作業數 (分區 3)|Count|最大值||None|
|evictedkeys3|收回的金鑰 (分區 3)|Count|總計||None|
|totalkeys3|金鑰總數 (分區 3)|Count|最大值||None|
|expiredkeys3|到期的金鑰 (分區 3)|Count|總計||None|
|usedmemory3|已用的記憶體 (分區 3)|位元組|最大值||None|
|usedmemoryRss3|已用的記憶體 RSS (分區 3)|位元組|最大值||None|
|serverLoad3|伺服器負載 (分區 3)|百分比|最大值||None|
|cacheWrite3|快取寫入 (分區 3)|每秒位元組|最大值||None|
|cacheRead3|快取讀取 (分區 3)|每秒位元組|最大值||None|
|percentProcessorTime3|CPU (分區 3)|百分比|最大值||None|
|connectedclients4|連線的用戶端 (分區 4)|Count|最大值||None|
|totalcommandsprocessed4|總作業數 (分區 4)|Count|總計||None|
|cachehits4|快取點擊 (分區 4)|Count|總計||None|
|cachemisses4|快取遺漏 (分區 4)|Count|總計||None|
|getcommands4|取得 (分區 4)|Count|總計||None|
|setcommands4|設定 (分區 4)|Count|總計||None|
|operationsPerSecond4|每秒的作業數 (分區 4)|Count|最大值||None|
|evictedkeys4|收回的金鑰 (分區 4)|Count|總計||None|
|totalkeys4|金鑰總數 (分區 4)|Count|最大值||None|
|expiredkeys4|到期的金鑰 (分區 4)|Count|總計||None|
|usedmemory4|已用的記憶體 (分區 4)|位元組|最大值||None|
|usedmemoryRss4|已用的記憶體 RSS (分區 4)|位元組|最大值||None|
|serverLoad4|伺服器負載 (分區 4)|百分比|最大值||None|
|cacheWrite4|快取寫入 (分區 4)|每秒位元組|最大值||None|
|cacheRead4|快取讀取 (分區 4)|每秒位元組|最大值||None|
|percentProcessorTime4|CPU (分區 4)|百分比|最大值||None|
|connectedclients5|連線的用戶端 (分區 5)|Count|最大值||None|
|totalcommandsprocessed5|總作業數 (分區 5)|Count|總計||None|
|cachehits5|快取點擊 (分區 5)|Count|總計||None|
|cachemisses5|快取遺漏 (分區 5)|Count|總計||None|
|getcommands5|取得 (分區 5)|Count|總計||None|
|setcommands5|設定 (分區 5)|Count|總計||None|
|operationsPerSecond5|每秒的作業數 (分區 5)|Count|最大值||None|
|evictedkeys5|收回的金鑰 (分區 5)|Count|總計||None|
|totalkeys5|金鑰總數 (分區 5)|Count|最大值||None|
|expiredkeys5|到期的金鑰 (分區 5)|Count|總計||None|
|usedmemory5|已用的記憶體 (分區 5)|位元組|最大值||None|
|usedmemoryRss5|已用的記憶體 RSS (分區 5)|位元組|最大值||None|
|serverLoad5|伺服器負載 (分區 5)|百分比|最大值||None|
|cacheWrite5|快取寫入 (分區 5)|每秒位元組|最大值||None|
|cacheRead5|快取讀取 (分區 5)|每秒位元組|最大值||None|
|percentProcessorTime5|CPU (分區 5)|百分比|最大值||None|
|connectedclients6|連線的用戶端 (分區 6)|Count|最大值||None|
|totalcommandsprocessed6|總作業數 (分區 6)|Count|總計||None|
|cachehits6|快取點擊 (分區 6)|Count|總計||None|
|cachemisses6|快取遺漏 (分區 6)|Count|總計||None|
|getcommands6|取得 (分區 6)|Count|總計||None|
|setcommands6|設定 (分區 6)|Count|總計||None|
|operationsPerSecond6|每秒的作業數 (分區 6)|Count|最大值||None|
|evictedkeys6|收回的金鑰 (分區 6)|Count|總計||None|
|totalkeys6|金鑰總數 (分區 6)|Count|最大值||None|
|expiredkeys6|到期的金鑰 (分區 6)|Count|總計||None|
|usedmemory6|已用的記憶體 (分區 6)|位元組|最大值||None|
|usedmemoryRss6|已用的記憶體 RSS (分區 6)|位元組|最大值||None|
|serverLoad6|伺服器負載 (分區 6)|百分比|最大值||None|
|cacheWrite6|快取寫入 (分區 6)|每秒位元組|最大值||None|
|cacheRead6|快取讀取 (分區 6)|每秒位元組|最大值||None|
|percentProcessorTime6|CPU (分區 6)|百分比|最大值||None|
|connectedclients7|連線的用戶端 (分區 7)|Count|最大值||None|
|totalcommandsprocessed7|總作業數 (分區 7)|Count|總計||None|
|cachehits7|快取點擊 (分區 7)|Count|總計||None|
|cachemisses7|快取遺漏 (分區 7)|Count|總計||None|
|getcommands7|取得 (分區 7)|Count|總計||None|
|setcommands7|設定 (分區 7)|Count|總計||None|
|operationsPerSecond7|每秒的作業數 (分區 7)|Count|最大值||None|
|evictedkeys7|收回的金鑰 (分區 7)|Count|總計||None|
|totalkeys7|金鑰總數 (分區 7)|Count|最大值||None|
|expiredkeys7|到期的金鑰 (分區 7)|Count|總計||None|
|usedmemory7|已用的記憶體 (分區 7)|位元組|最大值||None|
|usedmemoryRss7|已用的記憶體 RSS (分區 7)|位元組|最大值||None|
|serverLoad7|伺服器負載 (分區 7)|百分比|最大值||None|
|cacheWrite7|快取寫入 (分區 7)|每秒位元組|最大值||None|
|cacheRead7|快取讀取 (分區 7)|每秒位元組|最大值||None|
|percentProcessorTime7|CPU (分區 7)|百分比|最大值||None|
|connectedclients8|連線的用戶端 (分區 8)|Count|最大值||None|
|totalcommandsprocessed8|總作業數 (分區 8)|Count|總計||None|
|cachehits8|快取點擊 (分區 8)|Count|總計||None|
|cachemisses8|快取遺漏 (分區 8)|Count|總計||None|
|getcommands8|取得 (分區 8)|Count|總計||None|
|setcommands8|設定 (分區 8)|Count|總計||None|
|operationsPerSecond8|每秒的作業數 (分區 8)|Count|最大值||None|
|evictedkeys8|收回的金鑰 (分區 8)|Count|總計||None|
|totalkeys8|金鑰總數 (分區 8)|Count|最大值||None|
|expiredkeys8|到期的金鑰 (分區 8)|Count|總計||None|
|usedmemory8|已用的記憶體 (分區 8)|位元組|最大值||None|
|usedmemoryRss8|已用的記憶體 RSS (分區 8)|位元組|最大值||None|
|serverLoad8|伺服器負載 (分區 8)|百分比|最大值||None|
|cacheWrite8|快取寫入 (分區 8)|每秒位元組|最大值||None|
|cacheRead8|快取讀取 (分區 8)|每秒位元組|最大值||None|
|percentProcessorTime8|CPU (分區 8)|百分比|最大值||None|
|connectedclients9|連線的用戶端 (分區 9)|Count|最大值||None|
|totalcommandsprocessed9|總作業數 (分區 9)|Count|總計||None|
|cachehits9|快取點擊 (分區 9)|Count|總計||None|
|cachemisses9|快取遺漏 (分區 9)|Count|總計||None|
|getcommands9|取得 (分區 9)|Count|總計||None|
|setcommands9|設定 (分區 9)|Count|總計||None|
|operationsPerSecond9|每秒的作業數 (分區 9)|Count|最大值||None|
|evictedkeys9|收回的金鑰 (分區 9)|Count|總計||None|
|totalkeys9|金鑰總數 (分區 9)|Count|最大值||None|
|expiredkeys9|到期的金鑰 (分區 9)|Count|總計||None|
|usedmemory9|已用的記憶體 (分區 9)|位元組|最大值||None|
|usedmemoryRss9|已用的記憶體 RSS (分區 9)|位元組|最大值||None|
|serverLoad9|伺服器負載 (分區 9)|百分比|最大值||None|
|cacheWrite9|快取寫入 (分區 9)|每秒位元組|最大值||None|
|cacheRead9|快取讀取 (分區 9)|每秒位元組|最大值||None|
|percentProcessorTime9|CPU (分區 9)|百分比|最大值||None|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>微軟.Cdn/cdnweb應用程式防火牆策略

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Web 應用程式防火牆要求計數|Count|總計|Web 應用程式防火牆處理的用戶端要求數|原則名稱、規則名稱、操作|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|Average|虛擬機器目前使用的配置計算單位百分比。|None|
|Network In|Network In|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目。|None|
|Network Out|Network Out|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目。|None|
|Disk Read Bytes/Sec|磁碟讀取|每秒位元組|Average|監視期間從磁碟讀取的平均位元組。|None|
|Disk Write Bytes/Sec|磁碟寫入|每秒位元組|Average|監視期間寫入磁碟的平均位元組。|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS。|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS。|None|


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
|UsedCapacity|已使用容量|位元組|Average|帳戶使用的容量|None|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存用於處理成功請求的延遲(以毫秒為單位)。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向存儲服務或指定的 API 操作成功請求的端到端延遲(以毫秒為單位)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>微軟.經典存儲/存儲帳戶/blob服務

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BlobCapacity|Blob 容量|位元組|Average|儲存體帳戶 Blob 服務所使用的儲存體量 (位元組)。|Blob 類型,層|
|BlobCount|Blob 計數|Count|Average|儲存體帳戶 Blob 服務中的 Blob 數目。|Blob 類型,層|
|ContainerCount|Blob 容器計數|Count|Average|儲存體帳戶 Blob 服務中的容器數目。|None|
|IndexCapacity|索引容量|位元組|Average|ADLS Gen2(分層)索引(以位元組為單位)使用的儲存量。|None|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存用於處理成功請求的延遲(以毫秒為單位)。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向存儲服務或指定的 API 操作成功請求的端到端延遲(以毫秒為單位)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>微軟.經典存儲/存儲帳戶/表服務

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TableCapacity|資料表容量|位元組|Average|儲存體帳戶資料表服務所使用的儲存體量 (位元組)。|None|
|TableCount|資料表計數|Count|Average|儲存體帳戶資料表服務中的資料表數目。|None|
|TableEntityCount|資料表實體計數|Count|Average|儲存體帳戶資料表服務中的資料表實體數目。|None|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存用於處理成功請求的延遲(以毫秒為單位)。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向存儲服務或指定的 API 操作成功請求的端到端延遲(以毫秒為單位)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>微軟.經典存儲/儲存帳戶/檔案服務

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|FileCapacity|檔案容量|位元組|Average|儲存帳戶的檔案服務(以位元組為單位)使用的儲存量。|FileShare|
|FileCount|檔案計數|Count|Average|儲存帳戶的檔案服務中的檔案數。|FileShare|
|FileShareCount|檔案共用計數|Count|Average|儲存帳戶的檔案服務中的檔共享數。|None|
|檔案分享快照計數|檔案分享快照計數|Count|Average|存儲帳戶檔服務中共用上存在的快照數。|FileShare|
|檔案分享快照大小|檔案分享快照大小|位元組|Average|存儲帳戶的檔案服務中的快照使用的存儲量(以位元組為單位)。|FileShare|
|檔案共用配額|檔案共用配額大小|位元組|Average|Azure 檔案服務(以位元組為單位)可以使用的儲存量的上限。|FileShare|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證、檔案分享|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證,檔案分享|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證,檔案分享|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存用於處理成功請求的延遲(以毫秒為單位)。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證,檔案分享|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向存儲服務或指定的 API 操作成功請求的端到端延遲(以毫秒為單位)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證,檔案分享|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證,檔案分享|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>微軟.經典存儲/存儲帳戶/佇列服務

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueueCapacity|佇列容量|位元組|Average|儲存體帳戶佇列服務所使用的儲存體量 (位元組)。|None|
|QueueCount|佇列計數|Count|Average|儲存體帳戶佇列服務中的佇列數目。|None|
|QueueMessageCount|佇列訊息計數|Count|Average|儲存體帳戶佇列服務中的佇列訊息大約數目。|None|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存用於處理成功請求的延遲(以毫秒為單位)。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向存儲服務或指定的 API 操作成功請求的端到端延遲(以毫秒為單位)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TotalCalls|呼叫總數|Count|總計|呼叫總數。|阿皮名稱,操作名稱,區域|
|SuccessfulCalls|成功的呼叫|Count|總計|成功的呼叫數。|阿皮名稱,操作名稱,區域|
|TotalErrors|錯誤總數|Count|總計|具有錯誤回應 (HTTP 回應碼 4xx 或 5xx) 的呼叫總數。|阿皮名稱,操作名稱,區域|
|BlockedCalls|已封鎖的呼叫|Count|總計|超過速率或配額限制的呼叫數目。|阿皮名稱,操作名稱,區域|
|ServerErrors|伺服器錯誤|Count|總計|具有服務內部錯誤 (HTTP 回應碼 5xx) 的呼叫數目。|阿皮名稱,操作名稱,區域|
|ClientErrors|用戶端錯誤|Count|總計|具有用戶端錯誤 (HTTP 回應碼 4xx) 的呼叫數目。|阿皮名稱,操作名稱,區域|
|DataIn|資料輸入|位元組|總計|內送資料大小，以位元組為單位。|阿皮名稱,操作名稱,區域|
|DataOut|資料輸出|位元組|總計|輸出資料大小，以位元組為單位。|阿皮名稱,操作名稱,區域|
|Latency|Latency|毫秒|Average|延遲，以毫秒為單位。|阿皮名稱,操作名稱,區域|
|TotalTokenCalls|呼叫權杖總計|Count|總計|呼叫權杖次數總計。|阿皮名稱,操作名稱,區域|
|CharactersTranslated|轉譯字元數|Count|總計|傳入文字要求中的字元總數。|阿皮名稱,操作名稱,區域|
|字元已訓練|已訓練字元|Count|總計|已訓練的字元總數。|阿皮名稱,操作名稱,區域|
|SpeechSessionDuration|語音工作階段持續時間|秒|總計|語音工作階段的總持續時間 (秒)。|阿皮名稱,操作名稱,區域|
|TotalTransactions|交易數總計|Count|總計|交易總數。|None|
|已處理的影像|已處理的影像|Count|總計| 用於圖像處理的事務數。|Api 名稱、功能名稱、通道、區域|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|Average|目前虛擬機器正在使用中的已配置計算單位百分比|None|
|Network In|網路在計費(已棄用)|位元組|總計|虛擬機器(傳入流量)在所有網路介面上接收的計費位元組數(已棄用)|None|
|Network Out|網路出計費(已棄用)|位元組|總計|虛擬機器(傳出流量)在所有網路介面上計費位元組數(已棄用)|None|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|監視期間從磁碟讀取的位元組|None|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|監視期間寫入磁碟的位元組|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS|None|
|剩餘 CPU 信用額度|剩餘 CPU 信用額度|Count|Average|可用於高載的信用額度總數|None|
|CPU Credits Consumed|CPU Credits Consumed|Count|Average|由虛擬機器取用的信用額度總數|None|
|每一磁碟讀取位元組/秒|資料磁碟讀取位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|監視期間從單磁碟讀取位元組/秒|SlotId|
|每一磁碟寫入位元組/秒|資料磁碟寫入位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間寫入單磁碟的位元組/秒數|SlotId|
|每一磁碟讀取作業/秒|資料磁碟讀取操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單磁碟讀取 IOPS|SlotId|
|每一磁碟寫入作業/秒|資料磁碟寫入操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單磁碟寫入 IOPS|SlotId|
|每一磁碟 QD|資料磁碟[QD(已棄用)](portal-disk-metrics-deprecation.md)=(portal-disk-metrics-deprecation.md)|Count|Average|資料磁碟佇列深度 (或佇列長度)|SlotId|
|OS 每一磁碟讀取位元組/秒|OS 磁碟讀取位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取位元組/秒|None|
|OS 每一磁碟寫入位元組/秒|OS 磁碟寫入位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間寫入單磁碟的位元組/秒|None|
|OS 每一磁碟讀取作業/秒|OS 磁碟讀取操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取 IOPS|None|
|OS 每一磁碟寫入作業/秒|OS 磁碟寫入操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟寫入 IOPS|None|
|OS 每一磁碟 QD|OS 磁碟[QD(已棄用)](portal-disk-metrics-deprecation.md)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|None|
|資料磁碟讀取位元組/秒|資料磁碟讀取位元組/秒 (預覽)|每秒計數|Average|監視期間從單磁碟讀取位元組/秒|LUN|
|資料磁碟寫入位元組/秒|資料磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在監視期間寫入單磁碟的位元組/秒數|LUN|
|資料磁碟讀取操作/秒|資料磁碟讀取作業/秒 (預覽)|每秒計數|Average|在監視期間從單磁碟讀取 IOPS|LUN|
|資料磁碟寫入操作/秒|資料磁碟寫入作業/秒 (預覽)|每秒計數|Average|在監視期間從單磁碟寫入 IOPS|LUN|
|資料磁碟佇列深度|資料磁碟佇列深度(預覽)|Count|Average|資料磁碟佇列深度 (或佇列長度)|LUN|
|OS 磁碟讀取位元組/秒|OS 磁碟讀取位元組/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取位元組/秒|None|
|操作系統磁碟寫入位元組/秒|OS 磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間寫入單磁碟的位元組/秒|None|
|操作系統磁碟讀取操作/秒|OS 磁碟讀取作業/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取 IOPS|None|
|操作系統磁碟寫入操作/秒|OS 磁碟寫入作業/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟寫入 IOPS|None|
|OS 磁碟佇列深度|操作系統磁碟佇列深度(預覽)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|None|
|連入流量|連入流量|Count|Average|入站流是入站方向的電流數(進入 VM 的流量)|None|
|出站流|出站流|Count|Average|出站流是出站方向的電流數(從 VM 流出的流量)|None|
|將站流最大建立速率|將站流最大建立速率|每秒計數|Average|將站流的最大建立速率(進入 VM 的流量)|None|
|出站流最大建立速率|出站流最大建立速率|每秒計數|Average|出站流的最大建立速率(從 VM 流出的流量)|None|
|進階資料磁碟快取|進階資料磁碟快取讀取命中(預覽)|百分比|Average|進階資料磁碟快取|LUN|
|進階資料磁碟快取讀取誤區|進階資料磁碟快取讀取誤讀(預覽)|百分比|Average|進階資料磁碟快取讀取誤區|LUN|
|進階作業系統磁碟快取|進階作業系統磁碟快取讀取命中(預覽)|百分比|Average|進階作業系統磁碟快取|None|
|進階 OS 磁碟快取誤區|進階作業系統磁碟快取讀取誤讀(預覽)|百分比|Average|進階 OS 磁碟快取誤區|None|
|網路總數|網路總數|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|None|
|網路出出總計|網路出出總計|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|Average|目前虛擬機器正在使用中的已配置計算單位百分比|VMName|
|Network In|網路在計費(已棄用)|位元組|總計|虛擬機器(傳入流量)在所有網路介面上接收的計費位元組數(已棄用)|VMName|
|Network Out|網路出計費(已棄用)|位元組|總計|虛擬機器(傳出流量)在所有網路介面上計費位元組數(已棄用)|VMName|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|監視期間從磁碟讀取的位元組|VMName|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|監視期間寫入磁碟的位元組|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS|VMName|
|剩餘 CPU 信用額度|剩餘 CPU 信用額度|Count|Average|可用於高載的信用額度總數|None|
|CPU Credits Consumed|CPU Credits Consumed|Count|Average|由虛擬機器取用的信用額度總數|None|
|每一磁碟讀取位元組/秒|資料磁碟讀取位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|監視期間從單磁碟讀取位元組/秒|SlotId|
|每一磁碟寫入位元組/秒|資料磁碟寫入位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間寫入單磁碟的位元組/秒數|SlotId|
|每一磁碟讀取作業/秒|資料磁碟讀取操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單磁碟讀取 IOPS|SlotId|
|每一磁碟寫入作業/秒|資料磁碟寫入操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單磁碟寫入 IOPS|SlotId|
|每一磁碟 QD|資料磁碟[QD(已棄用)](portal-disk-metrics-deprecation.md)|Count|Average|資料磁碟佇列深度 (或佇列長度)|SlotId|
|OS 每一磁碟讀取位元組/秒|OS 磁碟讀取位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取位元組/秒|None|
|OS 每一磁碟寫入位元組/秒|OS 磁碟寫入位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間寫入單磁碟的位元組/秒|None|
|OS 每一磁碟讀取作業/秒|OS 磁碟讀取操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取 IOPS|None|
|OS 每一磁碟寫入作業/秒|OS 磁碟寫入操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟寫入 IOPS|None|
|OS 每一磁碟 QD|OS 磁碟[QD(已棄用)](portal-disk-metrics-deprecation.md)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|None|
|資料磁碟讀取位元組/秒|資料磁碟讀取位元組/秒 (預覽)|每秒計數|Average|監視期間從單磁碟讀取位元組/秒|LUN,VM 名稱|
|資料磁碟寫入位元組/秒|資料磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在監視期間寫入單磁碟的位元組/秒數|LUN,VM 名稱|
|資料磁碟讀取操作/秒|資料磁碟讀取作業/秒 (預覽)|每秒計數|Average|在監視期間從單磁碟讀取 IOPS|LUN,VM 名稱|
|資料磁碟寫入操作/秒|資料磁碟寫入作業/秒 (預覽)|每秒計數|Average|在監視期間從單磁碟寫入 IOPS|LUN,VM 名稱|
|資料磁碟佇列深度|資料磁碟佇列深度(預覽)|Count|Average|資料磁碟佇列深度 (或佇列長度)|LUN,VM 名稱|
|OS 磁碟讀取位元組/秒|OS 磁碟讀取位元組/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取位元組/秒|VMName|
|操作系統磁碟寫入位元組/秒|OS 磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間寫入單磁碟的位元組/秒|VMName|
|操作系統磁碟讀取操作/秒|OS 磁碟讀取作業/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取 IOPS|VMName|
|操作系統磁碟寫入操作/秒|OS 磁碟寫入作業/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟寫入 IOPS|VMName|
|OS 磁碟佇列深度|操作系統磁碟佇列深度(預覽)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|VMName|
|連入流量|連入流量|Count|Average|入站流是入站方向的電流數(進入 VM 的流量)|VMName|
|出站流|出站流|Count|Average|出站流是出站方向的電流數(從 VM 流出的流量)|VMName|
|將站流最大建立速率|將站流最大建立速率|每秒計數|Average|將站流的最大建立速率(進入 VM 的流量)|VMName|
|出站流最大建立速率|出站流最大建立速率|每秒計數|Average|出站流的最大建立速率(從 VM 流出的流量)|VMName|
|進階資料磁碟快取|進階資料磁碟快取讀取命中(預覽)|百分比|Average|進階資料磁碟快取|LUN,VM 名稱|
|進階資料磁碟快取讀取誤區|進階資料磁碟快取讀取誤讀(預覽)|百分比|Average|進階資料磁碟快取讀取誤區|LUN,VM 名稱|
|進階作業系統磁碟快取|進階作業系統磁碟快取讀取命中(預覽)|百分比|Average|進階作業系統磁碟快取|VMName|
|進階 OS 磁碟快取誤區|進階作業系統磁碟快取讀取誤讀(預覽)|百分比|Average|進階 OS 磁碟快取誤區|VMName|
|網路總數|網路總數|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|VMName|
|網路出出總計|網路出出總計|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|百分比|Average|目前虛擬機器正在使用中的已配置計算單位百分比|None|
|Network In|網路在計費(已棄用)|位元組|總計|虛擬機器(傳入流量)在所有網路介面上接收的計費位元組數(已棄用)|None|
|Network Out|網路出計費(已棄用)|位元組|總計|虛擬機器(傳出流量)在所有網路介面上計費位元組數(已棄用)|None|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|監視期間從磁碟讀取的位元組|None|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|監視期間寫入磁碟的位元組|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS|None|
|剩餘 CPU 信用額度|剩餘 CPU 信用額度|Count|Average|可用於高載的信用額度總數|None|
|CPU Credits Consumed|CPU Credits Consumed|Count|Average|由虛擬機器取用的信用額度總數|None|
|每一磁碟讀取位元組/秒|資料磁碟讀取位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|監視期間從單磁碟讀取位元組/秒|SlotId|
|每一磁碟寫入位元組/秒|資料磁碟寫入位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間寫入單磁碟的位元組/秒數|SlotId|
|每一磁碟讀取作業/秒|資料磁碟讀取操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單磁碟讀取 IOPS|SlotId|
|每一磁碟寫入作業/秒|資料磁碟寫入操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在監視期間從單磁碟寫入 IOPS|SlotId|
|每一磁碟 QD|資料磁碟[QD(已棄用)](portal-disk-metrics-deprecation.md)|Count|Average|資料磁碟佇列深度 (或佇列長度)|SlotId|
|OS 每一磁碟讀取位元組/秒|OS 磁碟讀取位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取位元組/秒|None|
|OS 每一磁碟寫入位元組/秒|OS 磁碟寫入位元組/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間寫入單磁碟的位元組/秒|None|
|OS 每一磁碟讀取作業/秒|OS 磁碟讀取操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取 IOPS|None|
|OS 每一磁碟寫入作業/秒|OS 磁碟寫入操作/秒[(已棄用)](portal-disk-metrics-deprecation.md)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟寫入 IOPS|None|
|OS 每一磁碟 QD|OS 磁碟[QD(已棄用)](portal-disk-metrics-deprecation.md)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|None|
|資料磁碟讀取位元組/秒|資料磁碟讀取位元組/秒 (預覽)|每秒計數|Average|監視期間從單磁碟讀取位元組/秒|LUN|
|資料磁碟寫入位元組/秒|資料磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在監視期間寫入單磁碟的位元組/秒數|LUN|
|資料磁碟讀取操作/秒|資料磁碟讀取作業/秒 (預覽)|每秒計數|Average|在監視期間從單磁碟讀取 IOPS|LUN|
|資料磁碟寫入操作/秒|資料磁碟寫入作業/秒 (預覽)|每秒計數|Average|在監視期間從單磁碟寫入 IOPS|LUN|
|資料磁碟佇列深度|資料磁碟佇列深度(預覽)|Count|Average|資料磁碟佇列深度 (或佇列長度)|LUN|
|OS 磁碟讀取位元組/秒|OS 磁碟讀取位元組/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取位元組/秒|None|
|操作系統磁碟寫入位元組/秒|OS 磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間寫入單磁碟的位元組/秒|None|
|操作系統磁碟讀取操作/秒|OS 磁碟讀取作業/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟讀取 IOPS|None|
|操作系統磁碟寫入操作/秒|OS 磁碟寫入作業/秒 (預覽)|每秒計數|Average|在作業系統磁碟的監視期間從單磁碟寫入 IOPS|None|
|OS 磁碟佇列深度|操作系統磁碟佇列深度(預覽)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|None|
|連入流量|連入流量|Count|Average|入站流是入站方向的電流數(進入 VM 的流量)|None|
|出站流|出站流|Count|Average|出站流是出站方向的電流數(從 VM 流出的流量)|None|
|將站流最大建立速率|將站流最大建立速率|每秒計數|Average|將站流的最大建立速率(進入 VM 的流量)|None|
|出站流最大建立速率|出站流最大建立速率|每秒計數|Average|出站流的最大建立速率(從 VM 流出的流量)|None|
|進階資料磁碟快取|進階資料磁碟快取讀取命中(預覽)|百分比|Average|進階資料磁碟快取|LUN|
|進階資料磁碟快取讀取誤區|進階資料磁碟快取讀取誤讀(預覽)|百分比|Average|進階資料磁碟快取讀取誤區|LUN|
|進階作業系統磁碟快取|進階作業系統磁碟快取讀取命中(預覽)|百分比|Average|進階作業系統磁碟快取|None|
|進階 OS 磁碟快取誤區|進階作業系統磁碟快取讀取誤讀(預覽)|百分比|Average|進階 OS 磁碟快取誤區|None|
|網路總數|網路總數|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|None|
|網路出出總計|網路出出總計|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CpuUsage|CPU 使用率|Count|Average|所有核心的 CPU 使用率 (以 millicores 為單位)。|containerName|
|MemoryUsage|記憶體使用量|位元組|Average|總記憶體使用率 (位元組)。|containerName|
|NetworkBytesReceivedPerSecond|每秒接收的網路位元組數|位元組|Average|每秒接收的網路位元組數。|None|
|NetworkBytesReceivedPerSecond|每秒傳輸的網路位元組數|位元組|Average|每秒傳輸的網路位元組數。|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|總拉計數|總拉取計數|Count|Average|影像拉取總數|None|
|成功拉計數|成功拉取計數|Count|Average|成功影像擷取次數|None|
|總推送計數|總推送計數|Count|Average|影像推送總數|None|
|成功推送計數|成功推送計數|Count|Average|成功影像推送數|None|
|執行時間|執行持續期間|毫秒|總計|以毫秒為單位執行持續時間|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|受控叢集中可用的 cpu 核心總數|Count|Average|受控叢集中可用的 cpu 核心總數|None|
|kube_node_status_allocatable_memory_bytes|受控叢集中可用的記憶體總量|位元組|Average|受控叢集中可用的記憶體總量|None|
|kube_pod_status_ready|處於就緒狀態的 Pod 數目|Count|Average|處於就緒狀態的 Pod 數目|命名空間,pod|
|kube_node_status_condition|各種節點條件的狀態|Count|Average|各種節點條件的狀態|條件,狀態,狀態2,節點|
|kube_pod_status_phase|各階段的 Pod 數目|Count|Average|各階段的 Pod 數目|相位、命名空間、pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>微軟.自定義供應商/資源供應商

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|成功滿要求|成功的要求|Count|總計|自訂提供者發出的成功要求|HTTPMethod 呼叫路徑,狀態代碼|
|FailedRequests|失敗的要求|Count|總計|取得自訂資源提供者誌|HTTPMethod 呼叫路徑,狀態代碼|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>微軟.DataBoxEdge/DataBox邊緣設備

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|NICRead輸送量|讀取輸送量(網路)|每秒位元組|Average|在報告期內,閘道中所有卷設備上的網路介面的讀取輸送量。|InstanceName|
|NICWrite輸送量|寫入輸送量(網路)|每秒位元組|Average|在報告期內,閘道中所有卷的網路介面的寫入輸送量。|InstanceName|
|雲讀取輸送量PerShare|雲下載輸送量(共用)|每秒位元組|Average|報告期間從共用下載到 Azure 的輸送量。|共用|
|雲上傳輸送量PerShare|雲上傳輸送量(共用)|每秒位元組|Average|報告期間從共享上載到 Azure 的輸送量。|共用|
|位元組上傳到雲佩爾共用|上傳雲位元組(共用)|位元組|Average|報告期間從共用上載到 Azure 的位元組總數。|共用|
|總容量|總容量|位元組|Average|總容量|None|
|可用容量|可用容量|位元組|Average|報告期內的可用容量(以位元組為單位)。|None|
|雲上傳輸送量|雲上傳輸送量|每秒位元組|Average|在報告期間,雲將輸送量上載到 Azure。|None|
|雲讀取輸送量|雲下載輸送量|每秒位元組|Average|在報告期間,雲將輸送量下載到 Azure。|None|
|位元組上傳到雲|上傳雲位元組(裝置)|位元組|Average|報告期間從設備上載到 Azure 的位元組總數。|None|
|超V虛擬處理器利用|邊緣計算 - CPU 百分比|百分比|Average|CPU 使用率|InstanceName|
|超V記憶體利用|邊緣計算 ─記憶體使用|百分比|Average|使用的 RAM 量|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>微軟資料目錄/資料目錄

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|依分類劃分的資產分配|依分類分列的資產分配|Count|總計|指示分配了特定分類的資產數,即它們與該標籤一起分類。|分類,來源|
|依儲存類型配置資產|依儲存類型配置資產|Count|總計|指示具有特定存儲類型的資產數。|StorageType|
|具有分類的資產數量|具有至少一個分類的資產數量|Count|Average|指示具有至少一個標記分類的資產數。|None|
|掃描取消|掃描已取消|Count|總計|指示已取消的掃描數。|None|
|掃描完成|掃描已完成|Count|總計|指示成功完成的掃描數。|None|
|掃描失敗|掃描失敗|Count|總計|指示掃描失敗數。|None|
|掃描時間|掃描時間|秒|總計|指示總掃描時間(以秒為單位)。|None|
|目錄活動使用者|每日活躍使用者|Count|總計|每日活動使用者數|None|
|目錄使用|依操作配置使用方式|Count|總計|指示使用者對目錄的操作數,即訪問、搜索、術語表。|作業|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|FailedRuns|失敗的執行|Count|總計||導管名稱,活動名稱|
|SuccessfulRuns|成功的執行|Count|總計||導管名稱,活動名稱|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PipelineFailedRuns|失敗的管線執行計量|Count|總計||失敗類型,名稱|
|PipelineSucceededRuns|成功的管線執行計量|Count|總計||失敗類型,名稱|
|管管取消執行|已取消的導管執行指標|Count|總計||失敗類型,名稱|
|ActivityFailedRuns|失敗的活動執行計量|Count|總計||活動型態、導管名稱、失敗類型、名稱|
|ActivitySucceededRuns|成功的活動執行計量|Count|總計||活動型態、導管名稱、失敗類型、名稱|
|作用已取消執行|已取消的活動執行指標|Count|總計||活動型態、導管名稱、失敗類型、名稱|
|TriggerFailedRuns|失敗的觸發程序執行計量|Count|總計||名稱,失敗類型|
|TriggerSucceededRuns|成功的觸發程序執行計量|Count|總計||名稱,失敗類型|
|觸發器取消執行|已取消的觸發器執行指標|Count|總計||名稱,失敗類型|
|IntegrationRuntimeCpuPercentage|整合執行階段 CPU 使用率|百分比|Average||整合執行時名稱,節點名稱|
|IntegrationRuntimeAvailableMemory|整合執行階段可用記憶體|位元組|Average||整合執行時名稱,節點名稱|
|整合執行時平均工作提取延遲|整合執行時佇列持續時間|秒|Average||整合執行時名稱|
|整合執行時要排程長度|整合執行時佇列長度|Count|Average||整合執行時名稱|
|整合執行時可以使用節點編號|整合執行時可以取得節點計數|Count|Average||整合執行時名稱|
|最大允許資源計數|允許的最大實體計數|Count|最大值||None|
|最大允許工廠尺寸單位|允許的最大出廠規模(GB 單位)|Count|最大值||None|
|資源計數|實體計數總數|Count|最大值||None|
|工廠尺寸單位|工廠總規模(GB 單位)|Count|最大值||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|JobEndedSuccess|成功的作業|Count|總計|成功作業計數。|None|
|JobEndedFailure|失敗的作業|Count|總計|失敗作業計數。|None|
|JobEndedCancelled|取消的作業|Count|總計|取消作業計數。|None|
|JobAUEndedSuccess|成功 AU 時間|秒|總計|成功作業的 AU 時間總計。|None|
|JobAUEndedFailure|失敗 AU 時間|秒|總計|失敗作業的 AU 時間總計。|None|
|JobAUEndedCancelled|取消的 AU 時間|秒|總計|取消作業的 AU 時間總計。|None|
|工作階段|階段中的工作|Count|總計|每個階段的作業數。|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TotalStorage|儲存體總計|位元組|最大值|儲存在帳戶中的總資料量。|None|
|DataWritten|寫入的資料|位元組|總計|寫入帳戶的總資料量。|None|
|DataRead|讀取的資料|位元組|總計|從帳戶讀取的總資料量。|None|
|WriteRequests|寫入要求|Count|總計|帳戶的資料寫入要求計數。|None|
|ReadRequests|讀取要求|Count|總計|帳戶的資料讀取要求計數。|None|


## <a name="microsoftdatashareaccounts"></a>微軟.資料共享/帳戶

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|共用計數|已送出共享|Count|最大值|帳號中已傳送的共享數|ShareName|
|共用訂閱計數|已接收股份|Count|最大值|帳號中已接收的股數|共用訂閱名稱|
|成功分享同步|已送出的分享成功快照|Count|Count|帳號中已送出的已送出分享成功快照數|None|
|失敗的分享同步|送出的分享失敗快照|Count|Count|帳號中傳送的分享失敗快照數|None|
|成功分享訂閱同步|已接收的分享成功快照|Count|Count|帳戶中已接收的已接收共用成功快照數|None|
|失敗的分享訂閱同步|收到的分享失敗快照|Count|Count|帳號中已接收的分享失敗快照數|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|None|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|None|
|io_consumption_percent|IO 百分比|百分比|Average|IO 百分比|None|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|None|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|None|
|storage_limit|儲存體限制|位元組|最大值|儲存體限制|None|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|Average|伺服器記錄儲存體百分比|None|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|Average|使用的伺服器記錄儲存體|None|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|Average|伺服器記錄儲存體限制|None|
|active_connections|作用中的連線|Count|Average|作用中的連線|None|
|connections_failed|失敗的連線|Count|總計|失敗的連線|None|
|seconds_behind_master|複寫延遲 (秒)|Count|最大值|複寫延遲 (秒)|None|
|backup_storage_used|使用的備份儲存|位元組|Average|使用的備份儲存|None|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|None|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|None|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|None|
|io_consumption_percent|IO 百分比|百分比|Average|IO 百分比|None|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|None|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|None|
|storage_limit|儲存體限制|位元組|最大值|儲存體限制|None|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|Average|伺服器記錄儲存體百分比|None|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|Average|使用的伺服器記錄儲存體|None|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|最大值|伺服器記錄儲存體限制|None|
|active_connections|作用中的連線|Count|Average|作用中的連線|None|
|connections_failed|失敗的連線|Count|總計|失敗的連線|None|
|seconds_behind_master|複寫延遲 (秒)|Count|最大值|複寫延遲 (秒)|None|
|backup_storage_used|使用的備份儲存|位元組|Average|使用的備份儲存|None|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|None|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|None|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|None|
|io_consumption_percent|IO 百分比|百分比|Average|IO 百分比|None|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|None|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|None|
|storage_limit|儲存體限制|位元組|最大值|儲存體限制|None|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|Average|伺服器記錄儲存體百分比|None|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|Average|使用的伺服器記錄儲存體|None|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|最大值|伺服器記錄儲存體限制|None|
|active_connections|作用中的連線|Count|Average|作用中的連線|None|
|connections_failed|失敗的連線|Count|總計|失敗的連線|None|
|backup_storage_used|使用的備份儲存|位元組|Average|使用的備份儲存|None|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|None|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|None|
|pg_replica_log_delay_in_seconds|複本延隔時間|秒|最大值|複製延遲(以秒為單位)|None|
|pg_replica_log_delay_in_bytes|複本之間的最大延隔時間|位元組|最大值|滯後複本位元組數時延遲|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>微軟.DBforPostgreSQL/伺服器2

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|None|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|None|
|伊奧普斯|IOPS|Count|Average|每秒IO操作|None|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|None|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|None|
|active_connections|作用中的連線|Count|Average|作用中的連線|None|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|None|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|None|


## <a name="microsoftdbforpostgresqlsingleservers"></a>微軟.DBforPostgreSQL/單伺服器

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|None|
|memory_percent|記憶體百分比|百分比|Average|記憶體百分比|None|
|伊奧普斯|IOPS|Count|Average|每秒IO操作|None|
|storage_percent|儲存體百分比|百分比|Average|儲存體百分比|None|
|storage_used|已使用儲存體|位元組|Average|已使用儲存體|None|
|active_connections|作用中的連線|Count|Average|作用中的連線|None|
|network_bytes_egress|Network Out|位元組|總計|跨作用中連線的網路輸出|None|
|network_bytes_ingress|Network In|位元組|總計|跨作用中連線的網路輸入|None|
|connections_failed|失敗的連線|Count|總計|失敗的連線|None|
|connections_succeeded|成功連線|Count|總計|成功連線|None|
|maximum_used_transactionIDs|最大已使用交易代碼|Count|Average|最大已使用交易代碼|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遙測訊息傳送嘗試|Count|總計|要嘗試傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|None|
|d2c.telemetry.ingress.success|已傳送的遙測訊息|Count|總計|成功傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|None|
|c2d.commands.egress.complete.success|已完成 C2D 訊息傳遞|Count|總計|裝置成功完成的雲端到裝置訊息傳遞數量|None|
|c2d.commands.egress.abandon.success|C2D 訊息已放棄|Count|總計|裝置放棄的雲端到裝置訊息數|None|
|c2d.commands.egress.reject.success|C2D 郵件被拒絕|Count|總計|裝置拒絕的雲端到裝置訊息數|None|
|C2D 訊息過期|C2D 郵件已過期(預覽)|Count|總計|過期的雲到裝置訊息數|None|
|devices.totalDevices|裝置總計 (已淘汰)|Count|總計|向 IoT 中樞註冊的裝置數目|None|
|devices.connectedDevices.allProtocol|連接的裝置 (已淘汰) |Count|總計|連接至 IoT 中樞的裝置數目|None|
|d2c.telemetry.egress.success|路由：已傳遞的遙測訊息|Count|總計|使用 IoT 中樞路由成功地將訊息傳遞到所有端點的次數。 如果將訊息路由至多個端點，這個值會為每一次成功傳遞加 1。 如果將訊息多次傳遞到同一個端點，這個值會為每一次成功傳遞加 1。|None|
|d2c.telemetry.egress.dropped|路由：已捨棄的遙測訊息 |Count|總計|IoT 中樞路由因為端點無效而捨棄訊息的次數。 這個值不會計算傳遞到後援路由的訊息，因為捨棄的訊息不會傳遞到那裡。|None|
|d2c.telemetry.egress.orphaned|路由：已遺棄的遙測訊息 |Count|總計|IoT 中樞路由因為訊息不符合任何路由規則 (包括後援規則) 而遺棄訊息的次數。 |None|
|d2c.telemetry.egress.invalid|路由：不相容的遙測訊息|Count|總計|IoT 中樞路由因為與端點不相容而無法傳遞訊息的次數。 這個值不包含重試次數。|None|
|d2c.telemetry.egress.fallback|路由：傳遞至後援的訊息|Count|總計|IoT 中樞路由傳遞訊息至與後援路由相關聯之端點的次數。|None|
|d2c.endpoints.egress.eventHubs|路由：傳遞至事件中樞的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至事件中樞端點的次數。|None|
|d2c.endpoints.latency.eventHubs|路由：事件中樞的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到事件中樞端點之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.serviceBusQueues|路由：傳遞至服務匯流排佇列的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排佇列端點的次數。|None|
|d2c.endpoints.latency.serviceBusQueues|路由：服務匯流排佇列的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.serviceBusTopics|路由：傳遞至服務匯流排主題的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排主題端點的次數。|None|
|d2c.endpoints.latency.serviceBusTopics|路由：服務匯流排主題的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.builtIn.events|路由：傳遞至訊息/事件的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至內建端點 (訊息/事件) 的次數。|None|
|d2c.endpoints.latency.builtIn.events|路由：訊息/事件的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到內建端點 (訊息/事件) 之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.storage|路由：傳遞至儲存體的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至儲存體端點的次數。|None|
|d2c.endpoints.latency.storage|路由：儲存體的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到儲存體端點之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.storage.bytes|路由：傳遞至儲存體的資料|位元組|總計|IoT 中樞路由傳遞至儲存體端點的資料量 (位元組)。|None|
|d2c.endpoints.egress.storage.blobs|路由：傳遞至儲存體的 BLOB|Count|總計|IoT 中樞路由將 BLOB 傳遞至儲存體端點的次數。|None|
|事件格線交付|事件格線交付(預覽)|Count|總計|發佈到事件網格的 IoT 中心事件數。 對成功和失敗的請求數使用"結果"維度。 事件類型維度顯示事件類型 (。https://aka.ms/ioteventgrid)|資源 Id、結果、事件類型|
|事件格格延遲|事件格線延遲 (預覽)|毫秒|Average|從生成 Iot 中心事件到將事件發佈到事件網格的平均延遲(毫秒)。 此數位是所有事件類型之間的平均值。 使用事件類型維度查看特定類型的事件的延遲。|資源 Id,事件型態|
|路由交付|路由交付(預覽)|毫秒|總計|IoT 中心嘗試使用路由將消息傳遞到所有終結點的次數。 要查看成功或失敗的嘗試次數,請使用"結果"維度。 要查看失敗原因(如無效、刪除或孤立),請使用故障原因類別維度。 您還可以使用終結點名稱和終結點類型維度來瞭解傳遞到不同終結點的消息數。 每次傳遞嘗試的指標值都會增加一個,包括郵件是否傳遞到多個終結點,或者郵件是否多次傳遞到同一終結點。|資源 Id、終結點類型、終結點名稱、失敗原因類別、結果、路由源|
|路由交付延遲|路由傳遞延遲(預覽)|毫秒|Average|消息入口到 IoT 中心和遙測消息進入終結點之間的平均延遲(毫秒)。 您可以使用端點名稱和終結點類型維度來瞭解不同終結點的延遲。|資源 Id、終結點類型、終結點名稱、路由源|
|d2c.twin.read.success|裝置的成功對應項讀取|Count|總計|裝置起始的所有成功對應項讀取的計數。|None|
|d2c.twin.read.failure|裝置的失敗對應項讀取|Count|總計|裝置起始的所有失敗對應項讀取的計數。|None|
|d2c.twin.read.size|裝置的對應項讀取回應大小|位元組|Average|裝置起始的所有成功對應項讀取的平均值、最小值和最大值。|None|
|d2c.twin.update.success|裝置的成功對應項更新|Count|總計|裝置起始的所有成功對應項更新的計數。|None|
|d2c.twin.update.failure|裝置的失敗對應項更新|Count|總計|裝置起始的所有失敗對應項更新的計數。|None|
|d2c.twin.update.size|裝置的對應項更新大小|位元組|Average|裝置起始的所有成功對應項更新的大小平均值、最小值和最大值。|None|
|c2d.methods.success|成功直接方法叫用|Count|總計|所有成功直接方法呼叫的計數。|None|
|c2d.methods.failure|失敗直接方法叫用|Count|總計|所有失敗直接方法呼叫的計數。|None|
|c2d.methods.requestSize|直接方法叫用的要求大小|位元組|Average|所有成功直接方法要求的平均值、最小值和最大值。|None|
|c2d.methods.responseSize|直接方法叫用的回應大小|位元組|Average|所有成功直接方法回應的平均值、最小值和最大值。|None|
|c2d.twin.read.success|後端的成功對應項讀取|Count|總計|後端起始的所有成功對應項讀取的計數。|None|
|c2d.twin.read.failure|後端的失敗對應項讀取|Count|總計|後端起始的所有失敗對應項讀取的計數。|None|
|c2d.twin.read.size|後端的對應項讀取回應大小|位元組|Average|後端起始的所有成功對應項讀取的平均值、最小值和最大值。|None|
|c2d.twin.update.success|後端的成功對應項更新|Count|總計|後端起始的所有成功對應項更新的計數。|None|
|c2d.twin.update.failure|後端的失敗對應項更新|Count|總計|後端起始的所有失敗對應項更新的計數。|None|
|c2d.twin.update.size|後端的對應項更新大小|位元組|Average|後端起始的所有成功對應項更新的大小平均值、最小值和最大值。|None|
|twinQueries.success|成功對應項查詢|Count|總計|所有成功對應項查詢的計數。|None|
|twinQueries.failure|失敗對應項查詢|Count|總計|所有失敗對應項查詢的計數。|None|
|twinQueries.resultSize|對應項查詢結果大小|位元組|Average|所有成功對應項查詢的結果大小平均值、最小值和最大值。|None|
|jobs.createTwinUpdateJob.success|成功建立的對應項更新作業|Count|總計|所有成功建立的對應項更新作業計數。|None|
|jobs.createTwinUpdateJob.failure|建立失敗的對應項更新作業|Count|總計|所有建立失敗的對應項更新作業計數。|None|
|jobs.createDirectMethodJob.success|成功建立的方法叫用作業|Count|總計|所有成功建立的直接方法叫用作業計數。|None|
|jobs.createDirectMethodJob.failure|建立失敗的方法叫用作業|Count|總計|所有建立失敗的直接方法叫用作業計數。|None|
|jobs.listJobs.success|成功呼叫列出作業|Count|總計|所有成功呼叫列出作業的計數。|None|
|jobs.listJobs.failure|呼叫列出作業失敗|Count|總計|所有呼叫列出作業失敗的計數。|None|
|jobs.cancelJob.success|成功取消作業|Count|總計|所有成功呼叫取消作業的計數。|None|
|jobs.cancelJob.failure|取消作業失敗|Count|總計|所有呼叫取消作業失敗的計數。|None|
|jobs.queryJobs.success|成功作業查詢|Count|總計|所有成功呼叫查詢作業的計數。|None|
|jobs.queryJobs.failure|失敗作業查詢|Count|總計|所有呼叫查詢作業失敗的計數。|None|
|jobs.completed|已完成的工作|Count|總計|所有已完成的作業計數。|None|
|jobs.failed|失敗作業|Count|總計|所有失敗作業計數。|None|
|d2c.telemetry.ingress.sendThrottle|節流錯誤數目|Count|總計|因裝置輸送量節流而導致的節流錯誤數目|None|
|dailyMessageQuotaUsed|已使用的訊息總數|Count|Average|今日已使用的總訊息數|None|
|deviceDataUsage|裝置資料使用總量|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|None|
|deviceDataUsageV2|裝置資料使用量總計 (預覽)|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|None|
|totalDeviceCount|裝置總計 (預覽)|Count|Average|向 IoT 中樞註冊的裝置數目|None|
|connectedDeviceCount|連接的裝置 (預覽)|Count|Average|連接至 IoT 中樞的裝置數目|None|
|組態|設定計量|Count|總計|設定作業的計量|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RegistrationAttempts|註冊嘗試數|Count|總計|嘗試的裝置註冊數|預配服務名稱,IotHub 名稱,狀態|
|DeviceAssignments|已指派的裝置數|Count|總計|已指派到 IoT 中樞的裝置數|預先定義服務名稱,IotHub 名稱|
|AttestationAttempts|證明嘗試數|Count|總計|嘗試的裝置證明數|預先定義服務名稱、狀態、協定|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|新增區域|已新增的區域|Count|Count|已新增的區域|區域|
|AvailableStorage|可用的儲存體|位元組|總計|在 5 分鐘粒度下報告的可用儲存總數|集合名稱、資料庫名稱、區域|
|CassandraConnectionClosures|Cassandra 連線終止|Count|總計|已關閉的 Cassandra 連線數,以 1 分鐘粒度報告|API 類型、區域、封閉原因|
|卡桑德拉鍵空格刪除|卡桑德拉·基空間已刪除|Count|Count|卡桑德拉·基空間已刪除|資源名稱、ApiKind、ApiKind 資源類型、操作類型|
|卡桑德拉基空間輸送量更新|卡桑德拉鍵空間輸送量更新|Count|Count|卡桑德拉鍵空間輸送量更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|卡桑德拉基空間更新|卡桑德拉鍵空間更新|Count|Count|卡桑德拉鍵空間更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|CassandraRequestCharges|Cassandra 要求費用|Count|總計|為卡桑德拉請求使用的 R|API 類型、資料庫名稱、集合名稱、區域、操作類型、資源類型|
|CassandraRequests|Cassandra 要求|Count|Count|提出的卡桑德拉請求數量|API 類型、資料庫名稱、集合名稱、區域、操作類型、資源類型、錯誤代碼|
|卡桑德拉錶刪除|卡桑德拉錶已刪除|Count|Count|卡桑德拉錶已刪除|資源名稱、子資源名稱、ApiKind、ApiKind 資源類型、操作類型|
|卡桑德拉錶通量更新|卡桑德拉錶輸送量已更新|Count|Count|卡桑德拉錶輸送量已更新|資源名稱、子資源名稱、ApiKind、Apikind 資源類型、輸送量請求|
|Catt更新|卡桑德拉錶已更新|Count|Count|卡桑德拉錶已更新|資源名稱、子資源名稱、ApiKind、Apikind 資源類型、輸送量請求|
|建立帳戶|已建立帳戶|Count|Count|已建立帳戶|None|
|DataUsage|資料使用量|位元組|總計|在 5 分鐘粒度下報告的總資料使用量|集合名稱、資料庫名稱、區域|
|刪除帳號|帳號已移除|Count|Count|帳號已移除|None|
|DocumentCount|文件計數|Count|總計|以 5 分鐘粒度報告的文件計數總數|集合名稱、資料庫名稱、區域|
|DocumentQuota|文件配額|位元組|總計|在 5 分鐘粒度下報告的總儲存配額|集合名稱、資料庫名稱、區域|
|格雷姆林資料庫刪除|格林林資料庫已刪除|Count|Count|格林林資料庫已刪除|資源名稱、ApiKind、ApiKind 資源類型、操作類型|
|格雷姆林資料庫輸送量更新|格雷姆林資料庫輸送量更新|Count|Count|格雷姆林資料庫輸送量更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|格雷姆林資料庫更新|格雷姆林資料庫已更新|Count|Count|格雷姆林資料庫已更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|格雷姆林圖刪除|格林林圖形已移除|Count|Count|格林林圖形已移除|資源名稱、子資源名稱、ApiKind、ApiKind 資源類型、操作類型|
|格雷姆林圖輸送量更新|格雷姆林圖形輸送量已更新|Count|Count|格雷姆林圖形輸送量已更新|資源名稱、子資源名稱、ApiKind、Apikind 資源類型、輸送量請求|
|格雷姆林圖形更新|格雷姆林圖形更新|Count|Count|格雷姆林圖形更新|資源名稱、子資源名稱、ApiKind、Apikind 資源類型、輸送量請求|
|IndexUsage|索引使用量|位元組|總計|在 5 分鐘粒度下報告的總索引使用方式|集合名稱、資料庫名稱、區域|
|MetadataRequests|中繼資料要求|Count|Count|中繼資料要求計數。 Cosmos DB 會維護每個帳戶的系統中繼資料集合，可讓您免費列舉集合、資料庫等及其設定。|資料庫名稱、集合名稱、區域、狀態代碼、角色|
|蒙戈收集刪除|蒙戈集合已刪除|Count|Count|蒙戈集合已刪除|資源名稱、子資源名稱、ApiKind、ApiKind 資源類型、操作類型|
|蒙戈收集輸送量更新|蒙戈收集輸送量已更新|Count|Count|蒙戈收集輸送量已更新|資源名稱、子資源名稱、ApiKind、Apikind 資源類型、輸送量請求|
|蒙戈收集更新|蒙戈集合已更新|Count|Count|蒙戈集合已更新|資源名稱、子資源名稱、ApiKind、Apikind 資源類型、輸送量請求|
|蒙戈DB資料庫更新|蒙戈資料庫已更新|Count|Count|蒙戈資料庫已更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|蒙戈資料庫刪除|蒙戈資料庫已刪除|Count|Count|蒙戈資料庫已刪除|資源名稱、ApiKind、ApiKind 資源類型、操作類型|
|蒙戈資料庫輸送量更新|蒙戈資料庫輸送量已更新|Count|Count|蒙戈資料庫輸送量已更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|MongoRequestCharge|Mongo 要求收費|Count|總計|已使用的 Mongo 要求單位|資料庫名稱、集合名稱、區域、指令名稱、錯誤代碼、狀態|
|MongoRequests|Mongo 要求|Count|Count|已提出的 Mongo 要求數目|資料庫名稱、集合名稱、區域、指令名稱、錯誤代碼、狀態|
|蒙戈請求計數|蒙戈請求率|每秒計數|Average|蒙戈請求每秒計數|資料庫名稱、集合名稱、區域、指令名稱、錯誤代碼|
|蒙戈請求刪除|蒙戈刪除要求率|每秒計數|Average|每秒移除 Mongo 請求|資料庫名稱、集合名稱、區域、指令名稱、錯誤代碼|
|蒙戈請求插入|蒙戈插入要求率|每秒計數|Average|蒙戈每秒插入計數|資料庫名稱、集合名稱、區域、指令名稱、錯誤代碼|
|蒙戈請求查詢|蒙戈查詢要求率|每秒計數|Average|每秒蒙戈查詢請求|資料庫名稱、集合名稱、區域、指令名稱、錯誤代碼|
|蒙戈請求更新|蒙戈更新請求率|每秒計數|Average|每秒蒙戈更新請求|資料庫名稱、集合名稱、區域、指令名稱、錯誤代碼|
|標準化的RU消費|標準化 RU 消耗|百分比|最大值|每分鐘最大 RU 消耗百分比|集合名稱、資料庫名稱、區域|
|ProvisionedThroughput|佈建的輸送量|Count|最大值|佈建的輸送量|資料庫名稱,集合名稱|
|區域故障移轉|區域故障結束|Count|Count|區域故障結束|None|
|刪除區域|已移除區域|Count|Count|已移除區域|區域|
|ReplicationLatency|P99 複製延遲|毫秒|Average|異地複寫啟用的帳戶其跨來源和目標區域的 P99 複寫延遲|來源區域,目標區域|
|伺服器側延遲|伺服器端延遲|毫秒|Average|伺服器端延遲|資料庫名稱、集合名稱、區域、連接模式、操作類型、公共 API 類型|
|ServiceAvailability|服務可用性|百分比|Average|帳戶請求可用性,時間為一小時、一天或每月粒度|None|
|Sql容器移除|Sql 容器已移除|Count|Count|Sql 容器已移除|資源名稱、子資源名稱、ApiKind、ApiKind 資源類型、操作類型|
|Sql容器輸送量更新|Sql 容器輸送量已更新|Count|Count|Sql 容器輸送量已更新|資源名稱、子資源名稱、ApiKind、Apikind 資源類型、輸送量請求|
|Sql容器更新|Sql 容器已更新|Count|Count|Sql 容器已更新|資源名稱、子資源名稱、ApiKind、Apikind 資源類型、輸送量請求|
|Sql 資料庫移除|Sql 資料庫已移除|Count|Count|Sql 資料庫已移除|資源名稱、ApiKind、ApiKind 資源類型、操作類型|
|Sql 資料庫輸送量更新|Sql 資料庫輸送量已更新|Count|Count|Sql 資料庫輸送量已更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|Sql 資料庫更新|Sql 資料庫已更新|Count|Count|Sql 資料庫已更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|表格刪除|Azure 表已移除|Count|Count|Azure 表已移除|資源名稱、ApiKind、ApiKind 資源類型、操作類型|
|表表輸送量更新|Azure 表吞吐量已更新|Count|Count|Azure 表吞吐量已更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|表格更新|Azure 表表已更新|Count|Count|Azure 表表已更新|資源名稱,ApiKind,ApiKind 資源類型,輸送量請求|
|TotalRequestUnits|要求單位總計|Count|總計|已使用的要求單位|資料庫名稱、集合名稱、區域、狀態代碼、操作類型、狀態|
|TotalRequests|要求總數|Count|Count|進行的要求數目|資料庫名稱、集合名稱、區域、狀態代碼、操作類型、狀態|
|更新帳號金鑰|帳號金鑰已更新|Count|Count|帳號金鑰已更新|KeyType|
|更新帳號網路設定|帳號網路設定已更新|Count|Count|帳號網路設定已更新|None|
|更新帳號複製設定|帳號複製設定已更新|Count|Count|帳號複製設定已更新|None|
|更新診斷設定|帳號診斷設定已更新|Count|Count|帳號診斷設定已更新|診斷設定名稱,資源群組名稱|



## <a name="microsoftenterpriseknowledgegraphservices"></a>微軟.企業知識圖/服務

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|交易計數|交易計數|Count|Count|總交易計數|交易計數|
|SuccessCount|成功計數|Count|Count|成功交易計數|SuccessCount|
|容錯計數|失敗計數|Count|Count|失敗的交易計數|容錯計數|
|成功延遲|成功延遲|毫秒|Average|成功事務的延遲|SuccessCount|

## <a name="microsofteventgriddomains"></a>微軟.事件網格/域

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|Count|總計|發佈至本主題的事件總數|主題|
|PublishFailCount|宣告失敗事件|Count|總計|發佈至本主題失敗的事件總數|主題, 錯誤型態, 錯誤|
|發佈成功延遲在M中|發佈成功延遲|毫秒|總計|以毫秒為單位發佈成功延遲|None|
|MatchedEventCount|相符的事件|Count|總計|與此事件訂閱相符的事件總數|主題、事件訂閱名稱、域事件訂閱名稱|
|DeliveryAttemptFailCount|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|主題、 事件訂閱名稱、 域事件訂閱名稱、 錯誤、 錯誤類型|
|DeliverySuccessCount|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|主題、事件訂閱名稱、域事件訂閱名稱|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|主題、事件訂閱名稱、域事件訂閱名稱|
|DroppedEventCount|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|主題、事件訂閱名稱、域事件訂閱名稱、刪除原因|
|DeadLetteredCount|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|主題, 事件訂閱名稱, 域事件訂閱名稱, 死信原因|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|Count|總計|發佈至本主題的事件總數|None|
|PublishFailCount|宣告失敗事件|Count|總計|發佈至本主題失敗的事件總數|錯誤類型,錯誤|
|UnmatchedEventCount|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|None|
|發佈成功延遲在M中|發佈成功延遲|毫秒|總計|以毫秒為單位發佈成功延遲|None|
|MatchedEventCount|相符的事件|Count|總計|與此事件訂閱相符的事件總數|事件訂閱名稱|
|DeliveryAttemptFailCount|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|錯誤、錯誤類型、事件訂閱名稱|
|DeliverySuccessCount|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|事件訂閱名稱|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|事件訂閱名稱|
|DroppedEventCount|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|移除原因,事件訂閱名稱|
|DeadLetteredCount|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|死信原因,事件訂閱名稱|

## <a name="microsofteventgridsystemtopics"></a>微軟.事件網格/系統主題

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|Count|總計|發佈至本主題的事件總數|None|
|PublishFailCount|宣告失敗事件|Count|總計|發佈至本主題失敗的事件總數|錯誤類型,錯誤|
|UnmatchedEventCount|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|None|
|發佈成功延遲在M中|發佈成功延遲|毫秒|總計|以毫秒為單位發佈成功延遲|None|
|MatchedEventCount|相符的事件|Count|總計|與此事件訂閱相符的事件總數|事件訂閱名稱|
|DeliveryAttemptFailCount|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|錯誤、錯誤類型、事件訂閱名稱|
|DeliverySuccessCount|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|事件訂閱名稱|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|事件訂閱名稱|
|DroppedEventCount|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|移除原因,事件訂閱名稱|
|DeadLetteredCount|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|死信原因,事件訂閱名稱|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|MatchedEventCount|相符的事件|Count|總計|與此事件訂閱相符的事件總數|None|
|DeliveryAttemptFailCount|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|錯誤類型|
|DeliverySuccessCount|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|None|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|None|
|DroppedEventCount|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|丟棄原因|
|DeadLetteredCount|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|Count|總計|發佈至本主題的事件總數|None|
|PublishFailCount|宣告失敗事件|Count|總計|發佈至本主題失敗的事件總數|錯誤類型,錯誤|
|UnmatchedEventCount|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|None|
|發佈成功延遲在M中|發佈成功延遲|毫秒|總計|以毫秒為單位發佈成功延遲|None|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的要求|Count|總計|Microsoft.EventHub 的成功要求。|實體名稱,操作結果|
|ServerErrors|伺服器錯誤。|Count|總計|Microsoft.EventHub 的伺服器錯誤。|實體名稱,操作結果|
|UserErrors|使用者錯誤。|Count|總計|Microsoft.EventHub 的使用者錯誤。|實體名稱,操作結果|
|QuotaExceededErrors|超出配額的錯誤。|Count|總計|Microsoft.EventHub 超出配額的錯誤。|實體名稱,操作結果|
|ThrottledRequests|節流的要求。|Count|總計|Microsoft.EventHub 的節流要求。|實體名稱,操作結果|
|IncomingRequests|傳入的要求|Count|總計|Microsoft.EventHub 的傳入要求。|EntityName|
|IncomingMessages|傳入訊息|Count|總計|Microsoft.EventHub 的傳入訊息。|EntityName|
|OutgoingMessages|外送訊息|Count|總計|Microsoft.EventHub 的傳出訊息。|EntityName|
|IncomingBytes|傳入位元組數。|位元組|總計|Microsoft.EventHub 的傳入位元組數。|EntityName|
|OutgoingBytes|傳出位元組數。|位元組|總計|Microsoft.EventHub 的傳出位元組數。|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Microsoft.EventHub 的使用中連線總數。|None|
|ConnectionsOpened|已開啟的連線。|Count|Average|Microsoft.EventHub 的已開啟連線。|EntityName|
|ConnectionsClosed|已關閉的連線。|Count|Average|Microsoft.EventHub 的已關閉連線。|EntityName|
|CaptureBacklog|擷取待辦項目。|Count|總計|Microsoft.EventHub 的擷取待辦項目。|EntityName|
|CapturedMessages|已擷取的訊息。|Count|總計|Microsoft.EventHub 的已擷取訊息。|EntityName|
|CapturedBytes|已擷取的位元組。|位元組|總計|Microsoft.EventHub 的已擷取位元組。|EntityName|
|大小|大小|位元組|Average|EventHub 的大小 (以位元組為單位)。|EntityName|
|INREQS|傳入要求(已棄用)|Count|總計|命名空間的傳入傳送要求總數(已棄用)|None|
|SUCCREQ|成功要求(已棄用)|Count|總計|命名空間成功要求總數(已棄用)|None|
|FAILREQ|失敗的要求(已棄用)|Count|總計|命名空間的失敗要求總數(已棄用)|None|
|SVRBSY|伺服器忙碌錯誤(已棄用)|Count|總計|命名空間的伺服器行程行程總數(已棄用)|None|
|INTERR|內部伺服器錯誤(已棄用)|Count|總計|命名空間的內部伺服器錯誤總數(已棄用)|None|
|MISCERR|其他錯誤(已棄用)|Count|總計|命名空間的失敗要求總數(已棄用)|None|
|INMSGS|傳入消息(已過時)(已棄用)|Count|總計|命名空間的傳入訊息總數。 此計量已過時。 請改為使用傳入訊息指標(已棄用)|None|
|EHINMSGS|連入訊息數 (已過時)|Count|總計|命名空間的傳入訊息總數(已棄用)|None|
|OUTMSGS|傳出消息(已過時)(已棄用)|Count|總計|命名空間的傳出訊息總數。 此計量已過時。 請改為使用傳出訊息指標(已棄用)|None|
|EHOUTMSGS|傳出訊息數 (已過時)|Count|總計|命名空間的總傳出訊息(已棄用)|None|
|EHINMBS|傳入位元組(已過時)(已棄用)|位元組|總計|命名空間的事件中樞傳入訊息輸送量。 此計量已過時。 請改用傳入位元組指標(已棄用)|None|
|EHINBYTES|傳入位元組數 (已過時)|位元組|總計|命名空間的事件中心傳入訊息輸送量(已棄用)|None|
|EHOUTMBS|傳出位元組(過時)(已棄用)|位元組|總計|命名空間的事件中樞傳出訊息輸送量。 此計量已過時。 請改用傳出位元組指標(已棄用)|None|
|EHOUTBYTES|傳出位元組數 (已過時)|位元組|總計|命名空間的事件中心傳出訊息輸送量(已棄用)|None|
|EHABL|封存送郵件(已棄用)|Count|總計|事件中心存檔名稱空間積壓工作中的消息(已棄用)|None|
|EHAMSGS|已歸檔訊息(已棄用)|Count|總計|事件中心在命名空間中存檔的消息(已棄用)|None|
|EHAMBS|封存訊息輸送量(已棄用)|位元組|總計|事件中心存檔的消息輸送量在命名空間(已棄用)|None|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的要求|Count|總計|Microsoft.EventHub 的成功要求。|OperationResult|
|ServerErrors|伺服器錯誤。|Count|總計|Microsoft.EventHub 的伺服器錯誤。|OperationResult|
|UserErrors|使用者錯誤。|Count|總計|Microsoft.EventHub 的使用者錯誤。|OperationResult|
|QuotaExceededErrors|超出配額的錯誤。|Count|總計|Microsoft.EventHub 超出配額的錯誤。|OperationResult|
|ThrottledRequests|節流的要求。|Count|總計|Microsoft.EventHub 的節流要求。|OperationResult|
|IncomingRequests|傳入的要求|Count|總計|Microsoft.EventHub 的傳入要求。|None|
|IncomingMessages|傳入訊息|Count|總計|Microsoft.EventHub 的傳入訊息。|None|
|OutgoingMessages|外送訊息|Count|總計|Microsoft.EventHub 的傳出訊息。|None|
|IncomingBytes|傳入位元組數。|位元組|總計|Microsoft.EventHub 的傳入位元組數。|None|
|OutgoingBytes|傳出位元組數。|位元組|總計|Microsoft.EventHub 的傳出位元組數。|None|
|ActiveConnections|ActiveConnections|Count|Average|Microsoft.EventHub 的使用中連線總數。|None|
|ConnectionsOpened|已開啟的連線。|Count|Average|Microsoft.EventHub 的已開啟連線。|None|
|ConnectionsClosed|已關閉的連線。|Count|Average|Microsoft.EventHub 的已關閉連線。|None|
|CaptureBacklog|擷取待辦項目。|Count|總計|Microsoft.EventHub 的擷取待辦項目。|None|
|CapturedMessages|已擷取的訊息。|Count|總計|Microsoft.EventHub 的已擷取訊息。|None|
|CapturedBytes|已擷取的位元組。|位元組|總計|Microsoft.EventHub 的已擷取位元組。|None|
|CPU|CPU|百分比|最大值|事件中樞叢集的 CPU 使用率 (百分比)|角色|
|AvailableMemory|可用的記憶體|百分比|最大值|事件中心群集的可用記憶體佔總記憶體的百分比。|角色|
|大小|EventHub 的大小 (以位元組為單位)。|位元組|Average|EventHub 的大小 (以位元組為單位)。|角色|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|GatewayRequests|閘道要求數|Count|總計|閘道器要求數目|HttpStatus|
|CategorizedGatewayRequests|已分類的閘道要求數|Count|總計|依類別 (1xx/2xx/3xx/4xx/5xx) 區分的閘道要求數目|HttpStatus|
|數位活動工作人員|活動工作人員數|Count|最大值|活動工作人員數|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ObservedMetricValue|觀察的計量值|Count|Average|執行時由自動調整規模計算的值|MetricTriggerSource|
|MetricThreshold|計量閾值|Count|Average|當自動調整規模執行時設定的自動調整規模閾值。|MetricTriggerRule|
|ObservedCapacity|觀察的容量|Count|Average|執行時向自動調整規模回報的容量。|None|
|ScaleActionsInitiated|已起始的調整規模動作數|Count|總計|調整規模作業的方向。|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|可用性 結果/可用性百分比|可用性|百分比|Average|成功完成可用性測試的百分比|可用性結果/名稱、可用性結果/位置|
|可用性 結果/計數|可用性集合|Count|Count|可用性測試計數|可用性結果/名稱、可用性結果/位置、可用性結果/成功|
|availabilityResults/duration|可用性測試持續時間|毫秒|Average|可用性測試持續時間|可用性結果/名稱、可用性結果/位置、可用性結果/成功|
|browserTimings/networkDuration|頁面載入網路連線時間|毫秒|Average|介於使用者要求和網路連線之間的時間。 包含 DNS 查閱和傳輸連接。|None|
|browserTimings/processingDuration|用戶端處理時間|毫秒|Average|從接收上個文件位元組直至載入 DOM 的經過時間。 系統可能仍在處理非同步要求。|None|
|browserTimings/receiveDuration|接收回應時間|毫秒|Average|介於第一個位元組和最後一個位元組 (或直到中斷連線) 之間的時間。|None|
|browserTimings/sendDuration|傳送要求時間|毫秒|Average|介於網路連線和接收第一個位元組之間的時間。|None|
|browserTimings/totalDuration|瀏覽器頁面載入時間|毫秒|Average|從使用者要求直至載入 DOM、樣式表、指令碼和影像的經過時間。|None|
|dependencies/count|相依性呼叫|Count|Count|應用程式對外部資源的呼叫計數。|依賴項/類型、依賴項/性能桶、依賴項/成功、依賴項/目標、依賴項/結果代碼、操作/綜合、雲/角色實例、雲/角色名稱|
|dependencies/duration|相依性持續時間|毫秒|Average|應用程式對外部資源的呼叫持續時間。|依賴項/類型、依賴項/性能桶、依賴項/成功、依賴項/目標、依賴項/結果代碼、操作/綜合、雲/角色實例、雲/角色名稱|
|dependencies/failed|相依項呼叫失敗|Count|Count|應用程式對外部資源進行相依性呼叫的失敗計數。|依賴項/類型、依賴項/性能桶、依賴項/成功、依賴項/目標、依賴項/結果代碼、操作/綜合、雲/角色實例、雲/角色名稱|
|pageViews/count|頁面檢視|Count|Count|頁面檢視計數。|操作/合成,雲/角色名稱|
|pageViews/duration|頁面檢視載入時間|毫秒|Average|頁面檢視載入時間|操作/合成,雲/角色名稱|
|performanceCounters/requestExecutionTime|HTTP 要求執行時間|毫秒|Average|最近要求的執行時間。|cloud/roleInstance|
|performanceCounters/requestsInQueue|應用程式佇列中的 HTTP 要求數|Count|Average|應用程式要求佇列的長度。|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP 要求率|每秒計數|Average|每秒從 ASP.NET 發出所有應用程式要求的速率。|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|例外狀況比率|每秒計數|Average|回報給 Windows 的已處理與未處理例外狀況計數，包括 .NET 例外狀況以及轉換成 .NET 例外狀況的非受控例外狀況。|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|處理程序 IO 速率|每秒位元組|Average|每秒讀取與寫入檔案、 網路和裝置的總位元組數。|cloud/roleInstance|
|performanceCounters/processCpuPercentage|處理程序 CPU|百分比|Average|所有處理序執行緒使用處理器執行指令已耗用時間的百分比。 差異可介於 0 到 100 之間。 這個計量只表示 w3wp 處理序的效能。|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|處理器時間|百分比|Average|處理器針對非閒置執行緒所耗費時間的百分比。|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|可用的記憶體|位元組|Average|可立即用於配置給處理序或系統使用的實體記憶體。|cloud/roleInstance|
|performanceCounters/processPrivateBytes|處理程序私人位元組|位元組|Average|以獨佔方式指派至監視應用程式處理程序的記憶體。|cloud/roleInstance|
|requests/duration|伺服器回應時間|毫秒|Average|從接收 HTTP 要求直至完成傳送回應的經過時間。|請求/效能儲存桶、請求/結果代碼、操作/綜合、雲/角色實例、請求/成功、雲/角色名稱|
|requests/count|伺服器要求|Count|Count|已完成 HTTP 要求的計數。|請求/效能儲存桶、請求/結果代碼、操作/綜合、雲/角色實例、請求/成功、雲/角色名稱|
|requests/failed|失敗的要求|Count|Count|標示為失敗的 HTTP 要求計數。 在大多數情況下，這些要求的回應碼會 >= 400，且不等於 401。|請求/效能儲存桶、請求/結果代碼、請求/成功、操作/綜合、雲/角色實例、雲/角色名稱|
|要求/速率|伺服器要求率|每秒計數|Average|每秒伺服器要求的速率|請求/效能儲存桶、請求/結果代碼、操作/綜合、雲/角色實例、請求/成功、雲/角色名稱|
|exceptions/count|例外狀況|Count|Count|所有未攔截的例外狀況合併計數。|雲/角色名稱、雲/角色實例、用戶端/類型|
|exceptions/browser|瀏覽器例外狀況|Count|Count|在瀏覽器中擲回的未攔截例外狀況計數。|用戶端/是伺服器,雲/角色名稱|
|exceptions/server|伺服器例外狀況|Count|Count|伺服器應用程式中擲回但未攔截的例外狀況計數。|用戶端/是伺服器、雲/角色名稱、雲/角色實例|
|traces/count|追蹤|Count|Count|追蹤文件計數|追蹤/嚴重性級別、操作/合成、雲/角色名稱、雲/角色實例|


## <a name="microsoftiotcentraliotapps"></a>微軟.物聯網/物聯網

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|connectedDeviceCount|連接裝置總數|Count|Average|連接到 IoT 中心的裝置數量|None|
|c2d.屬性.閱讀.成功|成功的裝置屬性從 IoT 中心讀取|Count|總計|從 IoT 中央啟動的所有成功屬性讀取的計數|None|
|c2d.屬性.讀取.失敗|從 IoT 內讀取失敗的裝置屬性|Count|總計|從 IoT 中央啟動的所有失敗屬性讀取的計數|None|
|d2c.屬性.閱讀.成功|成功的裝置屬性從裝置讀取|Count|總計|從裝置啟動的所有成功屬性讀取的計數|None|
|d2c.屬性.讀取.失敗|從裝置讀取失敗的裝置屬性|Count|總計|從裝置啟動的所有失敗屬性讀取的計數|None|
|c2d.屬性.更新.成功|從 IoT 中內更新裝置屬性|Count|總計|從 IoT 中央啟動的所有成功屬性更新的計數|None|
|c2d.屬性.更新.失敗|來自 IoT 中心的裝置屬性更新失敗|Count|總計|從 IoT 中央啟動的所有失敗屬性更新的計數|None|
|d2c.屬性.更新.成功|成功從裝置更新裝置屬性|Count|總計|從裝置啟動的所有成功屬性更新的計數|None|
|d2c.屬性.更新.失敗|裝置更新失敗|Count|總計|從裝置啟動的所有失敗屬性更新的計數|None|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ServiceApiHit|服務 API 點擊次數總計|Count|Count|服務 API 點擊次數總數|活動類型,活動名稱|
|ServiceApiLatency|整體服務 API 延遲|毫秒|Average|服務 API 要求的整體延遲|活動類型、活動名稱、狀態代碼、狀態代碼類|
|ServiceApiResult|服務 API 結果總計|Count|Count|服務 API 結果總數|活動類型、活動名稱、狀態代碼、狀態代碼類|
|飽和鞋盒|整體保管庫飽和度|百分比|Average|使用保險庫容量|活動類型、活動名稱、交易記錄類型|
|可用性|總體保險庫可用性|百分比|Average|保存庫要求可用性|活動類型、活動名稱、狀態代碼、狀態代碼類|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|快取利用率|快取利用率|百分比|Average|在叢集範圍的使用率等級|None|
|QueryDuration|查詢持續時間|毫秒|Average|查詢持續時間(以秒為單位)|QueryStatus|
|攝入利用率|攝入利用率|百分比|Average|在叢集中使用的擷取位置的比率|None|
|KeepAlive|保持活力|Count|Average|例行性檢查指出對查詢的叢集回應|None|
|攝入體積|攝入體積(以 MB)|Count|總計|擷取到叢集的資料總量 (以 MB 為單位)|資料庫|
|攝入延遲以秒為單位|攝入延遲(以秒為單位)|秒|Average|從來源 (例如，訊息位於 EventHub 中) 擷取到叢集的擷取時間，以秒為單位|None|
|為事件中心處理的事件|處理的事件(對於事件/IoT 中心)|Count|總計|從事件/IoT 中心引入時群集處理的事件數|EventStatus|
|IngestionResult|攝取結果|Count|Count|擷取作業數|IngestionResultDetails|
|CPU|CPU|百分比|Average|CPU 使用率等級|None|
|連續匯出記錄|連續匯出 = 匯出紀錄數|Count|總計|匯出的記錄數,對於匯出操作期間寫入的每個儲存工件已觸發的記錄數|連續匯出名稱,資料庫|
|匯出利用率|出口利用率|百分比|最大值|出口利用率|None|
|連續匯出掛起計數|連續匯出掛起計數|Count|最大值|待執行的掛起連續匯出作業數|None|
|連續出口最大完成分鐘數|連續匯出最大延遲|Count|最大值|群組連續匯出作業報告的延遲(以分鐘)為|None|
|連續匯出結果|連續匯出結果|Count|Count|指示連續匯出是成功還是失敗|連續匯出名稱、結果、資料庫|
|流式處理持續時間|串流式傳輸引入持續時間|毫秒|Average|以毫秒為單位流式傳輸引入持續時間|None|
|串流式傳輸資料速率|串流式傳輸引入資料速率|Count|Average|串流式傳輸引入資料速率 (MB/秒)|None|
|蒸化要求率|串流式傳輸引入要求速率|Count|速率要求每秒|串流式傳輸引入要求速率(每秒請求數)|None|
|流式處理結果|串流式傳送結果|Count|Average|流式傳輸攝影結果|結果|
|併發查詢總數|併發查詢總數|Count|總計|併發查詢總數|None|
|查詢總數|限制查詢的總數|Count|總計|限制查詢的總數|None|
|已限制的指令總數|限制指令的總數|Count|總計|限制指令的總數|CommandType|
|範圍總數|擴充磁碟區總數|Count|總計|資料範圍總數|None|
|InstanceCount|執行個體計數|Count|Average|實體計數總數|None|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RunsStarted|執行已啟動|Count|總計|已啟動的工作流程執行數目。|None|
|RunsCompleted|完成的執行|Count|總計|已完成的工作流程執行數目。|None|
|RunsSucceeded|已成功的執行|Count|總計|已成功的工作流程執行數目。|None|
|RunsFailed|失敗的執行|Count|總計|已失敗的工作流程執行數目。|None|
|RunsCancelled|取消的執行|Count|總計|已取消的工作流程執行數目。|None|
|RunLatency|執行延遲|秒|Average|已完成的工作流程執行延遲。|None|
|RunSuccessLatency|執行成功的延遲|秒|Average|已成功的工作流程執行延遲。|None|
|RunThrottledEvents|執行節流的事件|Count|總計|工作流程動作或觸發節流的事件數目。|None|
|執行啟動限制事件|執行啟動限制事件|Count|總計|工作流運行數啟動限制事件。|None|
|RunFailurePercentage|執行失敗百分比|百分比|總計|已失敗的工作流程執行百分比。|None|
|ActionsStarted|啟動的動作 |Count|總計|已啟動的工作流程動作數目。|None|
|ActionsCompleted|完成的動作 |Count|總計|已完成的工作流程動作數目。|None|
|ActionsSucceeded|成功的動作 |Count|總計|已成功的工作流程動作數目。|None|
|ActionsFailed|動作的失敗 |Count|總計|已失敗的工作流程動作數目。|None|
|ActionsSkipped|略過的動作 |Count|總計|已略過的工作流程動作數目。|None|
|ActionLatency|動作延遲 |秒|Average|已完成的工作流程動作延遲。|None|
|ActionSuccessLatency|動作成功延遲 |秒|Average|已成功的工作流程動作延遲。|None|
|ActionThrottledEvents|動作節流的事件|Count|總計|工作流程動作已節流的事件數目。|None|
|TriggersStarted|啟動的觸發程序 |Count|總計|已啟動的工作流程觸發程序數目。|None|
|TriggersCompleted|完成的觸發程序 |Count|總計|已完成的工作流程觸發程序數目。|None|
|TriggersSucceeded|成功的觸發程序 |Count|總計|已成功的工作流程觸發程序數目。|None|
|TriggersFailed|失敗的觸發程序 |Count|總計|已失敗的工作流程觸發程序數目。|None|
|TriggersSkipped|略過的觸發程序|Count|總計|已略過的工作流程觸發程序數目。|None|
|TriggersFired|引發的觸發程序 |Count|總計|已引發的工作流程觸發程序數目。|None|
|TriggerLatency|觸發程序延遲 |秒|Average|已完成的工作流程觸發程序延遲。|None|
|TriggerFireLatency|觸發程序引發延遲 |秒|Average|已引發的工作流程觸發程序延遲。|None|
|TriggerSuccessLatency|觸發程序成功延遲 |秒|Average|已成功的工作流程觸發程序延遲。|None|
|TriggerThrottledEvents|觸發程序節流的事件|Count|總計|工作流程觸發程序節流的事件數目。|None|
|BillableActionExecutions|可計費的動作執行|Count|總計|計費的工作流程動作執行數目。|None|
|BillableTriggerExecutions|可計費的觸發程序執行|Count|總計|計費的工作流程觸發程序執行數目。|None|
|TotalBillableExecutions|可計費的總執行次數|Count|總計|計費的工作流程執行數目。|None|
|BillingUsageNativeOperation|為使用原生作業執行計費|Count|總計|要計費的原生作業執行數。|None|
|BillingUsageStandardConnector|為使用標準連接器執行計費|Count|總計|要計費的標準連接器執行數。|None|
|BillingUsageStorageConsumption|為使用儲存體使用量執行計費|Count|總計|要計費的儲存體使用量執行數。|None|

## <a name="microsoftlogicintegrationserviceenvironments"></a>微軟.邏輯/整合服務環境

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RunsStarted|執行已啟動|Count|總計|已啟動的工作流程執行數目。|None|
|RunsCompleted|完成的執行|Count|總計|已完成的工作流程執行數目。|None|
|RunsSucceeded|已成功的執行|Count|總計|已成功的工作流程執行數目。|None|
|RunsFailed|失敗的執行|Count|總計|已失敗的工作流程執行數目。|None|
|RunsCancelled|取消的執行|Count|總計|已取消的工作流程執行數目。|None|
|RunLatency|執行延遲|秒|Average|已完成的工作流程執行延遲。|None|
|RunSuccessLatency|執行成功的延遲|秒|Average|已成功的工作流程執行延遲。|None|
|RunThrottledEvents|執行節流的事件|Count|總計|工作流程動作或觸發節流的事件數目。|None|
|執行啟動限制事件|執行啟動限制事件|Count|總計|工作流運行數啟動限制事件。|None|
|RunFailurePercentage|執行失敗百分比|百分比|總計|已失敗的工作流程執行百分比。|None|
|ActionsStarted|啟動的動作 |Count|總計|已啟動的工作流程動作數目。|None|
|ActionsCompleted|完成的動作 |Count|總計|已完成的工作流程動作數目。|None|
|ActionsSucceeded|成功的動作 |Count|總計|已成功的工作流程動作數目。|None|
|ActionsFailed|動作的失敗 |Count|總計|已失敗的工作流程動作數目。|None|
|ActionsSkipped|略過的動作 |Count|總計|已略過的工作流程動作數目。|None|
|ActionLatency|動作延遲 |秒|Average|已完成的工作流程動作延遲。|None|
|ActionSuccessLatency|動作成功延遲 |秒|Average|已成功的工作流程動作延遲。|None|
|ActionThrottledEvents|動作節流的事件|Count|總計|工作流程動作已節流的事件數目。|None|
|TriggersStarted|啟動的觸發程序 |Count|總計|已啟動的工作流程觸發程序數目。|None|
|TriggersCompleted|完成的觸發程序 |Count|總計|已完成的工作流程觸發程序數目。|None|
|TriggersSucceeded|成功的觸發程序 |Count|總計|已成功的工作流程觸發程序數目。|None|
|TriggersFailed|失敗的觸發程序 |Count|總計|已失敗的工作流程觸發程序數目。|None|
|TriggersSkipped|略過的觸發程序|Count|總計|已略過的工作流程觸發程序數目。|None|
|TriggersFired|引發的觸發程序 |Count|總計|已引發的工作流程觸發程序數目。|None|
|TriggerLatency|觸發程序延遲 |秒|Average|已完成的工作流程觸發程序延遲。|None|
|TriggerFireLatency|觸發程序引發延遲 |秒|Average|已引發的工作流程觸發程序延遲。|None|
|TriggerSuccessLatency|觸發程序成功延遲 |秒|Average|已成功的工作流程觸發程序延遲。|None|
|TriggerThrottledEvents|觸發程序節流的事件|Count|總計|工作流程觸發程序節流的事件數目。|None|
|整合服務環境工作流程處理器使用|整合服務環境的工作流程處理器使用方式|百分比|Average|集成服務環境的工作流處理器使用方式。|None|
|整合服務環境工作串流記憶體使用|整合服務環境的工作流記憶體使用方式|百分比|Average|集成服務環境的工作流記憶體使用方式。|None|
|整合服務環境連線器處理器使用|整合服務環境的連線器處理器使用方式|百分比|Average|集成服務環境的連接器處理器使用方式。|None|
|整合服務環境連接器記憶體使用|整合服務環境的連線器記憶體使用方式|百分比|Average|集成服務環境的連接器記憶體使用方式。|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|已取消的執行|已取消的執行|Count|總計|為此工作區取消的執行數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|取消要求的執行|取消要求的執行|Count|總計|為此工作區要求取消的執行數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|已完成執行|已完成執行|Count|總計|此工作區成功完成的執行數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|失敗的執行|失敗的執行|Count|總計|此工作區失敗的執行次數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|完成執行|完成執行|Count|總計|此工作區進入最終狀態的執行數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|未回應執行|未回應執行|Count|總計|未對此工作區回應的執行數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|未啟動執行|未啟動執行|Count|總計|此工作區處於未啟動狀態的執行次數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|準備執行|準備執行|Count|總計|此工作區準備的執行數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|預先執行|預先執行|Count|總計|此工作區預先定義的執行數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|佇列執行|佇列執行|Count|總計|此工作區佇列的執行數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|已啟動執行|已啟動執行|Count|總計|這個工作區啟動的執行次數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|開始執行|開始執行|Count|總計|這個工作區啟動的執行次數|方案、執行類型、已發布的管道 Id、計算類型、通管步進類型|
|Errors|Errors|Count|總計|此工作區中的執行錯誤數|狀況|
|警告|警告|Count|總計|此工作區中的執行警告數|狀況|
|模型註冊成功|模型註冊成功|Count|總計|在此工作區中成功的模型註冊數|狀況|
|模型註冊失敗|模型註冊失敗|Count|總計|在此工作區中失敗的模型註冊數|方案,狀態代碼|
|模型部署已啟動|模型部署已啟動|Count|總計|在此工作區中啟動的模型部署數|狀況|
|模型部署成功|模型部署成功|Count|總計|在此工作區中成功的模型部署數|狀況|
|模型部署失敗|模型部署失敗|Count|總計|這個工作區中失敗的模型部署數|方案,狀態代碼|
|節點總數|節點總數|Count|Average|總節點數。 此總計包括一些活動節點、空閒節點、不可用節點、預置節點、離開節點|方案,叢集名稱|
|活動節點|活動節點|Count|Average|Acitve 節點數。 這些是主動運行作業的節點。|方案,叢集名稱|
|閒置節點|閒置節點|Count|Average|空閒節點數。 閒置節點是未運行任何作業但可以接受新作業(如果可用)的節點。|方案,叢集名稱|
|無法使用的節點|無法使用的節點|Count|Average|不可用節點數。 由於某些無法解決的問題,無法使用的節點無法正常工作。 Azure將回收這些節點。|方案,叢集名稱|
|搶佔節點|搶佔節點|Count|Average|搶佔節點數。 這些節點是從可用節點池中獲取的低優先順序節點。|方案,叢集名稱|
|離開節點|離開節點|Count|Average|離開節點的數量。 離開節點是剛剛完成作業處理並將轉到空閑狀態的節點。|方案,叢集名稱|
|核心總數|核心總數|Count|Average|總核心數|方案,叢集名稱|
|活動核心|活動核心|Count|Average|活動核心數|方案,叢集名稱|
|閒置核心|閒置核心|Count|Average|閒置核心數|方案,叢集名稱|
|無法使用的核心|無法使用的核心|Count|Average|無法使用核心數|方案,叢集名稱|
|搶佔型核心|搶佔型核心|Count|Average|搶佔型內核數|方案,叢集名稱|
|離開核心|離開核心|Count|Average|離開核心的數量|方案,叢集名稱|
|配額利用率百分比|配額利用率百分比|Count|Average|使用配額百分比|方案,叢集名稱,Vm 家族名稱,Vm 優先|
|Cpu 利用|Cpu 利用|Count|Average|CPU (預覽)|方案,執行 Id,節點 Id,建立時間|
|Gpu 利用|Gpu 利用|Count|Average|GPU(預覽版)|方案,執行 Id,節點 Id,建立時間,裝置 Id|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|使用量|使用量|Count|Count|API 呼叫的計數|Api 類別、ApiName、結果類型、回應代碼|
|可用性|可用性|百分比|Average|API 可用性|Api 類別,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>微軟.媒體/媒體服務/串流媒體終端機

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|輸出|輸出|位元組|總計|出入口數據量(以位元組為單位)。|OutputFormat|
|SuccessE2ELatency|成功端到端延遲|毫秒|Average|成功請求的平均延遲(以毫秒為單位)。|OutputFormat|
|Requests|Requests|Count|總計|對流式處理終結點的請求。|輸出格式,HTTPStatuscode,錯誤代碼|


## <a name="microsoftmediamediaservices"></a>微軟.媒體/媒體服務

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|資產配額|資產配額|Count|Average|當前媒體服務帳戶允許多少資產|None|
|資產計數|資產計數|Count|Average|當前媒體服務帳戶中已創建多少個資產|None|
|資產配額使用百分比|資產配額使用百分比|百分比|Average|當前媒體服務帳戶中的資產使用百分比|None|
|內容關鍵原則配額|內容關鍵原則配額|Count|Average|目前媒體服務帳戶允許多少內容金鑰監管|None|
|內容關鍵原則計數|內容金鑰策略計數|Count|Average|目前媒體服務帳戶中已建立多少個內容金鑰政策|None|
|內容關鍵策略配額使用百分比|內容 關鍵策略配額使用百分比|百分比|Average|目前媒體服務帳戶中的內容金鑰策略使用百分比|None|
|流式處理策略配額|流式處理策略配額|Count|Average|目前媒體服務帳戶允許多少流原則|None|
|流式處理策略計數|串流原則計數|Count|Average|目前媒體服務帳戶中已建立多少流策略|None|
|流式處理策略配額使用百分比|串流策略配額使用百分比|百分比|Average|目前媒體服務帳戶中的流原則使用百分比|None|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>微軟.混合實境/遠端渲染帳戶

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|資產轉換|資產轉換|Count|總計|轉換的資產總數|應用程式Id,資源 Id,SDKversion|
|活動呈現工作階段|活動渲染工作階段|Count|總計|活動呈現工作階段總數|應用程式 Id、資源 Id、工作階段類型、SDKversion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>微軟.NetApp/NetApp帳戶/容量池/卷

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AverageReadLatency|讀取延遲的平均值|毫秒|Average|每個作業讀取延遲的平均值 (毫秒)|None|
|AverageWriteLatency|寫入延遲的平均值|毫秒|Average|每個作業寫入延遲的平均值 (毫秒)|None|
|VolumeLogicalSize|體積消耗大小|位元組|Average|磁碟區的邏輯大小 (已使用的位元組)|None|
|VolumeSnapshotSize|磁碟區快照集大小|位元組|Average|磁碟區所有快照集的大小|None|
|ReadIops|讀取 IOPS|每秒計數|Average|每秒的讀取 I/O 作業|None|
|WriteIops|寫入 IOPS|每秒計數|Average|每秒的寫入 I/O 作業|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|配置給卷大小的池|位元組|Average|集區的已使用配置大小|None|
|VolumePoolTotalLogicalSize|池消耗大小|位元組|Average|屬於集區之所有磁碟區的邏輯大小的總和|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BytesSentRate|傳送的位元組數|位元組|總計|網路介面傳送的位元組數|None|
|BytesReceivedRate|接收的位元組數|位元組|總計|網路介面接收的位元組數|None|
|PacketsSentRate|傳送的封包|Count|總計|網路介面傳送的封包數|None|
|PacketsReceivedRate|接收的封包|Count|總計|網路介面接收的封包數|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|VipAvailability|資料路徑可用性|Count|Average|每個持續時間 Load Balancer 資料路徑可用性的平均值|前置位址,前端埠|
|DipAvailability|健全狀況探查狀態|Count|Average|每個持續時間 Load Balancer 健全狀況探查狀態的平均值|協定類型,後端埠,前端IP位址,前端埠,後端IP位址|
|ByteCount|位元組計數|Count|總計|在期間內傳輸的位元組總數|前置IP位址,前端埠,方向|
|PacketCount|封包計數|Count|總計|在期間內傳輸的封包總數|前置IP位址,前端埠,方向|
|SYNCount|SYN 計數|Count|總計|在期間內傳輸的 SYN 封包總數|前置IP位址,前端埠,方向|
|SnatConnectionCount|SNAT 連線計數|Count|總計|在期間內建立的新 SNAT 連線總數|前端位址,後端IP位址,連接狀態|
|AllocatedSnatPorts|配置的 SNAT 連接埠 (預覽)|Count|總計|在期間內配置的 SNAT 連接埠總數|前端位址,後端IP位址,協定類型,正在等待刪除|
|UsedSnatPorts|使用的 SNAT 連接埠 (預覽)|Count|總計|在期間內使用的 SNAT 連接埠總數|前端位址,後端IP位址,協定類型,正在等待刪除|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueryVolume|查詢磁碟區|Count|總計|為 DNS 區域儲存的查詢數目|None|
|RecordSetCount|記錄集計數|Count|最大值|DNS 區域中的記錄集數目|None|
|RecordSetCapacityUtilization|記錄集容量使用率|百分比|最大值|DNS 區域所使用的記錄集容量百分比|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PacketsInDDoS|傳入封包 DDoS|每秒計數|最大值|傳入封包 DDoS|None|
|PacketsDroppedDDoS|傳入封包捨棄 DDoS|每秒計數|最大值|傳入封包捨棄 DDoS|None|
|PacketsForwardedDDoS|傳入封包轉寄 DDoS|每秒計數|最大值|傳入封包轉寄 DDoS|None|
|TCPPacketsInDDoS|傳入 TCP 封包 DDoS|每秒計數|最大值|傳入 TCP 封包 DDoS|None|
|TCPPacketsDroppedDDoS|傳入 TCP 封包捨棄 DDoS|每秒計數|最大值|傳入 TCP 封包捨棄 DDoS|None|
|TCPPacketsForwardedDDoS|傳入 TCP 封包轉送 DDoS|每秒計數|最大值|傳入 TCP 封包轉送 DDoS|None|
|UDPPacketsInDDoS|傳入 UDP 封包 DDoS|每秒計數|最大值|傳入 UDP 封包 DDoS|None|
|UDPPacketsDroppedDDoS|傳入 UDP 封包捨棄 DDoS|每秒計數|最大值|傳入 UDP 封包捨棄 DDoS|None|
|UDPPacketsForwardedDDoS|傳入 UDP 封包轉送 DDoS|每秒計數|最大值|傳入 UDP 封包轉送 DDoS|None|
|BytesInDDoS|傳入位元組數 DDoS|每秒位元組|最大值|傳入位元組數 DDoS|None|
|BytesDroppedDDoS|傳入位元組數捨棄 DDoS|每秒位元組|最大值|傳入位元組數捨棄 DDoS|None|
|BytesForwardedDDoS|傳入位元組數轉送 DDoS|每秒位元組|最大值|傳入位元組數轉送 DDoS|None|
|TCPBytesInDDoS|傳入 TCP 位元組數 DDoS|每秒位元組|最大值|傳入 TCP 位元組數 DDoS|None|
|TCPBytesDroppedDDoS|傳入 TCP 位元組數捨棄 DDoS|每秒位元組|最大值|傳入 TCP 位元組數捨棄 DDoS|None|
|TCPBytesForwardedDDoS|傳入 TCP 位元組數轉送 DDoS|每秒位元組|最大值|傳入 TCP 位元組數轉送 DDoS|None|
|UDPBytesInDDoS|傳入 UDP 位元組數 DDoS|每秒位元組|最大值|傳入 UDP 位元組數 DDoS|None|
|UDPBytesDroppedDDoS|傳入 UDP 位元組數捨棄 DDoS|每秒位元組|最大值|傳入 UDP 位元組數捨棄 DDoS|None|
|UDPBytesForwardedDDoS|傳入 UDP 位元組數轉送 DDoS|每秒位元組|最大值|傳入 UDP 位元組數轉送 DDoS|None|
|IfUnderDDoSAttack|是否正遭受 DDoS 攻擊|Count|最大值|是否正遭受 DDoS 攻擊|None|
|DDoSTriggerTCPPackets|傳入 TCP 封包數以觸發 DDoS 緩和|每秒計數|最大值|傳入 TCP 封包數以觸發 DDoS 緩和|None|
|DDoSTriggerUDPPackets|傳入 UDP 封包數以觸發 DDoS 緩和|每秒計數|最大值|傳入 UDP 封包數以觸發 DDoS 緩和|None|
|DDoSTriggerSYNPackets|用以觸發 DDoS 風險降低措施的輸入 SYN 封包數|每秒計數|最大值|用以觸發 DDoS 風險降低措施的輸入 SYN 封包數|None|
|VipAvailability|資料路徑可用性|Count|Average|每個持續時間 IP 位址可用性的平均值|連接埠|
|ByteCount|位元組計數|Count|總計|在期間內傳輸的位元組總數|連接埠方向|
|PacketCount|封包計數|Count|總計|在期間內傳輸的封包總數|連接埠方向|
|SynCount|SYN 計數|Count|總計|在期間內傳輸的 SYN 封包總數|連接埠方向|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PingMesh平均往返|Ping 到 VM 的往返時間|毫秒|Average|傳送到目標 VM 的 Ping 的往返時間|來源客戶位址,目標客戶位址|
|PingMeshProbe 失敗百分比|對 VM 的故障 Ping|百分比|Average|容錯 Ping 到目標 VM 的總傳送 Ping 的百分比|來源客戶位址,目標客戶位址|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|應用程式規則Hit|應用程式規則命中計數|Count|總計|已將應用程式規則的次數|狀態、原因、協定|
|網路規則Hit|網路規則命中計數|Count|總計|螢幕規則的次數|狀態、原因、協定|
|防火牆執行狀況|防火牆執行狀況狀態|百分比|Average|防火牆執行狀況狀態|狀態,原因|
|資料處理|已處理的資料量|位元組|總計|防火牆處理的資料總量|None|
|SNATPort 利用|SNAT 連接埠利用率|百分比|Average|SNAT 連接埠利用率|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Throughput|Throughput|每秒位元組|Average|應用程式閘道每秒已服務的位元組數目|None|
|UnhealthyHostCount|狀況不良的主機計數|Count|Average|狀況不良的後端主機數目|BackendSettingsPool|
|HealthyHostCount|狀況良好的主機計數|Count|Average|狀況良好的後端主機數目|BackendSettingsPool|
|TotalRequests|要求總數|Count|總計|應用程式閘道已服務的成功要求計數|BackendSettingsPool|
|平均請求計數Per健康主機|每個健康主機每分鐘的請求|Count|Average|池中每個正常後端主機每分鐘的平均請求計數|BackendSettingsPool|
|FailedRequests|失敗的要求|Count|總計|應用程式閘道已服務的失敗要求計數|BackendSettingsPool|
|ResponseStatus|回應狀態|Count|總計|應用程式閘道傳回的 Http 回應狀態|HttpStatusGroup|
|CurrentConnections|目前的連線數|Count|總計|目前與應用程式閘道建立的連線計數|None|
|新連線數秒|每秒新連線數|每秒計數|Average|使用應用程式閘道建立每秒新連線|None|
|Cpu 利用|CPU 使用率|百分比|Average|應用程式閘道的目前 CPU 使用率|None|
|容量單位|目前容量單位|Count|Average|消耗的容量單位|None|
|固定可計費容量單位|固定計費容量單位|Count|Average|將收取的最低容量單位|None|
|估計已計費容量單位|估計計費容量單位|Count|Average|將收取的估計容量單位|None|
|計算單位|目前計算單位|Count|Average|消耗的計算單位|None|
|後端介面狀態|後端介面狀態|Count|總計|後端成員生成的 HTTP 回應碼數。 這不包括應用程式閘道產生的任何回應代碼。|後端伺服器、後端介面、後端HTTP設定、HTTPStatusGroup|
|特爾斯協定|用戶端 TLS 協定|Count|總計|與應用程式閘道建立連接的用戶端啟動的 TLS 和非 TLS 請求數。 要查看 TLS 協議分佈,按維度 TLS 協定進行篩選。|監聽器,TlsProtocol|
|BytesSent|傳送的位元組數|位元組|總計|應用程式閘道傳送到客戶端的位元組總數|接聽程式|
|BytesReceived|接收的位元組數|位元組|總計|應用程式閘道從客戶端接收的位元總數|接聽程式|
|用戶端Rtt|用戶端 RTT|毫秒|Average|用戶端和應用程式閘道之間的平均往返時間。 此指標指示建立連線與傳回確認所需的時間|接聽程式|
|應用程式閘道總時間|應用程式閘道總時間|毫秒|Average|處理請求及其回應的平均時間。 計算此值的時間從應用程式閘道接收 HTTP 請求的第一個字節到回應發送操作完成的時間的間隔平均值計算。 請務必注意,這通常包括應用程式閘道處理時間、請求和回應數據包透過網路傳輸的時間以及後端伺服器進行回應的時間。|接聽程式|
|後端介面連線時間|後端介面連線時間|毫秒|Average|與後端伺服器建立連線所花費的時間|偵聽器、後端伺服器、後端介面、後端HTTP設定|
|後端第一位元節回應時間|後端第一位元節回應時間|毫秒|Average|建立與後端伺服器的連線開始到接收回應標頭的第一個字節之間的時間間隔,近似後端伺服器的處理時間|偵聽器、後端伺服器、後端介面、後端HTTP設定|
|後端介面響號回應時間|後端介面上一個字節回應時間|毫秒|Average|建立與後端伺服器的連線開始到接收回應正文的最後一個字節之間的時間間隔|偵聽器、後端伺服器、後端介面、後端HTTP設定|
|符合計數|Web 應用程式防火牆 v1 總規則分發|Count|總計|Web 應用程式防火牆 v1 傳入流量的總規則分發|規則群組,規則 Id|
|封鎖計數|Web 應用程式防火牆 v1 封鎖要求規則分發|Count|總計|Web 應用程式防火牆 v1 封鎖的要求規則分發|規則群組,規則 Id|
|已封鎖的 ReqCount|Web 應用程式防火牆 v1 阻止的請求計數|Count|總計|Web 應用程式防火牆 v1 阻止的請求計數|None|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|AverageBandwidth|閘道 S2S 頻寬|每秒位元組|Average|每秒閘道中站對站的平均頻寬 (位元組)|None|
|P2SBandwidth|閘道 S2S 頻寬|每秒位元組|Average|每秒閘道中點對站的平均頻寬 (位元組)|None|
|P2SConnectionCount|P2S 連線計數|Count|最大值|閘道的點對站連線計數|通訊協定|
|TunnelAverageBandwidth|通道頻寬|每秒位元組|Average|通道的平均頻寬 (位元組/秒)|連線名稱,遠端 IP|
|TunnelEgressBytes|通道輸出位元組數|位元組|總計|通道的傳出位元組數|連線名稱,遠端 IP|
|TunnelIngressBytes|通道輸入位元組數|位元組|總計|通道的傳入位元組數|連線名稱,遠端 IP|
|TunnelEgressPackets|通道輸出封包數|Count|總計|通道的傳出封包計數|連線名稱,遠端 IP|
|TunnelIngressPackets|通道輸入封包數|Count|總計|通道的傳入封包計數|連線名稱,遠端 IP|
|TunnelEgressPacketDropTSMismatch|通道輸出 TS 不相符封包捨棄|Count|總計|因與通道不符而從流量選取器捨棄的傳出封包計數|連線名稱,遠端 IP|
|TunnelIngressPacketDropTSMismatch|通道輸入 TS 不符合封包捨棄|Count|總計|因與通道不符而從流量選取器捨棄的傳入封包計數|連線名稱,遠端 IP|


## <a name="microsoftnetworkexpressrouteports"></a>微軟.網路/快速路由連接埠

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RxLight 等級|RxLight 等級|Count|Average|rx 光電平(dBm)|連結, 萊恩|
|TxLight 等級|TxLight 等級|Count|Average|tx 光電平(dBm)|連結, 萊恩|
|管理狀態|管理狀態|Count|Average|連接埠的管理狀態|連結|
|線協定|線協定|Count|Average|連接埠的線路協定狀態|連結|
|波特比辛第二|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|連結|
|連接埠時|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|連結|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|對等式|
|BitsOutPerSecond|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|對等式|
|全域覆寫位秒|全域覆寫位秒|每秒計數|Average|每秒輸入到 Azure 的位元數|對等電路金鑰|
|全域覆寫位出每秒|全域覆寫位出每秒|每秒計數|Average|每秒從 Azure 輸出的位元數|對等電路金鑰|
|Bgp 可用性|Bgp 可用性|百分比|Average|從 MSEE 到所有對等體的 BGP 可用性。|對等類型,對等|
|arp 可用性|Arp 可用性|百分比|Average|從 MSEE 到所有對等體的 ARP 可用性。|對等類型,對等|
|QosDropBitper秒|落點每秒|每秒計數|Average|每秒丟棄的資料入口位|None|
|QosDropBitOutPer秒|已離開的位數每秒|每秒計數|Average|每秒丟棄的資料出口位|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|None|
|BitsOutPerSecond|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|None|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|None|
|BitsOutPerSecond|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|None|

## <a name="microsoftnetworkexpressroutegateways"></a>微軟.網路/快速路由閘道

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ErGateway 連接位位秒|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|連線名稱|
|ErGateway 連線位出每秒|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|連線名稱|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QpsByEndpoint|傳回的依端點查詢數|Count|總計|在指定時間範圍內傳回 Traffic Manager 端點的次數|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|依端點的端點狀態|Count|最大值|若端點探查狀態為「已啟用」則為 1，否則為 0。|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ProbesFailedPercent|失敗的探查百分比|百分比|Average|失敗的連線能力監視探查百分比|None|
|AverageRoundtripMs|平均往返時間(毫秒)|毫秒|Average|在來源與目的地之間所傳送連線能力監視探查的平均網路來回時間 (毫秒)|None|
|檢查失敗百分比|檢查失敗百分比(預覽)|百分比|Average|連線監控檢查失敗的百分比|來源位址、來源名稱、源資源 Id、來源類型、協定、目標位址、目標名稱、目標資源 Id、目標類型、目標埠、測試組名稱、測試設定名稱|
|往返時間|往返時間 (ms) (預覽)|毫秒|Average|連接監控檢查的往返時間(以毫秒為單位)|來源位址、來源名稱、源資源 Id、來源類型、協定、目標位址、目標名稱、目標資源 Id、目標類型、目標埠、測試組名稱、測試設定名稱|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RequestCount|要求計數|Count|總計|HTTP/S Proxy 服務的用戶端要求數|HTTPStatus,HTTPStatusGroup,用戶端區域,用戶端國家|
|RequestSize|要求大小|位元組|總計|從用戶端傳送到 HTTP/S Proxy 的要求的位元組數|HTTPStatus,HTTPStatusGroup,用戶端區域,用戶端國家|
|ResponseSize|回應大小|位元組|總計|從 HTTP/S Proxy 傳送到用戶端的回應的位元組數|HTTPStatus,HTTPStatusGroup,用戶端區域,用戶端國家|
|計費回應大小|計費回應大小|位元組|總計|從 HTTP/S 代理發送到用戶端的回應時發送的計費位元組數(每個請求最少 2KB)。|HTTPStatus,HTTPStatusGroup,用戶端區域,用戶端國家|
|BackendRequestCount|後端要求計數|Count|總計|從 HTTP/S Proxy 傳送至後端的要求數|HTTPStatus,HTTPStatusGroup,後端介面|
|BackendRequestLatency|後端要求延遲|毫秒|Average|從 HTTP/S Proxy 傳送要求到後端一直到 HTTP/S Proxy 從後端接收最後回應位元組之間算出的時間|後端|
|TotalLatency|延遲總計|毫秒|Average|從 HTTP/S Proxy 接收用戶端要求一直到用戶端認可來自 HTTP/S Proxy 的最後回應位元組之間算出的時間|HTTPStatus,HTTPStatusGroup,用戶端區域,用戶端國家|
|BackendHealthPercentage|後端健康情況百分比|百分比|Average|從 HTTP/S Proxy 到後端的健全狀況探查成功百分比|後端池,後端池|
|WebApplicationFirewallRequestCount|Web 應用程式防火牆要求計數|Count|總計|Web 應用程式防火牆處理的用戶端要求數|原則名稱、規則名稱、操作|


## <a name="microsoftnetworkprivatednszones"></a>微軟.網路/私人Dns區

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueryVolume|查詢磁碟區|Count|總計|為專用 DNS 區域服務的查詢數|None|
|RecordSetCount|記錄集計數|Count|最大值|專用 DNS 區域的記錄集數|None|
|RecordSetCapacityUtilization|記錄集容量使用率|百分比|最大值|專用 DNS 區域使用的記錄集容量百分比|None|
|虛擬網路連結計數|虛擬網路連結計數|Count|最大值|連結到專用 DNS 區域的虛擬網路數|None|
|虛擬網路連結容量利用率|虛擬網路連結記憶體容量利用率|百分比|最大值|專用 DNS 區域使用的虛擬網路鏈路容量百分比|None|
|虛擬網路與註冊連結計數|虛擬網路註冊連結計數|Count|最大值|連線到開啟自動註冊的專用 DNS 區域的虛擬網路數|None|
|具有註冊容量的虛擬網路|虛擬網路註冊鏈路容量利用率|百分比|最大值|具有專用 DNS 區域使用自動註冊容量的虛擬網路鏈路百分比|None|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|registration.all|註冊作業數|Count|總計|所有成功註冊作業 (建立、更新、查詢和刪除) 的計數。 |None|
|registration.create|註冊建立作業|Count|總計|所有成功註冊建立的計數。|None|
|registration.update|註冊更新作業|Count|總計|所有成功註冊更新的計數。|None|
|registration.get|註冊讀取作業|Count|總計|所有成功註冊查詢的計數。|None|
|registration.delete|註冊刪除作業|Count|總計|所有成功註冊刪除的計數。|None|
|incoming|傳入訊息|Count|總計|所有成功傳送 API 呼叫的計數。 |None|
|incoming.scheduled|傳送已排程的推播通知|Count|總計|取消已排程的推播通知|None|
|incoming.scheduled.cancel|取消已排程的推播通知|Count|總計|取消已排程的推播通知|None|
|scheduled.pending|暫止已排程的通知|Count|總計|暫止已排程的通知|None|
|installation.all|安裝管理作業|Count|總計|安裝管理作業|None|
|installation.get|取得安裝作業|Count|總計|取得安裝作業|None|
|installation.upsert|建立或更新安裝作業|Count|總計|建立或更新安裝作業|None|
|installation.patch|修補安裝作業|Count|總計|修補安裝作業|None|
|installation.delete|刪除安裝作業|Count|總計|刪除安裝作業|None|
|outgoing.allpns.success|成功通知|Count|總計|所有成功通知的計數。|None|
|outgoing.allpns.invalidpayload|承載錯誤|Count|總計|因為 PNS 傳回錯誤承載錯誤而失敗的推播計數。|None|
|outgoing.allpns.pnserror|外部通知系統錯誤|Count|總計|因為與 PNS 通訊發生問題 (不包括驗證問題) 而失敗的推播計數。|None|
|outgoing.allpns.channelerror|通道錯誤|Count|總計|因為通道無效、未與正確的應用程式相關聯、已節流處理或過期，導致失敗的推播計數。|None|
|outgoing.allpns.badorexpiredchannel|錯誤或過期的通道錯誤|Count|總計|因為註冊中的通道/權杖/registrationId 過期或無效，導致失敗的推播計數。|None|
|outgoing.wns.success|WNS 成功通知|Count|總計|所有成功通知的計數。|None|
|outgoing.wns.invalidcredentials|WNS 授權錯誤 (無效認證)|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。 (Windows Live 無法識別認證)。|None|
|outgoing.wns.badchannel|WNS 不正確的通道錯誤|Count|總計|因為無法識別註冊中的 ChannelURI (WNS 狀態︰404 找不到)，導致失敗的推播計數。|None|
|outgoing.wns.expiredchannel|WNS 過期通道錯誤|Count|總計|因為 ChannelURIi 過期 (WNS 狀態︰ 410 不存在) 而失敗的推播計數。|None|
|outgoing.wns.throttled|WNS 節流通知|Count|總計|因為 WNS 正在節流此應用程式，導致失敗的推播計數 (WNS 狀態︰406 無法接受)。|None|
|outgoing.wns.tokenproviderunreachable|WNS 授權錯誤 (無法連線)|Count|總計|無法連線到 Windows Live。|None|
|outgoing.wns.invalidtoken|WNS 授權錯誤 (無效權杖)|Count|總計|提供給 WNS 的權杖無效 (WNS 狀態︰ 401 未經授權)。|None|
|outgoing.wns.wrongtoken|WNS 授權錯誤 (錯誤權杖)|Count|總計|提供給 WNS 的權杖有效，但卻是給另一個應用程式 (WNS 狀態︰403 禁止)。 如果註冊中的 ChannelURI 與另一個應用程式相關聯，可能會發生這種情形。 請檢查用戶端應用程式相關聯的應用程式，就是認證位於通知中樞的同一個應用程式。|None|
|outgoing.wns.invalidnotificationformat|WNS 無效通知格式|Count|總計|通知的格式無效 (WNS 狀態︰400)。 請注意，WNS 不會拒絕所有無效承載。|None|
|outgoing.wns.invalidnotificationsize|WNS 無效通知大小錯誤|Count|總計|通知承載太大 (WNS 狀態︰413)。|None|
|outgoing.wns.channelthrottled|WNS 通道已節流處理|Count|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (WNS 回應標頭︰X-WNS-NotificationStatus:channelThrottled)。|None|
|outgoing.wns.channeldisconnected|WNS 通道已中斷連線|Count|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (WNS 回應標頭︰X-WNS-DeviceConnectionStatus: disconnected)。|None|
|outgoing.wns.dropped|WNS 已捨棄通知|Count|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (X-WNS-NotificationStatus: dropped 但不是 X-WNS-DeviceConnectionStatus: disconnected)。|None|
|outgoing.wns.pnserror|WNS 錯誤|Count|總計|因為與 WNS 通訊錯誤而未傳遞通知。|None|
|outgoing.wns.authenticationerror|WNS 驗證錯誤|Count|總計|因為與 Windows Live 通訊錯誤、無效認證或不正確的權杖，導致未傳遞通知。|None|
|outgoing.apns.success|APNS 成功通知|Count|總計|所有成功通知的計數。|None|
|outgoing.apns.invalidcredentials|APNS 授權錯誤|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|None|
|outgoing.apns.badchannel|APNS 不正確的通道錯誤|Count|總計|因為權杖無效而失敗的推播計數 (APNS 狀態碼︰8)。|None|
|outgoing.apns.expiredchannel|APNS 過期通道錯誤|Count|總計|由 APNS 意見反應通道宣告失效的權杖計數。|None|
|outgoing.apns.invalidnotificationsize|APNS 無效通知大小錯誤|Count|總計|因為承載太大而失敗的推播計數 (APNS 狀態碼︰7)。|None|
|outgoing.apns.pnserror|APNS 錯誤|Count|總計|因為與 APNS 通訊錯誤而失敗的推播計數。|None|
|outgoing.gcm.success|GCM 成功通知|Count|總計|所有成功通知的計數。|None|
|outgoing.gcm.invalidcredentials|GCM 授權錯誤 (無效認證)|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|None|
|outgoing.gcm.badchannel|GCM 不正確的通道錯誤|Count|總計|因為無法識別註冊中的 registrationId 而失敗的推播計數 (GCM 結果：無效註冊)。|None|
|outgoing.gcm.expiredchannel|GCM 過期通道錯誤|Count|總計|因為註冊中的 registrationId 過期而失敗的推播計數 (GCM 結果：NotRegistered)。|None|
|outgoing.gcm.throttled|GCM 已將通知節流處理|Count|總計|因為 GCM 已將此應用程式節流處理而失敗的推播計數 (GCM 狀態碼︰501-599 或結果：Unavailable)。|None|
|outgoing.gcm.invalidnotificationformat|GCM 無效通知格式|Count|總計|因為承載未正確格式化而失敗的推播計數 (GCM 結果︰InvalidDataKey 或 InvalidTtl)。|None|
|outgoing.gcm.invalidnotificationsize|GCM 無效通知大小錯誤|Count|總計|因為承載太大而失敗的推播計數 (GCM 結果︰MessageTooBig)。|None|
|outgoing.gcm.wrongchannel|GCM 不正確的通道錯誤|Count|總計|因為註冊中的 registrationId 未與目前的應用程式相關聯，導致失敗的推播計數 (GCM 結果：InvalidPackageName)。|None|
|outgoing.gcm.pnserror|GCM 錯誤|Count|總計|因為與 GCM 通訊錯誤而失敗的推播計數。|None|
|outgoing.gcm.authenticationerror|GCM 驗證錯誤|Count|總計|因為 PNS 不接受提供的認證、已封鎖認證，或應用程式中未正確設定 SenderId，導致失敗的推播計數 (GCM 結果︰MismatchedSenderId)。|None|
|outgoing.mpns.success|MPNS 成功通知|Count|總計|所有成功通知的計數。|None|
|outgoing.mpns.invalidcredentials|MPNS 無效認證|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|None|
|outgoing.mpns.badchannel|MPNS 不正確的通道錯誤|Count|總計|因為無法識別註冊中的 ChannelURI 而失敗的推播計數 (MPNS 狀態︰404 找不到)。|None|
|outgoing.mpns.throttled|MPNS 已將通知節流處理|Count|總計|因為 MPNS 正在節流此應用程式，導致失敗的推播計數 (MPNS 狀態︰406 無法接受)。|None|
|outgoing.mpns.invalidnotificationformat|MPNS 無效通知格式|Count|總計|因為通知的承載太大而失敗的推播計數。|None|
|outgoing.mpns.channeldisconnected|MPNS 通道已中斷連線|Count|總計|因為註冊中的 ChannelURI 中斷連線而失敗的推播計數 (MPNS 狀態︰412 找不到)。|None|
|outgoing.mpns.dropped|MPNS 已捨棄通知|Count|總計|MPNS 已捨棄的推播計數 (MPNS 回應標頭︰X-NotificationStatus: QueueFull 或 Suppressed)。|None|
|outgoing.mpns.pnserror|MPNS 錯誤|Count|總計|因為與 MPNS 通訊錯誤而失敗的推播計數。|None|
|outgoing.mpns.authenticationerror|MPNS 驗證錯誤|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|None|
|notificationhub.pushes|所有外寄通知|Count|總計|通知中樞的所有外寄通知|None|
|incoming.all.requests|所有傳入要求|Count|總計|針對通知中樞傳入的要求總數|None|
|incoming.all.failedrequests|所有傳入的失敗要求|Count|總計|針對通知中樞傳入的失敗要求總數|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Count|Average|Average_% Free Inodes|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Free Space|% Free Space|Count|Average|Average_% Free Space|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Used Inodes|% Used Inodes|Count|Average|Average_% Used Inodes|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Used Space|% Used Space|Count|Average|Average_% Used Space|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec |Count|Average|Average_Disk Read Bytes/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Disk Reads/sec|Disk Reads/sec |Count|Average|Average_Disk Reads/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Disk Transfers/sec|Disk Transfers/sec|Count|Average|Average_Disk Transfers/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Count|Average|Average_Disk Write Bytes/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Disk Writes/sec|Disk Writes/sec|Count|Average|Average_Disk Writes/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Free Megabytes|Free Megabytes|Count|Average|Average_Free Megabytes|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Count|Average|Average_Logical Disk Bytes/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Available Memory|% Available Memory|Count|Average|Average_% Available Memory|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Available Swap Space|% Available Swap Space|Count|Average|Average_% Available Swap Space|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Used Memory|% Used Memory|Count|Average|Average_% Used Memory|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Used Swap Space|% Used Swap Space|Count|Average|Average_% Used Swap Space|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Available MBytes Memory|Available MBytes Memory|Count|Average|Average_Available MBytes Memory|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Available MBytes Swap|Available MBytes Swap|Count|Average|Average_Available MBytes Swap|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Page Reads/sec|Page Reads/sec|Count|Average|Average_Page Reads/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Page Writes/sec|Page Writes/sec|Count|Average|Average_Page Writes/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Pages/sec|Pages/sec|Count|Average|Average_Pages/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Count|Average|Average_Used MBytes Swap Space|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Used Memory MBytes|Used Memory MBytes|Count|Average|Average_Used Memory MBytes|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Count|Average|Average_Total Bytes Transmitted|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Total Bytes Received|Total Bytes Received|Count|Average|Average_Total Bytes Received|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Total Bytes|Total Bytes|Count|Average|Average_Total Bytes|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Total Packets Transmitted|Total Packets Transmitted|Count|Average|Average_Total Packets Transmitted|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Total Packets Received|Total Packets Received|Count|Average|Average_Total Packets Received|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Total Rx Errors|Total Rx Errors|Count|Average|Average_Total Rx Errors|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Total Tx Errors|Total Tx Errors|Count|Average|Average_Total Tx Errors|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Total Collisions|Total Collisions|Count|Average|Average_Total Collisions|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Avg. Disk sec/Read|平均磁碟秒/讀取|Count|Average|Average_Avg. Disk sec/Read|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Avg. Disk sec/Transfer|平均磁碟秒/傳輸|Count|Average|Average_Avg. Disk sec/Transfer|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Avg. Disk sec/Write|平均磁碟秒/寫入|Count|Average|Average_Avg. Disk sec/Write|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Count|Average|Average_Physical Disk Bytes/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Pct Privileged Time|Pct Privileged Time|Count|Average|Average_Pct Privileged Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Pct User Time|Pct User Time|Count|Average|Average_Pct User Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Used Memory kBytes|Used Memory kBytes|Count|Average|Average_Used Memory kBytes|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Virtual Shared Memory|Virtual Shared Memory|Count|Average|Average_Virtual Shared Memory|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% DPC Time|% DPC Time|Count|Average|Average_% DPC Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Idle Time|% Idle Time|Count|Average|Average_% Idle Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Interrupt Time|% Interrupt Time|Count|Average|Average_% Interrupt Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% IO Wait Time|% IO Wait Time|Count|Average|Average_% IO Wait Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Nice Time|% Nice Time|Count|Average|Average_% Nice Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Privileged Time|% Privileged Time|Count|Average|Average_% Privileged Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Processor Time|% Processor Time|Count|Average|Average_% Processor Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% User Time|% User Time|Count|Average|Average_% User Time|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Free Physical Memory|Free Physical Memory|Count|Average|Average_Free Physical Memory|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Free Space in Paging Files|Free Space in Paging Files|Count|Average|Average_Free Space in Paging Files|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Free Virtual Memory|Free Virtual Memory|Count|Average|Average_Free Virtual Memory|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Processes|處理序|Count|Average|Average_Processes|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Count|Average|Average_Size Stored In Paging Files|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Uptime|Uptime|Count|Average|Average_Uptime|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Users|使用者|Count|Average|Average_Users|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Current Disk Queue Length|Current Disk Queue Length|Count|Average|Average_Current Disk Queue Length|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Available MBytes|可用的 MB|Count|Average|Average_Available MBytes|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|Count|Average|Average_% Committed Bytes In Use|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Bytes Received/sec|Bytes Received/sec|Count|Average|Average_Bytes Received/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Bytes Sent/sec|Bytes Sent/sec|Count|Average|Average_Bytes Sent/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Bytes Total/sec|Bytes Total/sec|Count|Average|Average_Bytes Total/sec|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Average_Processor Queue Length|處理器佇列長度|Count|Average|Average_Processor Queue Length|電腦, 物件名稱, 實體名稱, 反路徑, 源系統|
|Heartbeat|Heartbeat|Count|總計|Heartbeat|電腦, OSType, 版本, 源電腦 Id|
|更新|更新|Count|Average|更新|電腦, 產品, 類別, 更新狀態, 選擇, 已批准|
|事件|事件|Count|Average|事件|來源、事件紀錄、電腦、事件類別、事件等級、事件等級名稱、事件ID|

## <a name="microsoftpeeringpeeringservices"></a>微軟.對等/對等服務

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|前置延遲|前置延遲|毫秒|Average|中位前置延遲|前置名稱|

## <a name="microsoftpeeringpeerings"></a>微軟.對等互連/對等

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|工作階段可用性V4|工作階段可用性 V4|百分比|Average|V4 工作階段可用性|ConnectionId|
|工作階段可用性V6|工作階段可用性 V6|百分比|Average|V6 工作階段可用性|ConnectionId|
|入口流量率|入口流量速率|位秒秒|Average|入口流量速率(以位/秒)|ConnectionId|
|出口流量率|出口流量率|位秒秒|Average|出口流量速率(以位/秒)|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueryDuration|查詢持續時間|毫秒|Average|最後一個間隔中的 DAX 查詢持續時間|無維度|
|QueryPoolJobQueueLength|執行緒︰查詢集區的作業佇列長度|Count|Average|查詢執行緒集區佇列中的作業數目。|無維度|
|qpu_high_utilization_metric|QPU 高使用率|Count|總計|前一分鐘的 QPU 高使用率，1 代表 QPU 高使用率，否則為 0|無維度|
|memory_metric|記憶體|位元組|Average|記憶體。 範圍：A1 為 0-3 GB、A2 為 0-5 GB、A3 為 0-10 GB、A4 為 0-25 GB、A5 為 0-50 GB、A6 為 0-100 GB|無維度|
|memory_thrashing_metric|記憶體猛移|百分比|Average|記憶體猛移的平均值。|無維度|


## <a name="microsoftprojectbabylonaccounts"></a>微軟.專案巴比倫/帳戶

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|依分類劃分的資產分配|依分類分列的資產分配|Count|總計|指示分配了特定分類的資產數,即它們與該標籤一起分類。|分類,來源,資源Id|
|依儲存類型配置資產|依儲存類型配置資產|Count|總計|指示具有特定存儲類型的資產數。|儲存類型,資源 Id|
|目錄活動使用者|每日活躍使用者|Count|總計|每日活動使用者數|ResourceId|
|目錄使用|依操作配置使用方式|Count|總計|指示使用者對目錄的操作數,即訪問、搜索、術語表。|操作,資源代碼|
|具有分類的資產數量|具有至少一個分類的資產數量|Count|Average|指示具有至少一個標記分類的資產數。|ResourceId|
|掃描取消|掃描已取消|Count|總計|指示已取消的掃描數。|ResourceId|
|掃描完成|掃描已完成|Count|總計|指示成功完成的掃描數。|ResourceId|
|掃描失敗|掃描失敗|Count|總計|指示掃描失敗數。|ResourceId|
|掃描時間|掃描時間|秒|總計|指示總掃描時間(以秒為單位)。|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|總計|Microsoft.Relay 的成功 ListenerConnections。|實體名稱,操作結果|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|總計|Microsoft.Relay 的 ListenerConnections 上發生的 ClientError。|實體名稱,操作結果|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|總計|Microsoft.Relay 的 ListenerConnections 上發生的 ServerError。|實體名稱,操作結果|
|SenderConnections-Success|SenderConnections-Success|Count|總計|Microsoft.Relay 的成功 SenderConnections。|實體名稱,操作結果|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|總計|Microsoft.Relay 的 SenderConnections 上發生的 ClientError。|實體名稱,操作結果|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|總計|Microsoft.Relay 的 SenderConnections 上發生的 ServerError。|實體名稱,操作結果|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|總計|Microsoft.Relay 的 ListenerConnections 總計。|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|總計|Microsoft.Relay 的 SenderConnections 要求總計。|EntityName|
|ActiveConnections|ActiveConnections|Count|總計|Microsoft.Relay 的 ActiveConnections 總計。|EntityName|
|ActiveListeners|ActiveListeners|Count|總計|Microsoft.Relay 的 ActiveListeners 總計。|EntityName|
|BytesTransferred|BytesTransferred|Count|總計|Microsoft.Relay 的 BytesTransferred 總計。|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|總計|Microsoft.Relay 的 ListenerDisconnects 總計。|EntityName|
|SenderDisconnects|SenderDisconnects|Count|總計|Microsoft.Relay 的 SenderDisconnects 總計。|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SearchLatency|搜尋延遲|秒|Average|搜尋服務的平均搜尋延遲|None|
|SearchQueriesPerSecond|每秒搜尋查詢|每秒計數|Average|搜尋服務的每秒搜尋查詢|None|
|ThrottledSearchQueriesPercentage|節流的搜尋查詢百分比|百分比|Average|已針對搜尋查詢進行節流的搜尋服務百分比|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|SuccessfulRequests|成功的要求|Count|總計|命名空間的成功要求總數|實體名稱,操作結果|
|ServerErrors|伺服器錯誤。|Count|總計|Microsoft.ServiceBus 的伺服器錯誤。|實體名稱,操作結果|
|UserErrors|使用者錯誤。|Count|總計|Microsoft.ServiceBus 的使用者錯誤。|實體名稱,操作結果|
|ThrottledRequests|節流的要求。|Count|總計|Microsoft.ServiceBus 的節流要求。|實體名稱,操作結果|
|IncomingRequests|傳入的要求|Count|總計|Microsoft.ServiceBus 的傳入要求。|EntityName|
|IncomingMessages|傳入訊息|Count|總計|Microsoft.ServiceBus 的傳入訊息。|EntityName|
|OutgoingMessages|外送訊息|Count|總計|Microsoft.ServiceBus 的傳出訊息。|EntityName|
|ActiveConnections|ActiveConnections|Count|總計|Microsoft.ServiceBus 的使用中連線總數。|None|
|ConnectionsOpened|已開啟的連線。|Count|Average|Microsoft.ServiceBus 的已開啟連線。|EntityName|
|ConnectionsClosed|已關閉的連線。|Count|Average|Microsoft.ServiceBus 的已關閉連線。|EntityName|
|大小|大小|位元組|Average|佇列/主題的大小 (以位元組為單位)。|EntityName|
|訊息|佇列/主題中的訊息計數。|Count|Average|佇列/主題中的訊息計數。|EntityName|
|ActiveMessages|佇列/主題中的作用中訊息計數。|Count|Average|佇列/主題中的作用中訊息計數。|EntityName|
|死信|佇列/主題中死字母消息的計數。|Count|Average|佇列/主題中死字母消息的計數。|EntityName|
|排程訊息|佇列/主題中計劃的消息計數。|Count|Average|佇列/主題中計劃的消息計數。|EntityName|
|命名空間Cpu使用|CPU|百分比|最大值|服務總線高級命名空間 CPU 使用率指標。|複本|
|命名空間記憶體使用|記憶體使用量|百分比|最大值|服務總線高級命名空間記憶體使用方式指標。|複本|
|CPUXNS|CPU(已棄用)|百分比|最大值|服務總線高級命名空間 CPU 使用率指標。 此指標已取消。 請改用 CPU 指標(命名空間 CpuUse)。|複本|
|WSXNS|記憶體使用(已棄用)|百分比|最大值|服務總線高級命名空間記憶體使用方式指標。 此計量已過時。 請使用記憶體使用方式(命名空間記憶體使用)指標。|複本|


## <a name="microsoftservicefabricmeshapplications"></a>微軟.服務布藝網格/應用程式

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|已配置 Cpu|已配置 Cpu|Count|Average|以毫子模式分配給此容器的 Cpu|應用程式名稱、服務名稱、程式碼包名稱、服務複本名稱|
|已配置記憶體|已配置記憶體|位元組|Average|以 MB 表示分配給此容器的記憶體|應用程式名稱、服務名稱、程式碼包名稱、服務複本名稱|
|實際Cpu|實際Cpu|Count|Average|以毫芯為單位的實際 CPU 使用率|應用程式名稱、服務名稱、程式碼包名稱、服務複本名稱|
|實際記憶體|實際記憶體|位元組|Average|以 MB 表示的實際記憶體使用量|應用程式名稱、服務名稱、程式碼包名稱、服務複本名稱|
|Cpu 利用|Cpu 利用|百分比|Average|此容器 CPU 的利用率(占已配置 Cpu 的百分比)|應用程式名稱、服務名稱、程式碼包名稱、服務複本名稱|
|記憶體利用|記憶體利用|百分比|Average|此容器 CPU 的利用率(占已配置 Cpu 的百分比)|應用程式名稱、服務名稱、程式碼包名稱、服務複本名稱|
|應用程式狀態|應用程式狀態|Count|Average|服務結構網格應用的狀態|應用程式名稱,狀態|
|ServiceStatus|ServiceStatus|Count|Average|服務結構網格應用程式中服務的執行狀況|應用程式名稱、狀態、服務名稱|
|服務複製狀態|服務複製狀態|Count|Average|服務結構網格應用程式中服務複本的執行狀況|應用程式名稱、狀態、服務名稱、服務複本名稱|
|容器狀態|容器狀態|Count|Average|服務結構網格應用程式中容器的狀態|應用程式名稱、服務名稱、程式碼包名稱、服務複本名稱、狀態|
|重新開機計數|重新開機計數|Count|Average|在服務結構網格應用程式中重新啟動容器計數|應用程式名稱、狀態、服務名稱、服務複本名稱、代碼包名稱|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ConnectionCount|連線計數|Count|最大值|使用者連線數量。|端點|
|MessageCount|訊息計數|Count|總計|訊息總量|None|
|InboundTraffic|輸入流量|位元組|總計|服務的輸入流量|None|
|OutboundTraffic|輸出流量|位元組|總計|服務的輸出流量|None|
|UserErrors|使用者錯誤|百分比|最大值|使用者錯誤的百分比|None|
|SystemErrors|系統錯誤|百分比|最大值|系統錯誤的百分比|None|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|None|
|physical_data_read_percent|資料 IO 百分比|百分比|Average|資料 IO 百分比|None|
|log_write_percent|記錄 IO 百分比|百分比|Average|記錄 IO 百分比。 不適用於數據倉庫。|None|
|dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比。 應用於基於 DTU 的資料庫。|None|
|儲存|使用的資料空間|位元組|最大值|使用的數據空間。 不適用於數據倉庫。|None|
|connection_successful|成功的連線|Count|總計|成功的連線|None|
|connection_failed|失敗的連線|Count|總計|失敗的連線|None|
|blocked_by_firewall|遭到防火牆封鎖|Count|總計|遭到防火牆封鎖|None|
|deadlock|死結|Count|總計|僵局。 不適用於數據倉庫。|None|
|storage_percent|使用資料空間百分比|百分比|最大值|數據空間使用百分比。 不適用於數據倉庫或超大規模資料庫。|None|
|xtp_storage_percent|記憶體內部 OLTP 儲存體百分比|百分比|Average|記憶體中 OLTP 儲存百分比。 不適用於數據倉庫。|None|
|workers_percent|背景工作角色百分比|百分比|Average|工人百分比。 不適用於數據倉庫。|None|
|sessions_percent|工作階段百分比|百分比|Average|會話百分比。 不適用於數據倉庫。|None|
|dtu_limit|DTU 限制|Count|Average|DTU 限制。 應用於基於 DTU 的資料庫。|None|
|dtu_used|已使用 DTU|Count|Average|使用了 DTU。 應用於基於 DTU 的資料庫。|None|
|cpu_limit|CPU 限制|Count|Average|CPU 限制。 適用於基於 vCore 的資料庫。|None|
|cpu_used|使用的 CPU|Count|Average|使用的 CPU。 適用於基於 vCore 的資料庫。|None|
|dwu_limit|DWU 限制|Count|最大值|DWU 限制。 僅適用於數據倉庫。|None|
|dwu_consumption_percent|DWU 百分比|百分比|最大值|DWU 百分比。 僅適用於數據倉庫。|None|
|dwu_used|已使用 DWU|Count|最大值|使用了 DWU。 僅適用於數據倉庫。|None|
|cache_hit_percent|快取命中的百分比|百分比|最大值|緩存命中百分比。 僅適用於數據倉庫。|None|
|cache_used_percent|已用快取的百分比|百分比|最大值|緩存使用的百分比。 僅適用於數據倉庫。|None|
|sqlserver_process_core_percent<sup>1</sup> |SQL 伺服器行程核心百分比|百分比|最大值|SQL Server 進程的 CPU 使用率百分比,由作業系統測量。|None|
|sqlserver_process_memory_percent<sup>1</sup> |SQL 伺服器行程記憶體百分比|百分比|最大值|SQL Server 進程的記憶體使用百分比,由作業系統測量。|None|
|tempdb_data_size<sup>2</sup> |Tempdb 資料檔案大小千位元組|Count|最大值|Tempdb 資料檔大小千位元組。|None|
|tempdb_log_size<sup>2</sup> |Tempdb 紀錄檔案大小千位元組|Count|最大值|Tempdb 紀錄檔大小千位元組。|None|
|tempdb_log_used_percent<sup>2</sup> |使用 Tempdb 百分比紀錄|百分比|最大值|使用百分比日誌。|None|
|local_tempdb_usage_percent|本機 tempdb 百分比|百分比|Average|本地溫度百分比。 僅適用於數據倉庫。|None|
|app_cpu_billed|應用程式 CPU 計費|Count|總計|應用 CPU 計費。 適用於無伺服器資料庫。|None|
|app_cpu_percent|套用 CPU 百分比|百分比|Average|應用 CPU 百分比。 適用於無伺服器資料庫。|None|
|app_memory_percent|套用記憶體百分比|百分比|Average|應用記憶體百分比。 適用於無伺服器資料庫。|None|
|allocated_data_storage|已配置的資料空間|位元組|Average|已分配的數據存儲。 不適用於數據倉庫。|None|
|memory_usage_percent|記憶體百分比|百分比|最大值|記憶體百分比。 僅適用於數據倉庫。|None|
|dw_backup_size_gb|資料儲存大小|Count|總計|數據存儲大小由數據的大小和事務日誌組成。 指標計入帳單的"存儲"部分。 僅適用於數據倉庫。|None|
|dw_snapshot_size_gb|快照儲存大小|Count|總計|快照儲存大小是快照捕獲的增量更改的大小,用於創建使用者定義和自動還原點。 指標計入帳單的"存儲"部分。 僅適用於數據倉庫。|None|
|dw_geosnapshot_size_gb|災害復原記憶體|Count|總計|災難恢復存儲大小在帳單中反映為"災難恢復存儲」。。 僅適用於數據倉庫。|None|
|wlg_allocation_relative_to_system_percent|依系統百分比分配工作負載器|百分比|最大值|為每個工作負載組分配的資源相對於整個系統的百分比。 僅適用於數據倉庫。|工作負載組名稱,已使用者定義|
|wlg_allocation_relative_to_wlg_effective_cap_percent|依上限資源百分比分配工作負載組|百分比|最大值|相對於每個工作負載組的指定上限資源的分配的資源百分比。 僅適用於數據倉庫。|工作負載組名稱,已使用者定義|
|wlg_active_queries|工作負載群組活動查詢|Count|總計|工作負荷組中的活動查詢。 僅適用於數據倉庫。|工作負載組名稱,已使用者定義|
|wlg_queued_queries|工作負載組排佇查詢|Count|總計|在工作負荷組中排隊的查詢。 僅適用於數據倉庫。|工作負載組名稱,已使用者定義|
|active_queries|活動查詢|Count|總計|跨所有工作負荷組的活動查詢。 僅適用於數據倉庫。|None|
|queued_queries|佇列查詢|Count|總計|跨所有工作負荷組的排隊查詢。 僅適用於數據倉庫。|None|
|wlg_active_queries_timeouts|工作負載組查詢逾時|Count|總計|已為工作負荷組超時的查詢。 僅適用於數據倉庫。|工作負載組名稱,已使用者定義|
|wlg_effective_min_resource_percent|有效最小資源百分比|百分比|最大值|考慮到服務級別最小值,為工作負載組保留和隔離的資源的最小百分比。 僅適用於數據倉庫。|工作負載組名稱,已使用者定義|
|wlg_effective_cap_resource_percent|有效上限資源百分比|百分比|最大值|考慮到分配給其他工作負荷組的有效最小資源百分比,對工作負載組允許的資源百分比硬限制。 僅適用於數據倉庫。|工作負載組名稱,已使用者定義|
|full_backup_size_bytes|完整備份儲存大小|位元組|最大值|累積完整備份存儲大小。 適用於基於 vCore 的資料庫。 不適用於超大規模資料庫。|None|
|diff_backup_size_bytes|差異備份儲存大小|位元組|最大值|累積差異備份存儲大小。 適用於基於 vCore 的資料庫。 不適用於超大規模資料庫。|None|
|log_backup_size_bytes|紀錄備份儲存大小|位元組|最大值|累積日誌備份存儲大小。 適用於基於 vCore 的超大規模資料庫。|None|
|snapshot_backup_size_bytes|快照備份儲存大小|位元組|最大值|累積快照備份存儲大小。 應用於超大規模資料庫。|None|
|base_blob_size_bytes|基本 Blob 儲存大小|位元組|最大值|基本 blob 儲存大小。 應用於超大規模資料庫。|None|

<sup>1</sup>此指標可用於使用 vCore 購買模型的資料庫,該模型具有 2 個 vCore 和更高版本,或者 200 DTU 和更高版本可用於基於 DTU 的採購模型。 

<sup>2</sup>此指標可用於使用 vCore 購買模型的資料庫,該模型具有 2 個 vCore 和更高版本,或者 200 DTU 和更高版本可用於基於 DTU 的採購模型。 此指標當前不適用於超大規模資料庫或數據倉庫。

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|None|
|database_cpu_percent|CPU 百分比|百分比|Average|CPU 百分比|DatabaseResourceId|
|physical_data_read_percent|資料 IO 百分比|百分比|Average|資料 IO 百分比|None|
|database_physical_data_read_percent|資料 IO 百分比|百分比|Average|資料 IO 百分比|DatabaseResourceId|
|log_write_percent|記錄 IO 百分比|百分比|Average|記錄 IO 百分比|None|
|database_log_write_percent|記錄 IO 百分比|百分比|Average|記錄 IO 百分比|DatabaseResourceId|
|dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比。 適用於基於 DTU 的彈性池。|None|
|database_dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比|DatabaseResourceId|
|storage_percent|使用資料空間百分比|百分比|Average|使用資料空間百分比|None|
|workers_percent|背景工作角色百分比|百分比|Average|背景工作角色百分比|None|
|database_workers_percent|背景工作角色百分比|百分比|Average|背景工作角色百分比|DatabaseResourceId|
|sessions_percent|工作階段百分比|百分比|Average|工作階段百分比|None|
|database_sessions_percent|工作階段百分比|百分比|Average|工作階段百分比|DatabaseResourceId|
|eDTU_limit|eDTU 限制|Count|Average|eDTU 限制。 適用於基於 DTU 的彈性池。|None|
|storage_limit|資料大小上限|位元組|Average|資料大小上限|None|
|eDTU_used|已使用 eDTU|Count|Average|使用了 eDTU。 適用於基於 DTU 的彈性池。|None|
|database_eDTU_used|已使用 eDTU|Count|Average|已使用 eDTU|DatabaseResourceId|
|storage_used|使用的資料空間|位元組|Average|使用的資料空間|None|
|database_storage_used|使用的資料空間|位元組|Average|使用的資料空間|DatabaseResourceId|
|xtp_storage_percent|記憶體內部 OLTP 儲存體百分比|百分比|Average|記憶體內部 OLTP 儲存體百分比|None|
|cpu_limit|CPU 限制|Count|Average|CPU 限制。 適用於基於 vCore 的彈性池。|None|
|database_cpu_limit|CPU 限制|Count|Average|CPU 限制|DatabaseResourceId|
|cpu_used|使用的 CPU|Count|Average|使用的 CPU。 適用於基於 vCore 的彈性池。|None|
|database_cpu_used|使用的 CPU|Count|Average|使用的 CPU|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|SQL 伺服器行程核心百分比|百分比|最大值|SQL Server 進程的 CPU 使用率百分比,由作業系統測量。 適用於彈性池。 |None|
|sqlserver_process_memory_percent<sup>1</sup>|SQL 伺服器行程記憶體百分比|百分比|最大值|SQL Server 進程的記憶體使用百分比,由作業系統測量。 適用於彈性池。 |None|
|tempdb_data_size<sup>2</sup>|Tempdb 資料檔案大小千位元組|Count|最大值|Tempdb 資料檔大小千位元組。|None|
|tempdb_log_size<sup>2</sup>|Tempdb 紀錄檔案大小千位元組|Count|最大值|Tempdb 紀錄檔大小千位元組。 |None|
|tempdb_log_used_percent<sup>2</sup>|使用 Tempdb 百分比紀錄|百分比|最大值|使用百分比日誌。|None|
|allocated_data_storage|已配置的資料空間|位元組|Average|已配置的資料空間|None|
|database_allocated_data_storage|已配置的資料空間|位元組|Average|已配置的資料空間|DatabaseResourceId|
|allocated_data_storage_percent|配置的資料空間百分比|百分比|最大值|配置的資料空間百分比|None|

<sup>1</sup>此指標可用於使用 vCore 購買模型的資料庫,該模型具有 2 個 vCore 和更高版本,或者 200 DTU 和更高版本可用於基於 DTU 的採購模型。 

<sup>2</sup>此指標可用於使用 vCore 購買模型的資料庫,該模型具有 2 個 vCore 和更高版本,或者 200 DTU 和更高版本可用於基於 DTU 的採購模型。 此指標當前不適用於超大規模資料庫。


## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU 百分比|百分比|Average|DTU 百分比|資料庫資源Id,彈性池資源Id|
|storage_used|使用的資料空間|位元組|Average|使用的資料空間|ElasticPoolResourceId|
|database_storage_used|使用的資料空間|位元組|Average|使用的資料空間|資料庫資源Id,彈性池資源Id|
|dtu_used|已使用 DTU|Count|Average|已使用 DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|virtual_core_count|虛擬核心計數|Count|Average|虛擬核心計數|None|
|avg_cpu_percent|CPU 百分比平均|百分比|Average|CPU 百分比平均|None|
|reserved_storage_mb|保留的儲存體空間|Count|Average|保留的儲存體空間|None|
|storage_space_used_mb|使用的儲存體空間|Count|Average|使用的儲存體空間|None|
|io_requests|IO 要求計數|Count|Average|IO 要求計數|None|
|io_bytes_read|讀取的 IO 位元組|位元組|Average|讀取的 IO 位元組|None|
|io_bytes_written|寫入的 IO 位元組|位元組|Average|寫入的 IO 位元組|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|UsedCapacity|已使用容量|位元組|Average|帳戶使用的容量|None|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BlobCapacity|Blob 容量|位元組|Average|儲存體帳戶 Blob 服務所使用的儲存體量 (位元組)。|Blob 類型,層|
|BlobCount|Blob 計數|Count|Average|儲存體帳戶 Blob 服務中的 Blob 數目。|Blob 類型,層|
|ContainerCount|Blob 容器計數|Count|Average|儲存體帳戶 Blob 服務中的容器數目。|None|
|IndexCapacity|索引容量|位元組|Average|ADLS Gen2(分層)索引(以位元組為單位)使用的儲存量。|None|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|TableCapacity|資料表容量|位元組|Average|儲存體帳戶資料表服務所使用的儲存體量 (位元組)。|None|
|TableCount|資料表計數|Count|Average|儲存體帳戶資料表服務中的資料表數目。|None|
|TableEntityCount|資料表實體計數|Count|Average|儲存體帳戶資料表服務中的資料表實體數目。|None|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|FileCapacity|檔案容量|位元組|Average|儲存帳戶的檔案服務(以位元組為單位)使用的儲存量。|FileShare|
|FileCount|檔案計數|Count|Average|儲存帳戶的檔案服務中的檔案數。|FileShare|
|FileShareCount|檔案共用計數|Count|Average|儲存帳戶的檔案服務中的檔共享數。|None|
|檔案分享快照計數|檔案分享快照計數|Count|Average|存儲帳戶檔服務中共用上存在的快照數。|FileShare|
|檔案分享快照大小|檔案分享快照大小|位元組|Average|存儲帳戶的檔案服務中的快照使用的存儲量(以位元組為單位)。|FileShare|
|檔案共用配額|檔案共用配額大小|位元組|Average|Azure 檔案服務(以位元組為單位)可以使用的儲存量的上限。|FileShare|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證、檔案分享|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證,檔案分享|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證,檔案分享|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證,檔案分享|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證,檔案分享|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證,檔案分享|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|QueueCapacity|佇列容量|位元組|Average|儲存體帳戶佇列服務所使用的儲存體量 (位元組)。|None|
|QueueCount|佇列計數|Count|Average|儲存體帳戶佇列服務中的佇列數目。|None|
|QueueMessageCount|佇列訊息計數|Count|Average|儲存體帳戶佇列服務中的佇列訊息大約數目。|None|
|交易|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|回應類型、地理類型、ApiName、身份驗證|
|輸入|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|地理類型,ApiName,認證|
|輸出|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|地理類型,ApiName,認證|
|SuccessServerLatency|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均延遲 (毫秒)。 此值不包括在 AverageE2ELatency 中指定的網路延遲。|地理類型,ApiName,認證|
|SuccessE2ELatency|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|地理類型,ApiName,認證|
|可用性|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|地理類型,ApiName,認證|





## <a name="microsoftstoragecachecaches"></a>微軟.儲存快取/快取

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|用戶端|客戶 IOPS 總數|Count|Average|快取處理的用戶端檔操作的速率。|None|
|用戶端延遲|平均用戶端延遲|毫秒|Average|用戶端檔操作對存儲緩存的平均延遲。|None|
|用戶端閱讀|用戶端讀取 IOPS|每秒計數|Average|用戶端每秒讀取操作。|None|
|用戶端讀取輸送量|平均快取取輸送量|每秒位元組|Average|用戶端讀取數據傳輸速率。|None|
|用戶端寫入|用戶端寫入 IOPS|每秒計數|Average|用戶端每秒寫入操作。|None|
|用戶端寫入輸送量|平均快取寫入輸送量|每秒位元組|Average|用戶端寫入數據傳輸速率。|None|
|用戶端中繼資料讀取|用戶端中繼資料讀取 IOPS|每秒計數|Average|發送到快取的用戶端檔案操作的速率(不包括資料讀取)不修改持久狀態。|None|
|用戶端中繼資料寫入|用戶端中繼資料寫入 IOPS|每秒計數|Average|發送到快取的用戶端檔案操作的速率(不包括資料寫入)來修改持久狀態。|None|
|用戶端鎖定|用戶端鎖定 IOPS|每秒計數|Average|每秒用戶端文件鎖定操作。|None|
|儲存目標執行狀況|儲存目標執行狀況|Count|Average|緩存和存儲目標之間的連接測試結果。|None|
|Uptime|Uptime|Count|Average|緩存和監控系統之間的連接測試結果。|None|
|儲存目標|總存儲目標 IOPS|Count|Average|快取到特定儲存目標的所有檔案操作的速率。|儲存目標|
|儲存目標寫入IOPS|儲存目標寫入 IOPS|Count|Average|快取傳送到特定儲存目標的檔案寫入操作的速率。|儲存目標|
|儲存目標同步寫入輸送量|儲存目標同步寫入輸送量|每秒位元組|Average|緩存非同步將資料寫入特定儲存目標的速率。 這些是機會型寫入,不會導致客戶端阻塞。|儲存目標|
|儲存目標同步寫入輸送量|儲存目標同步寫入輸送量|每秒位元組|Average|快取同步將資料寫入特定儲存目標的速率。 這些是導致用戶端阻止的寫入。|儲存目標|
|儲存目標總寫入輸送量|儲存目標總寫入輸送量|每秒位元組|Average|快取將資料寫入特定儲存目標的總速率。|儲存目標|
|儲存目標延遲|儲存目標延遲|毫秒|Average|快取發送到分段儲存目標的所有檔操作的平均往返延遲。|儲存目標|
|儲存目標中繼資料讀取IOPS|儲存目標中繼資料讀取 IOPS|每秒計數|Average|快取傳送到特定儲存目標的檔操作不修改持久狀態並排除讀取操作的速率。|儲存目標|
|儲存目標中繼資料寫入IOPS|儲存目標中繼資料寫入 IOPS|每秒計數|Average|快取到特定儲存目標的檔案操作的速率,這些操作修改持久狀態並排除寫入操作。|儲存目標|
|儲存目標讀取|儲存目標讀取 IOPS|每秒計數|Average|快取到特定儲存目標的檔案讀取操作的速率。|儲存目標|
|儲存目標讀取前通量|儲存目標提前讀取輸送量|每秒位元組|Average|緩存機會性地從存儲目標讀取數據的速率。|儲存目標|
|儲存目標填充輸送量|儲存目標填充輸送量|每秒位元組|Average|快取從 StorageTarget 讀取數據以處理緩存錯過的速率。|儲存目標|
|儲存目標總讀取輸送量|儲存目標總讀取輸送量|每秒位元組|Average|快取從特定儲存目標讀取數據的總速率。|儲存目標|

## <a name="microsoftstoragesyncstoragesyncservices"></a>微軟儲存同步/儲存同步服務

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|伺服器同步工作階段結果|同步工作階段結果|Count|Average|每次伺服器終結點成功完成與雲端終結點的同步工作階段時記錄值 1 的指標|同步群組名稱、伺服器終結點名稱、同步方向|
|儲存同步作業階段應用檔案計數|檔案已同步|Count|總計|同步的檔案計數|同步群組名稱、伺服器終結點名稱、同步方向|
|儲存同步工作階段Per專案錯誤計數|檔案無法同步|Count|總計|檔案計數未能同步|同步群組名稱、伺服器終結點名稱、同步方向|
|儲存同步批次轉移檔案位元組|同步的位元組|位元組|總計|將同步作業階段傳輸的檔案大小總計|同步群組名稱、伺服器終結點名稱、同步方向|
|儲存同步伺服器偵測訊號|伺服器上線狀態|Count|最大值|每次重新重新列印的伺服器成功記錄與雲端終結點的偵測訊號時記錄值 1 的指標|ServerName|
|儲存同步召回總大小位元組|雲端階層處理重新叫用|位元組|總計|伺服器召回的資料總大小|ServerName|
|儲存同步已呼叫的總網路位元組|雲分層召回大小|位元組|總計|重新叫用的資料大小|同步群組名稱,伺服器名稱|
|儲存同步儲存量位元數數|雲分層召回輸送量|每秒位元組|Average|資料召回輸送量大小|同步群組名稱,伺服器名稱|
|儲存同步呼叫網路位元組應用程式|依應用程式劃分的雲分層召回大小|位元組|總計|應用程式呼叫的資料大小|同步群組名稱、伺服器名稱、應用程式名稱|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>微軟.儲存同步/儲存同步服務/同步組

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|同步群組同步應用檔案計數|檔案已同步|Count|總計|同步的檔案計數|同步群組名稱、伺服器終結點名稱、同步方向|
|同步群組工作階段錯誤計數|檔案無法同步|Count|總計|檔案計數未能同步|同步群組名稱、伺服器終結點名稱、同步方向|
|同步群組轉移檔案位元組|同步的位元組|位元組|總計|將同步作業階段傳輸的檔案大小總計|同步群組名稱、伺服器終結點名稱、同步方向|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>微軟.儲存同步/儲存同步服務/同步組/伺服器終結點

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|伺服器端點同步應用檔案計數|檔案已同步|Count|總計|同步的檔案計數|伺服器終結點名稱,同步方向|
|伺服器端同步作業階段錯誤計數|檔案無法同步|Count|總計|檔案計數未能同步|伺服器終結點名稱,同步方向|
|伺服器終結點批量傳輸檔案位元組|同步的位元組|位元組|總計|將同步作業階段傳輸的檔案大小總計|伺服器終結點名稱,同步方向|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>微軟.儲存同步/儲存同步服務/註冊伺服器

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|伺服器偵測|伺服器上線狀態|Count|最大值|每次重新重新列印的伺服器成功記錄與雲端終結點的偵測訊號時記錄值 1 的指標|伺服器資源 Id,伺服器名稱|
|伺服器復回總大小位元組|雲端階層處理重新叫用|位元組|總計|伺服器召回的資料總大小|伺服器資源 Id,伺服器名稱|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 使用率|百分比|最大值|SU % 使用率|邏輯名稱,分區 Id|
|InputEvents|輸入事件|Count|總計|輸入事件|邏輯名稱,分區 Id|
|InputEventBytes|輸入事件位元組|位元組|總計|輸入事件位元組|邏輯名稱,分區 Id|
|LateInputEvents|延遲輸入事件|Count|總計|延遲輸入事件|邏輯名稱,分區 Id|
|OutputEvents|輸出事件|Count|總計|輸出事件|邏輯名稱,分區 Id|
|ConversionErrors|資料轉換錯誤|Count|總計|資料轉換錯誤|邏輯名稱,分區 Id|
|Errors|執行階段錯誤|Count|總計|執行階段錯誤|邏輯名稱,分區 Id|
|DroppedOrAdjustedEvents|次序錯誤事件|Count|總計|次序錯誤事件|邏輯名稱,分區 Id|
|AMLCalloutRequests|函式要求|Count|總計|函式要求|邏輯名稱,分區 Id|
|AMLCalloutFailedRequests|失敗的函式要求|Count|總計|失敗的函式要求|邏輯名稱,分區 Id|
|AMLCalloutInputEvents|函式事件|Count|總計|函式事件|邏輯名稱,分區 Id|
|DeserializationError|輸入還原序列化錯誤|Count|總計|輸入還原序列化錯誤|邏輯名稱,分區 Id|
|EarlyInputEvents|早期輸入事件|Count|總計|早期輸入事件|邏輯名稱,分區 Id|
|OutputWatermarkDelaySeconds|浮水印延遲秒數|秒|最大值|浮水印延遲秒數|邏輯名稱,分區 Id|
|輸入事件來源回記錄|待處理輸入事件數|Count|最大值|待處理輸入事件數|邏輯名稱,分區 Id|
|輸入事件來源每秒|收到的輸入來源數|Count|總計|收到的輸入來源數|邏輯名稱,分區 Id|

## <a name="microsoftsynapseworkspaces"></a>微軟.Synapse/工作區

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|商務流程導管執行已結束|導管執行結束|Count|總計|成功、失敗或已取消的業務流程管道執行計數|結果,失敗類型,導管|
|商務流程活動執行已結束|活動執行結束|Count|總計|成功、失敗或已取消的商務流程活動計數|結果、失敗類型、活動類型、活動類型、導管|
|商務流程觸發已結束|觸發器已結束|Count|總計|成功、失敗或已取消的業務流程觸發器計數|結果,失敗類型,觸發器|
|SQLOnDemandLogin 嘗試|登入嘗試|Count|總計|成功或失敗的登入嘗試數|結果|
|SQLOnDemand 查詢已結束|查詢已結束|Count|總計|成功、失敗或已取消的查詢計數|結果|
|SQLOnDemand 查詢處理位元組|已處理的資料量|位元組|總計|查詢處理的資料量|None|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>微軟.Synapse/工作區/大數據池

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|火花作業已結束|已結束的應用程式|Count|總計|已結束應用程式計數|作業類型,作業結果|
|核心容量|核心容量|Count|最大值|核心容量|None|
|記憶體容量GB|記憶體容量 (GB)|Count|最大值|記憶體容量 (GB)|None|

## <a name="microsoftsynapseworkspacessqlpools"></a>微軟.Synapse/工作區/sqlPools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|DWU 限制|DWU 限制|Count|最大值|SQL 池的服務等級目標|None|
|DWUused|已使用 DWU|Count|最大值|表示整個 SQL 池使用的高級表示形式。 依 DWU 限制 = DWU 百分比衡量|None|
|DWUused百分比|DWU 使用百分比|百分比|最大值|表示整個 SQL 池使用的高級表示形式。 透過 CPU 百分比與資料 IO 百分比之間取得最大值來測量|None|
|連接被防火牆阻止|被防火牆封鎖的連線|Count|總計|防火牆規則阻止的連接計數。 重新存取 SQL 池的存取控制策略,並在計數較高時監視這些連線|None|
|調適取緩取百分比|自適應快取命中百分比|百分比|最大值|測量工作負載使用自適應緩存的用量程度。 使用此指標與快取命中百分比指標一起確定是為其他容量進行縮放還是重新執行工作負載以滋潤緩存|None|
|調整快取使用百分比|調整快取使用百分比|百分比|最大值|測量工作負載使用自適應緩存的用量程度。 使用此指標與快取使用的百分比指標一起使用,以確定是為其他容量進行縮放,還是重新執行工作負載以滋潤緩存|None|
|本地溫度百分比|本地天分貝使用百分比|百分比|最大值|跨所有計算節點的本地 tempdb 利用率 - 每五分鐘發出一次值|None|
|記憶體使用百分比|記憶體使用百分比|百分比|最大值|SQL 池中所有節點的記憶體利用率|None|
|連接|連接|Count|總計|對 SQL 池的登入總數計數|結果|
|WLG主動查詢|工作負載群組活動查詢|Count|總計|工作負荷組中的活動查詢。 使用此指標未篩選與未分割會顯示系統上執行的所有活動查詢|使用者定義,工作負載組|
|WLG主動查詢超時|工作負載組查詢逾時|Count|總計|已超時的工作負載組的查詢。此指標回報查詢逾時僅在查詢開始執行後進行(不包括由於鎖定或資源等待而導致的等待時間)|使用者定義,工作負載組|
|依系統百分比劃分的 WLG 分配|依系統百分比分配工作負載器|百分比|最大值|相對於整個系統的資源配置的百分比|使用者定義,工作負載組|
|WLG 分配(按最大資源百分比)|依最大資源百分比分配工作負載組|百分比|最大值|顯示資源相對於每個工作負荷組的有效上限資源百分比分配的百分比。 此指標提供工作負載群組的有效利用|使用者定義,工作負載組|
|WLG 有效卡普資源百分比|有效上限資源百分比|百分比|最大值|工作負載組的有效上限資源百分比。 如果存在具有min_percentage_resource> 0 的其他工作負載組,則effective_cap_percentage_resource按比例降低|使用者定義,工作負載組|
|wlg_effective_min_resource_percent|有效最小資源百分比|百分比|最小值|允許考慮服務級別和工作負載組設置的有效最小資源百分比設置。 有效min_percentage_resource可在較低服務等級下調整得更高|使用者定義,工作負載組|
|WLGQueued 查詢|工作負載組排佇查詢|Count|總計|達到最大並發限制後排隊的請求的累積計數|使用者定義,工作負載組|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|IngressReceivedMessages|輸入接收的訊息|Count|總計|從所有事件中樞或 IoT 中樞事件來源讀取的訊息計數|None|
|IngressReceivedInvalidMessages|輸入接收的無效訊息|Count|總計|從所有事件中樞或 IoT 中樞事件來源讀取的無效訊息計數|None|
|IngressReceivedBytes|輸入接收的位元組|位元組|總計|從所有事件來源讀取的位元組計數|None|
|IngressStoredBytes|輸入儲存的位元組|位元組|總計|已成功處理且可供查詢的事件總大小|None|
|IngressStoredEvents|輸入儲存的事件|Count|總計|已成功處理且可供查詢的壓平合併事件計數|None|
|IngressReceivedMessagesTimeLag|輸入接收的訊息時間延遲|秒|最大值|訊息在事件來源中加入佇列的時間與在輸入中處理的時間之間的差異|None|
|IngressReceivedMessagesCountLag|輸入接收的訊息計數延遲|Count|Average|事件來源分割區中最後一個佇列號與在入口中處理的消息的序列號之間的差異|None|
|暖儲存最大值屬性|暖儲存最大屬性|Count|最大值|環境允許 S1/S2 SKU 的最大屬性數和 PAYG SKU 暖儲存允許的最大屬性數|None|
|熱儲存已用屬性|暖儲存使用屬性 |Count|最大值|環境用於 S1/S2 SKU 屬性數和暖儲存用於 PAYG SKU 屬性數|None|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|IngressReceivedMessages|輸入接收的訊息|Count|總計|從事件來源讀取的訊息計數|None|
|IngressReceivedInvalidMessages|輸入接收的無效訊息|Count|總計|從事件來源讀取的無效訊息計數|None|
|IngressReceivedBytes|輸入接收的位元組|位元組|總計|從事件來源讀取的位元組計數|None|
|IngressStoredBytes|輸入儲存的位元組|位元組|總計|已成功處理且可供查詢的事件總大小|None|
|IngressStoredEvents|輸入儲存的事件|Count|總計|已成功處理且可供查詢的壓平合併事件計數|None|
|IngressReceivedMessagesTimeLag|輸入接收的訊息時間延遲|秒|最大值|訊息在事件來源中加入佇列的時間與在輸入中處理的時間之間的差異|None|
|IngressReceivedMessagesCountLag|輸入接收的訊息計數延遲|Count|Average|事件來源分割區中最後一個佇列號與在入口中處理的消息的序列號之間的差異|None|
|暖儲存最大值屬性|暖儲存最大屬性|Count|最大值|環境允許 S1/S2 SKU 的最大屬性數和 PAYG SKU 暖儲存允許的最大屬性數|None|
|熱儲存已用屬性|暖儲存使用屬性 |Count|最大值|環境用於 S1/S2 SKU 屬性數和暖儲存用於 PAYG SKU 屬性數|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>微軟.VMware雲簡單/虛擬機器

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|磁碟讀取位元組秒|Disk Read Bytes/Sec|每秒位元組|Average|示例時間段內讀取操作導致的平均磁碟輸送量。|None|
|磁碟寫入位元陣數|Disk Write Bytes/Sec|每秒位元組|Average|示例期間寫入操作時導致的平均磁碟輸送量。|None|
|Disk Read Bytes|Disk Read Bytes|位元組|總計|示例時間段內讀取操作導致的總磁碟輸送量。|None|
|Disk Write Bytes|Disk Write Bytes|位元組|總計|示例期間寫入操作時導致的總磁碟輸送量。|None|
|磁碟讀取作業|磁碟讀取作業|Count|總計|上一個採樣期間中的 IO 讀取操作數。 請注意,這些操作的大小可能是可變的。|None|
|磁碟寫入作業|磁碟寫入作業|Count|總計|上一個範例期間中的 IO 寫入操作數。 請注意,這些操作的大小可能是可變的。|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|每秒計數|Average|上一個採樣期間的平均 IO 讀取操作數。 請注意,這些操作的大小可能是可變的。|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|每秒計數|Average|上一個採樣期間的平均 IO 寫入操作數。 請注意,這些操作的大小可能是可變的。|None|
|磁碟讀取延遲|磁碟讀取延遲|毫秒|Average|讀取延遲總數。 設備和內核讀取延遲的總和。|None|
|磁碟寫入延遲|磁碟寫入延遲|毫秒|Average|總寫入延遲。 設備和內核寫入延遲的總和。|None|
|網路位元|以位元組/秒為單位的網路|每秒位元組|Average|接收流量的平均網路輸送量。|None|
|網路外位元秒秒|網路出位元組/秒|每秒位元組|Average|傳輸流量的平均網路輸送量。|None|
|Network In|Network In|位元組|總計|接收流量的總網路輸送量。|None|
|Network Out|Network Out|位元組|總計|傳輸流量的總網路輸送量。|None|
|記憶體已使用|使用的記憶體|位元組|Average|VM 正在使用的計算機記憶體量。|None|
|記憶體授予|記憶體已授予|位元組|Average|主機授予 VM 的記憶體量。 在觸控主機一次之前,不會授予記憶體,如果 VMkernel 需要記憶體,則授予的記憶體可能會被交換或膨脹。|None|
|記憶體活動|記憶體活動|位元組|Average|VM 過去小視窗使用的內存量。 這是 VM 當前需要多少記憶體的「真」 數。 其他未使用的記憶體可能會被交換或膨脹,不會影響來賓的性能。|None|
|Percentage CPU|Percentage CPU|百分比|Average|CPU 利用率。 報告此值 100% 表示系統上的所有處理器內核。 例如,使用四核系統 50% 的雙向 VM 完全使用兩個內核。|None|
|百分比Cpu就緒|CPU 就緒百分比|毫秒|總計|準備時間是等待 CPU 在上次更新間隔內可用所花費的時間。|None|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CpuPercentage|CPU 百分比|百分比|Average|CPU 百分比|執行個體|
|MemoryPercentage|記憶體百分比|百分比|Average|記憶體百分比|執行個體|
|DiskQueueLength|磁碟佇列長度|Count|Average|磁碟佇列長度|執行個體|
|HttpQueueLength|Http 佇列長度|Count|Average|Http 佇列長度|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|TcpSynsent|TCP 合成傳送|Count|Average|TCP 合成傳送|執行個體|
|TcpSyn接收|收到的 TCP 合成|Count|Average|收到的 TCP 合成|執行個體|
|Tcp 已建立|TCP 成立|Count|Average|TCP 成立|執行個體|
|TcpFinWait1|TCP 鰭等待 1|Count|Average|TCP 鰭等待 1|執行個體|
|TcpFinWait2|TCP 鰭等待 2|Count|Average|TCP 鰭等待 2|執行個體|
|Tcp 關閉|TCP 關閉|Count|Average|TCP 關閉|執行個體|
|TcpClose 等待|TCP 關閉等待|Count|Average|TCP 關閉等待|執行個體|
|TcpLastack|TCP 最後一個確認|Count|Average|TCP 最後一個確認|執行個體|
|TcpTime等待|TCP 時間等待|Count|Average|TCP 時間等待|執行個體|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (不包括函式) 

> [!NOTE]
> **檔案系統使用方式**是一項新指標,正在全球推出,除非您已列入專用預覽白名單,否則不需要任何數據。

> [!IMPORTANT]
> **將棄用平均回應時間**,以避免與指標聚合混淆。 使用**回應時間**作為替換。

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|秒|總計|CPU 時間|執行個體|
|Requests|Requests|Count|總計|Requests|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http101|Http 101|Count|總計|Http 101|執行個體|
|Http2xx|Http 2xx|Count|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|Count|總計|Http 3xx|執行個體|
|Http401|Http 401|Count|總計|Http 401|執行個體|
|Http403|Http 403|Count|總計|Http 403|執行個體|
|Http404|Http 404|Count|總計|Http 404|執行個體|
|Http406|Http 406|Count|總計|Http 406|執行個體|
|Http4xx|Http 4xx|Count|總計|Http 4xx|執行個體|
|Http5xx|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|MemoryWorkingSet|記憶體工作集|位元組|Average|記憶體工作集|執行個體|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|Average|平均記憶體工作集|執行個體|
|ResponseTime|回應時間|秒|總計|回應時間|執行個體|
|AverageResponseTime|平均回應時間(已棄用)|秒|Average|平均回應時間|執行個體|
|AppConnections|連接|Count|Average|連接|執行個體|
|處理|控制代碼計數|Count|Average|控制代碼計數|執行個體|
|Threads|執行緒計數|Count|Average|執行緒計數|執行個體|
|PrivateBytes|私用位元組|位元組|Average|私用位元組|執行個體|
|IoReadBytesPerSecond|每秒的 IO 讀取位元組數|每秒位元組|總計|每秒的 IO 讀取位元組數|執行個體|
|IoWriteBytesPerSecond|每秒的 IO 寫入位元組數|每秒位元組|總計|每秒的 IO 寫入位元組數|執行個體|
|IoOtherBytesPerSecond|每秒的 IO 其他位元組數|每秒位元組|總計|每秒的 IO 其他位元組數|執行個體|
|IoReadOperationsPerSecond|每秒的 IO 讀取作業數|每秒位元組|總計|每秒的 IO 讀取作業數|執行個體|
|IoWriteOperationsPerSecond|每秒的 IO 寫入作業數|每秒位元組|總計|每秒的 IO 寫入作業數|執行個體|
|IoOtherOperationsPerSecond|每秒的 IO 其他作業數|每秒位元組|總計|每秒的 IO 其他作業數|執行個體|
|RequestsInApplicationQueue|應用程式佇列中的要求數|Count|Average|應用程式佇列中的要求數|執行個體|
|CurrentAssemblies|目前的組件|Count|Average|目前的組件|執行個體|
|TotalAppDomains|應用程式網域總計|Count|Average|應用程式網域總計|執行個體|
|TotalAppDomainsUnloaded|已卸載的應用程式網域總計|Count|Average|已卸載的應用程式網域總計|執行個體|
|Gen0Collections|Gen 0 記憶體回收|Count|總計|Gen 0 記憶體回收|執行個體|
|Gen1Collections|Gen 1 記憶體回收|Count|總計|Gen 1 記憶體回收|執行個體|
|Gen2Collections|Gen 2 記憶體回收|Count|總計|Gen 2 記憶體回收|執行個體|
|執行狀況檢查狀態|執行狀況檢查狀態|Count|Average|執行狀況檢查狀態|執行個體|
|檔案系統使用|檔案系統使用方式|位元組|Average|檔案系統使用方式|None|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (函式)

> [!NOTE]
> **檔案系統使用方式**是一項新指標,正在全球推出,除非您已列入專用預覽白名單,否則不需要任何數據。

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http5xx|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|MemoryWorkingSet|記憶體工作集|位元組|Average|記憶體工作集|執行個體|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|Average|平均記憶體工作集|執行個體|
|FunctionExecutionUnits|函式執行單位|MB/ 毫秒|總計|[函式執行單位](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|執行個體|
|FunctionExecutionCount|函式執行計數|Count|總計|函式執行計數|執行個體|
|PrivateBytes|私用位元組|位元組|Average|私用位元組|執行個體|
|IoReadBytesPerSecond|每秒的 IO 讀取位元組數|每秒位元組|總計|每秒的 IO 讀取位元組數|執行個體|
|IoWriteBytesPerSecond|每秒的 IO 寫入位元組數|每秒位元組|總計|每秒的 IO 寫入位元組數|執行個體|
|IoOtherBytesPerSecond|每秒的 IO 其他位元組數|每秒位元組|總計|每秒的 IO 其他位元組數|執行個體|
|IoReadOperationsPerSecond|每秒的 IO 讀取作業數|每秒位元組|總計|每秒的 IO 讀取作業數|執行個體|
|IoWriteOperationsPerSecond|每秒的 IO 寫入作業數|每秒位元組|總計|每秒的 IO 寫入作業數|執行個體|
|IoOtherOperationsPerSecond|每秒的 IO 其他作業數|每秒位元組|總計|每秒的 IO 其他作業數|執行個體|
|RequestsInApplicationQueue|應用程式佇列中的要求數|Count|Average|應用程式佇列中的要求數|執行個體|
|CurrentAssemblies|目前的組件|Count|Average|目前的組件|執行個體|
|TotalAppDomains|應用程式網域總計|Count|Average|應用程式網域總計|執行個體|
|TotalAppDomainsUnloaded|已卸載的應用程式網域總計|Count|Average|已卸載的應用程式網域總計|執行個體|
|Gen0Collections|Gen 0 記憶體回收|Count|總計|Gen 0 記憶體回收|執行個體|
|Gen1Collections|Gen 1 記憶體回收|Count|總計|Gen 1 記憶體回收|執行個體|
|Gen2Collections|Gen 2 記憶體回收|Count|總計|Gen 2 記憶體回收|執行個體|
|執行狀況檢查狀態|執行狀況檢查狀態|Count|Average|執行狀況檢查狀態|執行個體|
|檔案系統使用|檔案系統使用方式|位元組|Average|檔案系統使用方式|None|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|CpuTime|CPU 時間|秒|總計|CPU 時間|執行個體|
|Requests|Requests|Count|總計|Requests|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http101|Http 101|Count|總計|Http 101|執行個體|
|Http2xx|Http 2xx|Count|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|Count|總計|Http 3xx|執行個體|
|Http401|Http 401|Count|總計|Http 401|執行個體|
|Http403|Http 403|Count|總計|Http 403|執行個體|
|Http404|Http 404|Count|總計|Http 404|執行個體|
|Http406|Http 406|Count|總計|Http 406|執行個體|
|Http4xx|Http 4xx|Count|總計|Http 4xx|執行個體|
|Http5xx|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|MemoryWorkingSet|記憶體工作集|位元組|Average|記憶體工作集|執行個體|
|AverageMemoryWorkingSet|平均記憶體工作集|位元組|Average|平均記憶體工作集|執行個體|
|AverageResponseTime|平均回應時間|秒|Average|平均回應時間|執行個體|
|HTTP回應時間|回應時間|秒|Average|回應時間|執行個體|
|FunctionExecutionUnits|函式執行單位|Count|總計|函式執行單位|執行個體|
|FunctionExecutionCount|函式執行計數|Count|總計|函式執行計數|執行個體|
|AppConnections|連接|Count|Average|連接|執行個體|
|處理|控制代碼計數|Count|Average|控制代碼計數|執行個體|
|Threads|執行緒計數|Count|Average|執行緒計數|執行個體|
|PrivateBytes|私用位元組|位元組|Average|私用位元組|執行個體|
|IoReadBytesPerSecond|每秒的 IO 讀取位元組數|每秒位元組|總計|每秒的 IO 讀取位元組數|執行個體|
|IoWriteBytesPerSecond|每秒的 IO 寫入位元組數|每秒位元組|總計|每秒的 IO 寫入位元組數|執行個體|
|IoOtherBytesPerSecond|每秒的 IO 其他位元組數|每秒位元組|總計|每秒的 IO 其他位元組數|執行個體|
|IoReadOperationsPerSecond|每秒的 IO 讀取作業數|每秒位元組|總計|每秒的 IO 讀取作業數|執行個體|
|IoWriteOperationsPerSecond|每秒的 IO 寫入作業數|每秒位元組|總計|每秒的 IO 寫入作業數|執行個體|
|IoOtherOperationsPerSecond|每秒的 IO 其他作業數|每秒位元組|總計|每秒的 IO 其他作業數|執行個體|
|RequestsInApplicationQueue|應用程式佇列中的要求數|Count|Average|應用程式佇列中的要求數|執行個體|
|CurrentAssemblies|目前的組件|Count|Average|目前的組件|執行個體|
|TotalAppDomains|應用程式網域總計|Count|Average|應用程式網域總計|執行個體|
|TotalAppDomainsUnloaded|已卸載的應用程式網域總計|Count|Average|已卸載的應用程式網域總計|執行個體|
|Gen0Collections|Gen 0 記憶體回收|Count|總計|Gen 0 記憶體回收|執行個體|
|Gen1Collections|Gen 1 記憶體回收|Count|總計|Gen 1 記憶體回收|執行個體|
|Gen2Collections|Gen 2 記憶體回收|Count|總計|Gen 2 記憶體回收|執行個體|
|執行狀況檢查狀態|執行狀況檢查狀態|Count|Average|執行狀況檢查狀態|執行個體|
|檔案系統使用|檔案系統使用方式|位元組|Average|檔案系統使用方式|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|Requests|Requests|Count|總計|Requests|執行個體|
|BytesReceived|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|資料輸出|位元組|總計|資料輸出|執行個體|
|Http101|Http 101|Count|總計|Http 101|執行個體|
|Http2xx|Http 2xx|Count|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|Count|總計|Http 3xx|執行個體|
|Http401|Http 401|Count|總計|Http 401|執行個體|
|Http403|Http 403|Count|總計|Http 403|執行個體|
|Http404|Http 404|Count|總計|Http 404|執行個體|
|Http406|Http 406|Count|總計|Http 406|執行個體|
|Http4xx|Http 4xx|Count|總計|Http 4xx|執行個體|
|Http5xx|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|AverageResponseTime|平均回應時間|秒|Average|平均回應時間|執行個體|
|CpuPercentage|CPU 百分比|百分比|Average|CPU 百分比|執行個體|
|MemoryPercentage|記憶體百分比|百分比|Average|記憶體百分比|執行個體|
|DiskQueueLength|磁碟佇列長度|Count|Average|磁碟佇列長度|執行個體|
|HttpQueueLength|Http 佇列長度|Count|Average|Http 佇列長度|執行個體|
|ActiveRequests|使用中的要求|Count|總計|使用中的要求|執行個體|
|TotalFrontEnds|前端總數|Count|Average|前端總數|None|
|SmallAppServicePlanInstances|小型 App Service 方案背景工作角色|Count|Average|小型 App Service 方案背景工作角色|None|
|MediumAppServicePlanInstances|中型 App Service 方案背景工作角色|Count|Average|中型 App Service 方案背景工作角色|None|
|LargeAppServicePlanInstances|大型 App Service 方案背景工作角色|Count|Average|大型 App Service 方案背景工作角色|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|WorkersTotal|背景工作角色總數|Count|Average|背景工作角色總數|None|
|WorkersAvailable|可用的背景工作角色|Count|Average|可用的背景工作角色|None|
|WorkersUsed|已使用的背景工作角色|Count|Average|已使用的背景工作角色|None|
|CpuPercentage|CPU 百分比|百分比|Average|CPU 百分比|執行個體|
|MemoryPercentage|記憶體百分比|百分比|Average|記憶體百分比|執行個體|
## <a name="next-steps"></a>後續步驟
* [了解 Azure 監視器中的計量](data-platform.md)
* [建立計量警示](alerts-overview.md)
* [將計量匯出至儲存體、事件中樞或 Log Analytics](platform-logs-overview.md)

