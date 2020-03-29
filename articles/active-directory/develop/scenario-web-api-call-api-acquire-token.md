---
title: 獲取調用 Web API 的 Web API 權杖 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何構建調用需要獲取應用權杖的 Web API 的 Web API。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834105"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>調用 Web API 的 Web API：獲取應用的權杖

生成用戶端應用程式物件後，使用它獲取可用於調用 Web API 的權杖。

## <a name="code-in-the-controller"></a>控制器中的代碼

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

下面是在 API 控制器的操作中調用的代碼的示例。 它調用名為*tolist 的*下游 API。

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

`BuildConfidentialClient()`類似于調用 Web API 的 Web API 中的方案[：應用配置](scenario-web-api-call-api-app-configuration.md)。 `BuildConfidentialClient()``IConfidentialClientApplication`具現化緩存僅包含一個帳戶的資訊。 該帳戶由 方法`GetAccountIdentifier`提供。

該方法`GetAccountIdentifier`使用與 Web API 接收 JSON Web 權杖 （JWT） 的使用者的身份關聯的聲明：

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

# <a name="java"></a>[JAVA](#tab/java)
下面是在 API 控制器的操作中調用的代碼的示例。 它調用下游 API - 微軟圖形。

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

Python Web API 需要使用一些中介軟體來驗證從用戶端收到的無記名權杖。 然後，Web API 可以使用 MSAL Python 庫調用[`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of)方法獲取下游 API 的訪問權杖。 使用 MSAL Python 演示此流的示例尚不可用。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [調用 Web API 的 Web API：調用 API](scenario-web-api-call-api-call-api.md)
