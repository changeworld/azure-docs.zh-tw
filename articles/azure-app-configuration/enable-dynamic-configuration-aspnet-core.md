---
title: 教學課程：在 ASP.NET Core 中使用應用程式組態的動態組態
titleSuffix: Azure App Configuration
description: 在本教學課程，您將了解如何以動態方式更新 ASP.NET Core 應用程式的設定資料
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
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 217c564a6bdb340ec15262c1eaf54a75bbffc833
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585010"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>教學課程：在 ASP.NET Core 應用程式中使用動態設定

ASP.NET Core 具有插入式設定系統，可從各種來源讀取設定資料。 其可以動態地處理變更，而不會造成應用程式重新啟動。 ASP.NET Core 支援將組態設定繫結至強型別 .NET 類別。 它會使用各種 `IOptions<T>` 模式，將它們插入您的程式碼中。 這些模式其中之一 (具體而言即 `IOptionsSnapshot<T>`) 會在基礎資料變更時，自動重新載入應用程式的設定。 您可以將 `IOptionsSnapshot<T>` 插入應用程式中的控制器，以存取儲存在 Azure 應用程式設定中的最新設定。

您也可以設定應用程式組態 ASP.NET Core 用戶端程式庫，以使用中介軟體動態重新整理一組組態設定。 每當 Web 應用程式收到要求，組態設定就會透過設定存放區來更新。

應用程式組態會自動快取每個設定，以避免呼叫太多設定存放區。 重新整理作業會等到設定的快取值過期，才會更新該設定，即使其值在設定存放區中有所變更也一樣。 預設讀快取到期時間為 30 秒。 如有需要，您可以覆寫此到期時間。

本教學課程會示範如何在程式碼中實作動態設定更新。 本文會以快速入門中介紹的 Web 應用程式作為基礎。 繼續進行之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

您可以使用任何程式碼編輯器來進行本教學課程中的步驟。 Windows、macOS 及 Linux 平台上都有提供的 [Visual Studio Code](https://code.visualstudio.com/) 是一個絕佳的選項。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定您的應用程式，使其在應用程式組態存放區發生變更時更新其組態。
> * 在您應用程式的控制器中插入最新的設定。

## <a name="prerequisites"></a>Prerequisites

若要進行本教學課程，請安裝 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

繼續進行之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

## <a name="add-a-sentinel-key"></a>新增 Sentinel 金鑰

Sentinel 金鑰  是用來在設定變更時發出信號的特殊金鑰。 您的應用程式會監視 Sentinel 金鑰是否有變更。 偵測到變更時，您會重新整理所有設定值。 相較於監視所有金鑰的變更，此方法可減少您應用程式對應用程式組態所提出的要求總數。

1. 在 Azure 入口網站中，選取 [組態總管] > [建立] > [金鑰/值]  。

1. 針對 [金鑰]  ，請輸入 TestApp:Settings:Sentinel  。 針對 [值]  ，輸入 1。 請將 [標籤]  和 [內容類型]  保留空白。

1. 選取 [套用]  。

    > [!NOTE]
    > 如果您並非使用 Sentinel 金鑰，將需要手動註冊想要監看的每個金鑰。

## <a name="reload-data-from-app-configuration"></a>從應用程式設定重新載入資料

1. 透過執行下列命令，將參考新增至 `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet 套件：

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. 開啟 Program.cs  ，然後更新 `CreateWebHostBuilder` 方法以新增 `config.AddAzureAppConfiguration()` 方法。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    `ConfigureRefresh` 方法可用來指定相關設定，以用來在系統觸發重新整理作業時，使用應用程式組態存放區來更新組態資料。 `Register` 方法的 `refreshAll` 參數表示當 Sentinel 金鑰變更時，應該重新整理所有設定值。

    此外，`SetCacheExpiration` 方法會覆寫 30 秒的預設快取到期時間，並改為指定 5 分鐘。 這會減少對應用程式組態提出的要求數目。

    > [!NOTE]
    > 基於測試目的，您可以縮短快取到期時間。

    若要實際觸發重新整理作業，您必須設定重新整理中介軟體，才能讓應用程式在發生任何變更時，重新整理組態資料。 您會在稍後的步驟中了解如何執行此動作。

2. 新增 Settings.cs  檔案，以定義及實作新的 `Settings` 類別。

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. 開啟 Startup.cs  ，然後在 `ConfigureServices` 方法中使用 `IServiceCollection.Configure<T>`，以將組態資料繫結至 `Settings` 類別。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

    > [!TIP]
    > 若要深入了解讀取組態值時的選項模式，請參閱  [ASP.NET Core 中的選項模式](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1)。

4. 更新 `Configure` 方法以新增 `UseAzureAppConfiguration` 中介軟體，讓已註冊要重新整理的組態設定可以進行更新，同時讓 ASP.NET Core Web 應用程式繼續接收要求。


    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    中介軟體會使用 `Program.cs` 內的 `AddAzureAppConfiguration` 方法所指定的重新整理組態，來針對 ASP.NET Core Web 應用程式所收到的每個要求觸發重新整理。 每個要求都會觸發一個重新整理作業，而且用戶端程式庫會檢查已註冊的組態設定是否有已過期的快取值。 如果已過期，則會重新整理。

## <a name="use-the-latest-configuration-data"></a>使用最新的設定資料

1. 在控制器目錄中開啟 HomeController.cs  ，並將參考新增至 `Microsoft.Extensions.Options` 套件。

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. 將 `HomeController` 類別更新為透過相依性插入來接收 `Settings`，並使用其值。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. 開啟 [Views] > [Home] 目錄中的 Index.cshtml  ，然後以下列程式碼取代其內容：

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 若要使用 .NET Core CLI 來建置應用程式，請在命令殼層中執行下列命令：

```console
        dotnet build
```

1. 建置成功完成後，請執行下列命令以在本機執行 Web 應用程式：

```console
        dotnet run
```

1. 開啟瀏覽器視窗，然後移至 `dotnet run` 輸出中所顯示的 URL。

    ![在本機啟動快速入門應用程式](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [所有資源]  ，然後選取您在快速入門中建立的應用程式組態存放區執行個體。

1. 選取 [組態總管]  ，然後更新下列索引鍵的值：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:BackgroundColor | 綠色 |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Azure 應用程式設定的值 - 現在可以即時更新了！ |
    | TestApp:Settings:Sentinel | 2 |

1. 重新整理瀏覽器頁面，以查看新的組態設定。 您可能需要重新整理一次以上，才能反映變更。

    ![在本機啟動更新的快速入門應用程式](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已啟用 ASP.NET Core Web 應用程式，以動態方式從應用程式組態重新整理組態設定。 若要了解如何使用 Azure 受控服務識別來簡化對應用程式組態的存取，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
