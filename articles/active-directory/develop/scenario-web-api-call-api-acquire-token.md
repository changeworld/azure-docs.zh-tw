---
title: 為呼叫 web Api 的 Web API 取得權杖 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立 Web API，以呼叫需要取得應用程式權杖的 web Api。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7e0701cc5a9bb14800a48e2281dba1eb6ea0cf72
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026453"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>呼叫 web Api 的 Web API：取得應用程式的權杖

建立用戶端應用程式物件之後，請使用它來取得可用於呼叫 Web API 的權杖。

## <a name="code-in-the-controller"></a>控制器中的程式碼

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

以下是使用在 API 控制器的動作中呼叫的 Microsoft 程式碼範例。 它會呼叫名為*todolist*的下游 API。 若要取得權杖以呼叫下游 API，您可以在控制器的函式中，透過相依性 `ITokenAcquisition` 插入來插入服務（如果您使用 Blazor，則會使用您的頁面處理常式），並將它用於控制器動作中，取得使用者的權杖（ `GetAccessTokenForUserAsync` ），或在背景工作案例的情況下，為應用程式本身（ `GetAccessTokenForAppAsync` ）。

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

如需方法的詳細資訊 `callTodoListService` ，請參閱[呼叫 web api 的 Web API：呼叫 API](scenario-web-api-call-api-call-api.md)。

# <a name="java"></a>[Java](#tab/java)
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

# <a name="python"></a>[Python](#tab/python)

Python Web API 必須使用一些中介軟體來驗證從用戶端收到的持有人權杖。 然後，Web API 可以藉由呼叫方法，使用 MSAL Python 程式庫取得下游 API 的存取權杖 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 。 尚未提供使用 MSAL Python 來示範此流程的範例。

---

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 Web API：呼叫 API](scenario-web-api-call-api-call-api.md)
