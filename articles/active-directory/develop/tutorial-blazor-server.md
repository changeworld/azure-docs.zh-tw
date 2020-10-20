---
title: 教學課程 - 建立使用 Microsoft 身分識別平台進行驗證的 Blazor Server 應用程式 | Azure
titleSuffix: Microsoft identity platform
description: 在本教學課程中，您會在 Blazor Server 應用程式中使用 Microsoft 身分識別平台來設定驗證。
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 429d0b9c3a118061d713484a7db3aca376a24d04
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873178"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>教學課程：建立使用 Microsoft 身分識別平台進行驗證的 Blazor Server 應用程式

Blazor Server 提供在 ASP.NET Core 應用程式中將 Razor 元件裝載在伺服器上的支援。 在本教學課程中，您將了解如何使用 Microsoft 身分識別平台，從 Blazor Server 應用程式中的 Microsoft Graph 實作驗證和擷取資料。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立設定為使用 Azure Active Directory (Azure AD) 進行驗證的新 Blazor Server 應用程式
> * 使用 Microsoft.Identity.Web 處理驗證與授權
> * 從受保護的 Web API 擷取資料，Microsoft Graph

## <a name="prerequisites"></a>必要條件

- [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- 您可以註冊應用程式的 Azure AD 租用戶。 如果您沒有 Azure AD 租用戶的存取權，可以向 [Microsoft 365 開發人員方案](https://developer.microsoft.com/microsoft-365/dev-program)註冊或建立 [Azure 免費帳戶](https://azure.microsoft.com/free)來取得一個。

## <a name="register-the-app-in-the-azure-portal"></a>在 Azure 入口網站中註冊應用程式

使用 Azure Active Directory (Azure AD) 進行驗證的每個應用程式都必須向 Azure AD 註冊。 遵循[註冊應用程式](quickstart-register-app.md)中的指示新增下列項目：

- 在 [支援的帳戶類型] 區段中，選取 [僅限此組織目錄中的帳戶]。
- 將 [重新導向 URI] 下拉式設定保留為 [Web]，然後輸入 `https://localhost:5001/signin-oidc`。 在 Kestrel 上執行應用程式的預設連接埠是 5001。 如果應用程式可在不同的連接埠上使用，請指定該連接埠號碼，而不是 `5001`。

在 [驗證] > [隱含授與] 中，選取 [存取權杖] 和 [識別碼權杖] 的核取方塊，然後選取 [儲存] 按鈕。

最後，因為應用程式會呼叫受保護的 API (在此案例中為 Microsoft Graph)，所以其需要用戶端密碼，才能在要求存取權杖呼叫該 API 時驗證其身分識別。

1. 在相同的應用程式註冊中，於 [管理] 底下，選取 [憑證與秘密]。
2. 建立永不過期的 [新用戶端密碼]。
3. 請記下祕密的 [值]，因為您在下一個步驟中將會用到。 當您離開此窗格之後，就無法再次存取。 不過，您可以視需要將其重新建立。

## <a name="create-the-app-using-the-net-cli"></a>使用 .NET CLI 建立應用程式

執行下列命令來下載適用於 Microsoft.Identity.Web 的範本，我們將在本教學課程中使用。

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

接著，執行下列命令來建立應用程式。 將命令中的預留位置取代為您應用程式概觀頁面中的適當資訊，並在命令殼層中執行命令。 使用 `-o|--output` 選項指定的輸出位置會建立專案資料夾 (如果不存在)，並成為應用程式名稱的一部分。

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| 預留位置   | Azure 入口網站名稱       | 範例                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | 應用程式 (用戶端) 識別碼 | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | 目錄 (租用戶) 識別碼   | `e86c78e2-0000-0000-0000-918e0565a45e` |

現在，在您的編輯器中瀏覽至新的 Blazor 應用程式，並將用戶端密碼新增至 appsettings.json 檔案，以取代 "secret-from-app-registration" 文字。

```json
"ClientSecret": "xkAlNiG70000000_UI~d.OS4Dl.-Cy-1m3",
```

## <a name="test-the-app"></a>測試應用程式

您現在可以建置並執行應用程式。 當您執行此範本應用程式時，必須使用 --framework 來指定要執行的架構。 本教學課程使用 .NET Core 3.1 SDK。

```dotnetcli
dotnet run --framework netcoreapp3.1
```

在您的瀏覽器中，瀏覽至 `https://localhost:5001`，然後使用 Azure AD 的使用者帳戶登入，以查看執行中的應用程式。

## <a name="retrieving-data-from-microsoft-graph"></a>正在從 Microsoft Graph 中擷取資料

[Microsoft Graph](/graph/overview) 提供了一系列的 API，可讓您存取使用者的 Microsoft 365 資料。 因為 Microsoft Graph 直接支援 Microsoft 身分識別平台所發行的權杖，您可以使用 Microsoft 身分識別平台做為應用程式的身分識別提供者，藉此更輕鬆地存取這項資訊。 在本節中，您會新增程式碼，以在應用程式的 [提取資料] 頁面上顯示已登入使用者的電子郵件。

開始之前，請先登出您的應用程式，因為您將會變更必要的權限，而目前的權杖將無法使用。 如果您還沒有這麼做，請再次執行您的應用程式並選取 [登出]，然後再更新下面的程式碼。

現在您會更新應用程式的註冊和程式碼，以提取使用者的電子郵件，並在應用程式內顯示訊息。 若要達到此目的，請先在 Azure AD 中延伸應用程式註冊權限，以啟用電子郵件資料的存取權。 然後，將程式碼新增至 Blazor 應用程式，以在其中一個頁面中擷取並顯示此資料。

1. 在 Azure 入口網站的 [應用程式註冊] 中，選取您的應用程式。
1. 在 [管理] 之下選取 [API 權限]。
1. 選取 [新增權限] > [Microsoft Graph]。
1. 選取 [委派權限]，然後搜尋並選取 [Mail.Read] 權限。
1. 選取 [新增權限]。

在 appsettings.json 檔案中，更新您的程式碼，以便使用正確的權限來擷取適當的權杖。 在 "DownstreamAPI" 下的 "user.read" 範圍後面新增 "mail.read"。 這是用來指定應用程式會要求存取的範圍 (或權限)。

```json
"Scopes": "user.read mail.read"
```

接下來，更新 FetchData.razor 檔案中的程式碼，以擷取電子郵件資料，而不是預設的 (隨機) 氣象細節。 將該檔案中的程式碼取代為下列內容：

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

啟動應用程式。 您會注意到，系統會提示您輸入新增的權限，表示所有項目都如預期般運作。 現在，除了基本的使用者設定檔資料之外，應用程式也會要求存取電子郵件資料。

授與同意之後，請瀏覽至 [擷取資料] 頁面，以讀取部分電子郵件。

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="最終應用程式的螢幕擷取畫面。其中有標題顯示 Hello Nicholas，並會顯示屬於 Nicholas 的電子郵件清單。":::

## <a name="next-steps"></a>下一步

了解在多部分案例系列中呼叫會登入使用者的建置 Web 應用程式：

> [!div class="nextstepaction"]
> [案例：登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)
