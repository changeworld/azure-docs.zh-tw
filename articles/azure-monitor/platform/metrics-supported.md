---
title: Azure 監視器支援的資源類型計量
description: AAzure 監視器每一個資源類型的可用計量清單。
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 81e2abc1b4fd0c540b08f96e6b34c16fca3319f1
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132011"
---
# <a name="supported-metrics-with-azure-monitor"></a>支援 Azure 監視器的計量

> [!NOTE]
> 這份清單主要是從 Azure 監視器計量 REST API 自動產生。 透過 GitHub 對這份清單所做的任何修改都可以不發出警告的方式撰寫。 如需如何進行永久更新的詳細資訊，請洽詢這篇文章的作者。

Azure 監視器提供數種與計量進行互動的方式，包括在入口網站中製作計量圖表、透過 REST API 存取計量，或使用 PowerShell 或 CLI 查詢計量。 

本文是 Azure 監視器的匯總計量管線目前提供的所有平臺（也就是自動收集）度量的完整清單。 清單上次更新日期為2020年3月27日。 在此日期之後變更或新增的計量可能不會出現在下方。 若要以程式設計方式查詢和存取計量清單，請使用[2018-01-01 api 版本](/rest/api/monitor/metricdefinitions)。 不在此清單上的其他計量可能會在入口網站中提供，或使用舊版 Api。

計量是依資源提供者和資源類型來組織。 如需服務及其所屬資源提供者的清單，請參閱[Azure 服務的資源提供者](../../azure-resource-manager/management/azure-services-resource-providers.md)。 

## <a name="exporting-platform-metrics-to-other-locations"></a>將平臺計量匯出至其他位置

您可以透過兩種方式之一，將平台計量從 Azure 監視器管線匯出至其他位置。
1. 使用[計量 REST API](/rest/api/monitor/metrics/list)
2. 使用[診斷設定](diagnostic-settings.md)將平臺計量路由至 
    - Azure 儲存體
    - Azure 監視器記錄（以及 Log Analytics）
    - 事件中樞，這是您將它們放入非 Microsoft 系統的方式 

使用診斷設定是路由計量最簡單的方式，但有一些限制： 

- **某些不可匯出**-所有計量都可以使用 REST API 匯出，但某些度量則無法使用診斷設定匯出，因為 Azure 監視器後端中有複雜的情況。 透過下表中的*診斷設定匯出*的資料行，會列出可透過這種方式匯出的計量。  

- **多維度計量**-目前不支援透過診斷設定傳送多維度計量至其他位置。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。 「例如」**：可以在每個佇列層級瀏覽並繪製事件中樞上的「內送郵件」計量。 不過，當您透過診斷設定匯出時，計量將會呈現為事件中樞內所有佇列的所有內送郵件。

## <a name="guest-os-and-host-os-metrics"></a>來賓 OS 和主機 OS 計量

> [!WARNING]
> 在 Azure 虛擬機器、Service Fabric 和雲端服務中執行的客體作業系統（來賓 OS）計量**不**會列在此處。 您必須透過一或多個代理程式（執行于或作為客體作業系統的一部分）來收集來賓 OS 計量。  「虛擬作業系統」計量包括可追蹤來賓 CPU 百分比或記憶體使用量的效能計數器，這兩者都經常用於自動調整或警示。 
>
> **主機作業系統計量可供使用，如下所列。** 它們並不相同。 主機 OS 計量與裝載您的來賓 OS 會話的 Hyper-v 會話相關。 

> [!TIP]
> 最佳做法是使用並設定[Azure 診斷延伸](diagnostics-extension-overview.md)模組，將來賓 OS 效能計量傳送至儲存平臺計量的相同 Azure 監視器計量資料庫中。 此延伸模組會透過[自訂計量](metrics-custom-overview.md)API 來路由來賓 OS 計量。 然後，您可以圖表、警示，並以其他方式使用來賓 OS 計量，例如平臺計量。 或者，您也可以使用 Log Analytics 代理程式，將客體作業系統計量傳送至 Azure 監視器記錄/Log Analytics。 您可以在其中查詢這些計量與非度量資料的結合。 

如需重要的其他資訊，請參閱[監視代理程式總覽](agents-overview.md)。    

## <a name="table-formatting"></a>資料表格式

