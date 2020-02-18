---
title: 設定登入使用者的 web 應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立可登入使用者的 web 應用程式（程式碼設定）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d54103cad8a3550bdc300cba2308397dd1ce3d6c
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425573"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>登入使用者的 Web 應用程式：程式碼設定

瞭解如何為您的 web 應用程式設定登入使用者的程式碼。

## <a name="libraries-for-protecting-web-apps"></a>保護 web 應用程式的程式庫

<!-- This section can be in an include for Web App and Web APIs -->
用來保護 web 應用程式（和 Web API）的程式庫包括：

| 平台 | 程式庫 | 描述 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [適用于 .NET 的識別模型延伸模組](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | 適用于 .NET 的 Microsoft 身分識別模型延伸模組是由 ASP.NET 和 ASP.NET Core 直接使用，它會建議一組在 .NET Framework 和 .NET Core 上執行的 Dll。 從 ASP.NET 或 ASP.NET Core web 應用程式，您可以使用**TokenValidationParameters**類別（特別是在某些合作夥伴案例中）來控制權杖驗證。 |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL JAVA](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | JAVA web 應用程式的支援 |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Python web 應用程式的支援 |

選取對應至您感興趣之平臺的索引標籤：

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

本文中的程式碼片段和下列專案會從[ASP.NET Core web 應用程式累加式教學課程（第1章）](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)中解壓縮。

您可能想要參考此教學課程，以取得完整的執行詳細資料。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

本文中的程式碼片段和下列專案會從[ASP.NET web 應用程式範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)中解壓縮。

如需完整的執行詳細資料，您可以參考此範例。

# <a name="javatabjava"></a>[Java](#tab/java)

本文中的程式碼片段和下列專案會從[java web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)（在 MSAL java 中呼叫 Microsoft graph 範例）中解壓縮。

如需完整的執行詳細資料，您可以參考此範例。

# <a name="pythontabpython"></a>[Python](#tab/python)

本文中的程式碼片段和下列專案會從[python web 應用程式](https://github.com/Azure-Samples/ms-identity-python-webapp)（在 MSAL python 中呼叫 Microsoft graph 範例）中解壓縮。

如需完整的執行詳細資料，您可以參考此範例。

---

## <a name="configuration-files"></a>組態檔

使用 Microsoft 身分識別平臺登入使用者的 Web 應用程式，通常是透過設定檔來設定。 您需要填寫的設定如下：

- 如果您想要讓應用程式在國家雲端中執行，則為雲端實例（`Instance`），例如
- 租使用者識別碼中的物件（`TenantId`）
- 從 Azure 入口網站複製的應用程式用戶端識別碼（`ClientId`）

有時候，應用程式可以藉由 `Authority`來參數化，這是 `Instance` 和 `TenantId`的串連。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，這些設定位於 "AzureAd" 區段中的[appsettings。](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8)

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
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

在 ASP.NET Core 中，另一個檔案（[properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)）包含您的應用程式和各種設定檔的 URL （`applicationUrl`）和 SSL 埠（`sslPort`）。

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

在 Azure 入口網站中，您必須在應用程式的**驗證**頁面上註冊的回復 uri 必須符合這些 url。 在上述兩個設定檔中，將會 `https://localhost:44321/signin-oidc`。 原因是 `applicationUrl` `http://localhost:3110`，但指定 `sslPort` （44321）。 `CallbackPath` 是 `/signin-oidc`，如 `appsettings.json`中所定義。

以同樣的方式，登出 URI 會設定為 `https://localhost:44321/signout-callback-oidc`。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，應用程式是[透過 web.config 檔案](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15)，第12行設定為15。

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

在 Azure 入口網站中，您必須在應用程式的**驗證**頁面上註冊的回復 uri 必須符合這些 url。 也就是說，它們應該是 `https://localhost:44326/`。

# <a name="javatabjava"></a>[Java](#tab/java)

在 JAVA 中，設定位於位於 [`src/main/resources`] 底下的 [ [properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) ] 檔案中。

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

在 Azure 入口網站中，您必須在應用程式的 [**驗證**] 頁面上註冊的回復 uri，必須符合應用程式所定義的 `redirectUri` 實例。 也就是說，它們應該是 `http://localhost:8080/msal4jsample/secure/aad` 並 `http://localhost:8080/msal4jsample/graph/me`。

# <a name="pythontabpython"></a>[Python](#tab/python)

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

初始化程式碼會依平臺而有所不同。 對於 ASP.NET Core 和 ASP.NET，登入使用者會被委派給 OpenID Connect 中介軟體。 ASP.NET 或 ASP.NET Core 範本會為 Azure Active Directory （Azure AD） v1.0 端點產生 web 應用程式。 需要進行一些設定，才能將它們調整成 Microsoft 身分識別平臺（v2.0）端點。 就 JAVA 而言，它會以應用程式的合作，由春季處理。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core web apps （和 web Api）中，應用程式會受到保護，因為您在控制器或控制器動作上有 `[Authorize]` 屬性。 這個屬性會檢查使用者是否已驗證。 初始化應用程式的程式碼位於 Startup.cs 檔案中。

若要使用 Microsoft 身分識別平臺（先前為 Azure AD v2.0）來新增驗證，您需要新增下列程式碼。 程式碼中的批註應該一目了然。

> [!NOTE]
> 如果您使用 Visual studio 中的預設 ASP.NET Core Web 專案來啟動專案，或使用 `dotnet new mvc`，則預設會提供 `AddAzureAD` 的方法。 這是因為相關的套件會自動載入。
>
> 如果您從頭開始建立專案，並嘗試使用下列程式碼，建議您將**AspNetCore**的 NuGet 套件新增至您的專案，讓 `AddAzureAD` 方法可供使用。

下列程式碼可從啟動中取得[。 cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)。

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

`AddMicrosoftIdentityPlatformAuthentication` 擴充方法會定義在 WebAppServiceCollectionExtensions 中，也就是[L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)。 這樣

- 新增驗證服務。
- 設定讀取設定檔的選項。
- 設定 OpenID Connect 選項，讓使用的授權單位是 Microsoft 身分識別平臺（先前為 Azure AD v2.0）端點。
- 驗證權杖的簽發者。
- 確保對應至名稱的宣告會從識別碼權杖中的 `preferred_username` 宣告進行對應。

除了設定之外，您還可以在呼叫 `AddMicrosoftIdentityPlatformAuthentication`時指定 configuration 區段的名稱。 根據預設，它是 `AzureAd`。

追蹤 OpenId Connect 中介軟體事件可協助您針對 web 應用程式進行疑難排解（如果驗證無法使用）。 將 `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` 設定為 `true` 會向您顯示 ASP.NET Core 中介軟體從 HTTP 回應進行到 `HttpContext.User`中的使用者身分識別時，資訊的詳細資料。

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

`AadIssuerValidator` 類別可讓權杖的簽發者在許多情況下進行驗證。 此類別適用于 v1.0 或 v2.0 權杖、單一租使用者或多組織使用者共用應用程式，或在 Azure 公用雲端或國家雲端中使用其個人 Microsoft 帳戶登入使用者的應用程式。 可從[Microsoft. Identity. Web/Resource/AadIssuerValidator](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)取得。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

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

# <a name="javatabjava"></a>[Java](#tab/java)

JAVA 範例會使用春季架構。 應用程式會受到保護，因為您會執行一個篩選器來攔截每個 HTTP 回應。 在 JAVA web 應用程式的快速入門中，此篩選準則會在 `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`中 `AuthFilter`。

此篩選器會處理 OAuth 2.0 授權碼流程，並檢查使用者是否已驗證（`isAuthenticated()` 方法）。 如果使用者未經過驗證，則會計算 Azure AD 授權端點的 URL，並將瀏覽器重新導向至此 URI。

當回應抵達（包含授權碼）時，它會使用 MSAL JAVA 取得權杖。 當它最後從權杖端點（在重新導向 URI 上）收到權杖時，使用者就已登入。

如需詳細資訊，請參閱[AuthFilter](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)中的 `doFilter()` 方法。

> [!NOTE]
> `doFilter()` 的程式碼會以稍有不同的順序撰寫，但是流程就是其中所述。

如需此方法觸發之授權碼流程的詳細資訊，請參閱[Microsoft 身分識別平臺和 OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)。

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [登入和登出](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [登入和登出](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [登入和登出](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [登入和登出](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
