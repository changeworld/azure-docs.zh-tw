---
title: Application Insights 中的事件計數器 | Microsoft Docs
description: 監視 Application Insights 中的系統和自訂 .NET/.NET Core EventCounter。
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: f8ae36545eecbbad2a6695ca979fb7da8380e8cc
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657018"
---
# <a name="eventcounters-introduction"></a>EventCounter 簡介

`EventCounter` 是 .NET/.NET Core 機制，用來發佈和取用計數器或統計資料。 [本](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)文件提供 `EventCounters` 的概觀及其發佈和取用方式的範例。 所有作業系統平台 (Windows、Linux 和 macOS) 都支援 EventCounter。 您可以將其視為只有在 Windows 系統中才支援的 [PerformanceCounter](/dotnet/api/system.diagnostics.performancecounter) 的跨平台對等項目。

雖然使用者可以發佈任何自訂 `EventCounters` 以符合其需求，但 .Net Core 3.0 和更新版本的執行時間預設會發佈一組這些計數器。 本檔將逐步解說 `EventCounters` 在 Azure 應用程式 Insights 中收集和查看 (系統定義或使用者定義) 所需的步驟。

## <a name="using-application-insights-to-collect-eventcounters"></a>使用 Application Insights 收集 EventCounter

Application Insights 支援 `EventCounters` 以其 `EventCounterCollectionModule` 進行收集，這是新發行之 NuGet 套件 [ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)的一部分。 使用 [AspNetCore](asp-net-core.md) 或 [WorkerService](worker-service.md) 時，會自動啟用 `EventCounterCollectionModule`。 `EventCounterCollectionModule` 會以 60 秒的不可設定的收集頻率收集計數器。 收集 EventCounter 並不需要特殊的權限。

## <a name="default-counters-collected"></a>收集的預設計數器

針對在 .NET Core 3.0 或更高版本中執行的應用程式，SDK 會自動收集下列計數器。 計數器名稱的格式將為「類別|計數器」。

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

> [!NOTE]
> 從 2.15.0-Beta3 版本的 [ASPNETCORE SDK](asp-net-core.md) 或 [WorkerService SDK](worker-service.md)開始，預設不會收集任何計數器。 模組本身已啟用，因此使用者可以直接新增所需的計數器來收集它們。

## <a name="customizing-counters-to-be-collected"></a>自訂要收集的計數器

下列範例示範如何新增/移除計數器。 使用 `AddApplicationInsightsTelemetry()` 或 `AddApplicationInsightsWorkerService()` 啟用 Application Insights 遙測收集之後，就會在應用程式的 `ConfigureServices` 方法中完成此自訂。 以下是來自 ASP.NET Core 應用程式的範例程式碼。 如需其他類型的應用程式，請參閱[本](worker-service.md#configuring-or-removing-default-telemetrymodules)文件。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
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
    }
```

## <a name="disabling-eventcounter-collection-module"></a>停用 EventCounter 收集模組

`EventCounterCollectionModule` 可以使用停用 `ApplicationInsightsServiceOptions` 。 使用 ASP.NET Core SDK 的範例如下所示。

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

WorkerService SDK 也可以使用類似的方法，但必須變更命名空間，如下列範例所示。

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>計量瀏覽器中的事件計數器

若要在[計量瀏覽器](../platform/metrics-charts.md)中檢視 EventCounter 計量，請選取 Application Insights 資源，然後選擇 [記錄型計量] 作為計量命名空間。 接著，EventCounter 計量會顯示在 [自訂] 類別之下。

> [!div class="mx-imgBorder"]
> ![Application Insights 計量瀏覽器中報告的事件計數器](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Analytics 中的事件計數器

您也可以在 [Analytics](../log-query/log-query-overview.md) 的 **customMetrics** 資料表中，搜尋並顯示事件計數器報告。

例如，執行下列查詢以查看已收集哪些計數器並可供查詢：

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights Analytics 中回報的事件計數器](./media/event-counters/analytics-event-counters.png)

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
與其他計量一樣，您可以[設定警示](../platform/alerts-log.md)，在事件計數器超出您指定的界限時提出警告。 開啟 [警示] 窗格，然後按一下 [新增警示]。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-see-eventcounters-in-live-metrics"></a>我是否可以在即時計量中看到 EventCounter？

目前為止，即時計量不會顯示 EventCounter。 請使用 [計量瀏覽器] 或 Analytics 查看遙測。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>我已從 Azure Web 應用程式入口網站啟用 Application Insights。 但是我看不到 EventCounter。

 ASP.NET Core 的 [Application Insights 延伸模組](./azure-web-apps.md)尚不支援此功能。 當此功能受到支援時，將會更新此文件。

## <a name="next-steps"></a><a name="next"></a>後續步驟

* [相依性追蹤](./asp-net-dependencies.md)

