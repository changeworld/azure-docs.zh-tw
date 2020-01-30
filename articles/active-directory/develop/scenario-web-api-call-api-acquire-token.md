---
title: 為呼叫 web Api 的 Web API 取得權杖 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立 Web API，以呼叫需要取得應用程式權杖的 web Api。
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2721837459af24f39bb15ee17d394345cbb37eb1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834105"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>呼叫 web Api 的 Web API：取得應用程式的權杖

建立用戶端應用程式物件之後，請使用它來取得可用於呼叫 Web API 的權杖。

## <a name="code-in-the-controller"></a>控制器中的程式碼

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

以下是在 API 控制器的動作中呼叫的程式碼範例。 它會呼叫名為*todolist*的下游 API。

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` 類似于[Web API 中呼叫 Web api 的案例：應用程式](scenario-web-api-call-api-app-configuration.md)設定。 `BuildConfidentialClient()` 具現化具有僅包含一個帳戶資訊之快取的 `IConfidentialClientApplication`。 此帳戶是由 `GetAccountIdentifier` 方法所提供。

`GetAccountIdentifier` 方法會使用與使用者身分識別相關聯的宣告，其 Web API 收到 JSON Web 權杖（JWT）：

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="javatabjava"></a>[Java](#tab/java)
以下是在 API 控制器的動作中呼叫的程式碼範例。 它會呼叫下游 API Microsoft Graph。

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python Web API 必須使用一些中介軟體來驗證從用戶端收到的持有人權杖。 然後，Web API 可以藉由呼叫[`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of)方法，使用 MSAL Python 程式庫取得下游 API 的存取權杖。 尚未提供使用 MSAL Python 來示範此流程的範例。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 Web API：呼叫 API](scenario-web-api-call-api-call-api.md)
