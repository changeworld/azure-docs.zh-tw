---
title: 協助服務應用程式(非 HTTP 應用程式)
description: 使用 Azure 監視器應用程式見解監視 .NET Core/.NET 框架非 HTTP 應用。
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f043140e5a342d114f777ad16bba588790b7f8cc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536721"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>協助服務應用程式(非 HTTP 應用程式) 的應用程式見解

應用程式見解正在發佈一個新的 SDK,`Microsoft.ApplicationInsights.WorkerService`稱為 ,它最適合非 HTTP 工作負載,如消息傳送、後台任務、控制台應用程式等。這些類型的應用程式不像傳統的ASP.NET/ASP.NET酷 Web 應用程式那樣具有傳入 HTTP 請求的概念,因此不支援將應用程式見解包用於[ASP.NET](asp-net.md)或[ASP.NET Core](asp-net-core.md)應用程式。

新的 SDK 本身不執行任何遙測集合。 相反,它帶來了其他眾所周知的應用程式見解自動收集器,如[依賴收集器](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)[,PerfCounterCollector,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/)[應用程式見解記錄提供程式](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)等。此 SDK 公開`IServiceCollection`擴充方法 ,以啟用和設定遙測集合。

## <a name="supported-scenarios"></a>支援的案例

[輔助角色服務的應用程式見解 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)最適合非 HTTP 應用程式,無論它們在何處或如何運行。 如果應用程式正在運行並且與 Azure 具有網路連接,則可以收集遙測數據。 支援應用程式見解監視無處不在.NET Core。 此包可用於新引入的[.NET Core 3.0 輔助服務](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)[、Asp.Net Core 2.1/2.2、](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)控制台應用(.NET Core/.NET Framework)等的後台任務。

## <a name="prerequisites"></a>Prerequisites

