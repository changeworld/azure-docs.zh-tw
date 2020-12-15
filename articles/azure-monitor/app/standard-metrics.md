---
title: Azure 應用程式 Insights 標準計量 |Microsoft Docs
description: 本文列出支援的匯總和維度的 Azure 應用程式 Insights 計量。
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: f59dce5a450cd745ad05ea3b53d85606706a8eaa
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513319"
---
# <a name="application-insights-standard-metrics"></a>Application Insights 標準計量

標準計量會在收集期間預先匯總，在查詢時會有較佳的效能。 這讓他們成為儀表板和即時警示的最佳選擇。

## <a name="availability-metrics"></a>可用性度量

可用性類別中的計量可讓您查看 web 應用程式的健康情況，如世界各地的觀點所觀察。 [設定可用性測試](../app/monitor-web-app-availability.md) 以開始使用此類別中的任何計量。

### <a name="availability-availabilityresultsavailabilitypercentage"></a>可用性 (availabilityResults/availabilityPercentage) 
*可用性* 度量會顯示未偵測到任何問題之 web 測試回合的百分比。 最小的可能值為0，表示所有 web 測試回合都已失敗。 100的值表示所有 web 測試執行都通過驗證準則。

|測量單位|支援的匯總|支援的維度|
|---|---|---|---|---|---|
|百分比|平均|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>可用性測試持續時間 (availabilityResults/持續時間) 

[ *可用性測試持續時間* ] 計量會顯示 web 測試執行所花的時間。 若為 [多步驟的 web 測試](../app/availability-multistep.md)，度量會反映所有步驟的總執行時間。

|測量單位|支援的匯總|支援的維度|
|---|---|---|---|---|---|
|毫秒|平均、最小值、最大值|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>可用性測試 (availabilityResults/計數) 

「 *可用性測試* 」度量會反映 Azure 監視器所執行的 web 測試計數。

|測量單位|支援的匯總|支援的維度|
|---|---|---|---|---|---|
|Count|Count|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>瀏覽器計量

Application Insights JavaScript SDK 會從真實的終端使用者瀏覽器收集瀏覽器度量。 這些應用程式可為您的 web 應用程式提供使用者體驗的絕佳見解。 通常不會取樣瀏覽器計量，這表示它們會提供較高的有效位數，相較于伺服器端的度量，可能會透過取樣來扭曲。

> [!NOTE]
> 若要收集瀏覽器計量，您必須使用 [Application Insights JAVASCRIPT SDK](../app/javascript.md)來檢測您的應用程式。

### <a name="browser-page-load-time-browsertimingstotalduration"></a>瀏覽器頁面載入時間 (browserTimings/totalDuration) 

|測量單位|支援的匯總| 支援的維度|
|---|---|---|
|毫秒|平均、最小值、最大值|無|

### <a name="client-processing-time-browsertimingprocessingduration"></a>用戶端處理時間 (browserTiming/processingDuration) 

|測量單位|支援的匯總|支援的維度|
|---|---|---|
|毫秒|平均、最小值、最大值|無|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>頁面載入網路連接時間 (browserTimings/networkDuration) 

|測量單位|支援的匯總| 支援的維度|
|---|---|---|
|毫秒|平均、最小值、最大值|無|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>接收回應時間 (browserTimings/receiveDuration) 

|測量單位|支援的匯總| 支援的維度|
|---|---|---|
|毫秒|平均、最小值、最大值|無|

### <a name="send-request-time-browsertimingssendduration"></a>傳送要求時間 (browserTimings/sendDuration) 

|測量單位|支援的匯總| 支援的維度|
|---|---|---|
|毫秒|平均、最小值、最大值|無|

## <a name="failure-metrics"></a>失敗計量

**失敗** 中的計量會顯示處理要求、相依性呼叫及擲回例外狀況的問題。

### <a name="browser-exceptions-exceptionsbrowser"></a> (例外狀況/瀏覽器) 的瀏覽器例外狀況

此標準反映在瀏覽器中執行的應用程式程式碼所擲回的例外狀況數目。 只有使用 Application Insights API 呼叫追蹤的例外狀況 ```trackException()``` 會包含在度量中。

|測量單位|支援的匯總 | 支援的維度|
|---|---|---|---|
| Count | Count | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>相依性呼叫失敗 (相依項/失敗) 

失敗的相依性呼叫數目。

|測量單位|支援的匯總 | 支援的維度 |
|---|---|---|---|
|Count|Count| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>例外狀況 (例外狀況/計數) 

