---
title: 設定 web 應用程式以呼叫 web Api-Microsoft 身分識別平臺 |Azure
description: 瞭解如何設定 web 應用程式的程式碼來呼叫 web Api
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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759155"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>呼叫 web Api 的 web 應用程式：程式碼設定

如在[登入使用者案例的 web 應用程式](scenario-web-app-sign-user-overview.md)中所示，web 應用程式會使用[OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)來簽署使用者。 此流程有兩個步驟：

1. 要求授權碼。 此元件會將使用者的私用對話委派給 Microsoft 身分識別平臺。 在該對話期間，使用者會登入並同意使用 web Api。 當私用對話成功結束時，web 應用程式會在其重新導向 URI 上接收授權碼。
1. 藉由兌換授權碼來要求 API 的存取權杖。

[登入使用者案例的 Web 應用程式](scenario-web-app-sign-user-overview.md)只涵蓋第一個步驟。 在此，您將瞭解如何修改您的 web 應用程式，讓它不只會將使用者登入，同時也會呼叫 web Api。

## <a name="libraries-that-support-web-app-scenarios"></a>支援 web 應用程式案例的程式庫

Microsoft 驗證程式庫（MSAL）中的下列程式庫支援 web 應用程式的授權碼流程：

| MSAL 程式庫 | 說明 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援 .NET Framework 和 .NET Core 平臺。 不支援的是通用 Windows 平臺（UWP）、Xamarin 和 Xamarin，因為這些平臺是用來建立公用用戶端應用程式。 |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> 適用於 Python 的 MSAL | Python web 應用程式的支援。 |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> 適用於 Java 的 MSAL | JAVA web 應用程式的支援。 |

選取您感興趣之平臺的索引標籤：

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

因為使用者登入已委派給 Open ID connect （OIDC）中介軟體，所以您必須與 OIDC 流程互動。 您的互動方式取決於您所使用的架構。

針對 ASP.NET Core，您會訂閱中介軟體 OIDC 事件：

- 您會讓 ASP.NET Core 透過 Open ID Connect 中介軟體來要求授權碼。 ASP.NET 或 ASP.NET Core 會讓使用者登入和同意。
- 您會訂閱 web 應用程式，以接收授權碼。 此訂用帳戶是使用C#委派來完成。
- 收到授權碼時，您將使用 MSAL 程式庫兌換它。 產生的存取權杖和重新整理權杖會儲存在權杖快取中。 快取可用於應用程式的其他部分（例如控制器），以無訊息方式取得其他標記。

本文中的程式碼範例和下列其中一項會從[ASP.NET Core web 應用程式累加教學課程（第2章）](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)中解壓縮。 您可能想要參考該教學課程，以取得完整的執行詳細資料。