> [!IMPORTANT] 
> 這個最新的更新會新增新的資料行，並將計量重新排序為字母。 [新增資訊] 表示下表在底部可能會有水準捲軸，視瀏覽器視窗的寬度而定。 如果您認為遺漏資訊，請使用捲軸來查看整個資料表。


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|是|記憶體：清除工具目前價格|Count|Average|記憶體目前的價格 ($/位元組/時間)，並正規化為 1000。|ServerResourceType|
|CleanerMemoryNonshrinkable|是|記憶體：不可壓縮的清除器記憶體|位元組|Average|背景清除器將不會清除的記憶體數量，以 KB 為單位。|ServerResourceType|
|CleanerMemoryShrinkable|是|記憶體：可壓縮的清除器記憶體|位元組|Average|背景清除器將清除的記憶體數量，以 KB 為單位。|ServerResourceType|
|CommandPoolBusyThreads|是|執行緒：命令集區的忙碌執行緒數|Count|Average|命令執行緒集區中的忙碌執行緒數。|ServerResourceType|
|CommandPoolIdleThreads|是|執行緒：命令集區的閒置執行緒數|Count|Average|命令執行緒集區中的閒置執行緒數。|ServerResourceType|
|CommandPoolJobQueueLength|是|命令集區作業佇列長度|Count|Average|命令執行緒集區佇列中的作業數目。|ServerResourceType|
|CurrentConnections|是|連線：目前的連線數|Count|Average|目前已建立的用戶端連接數目。|ServerResourceType|
|CurrentUserSessions|是|目前的使用者工作階段|Count|Average|目前建立的使用者工作階段數目。|ServerResourceType|
|LongParsingBusyThreads|是|執行緒：完整剖析的忙碌執行緒數|Count|Average|完整剖析執行緒集區中的忙碌執行緒數目。|ServerResourceType|
|LongParsingIdleThreads|是|執行緒：完整剖析的閒置執行緒數|Count|Average|完整剖析執行緒集區中的閒置執行緒數目。|ServerResourceType|
|LongParsingJobQueueLength|是|執行緒：完整剖析的作業佇列長度|Count|Average|完整剖析執行緒集區佇列中的作業數目。|ServerResourceType|
|mashup_engine_memory_metric|是|M 引擎記憶體|位元組|Average|混搭引擎處理序的記憶體使用量|ServerResourceType|
|mashup_engine_private_bytes_metric|是|M 引擎私人位元組|位元組|Average|混搭引擎進程的私用位元組使用量。|ServerResourceType|
|mashup_engine_qpu_metric|是|M 引擎 QPU|Count|Average|混搭引擎處理序的 QPU 使用量|ServerResourceType|
|mashup_engine_virtual_bytes_metric|是|M 引擎虛擬位元組|位元組|Average|混搭引擎進程的虛擬位元組使用量。|ServerResourceType|
|memory_metric|是|記憶體|位元組|Average|記憶體。 範圍 0-25 GB (S1)、0-50 GB (S2) 和 0-100 GB (S4)|ServerResourceType|
|memory_thrashing_metric|是|記憶體猛移|百分比|Average|記憶體猛移的平均值。|ServerResourceType|
|MemoryLimitHard|是|記憶體：固定記憶體限制|位元組|Average|組態檔中的固定記憶體限制。|ServerResourceType|
|MemoryLimitHigh|是|記憶體：記憶體上限|位元組|Average|來自組態檔的記憶體上限。|ServerResourceType|
|MemoryLimitLow|是|記憶體：記憶體下限|位元組|Average|來自組態檔的記憶體下限。|ServerResourceType|
|MemoryLimitVertiPaq|是|記憶體：記憶體限制 VertiPaq|位元組|Average|來自組態檔的記憶體內部限制。|ServerResourceType|
|MemoryUsage|是|記憶體：記憶體使用量|位元組|Average|伺服器處理序用於計算清除器記憶體價格的記憶體使用量。 等於計數器 Process\PrivateBytes 加上記憶體對應的資料大小，忽略 xVelocity 記憶體內部分析引擎 (VertiPaq) 在超出 xVelocity 引擎記憶體限制外對應或配置的任何記憶體。|ServerResourceType|
|private_bytes_metric|是|私用位元組|位元組|Average|私用位元組。|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|是|執行緒：處理集區的忙碌 I/O 作業執行緒數|Count|Average|處理執行緒集區中執行 I/O 作業的執行緒數目。|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|是|執行緒：處理集區的忙碌非 I/O 執行緒數|Count|Average|處理執行緒集區中執行非 I/O 作業的執行緒數目。|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|是|執行緒：處理集區的閒置 I/O 作業執行緒數|Count|Average|處理執行緒集區中 I/O 作業的閒置執行緒數目。|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|是|執行緒：處理集區的閒置非 I/O 執行緒數|Count|Average|處理執行緒集區中專供非 I/O 作業使用的閒置執行緒數目。|ServerResourceType|
|ProcessingPoolIOJobQueueLength|是|執行緒：處理集區 I/O 作業佇列長度|Count|Average|處理執行緒集區佇列中的 I/O 作業數目。|ServerResourceType|
|ProcessingPoolJobQueueLength|是|處理集區作業佇列長度|Count|Average|處理執行緒集區佇列中的非 I/O 作業數目。|ServerResourceType|
|qpu_metric|是|QPU|Count|Average|QPU。 範圍 0-100 (S1)、0-200 (S2) 和 0-400 (S4)|ServerResourceType|
|QueryPoolBusyThreads|是|查詢集區忙碌執行緒|Count|Average|查詢執行緒集區中的忙碌執行緒數目。|ServerResourceType|
|QueryPoolIdleThreads|是|執行緒：查詢集區的閒置執行緒數|Count|Average|處理執行緒集區中 I/O 作業的閒置執行緒數目。|ServerResourceType|
|QueryPoolJobQueueLength|是|執行緒：查詢集區的作業佇列長度|Count|Average|查詢執行緒集區佇列中的作業數目。|ServerResourceType|
|Quota|是|記憶體：Quota|位元組|Average|目前的記憶體配額，以位元組為單位。 記憶體配額也就是指授與使用的記憶體，或是保留的記憶體。|ServerResourceType|
|QuotaBlocked|是|記憶體：封鎖的配額|Count|Average|在釋放其他記憶體配額之前，目前已封鎖的配額要求數目。|ServerResourceType|
|RowsConvertedPerSec|是|處理：每秒轉換的資料列|每秒計數|Average|處理期間資料列轉換的速率。|ServerResourceType|
|RowsReadPerSec|是|處理：每秒讀取的資料列|每秒計數|Average|從所有關聯式資料庫讀取資料列的速率。|ServerResourceType|
|RowsWrittenPerSec|是|處理：每秒寫入的資料列|每秒計數|Average|處理期間資料列寫入的速率。|ServerResourceType|
|ShortParsingBusyThreads|是|執行緒：簡短剖析的忙碌執行緒數|Count|Average|簡短剖析執行緒集區中的忙碌執行緒數目。|ServerResourceType|
|ShortParsingIdleThreads|是|執行緒：簡短剖析的閒置執行緒數|Count|Average|簡短剖析執行緒集區中的閒置執行緒數目。|ServerResourceType|
|ShortParsingJobQueueLength|是|執行緒：簡短剖析的作業佇列長度|Count|Average|簡短剖析執行緒集區佇列中的作業數目。|ServerResourceType|
|SuccessfullConnectionsPerSec|是|每秒連線成功的次數|每秒計數|Average|成功完成連線的速率。|ServerResourceType|
|TotalConnectionFailures|是|連線失敗的總計|Count|Average|連線嘗試失敗的總計。|ServerResourceType|
|TotalConnectionRequests|是|連線要求的總計|Count|Average|連線要求的總計。 這些是達到的。|ServerResourceType|
|VertiPaqNonpaged|是|記憶體：未分頁的 VertiPaq|位元組|Average|工作集中已封鎖來供記憶體內部引擎使用的記憶體位元組。|ServerResourceType|
|VertiPaqPaged|是|記憶體：分頁的 VertiPaq|位元組|Average|可供記憶體內部資料使用的分頁記憶體位元組。|ServerResourceType|
|virtual_bytes_metric|是|虛擬位元組|位元組|Average|虛擬位元組。|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|BackendDuration|是|後端要求的持續時間|毫秒|Average|後端要求的持續時間（以毫秒為單位）|位置，主機名稱|
|Capacity|是|Capacity|百分比|Average|ApiManagement 服務的使用率衡量標準|Location|
|Duration|是|閘道要求的整體持續期間|毫秒|Average|閘道要求的整體持續期間，以毫秒為單位|位置，主機名稱|
|EventHubDroppedEvents|是|已卸除的 EventHub 事件|Count|總計|因為達到佇列大小上限而略過的事件數目|Location|
|EventHubRejectedEvents|是|已拒絕的 EventHub 事件|Count|總計|已拒絕的 EventHub 事件數（錯誤設定或未經授權）|Location|
|EventHubSuccessfulEvents|是|成功的 EventHub 事件|Count|總計|成功的 EventHub 事件數目|Location|
|EventHubThrottledEvents|是|已節流的 EventHub 事件|Count|總計|已節流的 EventHub 事件數目|Location|
|EventHubTimedoutEvents|是|已逾時的 EventHub 事件|Count|總計|已超時的 EventHub 事件數|Location|
|EventHubTotalBytesSent|是|EventHub 事件的大小|位元組|總計|EventHub 事件的總大小（以位元組為單位）|Location|
|EventHubTotalEvents|是|EventHub 事件總數|Count|總計|傳送至 EventHub 的事件數目|Location|
|EventHubTotalFailedEvents|是|失敗的 EventHub 事件|Count|總計|失敗的 EventHub 事件數目|Location|
|FailedRequests|是|失敗的閘道要求 (已淘汰)|Count|總計|閘道要求中的失敗次數-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|位置，主機名稱|
|NetworkConnectivity|是|資源的網路線上狀態（預覽）|計數|Average|來自 API 管理服務之相依資源類型的網路線上狀態|Location、ResourceType|
|OtherRequests|是|其他閘道要求 (已淘汰)|Count|總計|其他閘道要求數目-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|位置，主機名稱|
|Requests|是|Requests|Count|總計|具有多個維度的閘道要求計量|Location、Hostname、LastErrorReason、BackendResponseCode、GatewayResponseCode、BackendResponseCodeCategory、GatewayResponseCodeCategory|
|SuccessfulRequests|是|成功的閘道要求 (已淘汰)|計數|總計|成功的閘道要求數目-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|位置，主機名稱|
|TotalRequests|是|閘道要求總數 (已淘汰)|計數|總計|閘道要求數目-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|位置，主機名稱|
|UnauthorizedRequests|是|未經授權的閘道要求 (已淘汰)|計數|總計|未授權的閘道要求數目-請改用具有 GatewayResponseCodeCategory 維度的多維度要求度量|位置，主機名稱|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|是|HttpIncomingRequestCount|Count|Count|傳入的 HTTP 要求總數。|StatusCode，驗證|
|HttpIncomingRequestDuration|是|HttpIncomingRequestDuration|計數|Average|Http 要求的延遲。|StatusCode，驗證|
|ThrottledHttpRequestCount|是|ThrottledHttpRequestCount|Count|Count|已節流的 HTTP 要求。|無維度|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|jvm. 資料大小|是|jvm. 資料大小|位元組|Average|完整 GC 之後的舊層代記憶體集區大小|部署、AppName、Pod|
|jvm. gc. 資料大小上限|是|jvm. gc. 資料大小上限|位元組|Average|舊版世代記憶體集區的大小上限|部署、AppName、Pod|
|已配置的 jvm。|是|已配置的 jvm。|位元組|最大值|在一個 GC 到下一個之前，會增加年輕世代記憶體集區的大小|部署、AppName、Pod|
|jvm. gc. 記憶體升級。|是|jvm. gc. 記憶體升級。|位元組|最大值|在 GC 到 GC 之後，舊的世代記憶體集區大小正增加的計數|部署、AppName、Pod|
|jvm. gc. pause. count|是|jvm. gc. pause. count|計數|總計|GC 暫停計數|部署、AppName、Pod|
|jvm. gc. pause. total. time|是|jvm. gc. pause. total. time|毫秒|總計|GC 暫停總時間|部署、AppName、Pod|
|jvm。認可的記憶體|是|jvm。認可的記憶體|位元組|Average|指派給 JVM 的記憶體（位元組）|部署、AppName、Pod|
|jvm. 記憶體。最大值|是|jvm. 記憶體。最大值|位元組|最大值|可用於記憶體管理的最大記憶體數量（以位元組為單位）|部署、AppName、Pod|
|jvm. 記憶體。使用|是|jvm. 記憶體。使用|位元組|Average|使用的應用程式記憶體（位元組）|部署、AppName、Pod|
|處理常式。 cpu 使用量|是|處理常式。 cpu 使用量|百分比|Average|應用程式 JVM CPU 使用量百分比|部署、AppName、Pod|
|system.object。使用方式|是|system.object。使用方式|百分比|Average|整個系統的最近 cpu 使用量|部署、AppName、Pod|
|tomcat. global 錯誤|是|tomcat. global 錯誤|Count|總計|Tomcat 全域錯誤|部署、AppName、Pod|
|tomcat. global. received|是|tomcat. global. received|位元組|總計|Tomcat 接收到的位元組總數|部署、AppName、Pod|
|tomcat. request. avg. time|是|tomcat. request. avg. time|毫秒|Average|Tomcat 要求平均時間|部署、AppName、Pod|
|tomcat. global. 要求數上限|是|tomcat. global. 要求數上限|毫秒|最大值|Tomcat 要求時間上限|部署、AppName、Pod|
|tomcat. total. count|是|tomcat. total. count|Count|總計|Tomcat 要求總計數|部署、AppName、Pod|
|tomcat. global. request. total. time|是|tomcat. global. request. total. time|毫秒|總計|Tomcat 要求總時間|部署、AppName、Pod|
|tomcat. global. 已傳送|是|tomcat. global. 已傳送|位元組|總計|Tomcat 已傳送的位元組總數|部署、AppName、Pod|
|tomcat。目前的|是|tomcat。目前的|Count|總計|Tomcat 會話作用中計數|部署、AppName、Pod|
|tomcat。作用中。最大值|是|tomcat。作用中。最大值|Count|總計|Tomcat 工作階段最大作用中計數|部署、AppName、Pod|
|tomcat. keep-alive. max|是|tomcat. keep-alive. max|毫秒|最大值|Tomcat 工作階段最大有效時間|部署、AppName、Pod|
|tomcat. 會話. 已建立|是|tomcat. 會話. 已建立|Count|總計|Tomcat 已建立的工作階段計數|部署、AppName、Pod|
|tomcat. 會話已過期|是|tomcat. 會話已過期|Count|總計|Tomcat 已過期的工作階段計數|部署、AppName、Pod|
|tomcat。已拒絕|是|tomcat。已拒絕|Count|總計|Tomcat 已拒絕的工作階段計數|部署、AppName、Pod|
|tomcat.threads.config。最大值|是|tomcat.threads.config。最大值|Count|總計|Tomcat 設定最大執行緒計數|部署、AppName、Pod|
|tomcat。目前的|是|tomcat。目前的|Count|總計|Tomcat 目前線程計數|部署、AppName、Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|TotalJob|是|工作總數|Count|總計|工作總數|Runbook、狀態|
|TotalUpdateDeploymentMachineRuns|是|更新部署機器執行總計|Count|總計|在軟體更新部署執行中執行的軟體更新部署電腦總數|SoftwareUpdateConfigurationName、Status、TargetComputer、SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|是|更新部署執行總計|Count|總計|軟體更新部署執行總計|SoftwareUpdateConfigurationName，狀態|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|CoreCount|否|專用核心計數|Count|總計|Batch 帳戶中的專用核心總數|無維度|
|CreatingNodeCount|否|建立節點計數|Count|總計|正在建立的節點數目|無維度|
|IdleNodeCount|否|閒置的節點計數|Count|總計|閒置節點的數目|無維度|
|JobDeleteCompleteEvent|是|作業刪除完成事件|Count|總計|已成功刪除的作業總數。|jobId|
|JobDeleteStartEvent|是|作業刪除啟動事件|Count|總計|已要求要刪除的作業總數。|jobId|
|JobDisableCompleteEvent|是|作業停用完成事件|Count|總計|已成功停用的作業總數。|jobId|
|JobDisableStartEvent|是|作業停用啟動事件|Count|總計|已要求要停用的作業總數。|jobId|
|JobStartEvent|是|作業啟動事件|Count|總計|已成功啟動的作業總數。|jobId|
|JobTerminateCompleteEvent|是|作業終止完成事件|Count|總計|已成功終止的作業總數。|jobId|
|JobTerminateCompleteEvent|是|作業終止啟動事件|Count|總計|已要求要終止的作業總數。|jobId|
|LeavingPoolNodeCount|否|離開集區節點計數|Count|總計|離開集區的節點數目|無維度|
|LowPriorityCoreCount|否|LowPriority 核心計數|Count|總計|Batch 帳戶中的低優先順序核心總數|無維度|
|OfflineNodeCount|否|離線節點計數|Count|總計|離線節點的數目|無維度|
|PoolCreateEvent|是|集區建立事件|Count|總計|已建立集區的總數|poolId|
|PoolDeleteCompleteEvent|是|集區刪除完成事件|Count|總計|已完成的集區刪除總次數|poolId|
|PoolDeleteStartEvent|是|集區刪除啟動事件|Count|總計|已啟動的集區刪除總次數|poolId|
|PoolResizeCompleteEvent|是|調整集區大小完成事件|Count|總計|已完成的調整集區大小總次數|poolId|
|PoolResizeStartEvent|是|調整集區大小啟動事件|Count|總計|已啟動的調整集區大小總次數|poolId|
|PreemptedNodeCount|否|先占節點計數|Count|總計|先占節點的數量|無維度|
|RebootingNodeCount|否|重新啟動節點計數|Count|總計|重新啟動節點的數目|無維度|
|RebootingNodeCount|否|重新安裝映像節點計數|Count|總計|重新安裝映像的節點數目|無維度|
|RunningNodeCount|否|執行節點計數|Count|總計|執行節點的數目|無維度|
|StartingNodeCount|否|啟動節點計數|Count|總計|啟動的節點數目|無維度|
|StartTaskFailedNodeCount|否|啟動工作失敗的節點計數|Count|總計|啟動工作失敗的節點數目|無維度|
|TaskCompleteEvent|是|工作完成事件|Count|總計|已完成的工作總次數|poolId，jobId|
|TaskFailEvent|是|工作失敗事件|Count|總計|失敗狀態中已完成的工作總數|poolId，jobId|
|TaskStartEvent|是|工作啟動的事件|Count|總計|已啟動的工作總次數|poolId，jobId|
|TotalLowPriorityNodeCount|否|低優先順序節點計數|Count|總計|Batch 帳戶中的低優先順序節點總數|無維度|
|TotalNodeCount|否|專用節點計數|Count|總計|Batch 帳戶中的專用節點總數|無維度|
|UnusableNodeCount|否|無法使用的節點計數|Count|總計|無法使用的節點數目|無維度|
|WaitingForStartTaskNodeCount|否|等候啟動工作節點計數|Count|總計|等待啟動工作完成的節點數目|無維度|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|作用中核心|是|作用中核心|計數|Average|使用中核心數目|案例，ClusterName|
|作用中節點|是|作用中節點|計數|Average|執行節點的數目|案例，ClusterName|
|閒置核心|是|閒置核心|計數|Average|閒置核心數|案例，ClusterName|
|閒置節點|是|閒置節點|計數|Average|閒置節點的數目|案例，ClusterName|
|已完成的作業|是|已完成的作業|Count|總計|已完成的作業數目|案例，ClusterName，ResultType|
|已提交的作業|是|已提交的作業|Count|總計|已提交的作業數目|案例，ClusterName|
|正在離開的核心|是|正在離開的核心|計數|Average|離開核心數|案例，ClusterName|
|正在離開的節點|是|正在離開的節點|計數|Average|離開節點的數目|案例，ClusterName|
|先佔節點|是|先佔節點|計數|Average|搶先核心數|案例，ClusterName|
|先佔節點|是|先佔節點|計數|Average|先占節點的數量|案例，ClusterName|
|配額使用率百分比|是|配額使用率百分比|計數|Average|使用的配額百分比|案例，ClusterName，VmFamilyName，VmPriority|
|總核心數|是|總核心數|計數|Average|核心總數|案例，ClusterName|
|節點總計|是|節點總計|計數|Average|總節點數|案例，ClusterName|
|無法使用的核心|是|無法使用的核心|Count|Average|無法使用的核心數目|案例，ClusterName|
|無法使用的節點|是|無法使用的節點|計數|Average|無法使用的節點數目|案例，ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|是|廣播處理的計數|計數|Average|已處理的交易數目|Node、channel、type、status|
|ConnectionAccepted|是|已接受的連線|Count|總計|已接受的連線|節點|
|ConnectionActive|是|作用中的連線|計數|Average|作用中的連線|節點|
|ConnectionHandled|是|已處理的連線|Count|總計|已處理的連線|節點|
|ConsensusEtcdraftCommittedBlockNumber|是|共識 Etcdraft 認可的區塊編號|計數|Average|認可的最新區塊的區塊數目|Node、channel|
|CpuUsagePercentageInDouble|是|CPU 使用率百分比|百分比|最大值|CPU 使用率百分比|節點|
|EndorserEndorsementFailures|是|Endorser 簽署失敗|計數|Average|失敗的簽署數量。|Node、channel、鏈碼、chaincodeerror|
|GossipLeaderElectionLeader|是|Gossip 領導人選舉領導者|Count|總計|對等是領導者（1）或第2方（0）|Node、channel|
|GossipMembershipTotalPeersKnown|是|Gossip 成員資格總數已知|計數|Average|已知對等總數|Node、channel|
|GossipStateHeight|是|Gossip 狀態高度|計數|Average|目前的總帳高度|Node、channel|
|IOReadBytes|是|IO 讀取位元組|位元組|總計|IO 讀取位元組|節點|
|IOWriteBytes|是|IO 寫入位元組|位元組|總計|IO 寫入位元組|節點|
|LedgerTransactionCount|是|總帳交易計數|計數|Average|已處理的交易數目|Node、channel、transaction_type、鏈碼、validation_code|
|MemoryLimit|是|記憶體限制|位元組|Average|記憶體限制|節點|
|MemoryUsage|是|記憶體使用量|位元組|Average|記憶體使用量|節點|
|MemoryUsagePercentageInDouble|是|記憶體使用量百分比|百分比|Average|記憶體使用量百分比|節點|
|PendingTransactions|是|擱置交易|計數|Average|擱置交易|節點|
|ProcessedBlocks|是|已處理的區塊|Count|總計|已處理的區塊|節點|
|ProcessedTransactions|是|已處理的交易|Count|總計|已處理的交易|節點|
|QueuedTransactions|是|已排入佇列的交易|計數|Average|已排入佇列的交易|節點|
|RequestHandled|是|已處理的要求|Count|總計|已處理的要求|節點|
|StorageUsage|是|儲存體使用量|位元組|Average|儲存體使用量|節點|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|cachehits|是|快取點擊|Count|總計||ShardId|
|cachehits0|是|快取點擊 (分區 0)|Count|總計||無維度|
|cachehits1|是|快取點擊 (分區 1)|Count|總計||無維度|
|cachehits2|是|快取點擊 (分區 2)|Count|總計||無維度|
|cachehits3|是|快取點擊 (分區 3)|Count|總計||無維度|
|cachehits4|是|快取點擊 (分區 4)|Count|總計||無維度|
|cachehits5|是|快取點擊 (分區 5)|Count|總計||無維度|
|cachehits6|是|快取點擊 (分區 6)|Count|總計||無維度|
|cachehits7|是|快取點擊 (分區 7)|Count|總計||無維度|
|cachehits8|是|快取點擊 (分區 8)|Count|總計||無維度|
|cachehits9|是|快取點擊 (分區 9)|Count|總計||無維度|
|cacheLatency|是|快取延遲 (毫秒) (預覽)|計數|Average||ShardId|
|cachemisses|是|快取遺漏|Count|總計||ShardId|
|cachemisses0|是|快取遺漏 (分區 0)|Count|總計||無維度|
|cachemisses1|是|快取遺漏 (分區 1)|Count|總計||無維度|
|cachemisses2|是|快取遺漏 (分區 2)|Count|總計||無維度|
|cachemisses3|是|快取遺漏 (分區 3)|Count|總計||無維度|
|cachemisses4|是|快取遺漏 (分區 4)|Count|總計||無維度|
|cachemisses5|是|快取遺漏 (分區 5)|Count|總計||無維度|
|cachemisses6|是|快取遺漏 (分區 6)|Count|總計||無維度|
|cachemisses7|是|快取遺漏 (分區 7)|Count|總計||無維度|
|cachemisses8|是|快取遺漏 (分區 8)|Count|總計||無維度|
|cachemisses9|是|快取遺漏 (分區 9)|Count|總計||無維度|
|cachemissrate|是|快取遺漏率|百分比|cachemissrate||ShardId|
|cacheRead|是|快取讀取|每秒位元組|最大值||ShardId|
|cacheRead0|是|快取讀取 (分區 0)|每秒位元組|最大值||無維度|
|cacheRead1|是|快取讀取 (分區 1)|每秒位元組|最大值||無維度|
|cacheRead2|是|快取讀取 (分區 2)|每秒位元組|最大值||無維度|
|cacheRead3|是|快取讀取 (分區 3)|每秒位元組|最大值||無維度|
|cacheRead4|是|快取讀取 (分區 4)|每秒位元組|最大值||無維度|
|cacheRead5|是|快取讀取 (分區 5)|每秒位元組|最大值||無維度|
|cacheRead6|是|快取讀取 (分區 6)|每秒位元組|最大值||無維度|
|cacheRead7|是|快取讀取 (分區 7)|每秒位元組|最大值||無維度|
|cacheRead8|是|快取讀取 (分區 8)|每秒位元組|最大值||無維度|
|cacheRead9|是|快取讀取 (分區 9)|每秒位元組|最大值||無維度|
|cacheWrite|是|快取寫入|每秒位元組|最大值||ShardId|
|cacheWrite0|是|快取寫入 (分區 0)|每秒位元組|最大值||無維度|
|cacheWrite1|是|快取寫入 (分區 1)|每秒位元組|最大值||無維度|
|cacheWrite2|是|快取寫入 (分區 2)|每秒位元組|最大值||無維度|
|cacheWrite3|是|快取寫入 (分區 3)|每秒位元組|最大值||無維度|
|cacheWrite4|是|快取寫入 (分區 4)|每秒位元組|最大值||無維度|
|cacheWrite5|是|快取寫入 (分區 5)|每秒位元組|最大值||無維度|
|cacheWrite6|是|快取寫入 (分區 6)|每秒位元組|最大值||無維度|
|cacheWrite7|是|快取寫入 (分區 7)|每秒位元組|最大值||無維度|
|cacheWrite8|是|快取寫入 (分區 8)|每秒位元組|最大值||無維度|
|cacheWrite9|是|快取寫入 (分區 9)|每秒位元組|最大值||無維度|
|connectedclients|是|連線的用戶端|Count|最大值||ShardId|
|connectedclients0|是|連線的用戶端 (分區 0)|Count|最大值||無維度|
|connectedclients1|是|連線的用戶端 (分區 1)|Count|最大值||無維度|
|connectedclients2|是|連線的用戶端 (分區 2)|Count|最大值||無維度|
|connectedclients3|是|連線的用戶端 (分區 3)|Count|最大值||無維度|
|connectedclients4|是|連線的用戶端 (分區 4)|Count|最大值||無維度|
|connectedclients5|是|連線的用戶端 (分區 5)|Count|最大值||無維度|
|connectedclients6|是|連線的用戶端 (分區 6)|Count|最大值||無維度|
|connectedclients7|是|連線的用戶端 (分區 7)|Count|最大值||無維度|
|connectedclients8|是|連線的用戶端 (分區 8)|Count|最大值||無維度|
|connectedclients9|是|連線的用戶端 (分區 9)|Count|最大值||無維度|
|錯誤|是|Errors|Count|最大值||ShardId、ErrorType|
|evictedkeys|是|收回的金鑰|計數|總計||ShardId|
|evictedkeys0|是|收回的金鑰 (分區 0)|計數|總計||無維度|
|evictedkeys1|是|收回的金鑰 (分區 1)|計數|總計||無維度|
|evictedkeys2|是|收回的金鑰 (分區 2)|計數|總計||無維度|
|evictedkeys3|是|收回的金鑰 (分區 3)|計數|總計||無維度|
|evictedkeys4|是|收回的金鑰 (分區 4)|計數|總計||無維度|
|evictedkeys5|是|收回的金鑰 (分區 5)|計數|總計||無維度|
|evictedkeys6|是|收回的金鑰 (分區 6)|計數|總計||無維度|
|evictedkeys7|是|收回的金鑰 (分區 7)|計數|總計||無維度|
|evictedkeys8|是|收回的金鑰 (分區 8)|計數|總計||無維度|
|evictedkeys9|是|收回的金鑰 (分區 9)|計數|總計||無維度|
|expiredkeys|是|到期的金鑰|Count|總計||ShardId|
|expiredkeys0|是|到期的金鑰 (分區 0)|Count|總計||無維度|
|expiredkeys1|是|到期的金鑰 (分區 1)|Count|總計||無維度|
|expiredkeys2|是|到期的金鑰 (分區 2)|Count|總計||無維度|
|expiredkeys3|是|到期的金鑰 (分區 3)|Count|總計||無維度|
|expiredkeys4|是|到期的金鑰 (分區 4)|Count|總計||無維度|
|expiredkeys5|是|到期的金鑰 (分區 5)|Count|總計||無維度|
|expiredkeys6|是|到期的金鑰 (分區 6)|Count|總計||無維度|
|expiredkeys7|是|到期的金鑰 (分區 7)|Count|總計||無維度|
|expiredkeys8|是|到期的金鑰 (分區 8)|Count|總計||無維度|
|expiredkeys9|是|到期的金鑰 (分區 9)|Count|總計||無維度|
|getcommands|是|取得|Count|總計||ShardId|
|getcommands0|是|取得 (分區 0)|Count|總計||無維度|
|getcommands1|是|取得 (分區 1)|Count|總計||無維度|
|getcommands2|是|取得 (分區 2)|Count|總計||無維度|
|getcommands3|是|取得 (分區 3)|Count|總計||無維度|
|getcommands4|是|取得 (分區 4)|Count|總計||無維度|
|getcommands5|是|取得 (分區 5)|Count|總計||無維度|
|getcommands6|是|取得 (分區 6)|計數|總計||無維度|
|getcommands7|是|取得 (分區 7)|計數|總計||無維度|
|getcommands8|是|取得 (分區 8)|計數|總計||無維度|
|getcommands9|是|取得 (分區 9)|計數|總計||無維度|
|operationsPerSecond|是|每秒的作業數|Count|最大值||ShardId|
|operationsPerSecond0|是|每秒的作業數 (分區 0)|Count|最大值||無維度|
|operationsPerSecond1|是|每秒的作業數 (分區 1)|Count|最大值||無維度|
|operationsPerSecond2|是|每秒的作業數 (分區 2)|Count|最大值||無維度|
|operationsPerSecond3|是|每秒的作業數 (分區 3)|Count|最大值||無維度|
|operationsPerSecond4|是|每秒的作業數 (分區 4)|Count|最大值||無維度|
|operationsPerSecond5|是|每秒的作業數 (分區 5)|Count|最大值||無維度|
|operationsPerSecond6|是|每秒的作業數 (分區 6)|Count|最大值||無維度|
|operationsPerSecond7|是|每秒的作業數 (分區 7)|Count|最大值||無維度|
|operationsPerSecond8|是|每秒的作業數 (分區 8)|Count|最大值||無維度|
|operationsPerSecond9|是|每秒的作業數 (分區 9)|Count|最大值||無維度|
|percentProcessorTime|是|CPU|百分比|最大值||ShardId|
|percentProcessorTime0|是|CPU (分區 0)|百分比|最大值||無維度|
|percentProcessorTime1|是|CPU (分區 1)|百分比|最大值||無維度|
|percentProcessorTime2|是|CPU (分區 2)|百分比|最大值||無維度|
|percentProcessorTime3|是|CPU (分區 3)|百分比|最大值||無維度|
|percentProcessorTime4|是|CPU (分區 4)|百分比|最大值||無維度|
|percentProcessorTime5|是|CPU (分區 5)|百分比|最大值||無維度|
|percentProcessorTime6|是|CPU (分區 6)|百分比|最大值||無維度|
|percentProcessorTime7|是|CPU (分區 7)|百分比|最大值||無維度|
|percentProcessorTime8|是|CPU (分區 8)|百分比|最大值||無維度|
|percentProcessorTime9|是|CPU (分區 9)|百分比|最大值||無維度|
|serverLoad|是|伺服器負載|百分比|最大值||ShardId|
|serverLoad0|是|伺服器負載 (分區 0)|百分比|最大值||無維度|
|serverLoad1|是|伺服器負載 (分區 1)|百分比|最大值||無維度|
|serverLoad2|是|伺服器負載 (分區 2)|百分比|最大值||無維度|
|serverLoad3|是|伺服器負載 (分區 3)|百分比|最大值||無維度|
|serverLoad4|是|伺服器負載 (分區 4)|百分比|最大值||無維度|
|serverLoad5|是|伺服器負載 (分區 5)|百分比|最大值||無維度|
|serverLoad6|是|伺服器負載 (分區 6)|百分比|最大值||無維度|
|serverLoad7|是|伺服器負載 (分區 7)|百分比|最大值||無維度|
|serverLoad8|是|伺服器負載 (分區 8)|百分比|最大值||無維度|
|serverLoad9|是|伺服器負載 (分區 9)|百分比|最大值||無維度|
|setcommands|是|集合|Count|總計||ShardId|
|setcommands0|是|設定 (分區 0)|Count|總計||無維度|
|setcommands1|是|設定 (分區 1)|Count|總計||無維度|
|setcommands2|是|設定 (分區 2)|Count|總計||無維度|
|setcommands3|是|設定 (分區 3)|Count|總計||無維度|
|setcommands4|是|設定 (分區 4)|Count|總計||無維度|
|setcommands5|是|設定 (分區 5)|Count|總計||無維度|
|setcommands6|是|設定 (分區 6)|Count|總計||無維度|
|setcommands7|是|設定 (分區 7)|Count|總計||無維度|
|setcommands8|是|設定 (分區 8)|Count|總計||無維度|
|setcommands9|是|設定 (分區 9)|Count|總計||無維度|
|totalcommandsprocessed|是|總作業數|Count|總計||ShardId|
|totalcommandsprocessed0|是|總作業數 (分區 0)|Count|總計||無維度|
|totalcommandsprocessed1|是|總作業數 (分區 1)|計數|總計||無維度|
|totalcommandsprocessed2|是|總作業數 (分區 2)|計數|總計||無維度|
|totalcommandsprocessed3|是|總作業數 (分區 3)|計數|總計||無維度|
|totalcommandsprocessed4|是|總作業數 (分區 4)|計數|總計||無維度|
|totalcommandsprocessed5|是|總作業數 (分區 5)|計數|總計||無維度|
|totalcommandsprocessed6|是|總作業數 (分區 6)|計數|總計||無維度|
|totalcommandsprocessed7|是|總作業數 (分區 7)|計數|總計||無維度|
|totalcommandsprocessed8|是|總作業數 (分區 8)|計數|總計||無維度|
|totalcommandsprocessed9|是|總作業數 (分區 9)|計數|總計||無維度|
|totalkeys|是|索引鍵總計|Count|最大值||ShardId|
|totalkeys0|是|金鑰總數 (分區 0)|Count|最大值||無維度|
|totalkeys1|是|金鑰總數 (分區 1)|Count|最大值||無維度|
|totalkeys2|是|金鑰總數 (分區 2)|Count|最大值||無維度|
|totalkeys3|是|金鑰總數 (分區 3)|Count|最大值||無維度|
|totalkeys4|是|金鑰總數 (分區 4)|Count|最大值||無維度|
|totalkeys5|是|金鑰總數 (分區 5)|Count|最大值||無維度|
|totalkeys6|是|金鑰總數 (分區 6)|Count|最大值||無維度|
|totalkeys7|是|金鑰總數 (分區 7)|Count|最大值||無維度|
|totalkeys8|是|金鑰總數 (分區 8)|Count|最大值||無維度|
|totalkeys9|是|金鑰總數 (分區 9)|Count|最大值||無維度|
|usedmemory|是|已使用的記憶體|位元組|最大值||ShardId|
|usedmemory0|是|已用的記憶體 (分區 0)|位元組|最大值||無維度|
|usedmemory1|是|已用的記憶體 (分區 1)|位元組|最大值||無維度|
|usedmemory2|是|已用的記憶體 (分區 2)|位元組|最大值||無維度|
|usedmemory3|是|已用的記憶體 (分區 3)|位元組|最大值||無維度|
|usedmemory4|是|已用的記憶體 (分區 4)|位元組|最大值||無維度|
|usedmemory5|是|已用的記憶體 (分區 5)|位元組|最大值||無維度|
|usedmemory6|是|已用的記憶體 (分區 6)|位元組|最大值||無維度|
|usedmemory7|是|已用的記憶體 (分區 7)|位元組|最大值||無維度|
|usedmemory8|是|已用的記憶體 (分區 8)|位元組|最大值||無維度|
|usedmemory9|是|已用的記憶體 (分區 9)|位元組|最大值||無維度|
|usedmemorypercentage|是|已用的記憶體百分比|百分比|最大值||ShardId|
|usedmemoryRss|是|已用的記憶體 RSS|位元組|最大值||ShardId|
|usedmemoryRss0|是|已用的記憶體 RSS (分區 0)|位元組|最大值||無維度|
|usedmemoryRss1|是|已用的記憶體 RSS (分區 1)|位元組|最大值||無維度|
|usedmemoryRss2|是|已用的記憶體 RSS (分區 2)|位元組|最大值||無維度|
|usedmemoryRss3|是|已用的記憶體 RSS (分區 3)|位元組|最大值||無維度|
|usedmemoryRss4|是|已用的記憶體 RSS (分區 4)|位元組|最大值||無維度|
|usedmemoryRss5|是|已用的記憶體 RSS (分區 5)|位元組|最大值||無維度|
|usedmemoryRss6|是|已用的記憶體 RSS (分區 6)|位元組|最大值||無維度|
|usedmemoryRss7|是|已用的記憶體 RSS (分區 7)|位元組|最大值||無維度|
|usedmemoryRss8|是|已用的記憶體 RSS (分區 8)|位元組|最大值||無維度|
|usedmemoryRss9|是|已用的記憶體 RSS (分區 9)|位元組|最大值||無維度|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft Cdn/cdnwebapplicationfirewallpolicies

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|是|Web 應用程式防火牆要求計數|Count|總計|Web 應用程式防火牆處理的用戶端要求數|PolicyName、RuleName、Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|否|磁碟讀取|每秒位元組|Average|監視期間從磁碟讀取的平均位元組。|RoleInstanceId|
|Disk Read Operations/Sec|是|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS。|RoleInstanceId|
|Disk Write Bytes/Sec|否|磁碟寫入|每秒位元組|Average|監視期間寫入磁碟的平均位元組。|RoleInstanceId|
|Disk Write Operations/Sec|是|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS。|RoleInstanceId|
|Network In|是|Network In|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目。|RoleInstanceId|
|Network Out|是|Network Out|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目。|RoleInstanceId|
|CPU 百分比|是|Percentage CPU|百分比|Average|虛擬機器目前使用的配置計算單位百分比。|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|否|磁碟讀取|每秒位元組|Average|監視期間從磁碟讀取的平均位元組。|無維度|
|Disk Read Operations/Sec|是|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS。|無維度|
|Disk Write Bytes/Sec|否|磁碟寫入|每秒位元組|Average|監視期間寫入磁碟的平均位元組。|無維度|
|Disk Write Operations/Sec|是|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS。|無維度|
|Network In|是|Network In|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目。|無維度|
|Network Out|是|Network Out|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目。|無維度|
|CPU 百分比|是|Percentage CPU|百分比|Average|虛擬機器目前使用的配置計算單位百分比。|無維度|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|
|輸出|是|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|交易|是|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName/、Authentication|
|UsedCapacity|否|已使用容量|位元組|Average|帳戶使用的容量|無維度|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|
|BlobCapacity|否|Blob 容量|位元組|Average|儲存體帳戶之 Blob 服務所使用的儲存體量 (位元組)。|BlobType，層|
|BlobCount|否|Blob 計數|計數|Average|儲存體帳戶之 Blob 服務中的 Blob 數目。|BlobType，層|
|ContainerCount|是|Blob 容器計數|Count|Average|儲存體帳戶之 Blob 服務中的容器數目。|無維度|
|輸出|是|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|IndexCapacity|否|索引容量|位元組|Average|ADLS Gen2 （階層）索引所使用的儲存體數量（以位元組為單位）。|無維度|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|交易|是|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName/、Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication、檔案共用|
|輸出|是|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication、檔案共用|
|FileCapacity|否|檔案容量|位元組|Average|儲存體帳戶之檔案服務所使用的儲存體量 (位元組)。|FileShare|
|FileCount|否|檔案計數|計數|Average|儲存體帳戶之檔案服務中的檔案數目。|FileShare|
|FileShareCount|否|檔案共用計數|計數|Average|儲存體帳戶之檔案服務中的檔案共用數目。|無維度|
|FileShareQuota|否|檔案共用配額大小|位元組|Average|Azure 檔案儲存體服務可使用的儲存體數量上限（以位元組為單位）。|FileShare|
|FileShareSnapshotCount|否|檔案共用快照集計數|Count|Average|儲存體帳戶之檔案服務中的共用上存在的快照集數目。|FileShare|
|FileShareSnapshotSize|否|檔案共用快照集大小|位元組|Average|儲存體帳戶之檔案服務中的快照集所使用的儲存體數量（以位元組為單位）。|FileShare|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication、檔案共用|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication、檔案共用|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication、檔案共用|
|交易|是|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication、檔案共用|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|
|輸出|是|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|QueueCapacity|是|佇列容量|位元組|Average|儲存體帳戶之佇列服務所使用的儲存體量 (位元組)。|無維度|
|QueueCount|是|佇列計數|計數|Average|儲存體帳戶之佇列服務中的佇列數目。|無維度|
|QueueMessageCount|是|佇列訊息計數|Count|Average|儲存體帳戶之佇列服務中的佇列訊息大約數目。|無維度|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|交易|是|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName/、Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|
|輸出|是|輸出|位元組|總計|輸出資料量 (位元組)。 此數目包括從外部用戶端輸出到 Azure 儲存體與 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|對儲存體服務或指定之 API 作業發出之成功要求的端對端延遲（以毫秒為單位）。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的延遲（以毫秒為單位）。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|TableCapacity|是|資料表容量|位元組|Average|儲存體帳戶之資料表服務所使用的儲存體量 (位元組)。|無維度|
|TableCount|是|資料表計數|計數|Average|儲存體帳戶之資料表服務中的資料表數目。|無維度|
|TableEntityCount|是|資料表實體計數|Count|Average|儲存體帳戶之資料表服務中的資料表實體數目。|無維度|
|交易|是|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName/、Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|BlockedCalls|是|已封鎖的呼叫|Count|總計|超過速率或配額限制的呼叫數目。|ApiName、OperationName、Region|
|CharactersTrained|是|已定型的字元|Count|總計|已定型的字元總數。|ApiName、OperationName、Region|
|CharactersTranslated|是|轉譯字元數|Count|總計|傳入文字要求中的字元總數。|ApiName、OperationName、Region|
|ClientErrors|是|用戶端錯誤|Count|總計|具有用戶端錯誤 (HTTP 回應碼 4xx) 的呼叫數目。|ApiName、OperationName、Region|
|DataIn|是|資料輸入|位元組|總計|內送資料大小，以位元組為單位。|ApiName、OperationName、Region|
|DataOut|是|資料輸出|位元組|總計|輸出資料大小，以位元組為單位。|ApiName、OperationName、Region|
|Latency|是|Latency|毫秒|Average|延遲，以毫秒為單位。|ApiName、OperationName、Region|
|ProcessedImages|是|已處理的影像|Count|總計| 影像處理的交易數目。|ApiName、功能區、UsageChannel、Region|
|ServerErrors|是|伺服器錯誤|Count|總計|具有服務內部錯誤 (HTTP 回應碼 5xx) 的呼叫數目。|ApiName、OperationName、Region|
|SpeechSessionDuration|是|語音工作階段持續時間|秒|總計|語音工作階段的總持續時間 (秒)。|ApiName、OperationName、Region|
|SuccessfulCalls|是|成功的呼叫|Count|總計|成功的呼叫數。|ApiName、OperationName、Region|
|TotalCalls|是|呼叫總數|Count|總計|呼叫總數。|ApiName、OperationName、Region|
|TotalErrors|是|錯誤總數|Count|總計|具有錯誤回應 (HTTP 回應碼 4xx 或 5xx) 的呼叫總數。|ApiName、OperationName、Region|
|TotalTokenCalls|是|呼叫權杖總計|Count|總計|呼叫權杖次數總計。|ApiName、OperationName、Region|
|TotalTransactions|是|交易數總計|Count|總計|交易總數。|無維度|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|CPU Credits Consumed|是|CPU Credits Consumed|計數|Average|由虛擬機器取用的信用額度總數|無維度|
|剩餘 CPU 信用額度|是|剩餘 CPU 信用額度|計數|Average|可用於高載的信用額度總數|無維度|
|資料磁碟佇列深度|是|資料磁碟佇列深度 (預覽)|Count|Average|資料磁碟佇列深度 (或佇列長度)|LUN|
|資料磁碟讀取位元組數/秒|是|資料磁碟讀取位元組/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|LUN|
|資料磁碟讀取作業/秒|是|資料磁碟讀取作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|LUN|
|資料磁碟寫入位元組數/秒|是|資料磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|LUN|
|資料磁碟寫入作業/秒|是|資料磁碟寫入作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|LUN|
|Disk Read Bytes|是|Disk Read Bytes|位元組|總計|在監視期間從磁片讀取的位元組數|無維度|
|Disk Read Operations/Sec|是|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS|無維度|
|Disk Write Bytes|是|Disk Write Bytes|位元組|總計|在監視期間寫入磁片的位元組數|無維度|
|Disk Write Operations/Sec|是|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS|無維度|
|連入流量|是|輸入流量|Count|Average|輸入流量是傳入方向的目前流量數（進入 VM 的流量）|無維度|
|輸入流量最大建立速率|是|輸入流量最大建立速率|每秒計數|Average|輸入流量的最大建立速率（進入 VM 的流量）|無維度|
|Network In|是|計費網路流入量 (已淘汰)|位元組|總計|虛擬機器在所有網路介面上接收的可計費位元組數（連入流量）（已淘汰）|無維度|
|網路流入量總計|是|網路流入量總計|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|無維度|
|Network Out|是|計費網路流出量 (已淘汰)|位元組|總計|虛擬機器（連出流量）在所有網路介面上傳出的可計費位元組數（已淘汰）|無維度|
|網路流出量總計|是|網路流出量總計|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|無維度|
|OS 磁碟佇列深度|是|OS 磁碟佇列深度 (預覽)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|無維度|
|OS 磁碟讀取位元組/秒|是|OS 磁碟讀取位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無維度|
|OS 磁碟讀取作業/秒|是|OS 磁碟讀取作業/秒 (預覽)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無維度|
|OS 磁碟寫入位元組數/秒|是|OS 磁碟寫入位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無維度|
|OS 磁碟寫入作業/秒|是|OS 磁碟寫入作業/秒 (預覽)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無維度|
|OS 每一磁碟 QD|是|OS 磁碟 QD (已淘汰)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|無維度|
|OS 每一磁碟讀取位元組/秒|是|OS 磁碟讀取位元組數/秒 (已淘汰)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無維度|
|OS 每一磁碟讀取作業/秒|是|OS 磁碟讀取作業數/秒 (已淘汰)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無維度|
|OS 每一磁碟寫入位元組/秒|是|OS 磁碟寫入位元組數/秒 (已淘汰)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無維度|
|OS 每一磁碟寫入作業/秒|是|OS 磁碟寫入作業數/秒 (已淘汰)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無維度|
|連出流量|是|連出流量|Count|Average|輸出流程是輸出方向的目前流量數（從 VM 傳出的流量）|無維度|
|輸出流量最大建立速率|是|輸出流量最大建立速率|每秒計數|Average|輸出流量的最大建立速率（從 VM 傳出的流量）|無維度|
|每一磁碟 QD|是|資料磁碟 QD (已淘汰)|Count|Average|資料磁碟佇列深度 (或佇列長度)|SlotId|
|每一磁碟讀取位元組/秒|是|資料磁碟讀取位元組數/秒 (已淘汰)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|SlotId|
|每一磁碟讀取作業/秒|是|資料磁碟讀取作業數/秒 (已淘汰)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|SlotId|
|每一磁碟寫入位元組/秒|是|資料磁碟寫入位元組數/秒 (已淘汰)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|SlotId|
|每一磁碟寫入作業/秒|是|資料磁碟寫入作業數/秒 (已淘汰)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|SlotId|
|CPU 百分比|是|Percentage CPU|百分比|Average|目前虛擬機器正在使用中的已配置計算單位百分比|無維度|
|進階資料磁碟快取讀取命中|是|進階資料磁碟快取讀取命中 (預覽)|百分比|Average|進階資料磁碟快取讀取命中|LUN|
|進階資料磁碟快取讀取遺漏|是|進階資料磁碟快取讀取遺漏 (預覽)|百分比|Average|進階資料磁碟快取讀取遺漏|LUN|
|進階 OS 磁碟快取讀取命中|是|進階 OS 磁碟快取讀取命中 (預覽)|百分比|Average|進階 OS 磁碟快取讀取命中|無維度|
|進階 OS 磁碟快取讀取遺漏|是|進階 OS 磁碟快取讀取遺漏 (預覽)|百分比|Average|進階 OS 磁碟快取讀取遺漏|無維度|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|CPU Credits Consumed|是|CPU Credits Consumed|Count|Average|由虛擬機器取用的信用額度總數|無維度|
|剩餘 CPU 信用額度|是|剩餘 CPU 信用額度|Count|Average|可用於高載的信用額度總數|無維度|
|資料磁碟佇列深度|是|資料磁碟佇列深度 (預覽)|Count|Average|資料磁碟佇列深度 (或佇列長度)|LUN，VMName|
|資料磁碟讀取位元組數/秒|是|資料磁碟讀取位元組/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|LUN，VMName|
|資料磁碟讀取作業/秒|是|資料磁碟讀取作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|LUN，VMName|
|資料磁碟寫入位元組數/秒|是|資料磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|LUN，VMName|
|資料磁碟寫入作業/秒|是|資料磁碟寫入作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|LUN，VMName|
|Disk Read Bytes|是|Disk Read Bytes|位元組|總計|在監視期間從磁片讀取的位元組數|VMName|
|Disk Read Operations/Sec|是|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS|VMName|
|Disk Write Bytes|是|Disk Write Bytes|位元組|總計|在監視期間寫入磁片的位元組數|VMName|
|Disk Write Operations/Sec|是|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS|VMName|
|連入流量|是|輸入流量|Count|Average|輸入流量是傳入方向的目前流量數（進入 VM 的流量）|VMName|
|輸入流量最大建立速率|是|輸入流量最大建立速率|每秒計數|Average|輸入流量的最大建立速率（進入 VM 的流量）|VMName|
|Network In|是|計費網路流入量 (已淘汰)|位元組|總計|虛擬機器在所有網路介面上接收的可計費位元組數（連入流量）（已淘汰）|VMName|
|網路流入量總計|是|網路流入量總計|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|VMName|
|Network Out|是|計費網路流出量 (已淘汰)|位元組|總計|虛擬機器（連出流量）在所有網路介面上傳出的可計費位元組數（已淘汰）|VMName|
|網路流出量總計|是|網路流出量總計|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|VMName|
|OS 磁碟佇列深度|是|OS 磁碟佇列深度 (預覽)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|VMName|
|OS 磁碟讀取位元組/秒|是|OS 磁碟讀取位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|VMName|
|OS 磁碟讀取作業/秒|是|OS 磁碟讀取作業/秒 (預覽)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|VMName|
|OS 磁碟寫入位元組數/秒|是|OS 磁碟寫入位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|VMName|
|OS 磁碟寫入作業/秒|是|OS 磁碟寫入作業/秒 (預覽)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|VMName|
|OS 每一磁碟 QD|是|OS 磁碟 QD (已淘汰)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|無維度|
|OS 每一磁碟讀取位元組/秒|是|OS 磁碟讀取位元組數/秒 (已淘汰)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無維度|
|OS 每一磁碟讀取作業/秒|是|OS 磁碟讀取作業數/秒 (已淘汰)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無維度|
|OS 每一磁碟寫入位元組/秒|是|OS 磁碟寫入位元組數/秒 (已淘汰)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無維度|
|OS 每一磁碟寫入作業/秒|是|OS 磁碟寫入作業數/秒 (已淘汰)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無維度|
|連出流量|是|連出流量|Count|Average|輸出流程是輸出方向的目前流量數（從 VM 傳出的流量）|VMName|
|輸出流量最大建立速率|是|輸出流量最大建立速率|每秒計數|Average|輸出流量的最大建立速率（從 VM 傳出的流量）|VMName|
|每一磁碟 QD|是|資料磁碟 QD (已淘汰)|Count|Average|資料磁碟佇列深度 (或佇列長度)|SlotId|
|每一磁碟讀取位元組/秒|是|資料磁碟讀取位元組數/秒 (已淘汰)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|SlotId|
|每一磁碟讀取作業/秒|是|資料磁碟讀取作業數/秒 (已淘汰)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|SlotId|
|每一磁碟寫入位元組/秒|是|資料磁碟寫入位元組數/秒 (已淘汰)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|SlotId|
|每一磁碟寫入作業/秒|是|資料磁碟寫入作業數/秒 (已淘汰)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|SlotId|
|CPU 百分比|是|Percentage CPU|百分比|Average|目前虛擬機器正在使用中的已配置計算單位百分比|VMName|
|進階資料磁碟快取讀取命中|是|進階資料磁碟快取讀取命中 (預覽)|百分比|Average|進階資料磁碟快取讀取命中|LUN，VMName|
|進階資料磁碟快取讀取遺漏|是|進階資料磁碟快取讀取遺漏 (預覽)|百分比|Average|進階資料磁碟快取讀取遺漏|LUN，VMName|
|進階 OS 磁碟快取讀取命中|是|進階 OS 磁碟快取讀取命中 (預覽)|百分比|Average|進階 OS 磁碟快取讀取命中|VMName|
|進階 OS 磁碟快取讀取遺漏|是|進階 OS 磁碟快取讀取遺漏 (預覽)|百分比|Average|進階 OS 磁碟快取讀取遺漏|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|CPU Credits Consumed|是|CPU Credits Consumed|Count|Average|由虛擬機器取用的信用額度總數|無維度|
|剩餘 CPU 信用額度|是|剩餘 CPU 信用額度|Count|Average|可用於高載的信用額度總數|無維度|
|資料磁碟佇列深度|是|資料磁碟佇列深度 (預覽)|Count|Average|資料磁碟佇列深度 (或佇列長度)|LUN|
|資料磁碟讀取位元組數/秒|是|資料磁碟讀取位元組/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|LUN|
|資料磁碟讀取作業/秒|是|資料磁碟讀取作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|LUN|
|資料磁碟寫入位元組數/秒|是|資料磁碟寫入位元組/秒 (預覽)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|LUN|
|資料磁碟寫入作業/秒|是|資料磁碟寫入作業/秒 (預覽)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|LUN|
|Disk Read Bytes|是|Disk Read Bytes|位元組|總計|在監視期間從磁片讀取的位元組數|無維度|
|Disk Read Operations/Sec|是|Disk Read Operations/Sec|每秒計數|Average|磁碟讀取 IOPS|無維度|
|Disk Write Bytes|是|Disk Write Bytes|位元組|總計|在監視期間寫入磁片的位元組數|無維度|
|Disk Write Operations/Sec|是|Disk Write Operations/Sec|每秒計數|Average|磁碟寫入 IOPS|無維度|
|連入流量|是|輸入流量|Count|Average|輸入流量是傳入方向的目前流量數（進入 VM 的流量）|無維度|
|輸入流量最大建立速率|是|輸入流量最大建立速率|每秒計數|Average|輸入流量的最大建立速率（進入 VM 的流量）|無維度|
|Network In|是|計費網路流入量 (已淘汰)|位元組|總計|虛擬機器在所有網路介面上接收的可計費位元組數（連入流量）（已淘汰）|無維度|
|網路流入量總計|是|網路流入量總計|位元組|總計|虛擬機器 (連入流量) 在所有網路介面上接收到的位元組數目|無維度|
|Network Out|是|計費網路流出量 (已淘汰)|位元組|總計|虛擬機器（連出流量）在所有網路介面上傳出的可計費位元組數（已淘汰）|無維度|
|網路流出量總計|是|網路流出量總計|位元組|總計|虛擬機器 (連出流量) 在所有網路介面上送出的位元組數目|無維度|
|OS 磁碟佇列深度|是|OS 磁碟佇列深度 (預覽)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|無維度|
|OS 磁碟讀取位元組/秒|是|OS 磁碟讀取位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無維度|
|OS 磁碟讀取作業/秒|是|OS 磁碟讀取作業/秒 (預覽)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無維度|
|OS 磁碟寫入位元組數/秒|是|OS 磁碟寫入位元組/秒 (預覽)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無維度|
|OS 磁碟寫入作業/秒|是|OS 磁碟寫入作業/秒 (預覽)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無維度|
|OS 每一磁碟 QD|是|OS 磁碟 QD (已淘汰)|Count|Average|OS 磁碟佇列深度 (或佇列長度)|無維度|
|OS 每一磁碟讀取位元組/秒|是|OS 磁碟讀取位元組數/秒 (已淘汰)|每秒計數|Average|OS 磁片在監視期間從單一磁片讀取的位元組/秒|無維度|
|OS 每一磁碟讀取作業/秒|是|OS 磁碟讀取作業數/秒 (已淘汰)|每秒計數|Average|在 OS 磁片監視期間從單一磁片讀取 IOPS|無維度|
|OS 每一磁碟寫入位元組/秒|是|OS 磁碟寫入位元組數/秒 (已淘汰)|每秒計數|Average|OS 磁片在監視期間寫入單一磁片的位元組/秒|無維度|
|OS 每一磁碟寫入作業/秒|是|OS 磁碟寫入作業數/秒 (已淘汰)|每秒計數|Average|針對 OS 磁片在監視期間從單一磁片寫入 IOPS|無維度|
|連出流量|是|連出流量|Count|Average|輸出流程是輸出方向的目前流量數（從 VM 傳出的流量）|無維度|
|輸出流量最大建立速率|是|輸出流量最大建立速率|每秒計數|Average|輸出流量的最大建立速率（從 VM 傳出的流量）|無維度|
|每一磁碟 QD|是|資料磁碟 QD (已淘汰)|Count|Average|資料磁碟佇列深度 (或佇列長度)|SlotId|
|每一磁碟讀取位元組/秒|是|資料磁碟讀取位元組數/秒 (已淘汰)|每秒計數|Average|在監視期間從單一磁片讀取的位元組/秒|SlotId|
|每一磁碟讀取作業/秒|是|資料磁碟讀取作業數/秒 (已淘汰)|每秒計數|Average|在監視期間從單一磁片讀取 IOPS|SlotId|
|每一磁碟寫入位元組/秒|是|資料磁碟寫入位元組數/秒 (已淘汰)|每秒計數|Average|在監視期間寫入單一磁片的位元組/秒|SlotId|
|每一磁碟寫入作業/秒|是|資料磁碟寫入作業數/秒 (已淘汰)|每秒計數|Average|在監視期間從單一磁片寫入 IOPS|SlotId|
|CPU 百分比|是|Percentage CPU|百分比|Average|目前虛擬機器正在使用中的已配置計算單位百分比|無維度|
|進階資料磁碟快取讀取命中|是|進階資料磁碟快取讀取命中 (預覽)|百分比|Average|進階資料磁碟快取讀取命中|LUN|
|進階資料磁碟快取讀取遺漏|是|進階資料磁碟快取讀取遺漏 (預覽)|百分比|Average|進階資料磁碟快取讀取遺漏|LUN|
|進階 OS 磁碟快取讀取命中|是|進階 OS 磁碟快取讀取命中 (預覽)|百分比|Average|進階 OS 磁碟快取讀取命中|無維度|
|進階 OS 磁碟快取讀取遺漏|是|進階 OS 磁碟快取讀取遺漏 (預覽)|百分比|Average|進階 OS 磁碟快取讀取遺漏|無維度|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|CpuUsage|是|CPU 使用率|Count|Average|所有核心的 CPU 使用率 (以 millicores 為單位)。|containerName|
|MemoryUsage|是|記憶體使用量|位元組|Average|總記憶體使用率 (位元組)。|containerName|
|NetworkBytesReceivedPerSecond|是|每秒接收的網路位元組數|位元組|Average|每秒接收的網路位元組數。|無維度|
|NetworkBytesReceivedPerSecond|是|每秒傳輸的網路位元組數|位元組|Average|每秒傳輸的網路位元組數。|無維度|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|是|AgentPool CPU 時間|秒|總計|AgentPool CPU 時間（秒）|無維度|
|RunDuration|是|執行持續時間|毫秒|總計|執行持續時間（毫秒）|無維度|
|SuccessfulPullCount|是|成功提取計數|計數|Average|成功的映射提取次數|無維度|
|SuccessfulPushCount|是|成功推送計數|計數|Average|成功的映射推送數目|無維度|
|TotalPullCount|是|提取總數|計數|Average|總映射提取次數|無維度|
|TotalPushCount|是|推送總數|計數|Average|映射推播總數總計|無維度|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|否|受控叢集中可用的 cpu 核心總數|計數|Average|受控叢集中可用的 cpu 核心總數|無維度|
|kube_node_status_allocatable_memory_bytes|否|受控叢集中可用的記憶體總量|位元組|Average|受控叢集中可用的記憶體總量|無維度|
|kube_node_status_condition|否|各種節點條件的狀態|計數|Average|各種節點條件的狀態|條件、狀態、status2、節點|
|kube_pod_status_phase|否|各階段的 Pod 數目|計數|Average|各階段的 Pod 數目|階段、命名空間、pod|
|kube_pod_status_ready|否|處於就緒狀態的 Pod 數目|計數|Average|處於就緒狀態的 Pod 數目|命名空間、pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>CustomProviders/resourceproviders

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|FailedRequests|是|失敗的要求|Count|總計|取得自訂資源提供者的可用記錄|HttpMethod、CallPath、StatusCode|
|SuccessfullRequests|是|成功的要求|Count|總計|自訂提供者提出的成功要求|HttpMethod、CallPath、StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AvailableCapacity|是|可用容量|位元組|Average|報告期間的可用容量（以位元組為單位）。|無維度|
|BytesUploadedToCloud|是|上傳的雲端位元組數 (裝置)|位元組|Average|報告期間從裝置上傳至 Azure 的位元組總數。|無維度|
|BytesUploadedToCloudPerShare|是|上傳的雲端位元組數 (共用)|位元組|Average|報告期間從共用上傳至 Azure 的位元組總數。|共用|
|CloudReadThroughput|是|雲端下載輸送量|每秒位元組|Average|在報告期間，將雲端下載至 Azure 的輸送量。|無維度|
|CloudReadThroughputPerShare|是|雲端下載輸送量 (共用)|每秒位元組|Average|報告期間從共用到 Azure 的下載輸送量。|共用|
|CloudUploadThroughput|是|雲端上傳輸送量|每秒位元組|Average|在報告期間，將雲端上傳至 Azure 的輸送量。|無維度|
|CloudUploadThroughputPerShare|是|雲端上傳輸送量 (共用)|每秒位元組|Average|報告期間從共用到 Azure 的上傳輸送量。|共用|
|HyperVMemoryUtilization|是|Edge 計算 - 記憶體使用量|百分比|Average|使用中的 RAM 數量|InstanceName|
|HyperVVirtualProcessorUtilization|是|Edge 計算 - 百分比 CPU|百分比|Average|CPU 使用量百分比|InstanceName|
|NICReadThroughput|是|讀取輸送量 (網路)|每秒位元組|Average|閘道中所有磁片區的報告期間，裝置上網路介面的讀取輸送量。|InstanceName|
|NICWriteThroughput|是|寫入輸送量 (網路)|每秒位元組|Average|閘道中所有磁片區的報告期間，裝置上網路介面的寫入輸送量。|InstanceName|
|TotalCapacity|是|容量總計|位元組|Average|容量總計|無維度|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|FailedRuns|是|失敗的執行|計數|總計||pipelineName、activityName|
|SuccessfulRuns|是|成功的執行|計數|總計||pipelineName、activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|是|已取消的活動執行計量|計數|總計||ActivityType、PipelineName、FailureType、Name|
|ActivityFailedRuns|是|失敗的活動執行計量|計數|總計||ActivityType、PipelineName、FailureType、Name|
|ActivitySucceededRuns|是|成功的活動執行計量|計數|總計||ActivityType、PipelineName、FailureType、Name|
|FactorySizeInGbUnits|是|處理站大小總計 (以 GB 為單位)|Count|最大值||無維度|
|IntegrationRuntimeAvailableMemory|是|整合執行階段可用記憶體|位元組|Average||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeAvailableNodeNumber|是|整合執行時間可用節點計數|計數|Average||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|是|整合執行階段佇列持續時間|秒|Average||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|是|整合執行階段 CPU 使用率|百分比|Average||IntegrationRuntimeName、NodeName|
|IntegrationRuntimeQueueLength|是|整合執行階段佇列長度|計數|Average||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|是|允許的處理站大小上限 (以 GB 為單位)|Count|最大值||無維度|
|MaxAllowedResourceCount|是|允許的實體計數上限|Count|最大值||無維度|
|PipelineCancelledRuns|是|已取消的管線執行計量|Count|總計||FailureType、Name|
|PipelineFailedRuns|是|失敗的管線執行計量|計數|總計||FailureType、Name|
|PipelineSucceededRuns|是|成功的管線執行計量|計數|總計||FailureType、Name|
|ResourceCount|是|實體總數|Count|最大值||無維度|
|TriggerCancelledRuns|是|已取消的觸發程序執行計量|計數|總計||Name、FailureType|
|TriggerFailedRuns|是|失敗的觸發程序執行計量|計數|總計||Name、FailureType|
|TriggerSucceededRuns|是|成功的觸發程序執行計量|計數|總計||Name、FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|DataRead|是|讀取的資料|位元組|總計|從帳戶讀取的總資料量。|無維度|
|DataWritten|是|寫入的資料|位元組|總計|寫入帳戶的總資料量。|無維度|
|ReadRequests|是|讀取要求|計數|總計|帳戶的資料讀取要求計數。|無維度|
|TotalStorage|是|儲存體總計|位元組|最大值|儲存在帳戶中的總資料量。|無維度|
|WriteRequests|是|寫入要求|Count|總計|帳戶的資料寫入要求計數。|無維度|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|active_connections|是|作用中的連線|計數|Average|作用中的連線|無維度|
|backup_storage_used|是|已使用的備份儲存體|位元組|Average|已使用的備份儲存體|無維度|
|connections_failed|是|失敗的連線|計數|總計|失敗的連線|無維度|
|cpu_percent|是|CPU 百分比|百分比|Average|CPU 百分比|無維度|
|io_consumption_percent|是|IO 百分比|百分比|Average|IO 百分比|無維度|
|memory_percent|是|記憶體百分比|百分比|Average|記憶體百分比|無維度|
|network_bytes_egress|是|Network Out|位元組|總計|跨作用中連線的網路輸出|無維度|
|network_bytes_ingress|是|Network In|位元組|總計|跨作用中連線的網路輸入|無維度|
|seconds_behind_master|是|複寫延遲 (秒)|Count|最大值|複寫延遲 (秒)|無維度|
|serverlog_storage_limit|是|伺服器記錄儲存體限制|位元組|Average|伺服器記錄儲存體限制|無維度|
|serverlog_storage_percent|是|伺服器記錄儲存體百分比|百分比|Average|伺服器記錄儲存體百分比|無維度|
|serverlog_storage_usage|是|使用的伺服器記錄儲存體|位元組|Average|使用的伺服器記錄儲存體|無維度|
|storage_limit|是|儲存體限制|位元組|最大值|儲存體限制|無維度|
|storage_percent|是|儲存體百分比|百分比|Average|儲存體百分比|無維度|
|storage_used|是|已使用儲存體|位元組|Average|已使用儲存體|無維度|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|active_connections|是|作用中的連線|計數|Average|作用中的連線|無維度|
|backup_storage_used|是|已使用的備份儲存體|位元組|Average|已使用的備份儲存體|無維度|
|connections_failed|是|失敗的連線|計數|總計|失敗的連線|無維度|
|cpu_percent|是|CPU 百分比|百分比|Average|CPU 百分比|無維度|
|io_consumption_percent|是|IO 百分比|百分比|Average|IO 百分比|無維度|
|memory_percent|是|記憶體百分比|百分比|Average|記憶體百分比|無維度|
|network_bytes_egress|是|Network Out|位元組|總計|跨作用中連線的網路輸出|無維度|
|network_bytes_ingress|是|Network In|位元組|總計|跨作用中連線的網路輸入|無維度|
|seconds_behind_master|是|複寫延遲 (秒)|Count|最大值|複寫延遲 (秒)|無維度|
|serverlog_storage_limit|是|伺服器記錄儲存體限制|位元組|最大值|伺服器記錄儲存體限制|無維度|
|serverlog_storage_percent|是|伺服器記錄儲存體百分比|百分比|Average|伺服器記錄儲存體百分比|無維度|
|serverlog_storage_usage|是|使用的伺服器記錄儲存體|位元組|Average|使用的伺服器記錄儲存體|無維度|
|storage_limit|是|儲存體限制|位元組|最大值|儲存體限制|無維度|
|storage_percent|是|儲存體百分比|百分比|Average|儲存體百分比|無維度|
|storage_used|是|已使用儲存體|位元組|Average|已使用儲存體|無維度|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|active_connections|是|作用中的連線|計數|Average|作用中的連線|無維度|
|backup_storage_used|是|已使用的備份儲存體|位元組|Average|已使用的備份儲存體|無維度|
|connections_failed|是|失敗的連線|Count|總計|失敗的連線|無維度|
|cpu_percent|是|CPU 百分比|百分比|Average|CPU 百分比|無維度|
|io_consumption_percent|是|IO 百分比|百分比|Average|IO 百分比|無維度|
|memory_percent|是|記憶體百分比|百分比|Average|記憶體百分比|無維度|
|network_bytes_egress|是|Network Out|位元組|總計|跨作用中連線的網路輸出|無維度|
|network_bytes_ingress|是|Network In|位元組|總計|跨作用中連線的網路輸入|無維度|
|pg_replica_log_delay_in_bytes|是|複本之間的最大延隔時間|位元組|最大值|最延遲複本的延遲（以位元組為單位）|無維度|
|pg_replica_log_delay_in_seconds|是|複本延隔時間|秒|最大值|複本延遲（秒）|無維度|
|serverlog_storage_limit|是|伺服器記錄儲存體限制|位元組|最大值|伺服器記錄儲存體限制|無維度|
|serverlog_storage_percent|是|伺服器記錄儲存體百分比|百分比|Average|伺服器記錄儲存體百分比|無維度|
|serverlog_storage_usage|是|使用的伺服器記錄儲存體|位元組|Average|使用的伺服器記錄儲存體|無維度|
|storage_limit|是|儲存體限制|位元組|最大值|儲存體限制|無維度|
|storage_percent|是|儲存體百分比|百分比|Average|儲存體百分比|無維度|
|storage_used|是|已使用儲存體|位元組|Average|已使用儲存體|無維度|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|active_connections|是|作用中的連線|Count|Average|作用中的連線|無維度|
|cpu_percent|是|CPU 百分比|百分比|Average|CPU 百分比|無維度|
|iops|是|IOPS|Count|Average|每秒的 IO 作業數|無維度|
|memory_percent|是|記憶體百分比|百分比|Average|記憶體百分比|無維度|
|network_bytes_egress|是|Network Out|位元組|總計|跨作用中連線的網路輸出|無維度|
|network_bytes_ingress|是|Network In|位元組|總計|跨作用中連線的網路輸入|無維度|
|storage_percent|是|儲存體百分比|百分比|Average|儲存體百分比|無維度|
|storage_used|是|已使用儲存體|位元組|Average|已使用儲存體|無維度|


