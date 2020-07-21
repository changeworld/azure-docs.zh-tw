---
title: Application Insights 中的事件計數器 | Microsoft Docs
description: 監視 Application Insights 中的系統和自訂 .NET/.NET Core EventCounter。
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 06bf15bf60b1ee5e2c301935a30b3981d5233a08
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539936"
---
# <a name="eventcounters-introduction"></a>EventCounter 簡介

`EventCounter` 是 .NET/.NET Core 機制，用來發佈和取用計數器或統計資料。 [本](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)文件提供 `EventCounters` 的概觀及其發佈和取用方式的範例。 所有作業系統平台 (Windows、Linux 和 macOS) 都支援 EventCounter。 您可以將其視為只有在 Windows 系統中才支援的 [PerformanceCounter](/dotnet/api/system.diagnostics.performancecounter) 的跨平台對等項目。

雖然使用者可以發佈任何自訂 `EventCounters` 以符合其需求，但 .NET Core 3.0 執行階段預設會發佈一組這些計數器。 此文件將逐步解說在 Azure Application Insights 中收集及檢視 `EventCounters` (系統定義或使用者定義) 所需的步驟。

## <a name="using-application-insights-to-collect-eventcounters"></a>使用 Application Insights 收集 EventCounter

Application Insights 支援以其 `EventCounterCollectionModule` 收集 `EventCounters`，這是新發行 NuGet 套件 [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector) 的一部分。 使用 [AspNetCore](asp-net-core.md) 或 [WorkerService](worker-service.md) 時，會自動啟用 `EventCounterCollectionModule`。 `EventCounterCollectionModule` 會以 60 秒的不可設定的收集頻率收集計數器。 收集 EventCounter 並不需要特殊的權限。

## <a name="default-counters-collected"></a>收集的預設計數器

SDK 會針對在 .NET Core 3.0 中執行的應用程式，自動收集下列計數器。 計數器名稱的格式將為「類別|計數器」。

|類別 | 計數器|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> ASP.NET Core 應用程式中只能新增 Microsoft.AspNetCore.Hosting 類別的計數器。

## <a name="customizing-counters-to-be-collected"></a>自訂要收集的計數器

下列範例示範如何新增/移除計數器。 使用 `AddApplicationInsightsTelemetry()` 或 `AddApplicationInsightsWorkerService()` 啟用 Application Insights 遙測收集之後，就會在應用程式的 `ConfigureServices` 方法中完成此自訂。 以下是來自 ASP.NET Core 應用程式的範例程式碼。 如需其他類型的應用程式，請參閱[本](worker-service.md#configuring-or-removing-default-telemetrymodules)文件。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>計量瀏覽器中的事件計數器

若要在[計量瀏覽器](../platform/metrics-charts.md)中檢視 EventCounter 計量，請選取 Application Insights 資源，然後選擇 [記錄型計量] 作為計量命名空間。 接著，EventCounter 計量會顯示在 [自訂] 類別之下。

> [!div class="mx-imgBorder"]
> ![Application Insights 中所報告的事件計數器](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Analytics 中的事件計數器

您也可以在 [Analytics](../log-query/log-query-overview.md) 的 **customMetrics** 資料表中，搜尋並顯示事件計數器報告。

例如，執行下列查詢以查看已收集哪些計數器並可供查詢：

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights 中所報告的事件計數器](./media/event-counters/analytics-event-counters.png)

若要取得最近一段時間內特定計數器的圖表 (例如：`ThreadPool Completed Work Item Count`)，請執行下列查詢。

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Application Insights 中的單一計數器圖表](./media/event-counters/analytics-completeditems-counters.png)

與其他遙測一樣，**customMetrics** 也有 `cloud_RoleInstance` 資料行，可指出應用程式執行所在主機伺服器執行個體的身分識別。 上述查詢會顯示每個執行個體的計數器值，而且可以用來比較不同伺服器執行個體的效能。

## <a name="alerts"></a>警示
與其他計量一樣，您可以[設定警示](../../azure-monitor/platform/alerts-log.md)，在事件計數器超出您指定的界限時提出警告。 開啟 [警示] 窗格，然後按一下 [新增警示]。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-see-eventcounters-in-live-metrics"></a>我是否可以在即時計量中看到 EventCounter？

目前為止，即時計量不會顯示 EventCounter。 請使用 [計量瀏覽器] 或 Analytics 查看遙測。

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>我可以在哪些平台上看到 .NET Core 3.0 計數器的預設清單？

EventCounter 不需要任何特殊權限，而且在支援 .NET Core 3.0 的所有平台上都支援。 這包括：

* **作業系統**：Windows、Linux 或 macOS。
* **裝載方法**：內部處理序或跨處理序。
* **部署方法**：架構相依或獨立式。
* **網頁伺服器**：IIS (網際網路資訊伺服器) 或 Kestrel。
* **裝載平台**：Azure App Service、Azure VM、Docker、Azure Kubernetes Service (AKS) 等的 Web Apps 功能。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>我已從 Azure Web 應用程式入口網站啟用 Application Insights。 但是我看不到 EventCounter。

 ASP.NET Core 的 [Application Insights 延伸模組](./azure-web-apps.md)尚不支援此功能。 當此功能受到支援時，將會更新此文件。

## <a name="next-steps"></a><a name="next"></a>後續步驟

* [相依性追蹤](../../azure-monitor/app/asp-net-dependencies.md)
