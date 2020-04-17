---
title: 設定在使用者中簽名的 Web 應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何建構在使用者中簽名的 Web 應用程式(程式碼設定)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3cb51a57baa87849e97f7b05762dc4d6eba787a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537106"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>在使用者中簽名的 Web 應用程式:代碼設定

瞭解如何為在使用者中簽名的 Web 應用配置代碼。

## <a name="libraries-for-protecting-web-apps"></a>保護 Web 應用程式庫

<!-- This section can be in an include for web app and web APIs -->
保護 Web 應用程式(與 Web API)的庫是:

| 平台 | 程式庫 | 描述 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [.NET 的識別模型擴展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | 由ASP.NET和ASP.NET核心直接使用,.NET的Microsoft識別模型擴展建議在.NET框架和.NET Core上運行一組DLL。 通過ASP.NET或ASP.NET酷睿 Web 應用,您可以使用**令牌驗證參數**類(特別是在某些合作夥伴方案中)控制令牌驗證。 |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | 支援 Java Web 應用程式 |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | 支援 Python Web 應用程式 |

選擇與您感興趣的平台對應的選項卡:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

本文中的代碼段和以下內容摘自[ASP.NET酷睿 Web 應用增量教程,第 1 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)。

您可能需要參考本教程瞭解完整的實現詳細資訊。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

本文中的代碼段和以下內容是從[ASP.NET Web 應用範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)中提取的。

您可能需要參考此範例瞭解完整的實現詳細資訊。

# <a name="java"></a>[Java](#tab/java)

本文中的代碼段和以下內容是從在 MSAL Java 中調用 Microsoft 圖形範例的[JAva Web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)中提取的。

您可能需要參考此範例瞭解完整的實現詳細資訊。

# <a name="python"></a>[Python](#tab/python)

本文中的代碼段和以下內容是從在 MSAL Python 中調用 Microsoft 圖形範例的[Python Web 應用程式](https://github.com/Azure-Samples/ms-identity-python-webapp)中提取的。

您可能需要參考此範例瞭解完整的實現詳細資訊。

---

## <a name="configuration-files"></a>組態檔

使用 Microsoft 識別平台登錄使用者的 Web 應用程式通常透過設定檔進行配置。 您需要填寫的設定包括:

- 如果您希望應用在國家雲`Instance`中運行,則雲實例 ( )
- 租戶代碼中的存取`TenantId`者 ( )
- 從 Azure`ClientId`門戶複製的應用程式的客戶端 ID ( )

有時,應用程式可以通過`Authority`參數化`Instance``TenantId`,這是和的串聯。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在ASP.NET核心中,這些設置位於[appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8)檔中的"AzureAd"部分。

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

在ASP.NET核心中,另一個檔 ([屬性_啟動設定.json)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)`applicationUrl`包含應用程式的`sslPort`URL () 和 TLS/SSL 連接埠 ( ) 和各種設定檔。

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

在 Azure 門戶中,需要在應用程式的**身份驗證**頁上註冊的回覆 URI 需要與這些 URL 匹配。 對於前面的兩個設定檔,它們將是`https://localhost:44321/signin-oidc`。 原因是`applicationUrl``http://localhost:3110`,`sslPort`但指定 (44321)。 `CallbackPath`是`/signin-oidc`,`appsettings.json`如 中定義的那樣。

同樣,登出 URI 將`https://localhost:44321/signout-callback-oidc`設定為 。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在ASP.NET中,應用程式通過[Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15)檔(行12到15)進行配置。

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

在 Azure 門戶中,需要在應用程式的**身份驗證**頁上註冊的回覆 URI 需要與這些 URL 匹配。 也就是說,它們應該是`https://localhost:44326/`。

# <a name="java"></a>[Java](#tab/java)

在 Java 中,設定位於[應用程式.屬性](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties)檔中`src/main/resources`,位於 。

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

在 Azure 門戶中,需要在應用程式的**身份驗證**頁上註冊的回覆`redirectUri`URI 需要與應用程式定義的 實例匹配。 也就是說,他們應該和`http://localhost:8080/msal4jsample/secure/aad``http://localhost:8080/msal4jsample/graph/me`。

# <a name="python"></a>[Python](#tab/python)

下面是[app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)中的 Python 設定檔:

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> 為了簡單起見,此快速入門提議將用戶端機密存儲在配置檔中。 在生產應用中,您需要使用其他方法來儲存機密,例如一個密鑰保管庫或[諸如 Flask 文件](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)中描述的環境變數。
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>初始化代碼

初始化代碼因平臺而異。 對於ASP.NET核心和ASP.NET,登錄使用者委託給OpenID Connect中間件。 ASP.NET或ASP.NET核心範本為Azure活動目錄 (Azure AD) v1.0 終結點生成 Web 應用程式。 需要某些配置來使其適應 Microsoft 標識平臺 (v2.0) 終結點。 在 Java 中,它由 Spring 與應用程式的協作處理。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET核心 Web 應用(和 Web API)`[Authorize]`中,應用程式受到保護 ,因為控制器或控制器操作上具有屬性。 此屬性檢查使用者是否經過身份驗證。 初始化應用程式的代碼位於Startup.cs檔中。

要使用 Microsoft 識別平臺(以前的 Azure AD v2.0)添加身份驗證,您需要添加以下代碼。 代碼中的註釋應不言自明。

> [!NOTE]
> 如果在 Visual Studio 中使用預設 ASP.NET`dotnet new mvc`核心 Web`AddAzureAD`專案或使用 啟動專案,則該方法預設可用。 這是因為相關包是自動載入的。
>
> 如果從頭開始生成專案並嘗試使用以下代碼,我們建議您將 NuGet 包**Microsoft.AspNetCore.身份驗證.AzureAD.UI**添加到`AddAzureAD`專案中以使 該方法可用。

以下代碼可從[Startup.cs_L33-L34 獲得](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)。

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

擴`AddMicrosoftIdentityPlatformAuthentication`充方法在 Microsoft 中定義[。標識.Web/WebAppService 收集擴展.cs_L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)。 它:

- 添加身份驗證服務。
- 設定選項以讀取配置檔。
- 設定 OpenID 連接選項,以便使用的許可權是 Microsoft 識別平臺(以前的 Azure AD v2.0)終結點。
- 驗證權杖的頒發者。
- 確保從 ID 權`preferred_username`杖中的聲明映射與名稱對應的聲明。

除了配置之外,還可以在調用`AddMicrosoftIdentityPlatformAuthentication`時指定配置部分的名稱。 預設情況下,它是`AzureAd`。

如果身份驗證不起作用,追蹤 OpenId Connect 中間件事件可以説明您解決 Web 應用程式故障。 設置為`subscribeToOpenIdConnectMiddlewareDiagnosticsEvents``true`將向您展示資訊如何由ASP.NET核心中間件集在從 HTTP`HttpContext.User`回應到 中 用戶標識時進行闡述。

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

在許多情況下`AadIssuerValidator`,該類允許驗證權杖的頒發者。 此類適用於 v1.0 或 v2.0 權杖、單租戶或多租戶應用程式,或在 Azure 公共雲或國家雲中使用其個人 Microsoft 帳戶登錄使用者的應用程式。 它可從[微軟.身份.Web/資源/Aad問題驗證器.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

與ASP.NET Web 應用和 Web API 中的身份驗證相關的代碼位於[App_Start/Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)檔中。

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

Java 示例使用 Spring 框架。 應用程式受到保護,因為您實現了一個篩選器,該篩選器會攔截每個 HTTP 回應。 在 Java Web 應用的快速入門`AuthFilter`中,`src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`此篩選器 位於中。

篩選器處理 OAuth 2.0 授權代碼串流,並檢查使用者是否`isAuthenticated()`經過身份驗證( 方法)。 如果使用者未經過身份驗證,它將計算 Azure AD 授權終結點的 URL,並將瀏覽器重定向到此 URI。

當回應到達時,包含授權代碼,它使用 MSAL Java 獲取權杖。 當它最終從權杖終結點(重定向URI上)收到令牌時,使用者將登錄。

有關詳細資訊,請參閱`doFilter()`[AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)中的方法。

> [!NOTE]
> `doFilter()`的代碼按略有不同的順序編寫,但流是描述的。

有關此方法觸發的授權代碼串流的詳細資訊,請參閱[Microsoft 識別平臺和 OAuth 2.0 授權代碼流](v2-oauth2-auth-code-flow.md)。

# <a name="python"></a>[Python](#tab/python)

Python 範例使用法條。 法爾克和MSAL Python的初始化是在[app.py_L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)中完成的。

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

在下一篇文章中,您將學習如何觸發登錄和註銷。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [登入和登出](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [登入和登出](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [登入和登出](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [登入和登出](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
