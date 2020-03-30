---
title: 使用每個環境配置
titleSuffix: Azure App Configuration
description: 使用標籤提供每個環境配置值
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056809"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>使用標籤為不同的環境啟用不同的配置

許多應用程式需要為不同的環境使用不同的配置。 假設應用程式具有定義要用於其後端資料庫的連接字串的配置值。 應用程式的開發人員使用與生產中使用的資料庫不同的資料庫。 應用程式使用的資料庫連接字串必須隨著應用程式從開發移動到生產而更改。

在 Azure 應用配置中，可以使用*標籤*為同一鍵定義不同的值。 例如，您可以定義具有不同值的*單個鍵，用於開發和**生產*。 您可以指定在連接到應用配置時要載入的標籤。

為了演示此功能，我們將修改在"快速入門"中創建的 Web 應用[：使用 Azure 應用配置創建ASP.NET核心應用](./quickstart-aspnet-core-app.md)，以便使用不同的配置設置進行開發與生產。 請先完成快速入門，然後再繼續。

## <a name="specify-a-label-when-adding-a-configuration-value"></a>添加配置值時指定標籤

在 Azure 門戶中，進入**配置資源管理器**並找到您在快速入門中創建的*TestApp：設置：FontColor*金鑰。 選擇其內容功能表，然後按一下"**添加值**"。

> [!div class="mx-imgBorder"]
> ![添加值功能表項目](media/labels-add-value.png)

在 **"增值"** 螢幕上，輸入紅色**值**和**red****開發****標籤**。 將**內容類型**留空。 選取 [**套用**]。

## <a name="loading-configuration-values-with-a-specified-label"></a>使用指定標籤載入配置值

預設情況下，Azure 應用配置僅載入沒有標籤的配置值。 如果已為配置值定義標籤，則需要指定連接到應用配置時要使用的標籤。

在最後一節中，您為*開發*環境創建了不同的配置值。 使用變數`HostingEnvironment.EnvironmentName`動態確定應用當前在哪個環境中運行的環境。 要瞭解更多資訊，請參閱[在 ASP.NET 酷中使用多個環境](/aspnet/core/fundamentals/environments)。

通過將環境名稱傳遞到方法中，載入具有與當前環境對應的標籤的`Select`配置值：

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> 上述程式碼片段從稱為`AppConfigConnectionString`的環境變數載入應用配置連接字串。 請確保此環境變數設置正確。

呼叫 `Select` 方法兩次。 第一次，它載入配置值，沒有標籤。 然後，它載入配置值與與當前環境對應的標籤。 這些特定于環境的值將覆蓋任何沒有標籤的相應值。 不需要為每個鍵定義特定于環境的值。 如果鍵沒有與當前環境對應的標籤的值，則使用沒有標籤的值。

## <a name="testing-in-different-environments"></a>在不同環境中進行測試

要測試不同的配置值，`launchSettings.json`請打開`Properties`目錄下的檔。 找到`config`下的`profiles`條目。 在`environmentVariables`節中，將`ASPNETCORE_ENVIRONMENT`變數設置為`Production`。

設置新值後，生成並運行應用程式。

```dotnetcli
dotnet build
dotnet run
```

使用 Web 瀏覽器導航到`http://localhost:5000`。 您會注意到字體顏色為黑色。

![使用生產配置運行的 Web 應用程式](media/labels-website-prod.png)

現在更新`launchSettings.json`以將`ASPNETCORE_ENVIRONMENT`變數設置為`Development`。 再次執行 `dotnet run`。 您會注意到字體顏色現在是紅色的。 這是因為應用程式現在使用具有`TestApp:Settings:FontColor``Development`標籤的值。 所有其他配置值與其生產值保持不變。

![使用開發配置運行的 Web 應用程式](media/labels-website-dev.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [ASP.NET Core 的設定](/aspnet/core/fundamentals/configuration/)
