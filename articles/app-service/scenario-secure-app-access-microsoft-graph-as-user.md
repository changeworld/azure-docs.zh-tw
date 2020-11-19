---
title: 教學課程 - Web 應用程式以使用者身分存取 Microsoft Graph | Azure
description: 在本教學課程中，您將了解如何代表已登入的使用者存取 Microsoft Graph 中的資料。
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ef007f045a5c53bf70f6d042167c157ab3f4decc
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428184"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>教學課程：從安全的應用程式以使用者身分存取 Microsoft Graph

了解如何從在 Azure App Service 上執行的 Web 應用程式存取 Microsoft Graph。

:::image type="content" alt-text="存取 Microsoft Graph" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

您想要新增從 Web 應用程式存取 Microsoft Graph 的權限，並以登入的使用者身分執行某些動作。 本節說明如何將委派的權限授與 Web 應用程式，並從 Azure Active Directory 取得登入使用者的設定檔資訊。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 將委派的權限授與 Web 應用程式
> * 代表已登入的使用者從 Web 應用程式呼叫 Microsoft Graph

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

* 在 Azure App Service 上執行且已啟用 [App Service 驗證/授權模組](scenario-secure-app-authentication-app-service.md)的 Web 應用程式。

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>授與呼叫 Microsoft Graph 的前端存取權

您已在 Web 應用程式上啟用驗證和授權，Web 應用程式會向 Microsoft 身分識別平台註冊，並受到 Azure AD 應用程式的支援。 在此步驟中，您會為 Web 應用程式提供代表使用者存取 Microsoft Graph 的權限。 (技術上，您會為 Web 應用程式的 Azure AD 應用程式提供代表使用者存取 Microsoft Graph AD 應用程式的權限。)

