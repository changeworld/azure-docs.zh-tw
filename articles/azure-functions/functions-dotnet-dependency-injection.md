---
title: 在 .NET Azure Functions 中使用相依性插入
description: 瞭解如何在 .NET 函式中使用相依性插入來註冊和使用服務
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: df2acedd7f472b96d55d9ecc294d47e7173c5f90
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78329011"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET Azure Functions 中使用相依性插入

Azure Functions 支援相依性插入（DI）軟體設計模式，這項技術可在類別及其相依性之間達成[控制反轉（IoC）](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 。

- Azure Functions 中的相依性插入是以 .NET Core 相依性插入功能為基礎。 建議您熟悉[.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)相依性插入。 不過，在覆寫相依性方面，以及如何使用取用方案 Azure Functions 讀取設定值的方式有一些差異。

- 相依性插入的支援是以 Azure Functions 2.x 開始。

## <a name="prerequisites"></a>Prerequisites

您必須先安裝下列 NuGet 套件，才可以使用相依性插入：

- [Microsoft. Azure。擴充功能](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- 1\.0.28 或更新版本的[函數套件](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

## <a name="register-services"></a>註冊伺服器

若要註冊服務，請建立方法來設定並將元件新增至 `IFunctionsHostBuilder` 實例。  Azure Functions 主機會建立 `IFunctionsHostBuilder` 的實例，並將它直接傳遞至您的方法。

若要註冊方法，請加入 `FunctionsStartup` 元件屬性，以指定啟動期間所使用的型別名稱。

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>警示

在執行時間處理啟動類別之前和之後執行的一系列註冊步驟。 因此，請記住下列專案：

- *Startup 類別僅適用于設定和註冊。* 避免在啟動過程中使用在啟動時註冊的服務。 例如，請勿嘗試在啟動期間註冊的記錄器中記錄訊息。 註冊程式的這個點太早無法使用您的服務。 執行 `Configure` 方法之後，函式執行時間會繼續註冊額外的相依性，這可能會影響服務的運作方式。

- 相依性*插入容器只會保存明確註冊的類型*。 唯一可用來做為得以插入類型的服務就是 `Configure` 方法中的設定。 因此，在安裝期間或得以插入類型中，不能使用如 `BindingContext` 和 `ExecutionContext` 之類的函數特定類型。

## <a name="use-injected-dependencies"></a>使用插入的相依性

函式插入是用來讓您的相依性可在函式中使用。 使用「處理常式」插入時，您不需要使用靜態類別。

下列範例示範如何將 `IMyService` 和 `HttpClient` 相依性插入 HTTP 觸發的函式中。 這個範例會使用在啟動時註冊 `HttpClient` 所需的[Microsoft Extensions. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)套件。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>服務存留期

Azure Functions 應用程式提供與 ASP.NET 相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)相同的服務存留期。 針對函式應用程式，不同的服務存留期的行為如下所示：

- **暫時性**：暫時性服務會在每個服務要求時建立。
- 限**域**：限定範圍的服務存留期符合函式執行存留期。 限域服務會在每次執行時建立一次。 稍後在執行期間對該服務提出的要求會重複使用現有的服務實例。
- **Singleton**：單一服務存留期會符合主機存留期，並會在該實例上的函式執行重複使用。 建議連接和用戶端使用單一存留期服務，例如 `SqlConnection` 或 `HttpClient` 實例。

在 GitHub 上查看或下載[不同服務存留期的範例](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>記錄服務

如果您需要自己的記錄提供者，請將自訂類型註冊為 `ILoggerProvider` 實例。 Application Insights 由 Azure Functions 自動新增。

> [!WARNING]
> - 請勿將 `AddApplicationInsightsTelemetry()` 新增至服務集合，因為它會註冊與環境所提供之服務衝突的服務。
> - 如果您使用內建的 Application Insights 功能，請勿註冊您自己的 `TelemetryConfiguration` 或 `TelemetryClient`。 如果您需要設定自己的 `TelemetryClient` 實例，請透過插入的 `TelemetryConfiguration` 建立一個，如 [[監視器 Azure Functions](./functions-monitoring.md#version-2x-and-later-2)] 所示。

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> 和 ILoggerFactory

主機會將 `ILogger<T>` 和 `ILoggerFactory` 服務插入至函式。  不過，根據預設，這些新的記錄篩選器將會篩選掉函數記錄。  您將需要修改 `host.json` 檔案，以加入宣告其他篩選準則和類別。  下列範例將示範如何使用將由主機公開的記錄來新增 `ILogger<HttpTrigger>`。

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

以及新增記錄篩選的 `host.json` 檔案。

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>函數應用程式提供的服務

函數主機會註冊許多服務。 下列服務可安全地做為應用程式中的相依性：

|服務類型|存留期|描述|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|實體|執行時間設定|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|實體|負責提供主控制項實例的識別碼|

如果您想要取得相依性的其他服務，請[在 GitHub 上建立問題並加以提議](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>覆寫主機服務

目前不支援覆寫主機所提供的服務。  如果您想要覆寫服務，請[在 GitHub 上建立問題並加以提議](https://github.com/azure/azure-functions-host)。

## <a name="working-with-options-and-settings"></a>使用選項和設定

在 [[應用程式設定](./functions-how-to-use-azure-function-app-settings.md#settings)] 中定義的值會在 `IConfiguration` 實例中提供，可讓您讀取啟動類別中的應用程式設定值。

您可以從 `IConfiguration` 實例中，將值解壓縮至自訂類型。 將應用程式設定值複製到自訂類型，可以讓這些值得以插入，輕鬆地測試您的服務。 讀入設定實例的設定必須是簡單的索引鍵/值組。

請考慮下列類別，其中包含名稱與應用程式設定一致的屬性：

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

以及可能會將自訂設定結構為的 `local.settings.json` 檔案，如下所示：
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

從 `Startup.Configure` 方法中，您可以使用下列程式碼，將值從 `IConfiguration` 實例解壓縮至您的自訂類型：

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

呼叫 `Bind` 會將具有相符屬性名稱的值從設定複製到自訂實例。 Options 實例現在可用於 IoC 容器中，以插入函式中。

Options 物件會插入至函式中，做為泛型 `IOptions` 介面的實例。 使用 `Value` 屬性來存取您的設定中找到的值。

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

如需有關使用選項的詳細資訊，請參閱[ASP.NET Core 中的選項模式](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

> [!WARNING]
> *請避免*嘗試從檔案（例如，appsettings）讀取值 *。 {環境}. json* （使用方式方案）。 因為裝載基礎結構無法存取設定資訊，所以無法在應用程式調整時使用從這些與觸發連線相關的檔案中讀取的值。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

- [如何監視您的函數應用程式](functions-monitoring.md)
- [函數的最佳做法](functions-best-practices.md)