有效的應用程式見解檢測密鑰。 此密鑰是向應用程式見解發送任何遙測數據所必需的。 如果需要建立新的應用程式見解資源來取得偵測金鑰,請參閱[建立應用程式見解資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

## <a name="using-application-insights-sdk-for-worker-services"></a>將應用程式見解 SDK 用於協助服務

1. 將[Microsoft.應用程式見解.輔助服務](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)包安裝到應用程式。
   以下代碼段顯示需要添加到專案檔中的`.csproj`更改。

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. 呼叫`AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)`分機`IServiceCollection`方法 ,提供檢測密鑰。 此方法應在應用程式開始時調用。 具體位置取決於應用程式的類型。

1. 通過調用`ILogger``TelemetryClient``serviceProvider.GetRequiredService<TelemetryClient>();`或使用建構函數注入從依賴項注入 (DI) 容器檢索實例或實例。 此步驟將觸發設置`TelemetryConfiguration`和自動收集模組。

以下各節介紹了每種應用程序類型的具體說明。

## <a name="net-core-30-worker-service-application"></a>.NET 核心 3.0 輔助服務應用程式

完整範例[在此處](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)分享

1. 下載並安裝[.NET 核心 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. 使用 Visual Studio 新專案樣本或命令列建立新的工作服務專案`dotnet new worker`
3. 將[Microsoft.應用程式見解.輔助服務](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)包安裝到應用程式。

4. 新增到`services.AddApplicationInsightsTelemetryWorkerService();``Program.cs`類別`CreateHostBuilder()`的方法,如以下範例所示:

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

    儘管可以將檢測密鑰作為 參數提供`AddApplicationInsightsTelemetryWorkerService`給 ,但我們建議您在配置中指定檢測密鑰。 以下代碼示例演示如何在`appsettings.json`中指定檢測密鑰。 請確保`appsettings.json`在發佈期間複製到應用程式根資料夾。

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

或者,在以下任一環境變數中指定檢測鍵。
`APPINSIGHTS_INSTRUMENTATIONKEY` 或 `ApplicationInsights:InstrumentationKey`

例如： `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
或`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

通常,`APPINSIGHTS_INSTRUMENTATIONKEY`指定作為 Web 作業部署到 Web 應用的應用程式的檢測密鑰。

> [!NOTE]
> 代碼中指定的檢測鍵勝於環境變數`APPINSIGHTS_INSTRUMENTATIONKEY`,它勝於其他選項。

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET託管服務的核心後臺任務

[本文檔](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio)介紹如何在 ASP.NET 酷 2.1/2.2 應用程式中創建背景任務。

完整範例[在此處](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)分享

1. 安裝 Microsoft.應用程式見解.輔助服務https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)( 應用程式包)。
2. 新增到`services.AddApplicationInsightsTelemetryWorkerService();`方法,`ConfigureServices()`如本範例所示:

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

下面是後台任務邏輯所在的`TimedHostedService`代碼。

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

3. 設置檢測金鑰。
   使用上面`appsettings.json`.NET 核心 3.0 輔助服務範例中的相同。

## <a name="net-corenet-framework-console-application"></a>.NET 核心/.NET 框架主控台應用程式

如本文開頭所述,新包可用於從常規控制台應用程式啟用應用程式見解遙測。 此包面向[`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard),因此可用於 .NET Core 2.0 或更高版本和 .NET 框架 4.7.2 或更高版本中的控制台應用。

完整範例[在此處](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)分享

1. 安裝 Microsoft.應用程式見解.輔助服務https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)( 應用程式包)。

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

此主控台應用程式也使用相同的預設`TelemetryConfiguration`值,並且可以自訂它的方式與前面部分中的範例相同。

## <a name="run-your-application"></a>執行您的應用程式

執行您的應用程式。 上述所有示例工作人員每秒對 bing.com進行 HTTP 調用,並使用 ILogger 發出很少的日誌。 這些行在調用`StartOperation`中`TelemetryClient`包裝 ,用於創建操作(在此示`RequestTelemetry`例中 稱為"操作")。 應用程式見解將收集這些 ILogger 紀錄(預設情況下警告或以上)和相依項,它們將與父子`RequestTelemetry`關係相關 。 相關性還工作跨進程/網路邊界。 例如,如果調用是另一個受監視的元件,則該調用也將與此父元件相關。

可以認為,`RequestTelemetry`此自定義操作等效於典型 Web 應用程式中的傳入 Web 請求。 雖然不需要使用操作,但它最適合[應用程式見解相關數據模型](https://docs.microsoft.com/azure/azure-monitor/app/correlation)-`RequestTelemetry`充當 父操作,在輔助反覆運算中生成的每個遙測數據被視為邏輯上屬於同一操作。 此方法可以確保產生的所有遙測(自動和手動)具有相同的`operation_id`。 由於採樣基於`operation_id`,採樣演演演算法可以保留或丟棄單個反覆運算中的所有遙測數據。

下面列出了應用程序見解自動收集的完整遙測數據。

### <a name="live-metrics"></a>即時計量

[即時指標](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)可用於快速驗證應用程式見解監視是否配置正確。 雖然遙測數據可能需要幾分鐘時間才能在門戶和分析中顯示,但即時指標會以近乎即時的方式顯示正在運行的進程的 CPU 使用方式。 它還可以顯示其他遙測數據,如請求、依賴項、跟蹤等。

### <a name="ilogger-logs"></a>ILogger 紀錄

通過`ILogger`嚴重`Warning`性 或更大級別發出的日誌將自動捕獲。 按照[ILogger 文件](ilogger.md#control-logging-level)自定義應用程式見解捕獲的日誌級別。

### <a name="dependencies"></a>相依性

默認情況下啟用依賴項集合。 [本文介紹了](asp-net-dependencies.md#automatically-tracked-dependencies)自動收集的依賴項,並包含執行手動跟蹤的步驟。

### <a name="eventcounter"></a>事件計數器

`EventCounterCollectionModule`默認情況下啟用,它將從 .NET Core 3.0 應用收集一組預設計數器。 [事件計數器](eventcounters.md)教程列出了收集的預設計數器集。 它還具有自定義清單的說明。

### <a name="manually-tracking-additional-telemetry"></a>手動追蹤其他遙測資料

雖然 SDK 自動收集如上所述的遙測數據,但在大多數情況下,使用者將需要向應用程式見解服務發送其他遙測數據。 跟蹤其他遙測的建議方法是從依賴項注入獲取的`TelemetryClient`實例,然後調用其中支援`TrackXXX()`的[API](api-custom-events-metrics.md)方法之一。 另一個典型的用例是[自訂操作追蹤](custom-operations-tracking.md)。 此方法在上面的"輔助角色"範例中進行了演示。

## <a name="configure-the-application-insights-sdk"></a>設定應用程式見解 SDK

輔助服務`TelemetryConfiguration`SDK 使用的預設值類似於ASP.NET或 ASP.NET 核心應用程式中使用的自動配置,`HttpContext`減去用於從 中豐富遙測的遙測初始化器。

您可以自定義輔助角色服務的應用程式見解 SDK 以更改預設配置。 應用程式見解ASP.NET核心 SDK 的使用者可能熟悉使用 ASP.NET 酷內置[依賴項注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)來更改配置。 輔助服務 SDK 也基於類似的原則。 通過在`IServiceCollection`上調用適當的方法`ConfigureServices()`, 對節中幾乎所有配置更改進行,如下所述。

> [!NOTE]
> 使用此 SDK 時,不支援`TelemetryConfiguration.Active`透過修改更改配置,並且不會反映更改。

### <a name="using-applicationinsightsserviceoptions"></a>使用應用程式的語音服務選項

可以通過傳遞`ApplicationInsightsServiceOptions``AddApplicationInsightsTelemetryWorkerService`到 修改一些常見設置,如本示例中所示:

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

請注意,`ApplicationInsightsServiceOptions`此 SDK 中`Microsoft.ApplicationInsights.WorkerService`位於命名`Microsoft.ApplicationInsights.AspNetCore.Extensions`空間 中, 而不是 ASP.NET 核心 SDK 中。

中常用的設定`ApplicationInsightsServiceOptions`

|設定 | 描述 | 預設
|---------------|-------|-------
|開啟快速脈衝測量串流 | 開啟/關閉即時指標功能 | true
|開啟自我調整樣樣 | 開啟/停用自我調整取樣 | true
|啟用偵測訊號 | 啟用/禁用檢測信號功能,該功能定期(預設值 15 分鐘)發送名為"HeartBeatState"的自定義指標,其中包含有關運行時的資訊,如 .NET 版本、Azure 環境資訊(如果適用)。等等。 | true
|新增自動收集公選器 | 啟用/禁用自動收集指標提取器,這是一個遙測處理器,用於在進行採樣之前發送有關請求/依賴項的預聚合指標。 | true

關於最新清單,請參考[的`ApplicationInsightsServiceOptions`可設定設定](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>取樣

適用於輔助角色服務的應用程式見解 SDK 支援固定利率和自適應採樣。 默認情況下啟用自適應採樣。 配置輔助服務採樣的方式與[ASP.NET核心應用程式](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)相同。

### <a name="adding-telemetryinitializers"></a>新增遙測初始化器

如果要定義使用所有遙測傳送的屬性,請使用[遙測初始化程式](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer)。

新增`TelemetryInitializer``DependencyInjection`元件,SDK 會自動將它們加入 。 `TelemetryConfiguration`

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>刪除遙測初始化器

默認情況下存在遙測初始化器。 要刪除所有或特定的遙測初始化器,請使用以下範例代碼 *,在*呼`AddApplicationInsightsTelemetryWorkerService()`叫後。

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

可以使用上的擴充`TelemetryConfiguration``AddApplicationInsightsTelemetryProcessor`方法 將自訂遙測處理器加入`IServiceCollection`。 在[進階篩選方案中](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)使用遙測處理器,以便更直接地控制發送到應用程式見解服務的遙測數據中包含的或排除的內容。 使用以下示例。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>設定或移除預設遙測模組

應用程式見解使用遙測模組自動收集有關特定工作負載的遙測數據,而無需手動跟蹤。

默認情況下啟用以下自動收集模組。 這些模組負責自動收集遙測數據。 您可以關閉或設定它們以更改其預設行為。

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

要配置任何預設值`TelemetryModule`,請使用`ConfigureTelemetryModule<T>``IServiceCollection`上的擴充方法,如以下範例所示。

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

### <a name="configuring-telemetry-channel"></a>設定遙測頻道

預設色版`ServerTelemetryChannel`為 。 您可以重寫它,如以下範例所示。

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

如果要有條件和動態地禁用遙測,則可以在代碼中的任意位置使用ASP.NET`TelemetryConfiguration`核心依賴項注入容器解析實例,並在該容器上`DisableTelemetry`設置 標誌。

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

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何跟蹤未自動收集的遙測數據?

使用建構函數注入`TelemetryClient`獲取的實例,並在其上調用所需`TrackXXX()`的方法。 我們不建議創建新`TelemetryClient`實例。 的`TelemetryClient`單個實例`DependencyInjection`已在 容器中註冊,該容器與其餘遙`TelemetryConfiguration`測數據 共用。 僅當新`TelemetryClient`實例需要獨立於遙測的其餘部分的配置時,才建議創建新實例。

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>我能否使用 Visual Studio IDE 將應用程式見解添加到輔助服務專案中?

視覺工作室 IDE 載入目前僅支援ASP.NET/ASP.NET核心應用程式。 當 Visual Studio 提供對載入輔助服務應用程式的支援時,本文檔將更新。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>我能否使用狀態監視器等工具啟用應用程式見解監控?

否。 [狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)和[狀態監視器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)目前僅支援 ASP.NET 4.x。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果我在 Linux 中運行應用程式,是否支援所有功能?

是。 除以下情況外,此 SDK 的功能支援在所有平臺中都是一樣的:

* 性能計數器僅在 Windows 中受支援,但即時指標中顯示的進程 CPU/記憶體除外。
* 即使`ServerTelemetryChannel`默認情況下啟用,如果應用程式在 Linux 或 MacOS 中運行,通道不會自動創建本地儲存資料夾,以臨時保留遙測(如果存在網路問題)。 由於此限制,當存在臨時網路或伺服器問題時,遙測將丟失。 要解決此問題,請為通道設定本地資料夾:

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

[.NET 核心主控台應用程式](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)如果您使用的是以 .NET 核心 (2.0 或更高) 或 .NET 架構 (4.7.2 或更高) 撰寫的主控台應用程式,請使用此範例

[ASP .NET 具有託管服務的後臺任務](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)如果您使用的是 Asp.Net Core 2.1/2.2,則使用此範例,並在此處根據[官方指南創建](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)後臺任務

[.NET 核心 3.0 員工服務](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)如果您根據此處的官方指南使用 .NET Core 3.0 工作人員服務應用程式[,請使用此範例](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>開放原始碼 SDK

[閱讀並貢獻代碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)。

## <a name="next-steps"></a>後續步驟

* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)發送您自己的事件和指標,以便詳細查看應用的性能和使用方式。
* [追蹤未自動追蹤的其他相依項](../../azure-monitor/app/auto-collect-dependencies.md)。
* [豐富或篩選自動收集的遙測](../../azure-monitor/app/api-filtering-sampling.md)數據。
* [ASP.NET核心中的相依注射](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。
