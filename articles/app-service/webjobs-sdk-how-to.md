---
title: 如何使用 Webjob SDK
description: 深入了解如何針對 WebJobs SDK 撰寫程式碼。 建立事件驅動的背景處理作業，以存取 Azure 和協力廠商服務中的資料。
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: a046791b8c50577c1921764b06bac5d88780194d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734989"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>如何使用 Azure WebJobs SDK 進行事件驅動幕後處理

本文提供如何使用 Azure WebJobs SDK 的指引。 若要立即開始使用 Webjob，請參閱[開始使用 AZURE WEBJOBS SDK 進行事件驅動的背景處理](webjobs-sdk-get-started.md)。 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

這是第3版之間的主要差異。*x*和第2版。*x*個 webjob SDK：

* 第3版。*x*加入 .net Core 的支援。
* 在第3版中。*x*，您需要明確安裝 webjob SDK 所需的儲存體系結延伸模組。 在第2版中。*x*，儲存體系結已包含在 SDK 中。
* 適用于 .NET Core 的 Visual Studio 工具（3.*x*）專案不同于 .NET Framework 的工具（2.*x*）專案。 若要深入瞭解，請參閱[使用 Visual Studio Azure App Service 開發和部署 webjob](webjobs-dotnet-deploy-vs.md)。

可能的話，這兩個版本3都有提供範例。*x*和第2版。*x*。

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md)是建置於 webjob SDK 上，本文提供一些主題的 Azure Functions 檔連結。 請注意函式與 Webjob SDK 之間的這些差異：
> * Azure Functions 版本2。*x*對應至 webjob SDK 第3版。*x*和 Azure Functions 1。*x*對應至 webjob SDK 2。*x*。 原始程式碼存放庫會使用 Webjob SDK 編號。
> * Azure Functions c # 類別庫的範例程式碼就像是 Webjob SDK 程式碼，但`FunctionName`您不需要在 webjob sdk 專案中使用屬性。
> * 某些系結類型僅在函式中受到支援，例如 HTTP （Webhook）和事件方格（以 HTTP 為基礎）。
>
> 如需詳細資訊，請參閱[比較 WebJobs SDK 和 Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs) (英文)。

## <a name="webjobs-host"></a>WebJobs 主機

此主機是函式的執行階段容器。  它會接聽觸發程序並呼叫函式。 在第3版中。*x*，主機是的執行`IHost`。 在第2版中。*x*，您可以使用`JobHost`物件。 您可以在程式碼中建立主機執行個體，並撰寫程式碼以自訂其行為。

這是直接使用 Webjob SDK，並透過 Azure Functions 間接使用的主要差異。 在 Azure Functions 中，服務會控制主機，而且您無法藉由撰寫程式碼來自訂主機。 Azure Functions 可讓您透過設定 host.json 檔案自訂主機行為。 這些設定是字串，而不是程式碼，這會限制您可以執行的自訂類型。

### <a name="host-connection-strings"></a>主機連接字串

當您在本機執行時，或在 Azure 中執行時，Webjob SDK 會尋找本機. settings. json 檔案中的 Azure 儲存體和 Azure 服務匯流排連接字串。 根據預設，需要名`AzureWebJobsStorage`為的儲存體連接字串設定。  

第2版。*x* SDK 可讓您針對這些連接字串使用您自己的名稱，或將其儲存在其他地方。 您可以使用[`JobHostConfiguration`]，在程式碼中設定名稱，如下所示：

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

因為第3版。*x*使用預設的 .net Core 設定 api，沒有 API 可變更連接字串名稱。

### <a name="host-development-settings"></a>主機開發設定

您可以在開發模式中執行主機，讓本機開發更有效率。 以下是當您在開發模式中執行時，所變更的一些設定：

