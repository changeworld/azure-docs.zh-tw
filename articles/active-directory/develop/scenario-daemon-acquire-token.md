---
title: 取得權杖以呼叫 web API (daemon 應用程式) -Microsoft 身分識別平臺 |蔚藍
description: '瞭解如何建立可呼叫 web Api (取得權杖的 daemon 應用程式) '
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
ms.openlocfilehash: 295897be03a7dd8e397e8202ff1cf10e6d59cdfb
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753868"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>呼叫 web Api 的 Daemon 應用程式-取得權杖

在您建立機密用戶端應用程式之後，您可以藉由呼叫 `AcquireTokenForClient` 、傳遞範圍，並選擇性地強制重新整理權杖，來取得應用程式的權杖。

## <a name="scopes-to-request"></a>要要求的範圍

用戶端認證流程要求的範圍是資源的名稱，後面接著 `/.default` 。 此標記法會告知 Azure Active Directory (Azure AD) 使用在應用程式註冊期間以靜態方式宣告的 *應用層級許可權* 。 此外，租使用者系統管理員必須授與這些 API 許可權。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

在 MSAL Python 中，設定檔看起來會像下列程式碼片段：

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD (v1.0) 資源

用戶端認證所使用的範圍應一律為資源識別碼，後面接著 `/.default` 。

> [!IMPORTANT]
> 當 MSAL 要求接受版本1.0 存取權杖之資源的存取權杖時，Azure AD 會取得最後一個斜線之前的所有內容，並將其作為資源識別碼，藉此從要求的範圍剖析所需的物件。
> 因此，如果 Azure SQL Database (**HTTPs： \/ /database.windows.net**) ，則資源預期會有一個以斜線 (結尾的物件 Azure SQL Database， `https://database.windows.net/`) ，您必須要求的範圍 `https://database.windows.net//.default` 。  (記下雙斜線。 ) 另請參閱 MSAL.NET 問題 [#747：省略資源 url 的尾端斜線，這會導致 sql 驗證失敗](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

若要取得應用程式的權杖，您將使用 `AcquireTokenForClient` 或其對等專案（視平臺而定）。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

### <a name="acquiretokenforclient-uses-the-application-token-cache"></a>AcquireTokenForClient 使用應用程式權杖快取

在 MSAL.NET 中，會 `AcquireTokenForClient` 使用應用程式權杖快取。  (所有其他 AcquireToken *XX* 方法都會使用使用者權杖快取。 `AcquireTokenSilent` 在呼叫之前，) 不會呼叫 `AcquireTokenForClient` ，因為會 `AcquireTokenSilent` 使用 *使用者* 權杖快取。 `AcquireTokenForClient` 檢查 *應用程式* 權杖快取本身，並加以更新。

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

此程式碼會從 [MSAL JAVA dev 範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)中解壓縮。

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>通訊協定

如果您還沒有所選語言的程式庫，您可能會想要直接使用通訊協定：

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>第一個案例：使用共用密碼存取權杖要求

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>第二種案例：使用憑證存取權杖要求

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

如需詳細資訊，請參閱通訊協定檔： [Microsoft 身分識別平臺和 OAuth 2.0 用戶端認證流程](v2-oauth2-client-creds-grant-flow.md)。

## <a name="troubleshooting"></a>疑難排解

### <a name="did-you-use-the-resourcedefault-scope"></a>您使用資源/. 預設範圍嗎？

如果您收到錯誤訊息，告知您使用了不正確範圍，您可能未使用該 `resource/.default` 範圍。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>您是否忘了提供管理員同意？ Daemon 應用程式需要！

如果您在呼叫 API 時， **沒有足夠的許可權來完成** 作業錯誤，租使用者系統管理員必須將許可權授與應用程式。 請參閱上述註冊用戶端應用程式的步驟6。
您通常會看到類似下列錯誤的錯誤：

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

### <a name="are-you-calling-your-own-api"></a>您是否呼叫自己的 API？

如果您呼叫自己的 web API，但無法將應用程式許可權新增至您的 daemon 應用程式的應用程式註冊，您是否會在 web API 中公開應用程式角色？

如需詳細資訊，請參閱 [公開應用程式角色 (的應用程式角色) ](scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) ，尤其 [是確保 Azure AD 會將 web API 的權杖簽發給僅允許的用戶端](scenario-protected-web-api-app-registration.md#ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients)。

## <a name="next-steps"></a>後續步驟

# <a name="net"></a>[.NET](#tab/dotnet)

請移至本案例的下一篇文章，以 [呼叫 WEB API](./scenario-daemon-call-api.md?tabs=dotnet)。

# <a name="python"></a>[Python](#tab/python)

請移至本案例的下一篇文章，以 [呼叫 WEB API](./scenario-daemon-call-api.md?tabs=python)。

# <a name="java"></a>[Java](#tab/java)

請移至本案例的下一篇文章，以 [呼叫 WEB API](./scenario-daemon-call-api.md?tabs=java)。

---
