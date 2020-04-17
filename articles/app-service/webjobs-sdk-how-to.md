---
title: 如何使用 Web 工作 SDK
description: 深入了解如何針對 WebJobs SDK 撰寫程式碼。 創建訪問 Azure 和第三方服務中數據的事件驅動的後台處理作業。
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: a1fd22772e72cba4cce3f9fa2751dc0df0e15bb9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535593"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>如何使用 Azure WebJobs SDK 進行事件驅動幕後處理

本文提供有關如何使用 Azure Web 作業 SDK 的指導。 要立即開始 Web 作業,請參閱[Azure Web 作業 SDK 開始進行事件驅動的後台處理](webjobs-sdk-get-started.md)。 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

這些是版本 3 之間的主要區別。*x*和版本 2。Web 作業 SDK 的*x:*

* 版本 3.*x*添加了對 .NET 核心的支援。
* 在版本3中。*x*, 您需要顯示式安裝 WebJobs SDK 所需的儲存系統延伸。 在版本 2 中。*x*,存儲綁定包含在 SDK 中。
* 用於 .NET 核心 (3) 的可視化工作室工具。*x*) 專案不同於 .NET 框架 (2) 的工具。*x*) 專案。 要瞭解更多資訊,請參閱[使用視覺化工作室 - Azure 應用服務開發和部署 Web 作業](webjobs-dotnet-deploy-vs.md)。

如果可能,為兩個版本 3 提供了示例。*x*和版本 2。*x*. .

> [!NOTE]
> [Azure 函數](../azure-functions/functions-overview.md)基於 WebJobs SDK 構建,本文提供了指向 Azure 函數文檔的連結,用於某些主題。 請注意函數與 Web 作業 SDK 之間的這些差異:
> * Azure 函數版本 2。*x*對應於 Web 作業 SDK 版本 3。*x*和 Azure 函數 1。*x*對應於 Web 作業 SDK 2。*x*. . 源代碼儲存庫使用 WebJobs SDK 編號。
> * Azure 函數 C# 類別庫的範例碼類似於 WebJobs SDK 代碼,只不過 WebJobs SDK 專案中不需要`FunctionName`屬性。
> * 某些綁定類型僅在函數中受支援,例如 HTTP(Webhook)和事件網格(基於 HTTP)。
>
> 如需詳細資訊，請參閱[比較 WebJobs SDK 和 Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs) (英文)。

## <a name="webjobs-host"></a>WebJobs 主機

此主機是函式的執行階段容器。  它會接聽觸發程序並呼叫函式。 在版本3中。*x*, 主機`IHost`是 的 實現。 在版本 2 中。*x*,`JobHost`使用 物件。 您可以在程式碼中建立主機執行個體，並撰寫程式碼以自訂其行為。

這是直接使用 WebJobs SDK 和通過 Azure 函數間接使用它之間的主要區別。 在 Azure 函數中,服務控制主機,並且不能通過編寫代碼來自定義主機。 Azure Functions 可讓您透過設定 host.json 檔案自訂主機行為。 這些設置是字串,而不是代碼,這限制了您可以執行的自定義類型。

### <a name="host-connection-strings"></a>主機連接字串

WebJob SDK 在本地端執行時或在本地運行時在本地.settings.json 檔案中或在 Azure 中執行時在 WebJob 環境中尋找 Azure 儲存和 Azure 服務總線連接字串。 預設情況下,需要名為的`AzureWebJobsStorage`儲存連接字串設置。  

版本 2。SDK 的*x*允許您對這些連接字串使用自己的名稱,或將其儲存在其他位置。 您可以使用 設定代碼中的名稱[`JobHostConfiguration`], 如下所示:

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

因為版本3。*x*使用預設的 .NET Core 設定 API,沒有用於更改連接字串名稱的 API。

### <a name="host-development-settings"></a>主機開發設定

您可以在開發模式中執行主機，讓本機開發更有效率。 以下是在開發模式下執行時更改的一些設定:

