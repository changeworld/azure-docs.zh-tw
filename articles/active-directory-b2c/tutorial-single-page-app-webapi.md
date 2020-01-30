---
title: 教學課程：授與從單頁應用程式存取 ASP.NET Core Web API 的權限
titleSuffix: Azure AD B2C
description: 在本教學課程中，了解如何使用 Active Directory B2C 來保護 .NET Core Web API，以及如何從單頁 Node.js 應用程式呼叫該 API。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 80b2165b0ec652358a3eb8ac9d55b64f525e4690
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849838"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>教學課程：使用 Azure Active Directory B2C 授與從單頁應用程式存取 ASP.NET Core Web API 的權限

本教學課程將說明如何從單頁應用程式呼叫 Azure Active Directory B2C (Azure AD B2C) 所保護的 ASP.NET Core Web API 資源。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 設定範例以使用應用程式

## <a name="prerequisites"></a>Prerequisites

* 完成以下教學課程中的步驟和必要條件：[教學課程：使用 Azure Active Directory B2C 在單頁應用程式中啟用驗證](tutorial-single-page-app.md)。
* Visual Studio 2019 或更新版本，或 Visual Studio Code
* .NET Core 2.2 或更新版本
* Node.js

## <a name="add-a-web-api-application"></a>新增 Web API 應用程式

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>設定範圍

範圍可用來控管對受保護資源的存取。 Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，有些使用者可能同時具有讀取和寫入權限，而有些則可能只有唯讀權限。 在本教學課程中，您會定義 Web API 的讀取和寫入權限。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

在設定單頁應用程式時，請記錄 `demo.read` 範圍的 [範圍]  之下的值，以供後續步驟使用。 完整範圍值會類似於 `https://contosob2c.onmicrosoft.com/api/demo.read`。

## <a name="grant-permissions"></a>授與權限

若要從另一個應用程式呼叫受保護的 Web API，您必須為該應用程式授與對 Web API 的權限。

在必要條件教學課程中，您已建立名為 *webapp1* 的 Web 應用程式。 在本教學課程中，您會設定該應用程式，以呼叫您在上一節中建立的 Web API，即 *webapi1*。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

您的單頁 Web 應用程式會進行註冊，以呼叫受保護的 Web API。 使用者會向 Azure AD B2C 進行驗證，以使用該單頁應用程式。 該單頁應用程式會從 Azure AD B2C 取得授權授與，以存取受保護的 Web API。

## <a name="configure-the-sample"></a>設定範例

現在已註冊 Web API 且已定義範圍，您可設定 Web API 程式碼以使用您的 Azure AD B2C 租用戶。 在本教學課程中，您會設定從 GitHub 下載的範例 .NET Core Web 應用程式。

[下載 \*.zip 封存檔](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)，或從 GitHub 複製範例 Web API 專案。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>設定 Web API

