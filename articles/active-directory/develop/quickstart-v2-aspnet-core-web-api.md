---
title: 快速入門：以 Microsoft 身分識別平台保護 ASP.NET Core Web API | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您可以下載並修改程式碼範例，練習如何使用 Microsoft 身分識別平台進行授權，以保護 ASP.NET Core 的 Web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: dc0cdca2355403bc8f5409d9a6ca7f4ae89caf25
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943823"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-microsoft-identity-platform"></a>快速入門：以 Microsoft 身分識別平台保護 ASP.NET Core Web API

在本快速入門中，您將使用範例程式碼來瞭解如何保護 ASP.NET Core Web API，使其只能被授權的帳戶存取。 帳戶可以是個人帳戶(hotmail.com、outlook.com 等)，以及任何 Azure Active Directory (Azure AD) 執行個體中的公司和學校帳戶。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>必要條件
>
> - 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> - [Azure Active Directory 租用戶](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 或 [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>步驟 1:註冊應用程式
>
> 首先，請在您的 Azure AD 租用戶中註冊 Web API，並遵循下列步驟來新增範圍：
>
> 1. 登入 [Azure 入口網站](https://portal.azure.com)。
> 1. 如果您有多個租用的存取權，請使用頂端功能表中的**目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
> 1. 搜尋並選取 [Azure Active Directory]  。
> 1. 在 [管理]下選取 [應用程式註冊]，再選取 [新增註冊]。
> 1. 輸入應用程式的 [名稱]，例如 `AspNetCoreWebApi-Quickstart`。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
> 1. 選取 [註冊]。
> 1. 在 [管理] 底下，選取 [公開 API]
> 1. 依序選取 [新增範圍]、[儲存並繼續]，接受預設的 [應用程式識別碼 URI]。
> 1. 在 [新增範圍] 窗格中，輸入下列值：
>    - **範圍名稱**：`access_as_user`
>    - **誰可以同意?** ：**管理員和使用者**
>    - **管理員同意顯示名稱**：`Access AspNetCoreWebApi-Quickstart`
>    - **管理員同意描述**：`Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **使用者同意顯示名稱**：`Access AspNetCoreWebApi-Quickstart`
>    - **使用者同意說明**：`Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **狀態**：**已啟用**
> 1. 選取 [新增範圍] 以完成範圍新增。

## <a name="step-2-download-the-aspnet-core-project"></a>步驟 2:下載 ASP.NET Core 專案

> [!div renderon="docs"]
> 從 GitHub [下載 ASP.NET Core 解決方案](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip)。

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>步驟 3：設定 ASP.NET Core 專案
>
> 在此步驟中設定範例程式碼，以使用您稍早建立的應用程式註冊。
>
> 1. 將 .zip 封存檔解壓縮至磁碟機根目錄附近的資料夾。 例如，解壓縮至 *C:\Azure-Samples*。
> 1. 在程式碼編輯器中，開啟 *webapi* 資料夾中的解決方案。
> 1. 開啟 *appsettings.json* 檔案，然後修改下列內容：
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - 將 `Enter_the_Application_Id_here` 取代為您在 Azure 入口網站中註冊的應用程式的**應用程式 (用戶端) 識別碼**。 您可以在應用程式的 [概觀] 頁面中找到**應用程式 (用戶端) 識別碼**。
>    - 將 `Enter_the_Tenant_Info_Here` 取代為下列其中一項：
>       - 如果您的應用程式支援 [僅此組織目錄中的帳戶]，請將此值取代為**目錄 (租用戶) 識別碼** (即 GUID) 或**租用戶名稱** (例如 `contoso.onmicrosoft.com`)。 您可以在應用程式的 [概觀] 頁面上找到**目錄 (租用戶) 識別碼**。
>       - 如果您的應用程式支援 [任何組織目錄中的帳戶]，請將此值取代為 `organizations`
>       - 如果您的應用程式支援 [所有 Microsoft 帳戶使用者]，請讓此值保持為 `common`
>
> 在本快速入門中，請勿變更 *appsettings.json* 檔案中的任何其他值。

## <a name="how-the-sample-works"></a>此範例的運作方式

Web API 會從用戶端應用程式接收權杖，而 Web API 中的程式碼會驗證權杖。 更詳細的說明請看[案例：受保護的 Web API](scenario-protected-web-api-overview.md)。

### <a name="startup-class"></a>啟始類別

*Microsoft.AspNetCore.Authentication* 中介軟體會使用 `Startup` 類別，此類別會在裝載程序初始化時執行。 在其 `ConfigureServices` 方法中，會呼叫 *Microsoft.Identity.Web* 提供的 `AddMicrosoftIdentityWebApi` 擴充方法。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

`AddAuthentication()` 方法會將服務設定為新增以 JwtBearer 為基礎的驗證。

包含 `.AddMicrosoftIdentityWebApi` 的這行程式碼會將 Microsoft 身分識別平台授權新增至您的 Web API。 接著，會將服務設定為根據 *appsettings.json* 設定檔中 `AzureAD` 區段的資訊，驗證由 Microsoft 身分識別平台端點核發的存取權杖：

| *appsettings.json* 金鑰 | 描述                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | 於 Azure 入口網站中註冊的應用程式的**應用程式 (用戶端) 識別碼**。                                                                                       |
| `Instance`             | 使用者進行驗證的 Security Token Service (STS) 端點。 此值通常為 `https://login.microsoftonline.com/`，代表 Azure 公用雲端。 |
| `TenantId`             | 租用戶名稱或其租用戶識別碼 (即 GUID)，或 *common* (使用公司或學校帳戶或 Microsoft 個人帳戶來登入使用者)。                             |

`Configure()` 方法包含兩個重要方法：`app.UseAuthentication()` 和 `app.UseAuthorization()`，可啟用其各自的具名功能：

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-a-controllers-method-or-a-razor-page"></a>保護控制器、控制器的方法或 Razor 頁面

您可以使用 `[Authorize]` 屬性來保護控制站或控制站方法。 這個屬性會將控制站或方法的存取限制為只允許已驗證的使用者，這表示如果使用者未經過驗證，則可啟動驗證挑戰來存取控制站。

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validate-the-scope-in-the-controller"></a>驗證控制器中的範圍

然後，API 中的程式碼會使用 `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` 來驗證所需的範圍是否在權杖中

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The Web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>下一步

包含本 ASP.NET Core Web API 程式碼範例的 GitHub 存放庫，之中有指示和更多的程式碼範例，可示範如何：

- 將驗證新增至新的 ASP.NET Core Web API
- 從電腦應用程式呼叫 Web API
- 呼叫下游 API，如 Microsoft Graph 和其他 Microsoft API

> [!div class="nextstepaction"]
> [GitHub 上的 ASP.NET Core Web API 教學課程](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
