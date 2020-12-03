---
title: 教學課程 - 從 Blazor WebAssembly 應用程式登入使用者並呼叫受保護的 API
titleSuffix: Microsoft identity platform
description: 在本教學課程中，您將使用 Blazor WebAssembly (WASM) 應用程式中的 Microsoft 身分識別平台來登入使用者並呼叫受保護的 API。
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 09e922ffddcce732d9213eb91026561528c0728a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169132"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>教學課程：從 Blazor WebAssembly 應用程式登入使用者並呼叫受保護的 API

在本教學課程中，您會建置 Blazor WebAssembly 應用程式，以藉由使用 Microsoft 身分識別平台並在 Azure Active Directory (Azure AD) 中註冊應用程式，來讓使用者登入並從 Microsoft Graph 取得資料。

我們也提供 [Blazor Server 的教學課程](tutorial-blazor-server.md)。 

在本教學課程中：

> [!div class="checklist"]
>
> * 建立新的 Blazor WebAssembly 應用程式，並設定為使用 Azure Active Directory (Azure AD) 來透過 Microsoft 身分識別平台進行[驗證和授權](authentication-vs-authorization.md)
> * 從受保護的 Web API 擷取資料，在此案例中為 [Microsoft Graph](/graph/overview)

## <a name="prerequisites"></a>必要條件

* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* 您可以註冊應用程式的 Azure AD 租用戶。 如果您沒有 Azure AD 租用戶的存取權，可以向 [Microsoft 365 開發人員方案](https://developer.microsoft.com/microsoft-365/dev-program)註冊或建立 [Azure 免費帳戶](https://azure.microsoft.com/free)來取得一個。

## <a name="register-the-app-in-the-azure-portal"></a>在 Azure 入口網站中註冊應用程式

使用 Azure Active Directory (Azure AD) 進行驗證的每個應用程式都必須向 Azure AD 註冊。 遵循以這些規格[註冊應用程式](quickstart-register-app.md)的指示：

- 在 [支援的帳戶類型] 區段中，選取 [僅限此組織目錄中的帳戶]。
- 將 [重新導向 URI] 下拉式設定保留為 [Web]，然後輸入 `https://localhost:5001/authentication/login-callback`。 在 Kestrel 上執行應用程式的預設連接埠是 5001。 如果應用程式可在不同的連接埠上使用，請指定該連接埠號碼，而不是 `5001`。

註冊後，在 [驗證] > [隱含授與] 中，選取 [存取權杖] 和 [識別碼權杖] 的核取方塊，然後選取 [儲存] 按鈕。

## <a name="create-the-app-using-the-net-core-cli"></a>使用 .NET Core CLI 建立應用程式

若要建立應用程式，您需要最新的 Blazor 範本。 您可以使用下列 .NET Core CLI 命令進行安裝：

```dotnetcli
dotnet new --install Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.1
```

接著，執行下列命令來建立應用程式。 將命令中的預留位置取代為您應用程式概觀頁面中的適當資訊，並在命令殼層中執行命令。 使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| 預留位置   | Azure 入口網站名稱       | 範例                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | 應用程式 (用戶端) 識別碼 | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | 目錄 (租用戶) 識別碼   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>測試應用程式

您現在可以建置並執行應用程式。 當您執行此範本應用程式時，必須使用 --framework 來指定要執行的架構。 本教學課程使用 .NET Standard 2.1，但範本也支援其他架構。

```dotnetcli
dotnet run --framework netstandard2.1
```

在您的瀏覽器中，瀏覽至 `https://localhost:5001`，然後使用 Azure AD 使用者帳戶登入，以查看使用 Microsoft 身分識別平台執行並登入使用者的應用程式。

此範本的元件會啟用以 Microsoft 身分識別平台搭配 Azure AD 的登入作業，如[本主題的 ASP.NET 文件](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package)所述。

## <a name="retrieving-data-from-microsoft-graph"></a>正在從 Microsoft Graph 中擷取資料

[Microsoft Graph](/graph/overview) 提供了一系列的 API，可讓您存取租用戶中使用者的 Microsoft 365 資料。 因為 Microsoft Graph 直接支援 Microsoft 身分識別平台所發行的權杖，您可以使用 Microsoft 身分識別平台做為應用程式的身分識別提供者，藉此更輕鬆地存取這項資訊。 在本節中，您會新增程式碼，以在應用程式的 [提取資料] 頁面上顯示已登入使用者的電子郵件。

開始之前，請先登出您的應用程式，因為您將會變更必要的權限，而目前的權杖將無法使用。 如果您還沒有這麼做，請再次執行您的應用程式並選取 [登出]，然後再更新下面的程式碼。

現在您會更新應用程式的註冊和程式碼，以提取使用者的電子郵件，並在應用程式內顯示訊息。

首先，將 `Mail.Read` API 權限新增至應用程式的註冊，讓 Azure AD 知道應用程式會要求存取其使用者的電子郵件。

1. 在 Azure 入口網站的 [應用程式註冊] 中，選取您的應用程式。
1. 在 [管理] 之下選取 [API 權限]。
1. 選取 [新增權限] > [Microsoft Graph]。
1. 選取 [委派權限]，然後搜尋並選取 [Mail.Read] 權限。
1. 選取 [新增權限]。

接下來，在 netstandard2.1 **ItemGroup** 中，將下列項目新增至您專案的 .csproj 檔案。 這可讓您在下一個步驟中建立自訂 HttpClient。

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

然後修改接下來幾個步驟中指定的程式碼。 這些變更會將[存取權杖](access-tokens.md)至傳送至 Microsoft Graph API 的傳出要求。 此模式會在 [ASP.NET Core Blazor WebAssembly 其他安全性案例](/aspnet/core/blazor/security/webassembly/additional-scenarios)中更詳細地討論。

首先，使用下列程式碼建立名為 GraphAuthorizationMessageHandler.cs 的新檔案。 此處理程式將會針對 `User.Read` 和 `Mail.Read` 範圍，將存取權杖新增至 Microsoft Graph API 的傳出要求。

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

將 Program.cs 中的 `Main` 方法內容取代為下列程式碼。 這段程式碼會使用新的 `GraphAPIAuthorizationMessageHandler`，並將 `User.Read` 和 `Mail.Read` 新增為應用程式在使用者第一次登入時會要求的預設範圍。

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

最後，將 FetchData.razor 頁面內容取代為下列程式碼。 此程式碼會從 Microsoft Graph API 提取使用者電子郵件資料，並將其顯示為清單。 `OnInitializedAsync` 中會建立使用適當存取權杖的新 `HttpClient`，並用來對 Microsoft Graph API 提出要求。

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

現在重新啟動應用程式。 您會注意到，系統會提示您授與應用程式讀取您郵件的存取權。 這是應用程式要求 `Mail.Read` 範圍時的預期情況。

授與同意之後，請瀏覽至 [擷取資料] 頁面，以讀取部分電子郵件。

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="最終應用程式的螢幕擷取畫面。其中有標題顯示 Hello Nicholas，並會顯示屬於 Nicholas 的電子郵件清單。":::

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [Microsoft 身分識別平台最佳做法和建議](./identity-platform-integration-checklist.md)