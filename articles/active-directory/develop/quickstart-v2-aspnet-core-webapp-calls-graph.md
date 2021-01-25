---
title: 快速入門：登入使用者並呼叫 Microsoft Graph 的 ASP.NET Core Web 應用程式 | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您將了解應用程式如何使用 OpenID Connect 和 Microsoft.Identity.Web 在 ASP.NET Core Web 應用程式中實作 Microsoft 登入，並呼叫 Microsoft Graph
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 8e54f71ef58b3ea76a5fe55347a1caa173046320
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754500"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>快速入門：式登入使用者並代表他們呼叫 Microsoft Graph 的 ASP.NET Core Web 應用程

在本快速入門中，您會下載並執行程式碼範例，該範例會示範 ASP.NET Core Web 應用程式如何從任何 Azure Active Directory (Azure AD) 組織登入使用者並呼叫 Microsoft Graph。  

如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>必要條件
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 或 [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>註冊並下載快速入門應用程式
> 有兩個選項可用來啟動快速入門應用程式：
> * [快速] [選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手動] [選項 2：註冊並手動設定您的應用程式和程式碼範例](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetCoreWebAppQuickstartPage/sourceType/docs" target="_blank">Azure 入口網站 - 應用程式註冊<span class="docon docon-navigate-external x-hidden-focus"></span></a>快速入門體驗。
> 1. 輸入應用程式的名稱，並選取 [註冊]  。
> 1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例
>
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
> 若要手動註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：
>
> 1. 登入 <a href="https://portal.azure.com/" target="_blank">Azure 入口網站<span class="docon docon-navigate-external x-hidden-focus"></span></a>。
> 1. 如果您有多個租用的存取權，請使用頂端功能表中的 **目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
> 1. 搜尋並選取 [Azure Active Directory]  。
> 1. 在 **管理** 下選取 [應用程式註冊] > [新增註冊]。
> 1. 輸入應用程式的 [名稱]，例如 `AspNetCoreWebAppCallsGraph-Quickstart`。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
> 1. 輸入值為 `https://localhost:44321/signin-oidc` 的 [重新導向 URI]。
> 1. 選取 [註冊]。
> 1. 在 [管理] 底下，選取 [驗證]。
> 1. 輸入值為 `https://localhost:44321/signout-oidc` 的 [登出 URL]。
> 1. 選取 [儲存]。
> 1. 在 **管理** 下，選取 [憑證與秘密] > [新增用戶端密碼]。
> 1. 輸入 [描述]，例如 `clientsecret1`。
> 1. 選取 [1 年後] 作為祕密的到期日。
> 1. 選取 [新增]，然後立即記錄祕密的 **值**，以用於後續步驟。 祕密值「不會再次顯示」，也不能以其他任何方式取得。 將其記錄在安全的位置，就像任何密碼一樣。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式
> 若要讓本快速入門中的程式碼範例能正常運作，您需要新增 `https://localhost:44321/signin-oidc` 作為回覆 URL，並新增 `https://localhost:44321/signout-oidc` 作為登出 URL。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這項變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-aspnet-webapp/green-check.png) 您的應用程式已設定了這些屬性。

#### <a name="step-2-download-the-aspnet-core-project"></a>步驟 2:下載 ASP.NET Core 專案

> [!div renderon="docs"]
> [下載 the ASP.NET Core 解決方案](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> 執行專案。

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [下載程式碼範例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步驟 3：您的應用程式已設定並準備好執行
> 我們已使用您的應用程式屬性值來設定您的專案，且專案已可供執行。
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>步驟 3：設定 ASP.NET Core 專案
> 1. 將 .zip 封存檔解壓縮至磁碟機根目錄附近的本機資料夾。 例如，解壓縮至 *C:\Azure-Samples*。
> 1. 在 Visual Studio 2019 中開啟解決方案。
> 1. 開啟 *appsettings.json* 檔案，然後修改下列內容：
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - 將 `Enter_the_Application_Id_here` 取代為您在 Azure 入口網站中註冊的應用程式的 **應用程式 (用戶端) 識別碼**。 您可以在應用程式的 [概觀] 頁面中找到 **應用程式 (用戶端) 識別碼**。
>    - 將 `common` 取代為下列其中一項：
>       - 如果您的應用程式支援 [僅此組織目錄中的帳戶]，請將此值取代為 **目錄 (租用戶) 識別碼** (即 GUID) 或 **租用戶名稱** (例如 `contoso.onmicrosoft.com`)。 您可以在應用程式的 [概觀] 頁面上找到 **目錄 (租用戶) 識別碼**。
>       - 如果您的應用程式支援 [任何組織目錄中的帳戶]，請將此值取代為 `organizations`
>       - 如果您的應用程式支援 [所有 Microsoft 帳戶使用者]，請讓此值保持為 `common`
>    - 將 `Enter_the_Client_Secret_Here` 取代為您在先前步驟中建立且記錄的 **用戶端密碼**。
> 
> 在本快速入門中，請勿變更 *appsettings.json* 檔案中的任何其他值。
>
> #### <a name="step-4-build-and-run-the-application"></a>步驟 4：建置並執行應用程式
>
> 在 Visual Studio 中建置並執行應用程式，方法是選取 [偵錯] 功能表 > [開始偵錯]，或按下 `F5` 鍵。
>
> 系統會提示您輸入認證，然後要求您同意應用程式所需的權限。 選取同意提示中的 [接受]。
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="[同意] 對話方塊，其中顯示應用程式從 > 使用者要求的權限":::
>
> 同意所要求的權限之後，應用程式會顯示您已使用 Azure Active Directory 認證成功登入，並且可以在頁面的 [API 結果] 區段中看到您的電子郵件地址。 這是使用 Microsoft Graph 所擷取的。
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="顯示執行中 Web 應用程式和使用者已登入的網頁瀏覽器":::

## <a name="about-the-code"></a>關於程式碼

本節概述登入使用者代表他們呼叫 Microsoft Graph API 所需的程式碼。 本概觀有助於了解程式碼的運作方式、主要引數，以及如何將登入新增至現有的 ASP.NET Core 應用程式並呼叫 Microsoft Graph。 這會用到 [MSAL.NET](msal-overview.md) 的包裝函式 [Microsoft.Identity.Web](microsoft-identity-web.md)。

### <a name="how-the-sample-works"></a>此範例的運作方式
![示範本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>啟始類別

*Microsoft.AspNetCore.Authentication* 中介軟體會使用 `Startup` 類別，此類別會在裝載程序初始化時執行：

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()` 方法會將服務設定為可新增 Cookie 型驗證 (用於瀏覽器案例)，以及可對 OpenID Connect 設定挑戰。

包含 `.AddMicrosoftIdentityWebApp` 的程式碼行會將 Microsoft 身分識別驗證新增至您的應用程式。 這是由 [Microsoft.Identity.Web](microsoft-identity-web.md) 所提供。 然後設定為使用 Microsoft 身分識別平臺來登入，根據 `AzureAD` 設定檔 *上appsettings.js* 一節中的資訊來登入：

| *appsettings.json* 金鑰 | 描述                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | 於 Azure 入口網站中註冊的應用程式的 **應用程式 (用戶端) 識別碼**。                                                                                       |
| `Instance`             | 使用者進行驗證的 Security Token Service (STS) 端點。 此值通常為 `https://login.microsoftonline.com/`，代表 Azure 公用雲端。 |
| `TenantId`             | 租用戶名稱或其租用戶識別碼 (即 GUID)，或 *common* (使用公司或學校帳戶或 Microsoft 個人帳戶來登入使用者)。                             |

`EnableTokenAcquisitionToCallDownstreamApi` 方法可讓您的應用程式取得權杖，以呼叫受保護的 Web API。 `AddMicrosoftGraph` 可讓您的控制器或 Razor 頁面直接受益於 `GraphServiceClient` (藉由相依性插入)，而 `AddInMemoryTokenCaches` 方法可讓您的應用程式受益於權杖快取。

`Configure()` 方法包含兩個重要方法：`app.UseAuthentication()` 和 `app.UseAuthorization()`，可啟用其各自的具名功能。 此外，在 `Configure()` 方法中，您必須使用至少一個 `endpoints.MapControllerRoute()` 呼叫或 `endpoints.MapControllers()` 呼叫來註冊 Microsoft 身分識別 Web 的路由。

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>保護控制器或控制器的方法

您可以藉由將 `[Authorize]` 屬性套用至控制器的類別或其中一個或多個方法，來保護控制器或其方法。 此 `[Authorize]` 屬性會設定限制，只允許經過驗證的使用者進行存取。 如果使用者尚未經過驗證，即可啟動存取控制器的驗證挑戰。 在本快速入門中，範圍會從設定檔中讀取：

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>後續步驟

本快速入門中所參考的 ASP.NET Core 程式碼範例位於 GitHub 存放庫，而該存放庫也包含執行下列作業的指示和更多程式碼範例：

- 將驗證新增至新的 ASP.NET Core Web 應用程式
- 呼叫 Microsoft Graph、其他 Microsoft API 或您自己的 Web API
- 新增授權
- 以國家雲端或社交身分識別登入使用者

> [!div class="nextstepaction"]
> [GitHub 上的 ASP.NET Core Web 應用程式教學課程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
