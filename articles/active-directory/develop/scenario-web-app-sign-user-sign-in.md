---
title: 撰寫可登入/登出使用者的 web 應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立可登入/登出使用者的 web 應用程式
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
ms.openlocfilehash: cbec6c5644690f6d7e522294a37c8ea5e2d49e30
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701513"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>登入使用者的 Web 應用程式：登入和登出

瞭解如何將登入新增至 web 應用程式的程式碼，以登入使用者。 然後，瞭解如何讓他們登出。

## <a name="sign-in"></a>登入

登入由兩個部分組成：

- [HTML] 頁面上的 [登入] 按鈕
- 控制器的程式碼後置中的登入動作

### <a name="sign-in-button"></a>登入按鈕

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，[登入] 按鈕會在 `Views\Shared\_LoginPartial.cshtml`中公開。 只有在沒有已驗證的帳戶時才會顯示。 也就是說，當使用者尚未登入或登出時，就會顯示它。

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET MVC 中，[登出] 按鈕會在 `Views\Shared\_LoginPartial.cshtml`中公開。 只有在有已驗證的帳戶時才會顯示。 也就是說，當使用者先前已登入時，就會顯示它。

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在我們的 JAVA 快速入門中，[登入] 按鈕位於[main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)檔案中。

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 快速入門中，沒有 [登入] 按鈕。 當程式碼後置到達 web 應用程式的根目錄時，會自動提示使用者進行登入。 請參閱[.py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)。

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>控制器的 `SignIn` 動作

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，選取 web 應用程式中的 [登**入**] 按鈕會觸發 `AccountController` 控制器上的 `SignIn` 動作。 在舊版的 ASP.NET core 範本中，`Account` 控制器已內嵌在 web 應用程式中。 因為控制器現在是 ASP.NET Core 架構的一部分，所以已不再是這種情況。

