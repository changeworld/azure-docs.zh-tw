---
title: 將登入新增至 Microsoft 身分識別平台 ASP.NET Web 應用程式
titleSuffix: Microsoft identity platform
description: 使用傳統網頁瀏覽器型應用程式和 OpenID Connect 標準在 ASP.NET 方案上實作 Microsoft 登入
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 50eb88373b05d979d7f4b67b317e98c2a944459b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701325"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>將「登入到 Microsoft」新增至 ASP.NET Web 應用程式

本指南示範如何透過傳統網頁瀏覽器型應用程式和 OpenID Connect 使用 ASP.NET MVC 方案實作「登入到 Microsoft」。

當您完成此指南時，您的應用程式就能夠接受來自 outlook.com 和 live.com 等個人帳戶的登入。 此外，與 Microsoft 身分識別平台整合的任何公司或組織中的公司和學校帳戶也可以登入您的應用程式。

> 此指南需要 Microsoft Visual Studio 2019。  沒有嗎？  [免費下載 Visual Studio 2019](https://www.visualstudio.com/downloads/)。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式

![示範本教學課程所產生的應用程式範例如何運作](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

您建立的範例應用程式是以您使用瀏覽器存取 ASP.NET 網站的案例為基礎，而該網站會提示使用者透過登入按鈕進行驗證。 在這個案例中，大部分轉譯網頁的工作會在伺服器端執行。

## <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|描述|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|可讓應用程式使用 OpenIdConnect 進行驗證的中介軟體|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|可讓應用程式使用 Cookie 維持使用者工作階段的中介軟體|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|可讓 OWIN 型應用程式藉由使用 ASP.NET 要求管線在 Internet Information Services (IIS) 上執行的中介軟體|

## <a name="set-up-your-project"></a>設定專案

本節說明如何使用 OpenID Connect 在 ASP.NET 專案上透過 OWIN 中介軟體安裝及設定驗證管線。

> 想要改為下載此範例的 Visual Studio 專案嗎？ [下載專案](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)並跳至[註冊您的應用程式](#register-your-application)，以在執行之前先設定程式碼範例。

### <a name="create-your-aspnet-project"></a>建立 ASP.NET 專案

1. 在 Visual Studio 中：移至 [檔案]   > [新增]   > [專案]  。
2. 在 [Visual C#\Web]  底下，選取 [ASP.NET Web 應用程式 (.NET Framework)]  。
3. 為您的應用程式命名並選取 [確定]  。
4. 選取 [空白]  ，然後選取核取方塊來新增 **MVC** 參考。

## <a name="add-authentication-components"></a>新增驗證元件

1. 在 Visual Studio 中：移至 [工具]   > [NuGet 套件管理員]   > [套件管理員主控台]  。
2. 在 [套件管理器主控台] 視窗中輸入下列內容以新增「OWIN 中介軟體 NuGet 套件」  ：

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>關於這些程式庫
> 這些程式庫可透過 Cookie 型驗證，使用 OpenID Connect 啟用單一登入 (SSO)。 完成驗證並將代表使用者的權杖傳送至您的應用程式之後，OWIN 中介軟體就會建立工作階段 Cookie。 接著瀏覽器會在後續要求中使用此 Cookie，因此使用者不需要重新輸入密碼，也不需要進行其他驗證。
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>設定驗證管線

下列步驟可用來建立 OWIN 中介軟體「啟動」類別，以設定 OpenID Connect 驗證。 此類別會在您的 IIS 處理序啟動時自動執行。

> [!TIP]
> 如果您專案的根資料夾中沒有 `Startup.cs` 檔案：
> 1. 以滑鼠右鍵按一下專案的根資料夾，然後選取 [新增]   > [新增項目]   > [OWIN 啟動類別]  。<br/>
> 2. 將其命名為 **Startup.cs**。
>
>> 確定選取的類別是 OWIN 啟動類別，而非標準 C# 類別。 若要確認，可檢查您是否看見 [assembly:OwinStartup(typeof({NameSpace}.Startup))] 出現在命名空間上。

1. 將 OWIN  和 Microsoft.IdentityModel  參考新增至 Startup.cs：

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. 以下列程式碼取代 Startup 類別：

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> 設定 `ValidateIssuer = false` 可簡化此快速入門。 在實際的應用程式中，您需要驗證簽發者。
> 請參閱範例以了解如何執行該動作。

<!--start-collapse-->
> ### <a name="more-information"></a>詳細資訊
> 您在 *OpenIDConnectAuthenticationOptions* 中提供的參數會作為應用程式與 Microsoft 身分識別平台進行通訊的座標。 因為 OpenID Connect 中介軟體會在背景中使用 Cookie，所以您也必須設定在上述程式碼顯示時進行 Cookie 驗證。 ValidateIssuer  值會告知 OpenIdConnect 不要針對某一特定組織限制存取。
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>新增控制器以處理登入和登出要求

若要建立新的控制器來公開登入和登出方法，請遵循下列步驟：

1.  以滑鼠右鍵按一下 [控制器]  資料夾，然後選取 [新增]   > [控制器]  。
2.  選取 [MVC (.NET 版本) 控制器 – 空白]  。
3.  選取 [新增]  。
4.  將其命名為 **HomeController**，然後選取 [新增]  。
5.  將 OWIN 參考新增至至類別：

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. 新增下列兩個方法，以透過起始驗證挑戰的方式，處理對控制器的登入和登出：

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>建立應用程式的使用者登入首頁

在 Visual Studio 中建立新的檢視來新增登入按鈕，並在驗證之後顯示使用者資訊：

1.  以滑鼠右鍵按一下 **Views\Home** 資料夾並選取 [新增檢視]  。
2.  將新的檢視命名為 **Index**。
3.  在以下檔案中新增下列 HTML (包括登入按鈕)：

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>詳細資訊
> 此頁面會以 SVG 格式新增一個具有黑色背景的登入按鈕：<br/>![使用 Microsoft 登入](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> 如需了解更多登入按鈕，請前往[商標指導方針](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "商標方針")。
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>新增控制器來顯示使用者的宣告
此控制器示範如何使用 `[Authorize]` 屬性來保護控制器。 此屬性會設定限制，只允許經過驗證的使用者存取控制器。 下列程式碼會利用屬性來顯示在登入過程中擷取的使用者宣告：

1.  以滑鼠右鍵按一下 [控制器]  資料夾，然後選取 [新增]   > [控制器]  。
2.  選取 [MVC {版本} 控制器 – 空白]  。
3.  選取 [新增]  。
4.  將它命名為 **ClaimsController**。
5.  以下列程式碼取代控制器類別的程式碼。 這會將 `[Authorize]` 屬性新增至該類別：

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>詳細資訊
> 因為使用了 `[Authorize]` 屬性，所以此控制器的所有方法都只能在使用者已通過驗證的情況下才能執行。 如果使用者未通過驗證而嘗試存取控制器，OWIN 就會起始驗證挑戰並強制使用者進行驗證。 前述程式碼會查看宣告清單，尋找使用者識別碼權杖中所包含的特定使用者屬性。 這些屬性包括使用者的完整名稱和使用者名稱，以及全域使用者識別元主體。 它也包含「租用戶識別碼」  ，這代表使用者所屬組織的識別碼。 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>建立檢視來顯示使用者的宣告

在 Visual Studio 中，建立新的檢視以在網頁中顯示使用者的宣告：

1.  以滑鼠右鍵按一下 **Views\Claims** 資料夾，然後選取 [新增檢視]  。
2.  將新的檢視命名為 **Index**。
3.  將下列 HTML 新增至檔案：

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>註冊您的應用程式

若要註冊應用程式並將應用程式註冊資訊新增到解決方案，您有兩個選項可以選擇：

### <a name="option-1-express-mode"></a>選項 1：快速模式

若要快速註冊您的應用程式，請遵循下列步驟：

1. 移至新的 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)窗格。
1. 輸入應用程式的名稱，並選取 [註冊]  。
1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。

### <a name="option-2-advanced-mode"></a>選項 2：進階模式

若要手動註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：

1. 開啟 Visual Studio，然後：
   1. 在 [方案總管] 中，選取專案並查看 [屬性] 視窗 (如果您沒有看到 [屬性] 視窗，請按 F4)。
   1. 將 [SSL 已啟用] 變更為 `True`。
   1. 在 Visual Studio 中的專案上按一下滑鼠右鍵，選取 [屬性]  ，然後選取 [Web]  索引標籤。在 [伺服器]  區段中，將 [專案 URL]  設定變更為 [SSL URL]  。
   1. 複製 SSL URL。 在下一個步驟中，將這個 URL 新增到註冊入口網站的重新導向 URL 清單。<br/><br/>![專案屬性](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 移至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. 選取 [新增註冊]  。
1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
   1. 在 [名稱]  區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 **ASPNET-Tutorial**。
   1. 在 [回覆 URL]  中，新增您透過步驟 1 從 Visual Studio 中複製的 SSL URL (例如 `https://localhost:44368/`)，然後選取 [註冊]  。
1. 選取 [驗證]  功能表、在 [隱含授與]  下方選取 [識別碼權杖]  ，然後選取 [儲存]  。
1. 在位於根資料夾的 `configuration\appSettings` 區段中，於 web.config 檔案中新增下列內容：

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. 以您剛剛註冊的「應用程式識別碼」取代 `ClientId`。
1. 以您專案的 SSL URL 取代 `redirectUri`。

## <a name="test-your-code"></a>測試您的程式碼

若要在 Visual Studio 中測試您的應用程式，請按 F5 來執行專案。 瀏覽器會開啟至 http://<span></span>localhost:{port} 位置，且您會看到 [使用 Microsoft 帳戶登入]  按鈕。 選取按鈕以啟動登入程序。

當您準備好執行測試時，請使用 Azure AD 帳戶 (公司或學校帳戶) 或個人 Microsoft 帳戶 (<span>live.</span>com 或 <span>outlook.</span>com) 帳戶登入。

![使用 Microsoft 登入](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![登入您的 Microsoft 帳戶](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Microsoft 身分識別平台端點中的權限和同意
>  與 Microsoft 身分識別平台整合的應用程式，會遵循可讓使用者和系統管理員控制資料存取方式的授權模型。 使用者向 Microsoft 身分識別平台進行驗證以存取此應用程式之後，系統會提示他們同意應用程式所要求的權限 (「檢視您的基本設定檔」和「持續存取您允許存取的資料」)。 接受這些權限之後，使用者會繼續處理應用程式的結果。 不過，如果發生下列其中一種情況，使用者可能會看到**需要管理員同意**的頁面：
>  > - 應用程式開發人員新增任何其他需要**管理員同意**的權限。
>  > - 或租用戶經過設定 (在 [企業應用程式] -> [使用者設定]  中)，使得使用者無法自行同意應用程式存取公司資料。
>
> 如需詳細資訊，請參閱 [Microsoft 身分識別平台端點中的權限和同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)。
<!--end-collapse-->

#### <a name="view-application-results"></a>檢視應用程式結果

登入之後，系統會將使用者重新導向至您網站的首頁。 首頁就是您在 Microsoft 應用程式註冊入口網站的應用程式註冊資訊中指定的 HTTPS URL。 首頁包含歡迎訊息「\<使用者>，您好」  、登出連結，以及用來檢視使用者宣告的連結。 使用者宣告的連結會連接至您稍早建立的「宣告」控制器。

### <a name="view-the-users-claims"></a>檢視使用者的宣告

若要檢視使用者的宣告，請選取瀏覽至控制器檢視的連結，僅可供已驗證的使用者使用。

#### <a name="view-the-claims-results"></a>檢視宣告結果

瀏覽至控制器檢視之後，您應該就會看到一個資料表，包含使用者的基本屬性：

|屬性 |值 |描述 |
|---|---|---|
|**名稱** |使用者的全名 | 使用者的名字和姓氏
|**使用者名稱** |user<span>@domain.com</span> | 用來識別使用者的使用者名稱|
|**主旨** |主體 |用來跨網站唯一識別使用者的字串|
|**租用戶識別碼** |Guid | 唯一代表使用者 Azure AD 組織的 **guid**|

此外，您應該會看到驗證要求中所有宣告的資料表。 如需詳細資訊，請參閱[識別碼權杖中的宣告清單](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>對具有 Authorize 屬性 (選用) 的方法進行存取測試

若要利用匿名使用者身分測試以 `Authorize` 屬性保護的控制器存取權限，請遵循以下步驟：

1. 選取連結以將使用者登出，並完成登出程序。
2. 在您的瀏覽器中輸入 http://<span></span>localhost:{port}/claims，存取以 `Authorize` 屬性保護的控制器。

#### <a name="expected-results-after-access-to-a-protected-controller"></a>存取受保護控制站之後的預期結果

系統會提示您使用受保護的控制器檢視進行驗證。

## <a name="advanced-options"></a>進階選項

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>保護您的整個網站
若要保護整個網站，請在 **Global.asax** 檔案中，將 `AuthorizeAttribute` 屬性新增至 `Application_Start` 方法中的 `GlobalFilters` 篩選：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>限制誰可以登入您的應用程式

根據預設，當您建置依照本指南建立的應用程式時，將能夠接受使用個人帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及與 Microsoft 身分識別平台整合的公司或組織中的公司與學校帳戶登入。 這是 SaaS 應用程式的建議選項。

若要限制使用者登入應用程式的存取權限，可使用多個選項。

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>選項 1：註冊只來自一個組織之 Active Directory 執行個體的使用者以登入您的應用程式 (單一租用戶)

此選項通常用於 LOB 應用程式  ：如果您想要讓應用程式只接受特定 Azure AD 執行個體的成員帳戶進行登入 (包括該執行個體的*來賓帳戶*)，請遵循下列步驟：

1. 在 web.config 檔案中，將 `Tenant` 參數的值從 `Common` 變更為組織的租用戶名稱 (如 `contoso.onmicrosoft.com`)。
2. 在您的 [OWIN Startup 類別](#configure-the-authentication-pipeline) 中，將 `ValidateIssuer` 引數設為 `true`。

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>選項 2：將存取限制為特定組織清單內的使用者

您可以將登入存取限制為僅限允許組織清單所示 Azure AD 組織中的使用者帳戶：
1. 在您的 [OWIN Startup 類別](#configure-the-authentication-pipeline) 中，將 `ValidateIssuer` 引數設為 `true`。
2. 將 `ValidIssuers` 參數的值設為允許組織的清單。

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>選項 3：使用自訂方法來驗證簽發者

您可以實作自訂方法，使用 **IssuerValidator** 參數來驗證簽發者。 如需此參數使用方式的詳細資訊，請參閱 [TokenValidationParameters 類別](/previous-versions/visualstudio/dn464192(v=vs.114))。

## <a name="next-steps"></a>後續步驟

深入了解 Web 應用程式如何呼叫 Web API。

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>了解如何建立本快速入門指南中所使用的應用程式

深入了解使用 Microsoft 身分識別平台呼叫 Web API 的 Web 應用程式：

> [!div class="nextstepaction"]
> [呼叫 Web API 的 Web 應用程式](scenario-web-app-sign-user-overview.md) \(英文\)

了解如何建立可呼叫 Microsoft Graph 的 Web 應用程式：

> [!div class="nextstepaction"]
> [Microsoft Graph ASP.NET 教學課程](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

協助我們改善 Microsoft 身分識別平台。 完成問卷調查的兩個問題，告訴我們您的想法：

> [!div class="nextstepaction"]
> [Microsoft 身分識別平台問卷調查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
