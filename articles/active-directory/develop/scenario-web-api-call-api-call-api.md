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
ms.openlocfilehash: c7c54c7c4718cea479a812574e961ef2338a6be6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701751"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>呼叫 web Api 的 Web API：呼叫 API

有了權杖之後，您就可以呼叫受保護的 Web API。 您可以從 ASP.NET 或 ASP.NET Core Web API 的控制器執行此動作。

## <a name="controller-code"></a>控制器程式碼

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

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 web Api 的 Web API：移至生產環境](scenario-web-api-call-api-production.md)
