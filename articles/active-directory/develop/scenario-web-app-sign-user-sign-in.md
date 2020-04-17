---
title: 編寫一個登錄/退出使用者的 Web 應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何建構/離開使用者的 Web 應用
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2ab5697ceff612e65174fdb7f9ef6137e2c8b9a5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537061"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>登入使用者的 Web 應用程式:登入和登出

瞭解如何向登錄使用者的 Web 應用的代碼添加登錄。 然後,學習如何讓他們註銷。

## <a name="sign-in"></a>登入

登入由兩部分組成:

- HTML 頁面上的登入按鈕
- 控制器中代碼後面的登入操作

### <a name="sign-in-button"></a>登入按鈕

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 核心中,登錄按鈕`Views\Shared\_LoginPartial.cshtml`在中 公開。 僅當沒有經過身份驗證的帳戶時,才會顯示它。 也就是說,當使用者尚未登錄或註銷時,將顯示它。

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 mVC ASP.NET 中,註`Views\Shared\_LoginPartial.cshtml`銷按鈕在 中公開。 僅當存在經過身份驗證的帳戶時,才會顯示它。 也就是說,它顯示在使用者以前登錄時。

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

在我們的 Java 快速入門中,登錄按鈕位於[main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)檔中。

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

在 Python 快速入門中,沒有登錄按鈕。 代碼後面會自動提示使用者登錄時,它到達 Web 應用的根。 請參閱[應用程式.py_L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)。

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`控制器的作業

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在ASP.NET中,選擇Web應用中的**登錄**按鈕將觸發控制器`SignIn``AccountController`上的操作。 在ASP.NET核心範本的早期版本中,`Account`控制器嵌入了Web應用。 情況已經不同了,因為控制器現在是ASP.NET核心框架的一部分。

的代碼`AccountController`可從[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)中的 ASP.NET 核心儲存庫中獲取。 帳戶控制通過重定向到 Microsoft 標識平台終結點來挑戰使用者。 有關詳細資訊,請參閱作為ASP.NET核心部分提供的[SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31)方法。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在ASP.NET中,登出是從控制器上`SignOut()`的方法觸發的(例如,[帳戶控制器.cs_L16-L23)。](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23) 此方法不是ASP.NET框架的一部分(與ASP.NET核心中發生的情況相反)。 在建議重定向 URI 後,它發送 OpenID 登錄質詢。

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

在 Java 中,通過直接調用 Microsoft 標識`logout`平台`post_logout_redirect_uri`終結點並提供 該值來處理註銷。 有關詳細資訊,請參閱[AuthPageController.java_L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)。

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

與其他平臺不同,MSAL Python 負責讓使用者從登錄頁面登錄。 請參閱[應用程式.py_L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)。

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

該方法`_build_msal_app()`在[app.py_L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88)中定義如下:

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

使用者登錄到你的應用后,您需要允許他們註銷。

## <a name="sign-out"></a>登出

從 Web 應用登出不僅僅涉及從 Web 應用的狀態中刪除有關登錄帳戶的資訊。
Web 應用還必須將使用者重定向到 Microsoft`logout`標識平台 終結點才能登出。

當 Web 應用將使用者重定向到終結`logout`點時 ,此終結點將從瀏覽器清除使用者的作業階段。 如果應用未轉到`logout`終結點,使用者將重新驗證你的應用,而無需再次輸入其憑據。 原因是他們將與 Microsoft 標識平台終結點具有有效的單一登錄會話。

要瞭解更多資訊,請參閱 Microsoft 標識平臺中的[「發送登出請求」](v2-protocols-oidc.md#send-a-sign-out-request)部分[和 OpenID 連接協議](v2-protocols-oidc.md)文檔。

### <a name="application-registration"></a>應用程式註冊

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在應用程式註冊期間,您將註冊註銷后URI。 在我們的教程`https://localhost:44321/signout-oidc`中,您在 **「身份驗證**」頁上**的高級設置**部分的登出**URL**欄位中註冊。 有關詳細資訊,請參閱[註冊 WebApp 應用](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在應用程式註冊期間,您將註冊註銷后URI。 在我們的教程`https://localhost:44308/Account/EndSession`中,您在 **「身份驗證**」頁上**的高級設置**部分的登出**URL**欄位中註冊。 有關詳細資訊,請參閱[註冊 WebApp 應用](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)。

