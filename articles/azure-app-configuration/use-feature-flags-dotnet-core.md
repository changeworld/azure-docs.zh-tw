---
title: 如何在 .NET Core 應用程式中使用功能旗標的教學課程 | Microsoft Docs
description: 在本教學課程中，您將了解如何在 .NET Core 應用程式中實作功能旗標。
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 6da2aa645549920cce2f5c0cfe8a32c98dc04708
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746135"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>教學課程：在 ASP.NET Core 應用程式中使用功能旗標

.NET Core 功能管理程式庫可讓您在 .NET 或 ASP.NET Core 應用程式中以慣用方式實作功能旗標。 這些程式庫可讓您以宣告方式在程式碼中新增功能旗標，因此您不必以手動方式為功能旗標撰寫所有 `if` 陳述式。

功能管理程式庫也可在幕後管理功能旗標的生命週期。 例如，這些程式庫會重新整理及快取旗標狀態，或保證旗標的狀態在要求呼叫期間不會變化。 此外，ASP.NET Core 程式庫還提供現成可用的整合，包括 MVC 控制器動作、檢視、路由和中介軟體。

[將功能旗標新增至 ASP.NET Core 應用程式快速入門](./quickstart-feature-flag-aspnet-core.md)會示範數種方法，讓您了解如何在 ASP.NET Core 應用程式中新增功能旗標。 本教學課程會詳細說明這些方法。 如需完整的參考，請參閱 [ASP.NET Core 功能管理文件](/dotnet/api/microsoft.featuremanagement)。

在本教學課程中，您將學會如何：

> [!div class="checklist"]
> * 在應用程式的重要部分新增功能旗標來控制功能的可用性。
> * 在您使用應用程式組態來管理功能旗標時與其進行整合。

## <a name="set-up-feature-management"></a>設定功能管理

新增 `Microsoft.FeatureManagement.AspNetCore` 和 `Microsoft.FeatureManagement` NuGet 套件的參考，以利用 .NET Core 功能管理員。
    
.NET Core 功能管理員 `IFeatureManager` 會從架構的原生組態系統取得功能旗標。 因此，您可以使用 .NET Core 所支援的組態來源 (包括本機 appsettings.json  檔或環境變數)，來定義應用程式的功能旗標。 `IFeatureManager` 會仰賴 .NET Core 相依性插入。 您可以使用標準慣例來註冊功能管理服務：

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

根據預設，功能管理員會從 .NET Core 組態資料的 `"FeatureManagement"` 區段擷取功能旗標。 下列範例會指示功能管理員改為讀取名為 `"MyFeatureFlags"` 的不同區段：

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

如果您在功能旗標中使用篩選條件，則需要納入其他程式庫並予以註冊。 下列範例說明如何使用名為 `PercentageFilter` 的內建功能篩選條件：

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

建議您將功能旗標置於應用程式外，並個別加以管理。 這樣做可讓您隨時修改旗標狀態，並讓那些變更在應用程式中立即生效。 應用程式組態可讓您透過專用的入口網站 UI 在集中的位置組織和控制所有功能旗標。 應用程式組態也可透過其 .NET Core 用戶端程式庫，直接將旗標傳遞到您的應用程式。

若要將 ASP.NET Core 應用程式連線至應用程式組態，最簡單的方式是透過組態提供者 `Microsoft.Azure.AppConfiguration.AspNetCore`。 請依照下列步驟來使用此 NuGet 套件。

1. 開啟 *Program.cs* 檔案，並新增下列程式碼。

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. 開啟 *Startup.cs* 並更新 `Configure` 方法，以新增名為 `UseAzureAppConfiguration` 的內建中介軟體。 此中介軟體可讓功能旗標值依週期性間隔重新整理，同時讓 ASP.NET Core Web 應用程式繼續接收要求。

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

功能旗標值應該會隨著時間而變更。 根據預設，功能旗標值的快取期間為 30 秒，因此在中介軟體接收要求時觸發的重新整理作業，必須要到快取的值過期後才會更新值。 下列程式碼說明如何透過 `options.UseFeatureFlags()` 呼叫將快取到期時間或輪詢間隔變更為 5分鐘。

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>功能旗標宣告

