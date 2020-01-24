---
title: 取得權杖以呼叫 Web API （daemon 應用程式）-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立可呼叫 web Api （取得權杖）的 daemon 應用程式
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
ms.openlocfilehash: 38df99f0a4932f477e900382c7ff1ae7b50febe9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702465"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>呼叫 web Api 的 Daemon 應用程式-取得權杖

一旦結構化機密用戶端應用程式，您就可以藉由呼叫 ``AcquireTokenForClient``、傳遞範圍，以及強制或不重新整理權杖，來取得應用程式的權杖。

## <a name="scopes-to-request"></a>要求的範圍

要求用戶端認證流程的範圍是資源的名稱，後面接著 `/.default`。 此標記法會告訴 Azure AD 在應用程式註冊期間，使用靜態宣告的**應用層級許可權**。 此外，如先前所見，這些 API 許可權必須由租使用者系統管理員授與

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在 MSAL Python 中，設定檔看起來會像下列程式碼片段：

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Azure AD （v1.0）資源的案例

用於用戶端認證的範圍應該一律是 resourceId + "/.default"

> [!IMPORTANT]
> 對於要求接受 v1.0 存取權杖的資源存取權杖的 MSAL，Azure AD 會在最後一個斜線之前取得所有專案，並使用它作為資源識別碼，以從要求的範圍中剖析所需的物件。
> 因此，如果 Azure SQL （ **https://database.windows.net** ）資源預期以斜線結尾的物件（適用于 azure sql： `https://database.windows.net/` ），您將需要要求 `https://database.windows.net//.default` 的範圍（請注意雙斜線）。 另請參閱 MSAL.NET 問題[#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)：省略資源 url 的尾端斜線，這會導致 sql 驗證失敗。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

若要取得應用程式的權杖，您將使用 `AcquireTokenForClient` 或對等的（視平臺而定）。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

這是[MSAL JAVA dev 範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)的摘錄。

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>通訊協定

如果您還沒有適用于您所選語言的程式庫，您可能會想要直接使用此通訊協定：

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一種情況︰使用共用密碼的存取權杖要求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>第二種情況︰使用憑證的存取權杖要求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
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

在 MSAL.NET 中，`AcquireTokenForClient` 使用**應用程式權杖**快取（所有其他的 AcquireTokenXX 方法使用使用者 token 快取）在呼叫 `AcquireTokenForClient` 之前，不會呼叫 `AcquireTokenSilent`，因為 `AcquireTokenSilent` 會使用**使用者**權杖快取。 `AcquireTokenForClient` 會檢查**應用程式**權杖快取本身並加以更新。

## <a name="troubleshooting"></a>疑難排解

### <a name="did-you-use-the-resourcedefault-scope"></a>您使用的是 resource/. 預設範圍嗎？

如果您收到錯誤訊息，告知您使用了不正確範圍，您可能未使用 `resource/.default` 範圍。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>您忘了提供系統管理員同意嗎？ Daemon 應用程式需要！

如果您在呼叫 API 的**許可權不足而無法完成**作業時收到錯誤，則租使用者系統管理員必須授與許可權給應用程式。 請參閱上面的註冊用戶端應用程式的步驟6。
您通常會看到和錯誤，如下列錯誤描述所示：

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

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon 應用程式-呼叫 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon 應用程式-呼叫 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon 應用程式-呼叫 Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
