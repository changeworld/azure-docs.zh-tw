---
title: 取得權杖以呼叫 Web API （daemon 應用程式）-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立可呼叫 web Api （取得權杖）的 daemon 應用程式
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
ms.openlocfilehash: d755573b53eb63d85165fb73fe4b97298dbeff09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868984"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>呼叫 web Api 的 Daemon 應用程式-取得權杖

在您已建立機密用戶端應用程式之後，您可以藉由呼叫`AcquireTokenForClient`、傳遞範圍，並選擇性地強制重新整理權杖，來取得應用程式的權杖。

## <a name="scopes-to-request"></a>要求的範圍

要求用戶端認證流程的範圍是資源的名稱，後面接著`/.default`。 此標記法會告訴 Azure Active Directory （Azure AD）使用在應用程式註冊期間以靜態方式宣告的*應用層級許可權*。 此外，租使用者系統管理員必須授與這些 API 許可權。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

在 MSAL Python 中，設定檔看起來就像下面這個程式碼片段：

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

### <a name="azure-ad-v10-resources"></a>Azure AD （v1.0）資源

用於用戶端認證的範圍應該一律是資源識別碼，後面接著`/.default`。

> [!IMPORTANT]
> 當 MSAL 針對接受版本1.0 存取權杖的資源要求存取權杖時，Azure AD 會在最後一個斜線之前取得所有專案，並使用它做為資源識別碼，以從要求的範圍中剖析所需的物件。
> 因此，如果 Azure SQL Database （**HTTPs：\//database.windows.net**），資源預期以斜線結尾的物件（適用于 Azure SQL Database `https://database.windows.net/`），您將需要要求的範圍`https://database.windows.net//.default`。 （請注意雙斜線）。另請參閱 MSAL.NET 問題[#747：省略資源 url 的尾端斜線，這會導致 sql 驗證失敗](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

若要取得應用程式的權杖，您將使用`AcquireTokenForClient`或其對等的（視平臺而定）。

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

這段程式碼是從[MSAL JAVA dev 範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)中解壓縮而來。

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

如果您還沒有所選語言的程式庫，您可能會想要直接使用此通訊協定：

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>第一種案例：使用共用密碼存取權杖要求

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>第二個案例：使用憑證存取權杖要求

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

## <a name="application-token-cache"></a>應用程式權杖快取

在 MSAL.NET 中`AcquireTokenForClient` ，會使用應用程式權杖快取。 （所有其他 AcquireToken*XX*方法都會使用使用者 token 快取）。請不要`AcquireTokenSilent`在呼叫`AcquireTokenForClient`之前呼叫， `AcquireTokenSilent`因為會使用*使用者*token 快取。 `AcquireTokenForClient`檢查*應用程式*權杖快取本身並加以更新。

## <a name="troubleshooting"></a>疑難排解

### <a name="did-you-use-the-resourcedefault-scope"></a>您使用的是 resource/. 預設範圍嗎？

如果您收到錯誤訊息，告知您使用了不正確範圍，表示您可能未使用`resource/.default`範圍。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>您忘了提供系統管理員同意嗎？ Daemon 應用程式需要！

如果您在呼叫 API 時取得的**許可權不足，無法完成**作業錯誤，租使用者系統管理員必須授與許可權給應用程式。 請參閱上面的註冊用戶端應用程式的步驟6。
您通常會看到類似此錯誤的錯誤：

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

## <a name="next-steps"></a>後續步驟

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon 應用程式-呼叫 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon 應用程式-呼叫 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon 應用程式-呼叫 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
