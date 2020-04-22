---
title: 在 .NET Azure Functions 中使用相依性插入
description: 瞭解如何使用相依項注入在 .NET 函數中註冊和使用服務
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678448"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET Azure Functions 中使用相依性插入

Azure 函數支援依賴項注入 (DI) 軟體設計模式,這是一種在類及其依賴項之間實現[控制反轉 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)的技術。

- Azure 函數中的依賴項注入基於 .NET 核心依賴項注入功能構建。 建議熟悉[.NET 核心依賴項注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。 There are differences in how you override dependencies and how configuration values are read with Azure Functions on the Consumption plan.

- 對依賴項注入的支援從 Azure 函數 2.x 開始。

## <a name="prerequisites"></a>Prerequisites

使用相依項的設定之前,必須安裝以下 NuGet 套件:

- [微軟.Azure.功能.擴展](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.功能包](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)版本 1.0.28 或更高版本

## <a name="register-services"></a>註冊伺服器

要註冊服務,請創建一個方法來配置元件並將其添加到`IFunctionsHostBuilder`實例中。  Azure 函數主機創建`IFunctionsHostBuilder`的 實例,並將其直接傳遞到方法中。

要註冊方法,請添加指定`FunctionsStartup`啟動期間使用的類型名稱的程式集屬性。

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

運行時處理啟動類之前和之後運行的一系列註冊步驟。 因此,請記住以下事項:

- *啟動類僅用於設置和註冊。* 避免使用在啟動過程中在啟動時註冊的服務。 例如,不要嘗試在啟動期間正在註冊的記錄器中記錄消息。 註冊過程的這一點為時過早,您的服務無法使用。 運行`Configure`方法后,函數運行時將繼續註冊其他依賴項,這可能會影響服務的操作方式。

- *相依項檔檔檔檔寫入的類別的類別*。 作為可注入類型提供的唯一服務是`Configure`方法中的設置。 因此,特定於函數的類型(如`BindingContext`和`ExecutionContext`在設置期間不可用)或作為可注入類型提供。

## <a name="use-injected-dependencies"></a>使用註入的相依項

建構函數注入用於使依賴項在函數中可用。 使用構造函數注入需要不使用靜態類。

下面的範例展示如何將`IMyService``HttpClient`和依賴項注入到 HTTP 觸發的函數中。 此示例使用在啟動時註冊`HttpClient`的[Microsoft.擴展.HTTP](https://www.nuget.org/packages/Microsoft.Extensions.Http/)包。

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

Azure 函數應用提供與[依賴項注入相同的](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)服務存留期ASP.NET。 對於函數應用,不同的服務存留期如下:

- **瞬態**:根據服務的每個請求創建瞬態服務。
- **作用域**: 作用域服務存留期與函數執行存留期匹配。 每次執行創建一次作用域服務。 稍後在執行期間請求該服務將重用現有服務實例。
- **Singleton**: singleton 服務存留期與主機存留期匹配,並可跨該實例上的函數執行重用。 建議為連接和用戶端(例如`SqlConnection`或`HttpClient`實例)提供 Singleton 存留期服務。

在 GitHub 上查看或下載[不同服務存留期的範例](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>記錄記錄服務

如果需要自己的日誌記錄提供者,請將自定義類型註冊為`ILoggerProvider`實例。 應用程式見解由 Azure 函數自動添加。

> [!WARNING]
> - 不要`AddApplicationInsightsTelemetry()`添加到服務集合,因為它註冊的服務與環境提供的服務衝突。
> - 不要註冊您自己的`TelemetryConfiguration`應用程式,`TelemetryClient`或者如果您正在使用內置的應用程式見解功能。 如果需要配置自己的`TelemetryClient`實例,請通過`TelemetryConfiguration`注入 的實例創建一個實例,如監視器 Azure[函數](./functions-monitoring.md#version-2x-and-later-2)所示。

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T>和 ILoggerFactory

主機將注入`ILogger<T>`和`ILoggerFactory`服務到建構函數中。  但是,默認情況下,這些新日誌記錄篩選器將從函數日誌中篩選出來。  您需要修改`host.json`檔案才能選擇加入其他篩選器和類別。  下面的範例演示添加主機將`ILogger<HttpTrigger>`公開的日誌。

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

以及添加`host.json`日誌篩選器的檔。

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

## <a name="function-app-provided-services"></a>功能應用提供的服務

函數主機註冊許多服務。 以下服務可以安全地作為應用程式中的依賴項:

|服務類型|存留期|描述|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|單一|執行時設定|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|單一|負責主機實體的識別碼|

如果還有其他服務要依賴,[請建立問題, 並在 GitHub 上提出它們](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>覆寫主機服務

主機提供的重寫服務當前不受支援。  如果有要重寫的服務,[請建立問題, 並在 GitHub 上提出它們](https://github.com/azure/azure-functions-host)。

## <a name="working-with-options-and-settings"></a>使用選項與設定

[在應用設定](./functions-how-to-use-azure-function-app-settings.md#settings)中定義的值`IConfiguration`在 實體中可用,這允許您讀取啟動類中的應用設定值。

可以從實例中提取`IConfiguration`值到自定義類型中。 將應用設置值複製到自定義類型,使這些值易於注入,從而輕鬆測試服務。 讀取到配置實例中的設置必須是簡單的鍵/值對。

請考慮以下類,該類包含名為與應用設置一致的屬性:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

`local.settings.json`檔案可能按照以下方式建構自訂設定:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

在方法內部`Startup.Configure`,可以使用以下代碼將`IConfiguration`實體中的值提取到自訂類型中:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

呼叫`Bind`具有匹配屬性名稱的值從設定複製到自定義實例中。 選項實例現在在 IoC 容器中可用,可以注入到函數中。

選項物件作為泛型`IOptions`介面的實例注入函數。 使用`Value`屬性存取配置中找到的值。

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

有關使用[選項的詳細資訊,請參閱 ASP.NET 的選項模式](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

> [!WARNING]
> 避免試著從*本地.settings.json*或應用程式設定等檔案讀取值 *。環境*.json*在消費計劃上。 從這些檔中讀取的與觸發器連接相關的值在應用縮放時不可用,因為託管基礎結構無法存取配置資訊。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

- [如何監控函式應用程式](functions-monitoring.md)
- [職能最佳實踐](functions-best-practices.md)