1. 在 Visual Studio 或 Visual Studio Code 中開啟 <em>B2C-WebApi/**appsettings.json**</em> 檔案。
1. 修改 `AzureAdB2C` 區塊以反映您的租用戶名稱、Web API 應用程式的應用程式識別碼、您的註冊/登入原則名稱，以及您先前定義的範圍。 該區塊應該會如下列範例所示 (具有適當的 `Tenant` 和 `ClientId` 值)：

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>啟用 CORS

若要允許您的單頁應用程式呼叫 ASP.NET Core Web API，您必須在 Web API 中啟用 [CORS](https://docs.microsoft.com/aspnet/core/security/cors)。

1. 在 *Startup.cs* 中，將 CORS 新增至 `ConfigureServices()` 方法。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. 此外，在 `ConfigureServices()` 方法中，將 `jwtOptions.Authority` 值設定為下列權杖簽發者 URI。

    請將 `<your-tenant-name>` 取代為您的 B2C 租用戶名稱。

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. 在 `Configure()` 方法中，設定 CORS。

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (僅限 Visual Studio) 在方案總管中的 [屬性]  下方，開啟 *launchSettings.json* 檔案，然後尋找 `iisExpress` 區塊。
1. (僅限 Visual Studio) 以您在先前的步驟中註冊 *webapi1* 應用程式時所指定的連接埠號碼來更新 `applicationURL` 值。 例如：

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>設定單頁應用程式

本系列[上一個教學課程](tutorial-single-page-app.md)中的單頁應用程式 (SPA)，會使用 Azure AD B2C 進行使用者註冊、登入，以及呼叫由 *frabrikamb2c* 示範租用戶保護的 ASP.NET Core Web API。

在本節中，您會更新單頁應用程式，以呼叫由*您的* Azure AD B2C 租用戶保護、且由您在本機電腦上執行的 ASP.NET Core Web API。

若要變更 SPA 中的設定：

1. 從您在上一個教學課程下載或複製的 [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] 專案中，開啟 *index.html* 檔案。
1. 使用您先前建立的 *demo.read* 範圍的 URI 以及 Web API 的 URL 來設定範例。
    1. 在 `appConfig` 定義中，將 `b2cScopes` 值取代為範圍的完整 URI (您先前記錄的 [完整範圍值]  )。
    1. 將 `webApi` 值變更為您在先前步驟中註冊 Web API 應用程式時所新增的重新導向 URI。

    `appConfig` 定義應該會如下列程式碼區塊所示 (以您的租用戶名稱取代 `<your-tenant-name>`)：

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>執行 SPA 和 Web API

最後，您會在本機電腦上執行 ASP.NET Core Web API 和 Node.js 單頁應用程式。 然後，您可以登入單頁應用程式，並按下按鈕以對受保護的 API 起始要求。

雖然這兩個應用程式在本教學課程中都在本機執行，但它們會使用 Azure AD B2C 進行安全的註冊/登入，並授與對受保護的 Web API 的存取權。

### <a name="run-the-aspnet-core-web-api"></a>執行 ASP.NET Core Web API

在 Visual Studio 中，按 **F5** 以建置 *B2C-WebAPI.sln* 解決方案並進行偵錯。 當專案啟動時，您的預設瀏覽器中會顯示一個網頁，宣告此 Web API 適用於要求。

如果您偏好使用 `dotnet` CLI，而不是 Visual Studio：

1. 開啟主控台視窗，並切換至包含 *\*.csproj* 檔案的目錄。 例如：

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. 藉由執行 `dotnet run` 來建置和執行 Web API。

    當 API 啟動並執行時，您應該會看到如下的輸出 (在教學課程中，您可以放心地忽略任何 `NETSDK1059` 警告)：

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>執行單頁應用程式

1. 開啟主控台視窗，並切換至包含 Node.js 範例的目錄。 例如：

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. 執行下列命令：

    ```console
    npm install && npm update
    node server.js
    ```

    主控台視窗會顯示裝載應用程式的連接埠號碼。

    ```console
    Listening on port 6420...
    ```

1. 在瀏覽器中瀏覽至 `http://localhost:6420` 可檢視應用程式。
1. 使用您在[上一個教學課程](tutorial-single-page-app.md)中使用的電子郵件地址和密碼登入。 成功登入時，您應該會看到 `User 'Your Username' logged-in` 訊息。
1. 選取 [呼叫 Web API]  按鈕。 SPA 會從 Azure AD B2C 取得授權，然後存取受保護的 Web API 以顯示其索引頁面的內容：

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增 Web API 應用程式
> * 設定 Web API 的範圍
> * 授與 Web API 的權限
> * 設定範例以使用應用程式

現在您已了解 SPA 如何向受保護的 Web API 要求資源，接下來請深入了解這些應用程式類型彼此之間以及與 Azure AD B2C 的互動方式。

> [!div class="nextstepaction"]
> [可以在 Active Directory B2C 中使用的應用程式類型 >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