在 Azure 入口網站功能表中，選取 [Azure Active Directory][](https://portal.azure.com)，或從任何頁面搜尋並選取 [Azure Active Directory]。

選取 [應用程式註冊] > [擁有的應用程式] > [檢視此目錄中的所有應用程式]。 選取您的 Web 應用程式名稱，然後選取 [API 權限]。

選取 [新增權限]，然後選取 Microsoft API 和 Microsoft Graph。

選取 [委派的權限]，然後從清單中選取 [User.Read]。  按一下 [新增權限]。

## <a name="configure-app-service-to-return-a-usable-access-token"></a>設定 App Service，以傳回可使用的存取權杖

Web 應用程式現在具有必要權限，能夠以登入使用者的身分存取 Microsoft Graph。 在此步驟中，您會設定 App Service 驗證和授權，讓自己取得可用來存取 Microsoft Graph 的存取權杖。 在此步驟中，您需要下游服務的用戶端/應用程式識別碼 (Microsoft Graph)。 00000003-0000-0000-c000-000000000000 是 Microsoft Graph 的應用程式識別碼。

> [!IMPORTANT]
> 如果您未設定 App Service 傳回可用的存取權杖，當您在程式碼中呼叫 Microsoft Graph API 時，就會收到 ```CompactToken parsing failed with error code: 80049217``` 錯誤。

瀏覽至 [Azure 資源總管](https://resources.azure.com/)並使用資源樹狀結構，找出您的 Web 應用程式。  此資源 URL 應類似於：`https://resources.azure.com/subscriptions/subscription-id/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914`

現在，在資源樹狀結構中選取您的 Web 應用程式，以開啟 Azure 資源總管。 在頁面頂端按一下 [讀取/寫入]，以啟用 Azure 資源的編輯。

在左側瀏覽器中，向下切入至 [config] > [authsettings]。

在 **authsettings** 檢視中，按一下 [編輯]。 使用您複製的用戶端識別碼，將 ```additionalLoginParams``` 設為下列 JSON 字串。

```json
"additionalLoginParams": ["response_type=code id_token","resource=00000003-0000-0000-c000-000000000000"],
```

按一下 **PUT** 以儲存您的設定。 此設定可能需要幾分鐘才會生效。  您的 Web 應用程式現在已設定為使用適當的存取權杖來存取 Microsoft Graph。  如果您沒有這麼做，Microsoft Graph 會傳回錯誤，指出精簡權杖的格式不正確。

## <a name="call-microsoft-graph-net"></a>呼叫 Microsoft Graph (.NET)

您的應用程式現已具有必要的權限，且會將 Microsoft Graph 的用戶端識別碼新增至登入參數。 使用 [Microsoft.Identity.Web library](https://github.com/AzureAD/microsoft-identity-web/)，Web 應用程式會取得 Microsoft Graph 驗證的存取權杖。 在 1.2.0 和更新版本中，Microsoft.Identity.Web 會與 App Service 整合，並可與 App Service 驗證/授權模組一起執行。  Microsoft 會偵測到 Web 應用程式裝載在應用程式服務中，並從應用程式服務驗證/授權模組取得存取權杖。  接著會使用 Microsoft Graph API，將存取權杖傳遞給已驗證的要求。

> [!NOTE]
> Web 應用程式中的基本驗證/授權不需要 Microsoft.Identity.Web 程式庫，也不需使用 Microsoft Graph 驗證要求。  您可以只啟用 App Service 驗證/授權模組，[安全地呼叫下游 API](tutorial-auth-aad.md#call-api-securely-from-server-code)。  
> 不過，App Service 的驗證/授權是針對更基本的驗證案例所設計。  針對更複雜的案例 (例如，處理自訂宣告)，您需要有 Microsoft.Identity.Web 程式庫或 [Microsoft 驗證程式庫](/azure/active-directory/develop/msal-overview)。 一開始還有更多的安裝和設定工作，但是 Microsoft.Identity.Web 程式庫可以與 App Service 驗證/授權模組一起執行。  之後，當 Web 應用程式需要處理更複雜的案例時，您可以停用 App Service 驗證/授權模組，而 Microsoft.Identity.Web 會是您應用程式的一部分。

### <a name="install-client-library-packages"></a>安裝用戶端程式庫套件

使用 .NET Core 命令列介面或 Visual Studio 中的套件管理員主控台，在您的專案中安裝 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) 和 [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph) NuGet 套件。

# <a name="command-line"></a>[命令列](#tab/command-line)

開啟命令列並切換至包含您專案檔的目錄。

執行安裝命令：

```dotnetcli
dotnet add package Microsoft.Graph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[套件管理員](#tab/package-manager)
在 Visual Studio 中開啟專案/方案，然後使用 **工具** > **NuGet 套件管理員** > **套件管理員主控台** 命令開啟主控台。

執行安裝命令：
```powershell
Install-Package Microsoft.Graph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

在 Startup.cs 檔案中，```AddMicrosoftIdentityWebApp``` 方法會將 Microsoft.Identity.Web 新增至您的 Web 應用程式。  ```AddMicrosoftGraph``` 方法會新增 Microsoft Graph 支援。

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

AzureAd 會指定 Microsoft.Identity.Web 程式庫的組態。  在 [Azure 入口網站](https://portal.azure.com)的功能表中選取 [Azure Active Directory]，然後選取 [應用程式註冊]。 選取當您啟用 App Service 驗證/授權模組時所建立的應用程式註冊 (應用程式註冊應該與您的 Web 應用程式名稱相同)。  您可以在應用程式註冊的概觀頁面中找到租用戶識別碼和用戶端識別碼。  您可以在租用戶的 Azure Active Directory 概觀頁面找到此網域名稱。

Graph 會指定應用程式所需的 Microsoft Graph 端點和初始範圍。

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

下列範例示範如何以已登入使用者的身分呼叫 Microsoft Graph，並取得一些使用者資訊。  ```GraphServiceClient``` 物件會插入至控制器，並由 Microsoft.Identity.Web 程式庫為您設定驗證。

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>清除資源

如果您已完成本教學課程，且不再需要 Web 應用程式或相關聯的資源，請[清除您建立的資源](scenario-secure-app-clean-up-resources.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> * 將委派的權限授與 Web 應用程式
> * 代表已登入的使用者從 Web 應用程式呼叫 Microsoft Graph

> [!div class="nextstepaction"]
> [App Service 以應用程式身分存取 Microsoft Graph](scenario-secure-app-access-microsoft-graph-as-app.md)