| 屬性 | 開發設定 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` 將記錄輸出最大化。 |
| `Queues.MaxPollingInterval`  | 最低值可確保立即觸發佇列方法。  |
| `Singleton.ListenerLockPeriod` | 15 秒在快速反覆式開發法中有幫助。 |

啟用開發模式的程式取決於 SDK 版本。 

#### <a name="version-3x"></a>第3版。*x*

第3版。*x*使用標準 ASP.NET Core api。 在[`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)實例[`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment)上呼叫方法。 傳遞名為`development`的字串，如下列範例所示：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>第2版。*x*

`JobHostConfiguration` 類別具有可啟用開發模式的 `UseDevelopmentSettings` 方法。  下列範例示範如何使用開發設定。 若要`config.IsDevelopment` `true`在本機執行時進行傳回，請使用值`AzureWebJobsEnv` `Development`來設定名為的本機環境變數。

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>管理並行連接（第2版）。*x*）

在第3版中。*x*，連接限制預設為無限連接。 如果基於某些原因而需要變更此限制，您可以使用[`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler)類別的屬性。

在第2版中。*x*，您可以使用[ServicePointManager. servicepointmanager.defaultconnectionlimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API 來控制對主機的並行連線數目。 在2中。*x*，在啟動 webjob 主機之前，您應該從預設值2增加此值。

透過使用`HttpClient` flow 通過`ServicePointManager`，從函式進行的所有傳出 HTTP 要求。 當您到達中設定的值`DefaultConnectionLimit`之後`ServicePointManager` ，會先將要求排入佇列，然後再傳送它們。 假設您的 `DefaultConnectionLimit` 設為 2，且您的程式碼會發出 1,000 個 HTTP 要求。 一開始，只允許兩個要求傳至作業系統。 另一個998會排入佇列，直到有足夠的空間。 這表示您`HttpClient`可能會因為似乎已提出要求，但不是由作業系統將要求傳送到目的地伺服器。 因此，您可能會看到一個似乎不合理的行為：您的本機 `HttpClient` 花費 10 秒完成要求，但您的服務正以 200 毫秒的時間傳回每個要求。 

ASP.NET 應用程式的預設值是`Int32.MaxValue`，這可能適用于在基本或更高版本 App Service 方案中執行的 webjob。 Webjob 通常需要 Always On 設定，而且只有基本和較高的 App Service 方案才支援。

如果 WebJob 正在免費或共用的 App Service 方案中執行，則您的應用程式會受到 App Service 沙箱的限制，沙箱目前的[連線限制為 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)。 在中`ServicePointManager`使用未系結的連接限制時，可能會達到沙箱連接閾值，而且網站將會關閉。 在此狀況下，將 `DefaultConnectionLimit` 設為更低的值，如 50 或 100，可防止這種狀況發生，同時仍允許有足夠的輸送量。

此設定必須先設定，才能發出任何 HTTP 要求。 基於這個理由，Webjob 主機不應自動調整設定。 可能會有在主機啟動之前發生的 HTTP 要求，這可能會導致非預期的行為。 最佳方法是在初始化`Main` `JobHost`之前，立即在您的方法中設定值，如下所示：

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>觸發程序

函數必須是公用方法，而且必須有一個 trigger 屬性或[`NoAutomaticTrigger`](#manual-triggers)屬性。

### <a name="automatic-triggers"></a>自動觸發程式

自動觸發程序會呼叫函式以回應事件。 請考慮此函式範例，此函式是由新增至 Azure 佇列儲存體的訊息所觸發。 它會藉由從 Azure Blob 儲存體讀取 blob 來回應：

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger`屬性會告知執行時間在佇列中`myqueue-items`出現佇列訊息時呼叫函式。 `Blob`屬性會告知執行時間使用佇列訊息來讀取*範例*工作專案容器中的 blob。 `samples-workitems`容器中的 blob 專案名稱是直接從佇列觸發程式取得，做為系結運算式（`{queueTrigger}`）。

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>手動觸發程式

若要手動觸發函式，請`NoAutomaticTrigger`使用屬性，如下所示：

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

手動觸發函式的程式取決於 SDK 版本。

#### <a name="version-3x"></a>第3版。*x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>第2版。*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>輸入與輸出繫結

輸入繫結會提供宣告式方法，讓 Azure 或協力廠商服務中的資料可供您的程式碼使用。 輸出繫結會提供更新資料的方法。 [開始](webjobs-sdk-get-started.md)使用文章會顯示每個的範例。

您可以藉由將屬性套用至方法傳回值，來使用輸出系結的方法傳回值。 請參閱[使用 Azure 函數傳回值](../azure-functions/functions-bindings-return-value.md)中的範例。

## <a name="binding-types"></a>繫結型別

安裝和管理系結類型的程式，取決於您使用的是第3版。*x*或第2版。*x*的 SDK。 您可以在該系結類型之 Azure Functions[參考文章](#binding-reference-information)的「封裝」區段中，找到要針對特定系結類型安裝的封裝。 例外狀況是檔案觸發程式和系結（針對本機檔案系統），Azure Functions 不支援。

#### <a name="version-3x"></a>第3版。*x*

在第3版中。*x*，儲存體系結會包含在`Microsoft.Azure.WebJobs.Extensions.Storage`封裝中。 在`ConfigureWebJobs`方法`AddAzureStorage`中呼叫擴充方法，如下所示：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

若要使用其他觸發程序與繫結類型，請安裝包含這些項目的 NuGet 套件，並呼叫在擴充功能中實作的 `Add<binding>` 擴充方法。 例如，如果您想要使用 Azure Cosmos DB 系結，請安裝`Microsoft.Azure.WebJobs.Extensions.CosmosDB`並呼叫`AddCosmosDB`，如下所示：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

若要使用核心服務中包含的計時器觸發程序或檔案繫結，請分別呼叫 `AddTimers` 或 `AddFiles` 擴充方法。

#### <a name="version-2x"></a>第2版。*x*

這些觸發程式和系結類型包含在第2版中。*x*封裝的`Microsoft.Azure.WebJobs` x：

* Blob 儲存體
* 佇列儲存體
* 資料表儲存體

若要使用其他觸發程序與繫結型別，請安裝包含它們的 NuGet 封裝，並在 `JobHostConfiguration` 物件上呼叫 `Use<binding>` 方法。 例如，如果您想要使用計時器觸發程式，請在`Microsoft.Azure.WebJobs.Extensions` `Main`方法中`UseTimers`安裝並呼叫，如下所示：

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

若要使用檔案繫結，請安裝 `Microsoft.Azure.WebJobs.Extensions` 並呼叫 `UseFiles`。

### <a name="executioncontext"></a>ExecutionContext

Webjob 可讓您系結[`ExecutionContext`]至。 使用此系結，您可以存取[`ExecutionContext`]做為函式簽章中的參數。 例如，下列程式碼會使用內容物件存取引動過程識別碼，而您可以使用此識別碼讓給定的函式引動過程所產生的所有記錄相互關聯。  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

系結至的[`ExecutionContext`]進程取決於您的 SDK 版本。

#### <a name="version-3x"></a>第3版。*x*

在`ConfigureWebJobs`方法`AddExecutionContextBinding`中呼叫擴充方法，如下所示：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>第2版。*x*

先前所提到的 `Microsoft.Azure.WebJobs.Extensions` 封裝方法也提供特殊的繫結型別，您可以呼叫 `UseCore` 方法來註冊該繫結型別。 這個系結可讓您[`ExecutionContext`]在函式簽章中定義參數，此功能的啟用方式如下所示：

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>繫結設定

您可以設定某些觸發程式和系結的行為。 設定它們的程式取決於 SDK 版本。

* **第3版。*x*：** 在中`Add<Binding>` `ConfigureWebJobs`呼叫方法時設定設定。
* **第2版。*x*：** 藉由在您傳入的設定物件中設定屬性來進行設定`JobHost`。

這些系結特有的設定相當於 Azure Functions 中的[主機 json 專案](../azure-functions/functions-host-json.md)檔中的設定。

您可以設定下列系結：

* [Azure CosmosDB 觸發程式](#azure-cosmosdb-trigger-configuration-version-3x)
* [事件中樞觸發程序](#event-hubs-trigger-configuration-version-3x)
* [佇列儲存體觸發程序](#queue-storage-trigger-configuration)
* [SendGrid 系結](#sendgrid-binding-configuration-version-3x)
* [服務匯流排觸發程序](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB 觸發程式設定（第3版。*x*）

這個範例顯示如何設定 Azure Cosmos DB 觸發程式：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

如需詳細資訊，請參閱[Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)系結一文。

### <a name="event-hubs-trigger-configuration-version-3x"></a>事件中樞觸發程式設定（第3版。*x*）

這個範例顯示如何設定事件中樞觸發程式：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

如需詳細資訊，請參閱[事件中樞](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json)系結文章。

### <a name="queue-storage-trigger-configuration"></a>佇列儲存體觸發程式設定

這些範例示範如何設定佇列儲存體觸發程式：

#### <a name="version-3x"></a>第3版。*x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

如需詳細資訊，請參閱[佇列儲存體](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)系結文章。

#### <a name="version-2x"></a>第2版。*x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

如需詳細資訊，請參閱[主機. json v1. x 參考](../azure-functions/functions-host-json-v1.md#queues)。

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid 系結設定（第3版。*x*）

此範例說明如何設定 SendGrid 輸出系結：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

如需詳細資訊，請參閱[SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)系結一文。

### <a name="service-bus-trigger-configuration-version-3x"></a>服務匯流排觸發程式設定（第3版。*x*）

這個範例顯示如何設定服務匯流排觸發程式：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

如需詳細資訊，請參閱[服務匯流排](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)系結文章。

### <a name="configuration-for-other-bindings"></a>其他繫結的設定

有些觸發程式和系結類型會定義自己的自訂設定類型。 例如，檔案觸發程式可讓您指定要監視的根路徑，如下列範例所示：

#### <a name="version-3x"></a>第3版。*x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>第2版。*x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>繫結運算式

在屬性建構函式參數中，您可以使用運算式來解析各種來源的值。 例如，在下列程式碼中，`BlobTrigger` 屬性的路徑會建立名為 `filename`。 當用於輸出繫結時，`filename` 會解析觸發中二進位大型物件的名稱。

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

如需繫結運算式的詳細資訊，請參閱 Azure Functions 文件中的[繫結運算式和模式](../azure-functions/functions-bindings-expressions-patterns.md) (英文)。

### <a name="custom-binding-expressions"></a>自訂繫結運算式

有時候您會想要在程式碼中指定佇列名稱、blob 名稱或容器，或資料表名稱，而不是對它進行硬式編碼。 例如，您可能想要在設定檔或環境變數中指定 `QueueTrigger` 屬性的佇列名稱。

您可以藉由將中的`NameResolver`物件傳遞至`JobHostConfiguration`物件來執行此動作。 在觸發程序或繫結屬性建構函式中包含預留位置，然後 `NameResolver` 程式碼會提供實際要用以取代這些預留位置的值。 您可以用百分比（%）括住預留位置來加以識別簽署，如下所示：

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

此程式碼可讓您在測試環境中使用名為 `logqueuetest` 的佇列，以及在生產環境中使用名為 `logqueueprod` 的佇列。 您不必使用硬式編碼的佇列名稱，而是在 `appSettings` 集合中指定項目的名稱。

如果您未提供`NameResolver`自訂的預設值，則會有一個生效。 此預設值會從應用程式設定或環境變數取得值。

您`NameResolver`的類別會從`appSettings`取得佇列名稱，如下所示：

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>第3版。*x*

您可以使用相依性插入來設定解析程式。 這些範例需要下列 `using` 陳述式：

```cs
using Microsoft.Extensions.DependencyInjection;
```

您可以藉由在上[`ConfigureServices`] [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)呼叫擴充方法來新增解析程式，如下列範例所示：

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>第2版。*x*

將您`NameResolver`的類別傳遞至`JobHost`物件，如下所示：

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions 會實作 `INameResolver`，以從應用程式設定取得值，如下面範例所示。 當您直接使用 WebJobs SDK 時，您可以撰寫自訂實作，以從任何您偏好的來源取得預留位置取代值。

## <a name="binding-at-runtime"></a>執行階段的繫結

如果您需要先在函式中執行一些工作， `Queue`再使用、 `Blob`或`Table`之類的系結屬性，您可以使用`IBinder`介面。

下列範例會使用輸入佇列訊息，並在輸出佇列中建立含有相同內容的新訊息。 輸出佇列名稱會由函數主體中的程式碼設定。

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

如需詳細資訊，請參閱 Azure Functions 文件中的[執行階段的繫結](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) (英文)。

## <a name="binding-reference-information"></a>繫結參考資訊

Azure Functions 檔提供有關每個系結類型的參考資訊。 您會在每個系結參考文章中找到下列資訊。 （此範例是以儲存體佇列為基礎）。

* [封裝](../azure-functions/functions-bindings-storage-queue.md)。 您需要安裝的套件，包括對 Webjob SDK 專案中的系結支援。
* [範例](../azure-functions/functions-bindings-storage-queue-trigger.md)。 程式碼範例。 C # 類別庫範例適用于 Webjob SDK。 只要省略`FunctionName`屬性即可。
* [屬性](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations)。 要用於系結類型的屬性。
* [Configuration](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration)設定。 屬性屬性和構造函式參數的說明。
* [使用](../azure-functions/functions-bindings-storage-queue-trigger.md#usage)方式。 您可以系結的類型，以及系結運作方式的相關資訊。 例如：輪詢演算法、有害佇列處理。
  
如需系結參考文章的清單，請參閱 Azure Functions 的觸發程式[和](../azure-functions/functions-triggers-bindings.md#supported-bindings)系結文章中的「支援的系結」。 在該清單中，只有 Azure Functions （而非 Webjob SDK）支援 HTTP、Webhook 和 Event Grid 系結。

## <a name="disable-attribute"></a>Disable 屬性 

[`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs)屬性可讓您控制是否可以觸發函數。 

在下列範例中，如果應用程式設定`Disable_TestJob`的值為`1`或`True` （不區分大小寫），則不會執行函數。 在此情況下，執行階段會建立記錄訊息「*函式 'Functions.TestJob' 已停用*」。

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

當您變更 Azure 入口網站中的應用程式設定值時，WebJob 會重新開機以挑選新的設定。

此屬性可在參數、方法或類別等級宣告。 設定名稱也可以包含繫結運算式。

## <a name="timeout-attribute"></a>Timeout 屬性

如果[`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs)函式未在指定的時間內完成，則會導致該函式取消。 在下列範例中，函數會執行一天，而不會有 Timeout 屬性。 Timeout 會在15秒後取消函式。

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

您可以將 Timeout 屬性套用至類別或方法層級，也可以使用`JobHostConfiguration.FunctionTimeout`來指定全域超時。 類別層級或方法層級的超時會覆寫全域超時。

## <a name="singleton-attribute"></a>Singleton 屬性

[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs)屬性可確保只有一個函式實例會執行，即使有多個主機 web 應用程式實例。 它會使用[分散式鎖定](#viewing-lease-blobs)來完成這項程式。

在此範例中，只有單一的函式`ProcessImage`實例會在任何指定的時間執行：

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

某些觸發程序內建並行管理的支援：

* **QueueTrigger**。 將 `JobHostConfiguration.Queues.BatchSize` 設定為 `1`。
* **ServiceBusTrigger**。 將 `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` 設定為 `1`。
* **FileTrigger**。 將 `FileProcessor.MaxDegreeOfParallelism` 設定為 `1`。

您可以使用這些設定確保函式在單一執行個體上以單一項目執行。 若要確保當 web 應用程式向外延展至多個實例時，只有單一函式的實例正在執行，請在函數（`[Singleton(Mode = SingletonMode.Listener)]`）上套用接聽程式層級的單一鎖定。 當 JobHost 啟動時，就會取得接聽程式鎖定。 如果三個擴充的執行個體全部同時啟動，則其中只有一個執行個體會取得鎖定，且只有一個接聽程式會啟動。

### <a name="scope-values"></a>範圍值

您可以在 singleton 上指定*範圍運算式/值*。 運算式/值可確保會序列化特定範圍的函式的所有執行。 以這種方式執行更細微的鎖定，可以讓您的函式有某種程度的平行處理，同時根據您的需求來序列化其他調用。 例如，在下列程式碼中，範圍運算式會系結至`Region`傳入訊息的值。 當佇列中有三個訊息分別包含 [東部]、[東部] 和 [West] 區域時，將會以序列方式執行具有美國東部的訊息，而區域西部的訊息則會與 [東部] 中的人平行執行。

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

鎖定的預設範圍是`SingletonScope.Function`，這表示鎖定範圍（blob 租用路徑）會系結至完整的函數名稱。 若要鎖定跨函式`SingletonScope.Host` ，請在您不想要同時執行的所有函式中，指定並使用相同的領域識別碼名稱。 在下列範例中，一次只有 `AddItem` 或 `RemoveItem` 的一個執行個體會執行：

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>檢視租用二進位大型物件

WebJobs SDK 使用 [Azure 二進位大型物件租用](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)以實作分散式鎖定。 Singleton 所使用的租用 blob 可以在`azure-webjobs-host` `AzureWebJobsStorage`儲存體帳戶的容器中的「鎖定」路徑下找到。 例如，稍早所示第一個 `ProcessImage` 範例的租用二進位大型物件路徑可以是 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`。 所有路徑皆包含 JobHost 識別碼，在此個案中為 061851c758f04938a4426aa9ab3869c0。

## <a name="async-functions"></a>Async 函數

如需如何撰寫非同步函式程式碼的相關資訊，請參閱[Azure Functions 檔](../azure-functions/functions-dotnet-class-library.md#async)。

## <a name="cancellation-tokens"></a>取消權杖

如需如何處理取消權杖的相關資訊，請參閱[取消權杖與順利關機](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens) (英文) 上的 Azure Functions 文件。

## <a name="multiple-instances"></a>多個執行個體

如果您的 Web 應用程式在多個執行個體上執行，則會有一個連續的 WebJob 在每個執行個體上執行，以接聽觸發程序和呼叫函式。 各種觸發程序繫結的設計是為了有效地共用在執行個體之間合作的工作，如此便能擴充至更多執行個體，讓您能處理更多負載。

雖然有些觸發程式可能會導致雙重處理，但佇列和 blob 儲存體觸發程式會自動防止函式處理佇列訊息或 blob 超過一次。 如需詳細資訊，請參閱 Azure Functions 檔中[的設計相同的輸入](../azure-functions/functions-idempotent.md)。

計時器觸發程序會自動確保計時器只有一個執行個體會執行，因此在指定的排程時間不會有多個函式正在執行中。

如果您想要確保即使有多個主 web 應用程式實例，也只會執行一個函式的實例，您可以使用[`Singleton`](#singleton-attribute)屬性。

## <a name="filters"></a>篩選條件

函式篩選條件 (預覽) 讓您能夠使用自己的邏輯自訂 WebJobs 執行管線。 篩選器類似[ASP.NET Core 篩選](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)條件。 您可以將它們實作為套用至函數或類別的宣告式屬性。 如需詳細資訊，請參閱[函式篩選條件](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters) (英文)。

## <a name="logging-and-monitoring"></a>記錄和監視

我們建議針對 ASP.NET 開發的記錄架構。 「[開始](webjobs-sdk-get-started.md)使用」文章會說明其用法。 

### <a name="log-filtering"></a>記錄篩選

`ILogger` 執行個體建立的每個記錄皆有相關聯的 `Category` 和 `Level`。 [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)是列舉，而整數程式碼表示相對重要性：

|LogLevel    |程式碼|
|------------|---|
|追蹤       | 0 |
|偵錯       | 1 |
|資訊 | 2 |
|警告     | 3 |
|錯誤       | 4 |
|重大    | 5 |
|無        | 6 |

您可以將每個類別個別篩選為[`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)特定。 例如，您可能想要看見二進位大型物件觸發程序處理的所有記錄，但只看見所有其他項目的 `Error` 以上等級記錄。

#### <a name="version-3x"></a>第3版。*x*

第3版。*x* SDK 會依賴內建于 .net Core 的篩選。 `LogCategories` 類別可讓您為特定的函式、觸發程序或使用者定義類別。 它也會針對特定的主機狀態（例如`Startup`和`Results`）定義篩選。 這可以讓您微調記錄輸出。 如果在已定義的類別內找不到相符項目，則在決定是否篩選訊息時，篩選條件會回復為 `Default` 值。

`LogCategories` 需要下列 using 陳述式：

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

下列範例會根據預設，建立篩選以篩選層級的`Warning`所有記錄。 和`Function` `results`類別目錄（相當於`Host.Results`第2版中的。*x*）會在`Error`層級進行篩選。 篩選條件會將目前的類別與 `LogCategories` 執行個體中所有已註冊的層級比較，並選擇最長的相符項目。 這表示註冊的`Debug`層級`Host.Triggers`符合`Host.Triggers.Queue`或。 `Host.Triggers.Blob` 這可讓您控制更廣泛的分類，但無需一一新增。

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>第2版。*x*

在第2版中。*x* SDK，您可以使用`LogCategoryFilter`類別來控制篩選。 `LogCategoryFilter`具有具有初始值`Default`的`Information`屬性，表示`Information`會記錄、 `Warning`、 `Error`或`Critical`層級上的任何訊息，但`Debug`或`Trace`層級的任何訊息都會篩選掉。

與第`LogCategories` 3 版中的相同。*x*， `CategoryLevels`屬性可讓您指定特定分類的記錄層級，讓您可以微調記錄輸出。 如果在 `CategoryLevels` 目錄內找不到相符項目，則當決定是否篩選訊息時，篩選條件會回復到 `Default` 值。

下列範例建構一個預設會篩選 `Warning` 等級所有記錄的篩選條件。 `Function`和`Host.Results`類別目錄會在`Error`層級進行篩選。 The `LogCategoryFilter` 會將目前分類與所有已註冊的 `CategoryLevels` 比較，並選擇最長的相符項目。 因此， `Debug`為`Host.Triggers`註冊的層級`Host.Triggers.Queue`會`Host.Triggers.Blob`符合或。 這可讓您控制更廣泛的分類，但無需一一新增。

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Application Insights 的自訂遙測

針對[Application Insights](../azure-monitor/app/app-insights-overview.md)執行自訂遙測的程式取決於 SDK 版本。 若要了解如何設定 Application Insights，請參閱[新增 Application Insights 記錄](webjobs-sdk-get-started.md#add-application-insights-logging)。

#### <a name="version-3x"></a>第3版。*x*

因為第3版。Web 工作 SDK 的*x*會依賴 .net Core 泛型主機，因此不會再提供自訂遙測 factory。 但是，您可以使用相依性插入將自訂遙測新增至管線。 本節中的範例需要下列 `using` 陳述式：

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

的下列自訂執行[`ITelemetryInitializer`]可讓您將自己[`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry)的新增至[`TelemetryConfiguration`]預設值。

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

在[`ConfigureServices`]建立器中呼叫，將您[`ITelemetryInitializer`]的自訂新增至管線。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

[`TelemetryConfiguration`]在結構化時，會包含所有已[`ITelemetryInitializer`]註冊的類型。 若要深入瞭解，請參閱[自訂事件和計量的 APPLICATION INSIGHTS API](../azure-monitor/app/api-custom-events-metrics.md)。

在第3版中。*x*， [`TelemetryClient`]當主機停止時，您就不再需要排清。 .NET Core 相依性插入系統會自動處置已註冊`ApplicationInsightsLoggerProvider`的，這會[`TelemetryClient`]清除。

#### <a name="version-2x"></a>第2版。*x*

在第2版中。*x*，由[`TelemetryClient`] webjob SDK 的 Application Insights 提供者在內部建立的會[`ServerTelemetryChannel`](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/.publicApi/Microsoft.AI.ServerTelemetryChannel.dll)使用。 當 Application Insights 端點無法使用或正在節流連入要求時，此通道[會將要求儲存在 Web 應用程式的檔案系統中，並於稍後重新提交](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)。

[`TelemetryClient`]是由執行的類別所建立`ITelemetryClientFactory`。 根據預設，這會是[`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)。

如果您想要修改 Application Insights 管線的任何部分，您可以提供自己`ITelemetryClientFactory`的，而主機將會使用您的類別來建立。 [`TelemetryClient`] 例如，此程式碼會`DefaultTelemetryClientFactory`覆寫以修改的`ServerTelemetryChannel`屬性：

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

`SamplingPercentageEstimatorSettings`物件會設定調適型[取樣](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)。 這表示在某些高容量的案例中，Application Insights 會將選取的遙測資料子集傳送至伺服器。

建立遙測 factory 之後，請將它傳遞給 Application Insights 記錄提供者：

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> 後續步驟

本文提供的程式碼片段會示範如何處理使用 Webjob SDK 的常見案例。 如需完整範例，請參閱 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost) (英文)。

[ExecutionCoNtext]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[ITelemetryInitializer]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[JobHostConfiguration]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