`AccountController` 的程式碼可從[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)中的 ASP.NET Core 存放庫取得。 帳戶控制會藉由重新導向至 Microsoft 身分識別平臺端點來挑戰使用者。 如需詳細資訊，請參閱 ASP.NET Core 中提供的登[入方法。](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，登出是從控制器上的 `SignOut()` 方法觸發（例如[AccountController .cs # l16 也-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)）。 這個方法不是 ASP.NET 架構的一部分（相對於 ASP.NET Core 所發生的情況）。 在建議重新導向 URI 之後，它會傳送 OpenID 登入挑戰。

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在 JAVA 中，登出的處理方式是直接呼叫 Microsoft 身分識別平臺 `logout` 端點，並提供 `post_logout_redirect_uri` 值。 如需詳細資訊，請參閱[AuthPageController. java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)。

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

與其他平臺不同的是，MSAL Python 會負責讓使用者從登入頁面登入。 請參閱[.py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)。

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

`_build_msal_app()` 的方法定義于[.py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88)中，如下所示：

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

使用者登入您的應用程式之後，您會想要讓他們登出。

## <a name="sign-out"></a>登出

登出 web 應用程式牽涉到從 web 應用程式的狀態中移除登入帳戶的相關資訊。
Web 應用程式也必須將使用者重新導向至 Microsoft 身分識別平臺 `logout` 端點來登出。 

當您的 web 應用程式將使用者重新導向至 `logout` 端點時，此端點會清除瀏覽器中的使用者會話。 如果您的應用程式未移至 `logout` 端點，則使用者會重新驗證您的應用程式，而不會再次輸入其認證。 這是因為他們會有有效的單一登入會話與 Microsoft 身分識別平臺端點。

若要深入瞭解，請參閱[Microsoft 身分識別平臺和 OpenID connect 通訊協定](v2-protocols-oidc.md)檔中的[傳送登出要求](v2-protocols-oidc.md#send-a-sign-out-request)一節。

### <a name="application-registration"></a>應用程式註冊

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在應用程式註冊期間，您會註冊登出後的 URI。 在本教學課程中，您已在 [**驗證**] 頁面上 [ **Advanced Settings** ] （設定）區段的 [**登出 URL** ] 欄位中註冊 `https://localhost:44321/signout-oidc`。 如需詳細資訊，請參閱[註冊 webApp 應用程式](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在應用程式註冊期間，您會註冊登出後的 URI。 在本教學課程中，您已在 [**驗證**] 頁面上 [ **Advanced Settings** ] （設定）區段的 [**登出 URL** ] 欄位中註冊 `https://localhost:44308/Account/EndSession`。 如需詳細資訊，請參閱[註冊 webApp 應用程式](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)。

# <a name="javatabjava"></a>[Java](#tab/java)

在應用程式註冊期間，您會註冊登出後的 URI。 在本教學課程中，您已在 [**驗證**] 頁面上 [ **Advanced Settings** ] （設定）區段的 [**登出 URL** ] 欄位中註冊 `http://localhost:8080/msal4jsample/sign_out`。

# <a name="pythontabpython"></a>[Python](#tab/python)

在應用程式註冊期間，您不需要註冊額外的登出 URL。 應用程式將會在其主要 URL 上呼叫回來。

---

### <a name="sign-out-button"></a>登出按鈕

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，[登出] 按鈕會在 `Views\Shared\_LoginPartial.cshtml`中公開。 只有在有已驗證的帳戶時才會顯示。 也就是說，當使用者先前已登入時，就會顯示它。

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET MVC 中，[登出] 按鈕會在 `Views\Shared\_LoginPartial.cshtml`中公開。 只有在有已驗證的帳戶時才會顯示。 也就是說，當使用者先前已登入時，就會顯示它。

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在我們的 JAVA 快速入門中，[登出] 按鈕位於 main/resources/templates/auth_page.html 檔案中。

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 快速入門中，[登出] 按鈕位於[templates/index .html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)檔案中。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>控制器的 `SignOut` 動作

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，選取 web 應用程式中的 [**登出**] 按鈕，會觸發 `AccountController` 控制器上的 `SignOut` 動作。 在舊版的 ASP.NET Core 範本中，`Account` 控制器已內嵌在 web 應用程式中。 因為控制器現在是 ASP.NET Core 架構的一部分，所以已不再是這種情況。

您可以從[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)中的 ASP.NET core 存放庫取得 `AccountController` 的程式碼。 帳戶控制：

- 將 OpenID 重新導向 URI 設定為 `/Account/SignedOut`，以便在 Azure AD 完成登出時回呼控制器。
- 呼叫 `Signout()`，讓 OpenID Connect 中介軟體與 Microsoft 身分識別平臺連線 `logout` 端點。 然後，端點會：

  - 從瀏覽器清除會話 cookie。
  - 回撥登出 URL。 [登出 URL] 預設會顯示 [已登出] 視圖頁面[SignedOut](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)。 此頁面也會提供做為 ASP.NET Core 的一部分。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，登出是從控制器上的 `SignOut()` 方法觸發（例如[AccountController .cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)）。 這個方法不是 ASP.NET 架構的一部分，與 ASP.NET Core 中發生的情況相反。 這樣

- 傳送 OpenID 登出挑戰。
- 清除快取。
- 重新導向至它想要的頁面。

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在 JAVA 中，登出的處理方式是直接呼叫 Microsoft 身分識別平臺 `logout` 端點，並提供 `post_logout_redirect_uri` 值。 如需詳細資訊，請參閱[AuthPageController. java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)。

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

登出使用者的程式碼位於[.py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)。

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>攔截對 `logout` 端點的呼叫

登出後的 URI 可讓應用程式參與全域登出。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect 中介軟體可讓您的應用程式藉由提供名為 `OnRedirectToIdentityProviderForSignOut`的 OpenID Connect 事件，攔截對 Microsoft 身分識別平臺 `logout` 端點的呼叫。 如需如何訂閱此事件（以清除權杖快取）的範例，請參閱[WebAppServiceCollectionExtensions。 cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)。

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，您會委派至中介軟體來執行登出，並清除會話 cookie：

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在 JAVA 快速入門中，登出後重新導向 URI 只會顯示 [.html] 頁面。

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 快速入門中，登出後重新導向 URI 只會顯示 [.html] 頁面。

---

## <a name="protocol"></a>通訊協定

如果您想要深入瞭解登出，請閱讀可從[OPEN ID Connect](./v2-protocols-oidc.md)取得的通訊協定檔。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-web-app-sign-user-production.md)
