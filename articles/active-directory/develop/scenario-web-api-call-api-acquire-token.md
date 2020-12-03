---
title: 取得可呼叫 web Api 的 web API 權杖 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何建立 web API，以呼叫需要取得應用程式權杖的 web Api。
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
ms.openlocfilehash: e8301a1961479f57528802e6d8c0f10ceb0569d5
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558261"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>呼叫 web Api 的 web API：取得應用程式的權杖

當您建立用戶端應用程式物件之後，請使用它來取得權杖，以供您用來呼叫 web API。

## <a name="code-in-the-controller"></a>控制器中的程式碼

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Web.config* 會新增擴充方法，以提供用來呼叫 Microsoft Graph 或下游 Web API 的便利服務。 這些方法會在 [呼叫 Web api 的 WEB api 中詳細說明：呼叫 API](scenario-web-api-call-api-call-api.md)。 使用這些 helper 方法時，您不需要手動取得權杖。

但是，如果您想要以手動方式取得權杖，下列程式碼將示範如何在 API 控制器中使用 *web.config。* 它會呼叫名為 *todolist* 的下游 API。
若要取得權杖來呼叫下游 API，您可以在控制器的函式中將相依性 `ITokenAcquisition` 插入插入服務 (或您的頁面函式（如果您使用 Blazor) ），並在您的控制器動作中使用它，為使用者 (`GetAccessTokenForUserAsync`) 或應用程式本身取得權杖 (`GetAccessTokenForAppAsync` 在背景程式案例的情況下) 。

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

如需此方法的詳細資訊 `callTodoListService` ，請參閱  [呼叫 web api 的 web Api：呼叫 API](scenario-web-api-call-api-call-api.md)。

### <a name="java"></a>[Java](#tab/java)

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

### <a name="python"></a>[Python](#tab/python)
 
Python web API 需要使用中介軟體來驗證從用戶端收到的持有人權杖。 然後，web API 就可以藉由呼叫方法，使用 MSAL Python 程式庫來取得下游 API 的存取權杖 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 。
 
以下是使用 `acquire_token_on_behalf_of` 方法和 Flask 架構取得存取權杖的程式碼範例。 它會呼叫下游 API-Azure 管理訂用帳戶端點。
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a> (Advanced) 從背景應用程式、Api 和服務存取已登入使用者的權杖快取

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>後續步驟

請移至本案例的下一篇文章，以 [呼叫 API](scenario-web-api-call-api-call-api.md)。
