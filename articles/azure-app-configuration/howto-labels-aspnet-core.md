---
title: 使用每個環境的設定
titleSuffix: Azure App Configuration
description: 使用標籤來提供每個環境的設定值
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056809"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>使用標籤為不同的環境啟用不同的設定

許多應用程式都需要針對不同的環境使用不同的設定。 假設應用程式具有設定值，可定義要用於其後端資料庫的連接字串。 應用程式的開發人員使用的資料庫與生產環境中所使用的不同。 應用程式所使用的資料庫連接字串必須在應用程式從開發移至生產環境時變更。

在 Azure 應用程式組態中，您可以使用*標籤*來定義相同索引鍵的不同值。 例如，您可以針對*開發*和*生產環境*定義具有不同值的單一索引鍵。 您可以指定連接到應用程式組態時要載入的標籤。

為了示範這項功能，我們將修改[快速入門：使用 Azure 應用程式組態建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)中建立的 web 應用程式，以針對開發與生產環境使用不同的設定。 請先完成快速入門，再繼續進行。

## <a name="specify-a-label-when-adding-a-configuration-value"></a>新增設定值時指定標籤

在 Azure 入口網站中，移至 [ **Configuration Explorer** ]，並找出您在快速入門中建立的*TestApp： Settings： FontColor*金鑰。 選取其內容功能表，然後按一下 [**新增值**]。

> [!div class="mx-imgBorder"]
> ![[加入值] 功能表項目](media/labels-add-value.png)

在 [**加入值**] 畫面上，輸入**紅色**的**值**和 [**開發**]**標籤**。 將 [**內容類型**] 保留空白。 選取 [套用]  。

## <a name="loading-configuration-values-with-a-specified-label"></a>使用指定的標籤載入設定值

根據預設，Azure 應用程式組態只會載入沒有標籤的設定值。 如果您已定義設定值的標籤，您會想要指定連接到應用程式組態時要使用的標籤。

在最後一節中，您為*開發*環境建立了不同的設定值。 您可以使用`HostingEnvironment.EnvironmentName`變數，以動態方式判斷應用程式目前正在執行的環境。 若要深入瞭解，請參閱[在 ASP.NET Core 中使用多個環境](/aspnet/core/fundamentals/environments)。

藉由將環境名稱傳遞至`Select`方法，以對應至目前環境的標籤載入設定值：

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
> 上述程式碼片段會從名`AppConfigConnectionString`為的環境變數載入應用程式組態連接字串。 請確定已正確設定此環境變數。

呼叫 `Select` 方法兩次。 第一次，它會載入不含標籤的設定值。 然後，它會使用對應至目前環境的標籤來載入設定值。 這些環境特定的值會覆寫任何不含標籤的對應值。 您不需要為每個索引鍵定義環境特定的值。 如果索引鍵沒有對應至目前環境之標籤的值，則會使用不含標籤的值。

## <a name="testing-in-different-environments"></a>在不同環境中測試

若要測試不同的設定值，請`launchSettings.json`開啟`Properties`目錄底下的檔案。 找出`config`底下的`profiles`專案。 在`environmentVariables`區段中，將`ASPNETCORE_ENVIRONMENT`變數設定為`Production`。

設定新的值後，建立並執行您的應用程式。

```dotnetcli
dotnet build
dotnet run
```

使用網頁瀏覽器流覽至`http://localhost:5000`。 您會發現字型色彩是黑色。

![使用生產環境設定執行的 Web 應用程式](media/labels-website-prod.png)

現在更新`launchSettings.json`以將`ASPNETCORE_ENVIRONMENT`變數設定為`Development`。 再次執行 `dotnet run`。 您會發現字型色彩現在是紅色。 這是因為應用程式現在會使用具有`TestApp:Settings:FontColor` `Development`標籤的值。 所有其他設定值仍與生產環境值相同。

![使用開發設定執行的 Web 應用程式](media/labels-website-dev.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [ASP.NET Core 的設定](/aspnet/core/fundamentals/configuration/)