每個功能旗標都有兩個部分：名稱以及一份清單，此清單內含可用來評估功能狀態是否為「開啟」  (也就是當其值為 `True` 時) 的一或多個篩選條件。 篩選條件會定義應開啟功能的使用案例。

如果功能旗標有多個篩選條件，則會依序周遊篩選條件清單，直到其中一個篩選條件確定應啟用功能。 屆時，功能旗標即會「開啟」  ，並略過剩餘的篩選結果。 如果沒有篩選條件指出應啟用功能，則功能旗標會「關閉」  。

功能管理員支援以 appsettings.json  作為功能旗標的組態來源。 下列範例說明如何在 JSON 檔案中設定功能旗標：

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

依照慣例，我們會將此 JSON 文件的 `FeatureManagement` 區段用於功能旗標設定。 上述範例顯示了三個功能旗標，及其在 `EnabledFor` 屬性中定義的篩選條件：

* `FeatureA` 為「開啟」  。
* `FeatureB` 為「關閉」  。
* `FeatureC` 會使用 `Parameters` 屬性指定名為 `Percentage` 的篩選條件。 `Percentage` 是可設定的篩選條件。 在此範例中，`Percentage` 會指定 `FeatureC` 旗標有 50% 的機率會「開啟」  。

## <a name="feature-flag-references"></a>功能旗標參考

為了能夠輕鬆參考程式碼中的功能旗標，您應將其定義為 `enum` 變數：

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>功能旗標檢查

功能管理的基本模式是先檢查功能旗標是否設定為「開啟」  。 如果是，則功能管理員會功能所包含的動作。 例如：

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>相依性插入

在 ASP.NET Core MVC 中，您可以透過相依性插入來存取功能管理員 `IFeatureManager`：

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>控制器動作

在 MVC 控制器中，您可以使用 `FeatureGate` 屬性來控制是要啟用整個控制器類別還是特定動作。 下列 `HomeController` 控制器必須在 `FeatureA`「開啟」  時，才能執行該控制器類別所包含的動作：

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

下列 `Index` 動作必須在 `FeatureA`「開啟」  時才能執行：

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

當 MVC 控制器或動作因為控制功能旗標「關閉」  而遭到封鎖時，系統便會呼叫已註冊的 `IDisabledFeaturesHandler` 介面。 預設的 `IDisabledFeaturesHandler` 介面會對用戶端傳回沒有回應本文的 404 狀態碼。

## <a name="mvc-views"></a>MVC 檢視

在 *Views* 目錄中開啟 _ViewImports.cshtml  ，並新增功能管理員標記協助程式：

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

在 MVC 檢視中，您可以使用 `<feature>` 標記，根據功能旗標是否已啟用來呈現內容：

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

若要在不符合需求時顯示替代內容，可以使用 `negate` 屬性。

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

如果清單中的任何功能或所有功能都啟用，功能 `<feature>` 標記也可以用來顯示內容。

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC 篩選條件

您可以將 MVC 篩選條件設定為根據功能旗標的狀態來啟用。 下列程式碼會新增名為 `SomeMvcFilter` 的 MVC 篩選條件。 只有在 `FeatureA` 已啟用時，才會在 MVC 管線內觸發此篩選條件。 這項功能受限於 `IAsyncActionFilter`。 

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="middleware"></a>中介軟體

您也可以使用功能旗標，根據條件新增應用程式分支和中介軟體。 下列程式碼只有在 `FeatureA` 啟用時，才會在要求管線中插入中介軟體元件：

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

此程式碼會建置更泛型的功能，以根據功能旗標為整個應用程式建立分支：

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何藉由使用 `Microsoft.FeatureManagement` 程式庫在 ASP.NET Core 應用程式中實作功能旗標。 若要進一步了解 ASP.NET Core 和應用程式組態中的功能管理支援，請參閱下列資源：

* [ASP.NET Core 功能旗標程式碼範例](./quickstart-feature-flag-aspnet-core.md)
* [Microsoft.FeatureManagement 文件](/dotnet/api/microsoft.featuremanagement)
* [編輯功能旗標](./manage-feature-flags.md)