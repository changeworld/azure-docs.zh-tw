---
title: 設定呼叫 web Api 的 Web API |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立呼叫 web Api 的 Web API （應用程式的程式碼設定）
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
ms.openlocfilehash: dd38cb58e6e6db9767be9adb8f299107601de580
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701768"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>呼叫 web Api 的 Web API：程式碼設定

註冊 Web API 之後，您可以設定應用程式的程式碼。

您用來設定 Web API 的程式碼，讓它呼叫下游 web Api 建置於用來保護 Web API 的程式碼之上。 如需詳細資訊，請參閱[Protected Web API：應用程式](scenario-protected-web-api-app-configuration.md)設定。

## <a name="code-subscribed-to-ontokenvalidated"></a>已訂閱 OnTokenValidated 的程式碼

在任何受保護 web Api 的程式碼設定上，您必須訂閱在呼叫您的 API 時所收到的持有人權杖驗證：

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

## <a name="on-behalf-of-flow"></a>代理者流程

AddAccountToCacheFromJwt （）方法必須：

- 將 Microsoft 驗證程式庫（MSAL）機密用戶端應用程式具現化。
- 呼叫 `AcquireTokenOnBehalf` 方法。 此呼叫會將用戶端為 Web API 所取得的持有人權杖，與相同使用者的持有人權杖交換，但它具有 API 呼叫下游 API。

### <a name="instantiate-a-confidential-client-application"></a>具現化機密用戶端應用程式

此流程僅適用于機密用戶端流程，因此受保護的 Web API 會透過 `WithClientSecret` 或 `WithCertificate` 方法，提供用戶端認證（用戶端密碼或憑證）給[ConfidentialClientApplicationBuilder 類別](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)。

![IConfidentialClientApplication 方法的清單](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

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

最後，機密用戶端應用程式可以使用用戶端判斷提示來證明其身分識別，而不是透過用戶端密碼或憑證來證明其身分識別。
如需這個 advanced 案例的詳細資訊，請參閱[機密用戶端判斷](msal-net-client-assertions.md)提示。

### <a name="how-to-call-on-behalf-of"></a>如何呼叫代理者

您可以在 `IConfidentialClientApplication` 介面上呼叫[AcquireTokenOnBehalf 方法](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder)，以建立代理者（OBO）呼叫。

`UserAssertion` 類別是從本身的用戶端由 Web API 所接收的持有人權杖所建立。 有[兩種構造](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)函式：
* 一個使用 JSON Web 權杖（JWT）持有人權杖
* 其中一個會採用任何類型的使用者判斷提示，另一種安全性權杖，其型別會在名為的其他參數中指定 `assertionType`

![UserAssertion 屬性和方法](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

實際上，OBO 流程通常用來取得下游 API 的權杖，並將它儲存在 MSAL.NET 使用者權杖快取中。 您可以這樣做，讓 Web API 的其他部分稍後可以呼叫 ``AcquireTokenOnSilent`` 的[覆寫](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet)來呼叫下游 api。 此呼叫具有重新整理權杖的效果（如有需要）。

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

您也可以在[node.js 和 Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)中查看 OBO 流程執行的範例。

## <a name="protocol"></a>通訊協定

如需 OBO 通訊協定的詳細資訊，請參閱[Microsoft 身分識別平臺和 OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)代理者流程。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 Web API：取得應用程式的權杖](scenario-web-api-call-api-acquire-token.md)
