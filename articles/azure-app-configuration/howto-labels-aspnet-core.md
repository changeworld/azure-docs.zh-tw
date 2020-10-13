---
title: 使用每個環境的設定
titleSuffix: Azure App Configuration
description: 使用標籤來提供每個環境的設定值。
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: 689fdbd444aa4b20f5bef225faa259788c47cf9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206664"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>使用標籤來啟用不同環境的設定

許多應用程式都需要針對不同的環境使用不同的設定。 假設應用程式具有設定值，可定義要用於其後端資料庫的連接字串。 應用程式開發人員會使用在生產環境中使用的不同資料庫。 應用程式使用的資料庫連接字串必須隨著應用程式從開發移至生產環境而變更。

在 Azure 應用程式組態中，您可以使用 *標籤* 來為相同的索引鍵定義不同的值。 例如，您可以使用不同的值來定義開發和生產環境的單一索引鍵。 您可以指定連接至應用程式設定時要載入的標籤。

為了示範這項功能，您將修改在快速入門中建立的 web 應用程式 [：使用 Azure 應用程式組態建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md) ，以針對開發與生產環境使用不同的設定設定。 先完成快速入門，再繼續進行。

## <a name="specify-a-label-when-adding-a-configuration-value"></a>新增設定值時指定標籤

在 Azure 入口網站中，移至 [ **Configuration Explorer** ]，並尋找您在快速入門中建立的 *TestApp： Settings： FontColor* 金鑰。 選取其內容功能表，然後選取 [ **加入值**]。

> [!div class="mx-imgBorder"]
> ![新增值功能表項目](media/labels-add-value.png)

在 [**加入值**] 畫面上，輸入 [**紅色**] 和 [**開發**]**標籤**的**值**。 將 [ **內容類型** ] 保留空白。 選取 [套用]。

## <a name="load-configuration-values-with-a-specified-label"></a>使用指定的標籤載入設定值

根據預設，Azure 應用程式組態只會載入沒有標籤的設定值。 如果您已針對設定值定義標籤，您會想要指定要在連線至應用程式設定時使用的標籤。

在上一節中，您為開發環境建立不同的設定值。 您可以使用 `HostingEnvironment.EnvironmentName` 變數來動態判斷應用程式目前執行所在的環境。 若要深入瞭解，請參閱 [在 ASP.NET Core 中使用多個環境](/aspnet/core/fundamentals/environments)。

藉由將環境名稱傳遞至方法，以對應至目前環境的標籤載入設定值 `Select` ：

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
> 上述程式碼片段會從名為的環境變數載入應用程式設定連接字串 `AppConfigConnectionString` 。 請確定已正確設定此環境變數。

呼叫 `Select` 方法兩次。 第一次，它會載入沒有標籤的設定值。 然後，它會使用對應至目前環境的標籤來載入設定值。 這些環境特定的值會覆寫任何沒有標籤的對應值。 您不需要為每個索引鍵定義環境特定的值。 如果索引鍵沒有具有對應至目前環境之標籤的值，則會使用沒有標籤的值。

## <a name="test-in-different-environments"></a>在不同的環境中測試

開啟 `launchSettings.json` 目錄下的檔案 `Properties` 。 尋找底下的 `config` 專案 `profiles` 。 在 `environmentVariables` 區段中，將 `ASPNETCORE_ENVIRONMENT` 變數設為 `Production` 。

設定新值之後，請建立並執行您的應用程式。

```dotnetcli
dotnet build
dotnet run
```

使用網頁瀏覽器移至 `http://localhost:5000` 。 您會發現字型色彩是黑色。

![使用生產環境設定執行的 Web 應用程式](media/labels-website-prod.png)

更新 `launchSettings.json` 以將 `ASPNETCORE_ENVIRONMENT` 變數設定為 `Development` 。 再次執行 `dotnet run`。 

您會發現字型色彩現在是紅色的。 這是因為應用程式現在會使用 `TestApp:Settings:FontColor` 具有標籤的值 `Development` 。 所有其他設定值會維持與其生產值相同。

![使用開發設定執行的 Web 應用程式](media/labels-website-dev.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [ASP.NET Core 的設定](/aspnet/core/fundamentals/configuration/)
