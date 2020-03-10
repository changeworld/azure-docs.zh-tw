---
title: 將 Microsoft 身分識別平台登入新增至 ASP.NET Web 應用程式 | Azure
description: 了解如何使用 OpenID Connect 在 ASP.NET Web 應用程式上實作 Microsoft 登入。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: eae26df61af203f9c3d09606ef96b5506f2e8701
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249112"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>快速入門：將 Microsoft 身分識別平台登入新增至 ASP.NET Web 應用程式
在本快速入門中，您將利用程式碼範例了解 ASP.NET Web 應用程式如何從任何 Azure Active Directory (Azure AD) 執行個體登入個人帳戶 (hotmail.com、outlook.com 等) 與公司和學校帳戶。  (如需圖例，請參閱[此範例的運作方式](#how-the-sample-works)。)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>註冊並下載快速入門應用程式
> 有兩個選項可用來啟動快速入門應用程式：
> * [快速] [選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手動] [選項 2：註冊並手動設定您的應用程式和程式碼範例](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>選項 1：註冊和自動設定您的應用程式，然後下載程式碼範例
>
> 1. 移至新的 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)窗格。
> 1. 輸入您的應用程式名稱，然後按一下 [註冊]  。
> 1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>選項 2：註冊並手動設定您的應用程式和程式碼範例
>
> #### <a name="step-1-register-your-application"></a>步驟 1:註冊您的應用程式
> 若要手動註冊您的應用程式，並將應用程式註冊資訊新增到您的解決方案，請執行下列步驟：
>
> 1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
> 1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
> 1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
> 1. 選取 [新增註冊]  。
> 1. 當 [註冊應用程式]  頁面出現時，輸入您應用程式的註冊資訊：
>      - 在 [名稱]  區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `ASPNET-Quickstart`。
>      - 在 [重新導向 URL]  中新增 `http://localhost:44368/`，然後按一下 [註冊]  。
>      - 從左側流覽窗格的 [管理] 區段下，選取 [驗證] 
>          - 在 [隱含授與]  子區段底下，選取 [識別碼權杖]  。
>          - 然後選取 [儲存]  。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>步驟 1:在 Azure 入口網站中設定您的應用程式
> 若要讓本快速入門中的程式碼範例能正常運作，您需要新增 `https://localhost:44368/` 作為回覆 URL。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [為我進行這項變更]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已設定](media/quickstart-v2-aspnet-webapp/green-check.png) 您的應用程式已設定了這個屬性

#### <a name="step-2-download-your-project"></a>步驟 2:下載您的專案

> [!div renderon="docs"]
> [下載 Visual Studio 2019 解決方案](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal"]
> 使用 Visual Studio 2019 執行專案。
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [下載程式碼範例]()

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步驟 3：您的應用程式已設定並準備好執行
> 我們已使用您的應用程式屬性值來設定您的專案。 

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>步驟 3：執行 Visual Studio 專案

1. 將 ZIP 檔案解壓縮至根資料夾附近的本機資料夾 - 例如 **C:\Azure-Samples**
1. 在 Visual Studio 中開啟解決方案 (AppModelv2-WebApp-OpenIDConnect-DotNet.sln)
1. 根據 Visual Studio 版本而定，您可能需要在專案 `AppModelv2-WebApp-OpenIDConnect-DotNet` 上按一下滑鼠右鍵並選取 [還原 NuGet 套件] 
1. 開啟套件管理員 (檢視 -> 其他視窗 -> 套件管理員主控台) 並執行 `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`

> [!div renderon="docs"]
> 5. 編輯 **Web.config**，並將 `ClientId` 和 `Tenant` 參數取代為：
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    ```
>    其中：
> - `Enter_the_Application_Id_here` - 是您註冊的應用程式所具備的應用程式識別碼。
> - `Enter_the_Tenant_Info_Here` - 是下列選項之一：
>   - 如果您的應用程式支援 [僅限我的組織]  ，請將此值取代為 [租用戶識別碼]  或 [租用戶名稱]  (例如 contoso.onmicrosoft.com)
>   - 如果您的應用程式支援 [任何組織目錄中的帳戶]  ，請將此值取代為 `organizations`
>   - 如果您的應用程式支援 [所有 Microsoft 帳戶使用者]  ，請將此值取代為 `common`
>
> > [!TIP]
> > - 若要尋找 [應用程式識別碼]  、[目錄 (租用戶) 識別碼]  和 [支援的帳戶類型]  ，請移至 [概觀]  頁面
> > - 確定 **Web.config** 中的 `redirectUri` 值對應至針對 AzureAD 中的應用程式註冊所定義的**重新導向 URI** (如果不是，請瀏覽至應用程式註冊的 [驗證]  功能表，並更新 [重新導向 URI]  以使兩者相符)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

## <a name="more-information"></a>詳細資訊

本節會概述登入使用者所需的程式碼。 本概觀有助於了解程式碼的運作方式、主要引數，以及如何將登入新增至現有的 ASP.NET 應用程式。

### <a name="how-the-sample-works"></a>此範例的運作方式
![示範本快速入門所產生之範例應用程式的運作方式](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN 中介軟體 NuGet 套件

您可以使用 OWIN 中介軟體套件在 ASP.NET 中搭配 OpenID Connect，設定具有以 Cookie 為基礎之驗證的驗證管線。 您可以在 Visual Studio 的 [套件管理員主控台]  中執行下列命令來安裝這些套件：

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb  
```

### <a name="owin-startup-class"></a>OWIN 啟動類別

OWIN 中介軟體使用*啟動類別*，此類別會在裝載處理序初始時執行。 在本快速入門中，*startup.cs* 檔案位於根資料夾中。 以下程式碼顯示本快速入門所使用的參數：

```csharp
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
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  |  |
> |---------|---------|
> | `ClientId`     | 來自註冊於 Azure 入口網站中之應用程式的應用程式識別碼 |
> | `Authority`    | 供使用者用於驗證的 STS 端點。 通常針對公用雲端為 <https://login.microsoftonline.com/{tenant}/v2.0>，其中 {tenant} 為您租用戶的名稱、您的租用戶識別碼，或 *common* 以參考一般端點 (用於多租用戶應用程式) |
> | `RedirectUri`  | 在使用者針對 Azure 身分識別平台端點完成驗證之後，會被送往的 URL |
> | `PostLogoutRedirectUri`     | 在使用者登出之後，會被送往的 URL |
> | `Scope`     | 所要求之範圍的清單 (以空格分隔) |
> | `ResponseType`     | 來自驗證的回應包含識別碼權杖的要求 |
> | `TokenValidationParameters`     | 用於權杖驗證的參數清單。 在此案例中，`ValidateIssuer` 是設為 `false`，以表示它可以接受來自任何個人或公司或學校帳戶類型的登入 |
> | `Notifications`     | 可在不同 *OpenIdConnect* 訊息上執行之委派的清單 |


> [!NOTE]
> 設定 `ValidateIssuer = false` 可簡化此快速入門。 在實際的應用程式中，您需要驗證簽發者。
> 請參閱範例以了解如何執行該動作。

### <a name="initiate-an-authentication-challenge"></a>起始驗證挑戰

您可在控制器中要求驗證挑戰，以強制使用者登入：

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> 使用上述方法來要求驗證挑戰是選擇性的，而且通常適用於您想要讓已驗證和未驗證使用者都能存取檢視的情況。 或者，您可以透過下一節所描述的方法來保護控制器。

### <a name="protect-a-controller-or-a-controllers-method"></a>保護控制器或控制器的方法

您可以使用 `[Authorize]` 屬性來保護控制器或控制器動作。 此屬性會透過只允許已驗證的使用者存取控制器中的動作，來限制對控制器或動作的存取；這表示當*未驗證*的使用者嘗試存取具有 `[Authorize]` 屬性的其中一個動作或控制器時，系統將會自動執行驗證挑戰。

## <a name="next-steps"></a>後續步驟

嘗試 ASP.NET 教學課程，以取得建置應用程式和新功能的完整逐步指南，包括本快速入門的完整說明。

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>了解建立本快速入門中所使用之應用程式的步驟

> [!div class="nextstepaction"]
> [登入教學課程](./tutorial-v2-asp-webapp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

協助我們改善 Microsoft 身分識別平台。 完成問卷調查簡短的兩個問題，告訴我們您的想法。

> [!div class="nextstepaction"]
> [Microsoft 身分識別平台問卷調查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
