---
title: 撰寫可登入/登出使用者的 web 應用程式 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何建立可登入/登出使用者的 web 應用程式
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: f8fa5532a5664741c9ddb9b78b35d5eed8e2e4e0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937845"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>登入使用者的 Web 應用程式：登入和登出

瞭解如何針對登入使用者的 web 應用程式，將登入新增至程式碼。 然後，瞭解如何讓他們登出。

## <a name="sign-in"></a>登入

登入是由兩個部分所組成：

- HTML 頁面上的登入按鈕
- 在控制器的程式碼後端中的登入動作

### <a name="sign-in-button"></a>登入按鈕

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，針對 Microsoft 身分識別平臺應用程式，[登 **入** ] 按鈕會在 MVC 應用程式的 (中公開， `Views\Shared\_LoginPartial.cshtml`) 或 `Pages\Shared\_LoginPartial.cshtm` 針對 Razor 應用程式)  (。 只有當使用者未通過驗證時，才會顯示。 也就是說，當使用者尚未登入或登出時，就會顯示它。相反地，當使用者已登入時，就會顯示 [ **登出** ] 按鈕。 請注意，帳戶控制器會定義在名為 **MicrosoftIdentity** 的區域中的 [**使用者** 名稱] NuGet 套件中。

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET MVC 中，會在中公開登出按鈕 `Views\Shared\_LoginPartial.cshtml` 。 只有在有經過驗證的帳戶時才會顯示。 也就是說，它會在使用者先前登入時顯示。

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

# <a name="java"></a>[Java](#tab/java)

在 JAVA 快速入門中，登入按鈕位於 [主要/資源/範本/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) 檔案中。

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

# <a name="python"></a>[Python](#tab/python)

在 Python 快速入門中，沒有登入按鈕。 當程式碼後端到達 web 應用程式的根目錄時，系統會自動提示使用者進行登入。 請參閱 [.py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)。

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` 控制器的動作

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，選取 web 應用程式中的 [登 **入** ] 按鈕，就會觸發 `SignIn` 控制器上的動作 `AccountController` 。 在舊版的 ASP.NET core 範本中， `Account` 控制器是以 web 應用程式內嵌。 這已不再是這樣，因為控制器現在是 **Web.config** NuGet 封裝的一部分。 如需詳細資訊，請參閱 [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) 。

此控制器也會處理 Azure AD B2C 的應用程式。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，登出是從 `SignOut()` (控制器上的方法觸發，例如 [AccountController .Cs # L16 也-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)) 。 這個方法不是 ASP.NET framework 的一部分， (與 ASP.NET Core) 中發生的情況相反。 它會在建議重新導向 URI 之後傳送 OpenID 登入挑戰。

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

# <a name="java"></a>[Java](#tab/java)

在 JAVA 中，登出的處理方式是直接呼叫 Microsoft 身分識別平臺 `logout` 端點並提供 `post_logout_redirect_uri` 值。 如需詳細資訊，請參閱 [AuthPageController。 java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)。

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

# <a name="python"></a>[Python](#tab/python)

與其他平臺不同的是，MSAL Python 會負責讓使用者從登入頁面登入。 請參閱 [.py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)。

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

`_build_msal_app()`方法是在[.Py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88)中定義，如下所示：

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

在使用者登入您的應用程式之後，您會想要讓他們登出。

## <a name="sign-out"></a>登出

從 web 應用程式登出，會比從 web 應用程式的狀態移除登入帳戶的相關資訊更多。
Web 應用程式也必須將使用者重新導向至 Microsoft 身分識別平臺 `logout` 端點，才能登出。

當您的 web 應用程式將使用者重新導向至 `logout` 端點時，此端點會清除瀏覽器中的使用者會話。 如果您的應用程式未移至 `logout` 端點，使用者會重新驗證您的應用程式，而不需要再次輸入認證。 原因是他們會與 Microsoft 身分識別平臺擁有有效的單一登入會話。

若要深入瞭解，請參閱 Microsoft 身分識別平臺中的 [傳送登出要求](v2-protocols-oidc.md#send-a-sign-out-request) 一節 [和 OpenID Connect 的通訊協定](v2-protocols-oidc.md) 檔。

### <a name="application-registration"></a>應用程式註冊

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在應用程式註冊期間，您會註冊一個前端通道的登出 URL。 在本教學課程中，您已 `https://localhost:44321/signout-oidc` 在 [**驗證**] 頁面上的 [**前端通道登出 URL** ] 欄位中註冊。 如需詳細資訊，請參閱 [註冊 webApp 應用程式](scenario-web-app-sign-user-app-registration.md#register-an-app-by-using-the-azure-portal)。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在應用程式註冊期間，您不需要註冊額外的前端通道登出 URL。 應用程式將會被回呼其主要 URL。 

# <a name="java"></a>[Java](#tab/java)

應用程式註冊中不需要任何前端通道的登出 URL。

# <a name="python"></a>[Python](#tab/python)

在應用程式註冊期間，您不需要註冊額外的前端通道登出 URL。 應用程式將會被回呼其主要 URL。

---

### <a name="sign-out-button"></a>登出按鈕

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，選取 web 應用程式中的 [ **登出** ] 按鈕會在 `SignOut` 控制器上觸發動作 `AccountController` (請參閱下文) 

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET MVC 中，會在中公開登出按鈕 `Views\Shared\_LoginPartial.cshtml` 。 只有在有經過驗證的帳戶時才會顯示。 也就是說，它會在使用者先前登入時顯示。

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

# <a name="java"></a>[Java](#tab/java)

在 JAVA 快速入門中，[登出] 按鈕位於主要/資源/範本/auth_page.html 檔案中。

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

在 Python 快速入門中，[登出] 按鈕位於 [templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) 檔案中。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` 控制器的動作

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在舊版的 ASP.NET core 範本中， `Account` 控制器是以 web 應用程式內嵌。 這已不再是這樣，因為控制器現在是 **Web.config** NuGet 封裝的一部分。 如需詳細資訊，請參閱 [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) 。

- 將 OpenID 重新導向 URI 設定為， `/Account/SignedOut` 以便在 Azure AD 完成登出時，回呼控制器。
- 呼叫 `Signout()` ，可讓 OpenID Connect 中介軟體與 Microsoft 身分識別平臺 `logout` 端點連線。 端點接著：

  - 清除瀏覽器中的會話 cookie。
  - 回撥登出後的重新導向 URI。 依預設，登出後重新導向 URI 會顯示已登出的視圖頁面 [SignedOut.cshtml.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs)。 此頁面也會提供為 Web.config 的一部分。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，登出是從 `SignOut()` (控制器上的方法觸發，例如 [AccountController .Cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)) 。 這個方法不是 ASP.NET 架構的一部分，與 ASP.NET Core 中發生的情況相反。 強烈

- 傳送 OpenID 登出挑戰。
- 清除快取。
- 重新導向至所要的頁面。

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

# <a name="java"></a>[Java](#tab/java)

在 JAVA 中，登出的處理方式是直接呼叫 Microsoft 身分識別平臺 `logout` 端點並提供 `post_logout_redirect_uri` 值。 如需詳細資訊，請參閱 [AuthPageController。 java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)。

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

# <a name="python"></a>[Python](#tab/python)

登出使用者的程式碼位於 [.py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)。

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>攔截對端點的呼叫 `logout`

登出後的 URI 可讓應用程式參與全域登出。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect 中介軟體可讓您的應用程式藉 `logout` 由提供名為的 OpenID Connect 事件，來攔截對 Microsoft 身分識別平臺端點的呼叫 `OnRedirectToIdentityProviderForSignOut` 。 這會由 (自動處理，這會在您的 web 應用程式呼叫 web api 的情況下，清除帳戶) 

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，您可以委派至中介軟體來執行登出，並清除會話 cookie：

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

# <a name="java"></a>[Java](#tab/java)

在 JAVA 快速入門中，登出後重新導向 URI 只會顯示 index.html 頁面。

# <a name="python"></a>[Python](#tab/python)

在 Python 快速入門中，登出後重新導向 URI 只會顯示 index.html 頁面。

---

## <a name="protocol"></a>通訊協定

如果您想要深入瞭解登出，請閱讀 [OPEN ID Connect](./v2-protocols-oidc.md)提供的通訊協定檔。

## <a name="next-steps"></a>後續步驟

移至本案例的下一篇文章， [移至生產環境](scenario-web-app-sign-user-production.md)。