## <a name="microsoftdbforpostgresqlsingleservers"></a>DBforPostgreSQL/singleservers

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|active_connections|是|作用中的連線|Count|Average|作用中的連線|無維度|
|connections_failed|是|失敗的連線|Count|總計|失敗的連線|無維度|
|connections_succeeded|是|成功的連接|Count|總計|成功的連接|無維度|
|cpu_percent|是|CPU 百分比|百分比|Average|CPU 百分比|無維度|
|iops|是|IOPS|計數|Average|每秒的 IO 作業數|無維度|
|maximum_used_transactionIDs|是|使用的交易識別碼上限|計數|Average|使用的交易識別碼上限|無維度|
|memory_percent|是|記憶體百分比|百分比|Average|記憶體百分比|無維度|
|network_bytes_egress|是|Network Out|位元組|總計|跨作用中連線的網路輸出|無維度|
|network_bytes_ingress|是|Network In|位元組|總計|跨作用中連線的網路輸入|無維度|
|storage_percent|是|儲存體百分比|百分比|Average|儲存體百分比|無維度|
|storage_used|是|已使用儲存體|位元組|Average|已使用儲存體|無維度|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|是|已放棄的 C2D 訊息|Count|總計|裝置放棄的雲端到裝置訊息數|無維度|
|c2d.commands.egress.complete.success|是|已完成的 C2D 訊息傳遞|Count|總計|裝置已成功完成的雲端到裝置訊息傳遞數|無維度|
|c2d.commands.egress.reject.success|是|已拒絕的 C2D 訊息|Count|總計|裝置拒絕的雲端到裝置訊息數目|無維度|
|c2d.methods.failure|是|失敗直接方法叫用|Count|總計|所有失敗直接方法呼叫的計數。|無維度|
|c2d.methods.requestSize|是|直接方法叫用的要求大小|位元組|Average|所有成功直接方法要求的平均值、最小值和最大值。|無維度|
|c2d.methods.responseSize|是|直接方法叫用的回應大小|位元組|Average|所有成功直接方法回應的平均值、最小值和最大值。|無維度|
|c2d.methods.success|是|成功直接方法叫用|Count|總計|所有成功直接方法呼叫的計數。|無維度|
|c2d.twin.read.failure|是|後端的失敗對應項讀取|Count|總計|後端起始的所有失敗對應項讀取的計數。|無維度|
|c2d.twin.read.size|是|後端的對應項讀取回應大小|位元組|Average|後端起始的所有成功對應項讀取的平均值、最小值和最大值。|無維度|
|c2d.twin.read.success|是|後端的成功對應項讀取|Count|總計|後端起始的所有成功對應項讀取的計數。|無維度|
|c2d.twin.update.failure|是|後端的失敗對應項更新|Count|總計|後端起始的所有失敗對應項更新的計數。|無維度|
|c2d.twin.update.size|是|後端的對應項更新大小|位元組|Average|後端起始的所有成功對應項更新的大小平均值、最小值和最大值。|無維度|
|c2d.twin.update.success|是|後端的成功對應項更新|Count|總計|後端起始的所有成功對應項更新的計數。|無維度|
|C2DMessagesExpired|是|已過期的 C2D 訊息 (預覽)|Count|總計|已過期的雲端到裝置訊息數目|無維度|
|組態|是|設定計量|Count|總計|設定作業的計量|無維度|
|connectedDeviceCount|否|連接的裝置 (預覽)|計數|Average|連接至 IoT 中樞的裝置數目|無維度|
|d2c.endpoints.egress.builtIn.events|是|路由：傳遞至訊息/事件的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至內建端點 (訊息/事件) 的次數。|無維度|
|d2c.endpoints.egress.eventHubs|是|路由：傳遞至事件中樞的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至事件中樞端點的次數。|無維度|
|d2c.endpoints.egress.serviceBusQueues|是|路由：傳遞至服務匯流排佇列的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排佇列端點的次數。|無維度|
|d2c.endpoints.egress.serviceBusTopics|是|路由：傳遞至服務匯流排主題的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排主題端點的次數。|無維度|
|d2c.endpoints.egress.storage|是|路由：傳遞至儲存體的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至儲存體端點的次數。|無維度|
|d2c.endpoints.egress.storage.blobs|是|路由：傳遞至儲存體的 BLOB|Count|總計|IoT 中樞路由將 BLOB 傳遞至儲存體端點的次數。|無維度|
|d2c.endpoints.egress.storage.bytes|是|路由：傳遞至儲存體的資料|位元組|總計|IoT 中樞路由傳遞至儲存體端點的資料量 (位元組)。|無維度|
|d2c.endpoints.latency.builtIn.events|是|路由：訊息/事件的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到內建端點 (訊息/事件) 之間的平均延遲 (毫秒)。|無維度|
|d2c.endpoints.latency.eventHubs|是|路由：事件中樞的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到事件中樞端點之間的平均延遲 (毫秒)。|無維度|
|d2c.endpoints.latency.serviceBusQueues|是|路由：服務匯流排佇列的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒)。|無維度|
|d2c.endpoints.latency.serviceBusTopics|是|路由：服務匯流排主題的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒)。|無維度|
|d2c.endpoints.latency.storage|是|路由：儲存體的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到儲存體端點之間的平均延遲 (毫秒)。|無維度|
|d2c.telemetry.egress.dropped|是|路由：已捨棄的遙測訊息 |Count|總計|IoT 中樞路由因為端點無效而捨棄訊息的次數。 這個值不會計算傳遞到後援路由的訊息，因為捨棄的訊息不會傳遞到那裡。|無維度|
|d2c.telemetry.egress.fallback|是|路由：傳遞至後援的訊息|Count|總計|IoT 中樞路由傳遞訊息至與後援路由相關聯之端點的次數。|無維度|
|d2c.telemetry.egress.invalid|是|路由：不相容的遙測訊息|Count|總計|IoT 中樞路由因為與端點不相容而無法傳遞訊息的次數。 這個值不包含重試次數。|無維度|
|d2c.telemetry.egress.orphaned|是|路由：已遺棄的遙測訊息 |計數|總計|IoT 中樞路由因為訊息不符合任何路由規則 (包括後援規則) 而遺棄訊息的次數。 |無維度|
|d2c.telemetry.egress.success|是|路由：已傳遞的遙測訊息|計數|總計|使用 IoT 中樞路由成功地將訊息傳遞到所有端點的次數。 如果將訊息路由至多個端點，這個值會為每一次成功傳遞加 1。 如果將訊息多次傳遞到同一個端點，這個值會為每一次成功傳遞加 1。|無維度|
|d2c.telemetry.ingress.allProtocol|是|遙測訊息傳送嘗試|計數|總計|要嘗試傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無維度|
|d2c.telemetry.ingress.sendThrottle|是|節流錯誤數目|計數|總計|因裝置輸送量節流而導致的節流錯誤數目|無維度|
|d2c.telemetry.ingress.success|是|已傳送的遙測訊息|計數|總計|成功傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無維度|
|d2c.twin.read.failure|是|裝置的失敗對應項讀取|計數|總計|裝置起始的所有失敗對應項讀取的計數。|無維度|
|d2c.twin.read.size|是|裝置的對應項讀取回應大小|位元組|Average|裝置起始的所有成功對應項讀取的平均值、最小值和最大值。|無維度|
|d2c.twin.read.success|是|裝置的成功對應項讀取|計數|總計|裝置起始的所有成功對應項讀取的計數。|無維度|
|d2c.twin.update.failure|是|裝置的失敗對應項更新|計數|總計|裝置起始的所有失敗對應項更新的計數。|無維度|
|d2c.twin.update.size|是|裝置的對應項更新大小|位元組|Average|裝置起始的所有成功對應項更新的大小平均值、最小值和最大值。|無維度|
|d2c.twin.update.success|是|裝置的成功對應項更新|計數|總計|裝置起始的所有成功對應項更新的計數。|無維度|
|dailyMessageQuotaUsed|是|已使用的訊息總數|Count|最大值|今日已使用的總訊息數|無維度|
|deviceDataUsage|是|裝置資料使用量總計|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|無維度|
|deviceDataUsageV2|是|裝置資料使用量總計 (預覽)|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|無維度|
|devices.connectedDevices.allProtocol|是|連接的裝置 (已淘汰) |Count|總計|連接至 IoT 中樞的裝置數目|無維度|
|devices.totalDevices|是|裝置總計 (已淘汰)|Count|總計|向 IoT 中樞註冊的裝置數目|無維度|
|EventGridDeliveries|是|事件方格傳遞（預覽）|Count|總計|已發佈至事件方格的 IoT 中樞事件數目。 使用 [結果] 維度來取得成功和失敗的要求數目。 [事件種類] 維度會顯示事件的類型（ https://aka.ms/ioteventgrid) 。|結果，事件|
|EventGridLatency|是|事件方格延遲 (預覽)|毫秒|Average|當事件發佈至事件方格時，從 Iot 中樞事件產生的平均延遲（毫秒）。 這個數位是所有事件種類之間的平均值。 使用 [事件種類] 維度來查看特定事件種類的延遲。|EventType|
|jobs.cancelJob.failure|是|取消作業失敗|Count|總計|所有呼叫取消作業失敗的計數。|無維度|
|jobs.cancelJob.success|是|成功取消作業|Count|總計|所有成功呼叫取消作業的計數。|無維度|
|jobs.completed|是|已完成的工作|Count|總計|所有已完成的作業計數。|無維度|
|jobs.createDirectMethodJob.failure|是|建立失敗的方法叫用作業|Count|總計|所有建立失敗的直接方法叫用作業計數。|無維度|
|jobs.createDirectMethodJob.success|是|成功建立的方法叫用作業|Count|總計|所有成功建立的直接方法叫用作業計數。|無維度|
|jobs.createTwinUpdateJob.failure|是|建立失敗的對應項更新作業|Count|總計|所有建立失敗的對應項更新作業計數。|無維度|
|jobs.createTwinUpdateJob.success|是|成功建立的對應項更新作業|Count|總計|所有成功建立的對應項更新作業計數。|無維度|
|jobs.failed|是|失敗作業|Count|總計|所有失敗作業計數。|無維度|
|jobs.listJobs.failure|是|呼叫列出作業失敗|Count|總計|所有呼叫列出作業失敗的計數。|無維度|
|jobs.listJobs.success|是|成功呼叫列出作業|Count|總計|所有成功呼叫列出作業的計數。|無維度|
|jobs.queryJobs.failure|是|失敗作業查詢|Count|總計|所有呼叫查詢作業失敗的計數。|無維度|
|jobs.queryJobs.success|是|成功作業查詢|Count|總計|所有成功呼叫查詢作業的計數。|無維度|
|RoutingDataSizeInBytesDelivered|是|路由傳遞訊息大小（以位元組為單位）（預覽）|位元組|總計|IoT 中樞傳遞至端點的訊息總大小（以位元組為單位）。 您可以使用端點和 EndpointType 維度來查看傳遞至不同端點的訊息大小（以位元組為單位）。 每個傳遞訊息的度量值都會增加，包括訊息是否傳遞至多個端點，或訊息是否多次傳遞至相同的端點。|EndpointType、端點、RoutingSource|
|RoutingDeliveries|是|路由傳遞（預覽）|Count|總計|IoT 中樞嘗試使用路由將訊息傳遞至所有端點的次數。 若要查看成功或失敗的嘗試次數，請使用 [結果] 維度。 若要查看失敗的原因，例如 [無效]、[已捨棄] 或 [孤立]，請使用 [FailureReasonCategory] 維度。 您也可以使用端點和 EndpointType 維度來瞭解有多少訊息傳遞至不同的端點。 計量值會針對每個傳遞嘗試增加一個，包括訊息是否傳遞至多個端點，或訊息是否多次傳遞至相同的端點。|EndpointType、端點、FailureReasonCategory、Result、RoutingSource|
|RoutingDeliveryLatency|是|路由傳遞延遲（預覽）|毫秒|Average|訊息輸入到 IoT 中樞的平均延遲（毫秒）和遙測訊息輸入到端點。 您可以使用端點和 EndpointType 維度來瞭解不同端點的延遲。|EndpointType、端點、RoutingSource|
|totalDeviceCount|否|裝置總計 (預覽)|計數|Average|向 IoT 中樞註冊的裝置數目|無維度|
|twinQueries.failure|是|失敗對應項查詢|Count|總計|所有失敗對應項查詢的計數。|無維度|
|twinQueries.resultSize|是|對應項查詢結果大小|位元組|Average|所有成功對應項查詢的結果大小平均值、最小值和最大值。|無維度|
|twinQueries.success|是|成功對應項查詢|Count|總計|所有成功對應項查詢的計數。|無維度|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AttestationAttempts|是|證明嘗試數|Count|總計|嘗試的裝置證明數|ProvisioningServiceName，狀態，通訊協定|
|DeviceAssignments|是|已指派的裝置數|Count|總計|已指派到 IoT 中樞的裝置數|ProvisioningServiceName，IotHubName|
|RegistrationAttempts|是|註冊嘗試數|Count|總計|嘗試的裝置註冊數|ProvisioningServiceName，IotHubName，狀態|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AddRegion|是|已新增區域|Count|Count|已新增區域|區域|
|AutoscaleMaxThroughput|否|自動調整最大輸送量|計數|最大值|自動調整最大輸送量|DatabaseName、CollectionName|
|AvailableStorage|否|不再可用的儲存體|位元組|總計|「可用的存放裝置」將會從2020年9月底的 Azure 監視器中移除。 Cosmos DB 集合儲存體大小現在不受限制。 唯一的限制是每個邏輯分割區索引鍵的儲存體大小為 20 gb。 您可以在診斷記錄中啟用 PartitionKeyStatistics，以瞭解前幾個分割區索引鍵的儲存體耗用量。 如需 Cosmos DB 儲存體配額的詳細資訊，請參閱此檔 https://docs.microsoft.com/azure/cosmos-db/concepts-limits 。 淘汰之後，仍然在已被取代的計量上定義的其餘警示規則將會在淘汰日期後自動停用。|CollectionName、DatabaseName、Region|
|CassandraConnectionClosures|否|Cassandra 連線終止|計數|總計|已關閉的 Cassandra 連接數，以1分鐘的資料細微性回報|Region、ClosureReason|
|CassandraConnectorAvgReplicationLatency|否|Cassandra 連接器平均 ReplicationLatency|毫秒|Average|Cassandra 連接器平均 ReplicationLatency|無維度|
|CassandraConnectorReplicationHealthStatus|否|Cassandra 連接器複寫健全狀況狀態|Count|Count|Cassandra 連接器複寫健全狀況狀態|NotStarted、ReplicationInProgress、Error|
|CassandraKeyspaceCreate|否|已建立 Cassandra Keyspace|Count|Count|已建立 Cassandra Keyspace|ResourceName |
|CassandraKeyspaceDelete|否|已刪除 Cassandra Keyspace|Count|Count|已刪除 Cassandra Keyspace|ResourceName |
|CassandraKeyspaceThroughputUpdate|否|已更新 Cassandra Keyspace 輸送量|Count|Count|已更新 Cassandra Keyspace 輸送量|ResourceName |
|CassandraKeyspaceUpdate|否|已更新 Cassandra Keyspace|Count|Count|已更新 Cassandra Keyspace|ResourceName |
|CassandraRequestCharges|否|Cassandra 要求費用|計數|總計|針對提出的 Cassandra 要求所耗用的 ru|DatabaseName、CollectionName、Region、OperationType、ResourceType|
|CassandraRequests|否|Cassandra 要求|Count|Count|提出的 Cassandra 要求數目|DatabaseName、CollectionName、Region、OperationType、ResourceType、ErrorCode|
|CassandraTableCreate|否|已建立 Cassandra 資料表|Count|計數|已建立 Cassandra 資料表|ChildResourceName、 |
|CassandraTableDelete|否|已刪除 Cassandra 資料表|Count|計數|已刪除 Cassandra 資料表|ChildResourceName、 |
|CassandraTableThroughputUpdate|否|已更新 Cassandra 資料表輸送量|Count|計數|已更新 Cassandra 資料表輸送量|ChildResourceName、 |
|CassandraTableUpdate|否|Cassandra 資料表已更新|Count|計數|Cassandra 資料表已更新|ChildResourceName、 |
|CreateAccount|是|帳戶已建立|Count|計數|帳戶已建立|無維度|
|DataUsage|否|資料使用量|位元組|總計|5分鐘資料細微性報告的總數據使用量|CollectionName、DatabaseName、Region|
|DeleteAccount|是|帳戶已刪除|Count|計數|帳戶已刪除|無維度|
|DocumentCount|否|文件計數|Count|總計|在5分鐘資料細微性報告的檔計數總計|CollectionName、DatabaseName、Region|
|DocumentQuota|否|文件配額|位元組|總計|5分鐘資料細微性報告的總儲存體配額|CollectionName、DatabaseName、Region|
|GremlinDatabaseCreate|否|已建立 Gremlin 資料庫|Count|計數|已建立 Gremlin 資料庫|ResourceName |
|GremlinDatabaseDelete|否|已刪除 Gremlin 資料庫|Count|計數|已刪除 Gremlin 資料庫|ResourceName |
|GremlinDatabaseThroughputUpdate|否|已更新 Gremlin 資料庫輸送量|Count|計數|已更新 Gremlin 資料庫輸送量|ResourceName |
|GremlinDatabaseUpdate|否|已更新 Gremlin 資料庫|Count|計數|已更新 Gremlin 資料庫|ResourceName |
|GremlinGraphCreate|否|已建立 Gremlin 圖形|Count|計數|已建立 Gremlin 圖形|ChildResourceName、 |
|GremlinGraphDelete|否|已刪除 Gremlin 圖形|Count|計數|已刪除 Gremlin 圖形|ChildResourceName、 |
|GremlinGraphThroughputUpdate|否|已更新 Gremlin 圖形輸送量|Count|計數|已更新 Gremlin 圖形輸送量|ChildResourceName、 |
|GremlinGraphUpdate|否|Gremlin 圖已更新|Count|計數|Gremlin 圖已更新|ChildResourceName、 |
|IndexUsage|否|索引使用量|位元組|總計|5分鐘資料細微性報告的索引使用量總計|CollectionName、DatabaseName、Region|
|MetadataRequests|否|中繼資料要求|Count|計數|中繼資料要求計數。 Cosmos DB 會維護每個帳戶的系統中繼資料集合，可讓您免費列舉集合、資料庫等及其設定。|DatabaseName、CollectionName、Region、StatusCode、 |
|MongoCollectionCreate|否|已建立 Mongo 收集|Count|計數|已建立 Mongo 收集|ChildResourceName、 |
|MongoCollectionDelete|否|已刪除 Mongo 集合|Count|計數|已刪除 Mongo 集合|ChildResourceName、 |
|MongoCollectionThroughputUpdate|否|已更新 Mongo 集合輸送量|Count|計數|已更新 Mongo 集合輸送量|ChildResourceName、 |
|MongoCollectionUpdate|否|已更新 Mongo 集合|Count|計數|已更新 Mongo 集合|ChildResourceName、 |
|MongoDatabaseDelete|否|已刪除 Mongo 資料庫|Count|計數|已刪除 Mongo 資料庫|ResourceName |
|MongoDatabaseThroughputUpdate|否|已更新 Mongo 資料庫輸送量|Count|計數|已更新 Mongo 資料庫輸送量|ResourceName |
|MongoDBDatabaseCreate|否|已建立 Mongo 資料庫|Count|計數|已建立 Mongo 資料庫|ResourceName |
|MongoDBDatabaseUpdate|否|已更新 Mongo 資料庫|Count|計數|已更新 Mongo 資料庫|ResourceName |
|MongoRequestCharge|是|Mongo 要求收費|計數|總計|已使用的 Mongo 要求單位|DatabaseName、CollectionName、Region、CommandName、ErrorCode、Status|
|MongoRequests|是|Mongo 要求|Count|計數|已提出的 Mongo 要求數目|DatabaseName、CollectionName、Region、CommandName、ErrorCode、Status|
|MongoRequestsCount|否|Mongo 要求率|每秒計數|Average|每秒的 Mongo 要求計數|DatabaseName、CollectionName、Region、ErrorCode|
|MongoRequestsDelete|否|Mongo 刪除要求率|每秒計數|Average|每秒的 Mongo 刪除要求數|DatabaseName、CollectionName、Region、ErrorCode|
|MongoRequestsInsert|否|Mongo 插入要求率|每秒計數|Average|每秒的 Mongo 插入計數|DatabaseName、CollectionName、Region、ErrorCode|
|MongoRequestsQuery|否|Mongo 查詢要求率|每秒計數|Average|每秒的 Mongo 查詢要求數|DatabaseName、CollectionName、Region、ErrorCode|
|MongoRequestsUpdate|否|Mongo 更新要求率|每秒計數|Average|每秒的 Mongo 更新要求數|DatabaseName、CollectionName、Region、ErrorCode|
|NormalizedRUConsumption|否|正規化 RU 耗用量|百分比|最大值|每分鐘的最大 RU 耗用量百分比|CollectionName、DatabaseName、Region、PartitionKeyRangeId|
|ProvisionedThroughput|否|佈建的輸送量|Count|最大值|佈建的輸送量|DatabaseName、CollectionName|
|RegionFailover|是|區域已故障切換|Count|計數|區域已故障切換|無維度|
|RemoveRegion|是|區域已移除|Count|計數|區域已移除|區域|
|ReplicationLatency|是|P99 複寫延遲|毫秒|Average|異地複寫啟用的帳戶其跨來源和目標區域的 P99 複寫延遲|SourceRegion、TargetRegion|
|ServerSideLatency|否|伺服器端延遲|毫秒|Average|伺服器端延遲|DatabaseName、CollectionName、Region、ConnectionMode、OperationType、PublicAPIType|
|ServiceAvailability|否|服務可用性|百分比|Average|帳戶要求可用性為一小時、一天或每月的資料細微性|無維度|
|SqlContainerCreate|否|已建立 Sql 容器|Count|計數|已建立 Sql 容器|ChildResourceName、 |
|SqlContainerDelete|否|Sql 容器已刪除|Count|計數|Sql 容器已刪除|ChildResourceName、 |
|SqlContainerThroughputUpdate|否|Sql 容器輸送量已更新|Count|計數|Sql 容器輸送量已更新|ChildResourceName、 |
|SqlContainerUpdate|否|已更新 Sql 容器|Count|計數|已更新 Sql 容器|ChildResourceName、 |
|SqlDatabaseCreate|否|Sql Database 已建立|Count|計數|Sql Database 已建立|ResourceName |
|SqlDatabaseDelete|否|Sql Database 已刪除|Count|計數|Sql Database 已刪除|ResourceName |
|SqlDatabaseThroughputUpdate|否|Sql 資料庫輸送量已更新|Count|計數|Sql 資料庫輸送量已更新|ResourceName |
|SqlDatabaseUpdate|否|Sql Database 已更新|Count|計數|Sql Database 已更新|ResourceName |
|TableTableCreate|否|已建立 AzureTable 資料表|Count|計數|已建立 AzureTable 資料表|ResourceName |
|TableTableDelete|否|已刪除 AzureTable 資料表|Count|計數|已刪除 AzureTable 資料表|ResourceName |
|TableTableThroughputUpdate|否|已更新 AzureTable 資料表輸送量|Count|計數|已更新 AzureTable 資料表輸送量|ResourceName |
|TableTableUpdate|否|AzureTable 資料表已更新|Count|計數|AzureTable 資料表已更新|ResourceName |
|TotalRequests|是|要求總數|Count|計數|進行的要求數目|DatabaseName、CollectionName、Region、StatusCode、OperationType、Status|
|TotalRequestUnits|是|要求單位總計|Count|總計|已使用的要求單位|DatabaseName、CollectionName、Region、StatusCode、OperationType、Status|
|UpdateAccountKeys|是|帳戶金鑰已更新|Count|計數|帳戶金鑰已更新|KeyType|
|UpdateAccountNetworkSettings|是|已更新帳戶網路設定|Count|計數|已更新帳戶網路設定|無維度|
|UpdateAccountReplicationSettings|是|已更新帳戶複寫設定|Count|計數|已更新帳戶複寫設定|無維度|
|UpdateDiagnosticsSettings|否|已更新帳戶診斷設定|Count|計數|已更新帳戶診斷設定|DiagnosticSettingsName、ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DeadLetterReason|
|DeliveryAttemptFailCount|否|傳遞失敗的事件|計數|總計|傳遞到此事件訂閱失敗的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、Error、ErrorType|
|DeliverySuccessCount|是|已傳遞的事件|計數|總計|已傳遞到此事件訂閱的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|否|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DroppedEventCount|是|卸除的事件|計數|總計|與此事件訂閱相符的已卸除事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DropReason|
|MatchedEventCount|是|相符的事件|計數|總計|與此事件訂閱相符的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|PublishFailCount|是|發行失敗的事件|計數|總計|發佈至本主題失敗的事件總數|主題、ErrorType、錯誤|
|PublishSuccessCount|是|已發佈的事件|Count|總計|發佈至本主題的事件總數|主題|
|PublishSuccessLatencyInMs|是|發行成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無維度|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason|
|DeliveryAttemptFailCount|否|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType|
|DeliverySuccessCount|是|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|無維度|
|DestinationProcessingDurationInMs|否|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|無維度|
|DroppedEventCount|是|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|DropReason|
|MatchedEventCount|是|相符的事件|Count|總計|與此事件訂閱相符的事件總數|無維度|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|PublishFailCount|是|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|PublishSuccessCount|是|已發佈的事件|Count|總計|發佈至本主題的事件總數|無維度|
|PublishSuccessLatencyInMs|是|發行成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無維度|
|UnmatchedEventCount|是|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|無維度|


