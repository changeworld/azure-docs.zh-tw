---
title: Azure 應用程式設定搭配 ASP.NET Core 的快速入門 | Microsoft Docs
description: 使用 Azure 應用程式組態建立 ASP.NET Core 應用程式，集中儲存和管理 ASP.NET Core 應用程式的應用程式設定。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperfq1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5fd042b91ede91491590a53abf4dec552fbf6487
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440426"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>快速入門：使用 Azure 應用程式設定建立 ASP.NET Core 應用程式

在本快速入門中，您將使用 Azure 應用程式組態，集中儲存和管理 ASP.NET Core 應用程式的應用程式設定。 ASP.NET Core 會使用應用程式所指定一或多個資料來源中的設定，來建置一個以索引鍵/值為基礎的組態物件。 這些資料來源稱為「設定提供者」  。 因為應用程式組態的 .NET Core 用戶端會實作為組態提供者，服務看來就像其他資料來源。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的命令列指令。 其中已預先安裝常用的 Azure 工具，包括 .NET Core SDK。 如果您已登入 Azure 訂用帳戶，請從 shell.azure.com 啟動您的 [Azure Cloud Shell](https://shell.azure.com)。 您可以[閱讀我們的文件](../cloud-shell/overview.md)，以深入了解 Azure Cloud Shell

## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. 選取 [作業] > [組態總管] > [建立] > [索引鍵/值] 以新增下列索引鍵/值組：

    | Key                                | 值                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Azure 應用程式設定的值* |

    目前先讓 [標籤] 和 [內容類型] 保持空白。 選取 [套用]  。

## <a name="create-an-aspnet-core-web-app"></a>建立 ASP.NET Core Web 應用程式

使用 [.NET Core 命令列介面 (CLI)](/dotnet/core/tools) 建立新的 ASP.NET Core MVC 專案。 [Azure Cloud Shell](https://shell.azure.com) 會為您提供這些工具。 也可以跨 Windows、macOS 及 Linux 平台使用這些工具。

執行下列命令，在新的 *TestAppConfig* 資料夾中建立 ASP.NET Core MVC Web 專案：

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>連線至應用程式組態存放區

1. 執行下列命令，以新增 [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) NuGet 套件參考：

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. 在與 *.csproj* 檔案相同的目錄中執行下列命令。 此命令會使用秘密管理員來儲存名為 `ConnectionStrings:AppConfig` 的密碼，其中儲存應用程式組態存放區的連接字串。 將 `<your_connection_string>` 預留位置取代為應用程式組態存放區的連接字串。 您可以在 Azure 入口網站中的 **[存取金鑰]** 下找到連接字串。

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > 除非以引號括住連接字串，否則某些殼層會截斷連接字串。 請確定 `dotnet user-secrets` 命令的輸出會顯示整個連接字串。 如果不是，請重新執行命令，並以引號括住連接字串。

    祕密管理員僅可用於在本機測試 Web 應用程式。 當應用程式部署至 [Azure App Service](https://azure.microsoft.com/services/app-service/web) 時，請使用 App Service 中的**連接字串**應用程式設定 (而不是秘密管理員) 來儲存連接字串。

    使用 .NET Core 組態 API 來存取此秘密。 在所有支援的平台上，組態 API 的組態名稱中都適用冒號 (`:`)。 如需詳細資訊，請參閱[組態金鑰和值](/aspnet/core/fundamentals/configuration#configuration-keys-and-values)。

1. 在 Program.cs 中，將參考新增至 .NET Core 組態 API 命名空間：

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. 藉由呼叫 `AddAzureAppConfiguration` 方法將 `CreateWebHostBuilder` 方法更新為使用應用程式設定。

    > [!IMPORTANT]
    > `CreateHostBuilder` 會取代 .NET Core 3.x 中的 `CreateWebHostBuilder`。 根據您的環境選取正確的語法。

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    透過先前的變更，[應用程式組態的組態提供者](https://go.microsoft.com/fwlink/?linkid=2074664)已向 .NET Core 組態 API 註冊。

## <a name="read-from-the-app-configuration-store"></a>從應用程式組態存放區讀取

請完成下列步驟，以讀取和顯示在應用程式組態存放區中儲存的值。 .NET Core 組態 API 將用來存取存放區。 Razor 語法將用來顯示金鑰的值。

開啟 *\<app root>/Views/Home/Index.cshtml*，並將其內容取代為下列程式碼：

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<style>
    body {
        background-color: @Configuration["TestApp:Settings:BackgroundColor"]
    }
    h1 {
        color: @Configuration["TestApp:Settings:FontColor"];
        font-size: @Configuration["TestApp:Settings:FontSize"]px;
    }
</style>

<h1>@Configuration["TestApp:Settings:Message"]</h1>
```

在上述程式碼中，會使用應用程式組態存放區的金鑰，如下所示：

* `TestApp:Settings:BackgroundColor` 金鑰的值會指派給 CSS `background-color` 屬性。
* `TestApp:Settings:FontColor` 金鑰的值會指派給 CSS `color` 屬性。
* `TestApp:Settings:FontSize` 金鑰的值會指派給 CSS `font-size` 屬性。
* `TestApp:Settings:Message` 金鑰的值會顯示為標題。

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 若要使用 .NET Core CLI 來建置應用程式，請瀏覽至專案的根目錄。 在命令殼層中執行下列命令：

    ```dotnetcli
    dotnet build
    ```

1. 成功完成建置後，請執行下列命令以在本機執行 Web 應用程式：

    ```dotnetcli
    dotnet run
    ```

1. 如果您是在本機電腦上工作，請使用瀏覽器瀏覽至 `http://localhost:5000`。 此位址是本機裝載 Web 應用程式的預設 URL。 如果您在 Azure Cloud Shell 中工作，請選取 [Web 預覽]  按鈕，然後選取 [設定]  。

    ![尋找 Web 預覽按鈕](./media/quickstarts/cloud-shell-web-preview.png)

    系統提示您設定預覽的連接埠時，請輸入 *5000*，並選取 [開啟並瀏覽]。 網頁會顯示「Azure 應用程式組態中的值」。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已：

* 已佈建新的應用程式組態存放區。
* 已註冊應用程式組態存放區的 .NET Core 組態提供者。
* 已使用組態提供者讀取應用程式組態存放區的金鑰。
* 使用 Razor 語法顯示應用程式組態存放區的金鑰值。

若要了解如何將 ASP.NET Core 應用程式設定為以動態方式重新整理組態設定，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [啟用動態組態](./enable-dynamic-configuration-aspnet-core.md)
