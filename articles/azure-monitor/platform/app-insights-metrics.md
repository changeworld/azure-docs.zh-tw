---
title: Azure 應用程式見解基於日誌的指標 |微軟文檔
description: 本文列出了具有支援的聚合和維度的 Azure 應用程式見解指標。 有關基於日誌的指標的詳細資訊包括基礎 Kusto 查詢語句。
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664910"
---
# <a name="application-insights-log-based-metrics"></a>基於日誌的應用程式洞察指標

通過基於日誌的應用程式見解指標，您可以分析受監視應用的運行狀況、創建功能強大的儀表板並配置警報。 有兩種類型的指標：

* 場景後面的[基於日誌的指標](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)將轉換為存儲事件的[Kusto 查詢](https://docs.microsoft.com/azure/kusto/query/)。
* [標準指標](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)存儲為預聚合時間序列。

由於*標準指標*在收集過程中預先聚合，因此在查詢時它們具有更好的性能。 這使得它們成為儀表板和即時警報的更好選擇。 *基於日誌的指標*具有更多的維度，這使得它們成為資料分析和臨時診斷的卓越選項。 使用[命名空間選擇器](metrics-getting-started.md#create-your-first-metric-chart)在[指標資源管理器](metrics-getting-started.md)中的基於日誌和標準指標之間切換。

## <a name="interpret-and-use-queries-from-this-article"></a>解釋和使用本文的查詢

本文列出了具有支援的聚合和維度的指標。 有關基於日誌的指標的詳細資訊包括基礎 Kusto 查詢語句。 為方便起見，每個查詢都使用預設的時間細微性、圖表類型，有時還使用拆分維度，從而簡化了在日誌分析中使用查詢的維度，而無需進行任何修改。

當您在[指標資源管理器](metrics-getting-started.md)中繪製相同的指標時，沒有預設值 - 查詢會根據您的圖表設置進行動態調整：

- 所選**時間範圍**將轉換為其他*時間戳記...* 例如，顯示最近 24 小時資料的圖表中，查詢包括 #*時間戳記>之前（24 小時）*。

- 所選**時間細微性**被放入最終*摘要中...按 bin（時間戳記，[時間粒]）* 子句。

- 任何選定的**篩選器**維度將轉換為其他*在何處*子句。

- 所選**的拆分圖表**維度將轉換為額外的匯總屬性。 例如，如果按*位置*拆分圖表，並使用 5 分鐘的時間細微性進行繪製，則*匯總*子句將匯總 *...按箱（時間戳記，5米），位置*。

> [!NOTE]
> 如果您是 Kusto 查詢語言的新增語言，則首先將 Kusto 語句複製並粘貼到日誌分析查詢窗格中，而無需進行任何修改。 按一下 **"運行"** 以查看基本圖表。 當您開始理解查詢語言的語法時，您可以開始進行一些小的修改，並查看更改的影響。 流覽自己的資料是開始實現[日誌分析和](../../azure-monitor/log-query/get-started-portal.md) [Azure 監視器](../../azure-monitor/overview.md)的全部功能的好方法。

## <a name="availability-metrics"></a>可用性度量

可用性類別中的指標使您能夠從世界各地的點看到 Web 應用程式的運行狀況。 [將可用性測試組態為](../../azure-monitor/app/monitor-web-app-availability.md)開始使用此類別的任何指標。

### <a name="availability-availabilityresultsavailabilitypercentage"></a>可用性（可用性結果/可用性百分比）
*可用性*指標顯示未檢測到任何問題的 Web 測試回合的百分比。 盡可能低的值為 0，表示所有 Web 測試回合都已失敗。 值 100 表示所有 Web 測試回合都通過了驗證條件。

|測量單位|支援的聚合|支援的尺寸|
|---|---|---|---|---|---|
|百分比|Average|運行位置，測試名稱|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>可用性測試持續時間（可用性結果/持續時間）

*可用性測試持續時間*指標顯示 Web 測試回合所花的時間。 對於[多步驟 Web 測試](../../azure-monitor/app/availability-multistep.md)，該指標反映所有步驟的總執行時間。

|測量單位|支援的聚合|支援的尺寸|
|---|---|---|---|---|---|
|毫秒|平均， 最小， 最大|運行位置、測試名稱、測試結果

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>可用性測試（可用性結果/計數）

*可用性測試*指標反映 Azure 監視器運行的 Web 測試的計數。

|測量單位|支援的聚合|支援的尺寸|
|---|---|---|---|---|---|
|Count|Count|運行位置、測試名稱、測試結果|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>瀏覽器指標

瀏覽器指標由應用程式洞察 JavaScript SDK 從實際最終使用者瀏覽器中收集。 它們提供了對使用者使用 Web 應用體驗的深入瞭解。 瀏覽器指標通常不進行採樣，這意味著與可能通過採樣傾斜的伺服器端指標相比，它們提供了更高的使用數位精度。

> [!NOTE]
> 要收集瀏覽器指標，必須使用[應用程式見解 JavaScript SDK](../../azure-monitor/app/javascript.md)來檢測應用程式。

### <a name="browser-page-load-time-browsertimingstotalduration"></a>瀏覽器頁面載入時間（瀏覽器計時/總持續時間）

|測量單位|支援的聚合|預聚合維度|
|---|---|---|
|毫秒|平均， 最小， 最大|None|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>用戶端處理時間（瀏覽器計時/處理持續時間）

|測量單位|支援的聚合|預聚合維度|
|---|---|---|
|毫秒|平均， 最小， 最大|None|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>頁面載入網路連接時間（瀏覽器計時/網路持續時間）

|測量單位|支援的聚合|預聚合維度|
|---|---|---|
|毫秒|平均， 最小， 最大|None|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>接收回應時間（瀏覽器計時/接收持續時間）

|測量單位|支援的聚合|預聚合維度|
|---|---|---|
|毫秒|平均， 最小， 最大|None|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>發送請求時間（瀏覽器計時/發送持續時間）

|測量單位|支援的聚合|預聚合維度|
|---|---|---|
|毫秒|平均， 最小， 最大|None|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>失敗指標

**失敗**中的指標顯示處理請求、依賴項調用和引發異常的問題。

### <a name="browser-exceptions-exceptionsbrowser"></a>瀏覽器例外（例外/瀏覽器）

此指標反映在瀏覽器中運行的應用程式代碼引發異常的數量。 指標中僅包含使用```trackException()```應用程式見解 API 呼叫跟蹤的異常。

|測量單位|支援的聚合|預聚合維度|注意|
|---|---|---|---|
|Count|Count|None|基於日誌的版本使用**總和**聚合|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>依賴項調用失敗（依賴項/失敗）

失敗的依賴項調用數。

|測量單位|支援的聚合|預聚合維度|注意|
|---|---|---|---|
|Count|Count|None|基於日誌的版本使用**總和**聚合|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>例外（例外/計數）

每次記錄應用程式見解的異常時，都會調用 SDK 的[trackException（） 方法](../../azure-monitor/app/api-custom-events-metrics.md#trackexception)。 "例外"指標顯示記錄的異常數。

|測量單位|支援的聚合|預聚合維度|注意|
|---|---|---|---|
|Count|Count|雲角色名稱、雲角色實例、裝置類型|基於日誌的版本使用**總和**聚合|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>失敗的請求（請求/失敗）

標記為*失敗的*已跟蹤伺服器請求的計數。 預設情況下，應用程式見解 SDK 會自動將返回 HTTP 回應代碼 5xx 或 4xx 的每個伺服器請求標記為失敗請求。 您可以通過修改[自訂遙測初始化器](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)中請求遙測項*的成功*屬性來自訂此邏輯。

|測量單位|支援的聚合|預聚合維度|注意|
|---|---|---|---|
|Count|Count|雲角色實例、雲角色名稱、實際或綜合流量、請求性能、回應代碼|基於日誌的版本使用**總和**聚合|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>伺服器異常（例外/伺服器）

此指標顯示伺服器異常數。

|測量單位|支援的聚合|預聚合維度|注意|
|---|---|---|---|
|Count|Count|雲角色名稱，雲角色實例|基於日誌的版本使用**總和**聚合|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>效能計數器

使用**效能計數器類別中的**指標訪問[應用程式見解收集的系統效能計數器](../../azure-monitor/app/performance-counters.md)。

### <a name="available-memory-performancecountersavailablememory"></a>可用記憶體（效能計數器/可用記憶體）

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>異常率（效能計數器/異常率）

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 要求執行時間（效能計數器/請求執行時間）

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 要求速率（效能計數器/請求每秒）

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>應用程式佇列中的 HTTP 要求（效能計數器/請求在佇列中）

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>進程 CPU（效能計數器/進程 Cpu 百分比）

該指標顯示託管受監視應用的進程消耗了總處理器容量的多少。

|測量單位|支援的聚合|支援的尺寸|
|---|---|---|
|百分比|平均， 最小， 最大|雲端角色執行個體

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>進程 IO 速率（效能計數器/過程位元組數秒）

|測量單位|支援的聚合|支援的尺寸|
|---|---|---|
|位元組/秒|平均， 最小， 最大|雲端角色執行個體

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>處理專用位元組（效能計數器/進程私人位元組）

受監視的進程為其資料分配的非共用記憶體量。

|測量單位|支援的聚合|支援的尺寸|
|---|---|---|
|位元組|平均， 最小， 最大|雲端角色執行個體

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>處理器時間（效能計數器/處理器Cpu百分比）

在受監視的伺服器實例上運行*的所有*進程消耗 CPU。

|測量單位|支援的聚合|支援的尺寸|
|---|---|---|
|百分比|平均， 最小， 最大|雲端角色執行個體

>[!NOTE]
> 處理器時間指標不適用於 Azure 應用服務中託管的應用程式。 使用[進程 CPU](#process-cpu-performancecountersprocesscpupercentage)指標跟蹤應用服務中託管的 Web 應用程式的 CPU 利用率。

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>伺服器計量

### <a name="dependency-calls-dependenciescount"></a>依賴項調用（依賴項/計數）

此指標與依賴項調用數相關。

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>依賴項持續時間（依賴項/持續時間）

此指標是指依賴項調用的持續時間。

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>伺服器請求（請求/計數）

此指標反映 Web 應用程式收到的傳入伺服器請求數。

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>伺服器回應時間（請求/持續時間）

此指標反映伺服器處理傳入請求所花的時間。

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>用量度量

### <a name="page-view-load-time-pageviewsduration"></a>網頁檢視載入時間（網頁檢視/持續時間）

此指標是指 PageView 事件載入所花費的時間量。

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>頁面流覽次數（頁面流覽/計數）

使用 TrackPageView（） 應用程式見解 API 記錄的 PageView 事件計數。

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>會話（會話/計數）

此指標引用不同會話指示的計數。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>跟蹤（跟蹤/計數）

使用 TrackTrace（） 應用程式見解 API 呼叫記錄的跟蹤語句的計數。

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>使用者（使用者/計數）

訪問您的應用程式的不同使用者數。 使用遙測採樣和篩選可能會顯著影響此指標的準確性。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>使用者，已驗證（使用者/已身份驗證）

在應用程式中進行身份驗證的不同使用者數。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
