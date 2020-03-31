---
title: 配置調用 Web API 的 Web API |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何構建調用 Web API 的 Web API（應用的代碼配置）
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 82b5e1d9753fbb65fd81f24b06016d302457144e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834088"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>調用 Web API 的 Web API：代碼配置

註冊 Web API 後，可以配置應用程式的代碼。

用於配置 Web API 以便調用下游 Web API 的代碼基於用於保護 Web API 的代碼。 有關詳細資訊，請參閱受保護的[Web API：應用配置](scenario-protected-web-api-app-configuration.md)。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>訂閱到 OnToken 驗證的代碼

除了任何受保護的 Web API 的代碼配置外，還需要訂閱調用 API 時收到的無記名權杖的驗證：

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>代表流程

添加帳戶從Jwt（）方法需要：

- 具現化 Microsoft 身份驗證庫 （MSAL） 機密用戶端應用程式。
- 呼叫 `AcquireTokenOnBehalf` 方法。 此調用將用戶端為 Web API 獲取的無記名權杖與同一使用者的無記名權杖交換，但它具有 API 呼叫下游 API。

### <a name="instantiate-a-confidential-client-application"></a>具現化機密用戶端應用程式

此流僅在機密用戶端流中可用，因此受保護的 Web API 通過`WithClientSecret`或 方法向[機密用戶端應用程式產生器類](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)提供用戶端憑據（用戶端金鑰或`WithCertificate`證書）。

![I機密用戶端應用程式方法清單](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

最後，機密用戶端應用程式無需通過用戶端機密或證書來證明其身份，而是可以使用用戶端斷言來證明其身份。
有關此高級方案的詳細資訊，請參閱[機密用戶端斷言](msal-net-client-assertions.md)。

### <a name="how-to-call-on-behalf-of"></a>如何代表

通過在`IConfidentialClientApplication`介面上調用[收購權杖代理方法](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder)進行代表 （OBO） 調用。

類`UserAssertion`是從 Web API 從其自己的用戶端接收的承載權杖生成的。 有[兩個建構函式](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)：
* 一個採用 JSON Web 權杖 （JWT） 無記名權杖的權杖
* 一種採用任何類型的使用者斷言，另一種安全權杖，其類型隨後在名為附加參數中指定`assertionType`

![使用者斷言屬性和方法](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

實際上，OBO 流通常用於獲取下游 API 的權杖並將其存儲在MSAL.NET使用者權杖緩存中。 執行此操作是為了 Web API 的其他部分以後可以調用 的``AcquireTokenOnSilent``[重寫](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet)來調用下游 API。 如果需要，此調用具有刷新權杖的效果。

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
# <a name="java"></a>[JAVA](#tab/java)

代表 （OBO） 流用於獲取調用下游 Web API 的權杖。 在此流中，Web API 從用戶端應用程式接收具有使用者委派許可權的承載權杖，然後將此權杖交換為另一個訪問權杖以調用下游 Web API。

以下代碼使用 Web API`SecurityContextHolder`中的 Spring 安全框架來獲取經過驗證的承載權杖。 然後，它使用 MSAL JAVA 庫使用`acquireToken`調用 獲取下游 API`OnBehalfOfParameters`的權杖。 MSAL 緩存權杖，以便對 API 的後續調用可用於`acquireTokenSilently`獲取緩存的權杖。

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

代表 （OBO） 流用於獲取調用下游 Web API 的權杖。 在此流中，Web API 從用戶端應用程式接收具有使用者委派許可權的承載權杖，然後將此權杖交換為另一個訪問權杖以調用下游 Web API。

Python Web API 需要使用一些中介軟體來驗證從用戶端收到的無記名權杖。 然後，Web API 可以使用 MSAL Python 庫調用[`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of)方法獲取下游 API 的訪問權杖。 使用 MSAL Python 演示此流的示例尚不可用。

---

您還可以在[Node.js 和 Azure 函數](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)中看到 OBO 流實現的示例。

## <a name="protocol"></a>通訊協定

有關 OBO 協定的詳細資訊，請參閱[Microsoft 標識平臺和 OAuth 2.0 代表流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [調用 Web API 的 Web API：獲取應用的權杖](scenario-web-api-call-api-acquire-token.md)
