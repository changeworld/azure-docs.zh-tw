---
title: 背景工作服務應用程式的 Application Insights （非 HTTP 應用程式）
description: 使用 Azure 監視器 Application Insights 監視 .NET Core/NET Framework 非 HTTP 應用程式。
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1f9b35022e63c4c3fe671237149602f8db465466
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83117872"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>背景工作服務應用程式的 Application Insights （非 HTTP 應用程式）

Application Insights 發行新的 SDK，稱為 `Microsoft.ApplicationInsights.WorkerService` ，最適合用於訊息、背景工作、主控台應用程式等非 HTTP 工作負載。這些類型的應用程式不會有傳入 HTTP 要求的概念，例如傳統的 ASP.NET/ASP.NET 核心 Web 應用程式，因此不支援使用適用于[ASP.NET](asp-net.md)或[ASP.NET Core](asp-net-core.md)應用程式的 Application Insights 套件。

新的 SDK 本身不會執行任何遙測收集。 相反地，它會引進其他知名的 Application Insights 自動收集器，例如[microsoft.applicationinsights.dependencycollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)、 [microsoft.applicationinsights.perfcountercollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/)、 [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)等。此 SDK 會在上公開擴充方法 `IServiceCollection` ，以啟用和設定遙測收集。

## <a name="supported-scenarios"></a>支援的案例

適用于背景[工作角色服務的 APPLICATION INSIGHTS SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)最適合非 HTTP 應用程式，不論其執行位置或方式為何。 如果您的應用程式正在執行，且具有與 Azure 的網路連線，則可以收集遙測。 支援 .NET Core 的任何位置都支援 Application Insights 監視。 此套件可用於新引進的[.Net Core 3.0 背景工作角色服務](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)、 [Asp.Net Core 2.1/2.2 的背景](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)工作、主控台應用程式（.net Core/.NET Framework）等。

## <a name="prerequisites"></a>必要條件