每次當您將例外狀況記錄到 Application Insights 時，會呼叫 SDK 的 [trackException ( # A1 方法](../app/api-custom-events-metrics.md#trackexception) 。 例外狀況度量會顯示已記錄的例外狀況數目。

|測量單位|支援的匯總 | 支援的維度 |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>失敗的要求 (要求/失敗) 

標示為 *失敗* 的追蹤伺服器要求計數。 根據預設，Application Insights SDK 會自動將傳回 HTTP 回應碼5xx 或4xx 的每個伺服器要求標記為失敗的要求。 您可以藉由在 [自訂遙測初始化運算式](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)中修改要求遙測專案的 *success* 屬性來自訂此邏輯。


|測量單位|支援的匯總 | 支援的維度 |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a> (例外狀況/伺服器) 的伺服器例外狀況

此度量會顯示伺服器例外狀況的數目。

|測量單位|支援的匯總 | 支援的維度 |
|---|---|---|---|
|Count|Count|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>效能計數器

使用 **效能計數器** 類別中的計量來存取 [Application Insights 所收集的系統效能計數器](../app/performance-counters.md)。

### <a name="available-memory-performancecountersavailablememory"></a>可用的記憶體 (performanceCounters/availableMemory) 

|測量單位|支援的匯總 | 支援的維度 |
|---|---|---|---|
|資料相依： Mb、Gb|平均、最大值、最小值|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>例外狀況速率 (performanceCounters/exceptionRate) 

|測量單位|支援的匯總 | 支援的維度 |
|---|---|---|---|
| 計數 | 平均、最大值、最小值 | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP 要求執行時間 (performanceCounters/requestExecutionTime) 

|測量單位|支援的匯總 | 支援的維度 |
|---|---|---|---|
| 毫秒 | 平均、最大值、最小值 | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP 要求速率 (performanceCounters/requestsPerSecond) 

|測量單位|支援的匯總 | 支援的維度 |
|---|---|---|---|
| 每秒要求 | 平均、最大值、最小值 | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>應用程式佇列中的 HTTP 要求 (performanceCounters/requestsInQueue) 

|測量單位|支援的匯總 | 支援的維度 |
|---|---|---|---|
| 計數 | 平均、最大值、最小值 | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>處理 CPU (performanceCounters/processCpuPercentage) 

計量會顯示裝載受監視應用程式之進程耗用的處理器總容量數量。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
|百分比|平均、最大值、最小值| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>進程 IO 速率 (performanceCounters/processIOBytesPerSecond) 

|測量單位|支援的匯總|支援的維度|
|---|---|---|
|每秒位元組數|平均、最小值、最大值|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>處理私用位元組 (performanceCounters/processPrivateBytes) 

受監視進程配置給其資料的非共用記憶體數量。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
|位元組|平均、最小值、最大值|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>處理器時間 (performanceCounters/processorCpuPercentage) 

受監視的伺服器實例上執行的 *所有* 進程的 CPU 耗用量。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
|百分比|平均、最小值、最大值|`Cloud role instance` |

>[!NOTE]
> Azure App 服務中裝載的應用程式無法使用處理器時間度量。 使用「  [進程 CPU](#process-cpu-performancecountersprocesscpupercentage) 」計量來追蹤應用程式服務中裝載之 web 應用程式的 cpu 使用率。

## <a name="server-metrics"></a>伺服器計量

### <a name="dependency-calls-dependenciescount"></a>相依性呼叫 (相依性/計數) 

此度量與相依性呼叫的數目相關。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>相依性持續時間 (相依性/持續時間) 

此度量是指相依性呼叫的持續時間。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
| 時間 | 平均、最小值、最大值 | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>伺服器要求速率 (要求/計數) 

此計量會反映您的 web 應用程式所接收的傳入伺服器要求數目。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
| Count | Average | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>伺服器要求 (要求/計數) 

|測量單位|支援的匯總|支援的維度|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>伺服器回應時間 (要求/持續時間) 

此度量會反映伺服器處理傳入要求所花費的時間。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
| 時間 | 平均、最小值、最大值 | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>使用計量

### <a name="page-view-load-time-pageviewsduration"></a>頁面流覽載入時間 (pageViews/持續時間) 

此標準是指載入 PageView 事件所花費的時間量。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
| 時間 | 平均、最小值、最大值 | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>頁面流覽 (pageViews/計數) 

使用 TrackPageView ( # A1 Application Insights API 記錄的 PageView 事件計數。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
| Count | Count | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>追蹤 (追蹤/計數) 

使用 TrackTrace 記錄的追蹤語句計數 ( # A1 Application Insights API 呼叫。

|測量單位|支援的匯總|支援的維度|
|---|---|---|
| Count | Count | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>後續步驟

* 瞭解 [記錄型和預先匯總的計量](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics)。
* 以[記錄為基礎的計量查詢和定義](https://docs.microsoft.com/azure/azure-monitor/platform/app-insights-metrics)。