---
title: 呼叫 Web API 的 Web API - 微軟身份平臺 |蔚藍
description: 瞭解如何建構除 Web API 的 Web API。
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
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885084"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>呼叫 Web API 的 Web API:呼叫 API

獲得權杖後,可以調用受保護的 Web API。 從 Web API 的控制器執行此操作。

## <a name="controller-code"></a>控制器代碼

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

以下代碼繼續範例代碼,該代碼顯示在[呼叫 Web API 的 Web API 中:取得應用的權杖](scenario-web-api-call-api-acquire-token.md)。 在 API 控制器的操作中呼叫代碼。 它調用名為*tolist 的*下游 API。

獲取權杖後,將其用作無記名令牌來調用下游 API。

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

以下代碼繼續範例代碼,該代碼顯示在[呼叫 Web API 的 Web API 中:取得應用的權杖](scenario-web-api-call-api-acquire-token.md)。 在 API 控制器的操作中呼叫代碼。 它調用下游 API MS 圖形。

獲取權杖後,將其用作無記名令牌來調用下游 API。

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
使用 MSAL Python 展示此流的範例尚不可用。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web API:移轉到生產](scenario-web-api-call-api-production.md)
