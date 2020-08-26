---
title: 設定呼叫 Web API 的 Web 應用程式 - Microsoft 身分識別平台 | Azure
description: 了解如何設定呼叫 Web API 的 Web 應用程式程式碼
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
ms.openlocfilehash: 8827d413144d8bc6f00c3948a99be3ee3aa2264e
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855437"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>呼叫 Web API 的 Web 應用程式：程式碼設定

如同[登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)案例中所示，Web 應用程式會使用 [OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)來登入使用者。 此流程包含兩個步驟：

1. 要求授權碼。 此部分會將與使用者的私人對話委派給 Microsoft 身分識別平台。 在該對話期間，使用者會進行登入，並同意使用 Web API。 當私人對話成功完成時，Web 應用程式即會在其重新導向 URI 上收到授權碼。
1. 透過兌換授權碼來要求 API 的存取權杖。

[登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)案例僅涵蓋第一個步驟。 您會在此了解如何修改 Web 應用程式，使其不僅會登入使用者，現在還會呼叫 Web API。

## <a name="libraries-that-support-web-app-scenarios"></a>支援 Web 應用程式案例的程式庫

下列 Microsoft 驗證程式庫 (MSAL) 中程式庫支援 Web 應用程式的授權碼流程：

| MSAL 程式庫 | 描述 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援 .NET Framework 和 .NET Core 平台。 不支援通用 Windows 平台 (UWP)、Xamarin.iOS 和 Xamarin.Android，因為這些平台會用來建置公用用戶端應用程式。 針對 ASP.NET Core web 應用程式和 web Api，MSAL.NET 會封裝[在名為](https://aka.ms/ms-identity-web)web.config 的較高層級程式庫中。|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> 適用於 Python 的 MSAL | Python Web 應用程式的支援。 |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> 適用於 Java 的 MSAL | Java Web 應用程式的支援。 |

選取感興趣的平台索引標籤：

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

若要讓 Web 應用程式在使用 Microsoft.Identity.Web 時呼叫受保護的 API，則只需要呼叫 `AddWebAppCallsProtectedWebApi` 並指定權杖快取序列化格式 (例如記憶體內部權杖快取)：

```C#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // more code here

    services.AddMicrosoftIdentityWebAppAuthentication(Configuration,
                                                      "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                    initialScopes: new string[] { "user.read" })
                .AddInMemoryTokenCaches();

    // more code here
}
```

若對深入了解權杖快取感興趣，請參閱[權杖快取序列化選項](#token-cache)

> [!NOTE]
> 若要完全了解此處的程式碼範例，則需要熟悉 [ASP.NET Core 基本概念](/aspnet/core/fundamentals) (機器翻譯)，特別是[相依性插入](/aspnet/core/fundamentals/dependency-injection)和[選項](/aspnet/core/fundamentals/configuration/options)。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

因為使用者登入已經委派給 Open ID Connect (OIDC) 中介軟體，所以必須與 OIDC 處理序互動。 其互動方式取決於所使用的架構。

針對 ASP.NET，您將會訂閱中介軟體 OIDC 事件：

- 您會讓 ASP.NET Core 透過 Open ID Connect 中介軟體來要求授權碼。 ASP.NET 或 ASP.NET Core 會讓使用者登入和同意。
- 您將會訂閱 Web 應用程式以接收授權碼。 此訂閱是透過使用 C# 委派進行的。
- 在收到授權碼時，您將會使用 MSAL 程式庫來進行兌換。 其結果產生的存取權杖和重新整理權杖會儲存在權杖快取中。 快取可用於應用程式的其他部分 (例如控制器) 來以無訊息方式取得其他權杖。

本文中程式碼範例和以下項目是從 [ASP.NET Web app sample](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) (ASP.NET Web 應用程式範例) 擷取的。 建議參考該範例以取得完整的實作詳細資料。

# <a name="java"></a>[Java](#tab/java)

本文中程式碼範例和下列項目是從 [Java web application that calls Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) (呼叫 Microsoft Graph 的 Java Web 應用程式) 擷取的，其為使用適用於 Java MSAL 的 Web 應用程式範例。
範例目前會讓適用於 Java 的 MSAL 產生授權碼 URL，並處理導覽至 Microsoft 身分識別平台的授權端點。 您也可以使用 Sprint 安全性來登入使用者。 建議參考範例以取得完整的實作詳細資料。

# <a name="python"></a>[Python](#tab/python)

本文中程式碼範例和下列項目是從 [Python web application calling Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) (呼叫 Microsoft Graph 的 Python Web 應用程式) 擷取的，其為使用 MSAL.Python 的 Web 應用程式範例。
範例目前會讓 MSAL.Python 產生授權碼 URL，並處理導覽至 Microsoft 身分識別平台的授權端點。 建議參考範例以取得完整的實作詳細資料。

---

## <a name="code-that-redeems-the-authorization-code"></a>兌換授權碼的程式碼

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web 會透過設定正確的 Open ID Connect 設定、訂閱授權碼接收事件，以及兌換授權碼來簡化程式碼。 兌換授權碼無需額外的程式碼。 如需其運作方式的詳細資訊，請參閱 [Microsoft 身分識別 Web 原始程式碼](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) 。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 處理的方式與 ASP.NET Core 相似，其差異在 OpenID Connect 的設定，以及 `OnAuthorizationCodeReceived` 事件的訂閱是在 [App_start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) 檔案中發生。 其概念也和 ASP.NET Core 中的概念相似，其差異為在 ASP.NET 中，您必須在 [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15) 中指定 `RedirectUri`。 這項設定的強固程度比 ASP.NET Core 中設定低，因為您將需要在部署應用程式時變更該設定。

以下是 Startup.Auth.cs 的程式碼：

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

[登入使用者的 Web 應用程式：程式碼設定](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)，以了解 Java 範例如何取得授權碼。 在應用程式收到授權碼後，[AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56)：

1. 會委派給 [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97) 中的 `AuthHelper.processAuthenticationCodeRedirect` 方法。
1. 呼叫 `getAuthResultByAuthCode`。

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

`getAuthResultByAuthCode` 方法是定義在 [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176) 中。 其會建立 MSAL `ConfidentialClientApplication`，然後使用從授權碼建立的 `AuthorizationCodeParameters` 來呼叫 `acquireToken()`。

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

授權碼流程要求如[登入使用者的 Web 應用程式：程式碼設定](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)中所示。 接著會在 `authorized` 函式上收到授權碼，之後 Flask 會從 `/getAToken` URL 進行路由。 如需此程式碼的完整內容，請參閱 [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)：

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

保密用戶端應用程式也可透過使用用戶端憑證，或用戶端判斷提示來證明其身分識別，而非使用用戶端密碼。
使用用戶端判斷提示是進階案例，[用戶端判斷提示](msal-net-client-assertions.md)中提供詳細的說明。

## <a name="token-cache"></a>權杖快取

> [!IMPORTANT]
> Web 應用程式或 Web API 的權杖快取實作與傳統型應用程式實作不同，因為後者經常是[以檔案為基礎](scenario-desktop-acquire-token.md#file-based-token-cache)。
> 基於安全性和效能考量，針對 Web 應用程式和 Web API 確認每個使用者帳戶都有一個權杖快取相當重要。 您必須為每個帳戶序列化權杖快取。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core 教學課程會使用相依性插入以供在應用程式的 Startup.cs 中決定權杖快取實作。 Microsoft.Identity.Web 隨附[權杖快取序列化](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)中所述的預建權杖快取序列化程式。 選擇 ASP.NET Core 另外一項有趣的可能性是[分散式記憶體快取](/aspnet/core/performance/caching/distributed#distributed-memory-cache)：

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

如需有關權杖快取提供者的詳細資訊，請參閱 Microsoft 的權杖快取 [序列化](https://aka.ms/ms-id-web/token-cache-serialization) 文章，以及 [ASP.NET Core 的 Web 應用程式教學課程 |](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) Web apps 教學課程的權杖快取階段。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Web 應用程式或 Web API 的權杖快取實作與傳統型應用程式實作不同，因為後者經常是[以檔案為基礎](scenario-desktop-acquire-token.md#file-based-token-cache)。

Web 應用程式實作可使用 ASP.NET 工作階段或伺服器記憶體。 例如，請參閱在 [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51) 中，快取實作是如何在建立 MSAL.NET 應用程式後掛勾的：

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java 提供序列化和還原序列化權杖快取的方法。 Java 範例會處理來自工作階段的序列化，如 [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)中的 `getAuthResultBySilentFlow` 方法內所示：

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

[適用於 Java 的 MSAL 範例](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)中提供了 `SessionManagementHelper` 類別的詳細資料。

# <a name="python"></a>[Python](#tab/python)

在 Python 範例中，每個帳戶的單一快取是透過為每一個要求重新建立保密用戶端應用程式，然後在 Flask 工作階段快取中序列化來加以確認：

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>後續步驟

此時，當使用者登入時，權杖即會儲存在權杖快取中。 讓我們來看看其接著在 Web 應用程式其他部分中使用的方式。

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web 應用程式：在全域登出時從快取中移除帳戶](scenario-web-app-call-api-sign-in.md)