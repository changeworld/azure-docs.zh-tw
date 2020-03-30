---
title: 輔助服務應用（非 HTTP 應用）的應用程式見解
description: 使用 Azure 監視器應用程式見解監視 .NET Core/.NET 框架非 HTTP 應用。
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 34a64ffa67b1a43a77391e0d50ddf1bfad0f73ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501167"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>輔助服務應用程式（非 HTTP 應用程式）的應用程式見解

應用程式見解正在發佈一個新的 SDK，`Microsoft.ApplicationInsights.WorkerService`稱為 ，它最適合非 HTTP 工作負載，如消息傳送、背景工作、主控台應用程式等。這些類型的應用程式不像傳統的ASP.NET/ASP.NET酷 Web 應用程式那樣具有傳入 HTTP 要求的概念，因此不支援將應用程式見解包用於[ASP.NET](asp-net.md)或[ASP.NET Core](asp-net-core.md)應用程式。

新的 SDK 本身不執行任何遙測集合。 相反，它帶來了其他眾所周知的應用程式見解自動收集器，如[依賴收集器](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)[，PerfCounterCollector，](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/)[應用程式見解記錄提供程式](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)等。此 SDK 公開擴充方法`IServiceCollection`，以啟用和配置遙測集合。

## <a name="supported-scenarios"></a>支援的案例

[輔助角色服務的應用程式見解 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)最適合非 HTTP 應用程式，無論它們在何處或如何運行。 如果應用程式正在運行並且與 Azure 具有網路連接，則可以收集遙測資料。 支援應用程式見解監視無處不在.NET Core。 此包可用於新引入的[.NET Core 3.0 輔助服務](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)[、Asp.Net Core 2.1/2.2、](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)主控台應用（.NET Core/.NET Framework）等的背景工作。

## <a name="prerequisites"></a>Prerequisites

