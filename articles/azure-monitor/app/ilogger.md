---
title: 使用 ILogger-Azure 應用程式 Insights 探索 .NET 追蹤記錄
description: 使用 Azure 應用程式 Insights ILogger 提供者搭配 ASP.NET Core 和主控台應用程式的範例。
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 987d5b78c5fe680f43ff6a001e7a31a8ae9f6124
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931456"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider for Microsoft 副檔名記錄

本文示範如何使用 `ApplicationInsightsLoggerProvider` ， `ILogger` 在主控台中捕獲記錄並 ASP.NET Core 應用程式。
若要深入瞭解記錄，請參閱 [ASP.NET Core 中的登入](/aspnet/core/fundamentals/logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 應用程式

`ApplicationInsightsLoggerProvider` 當使用程式 [代碼](./asp-net-core.md) 或無程式 [代碼的](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) 方法設定 ApplicationInsights 時，預設會為 ASP.NET Core 應用程式啟用。

依預設，只有 *警告* 和上面 `ILogger` 的記錄 (從所有 [類別](/aspnet/core/fundamentals/logging/#log-category)) 傳送至 Application Insights。 但是您可以 [自訂此行為](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection)。 需要額外的步驟才能從 **Program.cs** 或 **Startup.cs**中捕獲 ILogger 記錄。  (參閱 [ASP.NET Core 應用程式中從 Startup.cs 和 Program.cs 取得 ILogger 記錄](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)。 ) 

如果您只想要在 `ApplicationInsightsLoggerProvider` 沒有任何其他 Application Insights 監視的情況下使用，請使用下列步驟。

1. 安裝 NuGet 套件：

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. 如下 `Program.cs` 所示修改：

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
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

步驟2中的程式碼會進行設定 `ApplicationInsightsLoggerProvider` 。 下列程式碼顯示範例控制器類別，用 `ILogger` 來傳送記錄檔。 Application Insights 會捕獲記錄。

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
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>從 Startup.cs 和 Program.cs 的 ASP.NET Core apps 中捕獲 ILogger 記錄

> [!NOTE]
> 在 ASP.NET Core 3.0 和更新版本中，無法再插入 `ILogger` Startup.cs 和 Program.cs。 如需詳細資訊，請參閱 https://github.com/aspnet/Announcements/issues/353。

`ApplicationInsightsLoggerProvider` 可以在應用程式啟動時提早捕獲記錄。 雖然 ApplicationInsightsLoggerProvider 會在 Application Insights 中自動啟用 (從版本 2.7.1) 開始，在稍後的管線中，它並不會設定檢測金鑰。 因此，只會捕捉來自 **控制器**/其他類別的記錄。 若要從 **Program.cs** 和 **Startup.cs** 本身開始捕捉每個記錄檔，您必須明確啟用 ApplicationInsightsLoggerProvider 的檢測金鑰。 此外，當您從**Program.cs**或**Startup.cs**本身進行記錄時， *TelemetryConfiguration*不會完全設定。 因此，這些記錄將會有最少的設定，可使用 [InMemoryChannel](./telemetry-channels.md)、無 [取樣](./sampling.md)，以及無標準 [遙測初始化運算式或處理器](./api-filtering-sampling.md)。

下列範例會使用 **Program.cs** 和 **Startup.cs**來示範這項功能。

#### <a name="example-programcs"></a>範例 Program.cs

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

#### <a name="example-startupcs"></a>範例 Startup.cs

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

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>從舊的 ApplicationInsightsLoggerProvider 遷移

ApplicationInsights 在2.7.1 之前的版本支援現在已淘汰的記錄提供者。 此提供者是透過 ILoggerFactory 的 **AddApplicationInsights ( # B1 ** 擴充方法來啟用。 建議您遷移至新的提供者，其牽涉到兩個步驟：

1. 從**Startup.Configu)  ( # B3**方法移除*ILoggerFactory. AddApplicationInsights ( # B1*呼叫，以避免重複記錄。
2. 在程式碼中重新套用任何篩選規則，因為新的提供者不會遵守這些規則。 *ILoggerFactory. AddApplicationInsights ( # B1*的多載採用了最少的 LogLevel 或濾波器函數。 使用新的提供者時，篩選是記錄架構本身的一部分。 Application Insights 提供者不會這麼做。 因此，您應該移除透過 *ILoggerFactory. AddApplicationInsights ( # B1 * 多載所提供的任何篩選準則。 您應遵循 [控制項記錄層級](#control-logging-level) 的指示來提供和篩選規則。 如果您使用 *appsettings.json* 來篩選記錄，它會繼續使用新的提供者，因為兩者都使用相同的提供者別名 *ApplicationInsights*。

您仍然可以使用舊的提供者。  (只會在主要版本變更為3時移除。*xx*. ) 但基於下列原因，我們建議您遷移至新的提供者：

- 先前的提供者缺少 [記錄範圍](/aspnet/core/fundamentals/logging#log-scopes)的支援。 在新的提供者中，範圍中的屬性會自動新增為所收集遙測的自訂屬性。
- 現在您可以在應用程式啟動管線中更早地捕捉記錄。 現在可捕獲 **程式** 和 **啟動** 類別的記錄。
- 有了新的提供者，就可以在架構層級本身進行篩選。 您可以用與其他提供者相同的方式，將記錄篩選至 Application Insights 提供者，包括主控台、偵錯工具等內建提供者。 您也可以將相同的篩選器套用至多個提供者。
- 在 ASP.NET Core (2.0 和更新版本的) 中，  [啟用記錄提供者](https://github.com/aspnet/Announcements/issues/255) 的建議方式是在 **Program.cs** 本身的 ILoggingBuilder 上使用擴充方法。

> [!Note]
> 新的提供者適用于以 NETSTANDARD 2.0 或更新版本為目標的應用程式。 從 [ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 和更新版本2.14.0 起，新的提供者也適用于以 .NET Framework NET461 或更新版本為目標的應用程式。 如果您的應用程式是以較舊的 .NET Core 版本（例如 .NET Core 1.1）為目標，或是以小於 NET46 的 .NET Framework 為目標，請繼續使用舊的提供者。

## <a name="console-application"></a>主控台應用程式

> [!NOTE]
> 有新的 Application Insights SDK 稱為 [ApplicationInsights WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) ，可用來啟用任何主控台應用程式的 Application Insights (ILogger 和其他 Application Insights 遙測) 。 建議使用此套件，以及[這裡](./worker-service.md)的相關指示。

如果您只想要使用 ApplicationInsightsLoggerProvider 而不使用任何其他 Application Insights 監視，請使用下列步驟。

安裝的封裝：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
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

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

此範例使用獨立套件 `Microsoft.Extensions.Logging.ApplicationInsights` 。 根據預設，此設定會使用「最小」的 TelemetryConfiguration 將資料傳送至 Application Insights。 最小值表示 InMemoryChannel 是使用的通道。 沒有任何取樣和標準 TelemetryInitializers。 您可以針對主控台應用程式覆寫此行為，如下列範例所示。

安裝此額外封裝：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

下一節將說明如何使用 **services.Configu) \<TelemetryConfiguration> ( # B1 ** 方法來覆寫預設的 TelemetryConfiguration。 此範例會設定 `ServerTelemetryChannel` 和取樣。 它會將自訂 ITelemetryInitializer 新增至 TelemetryConfiguration。

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
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>控制記錄層級

`ILogger` 具有可套用 [記錄篩選](/aspnet/core/fundamentals/logging#log-filtering)的內建機制。 這可讓您控制傳送給每個已註冊提供者的記錄，包括 Application Insights 提供者。 篩選可以在設定 (中進行，通常是在檔案) 或程式碼中使用 *appsettings.js* 。

下列範例示範如何將篩選規則套用至 `ApplicationInsightsLoggerProvider` 。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>在設定中建立具有 appsettings.js的篩選規則

若為 ApplicationInsightsLoggerProvider，提供者別名為 `ApplicationInsights` 。 下一節的 *appsettings.js* 指示記錄提供者通常會記錄在層級 *警告* 和更高的位置。 然後，它會覆寫 `ApplicationInsightsLoggerProvider` 以「Microsoft」層級 *錯誤* 和更新版本開頭的記錄類別。

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

### <a name="create-filter-rules-in-code"></a>在程式碼中建立篩選規則

下列程式碼片段會將所有類別的 *警告* 和更新版本，以及從開頭為 "Microsoft" 之類別的 *錯誤* 和更新版本，設定為要傳送至的記錄檔 `ApplicationInsightsLoggerProvider` 。 這項設定與上一節中 *appsettings.js*的相同。

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>記錄範圍

`ApplicationInsightsLoggingProvider` 支援 [記錄檔範圍](/aspnet/core/fundamentals/logging#log-scopes) 和範圍預設為啟用。

如果範圍是型別 `IReadOnlyCollection<KeyValuePair<string,object>>` ，則會將集合中的每個索引鍵/值組新增至 application insights 遙測做為自訂屬性。 在下列範例中，將會捕捉記錄， `TraceTelemetry` 並在屬性中 )  ( "MyKey"、"MyValue"。

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

如果任何其他類型作為範圍使用，則會將它們儲存在 application insights 遙測中的「範圍」屬性下。 在下列範例中， `TraceTelemetry` 將會有一個稱為「範圍」的屬性，其中包含範圍。

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>新舊版本的 ApplicationInsightsLoggerProvider 有哪些？

[ApplicationInsights ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 包含內建的 ApplicationInsightsLoggerProvider (ApplicationInsights AspNetCore，這是透過 **ApplicationInsightsLoggerProvider** 擴充方法所啟用的) 。 此提供者已從版本2.7.1 標示為過時。 它將在下一個主要版本變更時完全移除。 [ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)封裝本身尚未淘汰。 您必須啟用對要求、相依性等的監視。

建議的替代方案是新的獨立套件 [ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)，其中包含改良的 ApplicationInsightsLoggerProvider (ApplicationInsights. ApplicationInsightsLoggerProvider) 和 ILoggerBuilder 上的擴充方法來啟用它。

[ApplicationInsights ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 會相依于新的封裝，並自動啟用 ILogger capture。

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>為什麼某些 ILogger 記錄會在 Application Insights 中顯示兩次？

如果您的舊版 (現在已淘汰) 版本的 ApplicationInsightsLoggerProvider，則會在上呼叫來啟用重複 `AddApplicationInsights` `ILoggerFactory` 。 檢查您的 **Configure** 方法是否具有下列各項，並將其移除：

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

如果您在從 Visual Studio 進行偵錯工具時遇到雙重記錄，請 `EnableDebugLogger` 在啟用 Application Insights 的程式碼中，將設為  *false* ，如下所示。 這項重複和修正僅適用于您正在對應用程式進行偵錯工具時。

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>我已更新為 [ApplicationInsights ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1，並且會自動捕獲來自 ILogger 的記錄。 如何? 完全關閉這項功能嗎？

請參閱 [控制項記錄層級](#control-logging-level) 一節，以瞭解如何篩選一般記錄。 若要關閉 ApplicationInsightsLoggerProvider，請使用 `LogLevel.None` ：

**在程式碼中：**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**在 config 中：**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>為什麼某些 ILogger 記錄與其他記錄沒有相同的屬性？

Application Insights 會使用用於其他所有遙測的相同 TelemetryConfiguration 來捕捉和傳送 ILogger 記錄。 但有一個例外狀況。 依預設，當您從 **Program.cs** 或 **Startup.cs**記錄時，TelemetryConfiguration 不會完整設定。 這些位置的記錄沒有預設設定，因此它們不會執行所有的 TelemetryInitializers 和 TelemetryProcessors。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>我使用的是獨立套件 ApplicationInsights，而我想要手動記錄一些額外的自訂遙測。 我該怎麼做？

當您使用獨立套件時， `TelemetryClient` 不會插入 DI 容器中，因此您必須建立的新實例， `TelemetryClient` 並使用與記錄器提供者所使用的相同設定，如下列程式碼所示。 這可確保所有自訂遙測以及來自 ILogger 的遙測都使用相同的設定。

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
> 如果您使用 ApplicationInsights AspNetCore 套件來啟用 Application Insights，請修改此程式碼以 `TelemetryClient` 直接在函式中取得。 如需範例，請參閱 [此常見問題](./asp-net-core.md#frequently-asked-questions)。


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger 記錄所產生的 Application Insights 遙測類型為何？ 或者，我可以在 Application Insights 中看到 ILogger 記錄？

ApplicationInsightsLoggerProvider 會捕捉 ILogger 記錄，並從中建立 TraceTelemetry。 如果將例外狀況物件傳遞至 `Log` 方法 `ILogger` ，則會建立 *ExceptionTelemetry* 而不是 TraceTelemetry。 這些遙測專案可以在與任何其他 TraceTelemetry 或 ExceptionTelemetry 相同的位置中找到 Application Insights，包括入口網站、分析或 Visual Studio 本機偵錯工具。

如果您想要一律傳送 TraceTelemetry，請使用此程式碼片段： ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>我未安裝 SDK，並使用 Azure Web Apps 擴充功能來啟用 ASP.NET Core 應用程式的 Application Insights。 如何? 使用新的提供者？ 

Azure Web Apps 中的 Application Insights 延伸模組會使用新的提供者。 您可以在應用程式的檔案 *appsettings.js* 中修改篩選規則。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>我使用獨立套件 ApplicationInsights，並藉由呼叫 builder 來啟用 Application Insights 提供者。 **AddApplicationInsights ( "ikey" ) **。 是否有從設定取得檢測金鑰的選項？


修改 Program.cs 和 appsettings.js，如下所示：

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

   相關區段來源 `appsettings.json` ：

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

只有當您使用獨立的記錄提供者時，才需要此程式碼。 針對定期 Application Insights 監視，會自動從設定路徑 *ApplicationInsights： Instrumentationkey*載入檢測金鑰。 Appsettings.js應如下所示：

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

* [ASP.NET Core 中的記錄](/aspnet/core/fundamentals/logging)
* [Application Insights 中的 .NET 追蹤記錄](./asp-net-trace-logs.md)

