---
title: 應用程式見解中的事件計數器 |微軟文檔
description: 監視系統和自訂 .NET/.NET 核心事件計數器在應用程式見解中。
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663584"
---
# <a name="eventcounters-introduction"></a>事件計數器介紹

`EventCounter`是 .NET/.NET 核心機制，用於發佈和使用計數器或統計資訊。 [本文檔](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)概述了`EventCounters`如何發佈和使用它們。 所有作業系統平臺都支援事件計數器 - Windows、Linux 和 macOS。 它可以被視為僅在 Windows 系統中支援[的效能計數器](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter)的跨平臺等效項。

雖然使用者可以發佈任何自訂以滿足`EventCounters`其需求，但 .NET Core 3.0 運行時預設發佈一組這些計數器。 本文檔將演練在 Azure 應用程式見解中收集和查看`EventCounters`（系統定義或使用者定義）所需的步驟。

## <a name="using-application-insights-to-collect-eventcounters"></a>使用應用程式見解收集事件計數器

應用程式見解支援`EventCounters`收集其`EventCounterCollectionModule`，這是新發佈的 nuget 包[Microsoft.應用程式見解.事件計數器收集器的一](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)部分。 `EventCounterCollectionModule`使用[AspNetCore](asp-net-core.md)或[輔助服務](worker-service.md)時，將自動啟用 。 `EventCounterCollectionModule`收集不可配置收集頻率為 60 秒的計數器。 收集事件計數器不需要特殊許可權。

## <a name="default-counters-collected"></a>收集的預設計數器

對於在 .NET Core 3.0 中運行的應用，SDK 會自動收集以下計數器。 計數器的名稱將為"類別"計數器"。

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
> 類別 Microsoft.AspNetCore.Hosting 的計數器僅在ASP.NET核心應用程式中添加。

## <a name="customizing-counters-to-be-collected"></a>自訂要收集的計數器

下面的示例演示如何添加/刪除計數器。 此自訂將在應用程式`ConfigureServices`方法中使用 或`AddApplicationInsightsTelemetry()``AddApplicationInsightsWorkerService()`啟用應用程式見解遙測集合後完成。 下面是來自ASP.NET核心應用程式的示例代碼。 對於其他類型的應用程式，請參閱[本文檔](worker-service.md#configuring-or-removing-default-telemetrymodules)。

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

## <a name="event-counters-in-metric-explorer"></a>指標資源管理器中的事件計數器

要查看[指標資源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)中的事件計數器指標，請選擇"應用程式見解"資源，並選擇基於日誌的指標作為指標命名空間。 然後，事件計數器指標將顯示在"自訂"類別下。

> [!div class="mx-imgBorder"]
> ![應用程式見解中報告的事件計數器](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>分析中的事件計數器

您還可以在**自訂指標**表中搜索和顯示["分析](../../azure-monitor/app/analytics.md)"中的事件計數器報表。

例如，運行以下查詢以查看收集哪些計數器並可供查詢：

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![應用程式見解中報告的事件計數器](./media/event-counters/analytics-event-counters.png)

要獲取最近一段時間內特定計數器的圖表（例如： `ThreadPool Completed Work Item Count`），請運行以下查詢。

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![在應用程式見解中聊天單個計數器](./media/event-counters/analytics-completeditems-counters.png)

與其他遙測資料一樣 **，customMetrics**也有一`cloud_RoleInstance`列，指示應用正在其上運行的主機伺服器實例的標識。 上述查詢顯示每個實例的計數器值，可用於比較不同伺服器實例的性能。

## <a name="alerts"></a>警示
與其他指標一樣，您可以[設置警報](../../azure-monitor/app/alerts.md)，以便在事件計數器超出您指定的限制時發出警告。 開啟 [警示] 窗格，然後按一下 [新增警示]。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="can-i-see-eventcounters-in-live-metrics"></a>我可以在即時指標中查看事件計數器嗎？

截至今天，即時指標不顯示事件計數器。 使用指標資源管理器或分析查看遙測資料。

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>我可以查看哪些平臺的預設清單 .NET Core 3.0 計數器？

EventCounter 不需要任何特殊許可權，並且在所有平臺中都支援 .NET Core 3.0。 這包括：

* **作業系統**：Windows、Linux 或 macOS。
* **託管方法**：在進程或進程外。
* **部署方法**：框架相關或自包含。
* **網路伺服器**：IIS（互聯網資訊伺服器）或凱斯特雷爾。
* **託管平臺**：Azure 應用服務、Azure VM、Docker、Azure 庫伯奈斯服務 （AKS） 等的 Web 應用功能。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>我已經從 Azure Web 應用門戶啟用了應用程式見解。 但我看不到事件計數器。

 ASP.NET核心[應用程式見解擴展](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps)尚未支援此功能。 此文檔將在支援此功能時更新。

## <a name="next-steps"></a><a name="next"></a>後續步驟

* [相依性追蹤](../../azure-monitor/app/asp-net-dependencies.md)
