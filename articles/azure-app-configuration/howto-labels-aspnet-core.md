---
title: 使用每個環境的設定
titleSuffix: Azure App Configuration
description: 使用標籤來提供每個環境的設定值。
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: 465ae86c5732c9dd54ade1b7096fa8415dfca513
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83118554"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>使用標籤來啟用不同環境的設定

許多應用程式都需要針對不同的環境使用不同的設定。 假設應用程式具有設定值，可定義要用於其後端資料庫的連接字串。 應用程式開發人員使用的資料庫與生產環境中所使用的不同。 應用程式使用的資料庫連接字串必須在應用程式從開發移至生產環境時變更。

在 Azure 應用程式組態中，您可以使用*標籤*來定義相同索引鍵的不同值。 例如，您可以針對開發和生產環境定義具有不同值的單一索引鍵。 您可以指定連接到應用程式組態時要載入的標籤。

為了示範這項功能，您將修改在[快速入門：使用 Azure 應用程式組態建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)中建立的 web 應用程式，以將不同的設定用於開發與生產環境。 繼續進行之前，請先完成快速入門。

## <a name="specify-a-label-when-adding-a-configuration-value"></a>新增設定值時指定標籤

在 Azure 入口網站中，移至 [ **Configuration Explorer** ]，並尋找您在快速入門中建立的*TestApp： Settings： FontColor*金鑰。 選取其內容功能表，然後選取 [**新增值**]。

> [!div class="mx-imgBorder"]
> ![[加入值] 功能表項目](media/labels-add-value.png)

在 [**加入值**] 畫面上，輸入**紅色**的**值**和 [**開發**]**標籤**。 將 [**內容類型**] 保留空白。 選取 [套用]。

## <a name="load-configuration-values-with-a-specified-label"></a>使用指定的標籤載入設定值

根據預設，Azure 應用程式組態只會載入沒有標籤的設定值。 如果您已定義設定值的標籤，您會想要指定連接到應用程式組態時要使用的標籤。

在上一節中，您為開發環境建立了不同的設定值。 您可以使用 `HostingEnvironment.EnvironmentName` 變數，以動態方式判斷應用程式目前執行的環境。 若要深入瞭解，請參閱[在 ASP.NET Core 中使用多個環境](/aspnet/core/fundamentals/environments)。

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
> 上述程式碼片段會從名為的環境變數載入應用程式組態連接字串 `AppConfigConnectionString` 。 請確定已正確設定此環境變數。

呼叫 `Select` 方法兩次。 第一次，它會載入不含標籤的設定值。 然後，它會使用對應至目前環境的標籤來載入設定值。 這些環境特定的值會覆寫任何不含標籤的對應值。 您不需要為每個索引鍵定義環境特定的值。 如果索引鍵沒有對應至目前環境之標籤的值，它會使用不含標籤的值。

## <a name="test-in-different-environments"></a>在不同環境中測試

開啟目錄底下的檔案 `launchSettings.json` `Properties` 。 尋找底下的 `config` 專案 `profiles` 。 在 `environmentVariables` 區段中，將 `ASPNETCORE_ENVIRONMENT` 變數設定為 `Production` 。

設定新的值後，建立並執行您的應用程式。

```dotnetcli
dotnet build
dotnet run
```

使用網頁瀏覽器移至 `http://localhost:5000` 。 您會發現字型色彩是黑色。

![使用生產環境設定執行的 Web 應用程式](media/labels-website-prod.png)

更新 `launchSettings.json` 以將 `ASPNETCORE_ENVIRONMENT` 變數設定為 `Development` 。 再次執行 `dotnet run`。 

您會發現字型色彩現在是紅色。 這是因為應用程式現在會使用 `TestApp:Settings:FontColor` 具有標籤的值 `Development` 。 所有其他設定值仍與生產環境值相同。

![使用開發設定執行的 Web 應用程式](media/labels-website-dev.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [ASP.NET Core 的設定](/aspnet/core/fundamentals/configuration/)
