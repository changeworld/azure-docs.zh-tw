---
title: 使用 ILogger 流覽 .NET 跟蹤日誌 - Azure 應用程式見解
description: 將 Azure 應用程式見解 ILogger 提供程式與ASP.NET核心和主控台應用程式一起使用的示例。
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f40c1c1a8ee7f20c769a62e9746da43face4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276371"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>應用程式見解記錄器提供程式 .NET 核心 ILogger 日誌

ASP.NET Core 支援與不同類型的內置和協力廠商日誌記錄提供程式配合的日誌記錄 API。 日誌記錄是通過在*ILogger*實例上調用**Log（）** 或其變體來完成的。 本文演示如何使用*應用程式InsightsLoggerProvider*在主控台和ASP.NET核心應用程式中捕獲 ILogger 日誌。 本文還介紹了應用程式洞察記錄器提供程式如何與其他應用程式見解遙測集成。
若要深入了解，請參閱 [ASP.NET Core 中的記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="aspnet-core-applications"></a>ASP.NET核心應用

預設情況下，在 Microsoft 中啟用應用程式[InsightsLoggerProvider.應用程式Insights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.1（及更高版本），當您通過以下任一方法打開常規應用程式見解監視時：

- 通過在 IWebHostBuilder 上調用**使用應用程式見解**擴充方法（現已過時）
- 通過在 IServiceCollection 上調用**添加應用程式見解遙測**擴充方法

應用程式 InsightsLoggerProvider 捕獲的 ILogger 日誌受與收集的任何其他遙測相同的配置的約束。 它們具有相同的遙測初始化器和遙測處理器集，使用相同的遙測通道，並且以與其他遙測相同的方式關聯和採樣。 如果您使用版本 2.7.1 或更高版本，則無需執行任何操作即可捕獲 ILogger 日誌。

預設情況下，只有*警告*或更高的 ILogger 日誌（來自所有[類別](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)）才會發送到應用程式見解。 但是，您可以[應用篩選器來修改此行為](#control-logging-level)。 需要執行其他步驟從**Program.cs**或**Startup.cs**捕獲 ILogger 日誌。 （請參閱[從ASP.NET核心應用程式中捕獲Startup.cs和Program.cs的 ILogger 日誌](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)。

如果您使用早期版本的 Microsoft.應用程式 Insights.AspNet SDK，或者您只想使用應用程式 InsightsLoggerProvider，而無需監視任何其他應用程式見解，請使用以下過程：

1. 安裝 NuGet 包：

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. 修改**Program.cs**如下所示：

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

步驟 2 中的代碼配置`ApplicationInsightsLoggerProvider`。 以下代碼顯示了用於發送日誌`ILogger`的示例控制器類。 日誌由應用程式見解捕獲。

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>在ASP.NET核心應用中從Startup.cs和Program.cs捕獲 ILogger 日誌

> [!NOTE]
> 在ASP.NET核心 3.0 及更高版本中，無法再注入`ILogger`Startup.cs和Program.cs。 如需更多詳細資料，請參閱 https://github.com/aspnet/Announcements/issues/353 \(英文\)。

新的應用程式InsightsLogger提供程式可以從應用程式啟動管道的早期捕獲日誌。 儘管在應用程式見解（從版本 2.7.1 開始）中自動啟用了應用程式 InsightsLoggerProvider，但它直到稍後在管道中才設置檢測金鑰。 因此，將僅捕獲**來自控制器**/其他類的日誌。 要捕獲從**Program.cs**開始並**Startup.cs**本身的每個日誌，必須顯式為應用程式 InsightsLoggerProvider 啟用檢測金鑰。 此外，遙測*配置*在從**Program.cs**或**Startup.cs**本身進行日誌時未完全設置。 因此，這些日誌將具有使用 InMemoryChannel 的最低配置，沒有採樣，也沒有標準遙測初始化器或處理器。

以下**示例演示了**此功能，Program.cs和**Startup.cs**。

#### <a name="example-programcs"></a>示例Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>示例Startup.cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>從舊的應用程式見解記錄器提供程式遷移

Microsoft.應用程式見解.AspNet SDK 版本在 2.7.1 之前支援一個現已過時的日誌記錄提供程式。 此提供程式是通過 ILoggerFactory 的**添加應用程式見解（）** 擴充方法啟用的。 我們建議您遷移到新提供程式，這涉及兩個步驟：

1. 從**啟動.配置（）** 方法中刪除*ILoggerFactory.AddApplicationInsights（）* 調用，以避免雙重日誌記錄。
2. 重新應用代碼中的任何篩選規則，因為新提供程式不會遵守這些規則。 *ILoggerFactory.AddApplicationInsights（）* 的重載具有最小的日誌級別或篩選器功能。 使用新提供程式時，篩選是日誌記錄框架本身的一部分。 它不是由應用程式見解提供程式完成的。 因此，任何通過*ILoggerFactory.Add應用程式Insights（）* 超載提供的篩檢程式都應該被刪除。 篩選規則應遵循[控制項日誌記錄級別](#control-logging-level)指令來提供。 如果使用*appsettings.json*來篩選日誌記錄，它將繼續與新提供程式一起工作，因為兩者都使用相同的提供程式別名 *"應用程式見解*"。

您仍可以使用舊提供程式。 （僅在主要版本更改為 3 時將其刪除。*xx*.） 但我們建議您遷移到新提供程式的原因如下：

- 以前的提供程式不支援[日誌作用域](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)。 在新提供程式中，作用域中的屬性將自動添加為自訂屬性到收集的遙測資料。
- 日誌現在可以在應用程式啟動管道中更早地捕獲。 現在可以捕獲**程式和****啟動**類中的日誌。
- 使用新提供程式，篩選在框架級別本身完成。 您可以像篩選到應用程式見解提供程式一樣，將日誌篩選到應用程式見解提供程式，包括內置提供程式（如主控台、調試等）。 您還可以將相同的篩選器應用於多個提供程式。
- 在ASP.NET酷（2.0 及更高版本）中，[啟用日誌記錄提供程式](https://github.com/aspnet/Announcements/issues/255)的建議方法是在 i、B，Program.cs本身使用**Program.cs**擴充方法。

> [!Note]
> 新提供程式可用於面向 NETSTANDARD2.0 或更高版本的應用程式。 如果應用程式的目標是較舊的 .NET Core 版本（如 .NET Core 1.1，或者它針對 .NET 框架），請繼續使用舊提供程式。

## <a name="console-application"></a>主控台應用程式

> [!NOTE]
> 有一個新的應用程式見解 SDK 稱為[Microsoft.應用程式見解.WorkerService，](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)可用於為任何主控台應用程式啟用應用程式見解（ILogger 和其他應用程式見解遙測）。 建議[在此處](../../azure-monitor/app/worker-service.md)使用此包和相關說明。

以下代碼顯示了一個示例主控台應用程式，該應用程式佈建為將 ILogger 跟蹤發送到應用程式見解。

安裝的封裝：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

此示例使用獨立包`Microsoft.Extensions.Logging.ApplicationInsights`。 預設情況下，此配置使用"最小"遙測配置將資料發送到應用程式見解。 最低為表示 InMemoryChannel 是所使用的頻道。 沒有採樣，也沒有標準的遙測初始化器。 對於主控台應用程式，可以重寫此行為，如下例所示。

安裝此額外封裝：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

以下部分演示如何使用服務覆蓋預設遙測配置 **。配置\<遙測配置>（）** 方法。 此示例設置`ServerTelemetryChannel`和採樣。 它將自訂 I遙測初始化器添加到遙測配置中。

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>控制日誌記錄級別

ASP.NET核心*ILogger*紅外線有一個內置的機制來應用[日誌篩選](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)。 這允許您控制發送到每個註冊提供程式（包括應用程式見解提供程式）的日誌。 篩選可以在配置中完成（通常使用*appsettings.json*檔）或代碼。 此功能由框架本身提供。 它不是特定于應用程式見解提供程式。

以下示例將篩選器規則應用於應用程式見解記錄器提供程式。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>使用應用設置在配置中創建篩選器規則。

對於應用程式見解記錄器提供程式，提供程式別名為`ApplicationInsights`。 *appsettings.json*的以下部分指示日誌記錄提供程式通常以級別*警告*和以上日誌。 然後，`ApplicationInsightsLoggerProvider`它重寫 要記錄以"Microsoft"開頭的類別級別 *"錯誤*"和"以上"。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>在代碼中創建篩選器規則

以下程式碼片段從所有類別配置*警告*日誌和上述日誌，從以"Microsoft"開頭的類別中配置*Error*及上述日誌`ApplicationInsightsLoggerProvider`。 此配置與*appsettings.json*中的上一部分相同。

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>應用程式洞察記錄器提供程式的舊版本和新版本是什麼？

[Microsoft.應用程式見解.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)包括一個內置的應用程式見解記錄器提供程式（Microsoft.應用程式見解.AspNetCore.日誌記錄.應用程式見解記錄器提供程式），該提供程式是通過**IloggerFactory**擴充方法啟用的。 此提供程式從版本 2.7.1 標記為過時。 它將在下一個主要版本更改中完全刪除。 [微軟.應用程式見解.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)套裝軟體本身並沒有過時。 它是必需的，以啟用監視請求、依賴項等。

建議的替代方案是新的獨立套裝軟體[微軟.擴展.日誌記錄.應用程式見解](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)，其中包含一個改進的應用程式見解記錄器提供程式（微軟.擴展.記錄.應用程式見解.應用程式見解.應用程式見解）和擴充方法在ILoggerBuilder啟用它。

[Microsoft.應用程式見解.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.1 依賴于新包，並自動啟用 ILogger 捕獲。

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>為什麼某些 ILogger 日誌在應用程式見解中顯示兩次？

如果您通過調用`AddApplicationInsights`啟用了應用程式 InsightsLoggerProvider 的舊版本（現已過時），則可能發生重複`ILoggerFactory`。 檢查**配置**方法是否具有以下內容，然後將其刪除：

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

如果在從 Visual Studio 調試時遇到雙重日誌記錄，`EnableDebugLogger`請設置為*false*在啟用應用程式見解的代碼中，如下所示。 此複製和修復僅在調試應用程式時才相關。

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>我更新到[微軟.應用程式見解.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.1，並且從 ILogger 的日誌會自動捕獲。 如何完全關閉此功能？

請參閱[控制項日誌記錄級別](../../azure-monitor/app/ilogger.md#control-logging-level)部分，瞭解如何篩選日誌。 要關閉應用程式見解記錄器提供程式，請使用`LogLevel.None`：

**在程式碼中：**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**在配置中：**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>為什麼某些 ILogger 日誌與其他日誌的屬性不同？

應用程式見解使用用於所有其他遙測的相同遙測配置捕獲和發送 ILogger 日誌。 但有一個例外。 預設情況下，遙測配置在從**Program.cs**或**Startup.cs**登錄時未完全設置。 來自這些位置的日誌將沒有預設配置，因此它們不會運行所有遙測初始化器和遙測處理器。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>我使用獨立包 Microsoft.擴展.日誌記錄.應用程式見解，我想手動記錄一些額外的自訂遙測資料。 我該怎麼做？

使用獨立包時，`TelemetryClient`不會注入 DI 容器，因此您需要創建 新的`TelemetryClient`實例，並使用與記錄器提供程式相同的配置，如下代碼所示。 這可確保相同的配置用於所有自訂遙測以及 ILogger 的遙測資料。

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> 如果您使用 Microsoft.ApplicationInsights.AspNetCore 包來啟用應用程式見解，請修改此代碼以`TelemetryClient`直接進入建構函式。 例如，請參閱[此常見問題解答](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)。


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>從 ILogger 日誌生成什麼應用程式見解遙測類型？ 或者，我在哪裡可以看到應用程式見解中的 ILogger 日誌？

應用程式見解記錄器提供程式捕獲 ILogger 日誌並從中創建跟蹤遙測。 如果將異常物件傳遞到 ILogger 上的**Log（）** 方法，則創建*異常遙測*而不是跟蹤遙測。 這些遙測專案可以在與應用程式見解的任何其他跟蹤遙測或異常遙測相同的位置找到，包括門戶、分析或 Visual Studio 本地調試器。

如果您希望始終發送跟蹤遙測，請使用此程式碼片段：```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>我沒有安裝 SDK，並且使用 Azure Web 應用擴展功能為ASP.NET核心應用程式啟用應用程式見解。 如何使用新提供程式？ 

Azure Web 應用中的應用程式見解擴展使用新提供程式。 您可以修改應用程式*應用設置.json*檔中的篩選規則。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>我使用獨立包 Microsoft.擴展.日誌記錄.應用程式見解，並通過調用產生器啟用應用程式見解提供程式 **。添加應用程式見解（"ikey"）**。 是否有從配置中獲取檢測金鑰的選項？


修改Program.cs和應用程式設定.json，如下所示：

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   相關部分來自`appsettings.json`：

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

僅當使用獨立日誌記錄提供程式時，才需要此代碼。 對於常規應用程式見解監視，檢測金鑰從配置路徑*應用程式見解：檢測鍵*自動載入。 應用設置.json 應該如下所示：

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>後續步驟

深入了解：

* [ASP.NET Core 中的記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [應用程式見解中的 .NET 跟蹤日誌](../../azure-monitor/app/asp-net-trace-logs.md)
