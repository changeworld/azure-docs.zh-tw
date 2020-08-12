---
title: 設定登入使用者的 web 應用程式-Microsoft 身分識別平臺 |Azure
description: '瞭解如何建立 web 應用程式，以將使用者登入 (程式碼設定) '
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 30b90b89300d6ca63255a000c7a6f7723f648056
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118752"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>登入使用者的 Web 應用程式：程式碼設定

瞭解如何為您的 web 應用程式設定登入使用者的程式碼。

## <a name="libraries-for-protecting-web-apps"></a>保護 web 應用程式的程式庫

<!-- This section can be in an include for web app and web APIs -->
用來保護 web 應用程式 (和 Web API) 的程式庫包括：

| 平台 | 程式庫 | 描述 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [適用于 .NET 的識別模型延伸模組](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | 適用于 .NET 的 Microsoft 身分識別模型延伸模組是由 ASP.NET 和 ASP.NET Core 直接使用，它會建議一組在 .NET Framework 和 .NET Core 上執行的 Dll。 從 ASP.NET 或 ASP.NET Core web 應用程式，您可以使用**TokenValidationParameters** (類別來控制權杖驗證，特別是在某些合作夥伴案例中) 。 實際上，複雜度會封裝在[Microsoft. Identity. Web](https://aka.ms/ms-identity-web) library 中 |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | JAVA web 應用程式的支援 |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Python web 應用程式的支援 |

選取對應至您感興趣之平臺的索引標籤：

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

本文中的程式碼片段和下列專案會從[ASP.NET Core web 應用程式累加式教學課程（第1章）](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)中解壓縮。

您可能想要參考此教學課程，以取得完整的執行詳細資料。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

本文中的程式碼片段和下列專案會從[ASP.NET web 應用程式範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)中解壓縮。

如需完整的執行詳細資料，您可以參考此範例。

# <a name="java"></a>[Java](#tab/java)

本文中的程式碼片段和下列專案會從[java web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)（在 MSAL java 中呼叫 Microsoft graph 範例）中解壓縮。

如需完整的執行詳細資料，您可以參考此範例。

# <a name="python"></a>[Python](#tab/python)

本文中的程式碼片段和下列專案會從[python web 應用程式](https://github.com/Azure-Samples/ms-identity-python-webapp)（在 MSAL python 中呼叫 Microsoft graph 範例）中解壓縮。

如需完整的執行詳細資料，您可以參考此範例。

---

## <a name="configuration-files"></a>組態檔

使用 Microsoft 身分識別平臺登入使用者的 Web 應用程式，是透過設定檔來設定。 您需要填寫的設定如下：

- `Instance`如果您想要讓應用程式在國家雲端中執行，則 () 雲端實例，例如
- [租使用者識別碼] 中的物件 (`TenantId`) 
- 應用程式的用戶端識別碼 (`ClientId`) ，如同從 Azure 入口網站複製而來

有時候，應用程式可以由參數化 `Authority` ，這是和的串連 `Instance` `TenantId` 。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，這些設定位於檔案[appsettings.js](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8)的 "AzureAd" 區段中。

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-oidc"
  }
}
```

在 ASP.NET Core 中，) 上的另一個檔案 ([properties\launchSettings.js](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)會包含 URL () `applicationUrl` 以及應用程式的 TLS/SSL 埠 () `sslPort` 和各種設定檔。

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

在 Azure 入口網站中，您必須在應用程式的**驗證**頁面上註冊的回復 uri 必須符合這些 url。 在上述兩個設定檔中，它們會是 `https://localhost:44321/signin-oidc` 。 原因是 `applicationUrl` `http://localhost:3110` ，但 `sslPort` 指定 (44321) 。 `CallbackPath`是 `/signin-oidc` ，如中所定義 `appsettings.json` 。

以同樣的方式，登出 URI 會設定為 `https://localhost:44321/signout-oidc` 。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，應用程式是透過[Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15)檔案，第12行到第15行來設定。

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

在 Azure 入口網站中，您必須在應用程式的**驗證**頁面上註冊的回復 uri 必須符合這些 url。 也就是說，它們應該是 `https://localhost:44326/` 。

# <a name="java"></a>[Java](#tab/java)

在 JAVA 中，設定位於位於的[應用程式. properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties)檔案中 `src/main/resources` 。

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

在 Azure 入口網站中，您必須在應用程式的**驗證**頁面上註冊的回復 uri，必須符合 `redirectUri` 應用程式所定義的實例。 也就是，它們應該是 `http://localhost:8080/msal4jsample/secure/aad` 和 `http://localhost:8080/msal4jsample/graph/me` 。

# <a name="python"></a>[Python](#tab/python)

以下是 app_config 中的 Python 設定檔[。 .py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)：

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> 本快速入門建議您將用戶端密碼儲存在設定檔中，以方便您進行。 在您的生產應用程式中，您會想要使用其他方式來儲存您的密碼，例如金鑰保存庫或環境變數，如[Flask 的檔](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)所述。
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>初始化程式碼

初始化程式碼會依平臺而有所不同。 對於 ASP.NET Core 和 ASP.NET，登入使用者會被委派給 OpenID Connect 中介軟體。 ASP.NET 或 ASP.NET Core 範本會針對 Azure Active Directory (Azure AD) v1.0 端點產生 web 應用程式。 需要進行一些設定，才能將它們調整成 Microsoft 身分識別平臺 (v2.0) 端點。 就 JAVA 而言，它會以應用程式的合作，由春季處理。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core web apps (和 web Api) 中，應用程式會受到保護，因為您在 `[Authorize]` 控制器或控制器動作上有一個屬性。 這個屬性會檢查使用者是否已驗證。 初始化應用程式的程式碼位於*Startup.cs*檔案中。

若要使用 Microsoft 身分識別平臺來新增驗證， (先前 Azure AD v2.0) ，您必須新增下列程式碼。 程式碼中的批註應該一目了然。

> [!NOTE]
> 如果您想要直接開始使用 Microsoft 身分識別平臺的新 ASP.NET Core 範本，利用 Microsoft 身分識別 Web，您可以下載包含 .NET Core 3.1 和 .NET 5.0 之專案範本的預覽 NuGet 套件。 然後，在安裝之後，您就可以直接將 ASP.NET Core web 應用程式具現化 (MVC 或 Blazor) 。 如需詳細資訊，請參閱[Microsoft web 應用程式專案範本](https://aka.ms/ms-id-web/webapp-project-templates)。 這是最簡單的方法，因為它會為您執行下列所有步驟。
>
> 如果您想要在 Visual Studio 內或使用或的情況下，使用目前的預設 ASP.NET Core Web 專案啟動專案 `dotnet new mvc --auth SingleAuth` `dotnet new webapp --auth SingleAuth` ，您會看到如下所示的程式碼：
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> 這段程式碼會使用舊版的**AspNetCore. AzureAD. UI** NuGet 封裝，此套件可用來建立 Azure AD v1.0 應用程式。 本文說明如何建立 Microsoft 身分識別平臺 (Azure AD v2.0) 應用程式來取代該程式碼。
>

1. 在您的專案中新增[Microsoft. identity.](https://www.nuget.org/packages/Microsoft.Identity.Web) [web 和 node.js NuGet 套件。](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) 移除 AspNetCore AzureAD. UI NuGet 套件（如果有的話）。

2. 更新中的程式碼， `ConfigureServices` 使其使用 `AddMicrosoftWebAppAuthentication` 和 `AddMicrosoftIdentityUI` 方法。

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. 在 `Configure` *Startup.cs*的方法中，使用呼叫來啟用驗證`app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

在上述程式碼中：
- `AddMicrosoftWebAppAuthentication`擴充方法是在**Microsoft. Identity. Web**中定義。 這樣
  - 新增驗證服務。
  - 設定選項以從 "AzureAD" 區段中讀取設定檔 () 
  - 設定 OpenID Connect 選項，讓授權單位是 Microsoft 身分識別平臺端點。
  - 驗證權杖的簽發者。
  - 確保對應至名稱的宣告會從識別碼權杖中的宣告進行對應 `preferred_username` 。

- 除了設定物件之外，您還可以在呼叫時指定 configuration 區段的名稱 `AddMicrosoftWebAppAuthentication` 。 根據預設，它是 `AzureAd` 。

- `AddMicrosoftWebAppAuthentication`具有 advanced 案例的其他參數。 例如，追蹤 OpenID Connect 中介軟體事件可協助您針對 web 應用程式進行疑難排解（如果驗證無法使用）。 將選擇性參數設定 `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` 為， `true` 會向您示範當 ASP.NET Core 中介軟體從 HTTP 回應進行到中的使用者身分識別時，資訊的處理方式 `HttpContext.User` 。

- `AddMicrosoftIdentityUI`擴充方法會定義在**Microsoft. Identity. Web UI**中。 它會提供預設控制器來處理登入和登出。

您可以在中找到更多關於如何讓您建立 web 應用程式的詳細資料。<https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> 目前，當使用 Azure AD 作為和外部登入提供者時，Microsoft. Identity. Web 不支援**個別使用者帳戶**的案例 (將使用者帳戶儲存在應用程式內) 。 如需詳細資訊，請參閱： [AzureAD/microsoft-身分識別-web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

與 ASP.NET web 應用程式和 web Api 中的驗證相關的程式碼位於[App_Start/startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)檔中。

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

JAVA 範例會使用春季架構。 應用程式會受到保護，因為您會執行一個篩選器來攔截每個 HTTP 回應。 在 JAVA web 應用程式的快速入門中，此篩選器位於 `AuthFilter` `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` 。

此篩選器會處理 OAuth 2.0 授權碼流程，並檢查使用者是否已驗證 (`isAuthenticated()` 方法) 。 如果使用者未經過驗證，則會計算 Azure AD 授權端點的 URL，並將瀏覽器重新導向至此 URI。

當回應抵達（包含授權碼）時，它會使用 MSAL JAVA 取得權杖。 當它最後從權杖端點接收權杖時 (在重新導向 URI) 上，使用者就已登入。

如需詳細資訊，請參閱 `doFilter()` [AuthFilter](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)中的方法。

> [!NOTE]
> 的 `doFilter()` 程式碼會以稍有不同的順序撰寫，但是流程就是其中所述。

如需此方法觸發之授權碼流程的詳細資訊，請參閱[Microsoft 身分識別平臺和 OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)。

# <a name="python"></a>[Python](#tab/python)

Python 範例會使用 Flask。 Flask 和 MSAL Python 的初始化會在[.py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)中完成。

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>後續步驟

在下一篇文章中，您將瞭解如何觸發登入和登出。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [登入和登出](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [登入和登出](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [登入和登出](./scenario-web-app-sign-user-sign-in.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [登入和登出](./scenario-web-app-sign-user-sign-in.md?tabs=python)

---