> [!NOTE]
> 若要完整瞭解此處的程式碼範例，您必須熟悉[ASP.NET Core 的基本](https://docs.microsoft.com/aspnet/core/fundamentals)概念，特別是使用相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)和[選項](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

因為使用者登入已委派給 Open ID connect （OIDC）中介軟體，所以您必須與 OIDC 流程互動。 您的互動方式取決於您所使用的架構。

針對 ASP.NET，您會訂閱中介軟體 OIDC 事件：

- 您會讓 ASP.NET Core 透過 Open ID Connect 中介軟體來要求授權碼。 ASP.NET 或 ASP.NET Core 會讓使用者登入和同意。
- 您會訂閱 web 應用程式，以接收授權碼。 此訂用帳戶是使用C#委派來完成。
- 收到授權碼時，您將使用 MSAL 程式庫兌換它。 產生的存取權杖和重新整理權杖會儲存在權杖快取中。 快取可用於應用程式的其他部分（例如控制器），以無訊息方式取得其他標記。

本文中的程式碼範例和下列其中一項會從[ASP.NET Web 應用程式範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)中解壓縮。 您可能想要參考該範例，以取得完整的執行詳細資料。

# <a name="javatabjava"></a>[Java](#tab/java)

本文中的程式碼範例和下列其中一項會從[呼叫 Microsoft Graph 的 java web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)中解壓縮，這是使用 MSAL for JAVA 的 web 應用程式範例。
此範例目前可讓 MSAL for JAVA 產生授權碼 URL，並處理流覽至 Microsoft 身分識別平臺的授權端點。 也可以使用短期衝刺安全性來登入使用者。 您可能想要參考範例，以取得完整的執行詳細資料。

# <a name="pythontabpython"></a>[Python](#tab/python)

本文中的程式碼範例和下列其中一項會從[Python web 應用程式（呼叫 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp)，這是使用 MSAL 的 web 應用程式範例）中解壓縮。Python.
此範例目前可讓您 MSAL。Python 會產生授權碼 URL，並處理流覽至 Microsoft 身分識別平臺的授權端點。 您可能想要參考範例，以取得完整的執行詳細資料。

---

## <a name="code-that-redeems-the-authorization-code"></a>贖回授權碼的程式碼

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

在 ASP.NET Core 的 `Startup.cs` 檔案中，您會訂閱 `OnAuthorizationCodeReceived` OpenID Connect 事件。 從這個事件呼叫 MSAL.NET `AcquireTokenFromAuthorizationCode` 方法。 這個方法會將下列權杖儲存在權杖快取中：

- 要求之 `scopes`的*存取權杖*。
- 重新整理*權杖*。 當存取權杖接近過期時，將會使用此權杖來重新整理它，或是代表相同的使用者或不同的資源取得另一個權杖。

[ASP.NET Core web 應用程式教學](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)課程會為您的 web 應用程式提供可重複使用的程式碼。

以下是[L40-L42 的程式](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42)代碼。 It 功能會呼叫：

- `AddMicrosoftIdentityPlatformAuthentication` 方法，可將驗證新增至 web 應用程式。
- `AddMsal` 方法，這會新增呼叫 Web Api 的功能。
- `AddInMemoryTokenCaches` 方法，這是關於選擇權杖快取的執行。

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead` 是在常數中定義[。 cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)：

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

您已經研究過[Web 應用程式中登入使用者-程式碼](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)設定的 `AddMicrosoftIdentityPlatformAuthentication` 內容。

### <a name="the-addmsal-method"></a>AddMsal 方法

`AddMsal` 的程式碼位於 WebAppServiceCollectionExtensions 中，則位於[L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)。

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

`AddMsal` 方法可確保：

- ASP.NET Core web 應用程式會要求使用者的識別碼權杖和驗證碼（`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`）。
- 已新增 `offline_access` 範圍。 此範圍會取得使用者同意，讓應用程式取得重新整理權杖。
- 應用程式會訂閱 OIDC `OnAuthorizationCodeReceived` 事件，並使用 MSAL.NET 贖回呼叫，這會封裝到可重複使用的元件來執行 `ITokenAcquisition`。

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync 方法

`TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` 的方法位於 TokenAcquisition 的[L145 # L101-](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)。 它可確保：

- ASP.NET 不會嘗試以平行方式將驗證碼兌換為 MSAL.NET （`context.HandleCodeRedemption();`）。
- 識別碼權杖中的宣告可供 MSAL 用來計算使用者帳戶的權杖快取金鑰。
- 必要時，會建立 MSAL.NET 應用程式的實例。
- MSAL.NET 應用程式會兌換此程式碼。
- 在呼叫 `context.HandleCodeRedemption(null, result.IdToken);`期間，新的識別碼權杖會與 ASP.NET Core 共用。 存取權杖不會與 ASP.NET Core 共用。 它會保留在與使用者相關聯的 MSAL.NET token 快取中，並可供在 ASP.NET Core 控制器中使用。

以下是 `TokenAcquisition`的相關程式碼：

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>TokenAcquisition. BuildConfidentialClientApplication 方法

在 ASP.NET Core 中，建立機密用戶端應用程式會使用 `HttpContext`中的資訊。 您可以使用 `CurrentHttpContext` 屬性來存取與要求相關聯的 `HttpContext`。 `HttpContext` 包含 web 應用程式 URL 和登入使用者（在 `ClaimsPrincipal`中）的相關資訊。 

`BuildConfidentialClientApplication` 方法也會使用 ASP.NET Core 設定。 此設定具有 "AzureAD" 區段，而且也會系結至下列兩個元素：

- [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)類型的 `_applicationOptions` 資料結構。
- [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)類型的 `azureAdOptions` 實例，定義于 ASP.NET Core `Authentication.AzureAD.UI`中。

最後，應用程式需要維護權杖快取。 您將會在下一節中進一步瞭解。

`GetOrBuildConfidentialClientApplication()` 方法的程式碼位於 TokenAcquisition 中。 [L333 # L290-](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333)。 它會使用相依性插入所插入的成員（以 TokenAcquisition 中的 `TokenAcquisition` 在[L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)中傳遞）。

以下是 `GetOrBuildConfidentialClientApplication`的程式碼：

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>摘要

`AcquireTokenByAuthorizationCode` 實際上是贖回 ASP.NET 要求的授權碼，並取得新增至 MSAL.NET 使用者權杖快取之權杖的方法。 在快取中，接著會在 ASP.NET Core 控制器中使用權杖。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 的處理方式類似于 ASP.NET Core，不同之處在于 OpenID Connect 的設定和 `OnAuthorizationCodeReceived` 事件的訂用帳戶會在[App_Start \startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs)檔中發生。 這些概念也與 ASP.NET Core 中的相同，不同之處在于 ASP.NET 中，您必須在[web.config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)中指定 `RedirectUri`。 這項設定比 ASP.NET Core 中的設定低，因為您必須在部署應用程式時加以變更。

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

# <a name="javatabjava"></a>[Java](#tab/java)

請參閱[登入使用者的 Web 應用程式：程式碼](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)設定，以瞭解 JAVA 範例如何取得授權碼。 在應用程式收到程式碼之後， [AuthFilter # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56)：

1. 在[AuthHelper # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)中委派至 `AuthHelper.processAuthenticationCodeRedirect` 方法。
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

`getAuthResultByAuthCode` 方法定義于[AuthHelper # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)中。 它會建立 MSAL `ConfidentialClientApplication`，然後使用從授權碼建立的 `AuthorizationCodeParameters` 呼叫 `acquireToken()`。

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

# <a name="pythontabpython"></a>[Python](#tab/python)

如 Web 應用程式中所示，會要求授權碼流程以[登入使用者： code configuration](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)。 然後會在 `authorized` 函式上接收程式碼，該函式會 Flask 來自 `/getAToken` URL 的路由。 如需此程式碼的完整內容，請參閱[.py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) ：

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

機密用戶端應用程式也可以使用用戶端憑證或用戶端判斷提示來證明其身分識別，而不是用戶端密碼。
使用用戶端判斷提示是一個先進的案例，在[用戶端判斷](msal-net-client-assertions.md)提示中有詳細說明。

## <a name="token-cache"></a>權杖快取

> [!IMPORTANT]
> Web 應用程式或 web Api 的權杖快取執行與桌面應用程式的執行方式不同，這通常是以檔案為[基礎](scenario-desktop-acquire-token.md#file-based-token-cache)。
> 基於安全性和效能考慮，請務必確定 web 應用程式和 web Api 上的每個使用者帳戶都有一個權杖快取。 您必須將每個帳戶的權杖快取序列化。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET core 教學課程會使用相依性插入，讓您在應用程式的 Startup.cs 檔中決定權杖快取的執行。 如權杖快取序列化中所述，Microsoft Identity. Web 隨附預先建立的權杖快取[序列化](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)程式。 有一個有趣的可能性是選擇 ASP.NET Core[分散式記憶體](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)快取：

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

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

如需權杖快取提供者的詳細資訊，另請參閱[ASP.NET Core Web 應用程式教學課程 |](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)教學課程的權杖快取階段。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Web 應用程式或 web Api 的權杖快取執行與桌面應用程式的執行方式不同，這通常是以檔案為[基礎](scenario-desktop-acquire-token.md#file-based-token-cache)。

Web 應用程式的執行可以使用 ASP.NET 會話或伺服器記憶體。 例如，請參閱在 MsalAppBuilder 中建立 MSAL.NET 應用程式之後，如何連結快取實作為[L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)：

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

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL JAVA 提供序列化和還原序列化權杖快取的方法。 JAVA 範例會處理來自會話的序列化，如[AuthHelper # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)中的 `getAuthResultBySilentFlow` 方法中所示：

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

`SessionManagementHelper` 類別的詳細資料會在[適用于 JAVA 的 MSAL 範例](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)中提供。

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 範例中，每個帳戶可以有一個快取，方法是為每個要求重新建立機密用戶端應用程式，然後在 Flask 會話快取中將它序列化：

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

此時，當使用者登入時，權杖會儲存在權杖快取中。 讓我們看看它如何用於 web 應用程式的其他部分。

> [!div class="nextstepaction"]
> [呼叫 web Api 的 web 應用程式：從全域登出的快取中移除帳戶](scenario-web-app-call-api-sign-in.md)