## <a name="microsofteventgridsystemtopics"></a>EventGrid/systemTopics

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|否|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|是|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|否|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|是|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|DropReason, EventSubscriptionName|
|MatchedEventCount|是|相符的事件|Count|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|PublishFailCount|是|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|PublishSuccessCount|是|已發佈的事件|Count|總計|發佈至本主題的事件總數|無維度|
|PublishSuccessLatencyInMs|是|發行成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無維度|
|UnmatchedEventCount|是|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|無維度|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|否|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|是|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|否|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|是|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|DropReason, EventSubscriptionName|
|MatchedEventCount|是|相符的事件|Count|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|PublishFailCount|是|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|PublishSuccessCount|是|已發佈的事件|Count|總計|發佈至本主題的事件總數|無維度|
|PublishSuccessLatencyInMs|是|發行成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無維度|
|UnmatchedEventCount|是|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|無維度|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ActiveConnections|否|ActiveConnections|計數|Average|Microsoft.EventHub 的使用中連線總數。|無維度|
|AvailableMemory|否|可用的記憶體|百分比|最大值|事件中樞叢集的可用記憶體（以總記憶體的百分比表示）。|角色|
|CaptureBacklog|否|擷取待辦項目。|Count|總計|Microsoft.EventHub 的擷取待辦項目。|無維度|
|CapturedBytes|否|已擷取的位元組。|位元組|總計|Microsoft.EventHub 的已擷取位元組。|無維度|
|CapturedMessages|否|已擷取的訊息。|Count|總計|Microsoft.EventHub 的已擷取訊息。|無維度|
|ConnectionsClosed|否|已關閉的連線。|計數|Average|Microsoft.EventHub 的已關閉連線。|無維度|
|ConnectionsOpened|否|已開啟的連線。|計數|Average|Microsoft.EventHub 的已開啟連線。|無維度|
|CPU|否|CPU|百分比|最大值|事件中樞叢集的 CPU 使用率 (百分比)|角色|
|IncomingBytes|是|傳入位元組數。|位元組|總計|Microsoft.EventHub 的傳入位元組數。|無維度|
|IncomingMessages|是|傳入訊息|Count|總計|Microsoft.EventHub 的傳入訊息。|無維度|
|IncomingRequests|是|傳入的要求|Count|總計|Microsoft.EventHub 的傳入要求。|無維度|
|OutgoingBytes|是|傳出位元組數。|位元組|總計|Microsoft.EventHub 的傳出位元組數。|無維度|
|OutgoingMessages|是|外送訊息|Count|總計|Microsoft.EventHub 的傳出訊息。|無維度|
|QuotaExceededErrors|否|超出配額的錯誤。|Count|總計|Microsoft.EventHub 超出配額的錯誤。|無維度|
|ServerErrors|否|伺服器錯誤。|Count|總計|Microsoft.EventHub 的伺服器錯誤。|無維度|
|Size|否|大小|位元組|Average|EventHub 的大小 (以位元組為單位)。|角色|
|SuccessfulRequests|否|成功的要求|Count|總計|Microsoft.EventHub 的成功要求。|無維度|
|ThrottledRequests|否|節流的要求。|Count|總計|Microsoft.EventHub 的節流要求。|無維度|
|UserErrors|否|使用者錯誤。|Count|總計|Microsoft.EventHub 的使用者錯誤。|無維度|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ActiveConnections|否|ActiveConnections|計數|Average|Microsoft.EventHub 的使用中連線總數。|無維度|
|CaptureBacklog|否|擷取待辦項目。|Count|總計|Microsoft.EventHub 的擷取待辦項目。|EntityName|
|CapturedBytes|否|已擷取的位元組。|位元組|總計|Microsoft.EventHub 的已擷取位元組。|EntityName|
|CapturedMessages|否|已擷取的訊息。|Count|總計|Microsoft.EventHub 的已擷取訊息。|EntityName|
|ConnectionsClosed|否|已關閉的連線。|計數|Average|Microsoft.EventHub 的已關閉連線。|EntityName|
|ConnectionsOpened|否|已開啟的連線。|計數|Average|Microsoft.EventHub 的已開啟連線。|EntityName|
|EHABL|是|封存待處理項目訊息 (已淘汰)|Count|總計|命名空間的待處理專案中的事件中樞封存訊息（已淘汰）|無維度|
|EHAMBS|是|封存訊息輸送量 (已淘汰)|位元組|總計|命名空間中的事件中樞封存訊息輸送量（已淘汰）|無維度|
|EHAMSGS|是|封存訊息 (已淘汰)|Count|總計|命名空間中的事件中樞封存訊息（已淘汰）|無維度|
|EHINBYTES|是|傳入位元組數 (已過時)|位元組|總計|命名空間的事件中樞傳入訊息輸送量（已淘汰）|無維度|
|EHINMBS|是|傳入位元組數 (過時) (已淘汰)|位元組|總計|命名空間的事件中樞傳入訊息輸送量。 此計量已過時。 請改用傳入位元組度量（已淘汰）|無維度|
|EHINMSGS|是|連入訊息數 (已過時)|Count|總計|命名空間的傳入訊息總計（已淘汰）|無維度|
|EHOUTBYTES|是|傳出位元組數 (已過時)|位元組|總計|命名空間的事件中樞傳出訊息輸送量（已淘汰）|無維度|
|EHOUTMBS|是|傳出位元組數 (過時) (已淘汰)|位元組|總計|命名空間的事件中樞傳出訊息輸送量。 此計量已過時。 請改用傳出位元組計量（已淘汰）|無維度|
|EHOUTMSGS|是|傳出訊息數 (已過時)|Count|總計|命名空間的傳出訊息總計（已淘汰）|無維度|
|FAILREQ|是|失敗的要求 (已淘汰)|Count|總計|命名空間的失敗要求總計（已淘汰）|無維度|
|IncomingBytes|是|傳入位元組數。|位元組|總計|Microsoft.EventHub 的傳入位元組數。|EntityName|
|IncomingMessages|是|傳入訊息|Count|總計|Microsoft.EventHub 的傳入訊息。|EntityName|
|IncomingRequests|是|傳入的要求|Count|總計|Microsoft.EventHub 的傳入要求。|EntityName|
|INMSGS|是|傳入的訊息 (過時) (已淘汰)|Count|總計|命名空間的傳入訊息總數。 此計量已過時。 請改用傳入訊息計量（已淘汰）|無維度|
|INREQS|是|傳入的要求 (已淘汰)|Count|總計|命名空間的連入傳送要求總數（已淘汰）|無維度|
|INTERR|是|內部伺服器錯誤 (已淘汰)|Count|總計|命名空間的內部伺服器錯誤總數（已淘汰）|無維度|
|MISCERR|是|其他錯誤 (已淘汰)|Count|總計|命名空間的失敗要求總計（已淘汰）|無維度|
|OutgoingBytes|是|傳出位元組數。|位元組|總計|Microsoft.EventHub 的傳出位元組數。|EntityName|
|OutgoingMessages|是|外送訊息|Count|總計|Microsoft.EventHub 的傳出訊息。|EntityName|
|OUTMSGS|是|傳出的訊息 (過時) (已淘汰)|Count|總計|命名空間的傳出訊息總數。 此計量已過時。 請改用外寄訊息度量（已淘汰）|無維度|
|QuotaExceededErrors|否|超出配額的錯誤。|Count|總計|Microsoft.EventHub 超出配額的錯誤。|EntityName |
|ServerErrors|否|伺服器錯誤。|Count|總計|Microsoft.EventHub 的伺服器錯誤。|EntityName |
|Size|否|大小|位元組|Average|EventHub 的大小 (以位元組為單位)。|EntityName|
|SuccessfulRequests|否|成功的要求|Count|總計|Microsoft.EventHub 的成功要求。|EntityName |
|SUCCREQ|是|成功的要求 (已淘汰)|Count|總計|命名空間的成功要求總數（已淘汰）|無維度|
|SVRBSY|是|伺服器忙線錯誤 (已淘汰)|計數|總計|命名空間的伺服器忙碌錯誤總計（已淘汰）|無維度|
|ThrottledRequests|否|節流的要求。|計數|總計|Microsoft.EventHub 的節流要求。|EntityName |
|UserErrors|否|使用者錯誤。|計數|總計|Microsoft.EventHub 的使用者錯誤。|EntityName |


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|是|已分類的閘道要求數|計數|總計|依類別 (1xx/2xx/3xx/4xx/5xx) 區分的閘道要求數目|HttpStatus|
|GatewayRequests|是|閘道要求數|計數|總計|閘道器要求數目|HttpStatus|
|NumActiveWorkers|是|作用中背景工作數目|Count|最大值|作用中背景工作數目|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|MetricThreshold|是|計量閾值|計數|Average|當自動調整規模執行時設定的自動調整規模閾值。|MetricTriggerRule|
|ObservedCapacity|是|觀察的容量|計數|Average|執行時向自動調整規模回報的容量。|無維度|
|ObservedMetricValue|是|觀察的計量值|計數|Average|執行時由自動調整規模計算的值|MetricTriggerSource|
|ScaleActionsInitiated|是|已起始的調整規模動作數|Count|總計|調整規模作業的方向。|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|是|可用性|百分比|Average|已成功完成可用性測試的百分比|availabilityResult/name、availabilityResult/location|
|availabilityResults/count|否|可用性集合|Count|計數|可用性測試計數|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|availabilityResults/duration|是|可用性測試持續期間|毫秒|Average|可用性測試持續期間|availabilityResult/name、availabilityResult/location、availabilityResult/success|
|browserTimings/networkDuration|是|頁面載入網路連線時間|毫秒|Average|介於使用者要求和網路連線之間的時間。 包含 DNS 查閱和傳輸連接。|無維度|
|browserTimings/processingDuration|是|用戶端處理時間|毫秒|Average|從接收上個文件位元組直至載入 DOM 的經過時間。 系統可能仍在處理非同步要求。|無維度|
|browserTimings/receiveDuration|是|接收回應時間|毫秒|Average|介於第一個位元組和最後一個位元組 (或直到中斷連線) 之間的時間。|無維度|
|browserTimings/sendDuration|是|傳送要求時間|毫秒|Average|介於網路連線和接收第一個位元組之間的時間。|無維度|
|browserTimings/totalDuration|是|瀏覽器頁面載入時間|毫秒|Average|從使用者要求直至載入 DOM、樣式表、指令碼和影像的經過時間。|無維度|
|dependencies/count|否|相依性呼叫|Count|計數|應用程式對外部資源的呼叫計數。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、dependency/resultCode、operation/綜合、雲端/roleInstance、雲端/擁有項|
|dependencies/duration|是|相依性持續時間|毫秒|Average|應用程式對外部資源的呼叫持續時間。|dependency/type、dependency/performanceBucket、dependency/success、dependency/target、dependency/resultCode、operation/綜合、雲端/roleInstance、雲端/擁有項|
|dependencies/failed|否|相依性呼叫失敗|Count|計數|應用程式對外部資源進行相依性呼叫的失敗計數。|dependency/type、dependency/performanceBucket、dependency/target、dependency/resultCode、operation/綜合、cloud/roleInstance、cloud/dependency|
|exceptions/browser|否|瀏覽器例外狀況|Count|計數|在瀏覽器中擲回的未攔截例外狀況計數。|雲端/擁有|
|exceptions/count|是|例外狀況|Count|計數|所有未攔截的例外狀況合併計數。|cloud/roleName、cloud/roleInstance、client/type|
|exceptions/server|否|伺服器例外狀況|Count|計數|伺服器應用程式中擲回但未攔截的例外狀況計數。|cloud/roleName、cloud/roleInstance|
|pageViews/count|是|頁面檢視|Count|計數|頁面檢視計數。|作業/綜合、雲端/擁有|
|pageViews/duration|是|頁面檢視載入時間|毫秒|Average|頁面檢視載入時間|作業/綜合、雲端/擁有|
|performanceCounters/exceptionsPerSecond|是|例外狀況比率|每秒計數|Average|回報給 Windows 的已處理與未處理例外狀況計數，包括 .NET 例外狀況以及轉換成 .NET 例外狀況的非受控例外狀況。|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|是|可用的記憶體|位元組|Average|可立即用於配置給處理序或系統使用的實體記憶體。|cloud/roleInstance|
|performanceCounters/processCpuPercentage|是|處理程序 CPU|百分比|Average|所有處理序執行緒使用處理器執行指令已耗用時間的百分比。 差異可介於 0 到 100 之間。 這個計量只表示 w3wp 處理序的效能。|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|是|處理程序 IO 速率|每秒位元組|Average|每秒讀取與寫入檔案、 網路和裝置的總位元組數。|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|是|處理器時間|百分比|Average|處理器針對非閒置執行緒所耗費時間的百分比。|cloud/roleInstance|
|performanceCounters/processPrivateBytes|是|處理程序私人位元組|位元組|Average|以獨佔方式指派至監視應用程式處理程序的記憶體。|cloud/roleInstance|
|performanceCounters/requestExecutionTime|是|HTTP 要求執行時間|毫秒|Average|最近要求的執行時間。|cloud/roleInstance|
|performanceCounters/requestsInQueue|是|應用程式佇列中的 HTTP 要求數|計數|Average|應用程式要求佇列的長度。|cloud/roleInstance|
|performanceCounters/requestsPerSecond|是|HTTP 要求率|每秒計數|Average|每秒從 ASP.NET 發出所有應用程式要求的速率。|cloud/roleInstance|
|requests/count|否|伺服器要求|Count|計數|已完成 HTTP 要求的計數。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|requests/duration|是|伺服器回應時間|毫秒|Average|從接收 HTTP 要求直至完成傳送回應的經過時間。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|requests/failed|否|失敗的要求|Count|計數|標示為失敗的 HTTP 要求計數。 在大多數情況下，這些要求的回應碼會 >= 400，且不等於 401。|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、cloud/roleName|
|要求/速率|否|伺服器要求率|每秒計數|Average|每秒伺服器要求的速率|request/performanceBucket、request/resultCode、operation/synthetic、cloud/roleInstance、request/success、cloud/roleName|
|traces/count|是|追蹤|Count|計數|追蹤文件計數|trace/severityLevel、operation/synthetic、cloud/roleName、cloud/roleInstance|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|整體保存庫可用性|百分比|Average|保存庫要求可用性|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|SaturationShoebox|否|整體保存庫飽和度|百分比|Average|使用的保存庫容量|ActivityType、ActivityName、TransactionType|
|ServiceApiHit|是|服務 API 點擊次數總計|Count|計數|服務 API 點擊次數總數|ActivityType、ActivityName|
|ServiceApiLatency|是|整體服務 API 延遲|毫秒|Average|服務 API 要求的整體延遲|ActivityType、ActivityName、StatusCode、StatusCodeClass|
|ServiceApiResult|是|服務 API 結果總計|Count|計數|服務 API 結果總數|ActivityType、ActivityName、StatusCode、StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|BatchBlobCount|是|Batch Blob 計數|計數|Average|用於內嵌的匯總批次中的資料來源數目。|資料庫|
|BatchDuration|是|批次持續時間|秒|Average|內嵌流程中匯總階段的持續時間。|資料庫|
|BatchesProcessed|是|已處理的批次|計數|Average|針對內嵌而匯總的批次數目。 批次完成原因：批次是否達到批次處理時間、資料大小或批次處理原則所設定的檔案數目限制|Database、SealReason|
|BatchSize|是|批次大小|位元組|Average|在內嵌的匯總批次中，未壓縮的預期資料大小。|資料庫|
|CacheUtilization|是|快取使用率|百分比|Average|在叢集範圍的使用率等級|無維度|
|ContinuousExportMaxLatenessMinutes|是|連續匯出最大延遲|計數|最大值|叢集中的連續匯出作業所報告的延遲（以分鐘為單位）|無維度|
|ContinuousExportNumOfRecordsExported|是|連續匯出–匯出記錄的數目|計數|總計|針對匯出作業期間寫入的每個儲存體成品所匯出的記錄數目|ContinuousExportName，資料庫|
|ContinuousExportPendingCount|是|連續匯出擱置計數|計數|最大值|準備好執行的暫止連續匯出作業數|無維度|
|ContinuousExportResult|是|連續匯出結果|計數|計數|指出連續匯出是否成功或失敗|ContinuousExportName，結果，資料庫|
|CPU|是|CPU|百分比|Average|CPU 使用率等級|無維度|
|EventsProcessedForEventHubs|是|已處理的事件 (針對事件/IoT 中樞)|計數|總計|從事件/IoT 中樞內嵌時，由叢集處理的事件數目|EventStatus|
|ExportUtilization|是|匯出使用率|百分比|最大值|匯出使用率|無維度|
|IngestionLatencyInSeconds|是|擷取延遲 (以秒為單位)|秒|Average|從來源 (例如，訊息位於 EventHub 中) 擷取到叢集的擷取時間，以秒為單位|無維度|
|IngestionResult|是|擷取結果|計數|計數|擷取作業數|IngestionResultDetails|
|IngestionUtilization|是|擷取使用率|百分比|Average|在叢集中使用的擷取位置的比率|無維度|
|IngestionVolumeInMB|是|擷取量 (以 MB 為單位)|計數|總計|擷取到叢集的資料總量 (以 MB 為單位)|無維度|
|InstanceCount|是|執行個體計數|計數|Average|總實例計數|無維度|
|KeepAlive|是|保持運作|計數|Average|例行性檢查指出對查詢的叢集回應|無維度|
|QueryDuration|是|查詢持續時間|毫秒|Average|查詢持續時間 (以秒為單位)|QueryStatus|
|SteamingIngestRequestRate|是|串流內嵌要求率|Count|RateRequestsPerSecond|串流內嵌要求速率（每秒的要求數）|無維度|
|StreamingIngestDataRate|是|串流內嵌資料速率|計數|Average|串流內嵌資料速率（每秒 MB）|無維度|
|StreamingIngestDuration|是|串流內嵌持續時間|毫秒|Average|串流內嵌持續時間（以毫秒為單位）|無維度|
|StreamingIngestResults|是|串流內嵌結果|計數|Average|串流內嵌結果|結果|
|TotalNumberOfConcurrentQueries|是|並行查詢的總數|Count|總計|並行查詢的總數|無維度|
|TotalNumberOfExtents|是|範圍總數|Count|總計|資料範圍總數|無維度|
|TotalNumberOfThrottledCommands|是|已節流的命令總數|Count|總計|已節流的命令總數|CommandType|
|TotalNumberOfThrottledQueries|是|已節流的查詢總數|Count|總計|已節流的查詢總數|無維度|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ActionLatency|是|動作延遲 |秒|Average|已完成的工作流程動作延遲。|無維度|
|ActionsCompleted|是|完成的動作 |Count|總計|已完成的工作流程動作數目。|無維度|
|ActionsFailed|是|動作的失敗 |Count|總計|已失敗的工作流程動作數目。|無維度|
|ActionsSkipped|是|略過的動作 |Count|總計|已略過的工作流程動作數目。|無維度|
|ActionsStarted|是|啟動的動作 |Count|總計|已啟動的工作流程動作數目。|無維度|
|ActionsSucceeded|是|成功的動作 |Count|總計|已成功的工作流程動作數目。|無維度|
|ActionSuccessLatency|是|動作成功延遲 |秒|Average|已成功的工作流程動作延遲。|無維度|
|ActionThrottledEvents|是|動作節流的事件|Count|總計|工作流程動作已節流的事件數目。|無維度|
|IntegrationServiceEnvironmentConnectorMemoryUsage|是|整合服務環境的連接器記憶體使用量|百分比|Average|整合服務環境的連接器記憶體使用量。|無維度|
|IntegrationServiceEnvironmentConnectorProcessorUsage|是|整合服務環境的連接器處理器使用量|百分比|Average|整合服務環境的連接器處理器使用量。|無維度|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|是|整合服務環境的工作流程記憶體使用量|百分比|Average|整合服務環境的工作流程記憶體使用量。|無維度|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|是|整合服務環境的工作流程處理器使用量|百分比|Average|整合服務環境的工作流程處理器使用量。|無維度|
|RunFailurePercentage|是|執行失敗百分比|百分比|總計|已失敗的工作流程執行百分比。|無維度|
|RunLatency|是|執行延遲|秒|Average|已完成的工作流程執行延遲。|無維度|
|RunsCancelled|是|取消的執行|Count|總計|已取消的工作流程執行數目。|無維度|
|RunsCompleted|是|完成的執行|Count|總計|已完成的工作流程執行數目。|無維度|
|RunsFailed|是|失敗的執行|Count|總計|已失敗的工作流程執行數目。|無維度|
|RunsStarted|是|執行已啟動|Count|總計|已啟動的工作流程執行數目。|無維度|
|RunsSucceeded|是|已成功的執行|Count|總計|已成功的工作流程執行數目。|無維度|
|RunStartThrottledEvents|是|執行開始節流處理事件|Count|總計|工作流程執行開始節流事件的數目。|無維度|
|RunSuccessLatency|是|執行成功的延遲|秒|Average|已成功的工作流程執行延遲。|無維度|
|RunThrottledEvents|是|執行節流的事件|Count|總計|工作流程動作或觸發節流的事件數目。|無維度|
|TriggerFireLatency|是|觸發程序引發延遲 |秒|Average|已引發的工作流程觸發程序延遲。|無維度|
|TriggerLatency|是|觸發程序延遲 |秒|Average|已完成的工作流程觸發程序延遲。|無維度|
|TriggersCompleted|是|完成的觸發程序 |Count|總計|已完成的工作流程觸發程序數目。|無維度|
|TriggersFailed|是|失敗的觸發程序 |Count|總計|已失敗的工作流程觸發程序數目。|無維度|
|TriggersFired|是|引發的觸發程序 |Count|總計|已引發的工作流程觸發程序數目。|無維度|
|TriggersSkipped|是|略過的觸發程序|Count|總計|已略過的工作流程觸發程序數目。|無維度|
|TriggersStarted|是|啟動的觸發程序 |Count|總計|已啟動的工作流程觸發程序數目。|無維度|
|TriggersSucceeded|是|成功的觸發程序 |Count|總計|已成功的工作流程觸發程序數目。|無維度|
|TriggerSuccessLatency|是|觸發程序成功延遲 |秒|Average|已成功的工作流程觸發程序延遲。|無維度|
|TriggerThrottledEvents|是|觸發程序節流的事件|Count|總計|工作流程觸發程序節流的事件數目。|無維度|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ActionLatency|是|動作延遲 |秒|Average|已完成的工作流程動作延遲。|無維度|
|ActionsCompleted|是|完成的動作 |Count|總計|已完成的工作流程動作數目。|無維度|
|ActionsFailed|是|動作的失敗 |Count|總計|已失敗的工作流程動作數目。|無維度|
|ActionsSkipped|是|略過的動作 |Count|總計|已略過的工作流程動作數目。|無維度|
|ActionsStarted|是|啟動的動作 |Count|總計|已啟動的工作流程動作數目。|無維度|
|ActionsSucceeded|是|成功的動作 |Count|總計|已成功的工作流程動作數目。|無維度|
|ActionSuccessLatency|是|動作成功延遲 |秒|Average|已成功的工作流程動作延遲。|無維度|
|ActionThrottledEvents|是|動作節流的事件|Count|總計|工作流程動作已節流的事件數目。|無維度|
|BillableActionExecutions|是|可計費的動作執行|Count|總計|計費的工作流程動作執行數目。|無維度|
|BillableTriggerExecutions|是|可計費的觸發程序執行|Count|總計|計費的工作流程觸發程序執行數目。|無維度|
|BillingUsageNativeOperation|是|為使用原生作業執行計費|Count|總計|要計費的原生作業執行數。|無維度|
|BillingUsageStandardConnector|是|為使用標準連接器執行計費|Count|總計|要計費的標準連接器執行數。|無維度|
|BillingUsageStorageConsumption|是|為使用儲存體使用量執行計費|Count|總計|要計費的儲存體使用量執行數。|無維度|
|RunFailurePercentage|是|執行失敗百分比|百分比|總計|已失敗的工作流程執行百分比。|無維度|
|RunLatency|是|執行延遲|秒|Average|已完成的工作流程執行延遲。|無維度|
|RunsCancelled|是|取消的執行|Count|總計|已取消的工作流程執行數目。|無維度|
|RunsCompleted|是|完成的執行|Count|總計|已完成的工作流程執行數目。|無維度|
|RunsFailed|是|失敗的執行|Count|總計|已失敗的工作流程執行數目。|無維度|
|RunsStarted|是|執行已啟動|Count|總計|已啟動的工作流程執行數目。|無維度|
|RunsSucceeded|是|已成功的執行|Count|總計|已成功的工作流程執行數目。|無維度|
|RunStartThrottledEvents|是|執行開始節流處理事件|Count|總計|工作流程執行開始節流事件的數目。|無維度|
|RunSuccessLatency|是|執行成功的延遲|秒|Average|已成功的工作流程執行延遲。|無維度|
|RunThrottledEvents|是|執行節流的事件|Count|總計|工作流程動作或觸發節流的事件數目。|無維度|
|TotalBillableExecutions|是|可計費的總執行次數|Count|總計|計費的工作流程執行數目。|無維度|
|TriggerFireLatency|是|觸發程序引發延遲 |秒|Average|已引發的工作流程觸發程序延遲。|無維度|
|TriggerLatency|是|觸發程序延遲 |秒|Average|已完成的工作流程觸發程序延遲。|無維度|
|TriggersCompleted|是|完成的觸發程序 |Count|總計|已完成的工作流程觸發程序數目。|無維度|
|TriggersFailed|是|失敗的觸發程序 |Count|總計|已失敗的工作流程觸發程序數目。|無維度|
|TriggersFired|是|引發的觸發程序 |Count|總計|已引發的工作流程觸發程序數目。|無維度|
|TriggersSkipped|是|略過的觸發程序|Count|總計|已略過的工作流程觸發程序數目。|無維度|
|TriggersStarted|是|啟動的觸發程序 |Count|總計|已啟動的工作流程觸發程序數目。|無維度|
|TriggersSucceeded|是|成功的觸發程序 |Count|總計|已成功的工作流程觸發程序數目。|無維度|
|TriggerSuccessLatency|是|觸發程序成功延遲 |秒|Average|已成功的工作流程觸發程序延遲。|無維度|
|TriggerThrottledEvents|是|觸發程序節流的事件|Count|總計|工作流程觸發程序節流的事件數目。|無維度|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|作用中核心|是|作用中核心|計數|Average|使用中核心數目|案例，ClusterName|
|作用中節點|是|作用中節點|計數|Average|Acitve 節點的數目。 這些是目前正在執行作業的節點。|案例，ClusterName|
|取消要求的執行|是|取消要求的執行|Count|總計|針對此工作區要求取消的執行次數。 已收到執行的取消要求時，會更新計數。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|取消的執行|是|取消的執行|Count|總計|已取消此工作區的執行數目。 成功取消執行時，會更新計數。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|已完成的執行|是|已完成的執行|Count|總計|已成功完成此工作區的執行數目。 當執行完成且已收集輸出時，就會更新計數。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|CpuUtilization|是|CpuUtilization|計數|Average|CPU 節點的記憶體使用率百分比。 使用率會以一分鐘為間隔回報。|案例，runId，，ClusterName|
|錯誤|是|Errors|Count|總計|此工作區中的執行錯誤數目。 每當執行遇到錯誤時，就會更新計數。|案例|
|失敗的執行|是|失敗的執行|Count|總計|此工作區的執行失敗次數。 當執行失敗時，會更新計數。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|正在完成執行|是|正在完成執行|Count|總計|此工作區的進入完成狀態的執行數目。 當執行完成但輸出收集仍在進行中時，就會更新計數。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|GpuUtilization|是|GpuUtilization|計數|Average|GPU 節點的記憶體使用率百分比。 使用率會以一分鐘為間隔回報。|案例，runId，實例，DeviceId，ClusterName|
|閒置核心|是|閒置核心|計數|Average|閒置核心數|案例，ClusterName|
|閒置節點|是|閒置節點|計數|Average|閒置節點的數目。 閒置節點是指未執行任何作業的節點，但可以接受新作業（如果有的話）。|案例，ClusterName|
|正在離開的核心|是|正在離開的核心|計數|Average|離開核心數|案例，ClusterName|
|正在離開的節點|是|正在離開的節點|計數|Average|離開節點的數目。 離開節點是剛完成作業並將進入閒置狀態的節點。|案例，ClusterName|
|失敗的模型部署|是|失敗的模型部署|Count|總計|在此工作區中失敗的模型部署數|案例，StatusCode|
|已啟動的模型部署|是|已啟動的模型部署|Count|總計|在此工作區中啟動的模型部署數目|案例|
|成功的模型部署|是|成功的模型部署|Count|總計|在此工作區中成功的模型部署數|案例|
|失敗的模型註冊|是|失敗的模型註冊|Count|總計|在此工作區中失敗的模型註冊數目|案例，StatusCode|
|成功的模型註冊|是|成功的模型註冊|Count|總計|在此工作區中成功的模型註冊數目|案例|
|未回應執行|是|未回應執行|Count|總計|此工作區的執行沒有回應的數目。 當執行進入沒有回應的狀態時，就會更新計數。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|未開始執行|是|未開始執行|Count|總計|此工作區的執行數目未啟動狀態。 當收到要求來建立執行，但尚未填入執行資訊時，就會更新計數。 |案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|先佔節點|是|先佔節點|計數|Average|搶先核心數|案例，ClusterName|
|先佔節點|是|先佔節點|計數|Average|已搶先節點的數目。 這些節點是從可用節點集區中取出的低優先順序節點。|案例，ClusterName|
|準備執行|是|準備執行|Count|總計|為此工作區準備的執行數目。 在準備執行環境時，當執行進入準備狀態時，就會更新計數。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|布建執行|是|布建執行|Count|總計|為此工作區布建的執行數目。 當執行正在等候計算目標建立或布建時，就會更新計數。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|佇列的執行|是|佇列的執行|Count|總計|為此工作區排入佇列的執行數目。 計算目標中的執行排入佇列時，會更新計數。 等候所需的計算節點準備就緒時，可以 occure。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|配額使用率百分比|是|配額使用率百分比|計數|Average|使用的配額百分比|案例，ClusterName，VmFamilyName，VmPriority|
|已啟動的執行|是|已啟動的執行|Count|總計|此工作區的執行數目。 當開始執行所需的資源時，會更新計數。|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|開始執行|是|開始執行|Count|總計|此工作區已開始執行的數目。 計數會在建立執行和執行資訊（例如，執行識別碼）已填入要求之後更新|案例，RunType，PublishedPipelineId，ComputeType，PipelineStepType|
|總核心數|是|總核心數|計數|Average|核心總數|案例，ClusterName|
|節點總計|是|節點總計|計數|Average|總節點數。 此總計包括一些作用中節點、閒置節點、無法使用的節點、Premepted 節點、離開節點|案例，ClusterName|
|無法使用的核心|是|無法使用的核心|計數|Average|無法使用的核心數目|案例，ClusterName|
|無法使用的節點|是|無法使用的節點|計數|Average|無法使用的節點數目。 無法使用的節點無法運作，因為有一些無法解析的問題。 Azure 將會回收這些節點。|案例，ClusterName|
|警告|是|警告|Count|總計|此工作區中的執行警告數目。 每當執行遇到警告時，就會更新計數。|案例|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|Api 的可用性|ApiCategory、ApiName|
|使用方式|否|使用量|Count|計數|API 呼叫的計數|ApiCategory、ApiName、ResultType、ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AssetCount|是|資產計數|計數|Average|目前的媒體服務帳戶中已建立多少個資產|無維度|
|AssetQuota|是|資產配額|計數|Average|目前的媒體服務帳戶允許多少個資產|無維度|
|AssetQuotaUsedPercentage|是|已使用的資產配額百分比|百分比|Average|目前媒體服務帳戶中的資產使用百分比|無維度|
|ContentKeyPolicyCount|是|內容金鑰原則計數|計數|Average|目前媒體服務帳戶中已建立的內容金鑰原則數目|無維度|
|ContentKeyPolicyQuota|是|內容金鑰原則配額|計數|Average|目前的媒體服務帳戶允許多少內容金鑰原則|無維度|
|ContentKeyPolicyQuotaUsedPercentage|是|已使用的內容金鑰原則配額百分比|百分比|Average|目前媒體服務帳戶中的內容金鑰原則使用百分比|無維度|
|StreamingPolicyCount|是|串流原則計數|計數|Average|目前媒體服務帳戶中已建立的串流原則數目|無維度|
|StreamingPolicyQuota|是|串流原則配額|Count|Average|目前媒體服務帳戶允許的串流原則數目|無維度|
|StreamingPolicyQuotaUsedPercentage|是|已使用的串流原則配額百分比|百分比|Average|目前媒體服務帳戶中的串流原則使用百分比|無維度|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|輸出|是|輸出|位元組|總計|輸出資料量（以位元組為單位）。|OutputFormat|
|Requests|是|Requests|Count|總計|對串流端點的要求。|OutputFormat、HttpStatusCode、ErrorCode|
|SuccessE2ELatency|是|成功端對端延遲|毫秒|Average|成功要求的平均延遲（以毫秒為單位）。|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|是|集區配置大小|位元組|Average|此集區的布建大小|無維度|
|VolumePoolAllocatedUsed|是|配置給磁片區大小的集區|位元組|Average|集區的已使用配置大小|無維度|
|VolumePoolTotalLogicalSize|是|已耗用的集區大小|位元組|Average|屬於集區之所有磁碟區的邏輯大小的總和|無維度|
|VolumePoolTotalSnapshotSize|是|集區的快照集大小總計|位元組|Average|此集區中所有磁片區的快照集大小總和|無維度|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AverageReadLatency|是|讀取延遲的平均值|毫秒|Average|每個作業讀取延遲的平均值 (毫秒)|無維度|
|AverageWriteLatency|是|寫入延遲的平均值|毫秒|Average|每個作業寫入延遲的平均值 (毫秒)|無維度|
|CbsVolumeBackupActive|是|磁片區備份作用中狀態|計數|Average|目前是否已暫停磁片區的備份。|無維度|
|CbsVolumeLogicalBackupBytes|是|已備份的邏輯位元組|位元組|Average|Toatl 此磁片區的未壓縮/未加密的位元組已備份。|無維度|
|CbsVolumeOperationComplete|是|作業狀態|計數|Average|上次備份/還原作業是否成功。|無維度|
|CbsVolumeOperationTransferredBytes|是|作業的傳輸位元組數|位元組|Average|上次備份/還原作業所傳輸的位元組總數。|無維度|
|CbsVolumeProtected|是|磁片區保護狀態|Count|Average|是由雲端備份服務所保護的磁片區。|無維度|
|ReadIops|是|讀取 IOPS|每秒計數|Average|每秒的讀取 I/O 作業|無維度|
|VolumeAllocatedSize|是|磁碟區配置大小|位元組|Average|磁片區的布建大小|無維度|
|VolumeLogicalSize|是|磁片區已耗用大小|位元組|Average|磁碟區的邏輯大小 (已使用的位元組)|無維度|
|VolumeSnapshotSize|是|磁碟區快照集大小|位元組|Average|磁碟區所有快照集的大小|無維度|
|WriteIops|是|寫入 IOPS|每秒計數|Average|每秒的寫入 I/O 作業|無維度|
|XregionReplicationHealthy|是|磁片區複寫狀態狀況良好|Count|Average|關聯性的條件，1或0。|無維度|
|XregionReplicationLagTime|是|磁片區複寫延遲時間|秒|Average|鏡像上的資料落後于來源的時間量（以秒為單位）。|無維度|
|XregionReplicationLastTransferDuration|是|磁片區複寫上次傳輸持續時間|秒|Average|最後一次傳送完成所需的時間量（以秒為單位）。|無維度|
|XregionReplicationLastTransferSize|是|磁片區複寫上次傳輸大小|位元組|Average|最後一次傳輸時傳輸的位元組總數。|無維度|
|XregionReplicationRelationshipProgress|是|磁片區複寫進度|位元組|Average|目前傳送作業所傳輸的總數據量。|無維度|
|XregionReplicationRelationshipTransferring|是|是否正在傳輸磁片區複寫|Count|Average|磁片區複寫的狀態是否為「傳輸中」。|無維度|
|XregionReplicationTotalTransferBytes|是|磁片區複寫總計傳輸|位元組|Average|為關聯性傳輸的累計位元組。|無維度|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|否|應用程式閘道總時間|毫秒|Average|處理要求所花費的平均時間，以及要傳送的回應。 這是以從應用程式閘道接收 HTTP 要求的第一個位元組到回應傳送作業完成時的時間間隔平均值來計算。 請務必注意，這通常包括應用程式閘道處理時間、要求和回應封包在網路上的傳輸時間，以及後端伺服器回應的時間。|接聽程式|
|AvgRequestCountPerHealthyHost|否|每個健康情況主機每分鐘的要求|計數|Average|集區中每個狀況良好後端主機每分鐘的平均要求計數|BackendSettingsPool|
|BackendConnectTime|否|後端連線時間|毫秒|Average|建立與後端伺服器連接所花費的時間|接聽程式，BackendServer，BackendPool，BackendHttpSetting|
|BackendFirstByteResponseTime|否|後端第一個位元組回應時間|毫秒|Average|開始建立與後端伺服器之間的連線，以及接收回應標頭的第一個位元組、將逼近後端伺服器的處理時間，兩者之間的時間間隔|接聽程式，BackendServer，BackendPool，BackendHttpSetting|
|BackendLastByteResponseTime|否|後端最後位元組回應時間|毫秒|Average|開始建立與後端伺服器之間的連線，以及接收回應主體最後一個位元組之間的時間間隔|接聽程式，BackendServer，BackendPool，BackendHttpSetting|
|BackendResponseStatus|是|後端回應狀態|Count|總計|後端成員所產生的 HTTP 回應碼數目。 這不包含任何由應用程式閘道產生的回應碼。|BackendServer、BackendPool、BackendHttpSetting、HttpStatusGroup|
|BlockedCount|是|Web 應用程式防火牆封鎖的要求規則散發|Count|總計|Web 應用程式防火牆封鎖要求規則散發|RuleGroup、RuleId|
|BlockedReqCount|是|Web 應用程式防火牆封鎖的要求計數|Count|總計|Web 應用程式防火牆封鎖的要求計數|無維度|
|BytesReceived|是|接收的位元組數|位元組|總計|應用程式閘道從用戶端接收的位元組總數|接聽程式|
|BytesSent|是|傳送的位元組數|位元組|總計|應用程式閘道傳送給用戶端的位元組總數|接聽程式|
|CapacityUnits|否|目前的容量單位|計數|Average|耗用的容量單位|無維度|
|ClientRtt|否|用戶端 RTT|毫秒|Average|用戶端與應用程式閘道之間的平均來回行程時間。 此計量指出建立連線和傳回通知所需的時間|接聽程式|
|ComputeUnits|否|目前的計算單位|Count|Average|耗用的計算單位|無維度|
|CpuUtilization|否|CPU 使用率|百分比|Average|應用程式閘道目前的 CPU 使用率|無維度|
|CurrentConnections|是|目前的連線數|Count|總計|目前與應用程式閘道建立的連線計數|無維度|
|EstimatedBilledCapacityUnits|否|預估的計費容量單位|計數|Average|預估的容量單位將收取費用|無維度|
|FailedRequests|是|失敗的要求|Count|總計|應用程式閘道已服務的失敗要求計數|BackendSettingsPool|
|FixedBillableCapacityUnits|否|固定計費容量單位|計數|Average|將收取的最小容量單位|無維度|
|HealthyHostCount|是|狀況良好的主機計數|計數|Average|狀況良好的後端主機數目|BackendSettingsPool|
|MatchedCount|是|Web 應用程式防火牆的規則散發總計|Count|總計|連入流量的 Web 應用程式防火牆總規則分佈|RuleGroup、RuleId|
|NewConnectionsPerSecond|否|每秒的新連線數|每秒計數|Average|以應用程式閘道建立的每秒新連線數|無維度|
|ResponseStatus|是|回應狀態|Count|總計|應用程式閘道傳回的 Http 回應狀態|HttpStatusGroup|
|輸送量|否|Throughput|每秒位元組|Average|應用程式閘道每秒已服務的位元組數目|無維度|
|TlsProtocol|是|用戶端 TLS 通訊協定|Count|總計|與應用程式閘道建立連線的用戶端所起始的 TLS 和非 TLS 要求數目。 若要查看 TLS 通訊協定散發，請依維度 TLS 通訊協定進行篩選。|接聽程式，TlsProtocol|
|TotalRequests|是|要求總數|Count|總計|應用程式閘道已服務的成功要求計數|BackendSettingsPool|
|UnhealthyHostCount|是|狀況不良的主機計數|Count|Average|狀況不良的後端主機數目|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|是|應用程式規則命中計數|計數|總計|遇到應用程式規則的次數|狀態、原因、通訊協定|
|DataProcessed|是|已處理的資料量|位元組|總計|此防火牆處理的總數據量|無維度|
|FirewallHealth|是|防火牆健全狀態|百分比|Average|指出此防火牆的整體健全狀況|狀態、原因|
|NetworkRuleHit|是|網路規則命中計數|計數|總計|點擊網路規則的次數|狀態、原因、通訊協定|
|SNATPortUtilization|是|SNAT 連接埠使用率|百分比|Average|目前使用中的輸出 SNAT 埠百分比|通訊協定|
|輸送量|否|輸送量|BitsPerSecond|Average|此防火牆處理的輸送量|無維度|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|BitsInPerSecond|是|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|無維度|
|BitsOutPerSecond|是|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|無維度|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|QueryVolume|是|查詢磁碟區|計數|總計|為 DNS 區域儲存的查詢數目|無維度|
|RecordSetCapacityUtilization|否|記錄集容量使用率|百分比|最大值|DNS 區域所使用的記錄集容量百分比|無維度|
|RecordSetCount|是|記錄集計數|Count|最大值|DNS 區域中的記錄集數目|無維度|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ArpAvailability|是|Arp 可用性|百分比|Average|從 MSEE 到所有對等的 ARP 可用性。|PeeringType、對等|
|BgpAvailability|是|Bgp 可用性|百分比|Average|從 MSEE 到所有對等的 BGP 可用性。|PeeringType、對等|
|BitsInPerSecond|否|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|PeeringType|
|BitsOutPerSecond|否|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|PeeringType|
|GlobalReachBitsInPerSecond|否|GlobalReachBitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|否|GlobalReachBitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|PeeredCircuitSKey|
|QosDropBitsInPerSecond|否|DroppedInBitsPerSecond|每秒計數|Average|每秒捨棄的輸入資料位|無維度|
|QosDropBitsOutPerSecond|否|DroppedOutBitsPerSecond|每秒計數|Average|每秒捨棄的輸出資料位|無維度|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|BitsInPerSecond|是|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|無維度|
|BitsOutPerSecond|是|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|無維度|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|否|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|否|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|ConnectionName|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AdminState|是|AdminState|計數|Average|埠的系統管理狀態|連結|
|LineProtocol|是|LineProtocol|計數|Average|埠的線路通訊協定狀態|連結|
|PortBitsInPerSecond|是|BitsInPerSecond|每秒計數|Average|每秒輸入到 Azure 的位元數|連結|
|PortBitsOutPerSecond|是|BitsOutPerSecond|每秒計數|Average|每秒從 Azure 輸出的位元數|連結|
|RxLightLevel|是|RxLightLevel|計數|Average|Rx 光線層級（dBm）|連結，Lane|
|TxLightLevel|是|TxLightLevel|計數|Average|以 dBm 為 Tx 的淺層級|連結，Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|是|後端健康情況百分比|百分比|Average|從 HTTP/S Proxy 到後端的健全狀況探查成功百分比|Backend、BackendPool|
|BackendRequestCount|是|後端要求計數|Count|總計|從 HTTP/S Proxy 傳送至後端的要求數|HttpStatus、HttpStatusGroup、Backend|
|BackendRequestLatency|是|後端要求延遲|毫秒|Average|從 HTTP/S Proxy 傳送要求到後端一直到 HTTP/S Proxy 從後端接收最後回應位元組之間算出的時間|後端|
|BillableResponseSize|是|可計費的回應大小|位元組|總計|當做回應從 HTTP/S proxy 傳送至用戶端的可計費位元組數（每個要求最少的2KB）。|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|RequestCount|是|要求計數|Count|總計|HTTP/S Proxy 服務的用戶端要求數|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|RequestSize|是|要求大小|位元組|總計|從用戶端傳送到 HTTP/S Proxy 的要求的位元組數|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|ResponseSize|是|回應大小|位元組|總計|從 HTTP/S Proxy 傳送到用戶端的回應的位元組數|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|TotalLatency|是|延遲總計|毫秒|Average|從 HTTP/S Proxy 接收用戶端要求一直到用戶端認可來自 HTTP/S Proxy 的最後回應位元組之間算出的時間|HttpStatus、HttpStatusGroup、ClientRegion、ClientCountry|
|WebApplicationFirewallRequestCount|是|Web 應用程式防火牆要求計數|Count|總計|Web 應用程式防火牆處理的用戶端要求數|PolicyName、RuleName、Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|否|配置的 SNAT 埠|計數|Average|在期間內配置的 SNAT 連接埠總數|FrontendIPAddress、BackendIPAddress、ProtocolType、 |
|ByteCount|是|位元組計數|Count|總計|在期間內傳輸的位元組總數|FrontendIPAddress、FrontendPort、Direction|
|DipAvailability|是|健全狀況探查狀態|計數|Average|每個持續時間 Load Balancer 健全狀況探查狀態的平均值|ProtocolType、BackendPort、FrontendIPAddress、FrontendPort、BackendIPAddress|
|PacketCount|是|封包計數|Count|總計|在期間內傳輸的封包總數|FrontendIPAddress、FrontendPort、Direction|
|SnatConnectionCount|是|SNAT 連線計數|Count|總計|在期間內建立的新 SNAT 連線總數|FrontendIPAddress、BackendIPAddress、ConnectionState|
|SYNCount|是|SYN 計數|Count|總計|在期間內傳輸的 SYN 封包總數|FrontendIPAddress、FrontendPort、Direction|
|UsedSnatPorts|否|使用的 SNAT 埠|計數|Average|在期間內使用的 SNAT 連接埠總數|FrontendIPAddress、BackendIPAddress、ProtocolType、 |
|VipAvailability|是|資料路徑可用性|Count|Average|每個持續時間 Load Balancer 資料路徑可用性的平均值|FrontendIPAddress、FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|BytesReceivedRate|是|接收的位元組數|位元組|總計|網路介面接收的位元組數|無維度|
|BytesSentRate|是|傳送的位元組數|位元組|總計|網路介面傳送的位元組數|無維度|
|PacketsReceivedRate|是|接收的封包|Count|總計|網路介面接收的封包數|無維度|
|PacketsSentRate|是|傳送的封包|Count|總計|網路介面傳送的封包數|無維度|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|是|Avg.來回時間 (毫秒)|毫秒|Average|在來源與目的地之間所傳送連線能力監視探查的平均網路來回時間 (毫秒)|無維度|
|ChecksFailedPercent|是|檢查失敗百分比 (預覽)|百分比|Average|% 的連線能力監視檢查失敗|SourceAddress、SourceResourceId、SourceType、Protocol、DestinationAddress、DestinationName、DestinationResourceId、DestinationType、DestinationPort、TestGroupName、TestConfigurationName|
|ProbesFailedPercent|是|失敗的探查百分比|百分比|Average|失敗的連線能力監視探查百分比|無維度|
|RoundTripTimeMs|是|來回時間 (毫秒) (預覽)|毫秒|Average|連線能力監視檢查的來回行程時間（以毫秒為單位）|SourceAddress、SourceResourceId、SourceType、Protocol、DestinationAddress、DestinationName、DestinationResourceId、DestinationType、DestinationPort、TestGroupName、TestConfigurationName|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ByteCount|是|位元組計數|Count|總計|在期間內傳輸的位元組總數|連接埠，方向|
|BytesDroppedDDoS|是|傳入位元組數捨棄 DDoS|每秒位元組|最大值|傳入位元組數捨棄 DDoS|無維度|
|BytesForwardedDDoS|是|傳入位元組數轉送 DDoS|每秒位元組|最大值|傳入位元組數轉送 DDoS|無維度|
|BytesInDDoS|是|傳入位元組數 DDoS|每秒位元組|最大值|傳入位元組數 DDoS|無維度|
|DDoSTriggerSYNPackets|是|用以觸發 DDoS 風險降低措施的輸入 SYN 封包數|每秒計數|最大值|用以觸發 DDoS 風險降低措施的輸入 SYN 封包數|無維度|
|DDoSTriggerTCPPackets|是|傳入 TCP 封包數以觸發 DDoS 緩和|每秒計數|最大值|傳入 TCP 封包數以觸發 DDoS 緩和|無維度|
|DDoSTriggerUDPPackets|是|傳入 UDP 封包數以觸發 DDoS 緩和|每秒計數|最大值|傳入 UDP 封包數以觸發 DDoS 緩和|無維度|
|IfUnderDDoSAttack|是|是否正遭受 DDoS 攻擊|Count|最大值|是否正遭受 DDoS 攻擊|無維度|
|PacketCount|是|封包計數|計數|總計|在期間內傳輸的封包總數|連接埠，方向|
|PacketsDroppedDDoS|是|傳入封包捨棄 DDoS|每秒計數|最大值|傳入封包捨棄 DDoS|無維度|
|PacketsForwardedDDoS|是|傳入封包轉寄 DDoS|每秒計數|最大值|傳入封包轉寄 DDoS|無維度|
|PacketsInDDoS|是|傳入封包 DDoS|每秒計數|最大值|傳入封包 DDoS|無維度|
|SynCount|是|SYN 計數|計數|總計|在期間內傳輸的 SYN 封包總數|連接埠，方向|
|TCPBytesDroppedDDoS|是|傳入 TCP 位元組數捨棄 DDoS|每秒位元組|最大值|傳入 TCP 位元組數捨棄 DDoS|無維度|
|TCPBytesForwardedDDoS|是|傳入 TCP 位元組數轉送 DDoS|每秒位元組|最大值|傳入 TCP 位元組數轉送 DDoS|無維度|
|TCPBytesInDDoS|是|傳入 TCP 位元組數 DDoS|每秒位元組|最大值|傳入 TCP 位元組數 DDoS|無維度|
|TCPPacketsDroppedDDoS|是|傳入 TCP 封包捨棄 DDoS|每秒計數|最大值|傳入 TCP 封包捨棄 DDoS|無維度|
|TCPPacketsForwardedDDoS|是|傳入 TCP 封包轉送 DDoS|每秒計數|最大值|傳入 TCP 封包轉送 DDoS|無維度|
|TCPPacketsInDDoS|是|傳入 TCP 封包 DDoS|每秒計數|最大值|傳入 TCP 封包 DDoS|無維度|
|UDPBytesDroppedDDoS|是|傳入 UDP 位元組數捨棄 DDoS|每秒位元組|最大值|傳入 UDP 位元組數捨棄 DDoS|無維度|
|UDPBytesForwardedDDoS|是|傳入 UDP 位元組數轉送 DDoS|每秒位元組|最大值|傳入 UDP 位元組數轉送 DDoS|無維度|
|UDPBytesInDDoS|是|傳入 UDP 位元組數 DDoS|每秒位元組|最大值|傳入 UDP 位元組數 DDoS|無維度|
|UDPPacketsDroppedDDoS|是|傳入 UDP 封包捨棄 DDoS|每秒計數|最大值|傳入 UDP 封包捨棄 DDoS|無維度|
|UDPPacketsForwardedDDoS|是|傳入 UDP 封包轉送 DDoS|每秒計數|最大值|傳入 UDP 封包轉送 DDoS|無維度|
|UDPPacketsInDDoS|是|傳入 UDP 封包 DDoS|每秒計數|最大值|傳入 UDP 封包 DDoS|無維度|
|VipAvailability|是|資料路徑可用性|計數|Average|每個持續時間 IP 位址可用性的平均值|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|是|依端點的端點狀態|Count|最大值|若端點探查狀態為「已啟用」則為 1，否則為 0。|EndpointName|
|QpsByEndpoint|是|傳回的依端點查詢數|計數|總計|在指定時間範圍內傳回 Traffic Manager 端點的次數|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AverageBandwidth|是|閘道 S2S 頻寬|每秒位元組|Average|每秒閘道中站對站的平均頻寬 (位元組)|無維度|
|P2SBandwidth|是|閘道 S2S 頻寬|每秒位元組|Average|每秒閘道中點對站的平均頻寬 (位元組)|無維度|
|P2SConnectionCount|是|P2S 連線計數|Count|最大值|閘道的點對站連線計數|通訊協定|
|TunnelAverageBandwidth|是|通道頻寬|每秒位元組|Average|通道的平均頻寬 (位元組/秒)|ConnectionName，RemoteIP|
|TunnelEgressBytes|是|通道輸出位元組數|位元組|總計|通道的傳出位元組數|ConnectionName，RemoteIP|
|TunnelEgressPacketDropTSMismatch|是|通道輸出 TS 不相符封包捨棄|Count|總計|因與通道不符而從流量選取器捨棄的傳出封包計數|ConnectionName，RemoteIP|
|TunnelEgressPackets|是|通道輸出封包數|Count|總計|通道的傳出封包計數|ConnectionName，RemoteIP|
|TunnelIngressBytes|是|通道輸入位元組數|位元組|總計|通道的傳入位元組數|ConnectionName，RemoteIP|
|TunnelIngressPacketDropTSMismatch|是|通道輸入 TS 不符合封包捨棄|Count|總計|因與通道不符而從流量選取器捨棄的傳入封包計數|ConnectionName，RemoteIP|
|TunnelIngressPackets|是|通道輸入封包數|Count|總計|通道的傳入封包計數|ConnectionName，RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|是|Ping 至 VM 的來回時間|毫秒|Average|傳送至目的地 VM 之 Ping 的來回時間|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|是|Ping 至 VM 失敗|百分比|Average|對目的地 VM 的已傳送 Ping 總數的失敗次數百分比|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|incoming|是|傳入訊息|Count|總計|所有成功傳送 API 呼叫的計數。 |無維度|
|incoming.all.failedrequests|是|所有傳入的失敗要求|Count|總計|針對通知中樞傳入的失敗要求總數|無維度|
|incoming.all.requests|是|所有傳入要求|Count|總計|針對通知中樞傳入的要求總數|無維度|
|incoming.scheduled|是|傳送已排程的推播通知|Count|總計|取消已排程的推播通知|無維度|
|incoming.scheduled.cancel|是|取消已排程的推播通知|Count|總計|取消已排程的推播通知|無維度|
|installation.all|是|安裝管理作業|Count|總計|安裝管理作業|無維度|
|installation.delete|是|刪除安裝作業|Count|總計|刪除安裝作業|無維度|
|installation.get|是|取得安裝作業|Count|總計|取得安裝作業|無維度|
|installation.patch|是|修補安裝作業|Count|總計|修補安裝作業|無維度|
|installation.upsert|是|建立或更新安裝作業|Count|總計|建立或更新安裝作業|無維度|
|notificationhub.pushes|是|所有外寄通知|Count|總計|通知中樞的所有外寄通知|無維度|
|outgoing.allpns.badorexpiredchannel|是|錯誤或過期的通道錯誤|Count|總計|因為註冊中的通道/權杖/registrationId 過期或無效，導致失敗的推播計數。|無維度|
|outgoing.allpns.channelerror|是|通道錯誤|Count|總計|因為通道無效、未與正確的應用程式相關聯、已節流處理或過期，導致失敗的推播計數。|無維度|
|outgoing.allpns.invalidpayload|是|承載錯誤|Count|總計|因為 PNS 傳回錯誤承載錯誤而失敗的推播計數。|無維度|
|outgoing.allpns.pnserror|是|外部通知系統錯誤|Count|總計|因為與 PNS 通訊發生問題 (不包括驗證問題) 而失敗的推播計數。|無維度|
|outgoing.allpns.success|是|成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.apns.badchannel|是|APNS 不正確的通道錯誤|Count|總計|因為權杖無效而失敗的推播計數 (APNS 狀態碼︰8)。|無維度|
|outgoing.apns.expiredchannel|是|APNS 過期通道錯誤|Count|總計|由 APNS 意見反應通道宣告失效的權杖計數。|無維度|
|outgoing.apns.invalidcredentials|是|APNS 授權錯誤|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無維度|
|outgoing.apns.invalidnotificationsize|是|APNS 無效通知大小錯誤|Count|總計|因為承載太大而失敗的推播計數 (APNS 狀態碼︰7)。|無維度|
|outgoing.apns.pnserror|是|APNS 錯誤|Count|總計|因為與 APNS 通訊錯誤而失敗的推播計數。|無維度|
|outgoing.apns.success|是|APNS 成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.gcm.authenticationerror|是|GCM 驗證錯誤|Count|總計|因為 PNS 不接受提供的認證、已封鎖認證，或應用程式中未正確設定 SenderId，導致失敗的推播計數 (GCM 結果︰MismatchedSenderId)。|無維度|
|outgoing.gcm.badchannel|是|GCM 不正確的通道錯誤|Count|總計|因為無法識別註冊中的 registrationId 而失敗的推播計數 (GCM 結果：無效註冊)。|無維度|
|outgoing.gcm.expiredchannel|是|GCM 過期通道錯誤|Count|總計|因為註冊中的 registrationId 過期而失敗的推播計數 (GCM 結果：NotRegistered)。|無維度|
|outgoing.gcm.invalidcredentials|是|GCM 授權錯誤 (無效認證)|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無維度|
|outgoing.gcm.invalidnotificationformat|是|GCM 無效通知格式|Count|總計|因為承載未正確格式化而失敗的推播計數 (GCM 結果︰InvalidDataKey 或 InvalidTtl)。|無維度|
|outgoing.gcm.invalidnotificationsize|是|GCM 無效通知大小錯誤|Count|總計|因為承載太大而失敗的推播計數 (GCM 結果︰MessageTooBig)。|無維度|
|outgoing.gcm.pnserror|是|GCM 錯誤|Count|總計|因為與 GCM 通訊錯誤而失敗的推播計數。|無維度|
|outgoing.gcm.success|是|GCM 成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.gcm.throttled|是|GCM 已將通知節流處理|Count|總計|因為 GCM 已將此應用程式節流處理而失敗的推播計數 (GCM 狀態碼︰501-599 或結果：Unavailable)。|無維度|
|outgoing.gcm.wrongchannel|是|GCM 不正確的通道錯誤|Count|總計|因為註冊中的 registrationId 未與目前的應用程式相關聯，導致失敗的推播計數 (GCM 結果：InvalidPackageName)。|無維度|
|outgoing.mpns.authenticationerror|是|MPNS 驗證錯誤|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無維度|
|outgoing.mpns.badchannel|是|MPNS 不正確的通道錯誤|Count|總計|因為無法識別註冊中的 ChannelURI 而失敗的推播計數 (MPNS 狀態︰404 找不到)。|無維度|
|outgoing.mpns.channeldisconnected|是|MPNS 通道已中斷連線|Count|總計|因為註冊中的 ChannelURI 中斷連線而失敗的推播計數 (MPNS 狀態︰412 找不到)。|無維度|
|outgoing.mpns.dropped|是|MPNS 已捨棄通知|Count|總計|MPNS 已捨棄的推播計數 (MPNS 回應標頭︰X-NotificationStatus: QueueFull 或 Suppressed)。|無維度|
|outgoing.mpns.invalidcredentials|是|MPNS 無效認證|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。|無維度|
|outgoing.mpns.invalidnotificationformat|是|MPNS 無效通知格式|Count|總計|因為通知的承載太大而失敗的推播計數。|無維度|
|outgoing.mpns.pnserror|是|MPNS 錯誤|Count|總計|因為與 MPNS 通訊錯誤而失敗的推播計數。|無維度|
|outgoing.mpns.success|是|MPNS 成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.mpns.throttled|是|MPNS 已將通知節流處理|Count|總計|因為 MPNS 正在節流此應用程式，導致失敗的推播計數 (MPNS 狀態︰406 無法接受)。|無維度|
|outgoing.wns.authenticationerror|是|WNS 驗證錯誤|Count|總計|因為與 Windows Live 通訊錯誤、無效認證或不正確的權杖，導致未傳遞通知。|無維度|
|outgoing.wns.badchannel|是|WNS 不正確的通道錯誤|Count|總計|因為無法識別註冊中的 ChannelURI (WNS 狀態︰404 找不到)，導致失敗的推播計數。|無維度|
|outgoing.wns.channeldisconnected|是|WNS 通道已中斷連線|Count|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (WNS 回應標頭︰X-WNS-DeviceConnectionStatus: disconnected)。|無維度|
|outgoing.wns.channelthrottled|是|WNS 通道已節流處理|Count|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (WNS 回應標頭︰X-WNS-NotificationStatus:channelThrottled)。|無維度|
|outgoing.wns.dropped|是|WNS 已捨棄通知|Count|總計|因為註冊中的 ChannelURI 已節流處理而捨棄通知 (X-WNS-NotificationStatus: dropped 但不是 X-WNS-DeviceConnectionStatus: disconnected)。|無維度|
|outgoing.wns.expiredchannel|是|WNS 過期通道錯誤|Count|總計|因為 ChannelURIi 過期 (WNS 狀態︰ 410 不存在) 而失敗的推播計數。|無維度|
|outgoing.wns.invalidcredentials|是|WNS 授權錯誤 (無效認證)|Count|總計|因為 PNS 不接受提供的認證或已封鎖認證，導致失敗的推播計數。 (Windows Live 無法識別認證)。|無維度|
|outgoing.wns.invalidnotificationformat|是|WNS 無效通知格式|Count|總計|通知的格式無效 (WNS 狀態︰400)。 請注意，WNS 不會拒絕所有無效承載。|無維度|
|outgoing.wns.invalidnotificationsize|是|WNS 無效通知大小錯誤|Count|總計|通知承載太大 (WNS 狀態︰413)。|無維度|
|outgoing.wns.invalidtoken|是|WNS 授權錯誤 (無效權杖)|Count|總計|提供給 WNS 的權杖無效 (WNS 狀態︰ 401 未經授權)。|無維度|
|outgoing.wns.pnserror|是|WNS 錯誤|Count|總計|因為與 WNS 通訊錯誤而未傳遞通知。|無維度|
|outgoing.wns.success|是|WNS 成功通知|Count|總計|所有成功通知的計數。|無維度|
|outgoing.wns.throttled|是|WNS 節流通知|Count|總計|因為 WNS 正在節流此應用程式，導致失敗的推播計數 (WNS 狀態︰406 無法接受)。|無維度|
|outgoing.wns.tokenproviderunreachable|是|WNS 授權錯誤 (無法連線)|Count|總計|無法連線到 Windows Live。|無維度|
|outgoing.wns.wrongtoken|是|WNS 授權錯誤 (錯誤權杖)|Count|總計|提供給 WNS 的權杖有效，但卻是給另一個應用程式 (WNS 狀態︰403 禁止)。 如果註冊中的 ChannelURI 與另一個應用程式相關聯，可能會發生這種情形。 請檢查用戶端應用程式相關聯的應用程式，就是認證位於通知中樞的同一個應用程式。|無維度|
|registration.all|是|註冊作業數|Count|總計|所有成功註冊作業 (建立、更新、查詢和刪除) 的計數。 |無維度|
|registration.create|是|註冊建立作業|Count|總計|所有成功註冊建立的計數。|無維度|
|registration.delete|是|註冊刪除作業|Count|總計|所有成功註冊刪除的計數。|無維度|
|registration.get|是|註冊讀取作業|Count|總計|所有成功註冊查詢的計數。|無維度|
|registration.update|是|註冊更新作業|Count|總計|所有成功註冊更新的計數。|無維度|
|scheduled.pending|是|暫止已排程的通知|Count|總計|暫止已排程的通知|無維度|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|Average_% Available Memory|是|% Available Memory|計數|Average|Average_% Available Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Available Swap Space|是|% Available Swap Space|計數|Average|Average_% Available Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Committed Bytes In Use|是|% Committed Bytes In Use|計數|Average|Average_% Committed Bytes In Use|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% DPC Time|是|% DPC Time|計數|Average|Average_% DPC Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Inodes|是|% Free Inodes|計數|Average|Average_% Free Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Free Space|是|% Free Space|計數|Average|Average_% Free Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Idle Time|是|% Idle Time|計數|Average|Average_% Idle Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Interrupt Time|是|% Interrupt Time|計數|Average|Average_% Interrupt Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% IO Wait Time|是|% IO Wait Time|計數|Average|Average_% IO Wait Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Nice Time|是|% Nice Time|計數|Average|Average_% Nice Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Privileged Time|是|% Privileged Time|Count|Average|Average_% Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Processor Time|是|% Processor Time|Count|Average|Average_% Processor Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Inodes|是|% Used Inodes|Count|Average|Average_% Used Inodes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Memory|是|% Used Memory|Count|Average|Average_% Used Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Space|是|% Used Space|Count|Average|Average_% Used Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% Used Swap Space|是|% Used Swap Space|Count|Average|Average_% Used Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_% User Time|是|% User Time|Count|Average|Average_% User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes|是|可用的 MB|Count|Average|Average_Available MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Memory|是|Available MBytes Memory|Count|Average|Average_Available MBytes Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Available MBytes Swap|是|Available MBytes Swap|Count|Average|Average_Available MBytes Swap|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Read|是|Avg.Disk sec/Read|Count|Average|Average_Avg. Disk sec/Read|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Transfer|是|Avg.Disk sec/Transfer|Count|Average|Average_Avg. Disk sec/Transfer|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Avg. Disk sec/Write|是|Avg.Disk sec/Write|Count|Average|Average_Avg. Disk sec/Write|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Received/sec|是|Bytes Received/sec|Count|Average|Average_Bytes Received/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Sent/sec|是|Bytes Sent/sec|Count|Average|Average_Bytes Sent/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Bytes Total/sec|是|Bytes Total/sec|Count|Average|Average_Bytes Total/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Current Disk Queue Length|是|Current Disk Queue Length|Count|Average|Average_Current Disk Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Read Bytes/sec|是|Disk Read Bytes/sec|Count|Average|Average_Disk Read Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Reads/sec|是|Disk Reads/sec|Count|Average|Average_Disk Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Transfers/sec|是|Disk Transfers/sec|Count|Average|Average_Disk Transfers/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Write Bytes/sec|是|Disk Write Bytes/sec|Count|Average|Average_Disk Write Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Disk Writes/sec|是|Disk Writes/sec|Count|Average|Average_Disk Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Megabytes|是|Free Megabytes|Count|Average|Average_Free Megabytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Physical Memory|是|Free Physical Memory|Count|Average|Average_Free Physical Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Space in Paging Files|是|Free Space in Paging Files|Count|Average|Average_Free Space in Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Free Virtual Memory|是|Free Virtual Memory|Count|Average|Average_Free Virtual Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Logical Disk Bytes/sec|是|Logical Disk Bytes/sec|Count|Average|Average_Logical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Reads/sec|是|Page Reads/sec|Count|Average|Average_Page Reads/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Page Writes/sec|是|Page Writes/sec|Count|Average|Average_Page Writes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pages/sec|是|Pages/sec|Count|Average|Average_Pages/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct Privileged Time|是|Pct Privileged Time|Count|Average|Average_Pct Privileged Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Pct User Time|是|Pct User Time|Count|Average|Average_Pct User Time|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Physical Disk Bytes/sec|是|Physical Disk Bytes/sec|Count|Average|Average_Physical Disk Bytes/sec|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processes|是|處理序|Count|Average|Average_Processes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Processor Queue Length|是|處理器佇列長度|Count|Average|Average_Processor Queue Length|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Size Stored In Paging Files|是|Size Stored In Paging Files|Count|Average|Average_Size Stored In Paging Files|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes|是|Total Bytes|Count|Average|Average_Total Bytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Received|是|Total Bytes Received|Count|Average|Average_Total Bytes Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Bytes Transmitted|是|Total Bytes Transmitted|Count|Average|Average_Total Bytes Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Collisions|是|Total Collisions|Count|Average|Average_Total Collisions|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Received|是|Total Packets Received|Count|Average|Average_Total Packets Received|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Packets Transmitted|是|Total Packets Transmitted|Count|Average|Average_Total Packets Transmitted|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Rx Errors|是|Total Rx Errors|Count|Average|Average_Total Rx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Total Tx Errors|是|Total Tx Errors|計數|Average|Average_Total Tx Errors|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Uptime|是|Uptime|計數|Average|Average_Uptime|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used MBytes Swap Space|是|Used MBytes Swap Space|計數|Average|Average_Used MBytes Swap Space|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory kBytes|是|Used Memory kBytes|計數|Average|Average_Used Memory kBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Used Memory MBytes|是|Used Memory MBytes|計數|Average|Average_Used Memory MBytes|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Users|是|使用者|計數|Average|Average_Users|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|Average_Virtual Shared Memory|是|Virtual Shared Memory|計數|Average|Average_Virtual Shared Memory|Computer、ObjectName、InstanceName、CounterPath、SourceSystem|
|事件|是|事件|計數|Average|事件|Source、EventLog、Computer、EventCategory、EventLevel、EventLevelName、EventID|
|活動訊號|是|Heartbeat|Count|總計|活動訊號|Computer、OSType、Version、SourceComputerId|
|更新|是|更新|Count|Average|更新|Computer、Product、Classification、UpdateState、Optional、Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft。對等互連/對等互連

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|EgressTrafficRate|是|輸出流量率|BitsPerSecond|Average|輸出流量速率（以每秒位數為單位）|ConnectionId|
|IngressTrafficRate|是|輸入流量率|BitsPerSecond|Average|輸入流量速率（以每秒位數為單位）|ConnectionId|
|SessionAvailabilityV4|是|會話可用性 V4|百分比|Average|V4 會話的可用性|ConnectionId|
|SessionAvailabilityV6|是|會話可用性 V6|百分比|Average|V6 會話的可用性|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft。對等互連/peeringServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|PrefixLatency|是|前置延遲|毫秒|Average|中間值前置延遲|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|memory_metric|是|記憶體|位元組|Average|記憶體。 範圍：A1 為 0-3 GB、A2 為 0-5 GB、A3 為 0-10 GB、A4 為 0-25 GB、A5 為 0-50 GB、A6 為 0-100 GB|無維度|
|memory_thrashing_metric|是|記憶體過度置換 (資料集)|百分比|Average|記憶體猛移的平均值。|無維度|
|qpu_high_utilization_metric|是|QPU 高使用率|Count|總計|前一分鐘的 QPU 高使用率，1 代表 QPU 高使用率，否則為 0|無維度|
|QueryDuration|是|查詢持續時間 (資料集)|毫秒|Average|最後一個間隔中的 DAX 查詢持續時間|無維度|
|QueryPoolJobQueueLength|是|查詢集區的作業佇列長度 (資料集)|計數|Average|查詢執行緒集區佇列中的作業數目。|無維度|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ActiveConnections|否|ActiveConnections|Count|總計|Microsoft.Relay 的 ActiveConnections 總計。|EntityName|
|ActiveListeners|否|ActiveListeners|Count|總計|Microsoft.Relay 的 ActiveListeners 總計。|EntityName|
|BytesTransferred|是|BytesTransferred|Count|總計|Microsoft.Relay 的 BytesTransferred 總計。|EntityName|
|ListenerConnections-ClientError|否|ListenerConnections-ClientError|Count|總計|Microsoft.Relay 的 ListenerConnections 上發生的 ClientError。|EntityName |
|ListenerConnections-ServerError|否|ListenerConnections-ServerError|Count|總計|Microsoft.Relay 的 ListenerConnections 上發生的 ServerError。|EntityName |
|ListenerConnections-Success|否|ListenerConnections-Success|Count|總計|Microsoft.Relay 的成功 ListenerConnections。|EntityName |
|ListenerConnections-TotalRequests|否|ListenerConnections-TotalRequests|Count|總計|Microsoft.Relay 的 ListenerConnections 總計。|EntityName|
|ListenerDisconnects|否|ListenerDisconnects|Count|總計|Microsoft.Relay 的 ListenerDisconnects 總計。|EntityName|
|SenderConnections-ClientError|否|SenderConnections-ClientError|Count|總計|Microsoft.Relay 的 SenderConnections 上發生的 ClientError。|EntityName |
|SenderConnections-ServerError|否|SenderConnections-ServerError|Count|總計|Microsoft.Relay 的 SenderConnections 上發生的 ServerError。|EntityName |
|SenderConnections-Success|否|SenderConnections-Success|Count|總計|Microsoft.Relay 的成功 SenderConnections。|EntityName |
|SenderConnections-TotalRequests|否|SenderConnections-TotalRequests|Count|總計|Microsoft.Relay 的 SenderConnections 要求總計。|EntityName|
|SenderDisconnects|否|SenderDisconnects|Count|總計|Microsoft.Relay 的 SenderDisconnects 總計。|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|SearchLatency|是|搜尋延遲|秒|Average|搜尋服務的平均搜尋延遲|無維度|
|SearchQueriesPerSecond|是|每秒搜尋查詢|每秒計數|Average|搜尋服務的每秒搜尋查詢|無維度|
|ThrottledSearchQueriesPercentage|是|節流的搜尋查詢百分比|百分比|Average|已針對搜尋查詢進行節流的搜尋服務百分比|無維度|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ActiveConnections|否|ActiveConnections|Count|總計|Microsoft.ServiceBus 的使用中連線總數。|無維度|
|ActiveMessages|否|佇列/主題中的作用中訊息計數。|計數|Average|佇列/主題中的作用中訊息計數。|EntityName|
|ConnectionsClosed|否|已關閉的連線。|計數|Average|Microsoft.ServiceBus 的已關閉連線。|EntityName|
|ConnectionsOpened|否|已開啟的連線。|計數|Average|Microsoft.ServiceBus 的已開啟連線。|EntityName|
|CPUXNS|否|CPU (已淘汰)|百分比|最大值|服務匯流排 premium 命名空間 CPU 使用量計量。 此度量為淘汰。 請改用 CPU 度量（NamespaceCpuUsage）。|無維度|
|DeadletteredMessages|否|佇列/主題中的無效訊息計數。|計數|Average|佇列/主題中的無效訊息計數。|EntityName|
|IncomingMessages|是|傳入訊息|Count|總計|Microsoft.ServiceBus 的傳入訊息。|EntityName|
|IncomingRequests|是|傳入的要求|Count|總計|Microsoft.ServiceBus 的傳入要求。|EntityName|
|Messages|否|佇列/主題中的訊息計數。|計數|Average|佇列/主題中的訊息計數。|EntityName|
|NamespaceCpuUsage|否|CPU|百分比|最大值|服務匯流排 premium 命名空間 CPU 使用量計量。|無維度|
|NamespaceMemoryUsage|否|記憶體使用量|百分比|最大值|服務匯流排 premium 命名空間記憶體使用量計量。|無維度|
|OutgoingMessages|是|外送訊息|Count|總計|Microsoft.ServiceBus 的傳出訊息。|EntityName|
|ScheduledMessages|否|佇列/主題中已排程的訊息計數。|Count|Average|佇列/主題中已排程的訊息計數。|EntityName|
|ServerErrors|否|伺服器錯誤。|Count|總計|Microsoft.ServiceBus 的伺服器錯誤。|EntityName |
|Size|否|大小|位元組|Average|佇列/主題的大小 (以位元組為單位)。|EntityName|
|SuccessfulRequests|否|成功的要求|Count|總計|命名空間的成功要求總數|EntityName |
|ThrottledRequests|否|節流的要求。|Count|總計|Microsoft.ServiceBus 的節流要求。|EntityName |
|UserErrors|否|使用者錯誤。|Count|總計|Microsoft.ServiceBus 的使用者錯誤。|EntityName |
|WSXNS|否|記憶體使用量 (已淘汰)|百分比|最大值|服務匯流排 premium 命名空間記憶體使用量計量。 此計量已過時。 請改用記憶體使用量（NamespaceMemoryUsage）度量。|無維度|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ActualCpu|否|ActualCpu|計數|Average|Milli> 核心中的實際 CPU 使用量|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ActualMemory|否|ActualMemory|位元組|Average|實際記憶體使用量（MB）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedCpu|否|AllocatedCpu|計數|Average|在 milli> 核心中配置給此容器的 Cpu|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|AllocatedMemory|否|AllocatedMemory|位元組|Average|配置給此容器的記憶體（MB）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|ApplicationStatus|否|ApplicationStatus|計數|Average|Service Fabric 網狀應用程式的狀態|ApplicationName，狀態|
|ContainerStatus|否|ContainerStatus|Count|Average|Service Fabric 網狀應用程式中的容器狀態|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName、Status|
|CpuUtilization|否|CpuUtilization|百分比|Average|此容器的 CPU 使用率（以 AllocatedCpu 的百分比表示）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|MemoryUtilization|否|MemoryUtilization|百分比|Average|此容器的 CPU 使用率（以 AllocatedCpu 的百分比表示）|ApplicationName、ServiceName、CodePackageName、ServiceReplicaName|
|RestartCount|否|RestartCount|計數|Average|Service Fabric 網狀應用程式中的容器重新開機計數|ApplicationName、Status、ServiceName、ServiceReplicaName、CodePackageName|
|ServiceReplicaStatus|否|ServiceReplicaStatus|計數|Average|Service Fabric 網狀應用程式中服務複本的健全狀況狀態|ApplicationName、Status、ServiceName、ServiceReplicaName|
|ServiceStatus|否|ServiceStatus|計數|Average|Service Fabric 網狀應用程式中服務的健全狀況狀態|ApplicationName、Status、ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ConnectionCount|是|連線計數|Count|最大值|使用者連線數量。|端點|
|InboundTraffic|是|輸入流量|位元組|總計|服務的輸入流量|無維度|
|MessageCount|是|訊息計數|Count|總計|訊息總量|無維度|
|OutboundTraffic|是|輸出流量|位元組|總計|服務的輸出流量|無維度|
|SystemErrors|是|系統錯誤|百分比|最大值|系統錯誤的百分比|無維度|
|UserErrors|是|使用者錯誤|百分比|最大值|使用者錯誤的百分比|無維度|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|avg_cpu_percent|是|CPU 百分比平均|百分比|Average|CPU 百分比平均|無維度|
|io_bytes_read|是|讀取的 IO 位元組|位元組|Average|讀取的 IO 位元組|無維度|
|io_bytes_written|是|寫入的 IO 位元組|位元組|Average|寫入的 IO 位元組|無維度|
|io_requests|是|IO 要求計數|計數|Average|IO 要求計數|無維度|
|reserved_storage_mb|是|保留的儲存體空間|計數|Average|保留的儲存體空間|無維度|
|storage_space_used_mb|是|使用的儲存體空間|計數|Average|使用的儲存體空間|無維度|
|virtual_core_count|是|虛擬核心計數|計數|Average|虛擬核心計數|無維度|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|active_queries|是|現用查詢|Count|總計|跨所有工作負載群組的作用中查詢。 僅適用于資料倉儲。|無維度|
|allocated_data_storage|是|已配置的資料空間|位元組|Average|已配置的資料儲存體。 不適用於資料倉儲。|無維度|
|app_cpu_billed|是|已計費應用程式 CPU|Count|總計|應用程式 CPU 計費。 適用于無伺服器資料庫。|無維度|
|app_cpu_percent|是|應用程式 CPU 百分比|百分比|Average|應用程式 CPU 百分比。 適用于無伺服器資料庫。|無維度|
|app_memory_percent|是|應用程式記憶體百分比|百分比|Average|應用程式記憶體百分比。 適用于無伺服器資料庫。|無維度|
|base_blob_size_bytes|是|基底 blob 儲存體大小|位元組|最大值|基底 blob 儲存體大小。 適用于超大規模資料庫資料庫。|無維度|
|blocked_by_firewall|是|遭到防火牆封鎖|Count|總計|遭到防火牆封鎖|無維度|
|cache_hit_percent|是|快取命中的百分比|百分比|最大值|快取命中百分比。 僅適用于資料倉儲。|無維度|
|cache_used_percent|是|已用快取的百分比|百分比|最大值|快取已使用百分比。 僅適用于資料倉儲。|無維度|
|connection_failed|是|失敗的連線|Count|總計|失敗的連線|無維度|
|connection_successful|是|成功的連線|Count|總計|成功的連線|無維度|
|cpu_limit|是|CPU 限制|計數|Average|CPU 限制。 適用于以 vCore 為基礎的資料庫。|無維度|
|cpu_percent|是|CPU 百分比|百分比|Average|CPU 百分比|無維度|
|cpu_used|是|已使用的 CPU|計數|Average|使用的 CPU。 適用于以 vCore 為基礎的資料庫。|無維度|
|deadlock|是|死結|Count|總計|發生. 不適用於資料倉儲。|無維度|
|diff_backup_size_bytes|是|差異備份儲存體大小|位元組|最大值|累計差異備份儲存體大小。 適用于以 vCore 為基礎的資料庫。 不適用於超大規模資料庫資料庫。|無維度|
|dtu_consumption_percent|是|DTU 百分比|百分比|Average|DTU 百分比。 適用于以 DTU 為基礎的資料庫。|無維度|
|dtu_limit|是|DTU 限制|Count|Average|DTU 限制。 適用于以 DTU 為基礎的資料庫。|無維度|
|dtu_used|是|已使用 DTU|計數|Average|使用的 DTU。 適用于以 DTU 為基礎的資料庫。|無維度|
|dw_backup_size_gb|是|資料儲存體大小 (GB)|Count|總計|資料儲存體大小是由您的資料和交易記錄檔的大小所組成。 度量會計入帳單的「儲存體」部分。 僅適用于資料倉儲。|無維度|
|dw_geosnapshot_size_gb|是|嚴重損壞修復儲存體大小（GB）|Count|總計|在您的帳單中，嚴重損壞修復儲存體大小會反映為「嚴重損壞修復儲存體」。 僅適用于資料倉儲。|無維度|
|dw_snapshot_size_gb|是|快照集儲存體大小（GB）|計數|總計|快照集儲存體大小是快照集所捕獲的累加變更大小，以建立使用者定義和自動還原點。 度量會計入帳單的「儲存體」部分。 僅適用于資料倉儲。|無維度|
|dwu_consumption_percent|是|DWU 百分比|百分比|最大值|DWU 百分比。 僅適用于資料倉儲。|無維度|
|dwu_limit|是|DWU 限制|Count|最大值|DWU 限制。 僅適用于資料倉儲。|無維度|
|dwu_used|是|已使用 DWU|Count|最大值|已使用 DWU。 僅適用于資料倉儲。|無維度|
|full_backup_size_bytes|是|完整備份儲存體大小|位元組|最大值|累計完整備份儲存體大小。 適用于以 vCore 為基礎的資料庫。 不適用於超大規模資料庫資料庫。|無維度|
|local_tempdb_usage_percent|是|本機 tempdb 百分比|百分比|Average|本機 tempdb 百分比。 僅適用于資料倉儲。|無維度|
|log_backup_size_bytes|是|記錄備份儲存體大小|位元組|最大值|累計記錄備份儲存體大小。 適用于以 vCore 為基礎的和超大規模資料庫資料庫。|無維度|
|log_write_percent|是|記錄 IO 百分比|百分比|Average|記錄 IO 百分比。 不適用於資料倉儲。|無維度|
|memory_usage_percent|是|記憶體百分比|百分比|最大值|記憶體百分比。 僅適用于資料倉儲。|無維度|
|physical_data_read_percent|是|資料 IO 百分比|百分比|Average|資料 IO 百分比|無維度|
|queued_queries|是|佇列查詢|計數|總計|跨所有工作負載群組的佇列查詢。 僅適用于資料倉儲。|無維度|
|sessions_percent|是|工作階段百分比|百分比|Average|會話百分比。 不適用於資料倉儲。|無維度|
|snapshot_backup_size_bytes|是|快照集備份儲存體大小|位元組|最大值|累計快照集備份儲存體大小。 適用于超大規模資料庫資料庫。|無維度|
|sqlserver_process_core_percent|是|SQL Server 處理常式核心百分比|百分比|最大值|CPU 使用量（以 SQL DB 進程的百分比表示）。 不適用於資料倉儲。|無維度|
|sqlserver_process_memory_percent|是|SQL Server 處理常式記憶體百分比|百分比|最大值|記憶體使用量，以 SQL DB 進程的百分比表示。 不適用於資料倉儲。|無維度|
|儲存|是|已使用的資料空間|位元組|最大值|使用的資料空間。 不適用於資料倉儲。|無維度|
|storage_percent|是|已使用的資料空間百分比|百分比|最大值|已使用的資料空間百分比。 不適用於資料倉儲或超大規模資料庫資料庫。|無維度|
|tempdb_data_size|是|TempDB 資料檔案大小 (KB)|Count|最大值|Tempdb 資料檔案大小（Kb）。 不適用於資料倉儲。|無維度|
|tempdb_log_size|是|Tempdb 記錄檔大小 (KB)|Count|最大值|Tempdb 記錄檔大小（Kb）。 不適用於資料倉儲。|無維度|
|tempdb_log_used_percent|是|Tempdb 使用的記錄百分比|百分比|最大值|使用的 Tempdb 百分比記錄。 不適用於資料倉儲。|無維度|
|wlg_active_queries|是|工作負載群組使用中查詢|計數|總計|工作負載群組內的使用中查詢。 僅適用于資料倉儲。|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|是|工作負載群組查詢逾時|計數|總計|工作負載群組已超時的查詢。 僅適用于資料倉儲。|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|是|依系統百分比配置的工作負載群組|百分比|最大值|配置的資源百分比，相對於每個工作負載群組的整個系統。 僅適用于資料倉儲。|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|是|依 cap 資源百分比配置的工作負載群組|百分比|最大值|針對每個工作負載群組，配置的資源百分比相對於指定的上限資源。 僅適用于資料倉儲。|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|是|有效的容量資源百分比|百分比|最大值|針對工作負載群組所允許的資源百分比固定限制，將配置給其他工作負載群組的有效最小資源百分比納入考慮。 僅適用于資料倉儲。|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|是|有效的最低資源百分比|百分比|最大值|針對工作負載群組保留和隔離的最小資源百分比，將服務層級的最小值納入考慮。 僅適用于資料倉儲。|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|是|已排入佇列的工作負載群組查詢|Count|總計|工作負載群組內已排入佇列的查詢。 僅適用于資料倉儲。|WorkloadGroupName, IsUserDefined|
|workers_percent|是|背景工作角色百分比|百分比|Average|背景工作百分比。 不適用於資料倉儲。|無維度|
|xtp_storage_percent|是|記憶體內部 OLTP 儲存體百分比|百分比|Average|記憶體內部 OLTP 儲存體百分比。 不適用於資料倉儲。|無維度|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|allocated_data_storage|是|已配置的資料空間|位元組|Average|已配置的資料空間|無維度|
|allocated_data_storage_percent|是|已配置的資料空間百分比|百分比|最大值|已配置的資料空間百分比|無維度|
|cpu_limit|是|CPU 限制|計數|Average|CPU 限制。 適用于以 vCore 為基礎的彈性集區。|無維度|
|cpu_percent|是|CPU 百分比|百分比|Average|CPU 百分比|無維度|
|cpu_used|是|已使用的 CPU|Count|Average|使用的 CPU。 適用于以 vCore 為基礎的彈性集區。|無維度|
|database_allocated_data_storage|否|已配置的資料空間|位元組|Average|已配置的資料空間|DatabaseResourceId|
|database_cpu_limit|否|CPU 限制|計數|Average|CPU 限制|DatabaseResourceId|
|database_cpu_percent|否|CPU 百分比|百分比|Average|CPU 百分比|DatabaseResourceId|
|database_cpu_used|否|已使用的 CPU|Count|Average|已使用的 CPU|DatabaseResourceId|
|database_dtu_consumption_percent|否|DTU 百分比|百分比|Average|DTU 百分比|DatabaseResourceId|
|database_eDTU_used|否|已使用 eDTU|Count|Average|已使用 eDTU|DatabaseResourceId|
|database_log_write_percent|否|記錄 IO 百分比|百分比|Average|記錄 IO 百分比|DatabaseResourceId|
|database_physical_data_read_percent|否|資料 IO 百分比|百分比|Average|資料 IO 百分比|DatabaseResourceId|
|database_sessions_percent|否|工作階段百分比|百分比|Average|工作階段百分比|DatabaseResourceId|
|database_storage_used|否|已使用的資料空間|位元組|Average|已使用的資料空間|DatabaseResourceId|
|database_workers_percent|否|背景工作角色百分比|百分比|Average|背景工作角色百分比|DatabaseResourceId|
|dtu_consumption_percent|是|DTU 百分比|百分比|Average|DTU 百分比。 適用于以 DTU 為基礎的彈性集區。|無維度|
|eDTU_limit|是|eDTU 限制|Count|Average|eDTU 限制。 適用于以 DTU 為基礎的彈性集區。|無維度|
|eDTU_used|是|已使用 eDTU|Count|Average|使用的 eDTU。 適用于以 DTU 為基礎的彈性集區。|無維度|
|log_write_percent|是|記錄 IO 百分比|百分比|Average|記錄 IO 百分比|無維度|
|physical_data_read_percent|是|資料 IO 百分比|百分比|Average|資料 IO 百分比|無維度|
|sessions_percent|是|工作階段百分比|百分比|Average|工作階段百分比|無維度|
|sqlserver_process_core_percent|是|SQL Server 處理常式核心百分比|百分比|最大值|CPU 使用量（以 SQL DB 進程的百分比表示）。 適用于彈性集區。|無維度|
|sqlserver_process_memory_percent|是|SQL Server 處理常式記憶體百分比|百分比|最大值|記憶體使用量，以 SQL DB 進程的百分比表示。 適用于彈性集區。|無維度|
|storage_limit|是|資料大小上限|位元組|Average|資料大小上限|無維度|
|storage_percent|是|已使用的資料空間百分比|百分比|Average|已使用的資料空間百分比|無維度|
|storage_used|是|已使用的資料空間|位元組|Average|已使用的資料空間|無維度|
|tempdb_data_size|是|TempDB 資料檔案大小 (KB)|Count|最大值|TempDB 資料檔案大小 (KB)|無維度|
|tempdb_log_size|是|Tempdb 記錄檔大小 (KB)|Count|最大值|Tempdb 記錄檔大小 (KB)|無維度|
|tempdb_log_used_percent|是|Tempdb 使用的記錄百分比|百分比|最大值|Tempdb 使用的記錄百分比|無維度|
|workers_percent|是|背景工作角色百分比|百分比|Average|背景工作角色百分比|無維度|
|xtp_storage_percent|是|記憶體內部 OLTP 儲存體百分比|百分比|Average|記憶體內部 OLTP 儲存體百分比|無維度|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|
|輸出|是|輸出|位元組|總計|輸出資料量。 此數目包括從 Azure 儲存體到外部用戶端的輸出，以及 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均時間。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|交易|是|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName/、Authentication|
|UsedCapacity|否|已使用容量|位元組|Average|儲存體帳戶所使用的儲存體數量。 若為標準儲存體帳戶，則為 Blob、資料表、檔案和佇列所使用的容量總和。 對於 premium 儲存體帳戶和 Blob 儲存體帳戶，它與 BlobCapacity 或 FileCapacity 相同。|無維度|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|
|BlobCapacity|否|Blob 容量|位元組|Average|儲存體帳戶之 Blob 服務所使用的儲存體量 (位元組)。|BlobType，層|
|BlobCount|否|Blob 計數|計數|Average|儲存體帳戶中所儲存的 Blob 物件數目。|BlobType，層|
|BlobProvisionedSize|否|Blob 布建大小|位元組|Average|在儲存體帳戶的 Blob 服務中布建的儲存體數量（以位元組為單位）。|BlobType，層|
|ContainerCount|是|Blob 容器計數|Count|Average|儲存體帳戶中的容器數目。|無維度|
|輸出|是|輸出|位元組|總計|輸出資料量。 此數目包括從 Azure 儲存體到外部用戶端的輸出，以及 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|IndexCapacity|否|索引容量|位元組|Average|Azure Data Lake Storage Gen2 階層式索引所使用的儲存體數量。|無維度|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均時間。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|交易|是|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName/、Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication、檔案共用|
|輸出|是|輸出|位元組|總計|輸出資料量。 此數目包括從 Azure 儲存體到外部用戶端的輸出，以及 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication、檔案共用|
|FileCapacity|否|檔案容量|位元組|Average|儲存體帳戶所使用的檔案儲存體數量。|FileShare|
|FileCount|否|檔案計數|計數|Average|儲存體帳戶中的檔案數目。|FileShare|
|FileShareCapacityQuota|否|檔案共用容量配額|位元組|Average|Azure 檔案儲存體服務可使用的儲存體數量上限（以位元組為單位）。|FileShare|
|FileShareCount|否|檔案共用計數|Count|Average|儲存體帳戶中的檔案共用數目。|無維度|
|FileShareProvisionedIOPS|否|檔案共用已布建的 IOPS|位元組|Average|Premium 檔案儲存體帳戶中 premium 檔案共用的已布建 IOPS 基準數目。 此數位是根據共用容量的布建大小（配額）計算而得。|FileShare|
|FileShareSnapshotCount|否|檔案共用快照集計數|計數|Average|儲存體帳戶之檔案服務中的共用上存在的快照集數目。|FileShare|
|FileShareSnapshotSize|否|檔案共用快照集大小|位元組|Average|儲存體帳戶之檔案服務中的快照集所使用的儲存體數量（以位元組為單位）。|FileShare|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication、檔案共用|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication、檔案共用|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均時間。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication、檔案共用|
|交易|是|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName、Authentication、檔案共用|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|
|輸出|是|輸出|位元組|總計|輸出資料量。 此數目包括從 Azure 儲存體到外部用戶端的輸出，以及 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|QueueCapacity|是|佇列容量|位元組|Average|儲存體帳戶所使用的佇列儲存體數量。|無維度|
|QueueCount|是|佇列計數|計數|Average|儲存體帳戶中的佇列數目。|無維度|
|QueueMessageCount|是|佇列訊息計數|Count|Average|儲存體帳戶中未到期的佇列訊息數目。|無維度|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均時間。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|交易|是|交易|Count|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName/、Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|可用性|是|可用性|百分比|Average|儲存體服務或所指定 API 作業的可用性百分比。 可用性的計算方式是將 TotalBillableRequests 值除以適用要求數目，包括產生意外錯誤的要求。 所有意外錯誤都會導致儲存體或所指定 API 作業的可用性降低。|GeoType、ApiName、Authentication|
|輸出|是|輸出|位元組|總計|輸出資料量。 此數目包括從 Azure 儲存體到外部用戶端的輸出，以及 Azure 內的輸出。 因此，此數目未反映可收費的輸出。|GeoType、ApiName、Authentication|
|輸入|是|輸入|位元組|總計|輸入資料量 (位元組)。 此數目包括從外部用戶端輸入到 Azure 儲存體與 Azure 內的輸入。|GeoType、ApiName、Authentication|
|SuccessE2ELatency|是|成功 E2E 延遲|毫秒|Average|向儲存體服務或所指定 API 作業發出之成功要求的平均端對端延遲 (毫秒)。 此值包括 Azure 儲存體內讀取要求、傳送回應及接收回應認可的必要處理時間。|GeoType、ApiName、Authentication|
|SuccessServerLatency|是|成功伺服器延遲|毫秒|Average|Azure 儲存體用來處理成功要求的平均時間。 此值不包括在 SuccessE2ELatency 中指定的網路延遲。|GeoType、ApiName、Authentication|
|TableCapacity|是|資料表容量|位元組|Average|儲存體帳戶所使用的表格儲存體數量。|無維度|
|TableCount|是|資料表計數|計數|Average|儲存體帳戶中的表格數目。|無維度|
|TableEntityCount|是|資料表實體計數|Count|Average|儲存體帳戶中的表格實體數目。|無維度|
|交易|是|交易|計數|總計|向儲存體服務或所指定 API 作業傳送的要求數。 此數目包括成功與失敗的要求，以及產生錯誤的要求。 使用 ResponseType 維度來取得不同類型回應的數目。|ResponseType、GeoType、ApiName/、Authentication|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|是|同步工作階段結果|計數|Average|每次伺服器端點成功完成與雲端端點的同步會話時，會將值記錄為1的度量|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncBatchTransferredFileBytes|是|同步的位元組|位元組|總計|同步會話的已傳輸檔案大小總計|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|是|雲端階層處理重新叫用大小 (依應用程式)|位元組|總計|應用程式重新叫用的資料大小|SyncGroupName、ServerName、ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|是|雲端階層處理重新叫用大小|位元組|總計|重新叫用的資料大小|SyncGroupName、ServerName|
|StorageSyncRecallIOTotalSizeBytes|是|雲端階層處理重新叫用|位元組|總計|伺服器回收的資料大小總計|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|是|雲端階層處理重新叫用輸送量|每秒位元組|Average|資料重新叫用輸送量的大小|SyncGroupName、ServerName|
|StorageSyncServerHeartbeat|是|伺服器線上狀態|Count|最大值|每次 resigtered 伺服器順利記錄與雲端端點的心跳時，其值會記錄1的度量|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|是|同步的檔案|計數|總計|已同步處理的檔案計數|SyncGroupName、ServerEndpointName、SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|是|檔案無法同步|Count|總計|無法同步的檔案計數|SyncGroupName、ServerEndpointName、SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ServerHeartbeat|是|伺服器線上狀態|Count|最大值|每次 resigtered 伺服器順利記錄與雲端端點的心跳時，其值會記錄1的度量|ServerResourceId、ServerName|
|ServerRecallIOTotalSizeBytes|是|雲端階層處理重新叫用|位元組|總計|伺服器回收的資料大小總計|ServerResourceId、ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|是|同步的位元組|位元組|總計|同步會話的已傳輸檔案大小總計|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|是|同步的檔案|Count|總計|已同步處理的檔案計數|SyncGroupName、ServerEndpointName、SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|是|檔案無法同步|Count|總計|無法同步的檔案計數|SyncGroupName、ServerEndpointName、SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|是|同步的位元組|位元組|總計|同步會話的已傳輸檔案大小總計|ServerEndpointName、SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|是|同步的檔案|Count|總計|已同步處理的檔案計數|ServerEndpointName、SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|是|檔案無法同步|Count|總計|無法同步的檔案計數|ServerEndpointName、SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|是|失敗的函式要求|Count|總計|失敗的函式要求|LogicalName、PartitionId|
|AMLCalloutInputEvents|是|函式事件|Count|總計|函式事件|LogicalName、PartitionId|
|AMLCalloutRequests|是|函式要求|Count|總計|函式要求|LogicalName、PartitionId|
|ConversionErrors|是|資料轉換錯誤|Count|總計|資料轉換錯誤|LogicalName、PartitionId|
|DeserializationError|是|輸入還原序列化錯誤|Count|總計|輸入還原序列化錯誤|LogicalName、PartitionId|
|DroppedOrAdjustedEvents|是|次序錯誤事件|計數|總計|次序錯誤事件|LogicalName、PartitionId|
|EarlyInputEvents|是|早期輸入事件|計數|總計|早期輸入事件|LogicalName、PartitionId|
|錯誤|是|執行階段錯誤|計數|總計|執行階段錯誤|LogicalName、PartitionId|
|InputEventBytes|是|輸入事件位元組|位元組|總計|輸入事件位元組|LogicalName、PartitionId|
|InputEvents|是|輸入事件|計數|總計|輸入事件|LogicalName、PartitionId|
|InputEventsSourcesBacklogged|是|待處理輸入事件數|Count|最大值|待處理輸入事件數|LogicalName、PartitionId|
|InputEventsSourcesPerSecond|是|收到的輸入來源數|計數|總計|收到的輸入來源數|LogicalName、PartitionId|
|LateInputEvents|是|延遲輸入事件|計數|總計|延遲輸入事件|LogicalName、PartitionId|
|OutputEvents|是|輸出事件|Count|總計|輸出事件|LogicalName、PartitionId|
|OutputWatermarkDelaySeconds|是|浮水印延遲秒數|秒|最大值|浮水印延遲秒數|LogicalName、PartitionId|
|ResourceUtilization|是|SU % 使用率|百分比|最大值|SU % 使用率|LogicalName、PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|OrchestrationActivityRunsEnded|否|活動執行結束|計數|總計|成功、失敗或已取消之協調流程活動的計數|Result、FailureType、Activity、ActivityType、Pipeline|
|OrchestrationPipelineRunsEnded|否|管線執行結束|計數|總計|成功、失敗或已取消之協調流程管線執行的計數|結果、FailureType、管線|
|OrchestrationTriggersEnded|否|觸發程式已結束|計數|總計|成功、失敗或已取消之協調流程觸發程式的計數|Result、FailureType、Trigger|
|SQLOnDemandLoginAttempts|否|登入嘗試|計數|總計|成功或失敗的登入嘗試計數|結果|
|SQLOnDemandQueriesEnded|否|查詢已結束|計數|總計|成功、失敗或已取消的查詢計數|結果|
|SQLOnDemandQueryProcessedBytes|否|已處理的資料量|位元組|總計|查詢所處理的資料量|無維度|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Synapse/workspace/bigDataPools

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|CoresCapacity|否|核心容量|Count|最大值|核心容量|無維度|
|MemoryCapacityGB|否|記憶體容量（GB）|Count|最大值|記憶體容量（GB）|無維度|
|SparkJobsEnded|是|結束的應用程式|計數|總計|已結束的應用程式計數|JobType、Jobresult.txt|


