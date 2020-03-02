---
title: Application Insights 中的事件計數器 |Microsoft Docs
description: 監視 Application Insights 中的系統和自訂 .NET/.NET Core EventCounters。
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663584"
---
# <a name="eventcounters-introduction"></a>EventCounters 簡介

`EventCounter` 是 .NET/.NET Core 機制，用來發行和使用計數器或統計資料。 [本](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)檔提供有關如何發佈和取用 `EventCounters` 的總覽和範例。 所有作業系統平臺（Windows、Linux 和 macOS）都支援 EventCounters。 您可以將它視為[PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter)的跨平臺對等，只有在 Windows 系統中才支援。

雖然使用者可以發佈任何自訂 `EventCounters` 以符合其需求，但 .NET Core 3.0 執行時間預設會發佈一組這些計數器。 本檔將逐步解說在 Azure 應用程式 Insights 中收集及查看 `EventCounters` （系統定義或使用者定義）所需的步驟。

## <a name="using-application-insights-to-collect-eventcounters"></a>使用 Application Insights 收集 EventCounters

Application Insights 支援以其 `EventCounterCollectionModule`來收集 `EventCounters`，這是[ApplicationInsights EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)的新發行 nuget 套件的一部分。 使用[AspNetCore](asp-net-core.md)或[WorkerService](worker-service.md)時，會自動啟用 `EventCounterCollectionModule`。 `EventCounterCollectionModule` 會收集不具可設定的集合頻率60秒的計數器。 收集 EventCounters 不需要任何特殊許可權。

## <a name="default-counters-collected"></a>收集的預設計數器

針對在 .NET Core 3.0 中執行的應用程式，SDK 會自動收集下列計數器。 計數器的名稱的格式為 "Category |Counter "。

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
> AspNetCore 類別目錄的計數器只會加入 ASP.NET Core 應用程式中。

## <a name="customizing-counters-to-be-collected"></a>自訂要收集的計數器

下列範例顯示如何新增/移除計數器。 使用 `AddApplicationInsightsTelemetry()` 或 `AddApplicationInsightsWorkerService()`啟用 Application Insights 遙測集合之後，就會在應用程式的 `ConfigureServices` 方法中完成這項自訂。 以下是來自 ASP.NET Core 應用程式的範例程式碼。 如需其他類型的應用程式，請參閱[這](worker-service.md#configuring-or-removing-default-telemetrymodules)份檔。

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

若要在計量[瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)中查看 EventCounter 度量，請選取 [Application Insights 資源]，然後選擇 [以記錄為基礎的計量] 做為 度量 然後，EventCounter 計量會顯示在 [自訂類別] 之下。

> [!div class="mx-imgBorder"]
> Application Insights ![中所報告的 事件計數器](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>分析中的事件計數器

您也可以在 [ **customMetrics** ] 資料表的 [[分析](../../azure-monitor/app/analytics.md)] 中搜尋和顯示事件計數器報告。

例如，執行下列查詢以查看哪些計數器已收集並可供查詢：

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> Application Insights](./media/event-counters/analytics-event-counters.png) 中所報告的 ![事件計數器

若要取得最近一段時間內特定計數器的圖表（例如： `ThreadPool Completed Work Item Count`），請執行下列查詢。

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![在 Application Insights 中對單一計數器的交談](./media/event-counters/analytics-completeditems-counters.png)

就像其他遙測一樣， **customMetrics**也有資料行 `cloud_RoleInstance`，指出您的應用程式執行所在之主機伺服器實例的身分識別。 上述查詢會顯示每個實例的計數器值，而且可以用來比較不同伺服器實例的效能。

## <a name="alerts"></a>警示
就像其他計量一樣，您可以[設定警示](../../azure-monitor/app/alerts.md)，在事件計數器超出您指定的限制時警告您。 開啟 [警示] 窗格，然後按一下 [新增警示]。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-see-eventcounters-in-live-metrics"></a>我可以看到即時計量中的 EventCounters 嗎？

目前為止，即時計量不會顯示 EventCounters。 使用 [計量瀏覽器] 或 [分析] 來查看遙測。

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>我可以在哪些平臺上看到 .NET Core 3.0 計數器的預設清單？

EventCounter 不需要任何特殊許可權，而且所有平臺都支援 .NET Core 3.0。 這包括：

* **作業系統**： Windows、Linux 或 macOS。
* **裝載方法**：處理中或進程外。
* **部署方法**：架構相依或獨立的。
* **網頁伺服器**： IIS （Internet information server）或 Kestrel。
* **裝載平臺**： Azure App Service、Azure VM、Docker、AZURE KUBERNETES SERVICE （AKS）等的 Web Apps 功能。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>我已從 Azure Web 應用程式入口網站啟用 Application Insights。 但是看不到 EventCounters。

 ASP.NET Core 的[Application Insights 延伸](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps)模組尚不支援這項功能。 當支援這項功能時，將會更新這份檔。

## <a name="next"></a>後續步驟

* [相依性追蹤](../../azure-monitor/app/asp-net-dependencies.md)
