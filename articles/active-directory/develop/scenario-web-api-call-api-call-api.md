---
title: 呼叫 web Api 的 web API-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫 web Api 的 Web API。
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
ms.openlocfilehash: d66a08d4e84a3771d6c3fa46b96c975869435452
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76833374"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>呼叫 web Api 的 Web API：呼叫 API

有了權杖之後，您就可以呼叫受保護的 Web API。 您可以從 Web API 的控制器執行此動作。

## <a name="controller-code"></a>控制器程式碼

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

下列程式碼會繼續在呼叫 web Api 的 Web API 中顯示的範例[程式碼：取得應用程式的權杖](scenario-web-api-call-api-acquire-token.md)。 程式碼會在 API 控制器的動作中呼叫。 它會呼叫名為*todolist*的下游 API。

取得權杖之後，請使用它做為持有人權杖來呼叫下游 API。

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

# <a name="javatabjava"></a>[Java](#tab/java)

下列程式碼會繼續在呼叫 web Api 的 Web API 中顯示的範例[程式碼：取得應用程式的權杖](scenario-web-api-call-api-acquire-token.md)。 程式碼會在 API 控制器的動作中呼叫。 它會呼叫下游 API MS Graph。

取得權杖之後，請使用它做為持有人權杖來呼叫下游 API。

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

# <a name="pythontabpython"></a>[Python](#tab/python)
尚未提供使用 MSAL Python 來示範此流程的範例。

---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 Web API：移至生產環境](scenario-web-api-call-api-production.md)