## <a name="microsoftsynapseworkspacessqlpools"></a>Synapse/workspace/sqlPools

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|否|調適型快取命中百分比|百分比|最大值|測量工作負載使用適應性快取的程度。 將此計量與快取點擊百分比計量搭配使用，以判斷是否要調整額外容量，或重新執行工作負載以序列化快取|無維度|
|AdaptiveCacheUsedPercent|否|調適型快取已使用百分比|百分比|最大值|測量工作負載使用適應性快取的程度。 使用此計量搭配 [快取使用百分比] 計量來決定是否要調整額外容量，或重新執行工作負載以序列化快取|無維度|
|連接|是|連接|Count|總計|SQL 集區的登入總數計數|結果|
|ConnectionsBlockedByFirewall|否|防火牆封鎖的連線|計數|總計|防火牆規則封鎖的連線計數。 若計數很高，請重新流覽 SQL 集區的存取控制原則，並監視這些連接|無維度|
|DWULimit|否|DWU 限制|Count|最大值|SQL 集區的服務等級目標|無維度|
|DWUUsed|否|已使用 DWU|Count|最大值|表示跨 SQL 集區使用方式的高階標記法。 測量依據 DWU 限制 * DWU 百分比|無維度|
|DWUUsedPercent|否|DWU 已使用百分比|百分比|最大值|表示跨 SQL 集區使用方式的高階標記法。 以 CPU 百分比與資料 IO 百分比之間的最大值來測量|無維度|
|LocalTempDBUsedPercent|否|本機 tempdb 使用百分比|百分比|最大值|跨所有計算節點的本機 tempdb 使用率-值會每五分鐘發出一次|無維度|
|MemoryUsedPercent|否|記憶體使用百分比|百分比|最大值|SQL 集區中所有節點的記憶體使用率|無維度|
|wlg_effective_min_resource_percent|是|有效的最低資源百分比|百分比|最低需求|允許考慮服務等級和工作負載群組設定的有效最小資源百分比設定。 有效的 min_percentage_resource 可以在較低的服務層級上調整|IsUserDefined、WorkloadGroup|
|WLGActiveQueries|否|工作負載群組使用中查詢|計數|總計|工作負載群組內的使用中查詢。 使用未篩選的此計量和解除會顯示在系統上執行的所有使用中查詢|IsUserDefined、WorkloadGroup|
|WLGActiveQueriesTimeouts|否|工作負載群組查詢逾時|計數|總計|查詢已超時的工作負載群組。此計量所報告的查詢超時，只有在查詢開始執行之後（不包括因鎖定或資源等候而造成的等候時間）|IsUserDefined、WorkloadGroup|
|WLGAllocationByMaxResourcePercent|否|依最大資源百分比配置的工作負載群組|百分比|最大值|針對每個工作負載群組，顯示相對於有效 cap 資源百分比的資源配置百分比。 此度量可提供工作負載群組的有效使用率|IsUserDefined、WorkloadGroup|
|WLGAllocationBySystemPercent|否|依系統百分比配置的工作負載群組|百分比|最大值|相對於整個系統的資源配置百分比|IsUserDefined、WorkloadGroup|
|WLGEffectiveCapResourcePercent|是|有效的容量資源百分比|百分比|最大值|工作負載群組的有效上限資源百分比。 如果有其他 min_percentage_resource > 0 的工作負載群組，effective_cap_percentage_resource 會按比例降低|IsUserDefined、WorkloadGroup|
|WLGQueuedQueries|否|已排入佇列的工作負載群組查詢|計數|總計|達到最大並行限制之後，佇列的要求累計計數|IsUserDefined、WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|是|輸入接收的位元組|位元組|總計|從所有事件來源讀取的位元組計數|無維度|
|IngressReceivedInvalidMessages|是|輸入接收的無效訊息|計數|總計|從所有事件中樞或 IoT 中樞事件來源讀取的無效訊息計數|無維度|
|IngressReceivedMessages|是|輸入接收的訊息|計數|總計|從所有事件中樞或 IoT 中樞事件來源讀取的訊息計數|無維度|
|IngressReceivedMessagesCountLag|是|輸入接收的訊息計數延遲|計數|Average|事件來源資料分割中最後排入佇列之訊息的序號與輸入中所處理訊息的序號之間的差異|無維度|
|IngressReceivedMessagesTimeLag|是|輸入接收的訊息時間延遲|秒|最大值|訊息在事件來源中加入佇列的時間與在輸入中處理的時間之間的差異|無維度|
|IngressStoredBytes|是|輸入儲存的位元組|位元組|總計|已成功處理且可供查詢的事件總大小|無維度|
|IngressStoredEvents|是|輸入儲存的事件|計數|總計|已成功處理且可供查詢的壓平合併事件計數|無維度|
|WarmStorageMaxProperties|是|暖儲存空間上限屬性|Count|最大值|適用于 S1/S2 SKU 的環境所允許的屬性數目上限，以及 PAYG SKU 的暖存放區所允許的最大屬性數目|無維度|
|WarmStorageUsedProperties|是|暖儲存體使用的屬性 |Count|最大值|適用于 S1/S2 SKU 的環境所使用的屬性數目，以及 PAYG SKU 的暖存放區所使用的屬性數目|無維度|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|是|輸入接收的位元組|位元組|總計|從事件來源讀取的位元組計數|無維度|
|IngressReceivedInvalidMessages|是|輸入接收的無效訊息|計數|總計|從事件來源讀取的無效訊息計數|無維度|
|IngressReceivedMessages|是|輸入接收的訊息|計數|總計|從事件來源讀取的訊息計數|無維度|
|IngressReceivedMessagesCountLag|是|輸入接收的訊息計數延遲|計數|Average|事件來源資料分割中最後排入佇列之訊息的序號與輸入中所處理訊息的序號之間的差異|無維度|
|IngressReceivedMessagesTimeLag|是|輸入接收的訊息時間延遲|秒|最大值|訊息在事件來源中加入佇列的時間與在輸入中處理的時間之間的差異|無維度|
|IngressStoredBytes|是|輸入儲存的位元組|位元組|總計|已成功處理且可供查詢的事件總大小|無維度|
|IngressStoredEvents|是|輸入儲存的事件|計數|總計|已成功處理且可供查詢的壓平合併事件計數|無維度|
|WarmStorageMaxProperties|是|暖儲存空間上限屬性|Count|最大值|適用于 S1/S2 SKU 的環境所允許的屬性數目上限，以及 PAYG SKU 的暖存放區所允許的最大屬性數目|無維度|
|WarmStorageUsedProperties|是|暖儲存體使用的屬性 |Count|最大值|適用于 S1/S2 SKU 的環境所使用的屬性數目，以及 PAYG SKU 的暖存放區所使用的屬性數目|無維度|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|Disk Read Bytes|是|Disk Read Bytes|位元組|總計|由於在取樣期間讀取作業的總磁片輸送量。|無維度|
|Disk Read Operations/Sec|是|Disk Read Operations/Sec|每秒計數|Average|上一個取樣期間內 IO 讀取作業的平均數目。 請注意，這些作業可能是可變大小的。|無維度|
|Disk Write Bytes|是|Disk Write Bytes|位元組|總計|因取樣期間的寫入作業而造成的總磁片輸送量。|無維度|
|Disk Write Operations/Sec|是|Disk Write Operations/Sec|每秒計數|Average|上一個範例期間的 IO 寫入作業平均數目。 請注意，這些作業可能是可變大小的。|無維度|
|DiskReadBytesPerSecond|是|Disk Read Bytes/Sec|每秒位元組|Average|在取樣期間讀取作業所造成的平均磁片輸送量。|無維度|
|DiskReadLatency|是|磁碟讀取延遲|毫秒|Average|總讀取延遲。 裝置和核心讀取延遲的總和。|無維度|
|DiskReadOperations|是|磁碟讀取作業|Count|總計|上一個範例期間的 IO 讀取作業數目。 請注意，這些作業可能是可變大小的。|無維度|
|DiskWriteBytesPerSecond|是|Disk Write Bytes/Sec|每秒位元組|Average|因取樣期間的寫入作業而造成的平均磁片輸送量。|無維度|
|DiskWriteLatency|是|磁碟寫入延遲|毫秒|Average|總寫入延遲。 裝置和核心寫入延遲的總和。|無維度|
|DiskWriteOperations|是|磁碟寫入作業|Count|總計|上一個範例期間的 IO 寫入作業數目。 請注意，這些作業可能是可變大小的。|無維度|
|MemoryActive|是|作用中記憶體|位元組|Average|VM 在過去小型時間範圍內使用的記憶體數量。 這是 VM 目前所需記憶體數量的 "true"。 其他、未使用的記憶體可能會交換或膨脹，而不會影響到來賓的效能。|無維度|
|MemoryGranted|是|已授與的記憶體|位元組|Average|主機授與 VM 的記憶體數量。 記憶體在觸及一次之後才會授與給主機，而且如果 VMkernel 需要記憶體，則會交換或膨脹。|無維度|
|MemoryUsed|是|已使用的記憶體|位元組|Average|VM 使用的電腦記憶體數量。|無維度|
|Network In|是|Network In|位元組|總計|已接收流量的網路輸送量總計。|無維度|
|Network Out|是|Network Out|位元組|總計|傳輸流量的網路輸送量總計。|無維度|
|NetworkInBytesPerSecond|是|網路輸入位元組數/秒|每秒位元組|Average|已接收流量的平均網路輸送量。|無維度|
|NetworkOutBytesPerSecond|是|網路輸出位元組數/秒|每秒位元組|Average|傳輸流量的平均網路輸送量。|無維度|
|CPU 百分比|是|Percentage CPU|百分比|Average|CPU 使用率。 此值是以100% 回報，代表系統上的所有處理器核心。 例如，使用四核心系統50% 的雙向 VM，完全使用兩個核心。|無維度|
|PercentageCpuReady|是|CPU 已就緒百分比|毫秒|總計|「就緒時間」是等待 CPU 在過去的更新間隔中變成可用的時間。|無維度|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|ActiveRequests|是|使用中的要求|Count|總計|使用中的要求|執行個體|
|AverageResponseTime|是|平均回應時間|秒|Average|平均回應時間|執行個體|
|BytesReceived|是|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|是|資料輸出|位元組|總計|資料輸出|執行個體|
|CpuPercentage|是|CPU 百分比|百分比|Average|CPU 百分比|執行個體|
|DiskQueueLength|是|磁碟佇列長度|計數|Average|磁碟佇列長度|執行個體|
|Http101|是|Http 101|Count|總計|Http 101|執行個體|
|Http2xx|是|Http 2xx|Count|總計|Http 2xx|執行個體|
|Http3xx|是|Http 3xx|Count|總計|Http 3xx|執行個體|
|Http401|是|Http 401|Count|總計|Http 401|執行個體|
|Http403|是|Http 403|Count|總計|Http 403|執行個體|
|Http404|是|Http 404|Count|總計|Http 404|執行個體|
|Http406|是|Http 406|Count|總計|Http 406|執行個體|
|Http4xx|是|Http 4xx|Count|總計|Http 4xx|執行個體|
|Http5xx|是|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|HttpQueueLength|是|Http 佇列長度|Count|Average|Http 佇列長度|執行個體|
|LargeAppServicePlanInstances|是|大型 App Service 方案背景工作角色|計數|Average|大型 App Service 方案背景工作角色|無維度|
|MediumAppServicePlanInstances|是|中型 App Service 方案背景工作角色|計數|Average|中型 App Service 方案背景工作角色|無維度|
|MemoryPercentage|是|記憶體百分比|百分比|Average|記憶體百分比|執行個體|
|Requests|是|Requests|Count|總計|Requests|執行個體|
|SmallAppServicePlanInstances|是|小型 App Service 方案背景工作角色|計數|Average|小型 App Service 方案背景工作角色|無維度|
|TotalFrontEnds|是|前端總數|計數|Average|前端總數|無維度|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|CpuPercentage|是|CPU 百分比|百分比|Average|CPU 百分比|執行個體|
|MemoryPercentage|是|記憶體百分比|百分比|Average|記憶體百分比|執行個體|
|WorkersAvailable|是|可用的背景工作角色|計數|Average|可用的背景工作角色|無維度|
|WorkersTotal|是|背景工作角色總數|計數|Average|背景工作角色總數|無維度|
|WorkersUsed|是|已使用的背景工作角色|Count|Average|已使用的背景工作角色|無維度|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|BytesReceived|是|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|是|資料輸出|位元組|總計|資料輸出|執行個體|
|CpuPercentage|是|CPU 百分比|百分比|Average|CPU 百分比|執行個體|
|DiskQueueLength|是|磁碟佇列長度|Count|Average|磁碟佇列長度|執行個體|
|HttpQueueLength|是|Http 佇列長度|Count|Average|Http 佇列長度|執行個體|
|MemoryPercentage|是|記憶體百分比|百分比|Average|記憶體百分比|執行個體|
|SocketInboundAll|是|SocketInboundAll|Count|Average|SocketInboundAll|執行個體|
|SocketLoopback|是|SocketLoopback|Count|Average|SocketLoopback|執行個體|
|SocketOutboundAll|是|SocketOutboundAll|Count|Average|SocketOutboundAll|執行個體|
|SocketOutboundEstablished|是|SocketOutboundEstablished|Count|Average|SocketOutboundEstablished|執行個體|
|SocketOutboundTimeWait|是|SocketOutboundTimeWait|Count|Average|SocketOutboundTimeWait|執行個體|
|TcpCloseWait|是|等候關閉的 TCP|Count|Average|等候關閉的 TCP|執行個體|
|TcpClosing|是|正在關閉的 TCP|Count|Average|正在關閉的 TCP|執行個體|
|TcpEstablished|是|已建立的 TCP|Count|Average|已建立的 TCP|執行個體|
|TcpFinWait1|是|TCP 完成等候 1|Count|Average|TCP 完成等候 1|執行個體|
|TcpFinWait2|是|TCP 完成等候 2|Count|Average|TCP 完成等候 2|執行個體|
|TcpLastAck|是|TCP 上次認可|Count|Average|TCP 上次認可|執行個體|
|TcpSynReceived|是|已接收的 TCP 同步|Count|Average|已接收的 TCP 同步|執行個體|
|TcpSynSent|是|已傳送的 TCP 同步|Count|Average|已傳送的 TCP 同步|執行個體|
|TcpTimeWait|是|TCP 等候時間|Count|Average|TCP 等候時間|執行個體|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (不包括函式) 