# <a name="java"></a>[Java](#tab/java)

在應用程式註冊期間,您將註冊註銷后URI。 在我們的教程`http://localhost:8080/msal4jsample/sign_out`中,您在 **「身份驗證**」頁上**的高級設置**部分的登出**URL**欄位中註冊。

# <a name="python"></a>[Python](#tab/python)

在應用程式註冊期間,您無需註冊額外的註銷 URL。 該應用程式將在其主 URL 上調用。

---

### <a name="sign-out-button"></a>登出按鈕

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 核心中,註銷按鈕`Views\Shared\_LoginPartial.cshtml`在 中 公開。 僅當存在經過身份驗證的帳戶時,才會顯示它。 也就是說,它顯示在使用者以前登錄時。

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 mVC ASP.NET 中,註`Views\Shared\_LoginPartial.cshtml`銷按鈕在 中公開。 僅當存在經過身份驗證的帳戶時,才會顯示它。 也就是說,它顯示在使用者以前登錄時。

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

在我們的 Java 快速入門中,登出按鈕位於main/resources/templates/auth_page.html檔中。

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

在 Python 快速啟動中,登出按鈕位於[樣本/ index.html_L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)檔中。

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

### <a name="signout-action-of-the-controller"></a>`SignOut`控制器的作業

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在ASP.NET中,選擇Web應用中的 **「註銷**」按鈕將觸`SignOut`發 控制`AccountController`器上的操作。 在以前版本的ASP.NET核心範本中,`Account`控制器嵌入了Web應用。 情況已經不同了,因為控制器現在是ASP.NET核心框架的一部分。

的代碼`AccountController`可從[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)中的 ASP.NET 核心儲存庫中獲取。 帳戶控制:

- 將 OpenID 重定`/Account/SignedOut`向 URI 設定,以便在 Azure AD 完成登出時呼叫控制器。
- 呼叫`Signout()`,這允許 OpenID 連接中間件`logout`與 Microsoft 識別平台終結點聯繫。 然後,終結點:

  - 從瀏覽器清除會話 Cookie。
  - 回叫註銷 URL。 預設情況下,登出 URL 顯示登出檢視頁[「簽署退出.html」。](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) 此頁面也作為ASP.NET核心的一部分提供。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在ASP.NET中,登出是從控制器上`SignOut()`的方法觸發的(例如,[帳戶控制器.cs_L25-L31)。](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31) 此方法不是ASP.NET框架的一部分,這與ASP.NET Core 中發生的情況相反。 它:

- 發送 OpenID 登出質詢。
- 清除緩存。
- 重定向到它想要的頁面。

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

在 Java 中,通過直接調用 Microsoft 標識`logout`平台`post_logout_redirect_uri`終結點並提供 該值來處理註銷。 有關詳細資訊,請參閱[AuthPageController.java_L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)。

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

向使用者發出簽名的代碼位於[app.py_L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)中。

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>攔截對終結點的`logout`呼叫

註銷後URI使應用程式能夠參與全域註銷。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET核心 OpenID Connect 中間件使你的應用可以`logout``OnRedirectToIdentityProviderForSignOut`通過提供名為的 OpenID Connect 事件來攔截對 Microsoft 標識平臺終結點的調用。 有關如何訂閱此事件的範例(清除令牌緩存),請參閱[Microsoft.標識.Web/WebAppService 收集擴展.cs_L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)。

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在ASP.NET中,您將委託給中間件執行登出,清除會話 Cookie:

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

在 Java 快速啟動中,登出後重定向 URI 僅顯示 index.html 頁面。

# <a name="python"></a>[Python](#tab/python)

在 Python 快速啟動中,登出後重定向 URI 僅顯示 index.html 頁面。

---

## <a name="protocol"></a>通訊協定

如果要瞭解有關注銷的更多內容,請閱讀[從 Open ID 連接](./v2-protocols-oidc.md)中提供的協議文檔。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移動至生產環境](scenario-web-app-sign-user-production.md)
