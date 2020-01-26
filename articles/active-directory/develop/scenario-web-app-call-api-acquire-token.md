---
title: 在呼叫 web Api 的 web 應用程式中取得權杖-Microsoft 身分識別平臺 |Azure
description: 瞭解如何為呼叫 web Api 的 web 應用程式取得權杖
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759069"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>呼叫 web Api 的 web 應用程式：取得應用程式的權杖

您已建立用戶端應用程式物件。 現在，您將使用它來取得權杖，以呼叫 Web API。 在 ASP.NET 或 ASP.NET Core 中，呼叫 Web API 是在控制器中完成：

- 使用權杖快取取得 Web API 的權杖。 若要取得此權杖，請呼叫 `AcquireTokenSilent` 方法。
- 呼叫受保護的 API，並將存取權杖當做參數傳遞給它。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

控制器方法會受到 `[Authorize]` 屬性的保護，強制使用者進行驗證以使用 web 應用程式。 以下是呼叫 Microsoft Graph 的程式碼：

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

使用相依性插入，ASP.NET 會插入 `ITokenAcquisition` 服務。

以下是 `HomeController`的動作簡化的程式碼，它會取得要呼叫 Microsoft Graph 的權杖：

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

若要進一步瞭解此案例所需的程式碼，請參閱[aspnetcore-webapp-教學](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)課程教學課程的第2階段（[2-1-Web 應用程式呼叫 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)）步驟。

還有其他複雜的變化，例如：

- 呼叫數個 Api。
- 處理累加同意和條件式存取。

[3-WebApp-多 api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)教學課程的第3章將涵蓋這些 advanced 步驟。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 的程式碼類似于 ASP.NET Core 顯示的程式碼：

- 由 [授權] 屬性保護的控制器動作，會解壓縮控制器之 `ClaimsPrincipal` 成員的租使用者識別碼和使用者識別碼。 （ASP.NET 會使用 `HttpContext.User`）。
- 接著，它會建立一個 MSAL.NET `IConfidentialClientApplication` 物件。
- 最後，它會呼叫機密用戶端應用程式的 `AcquireTokenSilent` 方法。

# <a name="javatabjava"></a>[Java](#tab/java)

在 JAVA 範例中，呼叫 API 的程式碼位於[AuthPageController. JAVA # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)的 getUsersFromGraph 方法中。

方法會嘗試呼叫 `getAuthResultBySilentFlow`。 如果使用者需要同意更多的範圍，程式碼會處理 `MsalInteractionRequiredException` 物件，以挑戰使用者。

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 範例中，呼叫 Microsoft Graph 的程式碼位於[.py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62)。

程式碼會嘗試從權杖快取取得權杖。 然後，在設定 authorization 標頭之後，它會呼叫 Web API。 如果它無法取得權杖，它就會再次將使用者重新登入。

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-web-app-call-api-call-api.md)