> [!NOTE]
> **檔案系統使用**方式是全域推出的新計量，除非您已列入個人預覽版的白名單中，否則不會有任何資料。

> [!IMPORTANT]
> **平均回應時間**將會被取代，以避免與計量匯總產生混淆。 使用**回應時間**做為取代。

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AppConnections|是|連接|Count|Average|連接|執行個體|
|AverageMemoryWorkingSet|是|平均記憶體工作集|位元組|Average|平均記憶體工作集|執行個體|
|AverageResponseTime|是|平均回應時間 **（已淘汰）**|秒|Average|平均回應時間|執行個體|
|BytesReceived|是|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|是|資料輸出|位元組|總計|資料輸出|執行個體|
|CpuTime|是|CPU 時間|秒|總計|CPU 時間|執行個體|
|CurrentAssemblies|是|目前的組件|Count|Average|目前的組件|執行個體|
|FileSystemUsage|是|檔案系統使用方式|位元組|Average|檔案系統使用方式|無維度|
|Gen0Collections|是|Gen 0 記憶體回收|Count|總計|Gen 0 記憶體回收|執行個體|
|Gen1Collections|是|Gen 1 記憶體回收|Count|總計|Gen 1 記憶體回收|執行個體|
|Gen2Collections|是|Gen 2 記憶體回收|Count|總計|Gen 2 記憶體回收|執行個體|
|處理|是|控制代碼計數|計數|Average|控制代碼計數|執行個體|
|HealthCheckStatus|是|健康情況檢查狀態|Count|Average|健康情況檢查狀態|執行個體|
|Http101|是|Http 101|Count|總計|Http 101|執行個體|
|Http2xx|是|Http 2xx|Count|總計|Http 2xx|執行個體|
|Http3xx|Http 3xx|Count|總計|Http 3xx|執行個體|
|Http401|是|Http 401|Count|總計|Http 401|執行個體|
|Http403|是|Http 403|Count|總計|Http 403|執行個體|
|Http404|是|Http 404|Count|總計|Http 404|執行個體|
|Http406|是|Http 406|Count|總計|Http 406|執行個體|
|Http4xx|是|Http 4xx|Count|總計|Http 4xx|執行個體|
|Http5xx|是|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|HttpResponseTime|是|回應時間|秒|Average|回應時間|執行個體|
|IoOtherBytesPerSecond|是|每秒的 IO 其他位元組數|每秒位元組|總計|每秒的 IO 其他位元組數|執行個體|
|IoOtherOperationsPerSecond|是|每秒的 IO 其他作業數|每秒位元組|總計|每秒的 IO 其他作業數|執行個體|
|IoReadBytesPerSecond|是|每秒的 IO 讀取位元組數|每秒位元組|總計|每秒的 IO 讀取位元組數|執行個體|
|IoReadOperationsPerSecond|是|每秒的 IO 讀取作業數|每秒位元組|總計|每秒的 IO 讀取作業數|執行個體|
|IoWriteBytesPerSecond|是|每秒的 IO 寫入位元組數|每秒位元組|總計|每秒的 IO 寫入位元組數|執行個體|
|IoWriteOperationsPerSecond|是|每秒的 IO 寫入作業數|每秒位元組|總計|每秒的 IO 寫入作業數|執行個體|
|MemoryWorkingSet|是|記憶體工作集|位元組|Average|記憶體工作集|執行個體|
|PrivateBytes|是|私用位元組|位元組|Average|私用位元組|執行個體|
|Requests|是|Requests|Count|總計|Requests|執行個體|
|RequestsInApplicationQueue|是|應用程式佇列中的要求數|計數|Average|應用程式佇列中的要求數|執行個體|
|Threads|是|執行緒計數|計數|Average|執行緒計數|執行個體|
|TotalAppDomains|是|應用程式網域總計|計數|Average|應用程式網域總計|執行個體|
|TotalAppDomainsUnloaded|是|已卸載的應用程式網域總計|Count|Average|已卸載的應用程式網域總計|執行個體|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (函式)

