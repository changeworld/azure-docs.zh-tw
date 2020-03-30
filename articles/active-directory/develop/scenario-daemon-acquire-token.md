---
title: 獲取權杖來調用 Web API（守護進程應用） - 微軟身份平臺 |蔚藍
description: 瞭解如何構建調用 Web API 的守護進程應用（獲取權杖）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7f1010949a72f95ef2836c43666e6cea9281e04d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262642"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>調用 Web API 的守護程式應用 - 獲取權杖

構造機密用戶端應用程式後，可以通過調用`AcquireTokenForClient`、傳遞作用域以及選擇性地強制刷新權杖來獲取應用程式的權杖。

## <a name="scopes-to-request"></a>要請求的範圍

請求用戶端憑據流的範圍是後跟`/.default`的資源的名稱。 此標記法告訴 Azure 活動目錄 （Azure AD） 使用應用程式*級許可權*在應用程式註冊期間靜態聲明。 此外，租戶管理員必須授予這些 API 許可權。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

在 MSAL Python 中，設定檔如下所示的程式碼片段：

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[JAVA](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD （v1.0） 資源

用於用戶端憑據的範圍應始終為 後跟的資源`/.default`ID。

> [!IMPORTANT]
> 當 MSAL 請求接受版本 1.0 訪問權杖的資源的訪問權杖時，Azure AD 會通過在最後一個斜杠之前獲取所有內容並將其用作資源識別碼，從請求的作用域解析所需的訪問群體。
> 因此，如果與 Azure SQL 資料庫 **（HTTPs：\//database.windows.net）** 一樣，資源需要以斜杠結束（`https://database.windows.net/`對於 Azure SQL 資料庫），則需要請求`https://database.windows.net//.default`的範圍。 （注意雙斜杠。另請參閱MSAL.NET問題[#747：資源 URL 的尾隨斜杠被省略，這會導致 sql auth 失敗](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

## <a name="acquiretokenforclient-api"></a>獲取權杖用於用戶端 API

要獲取應用的權杖，您將使用`AcquireTokenForClient`或等效應用，具體取決於平臺。

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

# <a name="java"></a>[JAVA](#tab/java)

此代碼是從[MSAL JAVA 開發示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)中提取的。

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

如果尚未為所選語言建立庫，則可能需要直接使用該協定：

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>第一種情況：使用共用金鑰訪問權杖請求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>第二種情況：使用證書訪問權杖請求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

有關詳細資訊，請參閱協定文檔[：Microsoft 標識平臺和 OAuth 2.0 用戶端憑據流](v2-oauth2-client-creds-grant-flow.md)。

## <a name="application-token-cache"></a>應用程式權杖緩存

在MSAL.NET，`AcquireTokenForClient`使用應用程式權杖緩存。 （所有其他獲取權杖*XX*方法都使用使用者權杖緩存。`AcquireTokenSilent`在調用`AcquireTokenForClient`之前不要調用，因為`AcquireTokenSilent`使用*使用者*權杖緩存。 `AcquireTokenForClient`檢查*應用程式*權杖緩存本身並對其進行更新。

## <a name="troubleshooting"></a>疑難排解

### <a name="did-you-use-the-resourcedefault-scope"></a>您是否使用資源/.預設作用域？

如果收到一條錯誤訊息，告訴您使用了無效作用域，則可能沒有使用該`resource/.default`作用域。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>您是否忘記提供管理員同意？ 守護神應用程式需要它！

如果在調用 API 時獲得**完成操作錯誤的許可權不足**，租戶管理員需要向應用程式授予許可權。 請參閱上面註冊用戶端應用的步驟 6。
您通常會看到如下所示的錯誤：

```JSon
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
> [守護進程應用 - 調用 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [守護進程應用 - 調用 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[JAVA](#tab/java)

> [!div class="nextstepaction"]
> [守護進程應用 - 調用 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