| 屬性 | 開發設定 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` 將記錄輸出最大化。 |
| `Queues.MaxPollingInterval`  | 最低值可確保立即觸發佇列方法。  |
| `Singleton.ListenerLockPeriod` | 15 秒在快速反覆式開發法中有幫助。 |

啟用開發模式的過程取決於 SDK 版本。 

#### <a name="version-3x"></a>版本 3.*x*

版本 3.*x*使用標準ASP.NET核心 API。 調用[`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment)[`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)實例上的方法。 傳遞名為`development`的字串,如本範例中所示:

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

#### <a name="version-2x"></a>版本 2。*x*

`JobHostConfiguration` 類別具有可啟用開發模式的 `UseDevelopmentSettings` 方法。  下列範例示範如何使用開發設定。 在`config.IsDevelopment`本地`true`端執行時傳回,設定`AzureWebJobsEnv``Development`使用 值 命名的本地環境變數 。

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

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>管理併發連接(版本 2)。*x*)

在版本3中。*x*,連接限制預設值為無限連接。 如果由於某種原因需要更改此限制,則可以使用[`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver)[`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler)類的屬性。

在版本 2 中。*x*,您可以使用[ServicePointManager.默認連接限制](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit)API 來控制與主機的併發連接數。 在 2 中。*x*,在啟動 Web 作業主機之前,應從預設值 2 中增加此值。

使用`HttpClient`流流`ServicePointManager`從函數發出的所有傳出 HTTP 請求。 到達`DefaultConnectionLimit`中 設定的值`ServicePointManager`後, 在發送請求之前開始排隊。 假設您的 `DefaultConnectionLimit` 設為 2，且您的程式碼會發出 1,000 個 HTTP 要求。 一開始，只允許兩個要求傳至作業系統。 其他的 998 個要求會排入佇列，直到有足夠的空間給它們。 這意味著您可能`HttpClient`超時,因為它似乎已經發出請求,但請求從未由作業系統發送到目標伺服器。 因此，您可能會看到一個似乎不合理的行為：您的本機 `HttpClient` 花費 10 秒完成要求，但您的服務正以 200 毫秒的時間傳回每個要求。 

ASP.NET應用程式的預設值為`Int32.MaxValue`,這可能適用於在基本或更高應用服務計畫中運行的 Web 作業。 Web 作業通常需要「始終打開」設置,並且僅支援基本和更高的應用服務計劃。

如果 WebJob 正在免費或共用的 App Service 方案中執行，則您的應用程式會受到 App Service 沙箱的限制，沙箱目前的[連線限制為 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)。 在 中為未綁定`ServicePointManager`連接 限制時,更有可能達到沙箱連接閾值,網站將關閉。 在此狀況下，將 `DefaultConnectionLimit` 設為更低的值，如 50 或 100，可防止這種狀況發生，同時仍允許有足夠的輸送量。

此設定必須先設定，才能發出任何 HTTP 要求。 因此,WebJobs 主機不應自動調整設置。 主機啟動之前可能會出現 HTTP 請求,這可能導致意外行為。 最好的方法是在初始化`Main``JobHost`之前立即在方法中設置值,如下所示:

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

函數必須是公共方法,並且必須具有一個觸發器屬性或屬性[`NoAutomaticTrigger`](#manual-triggers)。

### <a name="automatic-triggers"></a>自動觸發器

自動觸發程序會呼叫函式以回應事件。 請考慮由添加到 Azure 佇列存儲的消息觸發的函數的範例。 它透過 Azure Blob 儲存讀取 Blob 來回應:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger``myqueue-items`每當 佇列中出現佇列消息時,該屬性都會告訴運行時調用函數。 該`Blob`屬性告訴執行時使用佇列消息讀取*範例工作項*容器中的 Blob。 `samples-workitems`容器中的 blob 項的名稱直接從佇列觸發器作為綁定運算式`{queueTrigger}`() 獲得。

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>手動觸發器

要手動觸發函數,請使用`NoAutomaticTrigger`屬性 ,如下所示:

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

手動觸發函數的過程取決於 SDK 版本。

#### <a name="version-3x"></a>版本 3.*x*

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

#### <a name="version-2x"></a>版本 2。*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>輸入與輸出繫結

輸入繫結會提供宣告式方法，讓 Azure 或協力廠商服務中的資料可供您的程式碼使用。 輸出繫結會提供更新資料的方法。 入門[文章](webjobs-sdk-get-started.md)顯示了每個示例。

通過將屬性應用於方法返回值,可以為輸出綁定使用方法返回值。 請參閱[使用 Azure 函數返回值](../azure-functions/functions-bindings-return-value.md)中的範例。

## <a name="binding-types"></a>繫結型別

安裝和管理綁定類型的過程取決於您是否使用版本 3。*x*或版本 2。SDK 的*x。* 您可以在該繫結型類型的 Azure 函數[引用文章](#binding-reference-information)的「包」部分中找到要為特定綁定類型安裝的包。 一個例外是檔觸發器和綁定(對於本地檔案系統),Azure 函數不支援它。

#### <a name="version-3x"></a>版本 3.*x*

在版本3中。*X*,儲存結合包含在套件中`Microsoft.Azure.WebJobs.Extensions.Storage`。 呼叫方法`AddAzureStorage`中的擴充`ConfigureWebJobs`方法, 如下所示:

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

若要使用其他觸發程序與繫結類型，請安裝包含這些項目的 NuGet 套件，並呼叫在擴充功能中實作的 `Add<binding>` 擴充方法。 例如,如果要使用 Azure Cosmos DB 綁`Microsoft.Azure.WebJobs.Extensions.CosmosDB`定,請`AddCosmosDB`安裝 和調用 ,如下所示:

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

#### <a name="version-2x"></a>版本 2。*x*

這些觸發器和綁定類型包含在版本 2 中。*x*`Microsoft.Azure.WebJobs`套件的 x:

* Blob 儲存體
* 佇列儲存體
* 表格儲存體

若要使用其他觸發程序與繫結型別，請安裝包含它們的 NuGet 封裝，並在 `JobHostConfiguration` 物件上呼叫 `Use<binding>` 方法。 例如,如果要使用計時器觸發器,請在方法中安裝`Microsoft.Azure.WebJobs.Extensions`和呼`UseTimers`叫`Main`, 如下所示:

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

Web 工作允許您連結到[`ExecutionContext`]。 使用此繫結,您可以在函數簽名中訪問[`ExecutionContext`]作為 參數。 例如，下列程式碼會使用內容物件存取引動過程識別碼，而您可以使用此識別碼讓給定的函式引動過程所產生的所有記錄相互關聯。  

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

綁定到[`ExecutionContext`]的過程 取決於您的 SDK 版本。

#### <a name="version-3x"></a>版本 3.*x*

呼叫方法`AddExecutionContextBinding`中的擴充`ConfigureWebJobs`方法, 如下所示:

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

#### <a name="version-2x"></a>版本 2。*x*

先前所提到的 `Microsoft.Azure.WebJobs.Extensions` 封裝方法也提供特殊的繫結型別，您可以呼叫 `UseCore` 方法來註冊該繫結型別。 此繫結允許您在函數簽名[`ExecutionContext`]中定義參數,該參數啟用時如下所示:

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

您可以配置某些觸發器和綁定的行為。 配置它們的過程取決於 SDK 版本。

* **版本 3.*x***:`Add<Binding>`在中呼`ConfigureWebJobs`叫方法 時設定設定。
* **版本 2。*x*:** 透過在傳遞給的設定物件設定屬性設定設定`JobHost`。

這些特定於綁定的設置等效於 Azure 函數中的[host.json 專案檔中](../azure-functions/functions-host-json.md)的設置。

您可以設定以下繫結:

* [Azure 宇宙DB 觸發器](#azure-cosmosdb-trigger-configuration-version-3x)
* [事件中樞觸發程序](#event-hubs-trigger-configuration-version-3x)
* [佇列儲存體觸發程序](#queue-storage-trigger-configuration)
* [傳送格線繫結](#sendgrid-binding-configuration-version-3x)
* [服務匯流排觸發程序](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure 宇宙DB 觸發器配置(版本 3)。*x*)

此範例展示如何設定 Azure 宇宙資料庫觸發器:

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

有關詳細資訊,請參閱 Azure [CosmosDB 綁定](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)文章。

### <a name="event-hubs-trigger-configuration-version-3x"></a>事件中心觸發配置(版本 3)。*x*)

此範例展示如何設定事件中心觸發器:

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

有關詳細資訊,請參閱[事件中心綁定](../azure-functions/functions-bindings-event-hubs-output.md#hostjson-settings)文章。

### <a name="queue-storage-trigger-configuration"></a>區列儲存觸發器設定

這些範例展示如何設定佇列儲存觸發器:

#### <a name="version-3x"></a>版本 3.*x*

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

有關詳細資訊,請參閱[佇列存儲綁定](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)一文。

#### <a name="version-2x"></a>版本 2。*x*

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

有關詳細資訊,請參閱[主機.json v1.x 引用](../azure-functions/functions-host-json-v1.md#queues)。

### <a name="sendgrid-binding-configuration-version-3x"></a>發送格線線綁定配置(版本 3。*x*)

此範例展示如何設定 SendGrid 輸出繫結:

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

有關詳細資訊,請參閱[SendGrid 綁定](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)文章。

### <a name="service-bus-trigger-configuration-version-3x"></a>服務總線觸發器配置(版本 3。*x*)

此範例展示如何設定服務匯流線觸發程式:

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

有關詳細資訊,請參閱[服務總線綁定](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)文章。

### <a name="configuration-for-other-bindings"></a>其他繫結的設定

某些觸發器和綁定類型定義它們自己的自定義配置類型。 例如,檔案觸發器允許您指定要監視的根路徑,如以下範例所示:

#### <a name="version-3x"></a>版本 3.*x*

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

#### <a name="version-2x"></a>版本 2。*x*

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

有時,您希望在代碼中指定佇列名稱、blob 名稱或容器或表名,而不是對其進行硬編碼。 例如，您可能想要在設定檔或環境變數中指定 `QueueTrigger` 屬性的佇列名稱。

可以通過`NameResolver`將 物件`JobHostConfiguration`傳遞給 對象來執行此操作。 在觸發程序或繫結屬性建構函式中包含預留位置，然後 `NameResolver` 程式碼會提供實際要用以取代這些預留位置的值。 您可以透過以百分比 (%) 來識別占位符符號,如下所示:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

此程式碼可讓您在測試環境中使用名為 `logqueuetest` 的佇列，以及在生產環境中使用名為 `logqueueprod` 的佇列。 您不必使用硬式編碼的佇列名稱，而是在 `appSettings` 集合中指定項目的名稱。

如果未提供自訂預設值,`NameResolver`則預設預設值將生效。 此預設值會從應用程式設定或環境變數取得值。

您的`NameResolver`類別`appSettings`從 取得的佇列名稱,如下所示:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>版本 3.*x*

使用依賴項注入配置解析器。 這些範例需要下列 `using` 陳述式：

```cs
using Microsoft.Extensions.DependencyInjection;
```

通過在[`ConfigureServices`][`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)上 呼叫擴充方法來新增解析器,如以下範例所示:

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

#### <a name="version-2x"></a>版本 2。*x*

將類別`NameResolver`傳遞給物件,`JobHost`如下所示:

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

如果在使用`Queue`綁定屬性(如`Blob``Table`, 或 )之前需要在函數中執行一些工作,`IBinder`則可以使用 該介面。

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

Azure 函數文件提供有關每種綁定類型的參考資訊。 您將在每個綁定參考文章中找到以下資訊。 (此示例基於存儲佇列。

* [套件](../azure-functions/functions-bindings-storage-queue.md)。 您需要安裝的套件,以在 WebJobs SDK 專案中包含對綁定的支援。
* [範例](../azure-functions/functions-bindings-storage-queue-trigger.md)。 代碼示例。 C# 類別庫範例適用於 WebJobs SDK。 省略屬性`FunctionName`。
* [屬性](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations)。 用於綁定類型的屬性。
* [設定](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). 屬性屬性和建構函數參數的說明。
* [用法](../azure-functions/functions-bindings-storage-queue-trigger.md#usage)。 可以綁定到的類型以及有關綁定工作原理的資訊。 例如：輪詢演算法、有害佇列處理。
  
有關綁定參考文章的清單,請參閱 Azure 函數的[觸發器和綁定](../azure-functions/functions-triggers-bindings.md#supported-bindings)文章中的"受支援綁定" 在清單中,HTTP、Webhook 和事件網格綁定僅由 Azure 函數支援,而 WebJobs SDK 不支援。

## <a name="disable-attribute"></a>Disable 屬性 

該[`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs)屬性允許您控制是否可以觸發函數。 

在下面的範例中,如果應用設定`Disable_TestJob`的值為`1`或`True`(不區分大小寫),則函數將不會運行。 在此情況下，執行階段會建立記錄訊息「*函式 'Functions.TestJob' 已停用*」。

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

更改 Azure 門戶中的應用設置值時,WebJob 將重新啟動以選取新設置。

此屬性可在參數、方法或類別等級宣告。 設定名稱也可以包含繫結運算式。

## <a name="timeout-attribute"></a>Timeout 屬性

如果[`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs)函數未在指定的時間內完成,則該屬性會導致該函數被取消。 在下面的示例中,函數將運行一天,而不運行超時屬性。 超時導致功能在15秒後被取消。

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

可以在類或方法等級應用超時屬性,並且可以使用 指定全域超時`JobHostConfiguration.FunctionTimeout`。 類級或方法級超時覆蓋全域超時。

## <a name="singleton-attribute"></a>Singleton 屬性

該[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs)屬性可確保僅運行函數的一個實例,即使主機 Web 應用有多個實例也是如此。 它通過使用[分散式鎖定](#viewing-lease-blobs)來這樣做。

此範例中,`ProcessImage`在任何定時間僅執行函數的單一個實體:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

某些觸發程序內建並行管理的支援：

* **佇列觸發器**。 將 `JobHostConfiguration.Queues.BatchSize` 設定為 `1`。
* **服務匯流發程式**。 將 `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` 設定為 `1`。
* **檔案觸發器**。 將 `FileProcessor.MaxDegreeOfParallelism` 設定為 `1`。

您可以使用這些設定確保函式在單一執行個體上以單一項目執行。 為了確保在 Web 應用擴展到多個實例時,該函數的單個實例僅運行,請對函數`[Singleton(Mode = SingletonMode.Listener)]`() 應用偵聽器級單例鎖。 作業主機啟動時獲取偵聽器鎖。 如果三個擴充的執行個體全部同時啟動，則其中只有一個執行個體會取得鎖定，且只有一個接聽程式會啟動。

### <a name="scope-values"></a>範圍值

您可以在單一上指定*作用網域表示式/值*。 運算式/值可確保在特定作用域下對函數的所有執行都將序列化。 以這種方式實現更精細的鎖定可以允許函數實現某種程度的並行性,同時按要求序列化其他調用。 例如,在以下代碼中,作用域運算式綁定到傳入消息`Region`的值。 當佇列分別在「東部」、「東部」和「西部」區域包含三條消息時,具有「東部」區域的消息將串行運行,而具有「西部區域」的消息將與東部的消息並行運行。

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

鎖的預設作用網域`SingletonScope.Function`是 ,這意味著鎖範圍(blob 租約路徑)綁定到完全限定的函數名稱。 要跨函數鎖定,請`SingletonScope.Host`指定並使用與不想同時運行的所有函數相同的作用域 ID 名稱。 在下列範例中，一次只有 `AddItem` 或 `RemoveItem` 的一個執行個體會執行：

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

WebJobs SDK 使用 [Azure 二進位大型物件租用](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)以實作分散式鎖定。 Singleton 使用的租約 Blob 可以在路徑`azure-webjobs-host`"鎖" 下`AzureWebJobsStorage`的存儲帳戶 中的容器中找到。 例如，稍早所示第一個 `ProcessImage` 範例的租用二進位大型物件路徑可以是 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`。 所有路徑皆包含 JobHost 識別碼，在此個案中為 061851c758f04938a4426aa9ab3869c0。

## <a name="async-functions"></a>Async 函數

有關如何編寫非同步函數的代碼,請參閱[Azure 函式文件](../azure-functions/functions-dotnet-class-library.md#async)。

## <a name="cancellation-tokens"></a>取消權杖

如需如何處理取消權杖的相關資訊，請參閱[取消權杖與順利關機](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens) (英文) 上的 Azure Functions 文件。

## <a name="multiple-instances"></a>多個執行個體

如果您的 Web 應用程式在多個執行個體上執行，則會有一個連續的 WebJob 在每個執行個體上執行，以接聽觸發程序和呼叫函式。 各種觸發程序繫結的設計是為了有效地共用在執行個體之間合作的工作，如此便能擴充至更多執行個體，讓您能處理更多負載。

雖然某些觸發器可能會導致雙重處理,但佇列和 Blob 儲存觸發器會自動阻止函數多次處理佇列消息或 Blob。 有關詳細資訊,請參閱在 Azure 函式文件中[設計相同的輸入](../azure-functions/functions-idempotent.md)。

計時器觸發程序會自動確保計時器只有一個執行個體會執行，因此在指定的排程時間不會有多個函式正在執行中。

如果要確保即使主機 Web 應用有多個實例,也只能運行一個函數實例,則可以[`Singleton`](#singleton-attribute)使用 該 屬性。

## <a name="filters"></a>篩選器

函式篩選條件 (預覽) 讓您能夠使用自己的邏輯自訂 WebJobs 執行管線。 篩選器類似於[ASP.NET 核心篩選器](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)。 您可以將它們實現為應用於函數或類的聲明性屬性。 如需詳細資訊，請參閱[函式篩選條件](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters) (英文)。

## <a name="logging-and-monitoring"></a>記錄和監視

我們建議為ASP.NET開發的日誌記錄框架。 [入門](webjobs-sdk-get-started.md)文章演示如何使用它。 

### <a name="log-filtering"></a>記錄篩選

`ILogger` 執行個體建立的每個記錄皆有相關聯的 `Category` 和 `Level`。 [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)是枚舉,整數代碼表示相對重要性:

|LogLevel    |程式碼|
|------------|---|
|追蹤       | 0 |
|偵錯       | 1 |
|資訊 | 2 |
|警告     | 3 |
|錯誤       | 4 |
|重大    | 5 |
|None        | 6 |

您可以獨立地將每個類別篩選為特定[`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)。 例如，您可能想要看見二進位大型物件觸發程序處理的所有記錄，但只看見所有其他項目的 `Error` 以上等級記錄。

#### <a name="version-3x"></a>版本 3.*x*

版本 3.SDK 的*x*依賴於內建於 .NET Core 中的篩選。 `LogCategories` 類別可讓您為特定的函式、觸發程序或使用者定義類別。 它還為特定主機狀態(如和`Startup``Results`)定義篩選器。 這使您能夠微調日誌記錄輸出。 如果在已定義的類別內找不到相符項目，則在決定是否篩選訊息時，篩選條件會回復為 `Default` 值。

`LogCategories` 需要下列 using 陳述式：

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

下面的範例建構一個篩選器,預設情況下,該篩選器篩選`Warning`該級別中的所有日誌。 `Function`和`results`類別(等效`Host.Results`於版本2中。*x*)`Error`在級別上進行篩選。 篩選條件會將目前的類別與 `LogCategories` 執行個體中所有已註冊的層級比較，並選擇最長的相符項目。 `Debug`這意味著為`Host.Triggers`匹配`Host.Triggers.Queue`或`Host.Triggers.Blob`註冊級別。 這可讓您控制更廣泛的分類，但無需一一新增。

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

#### <a name="version-2x"></a>版本 2。*x*

在版本 2 中。SDK 的*x,*`LogCategoryFilter`您可以使用 類別控制篩選。 `LogCategoryFilter`具有`Default`初始值的屬性`Information`,這意味著將記錄`Information`、 `Warning`、 `Error`、`Critical`或級別上的任何消息`Debug`,`Trace`但 或級別上的任何消息都被過濾掉。

與版本`LogCategories`3一樣。屬性允許您為特定類別指定日誌級別,以便可以微調日誌記錄輸出。 *x* `CategoryLevels` 如果在 `CategoryLevels` 目錄內找不到相符項目，則當決定是否篩選訊息時，篩選條件會回復到 `Default` 值。

下列範例建構一個預設會篩選 `Warning` 等級所有記錄的篩選條件。 和`Function``Host.Results`類別在`Error`級別上進行篩選。 The `LogCategoryFilter` 會將目前分類與所有已註冊的 `CategoryLevels` 比較，並選擇最長的相符項目。 因此,`Debug`註冊`Host.Triggers`的等級會`Host.Triggers.Queue`符合`Host.Triggers.Blob`或 。 這可讓您控制更廣泛的分類，但無需一一新增。

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

實現[應用程式見解](../azure-monitor/app/app-insights-overview.md)的自定義遙測的過程取決於 SDK 版本。 若要了解如何設定 Application Insights，請參閱[新增 Application Insights 記錄](webjobs-sdk-get-started.md#add-application-insights-logging)。

#### <a name="version-3x"></a>版本 3.*x*

因為版本3。*WebJobs* SDK 的 x 依賴於 .NET Core 泛型主機,不再提供自定義遙測工廠。 但是,您可以使用依賴項注入向管道添加自定義遙測。 本節中的範例需要下列 `using` 陳述式：

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

下面的自訂讓[`ITelemetryInitializer`]您將自己新增到[`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry)預設值[`TelemetryConfiguration`]。

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

調用[`ConfigureServices`]生成器以將自[`ITelemetryInitializer`]定義 添加到管道。

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

構造[`TelemetryConfiguration`]時,將包含所有已註冊[`ITelemetryInitializer`]的 類型。 要瞭解更多資訊,請參閱[有關自定義事件和指標的應用程式見解 API。](../azure-monitor/app/api-custom-events-metrics.md)

在版本3中。*x*,當主機[`TelemetryClient`]停止 時,您不再需要刷新 。 .NET 核心依賴項注入系統自動釋放已註冊`ApplicationInsightsLoggerProvider`的 ,後者[`TelemetryClient`]刷新 。

#### <a name="version-2x"></a>版本 2。*x*

在版本 2 中。*x*,WebJobs [`TelemetryClient`] SDK 的應用程式見解提供者在內部[`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)建立的 使用 。 當 Application Insights 端點無法使用或正在節流連入要求時，此通道[會將要求儲存在 Web 應用程式的檔案系統中，並於稍後重新提交](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)。

[`TelemetryClient`]由實現`ITelemetryClientFactory`的類創建。 預設情況下,這是[`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)。

如果要修改應用程式見解導管的任何部分,可以提供您`ITelemetryClientFactory`自己的 ,並且主機將使用類建[`TelemetryClient`]構 。 例如,此代碼重寫`DefaultTelemetryClientFactory`以修改`ServerTelemetryChannel`屬性:

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

物件`SamplingPercentageEstimatorSettings`設定[調整取樣 。](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) 這意味著在某些大容量方案中,應用程式見解向伺服器發送選定的遙測數據子集。

建立遙測工廠後,將其傳遞給應用程式見解紀錄記錄提供者:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> 後續步驟

本文提供了代碼段,其中演示如何處理使用 WebJobs SDK 的常見方案。 如需完整範例，請參閱 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost) (英文)。

["執行上下文"]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
["I遙測初始化器"]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
["作業主機配置"]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
