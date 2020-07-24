---
title: 在 .NET Azure Functions 中使用相依性插入
description: 了解如何在 .NET 函式中使用相依性插入來註冊和使用服務
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 02cb862c5ec6f75d546aabcd6e8ac97a4de961a4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082948"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET Azure Functions 中使用相依性插入

Azure Functions 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。

- Azure Functions 中的相依性插入是以 .NET Core 相依性插入功能為基礎。 建議熟悉 [.NET Core 相依性插入](/aspnet/core/fundamentals/dependency-injection) (機器翻譯)。 您覆寫相依性的方式，與在取用方案上使用 Azure Functions 讀取設定值的方式有所不同。

- 從 Azure Functions 2.x 開始支援相依性插入。

## <a name="prerequisites"></a>Prerequisites

您必須安裝下列 NuGet 套件，才能使用相依性插入：

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 套件 1.0.28 版或更新版本

## <a name="register-services"></a>註冊伺服器

若要註冊服務，請建立方法來設定元件並將其新增至 `IFunctionsHostBuilder` 執行個體。  Azure Functions 主機會建立 `IFunctionsHostBuilder` 的執行個體，並將其直接傳入方法。

若要註冊方法，請新增 `FunctionsStartup` 組件屬性，以指定啟動期間所使用的類型名稱。

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

此範例會使用在啟動時註冊 `HttpClient` 所需的 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 套件。

### <a name="caveats"></a>警示

在執行階段處理啟動類別的前後會執行一系列註冊步驟。 因此，請記住下列項目：

- 啟動類別僅用於設定和註冊。 避免使用啟動時在啟動程序期間註冊的服務。 例如，請勿嘗試在啟動期間註冊的記錄器中記錄訊息。 註冊程序的這個時間點太早，還無法提供服務使用。 執行 `Configure` 方法之後，Functions 執行階段會繼續註冊其他相依性，這可能會影響服務的運作方式。

- 相依性插入容器只會保留明確註冊的類型。 唯一作為可插入類型使用的服務是在 `Configure` 方法中所設定服務。 因此，Function 特定類型 (例如 `BindingContext` 和 `ExecutionContext`) 無法在設定期間使用或作為可插入的類型使用。

## <a name="use-injected-dependencies"></a>使用插入的相依性

您可使用建構函式插入，讓相依性可供函式使用。 使用「處理常式」插入時，您不需要針對插入的服務或您的函式類別使用靜態類別。

下列範例示範如何將 `IMyService` 和 `HttpClient` 相依性插入 HTTP 觸發的函式中。

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, MyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

此範例會使用在啟動時註冊 `HttpClient` 所需的 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 套件。

## <a name="service-lifetimes"></a>服務存留期

Azure Functions 應用程式提供與 [ASP.NET 相依性插入](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)相同的服務存留期。 針對 Functions 應用程式，不同服務存留期的行為如下所示：

- **暫時性**：暫時性服務會在每次要求服務時建立。
- **限域**：限域服務存留期符合函式執行存留期。 限域服務會在每次執行後建立。 稍後在執行期間對該服務提出的要求會重複使用現有服務執行個體。
- **單一**：單一服務存留期符合主機存留期，並會在該執行個體上跨函式執行重複使用。 建議連線和用戶端使用單一存留期服務，例如 `DocumentClient` 或 `HttpClient` 執行個體。

請前往 GitHub 檢視或下載[不同服務存留期的範本](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>記錄服務

如果您需要自己的記錄提供者，請將自訂類型註冊為的實例 [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) ，這可透過[Microsoft Extensions. 記錄抽象](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)NuGet 封裝取得。

Azure Functions 會自動新增 Application Insights。

> [!WARNING]
> - 請勿將 `AddApplicationInsightsTelemetry()` 新增至服務集合，因為這會註冊與環境所提供服務衝突的服務。
> - 如果使用內建 Application Insights 功能，請勿註冊自己的 `TelemetryConfiguration` 或 `TelemetryClient`。 如果需要設定自己的 `TelemetryClient` 執行個體，請透過插入的 `TelemetryConfiguration` 建立一個，如[監視 Azure Functions](./functions-monitoring.md#version-2x-and-later-2) 中所示。

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> 和 ILoggerFactory

主機會將 `ILogger<T>` 和 `ILoggerFactory` 服務插入至函式。  不過，根據預設，這些新的記錄篩選器會篩選掉函數記錄。  您必須修改檔案 `host.json` ，以加入宣告其他篩選準則和類別目錄。

下列範例示範如何加入 `ILogger<HttpTrigger>` 具有記錄檔的，而這些記錄會公開至主機。

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

下列範例檔案會 `host.json` 加入記錄篩選器。

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

函式主機會註冊許多服務。 下列服務可安全地作為應用程式中的相依性：

|服務類型|存留期|描述|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|單一|執行階段設定|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|單一|負責提供主機執行個體的識別碼|

如有其他要相依的服務，請[在 GitHub 上建立並張貼問題](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>覆寫主機服務

目前不支援覆寫主機所提供的服務。  如有要覆寫的服務，請[在 GitHub 上建立並張貼問題](https://github.com/azure/azure-functions-host)。

## <a name="working-with-options-and-settings"></a>使用選項和設定

[應用程式設定](./functions-how-to-use-azure-function-app-settings.md#settings)中定義的值位於 `IConfiguration` 執行個體中，其可供讀取啟動類別中的應用程式設定值。

您可將值從 `IConfiguration` 執行個體擷取到自訂類型中。 將應用程式設定值複製到自訂類型會使這些值得以插入，以供輕鬆地測試服務。 讀入設定執行個體的設定必須是簡單的索引鍵/值組。

請考慮下列類別，其中包含名稱與應用程式設定一致的屬性：

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

而 `local.settings.json` 檔案可能會建構自訂設定，如下所示：
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

您可從 `Startup.Configure` 方法內部使用下列程式碼，以將值從 `IConfiguration` 執行個體擷取到自訂類型中：

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

呼叫 `Bind` 會將具有相符屬性名稱的值從設定複製到自訂執行個體中。 選項執行個體現在可供 IoC 容器用來插入函式中。

選項物件會作為一般 `IOptions` 介面的執行個體插入函式中。 您可使用 `Value` 屬性來存取在設定中找到的值。

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

如需使用選項的詳細資訊，請參閱 [ASP.NET Core 中的選項模式](/aspnet/core/fundamentals/configuration/options)。

> [!WARNING]
> 避免嘗試從取用方案上的 *local.settings.json* 或 *appsettings.{environment}.json* 等檔案讀取值。 從這些與觸發程式連線相關之檔案中讀取的值，無法在應用程式調整時提供，因為在調整控制器建立應用程式的新實例時，裝載基礎結構無法存取設定資訊。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

- [如何監視函數應用程式](functions-monitoring.md)
- [函式的最佳做法](functions-best-practices.md)