有效的 Application Insights 檢測金鑰。 必須要有此金鑰，才能將任何遙測資料傳送至 Application Insights。 如果您需要建立新的 Application Insights 資源來取得檢測金鑰，請參閱[建立 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

## <a name="using-application-insights-sdk-for-worker-services"></a>使用適用于背景工作角色服務的 Application Insights SDK

1. 將[ApplicationInsights WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)套件安裝到應用程式。
   下列程式碼片段顯示需要新增至專案檔的變更 `.csproj` 。

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. 呼叫 `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` 上的擴充方法 `IServiceCollection` ，並提供檢測金鑰。 這個方法應該在應用程式開始時呼叫。 確切的位置取決於應用程式的類型。

1. 藉 `ILogger` `TelemetryClient` 由呼叫或使用函式插入，從相依性插入（DI）容器中取出實例或實例 `serviceProvider.GetRequiredService<TelemetryClient>();` 。 此步驟會觸發設定 `TelemetryConfiguration` 和自動收集模組。

下列各節將說明每種應用程式類型的特定指示。

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 背景工作服務應用程式

完整範例會在[這裡](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)共用

1. 下載並安裝[.Net Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. 使用 Visual Studio 新增專案範本或命令列，建立新的背景工作服務專案`dotnet new worker`
3. 將[ApplicationInsights WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)套件安裝到應用程式。

4. 將新增 `services.AddApplicationInsightsTelemetryWorkerService();` 至 `CreateHostBuilder()` 類別中的方法 `Program.cs` ，如下列範例所示：

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. `Worker.cs`根據下列範例修改您的。

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. 設定檢測金鑰。

    雖然您可以提供檢測金鑰做為的引數 `AddApplicationInsightsTelemetryWorkerService` ，但我們建議您在設定中指定檢測金鑰。 下列程式碼範例顯示如何在中指定檢測金鑰 `appsettings.json` 。 請確定在 `appsettings.json` 發行期間，會將複製到應用程式根資料夾。

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

或者，在下列其中一個環境變數中指定檢測金鑰。
`APPINSIGHTS_INSTRUMENTATIONKEY` 或 `ApplicationInsights:InstrumentationKey`

例如：`SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
或`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

通常會 `APPINSIGHTS_INSTRUMENTATIONKEY` 針對部署至 Web Apps 做為 Web 作業的應用程式指定檢測金鑰。

> [!NOTE]
> 在程式碼中指定的檢測金鑰是透過環境變數 `APPINSIGHTS_INSTRUMENTATIONKEY` 來進行，這會優先于其他選項。

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>使用託管服務 ASP.NET Core 背景工作

[本](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio)檔說明如何在 ASP.NET Core 2.1/2.2 應用程式中建立背景工作。

完整範例會在[這裡](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)共用

1. 將 WorkerService （套件）安裝 https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 至應用程式。
2. 將加入 `services.AddApplicationInsightsTelemetryWorkerService();` 至 `ConfigureServices()` 方法，如下列範例所示：

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

以下是 `TimedHostedService` 背景工作邏輯所在位置的程式碼。

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. 設定檢測金鑰。
   使用與 `appsettings.json` 上述 .Net Core 3.0 背景工作角色服務範例相同的。

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework 主控台應用程式

如同本文開頭所述，新封裝可以用來啟用甚至是一般主控台應用程式的 Application Insights 遙測。 此套件 [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard) 的目標是，因此可用於 .Net Core 2.0 或更高版本中的主控台應用程式，並 .NET Framework 4.7.2 或更高版本。

完整範例會在[這裡](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)共用

1. 將 WorkerService （套件）安裝 https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 至應用程式。

2. 修改 Program.cs，如下列範例所示。

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

這個主控台應用程式也會使用相同的預設值 `TelemetryConfiguration` ，而且可以用與先前章節中的範例相同的方式進行自訂。

## <a name="run-your-application"></a>執行您的應用程式

執行您的應用程式。 上述所有的範例背景工作會每秒發出一次 HTTP 呼叫來 bing.com，同時也會使用 ILogger 發出一些記錄。 這些行會包裝在 `StartOperation` 的呼叫內 `TelemetryClient` ，用來建立作業（在此範例中 `RequestTelemetry` 名為 "operation"）。 Application Insights 會收集這些 ILogger 記錄（預設為警告或以上）和相依性，而且它們會 `RequestTelemetry` 與具有父子式關聯性的相互關聯。 相互關聯也適用于跨進程/網路界限。 例如，如果對另一個受監視的元件進行呼叫，它也會與此父系相互關聯。

這項自訂作業 `RequestTelemetry` 可視為一般 Web 應用程式中傳入的 web 要求。 雖然不一定要使用作業，但它最適合用於 Application Insights 的相互[關聯資料模型](https://docs.microsoft.com/azure/azure-monitor/app/correlation)，其可 `RequestTelemetry` 做為父系作業，而在背景工作反復專案中產生的每個遙測會視為邏輯上屬於相同作業。 這種方法也可確保所有產生的遙測（自動和手動）都會有相同的 `operation_id` 。 當取樣是以為基礎時 `operation_id` ，取樣演算法會保留或卸載單一反復專案中的所有遙測。

下列列出 Application Insights 自動收集的完整遙測。

### <a name="live-metrics"></a>即時計量    

[即時計量](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)可以用來快速驗證是否已正確設定 Application Insights 監視。 雖然可能需要幾分鐘的時間，遙測才會開始出現在入口網站和分析中，但即時計量會以近乎即時的方式顯示執行中進程的 CPU 使用量。 它也可以顯示其他遙測，例如要求、相依性、追蹤等等。

### <a name="ilogger-logs"></a>ILogger 記錄

`ILogger` `Warning` 系統會自動捕捉透過嚴重性或更新版本發出的記錄。 遵循[ILogger](ilogger.md#control-logging-level)檔來自訂 Application Insights 所要捕獲的記錄層級。

### <a name="dependencies"></a>相依性

依預設會啟用相依性集合。 [本文說明](asp-net-dependencies.md#automatically-tracked-dependencies)自動收集的相依性，也包含執行手動追蹤的步驟。

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`預設為啟用，而且會從 .NET Core 3.0 應用程式收集一組預設的計數器。 [EventCounter](eventcounters.md)教學課程會列出所收集的預設計數器集合。 其中也包含自訂清單的指示。

### <a name="manually-tracking-additional-telemetry"></a>手動追蹤其他遙測

雖然 SDK 會自動收集如上面所述的遙測，但在大多數情況下，使用者必須將額外的遙測傳送至 Application Insights 服務。 追蹤額外遙測的建議方式是從相依性插入取得的實例 `TelemetryClient` ，然後在其中呼叫其中一個支援的 `TrackXXX()` [API](api-custom-events-metrics.md)方法。 另一個典型的使用案例是[作業的自訂追蹤](custom-operations-tracking.md)。 這種方法會在上述的背景工作範例中示範。

## <a name="configure-the-application-insights-sdk"></a>設定 Application Insights SDK

背景 `TelemetryConfiguration` 工作服務 SDK 所使用的預設值類似于 ASP.NET 或 ASP.NET Core 應用程式中使用的自動設定，減去用來從擴充遙測的 TelemetryInitializers `HttpContext` 。

您可以自訂適用于背景工作角色服務的 Application Insights SDK，以變更預設設定。 Application Insights ASP.NET Core SDK 的使用者可能很熟悉使用 ASP.NET Core 內建相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)來變更設定。 WorkerService SDK 也是以類似的原則為基礎。 藉 `ConfigureServices()` 由在上呼叫適當的方法，在區段中進行幾乎所有設定變更 `IServiceCollection` ，如下所述。

> [!NOTE]
> 使用此 SDK 時，不支援透過修改來變更設定 `TelemetryConfiguration.Active` ，而且不會反映變更。

### <a name="using-applicationinsightsserviceoptions"></a>使用 ApplicationInsightsServiceOptions

您可以藉由傳遞至來修改一些一般設定 `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService` ，如下列範例所示：

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

請注意， `ApplicationInsightsServiceOptions` 在此 sdk 中，是在命名空間中，而不是在 `Microsoft.ApplicationInsights.WorkerService` `Microsoft.ApplicationInsights.AspNetCore.Extensions` ASP.NET Core SDK 中。

中常用的設定`ApplicationInsightsServiceOptions`

|設定 | 描述 | 預設
|---------------|-------|-------
|EnableQuickPulseMetricStream | 啟用/停用 LiveMetrics 功能 | true
|EnableAdaptiveSampling | 啟用/停用調適型取樣 | true
|EnableHeartbeat | [啟用/停用心跳] 功能會定期（15分鐘的預設值）傳送名為 ' HeartBeatState ' 的自訂計量，其中包含執行時間的相關資訊，例如 .NET 版本、Azure 環境資訊（如果適用）等等。 | true
|AddAutoCollectedMetricExtractor | 啟用/停用 AutoCollectedMetrics 解壓縮程式，這是一種 TelemetryProcessor，會在進行取樣之前，先傳送要求/相依性的預先匯總計量。 | true

如需最新清單，請參閱[中 `ApplicationInsightsServiceOptions` 的可設定設定](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>取樣

Application Insights SDK for Worker 服務同時支援固定速率和調適型取樣。 預設會啟用調適型取樣。 為背景工作角色服務設定取樣的方式，與[ASP.NET Core 應用程式](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)的做法相同。

### <a name="adding-telemetryinitializers"></a>加入 TelemetryInitializers

當您想要定義與所有遙測一起傳送的屬性時，請使用[遙測初始化運算式](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer)。

將任何新 `TelemetryInitializer` 的新增至 `DependencyInjection` 容器，SDK 就會自動將其加入至 `TelemetryConfiguration` 。

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>移除 TelemetryInitializers

預設會顯示遙測初始化運算式。 若要移除所有或特定的遙測初始化運算式，請在呼叫*之後*使用下列範例程式碼 `AddApplicationInsightsTelemetryWorkerService()` 。

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>新增遙測處理器

您可以 `TelemetryConfiguration` 使用上的擴充方法，將自訂遙測處理器加入至 `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` 。 您可以在「[高級篩選」案例](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)中使用遙測處理器，以更直接控制您傳送至 Application Insights 服務的遙測資料所包含或排除的內容。 請使用下列範例。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>設定或移除預設 TelemetryModules

Application Insights 使用遙測模組自動收集有關特定工作負載的遙測，而不需要手動追蹤。

預設會啟用下列自動收集模組。 這些模組負責自動收集遙測。 您可以停用或設定它們來改變其預設行為。

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`-（目前有關于此遙測模組的問題。 如需暫時的解決方法，請參閱[GitHub 問題 1689](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
)。）
* `AzureInstanceMetadataTelemetryModule`

若要設定任何預設值 `TelemetryModule` ，請在上使用擴充方法 `ConfigureTelemetryModule<T>` `IServiceCollection` ，如下列範例所示。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>正在設定遙測通道

預設的通道是 `ServerTelemetryChannel` 。 您可以覆寫它，如下列範例所示。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>動態停用遙測

如果您想要有條件且動態地停用遙測，您可以在 `TelemetryConfiguration` 程式碼中的任何位置使用 ASP.NET Core 相依性插入容器來解析實例，並 `DisableTelemetry` 在其上設定旗標。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何追蹤不會自動收集的遙測？

使用函式插入來取得的實例 `TelemetryClient` ，並在其上呼叫所需的 `TrackXXX()` 方法。 我們不建議您建立新 `TelemetryClient` 的實例。 的單一實例 `TelemetryClient` 已經在容器中註冊 `DependencyInjection` ，而這會 `TelemetryConfiguration` 與其余的遙測資料共用。 `TelemetryClient`只有當您需要的設定與其余的遙測資料不同時，才建議建立新的實例。

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>我可以使用 Visual Studio IDE 將 Application Insights 上線至背景工作角色服務專案嗎？

目前僅針對 ASP.NET/ASP.NET 核心應用程式支援 Visual Studio IDE 上架。 當 Visual Studio 提供上線工作者服務應用程式的支援時，將會更新這份檔。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>我可以使用狀態監視器之類的工具來啟用 Application Insights 監視嗎？

否。 [狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)和[狀態監視器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)目前僅支援 ASP.NET 4.x。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果我在 Linux 中執行應用程式，是否支援所有功能？

是。 此 SDK 的功能支援在所有平臺上都相同，但有下列例外狀況：

* 只有在 [即時計量] 中顯示的進程 CPU/記憶體除外，才支援 Windows 中的效能計數器。
* 雖然 `ServerTelemetryChannel` 預設會啟用，但如果應用程式是在 Linux 或 MacOS 中執行，則通道不會自動建立本機儲存體資料夾，以在發生網路問題時暫時保存遙測。 由於這項限制，當發生暫時性網路或伺服器問題時，就會遺失遙測。 若要解決此問題，請設定通道的本機資料夾：

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>範例應用程式

[.Net Core 主控台應用程式](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)如果您使用的是以 .NET Core （2.0 或更新版本）或 .NET Framework （4.7.2 或更高版本）撰寫的主控台應用程式，請使用此範例

[使用 HostedServices 的 ASP .Net Core 背景](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)工作如果您是在 Asp.Net Core 2.1/2.2 中，請使用此範例，並根據[此處](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)的官方指引建立背景工作

[.Net Core 3.0 背景工作服務](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)如果您有 .NET Core 3.0 背景工作角色服務應用程式，請依照[這裡](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)的官方指引使用此範例

## <a name="open-source-sdk"></a>開放原始碼 SDK

[閱讀並參與程式碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)。

## <a name="next-steps"></a>後續步驟

* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)來傳送您自己的事件和計量，以深入瞭解應用程式的效能和使用方式。
* [追蹤不會自動追蹤的其他](../../azure-monitor/app/auto-collect-dependencies.md)相依性。
* 擴充[或篩選自動收集的遙測](../../azure-monitor/app/api-filtering-sampling.md)。
* [ASP.NET Core 中的](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)相依性插入。
