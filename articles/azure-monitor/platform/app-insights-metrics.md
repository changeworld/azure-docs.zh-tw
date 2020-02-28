---
title: Azure 應用程式 Insights 記錄式計量 |Microsoft Docs
description: 本文列出具有支援之匯總和維度的 Azure 應用程式 Insights 計量。 記錄式計量的詳細資料包含基礎 Kusto 查詢語句。
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664910"
---
# <a name="application-insights-log-based-metrics"></a>Application Insights 以記錄為基礎的計量

Application Insights 以記錄為基礎的計量可讓您分析受監視應用程式的健康情況、建立強大的儀表板，以及設定警示。 計量有兩種：

* 場景後方以[記錄為基礎的度量](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)會從預存事件轉譯成[Kusto 查詢](https://docs.microsoft.com/azure/kusto/query/)。
* [標準計量](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)會儲存為預先匯總的時間序列。

由於*標準計量*會在集合期間預先匯總，因此在查詢時會有較佳的效能。 這讓它們成為儀表板管理和即時警示的較佳選擇。 以*記錄為基礎的度量*有更多維度，這使得它們成為資料分析和臨機操作診斷的絕佳選項。 使用[命名空間選取器](metrics-getting-started.md#create-your-first-metric-chart)，在[計量瀏覽器](metrics-getting-started.md)中切換記錄式和標準計量。

## <a name="interpret-and-use-queries-from-this-article"></a>解讀和使用本文中的查詢

本文列出具有支援之匯總和維度的計量。 記錄式計量的詳細資料包含基礎 Kusto 查詢語句。 為了方便起見，每個查詢都會使用時間細微性的預設值、圖表類型，有時會分割維度，這可簡化在 Log Analytics 中使用查詢，而不需要修改。

當您在[計量瀏覽器](metrics-getting-started.md)中繪製相同的度量時，沒有預設值-根據您的圖表設定，會動態調整查詢：

- 選取的**時間範圍**會轉譯為額外的*where timestamp ...* 子句，以便只從選取的時間範圍挑選事件。 例如，顯示最近24小時資料的圖表，查詢包含 *| where timestamp > 前（24 h）* 。

- 選取的**時間細微性**會放入最終的*摘要 .。。依 bin （時間戳記、[時間細微性]）* 子句。

- 任何選取的**篩選**維度都會轉譯成其他*的 where*子句。

- 選取的**分割圖表**維度會轉譯成額外的摘要屬性。 例如，如果您依*位置*分割您的圖表，並使用5分鐘的時間資料細微性繪製，則*摘要子句會*摘要*列出 .。。依 bin （時間戳記、5 m）、位置*。

> [!NOTE]
> 如果您不熟悉 Kusto 查詢語言，請從將 Kusto 語句複製並貼入 Log Analytics 查詢窗格開始，而不進行任何修改。 按一下 [**執行**] 以查看基本圖表。 當您開始瞭解查詢語言的語法時，您可以開始進行小型修改，並查看變更的影響。 探索您自己的資料是開始實現[Log Analytics](../../azure-monitor/log-query/get-started-portal.md)和[Azure 監視器](../../azure-monitor/overview.md)的完整威力的絕佳方式。

## <a name="availability-metrics"></a>可用性度量

[可用性] 類別中的計量可讓您查看 web 應用程式的健全狀況，如世界各地的觀點所觀察到的。 [設定可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)，以開始使用此類別中的任何計量。

### <a name="availability-availabilityresultsavailabilitypercentage"></a>可用性（availabilityResults/availabilityPercentage）
[*可用性*] 度量會顯示未偵測到任何問題的 web 測試回合百分比。 可能的最小值為0，表示所有 web 測試回合都已失敗。 100的值表示所有 web 測試執行都會通過驗證準則。

|測量單位|支援的匯總|支援的維度|
|---|---|---|---|---|---|
|百分比|Average|執行位置，測試名稱|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>可用性測試持續時間（availabilityResults/duration）

[*可用性測試持續時間*] 計量會顯示 web 測試執行所花的時間。 針對[多重步驟 web 測試](../../azure-monitor/app/availability-multistep.md)，度量會反映所有步驟的總執行時間。

|測量單位|支援的匯總|支援的維度|
|---|---|---|---|---|---|
|毫秒|平均值、最小值、最大值|執行位置、測試名稱、測試結果

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>可用性測試（availabilityResults/count）

[*可用性測試*] 計量會藉由 Azure 監視器來反映 web 測試執行的計數。

|測量單位|支援的匯總|支援的維度|
|---|---|---|---|---|---|
|[計數]|[計數]|執行位置、測試名稱、測試結果|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>瀏覽器計量

瀏覽器計量會由 Application Insights JavaScript SDK 從實際的使用者瀏覽器收集。 它們可讓您深入瞭解您的 web 應用程式使用者體驗。 瀏覽器計量通常不會取樣，這表示它們會提供比伺服器端計量更高的使用號碼精確度，這可能會因取樣而扭曲。

> [!NOTE]
> 若要收集瀏覽器計量，您的應用程式必須使用[Application Insights JAVASCRIPT SDK](../../azure-monitor/app/javascript.md)進行檢測。

### <a name="browser-page-load-time-browsertimingstotalduration"></a>瀏覽器頁面載入時間（browserTimings/totalDuration）

|測量單位|支援的匯總|預先匯總的維度|
|---|---|---|
|毫秒|平均值、最小值、最大值|無|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>用戶端處理時間（browserTiming/processingDuration）

|測量單位|支援的匯總|預先匯總的維度|
|---|---|---|
|毫秒|平均值、最小值、最大值|無|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>頁面載入網路連接時間（browserTimings/networkDuration）

|測量單位|支援的匯總|預先匯總的維度|
|---|---|---|
|毫秒|平均值、最小值、最大值|無|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>接收回應時間（browserTimings/receiveDuration）

|測量單位|支援的匯總|預先匯總的維度|
|---|---|---|
|毫秒|平均值、最小值、最大值|無|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>傳送要求時間（browserTimings/sendDuration）

|測量單位|支援的匯總|預先匯總的維度|
|---|---|---|
|毫秒|平均值、最小值、最大值|無|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>失敗計量

**失敗**中的計量會顯示處理要求、相依性呼叫和擲回例外狀況的問題。

### <a name="browser-exceptions-exceptionsbrowser"></a>瀏覽器例外狀況（例外狀況/瀏覽器）

此標準會反映在瀏覽器中執行的應用程式程式碼所擲回的例外狀況數目。 只有使用 ```trackException()``` Application Insights API 呼叫追蹤的例外狀況才會包含在計量中。

|測量單位|支援的匯總|預先匯總的維度|注意事項|
|---|---|---|---|
|[計數]|[計數]|無|以記錄為基礎的版本使用**Sum**匯總|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>相依性呼叫失敗（相依性/失敗）

失敗的相依性呼叫數目。

|測量單位|支援的匯總|預先匯總的維度|注意事項|
|---|---|---|---|
|[計數]|[計數]|無|以記錄為基礎的版本使用**Sum**匯總|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>例外狀況（例外狀況/計數）

每次當您將例外狀況記錄到 Application Insights 時，就會呼叫 SDK 的[trackException （）方法](../../azure-monitor/app/api-custom-events-metrics.md#trackexception)。 [例外狀況] 度量會顯示已記錄的例外狀況數目。

|測量單位|支援的匯總|預先匯總的維度|注意事項|
|---|---|---|---|
|[計數]|[計數]|雲端角色名稱、雲端角色實例、裝置類型|以記錄為基礎的版本使用**Sum**匯總|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>失敗的要求（要求/失敗）

標示為*失敗*的追蹤伺服器要求計數。 根據預設，Application Insights SDK 會自動將傳回 HTTP 回應碼5xx 或4xx 的每個伺服器要求標示為失敗的要求。 您可以在[自訂遙測初始化運算式](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)中修改要求遙測專案的*success*屬性，以自訂此邏輯。

|測量單位|支援的匯總|預先匯總的維度|注意事項|
|---|---|---|---|
|[計數]|[計數]|雲端角色實例，雲端角色名稱，實際或綜合流量，要求效能，回應碼|以記錄為基礎的版本使用**Sum**匯總|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>伺服器例外狀況（例外狀況/伺服器）

此度量會顯示伺服器例外狀況的數目。

|測量單位|支援的匯總|預先匯總的維度|注意事項|
|---|---|---|---|
|[計數]|[計數]|雲端角色名稱、雲端角色實例|以記錄為基礎的版本使用**Sum**匯總|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>效能計數器

使用**效能計數器**類別中的計量來存取[Application Insights 所收集的系統效能計數器](../../azure-monitor/app/performance-counters.md)。

### <a name="available-memory-performancecountersavailablememory"></a>可用的記憶體（performanceCounters/availableMemory）

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>例外狀況率（performanceCounters/exceptionRate）

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 要求執行時間（performanceCounters/requestExecutionTime）

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 要求率（performanceCounters/requestsPerSecond）

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>應用程式佇列中的 HTTP 要求（performanceCounters/requestsInQueue）

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>進程 CPU （performanceCounters/processCpuPercentage）

此度量會顯示裝載受監視應用程式的進程耗用的處理器總容量數量。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
|百分比|平均值、最小值、最大值|雲端角色執行個體

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>進程 IO 速率（performanceCounters/processIOBytesPerSecond）

|測量單位|支援的匯總|支援的維度|
|---|---|---|
|每秒位元組數|平均值、最小值、最大值|雲端角色執行個體

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>進程私用位元組（performanceCounters/processPrivateBytes）

受監視進程配置給其資料的非共用記憶體數量。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
|位元組|平均值、最小值、最大值|雲端角色執行個體

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>處理器時間（performanceCounters/processorCpuPercentage）

受監視伺服器實例上執行之*所有*進程的 CPU 耗用量。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
|百分比|平均值、最小值、最大值|雲端角色執行個體

>[!NOTE]
> Azure App 服務中裝載的應用程式無法使用處理器時間度量。 使用[處理常式 cpu](#process-cpu-performancecountersprocesscpupercentage)度量來追蹤應用程式服務中裝載之 web 應用程式的 CPU 使用率。

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>伺服器計量

### <a name="dependency-calls-dependenciescount"></a>相依性呼叫（相依性/計數）

此度量與相依性呼叫的數目有關。

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>相依性持續時間（相依性/持續時間）

這項計量是指相依性呼叫的持續時間。

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

### <a name="server-requests-requestscount"></a>伺服器要求（要求/計數）

此標準會反映 web 應用程式收到的傳入伺服器要求數目。

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>伺服器回應時間（要求/持續時間）

此標準會反映伺服器處理傳入要求所花費的時間。

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

### <a name="page-view-load-time-pageviewsduration"></a>頁面流覽載入時間（pageViews/duration）

此計量是指 PageView 事件載入所花費的時間量。

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

### <a name="page-views-pageviewscount"></a>頁面流覽（pageViews/計數）

使用 TrackPageView （） Application Insights API 記錄的 PageView 事件計數。

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>會話（會話數/計數）

此度量會參考不同會話識別碼的計數。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>追蹤（追蹤/計數）

使用 TrackTrace （） Application Insights API 呼叫所記錄的追蹤語句計數。

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>使用者（使用者/計數）

存取您應用程式的不同使用者數目。 使用遙測取樣和篩選，可能會大幅影響此度量的精確度。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>使用者，已驗證（使用者/已驗證）

已通過應用程式驗證的不同使用者數目。

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
