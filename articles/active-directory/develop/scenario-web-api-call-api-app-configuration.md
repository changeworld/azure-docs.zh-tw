---
title: 設定呼叫 web Api 的 Web API |Azure
titleSuffix: Microsoft identity platform
description: '瞭解如何建立 Web API，以呼叫 web Api (應用程式的程式碼設定) '
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/05/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 29c57411a2a35c36d0b4a9d4def931821b795094
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121131"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>呼叫 web Api 的 Web API：程式碼設定

註冊 Web API 之後，您可以設定應用程式的程式碼。

您用來設定 Web API 的程式碼，讓它呼叫下游 web Api 建置於用來保護 Web API 的程式碼之上。 如需詳細資訊，請參閱[Protected Web API：應用程式](scenario-protected-web-api-app-configuration.md)設定。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>用戶端密碼或用戶端憑證

假設您的 Web API 現在會呼叫下游 Web API，您必須在檔案的*appsettings.js*中提供用戶端密碼或用戶端憑證。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 }
}
```

您可以提供用戶端憑證，而不是用戶端密碼。 下列程式碼片段示範如何使用儲存在 Azure Key Vault 中的憑證。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
  ]
 }
}
```

Microsoft 會提供數種方式來描述憑證，無論是透過設定或透過程式碼。 如需詳細資訊，請參閱[Microsoft. Identity. Web wiki-使用](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates)GitHub 上的憑證。

## <a name="startupcs"></a>Startup.cs

使用 Startup.cs 時，如果您想要讓 Web API 呼叫下游 Web Api，請在 `.AddMicrosoftWebApiCallsWebApi()` 後面新增一行 `.AddMicrosoftWebApiAuthentication(Configuration)` ，然後選擇權杖快取的執行，例如 `.AddInMemoryTokenCaches()` ，在*Startup.cs*中：

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
   services.AddMicrosoftWebApiAuthentication(Configuration)
           .AddMicrosoftWebApiCallsWebApi(Configuration)
           .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

如同 web apps，您可以選擇各種權杖快取實現。 如需詳細資訊，請參閱 GitHub 上的[Microsoft 身分識別 web wiki-權杖](https://aka.ms/ms-id-web/token-cache-serialization)快取序列化。

如果您確定您的 Web API 需要特定範圍，您可以選擇性地將它們當做引數傳遞至 `AddMicrosoftWebApiCallsWebApi` 。

# <a name="java"></a>[Java](#tab/java)

代理者 (OBO) 流程可用來取得權杖，以呼叫下游 Web API。 在此流程中，您的 Web API 會從用戶端應用程式接收具有使用者委派許可權的持有人權杖，然後將此權杖交換給另一個存取權杖，以呼叫下游 Web API。

下列程式碼會在 Web API 中使用春季安全性架構 `SecurityContextHolder` ，以取得已驗證的持有人權杖。 接著，它會使用 MSAL JAVA 程式庫，透過呼叫來取得下游 API 的權杖 `acquireToken` `OnBehalfOfParameters` 。 MSAL 會快取權杖，讓後續對 API 的呼叫可以使用 `acquireTokenSilently` 來取得緩存的權杖。

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

代理者 (OBO) 流程可用來取得權杖，以呼叫下游 Web API。 在此流程中，您的 Web API 會從用戶端應用程式接收具有使用者委派許可權的持有人權杖，然後將此權杖交換給另一個存取權杖，以呼叫下游 Web API。

Python Web API 必須使用一些中介軟體來驗證從用戶端收到的持有人權杖。 然後，Web API 可以藉由呼叫方法，使用 MSAL Python 程式庫取得下游 API 的存取權杖 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 。 如需使用此 API 的範例，請參閱[GitHub 上的 microsoft 驗證-適用于 python 的測試程式碼](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472)。 另請參閱此相同存放庫中的[問題 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53)討論，以取得略過中介層應用程式需求的方法。

---

您也可以在[Node.js 和 Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61)中查看 OBO 流程執行的範例。

## <a name="protocol"></a>通訊協定

如需 OBO 通訊協定的詳細資訊，請參閱[Microsoft 身分識別平臺和 OAuth 2.0](./v2-oauth2-on-behalf-of-flow.md)代理者流程。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 Web API：取得應用程式的權杖](scenario-web-api-call-api-acquire-token.md)