> [!NOTE]
> **檔案系統使用**方式是全域推出的新計量，除非您已列入個人預覽版的白名單中，否則不會有任何資料。

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AverageMemoryWorkingSet|是|平均記憶體工作集|位元組|Average|平均記憶體工作集|執行個體|
|BytesReceived|是|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|是|資料輸出|位元組|總計|資料輸出|執行個體|
|CurrentAssemblies|是|目前的組件|計數|Average|目前的組件|執行個體|
|FileSystemUsage|是|檔案系統使用方式|位元組|Average|檔案系統使用方式|無維度|
|FunctionExecutionCount|是|函式執行計數|Count|總計|函式執行計數|執行個體|
|FunctionExecutionUnits|是|函式執行單位|Count|總計|[函式執行單位](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|執行個體|
|Gen0Collections|是|Gen 0 記憶體回收|Count|總計|Gen 0 記憶體回收|執行個體|
|Gen1Collections|是|Gen 1 記憶體回收|Count|總計|Gen 1 記憶體回收|執行個體|
|Gen2Collections|是|Gen 2 記憶體回收|Count|總計|Gen 2 記憶體回收|執行個體|
|HealthCheckStatus|是|健康情況檢查狀態|Count|Average|健康情況檢查狀態|執行個體|
|Http5xx|是|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|IoOtherBytesPerSecond|是|每秒的 IO 其他位元組數|每秒位元組|總計|每秒的 IO 其他位元組數|執行個體|
|IoOtherOperationsPerSecond|是|每秒的 IO 其他作業數|每秒位元組|總計|每秒的 IO 其他作業數|執行個體|
|IoReadBytesPerSecond|是|每秒的 IO 讀取位元組數|每秒位元組|總計|每秒的 IO 讀取位元組數|執行個體|
|IoReadOperationsPerSecond|是|每秒的 IO 讀取作業數|每秒位元組|總計|每秒的 IO 讀取作業數|執行個體|
|IoWriteBytesPerSecond|是|每秒的 IO 寫入位元組數|每秒位元組|總計|每秒的 IO 寫入位元組數|執行個體|
|IoWriteOperationsPerSecond|是|每秒的 IO 寫入作業數|每秒位元組|總計|每秒的 IO 寫入作業數|執行個體|
|MemoryWorkingSet|是|記憶體工作集|位元組|Average|記憶體工作集|執行個體|
|PrivateBytes|是|私用位元組|位元組|Average|私用位元組|執行個體|
|RequestsInApplicationQueue|是|應用程式佇列中的要求數|Count|Average|應用程式佇列中的要求數|執行個體|
|TotalAppDomains|是|應用程式網域總計|Count|Average|應用程式網域總計|執行個體|
|TotalAppDomainsUnloaded|是|已卸載的應用程式網域總計|Count|Average|已卸載的應用程式網域總計|執行個體|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|說明|維度|
|---|---|---|---|---|---|---|
|AppConnections|是|連接|Count|Average|連接|執行個體|
|AverageMemoryWorkingSet|是|平均記憶體工作集|位元組|Average|平均記憶體工作集|執行個體|
|AverageResponseTime|是|平均回應時間|秒|Average|平均回應時間|執行個體|
|BytesReceived|是|資料輸入|位元組|總計|資料輸入|執行個體|
|BytesSent|是|資料輸出|位元組|總計|資料輸出|執行個體|
|CpuTime|是|CPU 時間|秒|總計|CPU 時間|執行個體|
|CurrentAssemblies|是|目前的組件|計數|Average|目前的組件|執行個體|
|FileSystemUsage|是|檔案系統使用方式|位元組|Average|檔案系統使用方式|無維度|
|FunctionExecutionCount|是|函式執行計數|Count|總計|函式執行計數|執行個體|
|FunctionExecutionUnits|是|函式執行單位|Count|總計|函式執行單位|執行個體|
|Gen0Collections|是|Gen 0 記憶體回收|Count|總計|Gen 0 記憶體回收|執行個體|
|Gen1Collections|是|Gen 1 記憶體回收|Count|總計|Gen 1 記憶體回收|執行個體|
|Gen2Collections|是|Gen 2 記憶體回收|Count|總計|Gen 2 記憶體回收|執行個體|
|處理|是|控制代碼計數|計數|Average|控制代碼計數|執行個體|
|HealthCheckStatus|是|健康情況檢查狀態|Count|Average|健康情況檢查狀態|執行個體|
|Http101|是|Http 101|Count|總計|Http 101|執行個體|
|Http2xx|是|Http 2xx|Count|總計|Http 2xx|執行個體|
|Http3xx|是|Http 3xx|Count|總計|Http 3xx|執行個體|
|Http401|是|Http 401|Count|總計|Http 401|執行個體|
|Http403|是|Http 403|Count|總計|Http 403|執行個體|
|Http404|是|Http 404|Count|總計|Http 404|執行個體|
|Http406|是|Http 406|Count|總計|Http 406|執行個體|
|Http4xx|是|Http 4xx|Count|總計|Http 4xx|執行個體|
|Http5xx|是|Http 伺服器錯誤|Count|總計|Http 伺服器錯誤|執行個體|
|HttpResponseTime|是|回應時間|秒|Average|回應時間|執行個體|
|IoOtherBytesPerSecond|是|每秒的 IO 其他位元組數|每秒位元組|總計|每秒的 IO 其他位元組數|執行個體|
|IoOtherOperationsPerSecond|是|每秒的 IO 其他作業數|每秒位元組|總計|每秒的 IO 其他作業數|執行個體|
|IoReadBytesPerSecond|是|每秒的 IO 讀取位元組數|每秒位元組|總計|每秒的 IO 讀取位元組數|執行個體|
|IoReadOperationsPerSecond|是|每秒的 IO 讀取作業數|每秒位元組|總計|每秒的 IO 讀取作業數|執行個體|
|IoWriteBytesPerSecond|是|每秒的 IO 寫入位元組數|每秒位元組|總計|每秒的 IO 寫入位元組數|執行個體|
|IoWriteOperationsPerSecond|是|每秒的 IO 寫入作業數|每秒位元組|總計|每秒的 IO 寫入作業數|執行個體|
|MemoryWorkingSet|是|記憶體工作集|位元組|Average|記憶體工作集|執行個體|
|PrivateBytes|是|私用位元組|位元組|Average|私用位元組|執行個體|
|Requests|是|Requests|Count|總計|Requests|執行個體|
|RequestsInApplicationQueue|是|應用程式佇列中的要求數|計數|Average|應用程式佇列中的要求數|執行個體|
|Threads|是|執行緒計數|計數|Average|執行緒計數|執行個體|
|TotalAppDomains|是|應用程式網域總計|計數|Average|應用程式網域總計|執行個體|
|TotalAppDomainsUnloaded|是|已卸載的應用程式網域總計|Count|Average|已卸載的應用程式網域總計|執行個體|


## <a name="next-steps"></a>後續步驟
* [了解 Azure 監視器中的計量](data-platform.md)
* [建立計量警示](alerts-overview.md)
* [將計量匯出至儲存體、事件中樞或 Log Analytics](platform-logs-overview.md)
