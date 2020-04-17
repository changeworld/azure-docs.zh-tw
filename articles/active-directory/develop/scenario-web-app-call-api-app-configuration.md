---
title: 設定呼叫 Web API 的 Web 應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何設定呼叫 Web API 的 Web 應用程式的代碼
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
ms.openlocfilehash: 7f05d33b43df85c49a0c92b60157e2a6448325ac
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537129"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>呼叫 Web API 的 Web 應用:程式碼設定

如在使用者方案中[簽名](scenario-web-app-sign-user-overview.md)的 Web 應用所示,Web 應用使用[OAuth 2.0 授權代碼流](v2-oauth2-auth-code-flow.md)登錄使用者。 此流有兩個步驟:

1. 請求授權代碼。 此部分將使用者的私人對話委託給 Microsoft 標識平臺。 在對話期間,用戶登錄並同意使用 Web API。 當專用對話成功結束時,Web 應用在其重定向 URI 上收到授權代碼。
1. 通過兌換授權代碼請求 API 的訪問權杖。

[在使用者方案中簽名](scenario-web-app-sign-user-overview.md)的 Web 應用僅涵蓋第一步。 在這裡,您將學習如何修改 Web 應用,以便它不僅在使用者中簽名,而且現在還會調用 Web API。

## <a name="libraries-that-support-web-app-scenarios"></a>支援 Web 應用專案的庫

Microsoft 身份驗證函式庫 (MSAL) 中的以下函式庫支援 Web 應用的授權代碼串流:

| MSAL 程式庫 | 描述 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援 .NET 框架和 .NET 核心平臺。 不支援通用 Windows 平臺 (UWP)、Xamarin.iOS 和 Xamarin.Android,因為這些平臺用於構建公共用戶端應用程式。 |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> 適用於 Python 的 MSAL | 支援 Python Web 應用程式。 |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> 適用於 Java 的 MSAL | 支援 Java Web 應用程式。 |

選擇您感興趣的平台的選項卡:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

由於使用者登錄委託給開放 ID 連接 (OIDC) 中間件,因此您必須與 OIDC 進程進行交互。 互動方式取決於您使用的框架。

對於ASP.NET核心,您將訂閱中間件 OIDC 事件:

- 您將允許ASP.NET Core 透過Open ID 連接中間件請求授權代碼。 ASP.NET或ASP.NET核心將允許用戶登錄並同意。
- 您將訂閱 Web 應用以接收授權代碼。 此訂閱使用 C# 委託完成。
- 收到授權代碼後,您將使用 MSAL 庫來兌換它。 生成的訪問權杖和刷新權杖儲存在權杖快取中。 緩存可用於應用程式的其他部分(如控制器)以靜默方式獲取其他權杖。

本文中的代碼示例和以下範例摘自[ASP.NET 酷 Web 應用增量教程,第 2 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)。 您可能需要參考該教程瞭解完整的實現詳細資訊。

