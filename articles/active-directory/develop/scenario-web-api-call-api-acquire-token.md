---
title: 取得呼叫 Web API 的 Web API 權杖 ( API)蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何建構呼用需要取得應用權杖的 Web API 的 Web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885135"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>呼叫 Web API 的 Web API:取得應用的權杖

生成用戶端應用程式物件後,使用它獲取可用於調用 Web API 的權杖。

## <a name="code-in-the-controller"></a>控制器中的代碼

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

下面是在 API 控制器的操作中調用的代碼的範例。 它調用名為*tolist 的*下游 API。

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

`BuildConfidentialClient()`類似於呼叫 Web API 的 Web API 的專案[:應用設定](scenario-web-api-call-api-app-configuration.md)。 `BuildConfidentialClient()``IConfidentialClientApplication`實例化緩存僅包含一個帳戶的資訊。 該帳戶由方法`GetAccountIdentifier`提供。

該方法`GetAccountIdentifier`使用與 Web API 接收 JSON Web 權杖 (JWT) 的使用者的身份關聯的聲明:

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

# <a name="java"></a>[Java](#tab/java)
下面是在 API 控制器的操作中調用的代碼的範例。 它調用下游 API - 微軟圖形。

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

Python Web API 需要使用一些中間件來驗證從用戶端收到的無記名權杖。 然後,Web API 可以使用 MSAL Python 庫調用[`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of)方法獲取下游 API 的訪問權杖。 使用 MSAL Python 展示此流的範例尚不可用。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web API:呼叫 API](scenario-web-api-call-api-call-api.md)