有效的應用程式見解檢測金鑰。 此金鑰是向應用程式見解發送任何遙測資料所必需的。 如果需要創建新的應用程式見解資源來獲取檢測金鑰，請參閱[創建應用程式見解資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

## <a name="using-application-insights-sdk-for-worker-services"></a>將應用程式見解 SDK 用於輔助服務

1. 將[Microsoft.應用程式見解.輔助服務](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)包安裝到應用程式。
   以下程式碼片段顯示需要添加到專案檔案中的`.csproj`更改。

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. 調用`AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)`分機方法`IServiceCollection`，提供檢測金鑰。 此方法應在應用程式開始時調用。 具體位置取決於應用程式的類型。

1. 通過調用`ILogger``TelemetryClient``serviceProvider.GetRequiredService<TelemetryClient>();`或使用建構函式注入從依賴項注入 （DI） 容器檢索實例或實例。 此步驟將觸發設置`TelemetryConfiguration`和自動收集模組。

以下各節介紹了每種應用程式類型的具體說明。

## <a name="net-core-30-worker-service-application"></a>.NET 核心 3.0 輔助服務應用程式

完整示例[在此處](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)共用

1. 下載並安裝[.NET 核心 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. 通過使用 Visual Studio 新專案範本或命令列創建新的工作服務專案`dotnet new worker`
3. 將[Microsoft.應用程式見解.輔助服務](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)包安裝到應用程式。

4. 添加到`services.AddApplicationInsightsTelemetryWorkerService();``Program.cs`類中`CreateHostBuilder()`的方法，如以下示例所示：

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. 按以下`Worker.cs`示例修改您的。

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

6. 設置檢測金鑰。

    儘管可以將檢測金鑰作為 參數提供給`AddApplicationInsightsTelemetryWorkerService`，但我們建議您在配置中指定檢測金鑰。 以下代碼示例演示如何在 中`appsettings.json`指定檢測金鑰。 請確保`appsettings.json`在發佈期間複製到應用程式根資料夾。

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

或者，在以下任一環境變數中指定檢測鍵。
`APPINSIGHTS_INSTRUMENTATIONKEY` 或 `ApplicationInsights:InstrumentationKey`

例如： `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
或`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

通常，`APPINSIGHTS_INSTRUMENTATIONKEY`指定作為 Web 作業部署到 Web 應用的應用程式的檢測金鑰。

> [!NOTE]
> 代碼中指定的檢測鍵勝於環境變數`APPINSIGHTS_INSTRUMENTATIONKEY`，它勝於其他選項。

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET託管服務的核心背景工作

[本文檔](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio)介紹如何在 ASP.NET 酷 2.1/2.2 應用程式中創建背景任務。

完整示例[在此處](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)共用

1. 安裝 Microsoft.應用程式見解.輔助服務（https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)應用程式包）。
2. 添加到`services.AddApplicationInsightsTelemetryWorkerService();`方法，`ConfigureServices()`如本示例所示：

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

下面是背景工作邏輯所在的`TimedHostedService`代碼。

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
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. 設置檢測金鑰。
   使用上面`appsettings.json`.NET 核心 3.0 輔助服務示例中的相同。

## <a name="net-corenet-framework-console-application"></a>.NET 核心/.NET 框架主控台應用程式

如本文開頭所述，新包可用於從常規主控台應用程式啟用應用程式見解遙測。 此包面向[`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)，因此可用於 .NET Core 2.0 或更高版本和 .NET 框架 4.7.2 或更高版本中的主控台應用。

完整示例[在此處](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)共用

1. 安裝 Microsoft.應用程式見解.輔助服務（https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)應用程式包）。

2. 修改Program.cs如下例。

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

此主控台應用程式也使用相同的預設值`TelemetryConfiguration`，並且可以自訂它的方式與前面部分中的示例相同。

## <a name="run-your-application"></a>執行您的應用程式

執行您的應用程式。 上述所有示例工作人員每秒對 bing.com進行 HTTP 調用，並使用 ILogger 發出很少的日誌。 這些行在 調用`StartOperation`中包裝`TelemetryClient`，用於創建操作（在此示例中`RequestTelemetry`稱為"操作"）。 應用程式見解將收集這些 ILogger 日誌（預設情況下警告或以上）和依賴項，它們將與父子關係相關`RequestTelemetry`。 相關性還工作跨進程/網路邊界。 例如，如果調用是另一個受監視的元件，則該調用也將與此父元件相關。

可以認為，`RequestTelemetry`此自訂操作等效于典型 Web 應用程式中的傳入 Web 請求。 雖然不需要使用操作，但它最適合[應用程式見解相關資料模型](https://docs.microsoft.com/azure/azure-monitor/app/correlation)- 充當`RequestTelemetry`父操作，在輔助反覆運算中生成的每個遙測資料被視為邏輯上屬於同一操作。 此方法還可確保生成的所有遙測（自動和手動）具有相同的`operation_id`。 由於採樣基於`operation_id`，採樣演算法可以保留或丟棄單個反覆運算中的所有遙測資料。

下面列出了應用程式見解自動收集的完整遙測資料。

### <a name="live-metrics"></a>即時計量

[即時指標](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)可用於快速驗證應用程式見解監視是否配置正確。 雖然遙測資料可能需要幾分鐘時間才能在門戶和分析中顯示，但即時指標會以近乎即時的方式顯示正在運行的進程的 CPU 使用方式。 它還可以顯示其他遙測資料，如請求、依賴項、跟蹤等。

### <a name="ilogger-logs"></a>ILogger 日誌

通過`ILogger`嚴重性`Warning`或更大級別發出的日誌將自動捕獲。 按照[ILogger 文檔](ilogger.md#control-logging-level)自訂應用程式見解捕獲的日誌級別。

### <a name="dependencies"></a>相依性

預設情況下啟用依賴項集合。 [本文介紹了](asp-net-dependencies.md#automatically-tracked-dependencies)自動收集的依賴項，並包含執行手動跟蹤的步驟。

### <a name="eventcounter"></a>事件計數器

`EventCounterCollectionModule`預設情況下啟用，它將從 .NET Core 3.0 應用收集一組預設計數器。 [事件計數器](eventcounters.md)教程列出了收集的預設計數器集。 它還具有自訂清單的說明。

### <a name="manually-tracking-additional-telemetry"></a>手動跟蹤其他遙測資料

雖然 SDK 自動收集如上所述的遙測資料，但在大多數情況下，使用者將需要向應用程式見解服務發送其他遙測資料。 跟蹤其他遙測的建議方法是從依賴項注入獲取的`TelemetryClient`實例，然後調用其中支援的`TrackXXX()` [API](api-custom-events-metrics.md)方法之一。 另一個典型的用例是[自訂操作跟蹤](custom-operations-tracking.md)。 此方法在上面的"輔助角色"示例中進行了演示。

## <a name="configure-the-application-insights-sdk"></a>配置應用程式見解 SDK

輔助服務`TelemetryConfiguration`SDK 使用的預設值類似于ASP.NET或 ASP.NET 核心應用程式中使用的自動設定，減去用於從`HttpContext`中豐富遙測的遙測初始化器。

您可以自訂輔助角色服務的應用程式見解 SDK 以更改預設配置。 應用程式見解ASP.NET核心 SDK 的使用者可能熟悉使用 ASP.NET 酷內置[依賴項注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)來更改配置。 輔助服務 SDK 也基於類似的原則。 通過在 上`IServiceCollection`調用適當的方法，`ConfigureServices()`對節中幾乎所有配置更改進行，如下所述。

> [!NOTE]
> 使用此 SDK 時，不支援通過修改`TelemetryConfiguration.Active`更改配置，並且不會反映更改。

### <a name="using-applicationinsightsserviceoptions"></a>使用應用程式見解服務選項

可以通過傳遞到`ApplicationInsightsServiceOptions``AddApplicationInsightsTelemetryWorkerService`修改一些常見設置，如本示例中所示：

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

請注意，`ApplicationInsightsServiceOptions`此 SDK 中位於命名空間`Microsoft.ApplicationInsights.WorkerService`中，`Microsoft.ApplicationInsights.AspNetCore.Extensions`而不是ASP.NET核心 SDK 中。

中常用的設置`ApplicationInsightsServiceOptions`

|設定 | 描述 | 預設
|---------------|-------|-------
|啟用快速脈衝測量流 | 啟用/禁用即時指標功能 | true
|啟用自我調整採樣 | 啟用/禁用自我調整採樣 | true
|啟用活動訊號 | 啟用/禁用活動訊號功能，該功能定期（預設值 15 分鐘）發送名為"HeartBeatState"的自訂指標，其中包含有關運行時的資訊，如 .NET 版本、Azure 環境資訊（如果適用）。等等。 | true
|添加自動收集公選器 | 啟用/禁用自動收集指標提取器，這是一個遙測處理器，用於在進行採樣之前發送有關請求/依賴項的預聚合指標。 | true

有關最新清單，請參閱[中的`ApplicationInsightsServiceOptions`可配置設置](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>取樣

適用于輔助角色服務的應用程式見解 SDK 支援固定利率和自我調整採樣。 預設情況下啟用自我調整採樣。 配置輔助服務採樣的方式與[ASP.NET核心應用程式](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)相同。

### <a name="adding-telemetryinitializers"></a>添加遙測初始化器

如果要定義使用所有遙測發送的屬性，請使用[遙測初始化程式](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer)。

向`TelemetryInitializer``DependencyInjection`容器添加任何新元件，SDK 將自動將它們添加到 。 `TelemetryConfiguration`

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>刪除遙測初始化器

預設情況下存在遙測初始化器。 要刪除所有或特定的遙測初始化器，請使用以下示例代碼 *，在*調用`AddApplicationInsightsTelemetryWorkerService()`後。

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

### <a name="adding-telemetry-processors"></a>添加遙測處理器

可以使用 上的擴充方法`TelemetryConfiguration``AddApplicationInsightsTelemetryProcessor`將自訂遙測處理器添加到 。 `IServiceCollection` 在[高級篩選方案中](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)使用遙測處理器，以便更直接地控制發送到應用程式見解服務的遙測資料中包含的或排除的內容。 使用以下示例。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>配置或刪除預設遙測模組

應用程式見解使用遙測模組自動收集有關特定工作負載的遙測資料，而無需手動跟蹤。

預設情況下啟用以下自動收集模組。 這些模組負責自動收集遙測資料。 您可以禁用或配置它們以更改其預設行為。

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

要配置任何預設值`TelemetryModule`，請使用 上的`ConfigureTelemetryModule<T>``IServiceCollection`擴充方法，如以下示例所示。

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

### <a name="configuring-telemetry-channel"></a>配置遙測通道

預設通道為`ServerTelemetryChannel`。 您可以重寫它，如以下示例所示。

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

### <a name="disable-telemetry-dynamically"></a>動態禁用遙測

如果要有條件和動態地禁用遙測，則可以在代碼中的任意位置使用ASP.NET`TelemetryConfiguration`核心依賴項注入容器解析實例，並在該容器上設置`DisableTelemetry`標誌。

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

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何跟蹤未自動收集的遙測資料？

使用建構函式注入`TelemetryClient`獲取 的實例，並在其上調用所需的`TrackXXX()`方法。 我們不建議創建新`TelemetryClient`實例。 的`TelemetryClient`單個實例已在`DependencyInjection`容器中註冊，該容器與其余遙測資料`TelemetryConfiguration`共用。 僅當新`TelemetryClient`實例需要獨立于遙測的其餘部分的配置時，才建議創建新實例。

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>我能否使用 Visual Studio IDE 將應用程式見解添加到輔助服務專案中？

視覺工作室 IDE 載入目前僅支援ASP.NET/ASP.NET核心應用程式。 當 Visual Studio 提供對載入輔助服務應用程式的支援時，本文檔將更新。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>我能否使用狀態監視器等工具啟用應用程式見解監控？

否。 [狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)和[狀態監視器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)當前僅支援 ASP.NET 4.x。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果我在 Linux 中運行應用程式，是否支援所有功能？

是。 除以下情況外，此 SDK 的功能支援在所有平臺中都是一樣的：

* 效能計數器僅在 Windows 中受支援，但即時指標中顯示的進程 CPU/記憶體除外。
* 即使`ServerTelemetryChannel`預設情況下啟用，如果應用程式在 Linux 或 MacOS 中運行，通道不會自動創建本機存放區資料夾，以臨時保留遙測（如果存在網路問題）。 由於此限制，當存在臨時網路或伺服器問題時，遙測將丟失。 要解決此問題，請為通道配置本地資料夾：

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

[.NET 核心主控台應用程式](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)如果您使用的是以 .NET 核心 （2.0 或更高） 或 .NET 框架 （4.7.2 或更高） 編寫的主控台應用程式，請使用此示例

[ASP .NET 具有託管服務的背景工作](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)如果您使用的是 Asp.Net Core 2.1/2.2，則使用此示例，並在此處根據[官方指南創建](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)背景工作

[.NET 核心 3.0 員工服務](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)如果您根據此處的官方指南使用 .NET Core 3.0 工作人員服務應用程式[，請使用此示例](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>開放原始碼 SDK

[閱讀並貢獻代碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)。

## <a name="next-steps"></a>後續步驟

* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)發送您自己的事件和指標，以便詳細查看應用的性能和使用方式。
* [跟蹤未自動跟蹤的其他依賴項](../../azure-monitor/app/auto-collect-dependencies.md)。
* [豐富或篩選自動收集的遙測](../../azure-monitor/app/api-filtering-sampling.md)資料。
* [ASP.NET核心中的依賴注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。
