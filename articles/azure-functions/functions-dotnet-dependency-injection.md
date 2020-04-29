---
title: 在 .NET Azure Functions 中使用相依性插入
description: 瞭解如何在 .NET 函式中使用相依性插入來註冊和使用服務
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678448"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET Azure Functions 中使用相依性插入

Azure Functions 支援相依性插入（DI）軟體設計模式，這項技術可在類別及其相依性之間達成[控制反轉（IoC）](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 。

- Azure Functions 中的相依性插入是以 .NET Core 相依性插入功能為基礎。 建議您熟悉[.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)相依性插入。 覆寫相依性的方式與使用取用方案 Azure Functions 的讀取設定值有一些差異。

- 相依性插入的支援是以 Azure Functions 2.x 開始。

## <a name="prerequisites"></a>先決條件

您必須先安裝下列 NuGet 套件，才可以使用相依性插入：

- [Microsoft. Azure。擴充功能](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- 1.0.28 或更新版本的[函數套件](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

## <a name="register-services"></a>註冊伺服器

若要註冊服務，請建立方法來設定並將元件加入`IFunctionsHostBuilder`至實例。  Azure Functions 主機會建立的實例， `IFunctionsHostBuilder`並將它直接傳遞至您的方法。

若要註冊方法，請加入`FunctionsStartup`元件屬性，以指定啟動期間所使用的型別名稱。

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

- *Startup 類別僅適用于設定和註冊。* 避免在啟動過程中使用在啟動時註冊的服務。 例如，請勿嘗試在啟動期間註冊的記錄器中記錄訊息。 註冊程式的這個點太早無法使用您的服務。 執行`Configure`方法之後，函式執行時間會繼續註冊其他相依性，這可能會影響服務的運作方式。

- 相依性*插入容器只會保存明確註冊的類型*。 唯一可作為得以插入類型的服務是在`Configure`方法中設定的。 因此，在安裝期間或作為得以插入類型`BindingContext`時`ExecutionContext` ，不會提供函式特定類型（例如和）。

## <a name="use-injected-dependencies"></a>使用插入的相依性

函式插入是用來讓您的相依性可在函式中使用。 使用「處理常式」插入時，您不需要使用靜態類別。

下列範例示範如何將`IMyService`和`HttpClient`相依性插入 HTTP 觸發的函式中。 這個範例會使用在啟動`HttpClient`時註冊所需的[Microsoft Extensions. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)套件。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
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

## <a name="service-lifetimes"></a>執行個體存留期

Azure Functions 應用程式提供與 ASP.NET 相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)相同的服務存留期。 針對函式應用程式，不同的服務存留期的行為如下所示：

- **暫時性**：暫時性服務會在每個服務要求時建立。
- 限**域**：限定範圍的服務存留期符合函式執行存留期。 限域服務會在每次執行時建立一次。 稍後在執行期間對該服務提出的要求會重複使用現有的服務實例。
- **Singleton**：單一服務存留期會符合主機存留期，並會在該實例上的函式執行重複使用。 針對連接和用戶端（例如`SqlConnection`或`HttpClient`實例），建議使用單一存留期服務。

在 GitHub 上查看或下載[不同服務存留期的範例](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>記錄服務

如果您需要自己的記錄提供者，請將自訂類型`ILoggerProvider`註冊為實例。 Application Insights 由 Azure Functions 自動新增。

> [!WARNING]
> - 請不要加入`AddApplicationInsightsTelemetry()`服務集合，因為它會註冊與環境所提供之服務衝突的服務。
> - 如果您使用內建`TelemetryConfiguration`的`TelemetryClient` Application Insights 功能，請勿註冊您自己的或。 如果您需要設定自己`TelemetryClient`的實例，請透過插入`TelemetryConfiguration`的來建立一個，如 [[監視 Azure Functions](./functions-monitoring.md#version-2x-and-later-2)] 所示。

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T>和 ILoggerFactory

主機會將`ILogger<T>`和`ILoggerFactory`服務插入至「函式」。  不過，根據預設，這些新的記錄篩選器將會篩選掉函數記錄。  您將需要修改`host.json`檔案，以加入宣告其他篩選準則和類別。  下列範例會示範如何加入`ILogger<HttpTrigger>`具有記錄檔的，而這些記錄會由主機公開。

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

`host.json`以及新增記錄篩選器的檔案。

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
|`Microsoft.Extensions.Configuration.IConfiguration`|單一|執行時間設定|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|單一|負責提供主控制項實例的識別碼|

如果您想要取得相依性的其他服務，請[在 GitHub 上建立問題並加以提議](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>覆寫主機服務

目前不支援覆寫主機所提供的服務。  如果您想要覆寫服務，請[在 GitHub 上建立問題並加以提議](https://github.com/azure/azure-functions-host)。

## <a name="working-with-options-and-settings"></a>使用選項和設定

在 [[應用程式設定](./functions-how-to-use-azure-function-app-settings.md#settings)] 中定義的`IConfiguration`值可在實例中使用，讓您可以讀取啟動類別中的應用程式設定值。

您可以將實例中的`IConfiguration`值解壓縮至自訂類型。 將應用程式設定值複製到自訂類型，可以讓這些值得以插入，輕鬆地測試您的服務。 讀入設定實例的設定必須是簡單的索引鍵/值組。

請考慮下列類別，其中包含名稱與應用程式設定一致的屬性：

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

`local.settings.json`而且可能會將自訂設定視為結構的檔案，如下所示：
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

從`Startup.Configure`方法內部，您可以使用下列程式碼， `IConfiguration`將實例中的值解壓縮至您的自訂類型：

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

呼叫`Bind`會將具有相符屬性名稱的值從設定複製到自訂實例。 Options 實例現在可用於 IoC 容器中，以插入函式中。

Options 物件會插入至函式中，做為泛型`IOptions`介面的實例。 使用`Value`屬性來存取您的設定中找到的值。

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
