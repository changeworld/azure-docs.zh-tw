---
title: 使用 ILogger Azure 應用程式 Insights 探索 .NET 追蹤記錄
description: 搭配 ASP.NET Core 和主控台應用程式使用 Azure 應用程式 Insights ILogger 提供者的範例。
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 33effe9cfec6d766d573617ff03b58564e5b34d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81313664"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>適用于 .NET Core ILogger 記錄的 ApplicationInsightsLoggerProvider

ASP.NET Core 支援記錄 API，其適用于不同類型的內建和協力廠商記錄提供者。 記錄是透過在*ILogger*實例上呼叫**Log （）** 或它的 variant 來完成。 本文示範如何使用*ApplicationInsightsLoggerProvider* ，在主控台和 ASP.NET Core 應用程式中捕獲 ILogger 記錄。 本文也說明 ApplicationInsightsLoggerProvider 如何與其他 Application Insights 遙測整合。
若要深入了解，請參閱 [ASP.NET Core 中的記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 應用程式

當您透過下列其中一種方法開啟一般 Application Insights 監視時，會依預設在[ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.1 （和更新版本）中啟用 ApplicationInsightsLoggerProvider：

- 藉由在 IWebHostBuilder 上呼叫 **.useapplicationinsights**擴充方法（現已過時）
- 藉由在 IServiceCollection 上呼叫**對於 addapplicationinsightstelemetry**擴充方法

ApplicationInsightsLoggerProvider 捕捉的 ILogger 記錄會受到所收集之任何其他遙測的相同設定。 它們具有一組相同的 TelemetryInitializers 和 TelemetryProcessors、使用相同的 TelemetryChannel，而且相互關聯和取樣的方式與其他遙測相同。 如果您使用2.7.1 或更新版本，則不需要採取任何動作來捕捉 ILogger 記錄。

根據預設，只有*警告*或更高的 ILogger 記錄（來自所有[類別](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)）才會傳送至 Application Insights。 但是，您可以套用[篩選來修改此行為](#control-logging-level)。 需要額外的步驟，才能從**Program.cs**或**Startup.cs**捕獲 ILogger 記錄。 （請參閱[在 ASP.NET Core 應用程式中從 Startup.cs 和 Program.cs 捕獲 ILogger 記錄](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)）。

如果您使用較早版本的 ApplicationInsights，或只想要使用 ApplicationInsightsLoggerProvider 而不需要任何其他 Application Insights 監視，請使用下列程式：

1. 安裝 NuGet 套件：

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. 修改**Program.cs** ，如下所示：

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

步驟2中的程式碼`ApplicationInsightsLoggerProvider`會設定。 下列程式碼顯示範例控制器類別，它會使用`ILogger`來傳送記錄。 記錄檔是由 Application Insights 所捕捉。

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>在 ASP.NET Core 應用程式中從 Startup.cs 和 Program.cs 捕獲 ILogger 記錄

> [!NOTE]
> 在 ASP.NET Core 3.0 和更新版本中，不能再插入`ILogger` Startup.cs 和 Program.cs。 如需更多詳細資料，請參閱 https://github.com/aspnet/Announcements/issues/353 \(英文\)。

新的 ApplicationInsightsLoggerProvider 可以提早在應用程式啟動管線中捕捉記錄。 雖然 ApplicationInsightsLoggerProvider 會在 Application Insights 中自動啟用（從版本2.7.1 開始），但在管線中稍後的時間內不會設定檢測金鑰。 因此，只會捕捉來自**控制器**/其他類別的記錄。 若要從**Program.cs**和**Startup.cs**本身開始捕捉每個記錄檔，您必須明確啟用 ApplicationInsightsLoggerProvider 的檢測金鑰。 此外，當您從**Program.cs**或**Startup.cs**本身記錄時， *TelemetryConfiguration*並未完整設定。 因此，這些記錄會有使用 InMemoryChannel、沒有取樣，以及沒有標準遙測初始化運算式或處理器的最低設定。

下列範例會使用**Program.cs**和**Startup.cs**來示範這項功能。

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>從舊的 ApplicationInsightsLoggerProvider 遷移

2.7.1 之前的 ApplicationInsights 版本，支援現在已過時的記錄提供者。 此提供者是透過 ILoggerFactory 的**AddApplicationInsights （）** 擴充方法來啟用。 我們建議您遷移至新的提供者，這牽涉到兩個步驟：

1. 從**Startup. Configure （）** 方法中移除*ILoggerFactory. AddApplicationInsights （）* 呼叫，以避免進行雙重記錄。
2. 重新套用程式碼中的任何篩選規則，因為新的提供者不會遵守它們。 *ILoggerFactory. AddApplicationInsights （）* 的多載會採用最少的 LogLevel 或 filter 函數。 有了新的提供者，篩選就是記錄架構本身的一部分。 Application Insights 提供者不會這麼做。 因此，您應該移除透過*ILoggerFactory AddApplicationInsights （）* 多載所提供的任何篩選。 您應遵循[控制項記錄層級](#control-logging-level)指示來提供和篩選規則。 如果您使用*appsettings*來篩選記錄，它會繼續使用新的提供者，因為兩者都使用相同的提供者別名*ApplicationInsights*。

您仍然可以使用舊的提供者。 （只有在主要版本變更為3時，才會將它移除。*xx*）但基於下列原因，建議您遷移至新的提供者：

- 先前的提供者缺少[記錄範圍](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)的支援。 在新的提供者中，範圍的屬性會自動新增為所收集遙測的自訂屬性。
- 現在可以在應用程式啟動管線中更早地捕捉記錄。 現在可以捕捉來自**程式**和**啟動**類別的記錄。
- 有了新的提供者，篩選就會在架構層級本身完成。 您可以用與其他提供者相同的方式，將記錄篩選 Application Insights 提供者，包括主控台、Debug 之類的內建提供者。 您也可以將相同的篩選準則套用至多個提供者。
- 在 ASP.NET Core （2.0 和更新版本）中，[啟用記錄提供者](https://github.com/aspnet/Announcements/issues/255)的建議方式是在**Program.cs**本身的 ILoggingBuilder 上使用擴充方法。

> [!Note]
> 新的提供者適用于以 NETSTANDARD 2.0 或更新版本為目標的應用程式。 從[ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)的2.14.0 版本開始，新的提供者也適用于以 .NET Framework NET461 或更新版本為目標的應用程式。 如果您的應用程式是以較舊的 .NET Core 版本（例如 .NET Core 1.1）為目標，或是以小於 NET46 的 .NET Framework 為目標，請繼續使用舊的提供者。

## <a name="console-application"></a>主控台應用程式

> [!NOTE]
> 有一個新的 Application Insights SDK，稱為[ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) ，可用來為任何主控台應用程式啟用 Application Insights （ILogger 和其他 Application Insights 遙測）。 建議您在[這裡](../../azure-monitor/app/worker-service.md)使用此封裝和相關聯的指示。

下列程式碼顯示的範例主控台應用程式，已設定為將 ILogger 追蹤傳送至 Application Insights。

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

這個範例會使用獨立封裝`Microsoft.Extensions.Logging.ApplicationInsights`。 根據預設，此設定會使用「最少」 TelemetryConfiguration，將資料傳送至 Application Insights。 「最小值」表示 InMemoryChannel 是所使用的通道。 沒有取樣，也沒有標準 TelemetryInitializers。 此行為可以針對主控台應用程式覆寫，如下列範例所示。

安裝此額外封裝：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

下一節說明如何使用服務來覆寫預設的 TelemetryConfiguration **。設定\<TelemetryConfiguration> （）** 方法。 這個範例會設定`ServerTelemetryChannel`和取樣。 它會將自訂 ITelemetryInitializer 新增至 TelemetryConfiguration。

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

## <a name="control-logging-level"></a>控制記錄層級

*ILogger*基礎的 ASP.NET Core 具有內建機制，可套用[記錄篩選](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)。 這可讓您控制傳送給每個已註冊提供者的記錄，包括 Application Insights 提供者。 篩選可以在設定中完成（通常是使用*appsettings*檔案）或在程式碼中進行。 這項功能是由架構本身所提供。 這不是 Application Insights 提供者特有的。

下列範例會將篩選規則套用至 ApplicationInsightsLoggerProvider。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>使用 appsettings 在設定中建立篩選規則

若為 ApplicationInsightsLoggerProvider，提供者別名`ApplicationInsights`為。 *Appsettings*的下列區段會指示記錄提供者，通常會在層級*警告*和更新版本中記錄。 然後，它會`ApplicationInsightsLoggerProvider`覆寫在層級*錯誤*和更新版本中，以 "Microsoft" 開頭的記錄類別。

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

下列程式碼片段會從所有類別設定*警告*和更新版本的記錄檔，並從開頭為 "Microsoft" 的類別中，針對要傳送`ApplicationInsightsLoggerProvider`至的分類設定*錯誤*和更新版本。 這項設定與上一節的*appsettings*相同。

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>新舊版本的 ApplicationInsightsLoggerProvider 有哪些？

[ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)包含內建的 ApplicationInsightsLoggerProvider （ApplicationInsights），這是透過**ApplicationInsightsLoggerProvider**擴充方法所啟用的功能所提供。 此提供者已從版本2.7.1 標記為過時。 它將在下一個主要版本變更中完全移除。 [ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)套件本身並不會過時。 您必須啟用對要求、相依性等的監視。

建議的替代方案是[ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)的新獨立套件，其中包含改良的 ApplicationInsightsLoggerProvider （ApplicationInsights ApplicationInsightsLoggerProvider）和 ILoggerBuilder 上的擴充方法來啟用它。

[ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.1 會相依于新的封裝，並自動啟用 ILogger capture。

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>為什麼某些 ILogger 記錄會在 Application Insights 中顯示兩次？

如果您已藉由在上`AddApplicationInsights` `ILoggerFactory`呼叫來啟用 ApplicationInsightsLoggerProvider 的舊版（現在已過時）版本，則可能會發生重複的情況。 檢查您的**Configure**方法是否具有下列內容，並將其移除：

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

如果您在從 Visual Studio 進行調試時遇到雙重記錄， `EnableDebugLogger`請在啟用 Application Insights 的程式碼中將設定為*false* ，如下所示。 只有在您要進行應用程式的調試時，才會有這個重複和修正的關聯。

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>我已更新為[ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本2.7.1，並自動捕捉來自 ILogger 的記錄。 如何? 完全關閉此功能嗎？

請參閱[控制記錄層級](../../azure-monitor/app/ilogger.md#control-logging-level)一節，以瞭解如何在一般情況中篩選記錄。 若要關閉 ApplicationInsightsLoggerProvider，請使用`LogLevel.None`：

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

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>為什麼有些 ILogger 記錄的屬性與其他不同？

Application Insights 會使用用於每個其他遙測的相同 TelemetryConfiguration 來捕捉和傳送 ILogger 記錄。 但有一個例外。 根據預設，當您從**Program.cs**或**Startup.cs**記錄時，TelemetryConfiguration 並未完整設定。 這些位置的記錄不會有預設設定，因此不會執行所有的 TelemetryInitializers 和 TelemetryProcessors。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>我使用獨立套件 ApplicationInsights，而我想要手動記錄一些額外的自訂遙測。 該怎麼做？

當您使用獨立封裝時， `TelemetryClient`不會插入 DI 容器，因此您需要建立的新實例， `TelemetryClient`並使用與記錄器提供者所使用的相同設定，如下列程式碼所示。 這可確保相同的設定會用於所有自訂遙測和來自 ILogger 的遙測。

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
> 如果您使用 ApplicationInsights AspNetCore 套件來啟用 Application Insights，請修改此程式碼，直接在此`TelemetryClient`函式中取得。 如需範例，請參閱[此常見問題](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)。


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger 記錄會產生哪些 Application Insights 遙測類型？ 或者，我可以在 Application Insights 中看到 ILogger 記錄的位置？

ApplicationInsightsLoggerProvider 會捕獲 ILogger 記錄，並從中建立 TraceTelemetry。 如果將例外狀況物件傳遞至 ILogger 上的**Log （）** 方法，則會建立*ExceptionTelemetry* ，而不是 TraceTelemetry。 這些遙測專案可以在 Application Insights 的任何其他 TraceTelemetry 或 ExceptionTelemetry 的相同位置找到，包括入口網站、分析，或 Visual Studio 本機偵錯工具。

如果您想要一律傳送 TraceTelemetry，請使用此程式碼片段：```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>我未安裝 SDK，而且我使用 Azure Web Apps 擴充功能來為 ASP.NET Core 的應用程式啟用 Application Insights。 如何? 使用新的提供者嗎？ 

Azure Web Apps 中的 Application Insights 延伸模組會使用新的提供者。 您可以在應用程式的*appsettings*中修改篩選規則。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>我使用獨立封裝 ApplicationInsights，並藉由呼叫 builder 來啟用 Application Insights 提供者 **。AddApplicationInsights （"ikey"）**。 是否有從設定取得檢測金鑰的選項？


修改 Program.cs 和 appsettings，如下所示：

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

   相關章節來源`appsettings.json`：

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

只有當您使用獨立記錄提供者時，才需要此程式碼。 針對一般 Application Insights 監視，會自動從設定路徑*ApplicationInsights： Instrumentationkey*載入檢測金鑰。 Appsettings 看起來應該像這樣：

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
* [Application Insights 中的 .NET 追蹤記錄](../../azure-monitor/app/asp-net-trace-logs.md)