> [!NOTE]
> 要完全理解此處的代碼範例,您需要熟悉[ASP.NET 核心基礎知識](https://docs.microsoft.com/aspnet/core/fundamentals),特別是[相依項注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)與[選項](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

由於使用者登錄委託給開放 ID 連接 (OIDC) 中間件,因此您必須與 OIDC 進程進行交互。 互動方式取決於您使用的框架。

對於ASP.NET,您將訂閱中間件 OIDC 事件:

- 您將允許ASP.NET Core 透過Open ID 連接中間件請求授權代碼。 ASP.NET或ASP.NET核心將允許用戶登錄並同意。
- 您將訂閱 Web 應用以接收授權代碼。 此訂閱使用 C# 委託完成。
- 收到授權代碼後,您將使用 MSAL 庫來兌換它。 生成的訪問權杖和刷新權杖儲存在權杖快取中。 緩存可用於應用程式的其他部分(如控制器)以靜默方式獲取其他權杖。

本文中的代碼示例和以下範例是從[ASP.NET Web 應用範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)中提取的。 您可能需要參考該範例瞭解完整的實現詳細資訊。

# <a name="java"></a>[Java](#tab/java)

本文中的代碼示例和下面的代碼示例是從調用 Microsoft Graph 的[Java Web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)中提取的,這是一個使用 MSAL 進行 Java 的 Web 應用範例。
該示例目前允許 JAVA 的 MSAL 生成授權代碼網址並處理 Microsoft 標識平台的授權終結點的導航。 也可以使用 Sprint 安全登錄使用者。 您可能需要參考範例瞭解完整的實現詳細資訊。

# <a name="python"></a>[Python](#tab/python)

本文中的代碼示例和下面的代碼示例是從稱為 Microsoft Graph 的[Python Web 應用程式](https://github.com/Azure-Samples/ms-identity-python-webapp)中提取的,這是一個使用 MSAL 的 Web 應用範例。Python。
該示例當前允許 MSAL。Python 生成授權代碼網址並處理 Microsoft 標識平台的授權終結點的導航。 您可能需要參考範例瞭解完整的實現詳細資訊。

---

## <a name="code-that-redeems-the-authorization-code"></a>兌換授權代碼的代碼

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

在ASP.NET核心中,在`Startup.cs`檔案中,您訂閱`OnAuthorizationCodeReceived`OpenID 連接事件。 從此事件中,調用MSAL.NET`AcquireTokenFromAuthorizationCode`方法。 此方法在權杖快取中儲存以下令牌:

- 請求*access token*`scopes`的訪問權杖。
- *重新載入權杖*。 此令牌將用於在訪問權杖接近到期時刷新該權杖,或代表同一使用者獲取另一個權杖,但用於其他資源。

[ASP.NET酷酷 Web 應用教學](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)為您提供了 Web 應用的可重用代碼。

以下是[來自 Startup.cs_L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42)的代碼。 它具有呼叫:

- 方法`AddMicrosoftIdentityPlatformAuthentication`,該方法向 Web 應用添加身份驗證。
- 該方法`AddMsal`,它添加了調用 Web API 的功能。
- 方法`AddInMemoryTokenCaches`,即選擇令牌緩存實現。

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

`Constants.ScopeUserRead`在[常數中定義 cs_L5 :](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

您已經研究了在 Web`AddMicrosoftIdentityPlatformAuthentication`應用程式中 簽署的內容[─代碼設定](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)。

### <a name="the-addmsal-method"></a>AddMsal 方法

的代碼`AddMsal`位於[Microsoft.標識.Web/WebAppService 收集擴展.cs_L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)。

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

該方法`AddMsal`可確保:

- ASP.NET酷睿 Web 應用同時請求使用者的 ID 權杖`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`和身份驗證代碼 ()。
- 將`offline_access`添加作用域。 此作用域獲取使用者同意應用程序獲取刷新權杖。
- 這個應用程式訂閱 OIDC`OnAuthorizationCodeReceived`事件並使用 MSAL.NET 來呼叫,該 MSAL.NET 封裝在此處,可`ITokenAcquisition`重用的元件實現 。

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>權杖取得.從授權代碼async方法加入帳戶快取

該方法`TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync`位於[Microsoft.標識.Web/TokenAcquisition.cs_L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)。 它確保:

- ASP.NET 不會嘗試與 MSAL.NET 並行兌換身份`context.HandleCodeRedemption();`驗證代碼 。
- ID 權杖中的聲明可用於 MSAL 計算使用者帳戶的權杖快取金鑰。
- 如有必要,將創建MSAL.NET應用程式的實例。
- 代碼由MSAL.NET應用程式兌換。
- 在調用`context.HandleCodeRedemption(null, result.IdToken);`期間,將ASP.NET核心共用新的 ID 權杖。 訪問權杖不與ASP.NET核心共用。 它保留在與使用者關聯的MSAL.NET權杖緩存中,即可在ASP.NET核心控制器中使用。

下面是`TokenAcquisition`: 的相關代碼:

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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>權杖取得.產生機密客戶端應用程式方法

在 ASP.NET 核心中,建構機密客戶端應用程式`HttpContext`將使用中的資訊 。 使用`HttpContext``CurrentHttpContext`屬性訪問與請求關聯的關聯。 `HttpContext`包含有關 Web 應用的 URL 和登錄`ClaimsPrincipal`使用者的資訊(在中)。

該方法`BuildConfidentialClientApplication`還使用ASP.NET核心配置。 設定具有「AzureAD」部分,並且還綁定到以下兩個元素:

- `_applicationOptions`[類型機密客戶端應用程式選項](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)的數據結構。
- `azureAdOptions` [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)類型的實例,在`Authentication.AzureAD.UI`ASP.NET核心 中定義。

最後,應用程式需要維護令牌緩存。 您將在下一節中瞭解有關這一點的更多詳細資訊。

`GetOrBuildConfidentialClientApplication()`該方法的代碼在[Microsoft.標識.Web/TokenAcquisition.cs_L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333)中。 它使用透過相依性的成員(`TokenAcquisition`在 Microsoft 中的建構函數中傳遞[。](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)

下面是`GetOrBuildConfidentialClientApplication`: 的代碼:

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

`AcquireTokenByAuthorizationCode`實際上是ASP.NET請求的授權代碼,並獲取添加到MSAL.NET使用者權杖緩存的權杖的方法。 然後,在ASP.NET核心控制器中使用權杖。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET處理類似ASP.NET酷,只不過 OpenID Connect`OnAuthorizationCodeReceived`的配置和 事件的訂閱發生在[App_Start_Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs)檔中。 這些概念也類似於ASP.NET核心中的概念,只不過在ASP.NET必須在`RedirectUri`[Web.config_L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)中指定 。 此配置比 ASP.NET 酷中配置的配置要強一些,因為在部署應用程式時需要更改它。

下面是Startup.Auth.cs的代碼:

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

請參閱[在使用者中簽名的 Web 應用程式:程式碼設定](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)以瞭解 Java 範例如何取得授權代碼。 套用收到代碼後[,AuthFilter.java_L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. 在`AuthHelper.processAuthenticationCodeRedirect`[AuthHelper.java_L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)中對該方法的委託。
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

該方法`getAuthResultByAuthCode`在[AuthHelper.java_L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)中定義。 它建立 MSAL,`ConfidentialClientApplication``acquireToken()``AuthorizationCodeParameters`然後使用從授權代碼建立的呼叫。

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

要求授權代碼串流,如[在使用者中簽名的 Web 應用程式中所示:代碼設定](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)。 然後在函數上`authorized`接收代碼,該函數會`/getAToken`從 URL 中跳蚤路由。 有關此代碼的完整上下文,請參閱[app.py_L30-L44:](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

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

機密用戶端應用程式還可以使用用戶端證書或客戶端斷言來證明其身份,而不是用戶端機密。
用戶端斷言的使用是一個高級方案,詳見[客戶端斷言](msal-net-client-assertions.md)。

## <a name="token-cache"></a>權杖快取

> [!IMPORTANT]
> Web 應用程式或 Web API 的權取快取與桌面應用程式的完整,桌面應用程式通常[是檔案](scenario-desktop-acquire-token.md#file-based-token-cache)。
> 出於安全性和性能原因,請務必確保對於 Web 應用和 Web API,每個使用者帳戶有一個令牌緩存。 您必須序列化每個帳戶的權杖快取。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET核心教程使用依賴項注入來決定應用程式Startup.cs檔中的權杖緩存實現。 Microsoft.Identity.Web 附帶了[令牌緩存序列化](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)中描述的預構建令牌緩存序列化器。 一個有趣的可能性是選擇 ASP.NET 核心[分散式記憶體快取](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

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

有關令牌緩存提供程式的詳細資訊,請參閱[ASP.NET核心 Web 應用教程 |令牌緩存](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)教程的階段。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Web 應用程式或 Web API 的權取快取與桌面應用程式的完整,桌面應用程式通常[是檔案](scenario-desktop-acquire-token.md#file-based-token-cache)。

Web 應用實現可以使用ASP.NET會話或伺服器記憶體。 例如,請參閱在[MsalAppBuilder.cs_L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)中創建MSAL.NET應用程式後如何掛接緩存實現:

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

MSAL Java 提供了序列化和反序列化令牌緩存的方法。 Java 示例處理工作階段中的序列化,`getAuthResultBySilentFlow`如[AuthHelper.java_L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)中的方法所示:

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

`SessionManagementHelper`該類的詳細資訊在 JAVA 的[MSAL 範例中](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)提供。

# <a name="python"></a>[Python](#tab/python)

在 Python 範例中,透過每個請求重新建立機密客戶端應用程式,然後在 Flask 工作階段快取中序列化,確保每個帳戶有一個緩存:

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

此時,當用戶登錄時,令牌存儲在令牌緩存中。 讓我們看看它是如何在 Web 應用的其他部分使用的。

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web 應用:從全域登出時從快取中移除帳戶](scenario-web-app-call-api-sign-in.md)
