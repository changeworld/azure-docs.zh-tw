---
title: 教學課程：建立 Windows Presentation Foundation (WPF) 應用程式，使用 Microsoft 身分識別平台進行驗證 | Azure
titleSuffix: Microsoft identity platform
description: 在本教學課程中，您會建置 WPF 應用程式，使用 Microsoft 身分識別平台來登入使用者，並取得存取權杖來代表他們呼叫 Microsoft Graph API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7effb1592fb19f92958353a3333edf6fdf9a51af
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574257"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>從 Windows 傳統型應用程式呼叫 Microsoft 圖形 API

本指南示範原生 Windows Desktop .NET (XAML) 應用程式如何使用存取權杖以呼叫 Microsoft Graph API。 此應用程式也可以存取其他 API，這些 API 需要 Microsoft 身分識別平台提供的存取權杖。

當您完成本指南之後，您的應用程式就可以呼叫受保護的 API (使用個人帳戶，包括 outlook.com、live.com 等等)。 應用程式也可以使用任何公司或組織 (使用 Azure Active Directory) 的公司和學校帳戶。

本教學課程內容：

> [!div class="checklist"]
> * 在 Visual Studio 中建立 *Windows Presentation Foundation (WPF)* 專案
> * 安裝適用於 .NET 的 Microsoft Authentication Library (MSAL)
> * 在 Azure 入口網站中註冊應用程式
> * 新增程式碼以支援使用者登入和登出
> * 新增程式碼以呼叫 Microsoft Graph API
> * 測試應用程式

## <a name="prerequisites"></a>必要條件

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式

![示範本教學課程所產生的應用程式範例如何運作](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

本指南建立的應用程式範例，可讓 Windows 傳統型應用程式查詢 Microsoft Graph API，或查詢從 Microsoft 身分識別平台端點接受權杖的 Web API。 針對這個案例，您會透過授權標頭將一個權杖新增到 HTTP 要求。 Microsoft Authentication Library (MSAL) 會處理權杖取得和更新作業。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>處理權杖取得以存取受保護的 Web API

使用者經過驗證之後，應用程式範例就會收到一個權杖，其可用來查詢 Microsoft Graph API 或由開發人員所用 Microsoft 身分識別平台保護的 Web API。

例如，Microsoft Graph 需要可允許存取特定資源的權杖。 例如，需要權杖才能讀取使用者的設定檔、存取使用者的行事曆，或傳送電子郵件。 您的應用程式可以使用 MSAL 要求存取權杖，以透過指定 API 範圍來存取這些資源。 此存取權杖接著會新增到 HTTP 授權標頭，這是針對受保護資源進行之每個呼叫的授權標頭。

MSAL 會為您管理快取和重新整理存取權杖，因此您的應用程式不需要執行這些作業。

## <a name="nuget-packages"></a>NuGet 套件

本指南會使用以下 NuGet 套件：

|程式庫|描述|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>設定專案

您會在本節中建立新的專案來示範如何整合 Windows Desktop .NET 應用程式 (XAML) 與使用 Microsoft 登入，以便應用程式可以查詢需要權杖的 Web API。

您使用本指南所建立的應用程式會顯示用來呼叫圖表的按鈕、在畫面上顯示結果的區域，以及登出按鈕。

> [!NOTE]
> 想要改為下載此範例的 Visual Studio 專案嗎？ [下載專案](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)並跳至[設定步驟](#register-your-application)，以在執行之前先設定程式碼範例。
>

若要建立應用程式，請執行下列動作：

1. 在 Visual Studio 中，選取 [檔案] >  [新增] >  [專案]。
2. 在 [範本] 底下，選取 [Visual C#]。
3. 選取 [WPF 應用程式 (.NET Framework)]，視您所使用的 Visual Studio 版本而定。

## <a name="add-msal-to-your-project"></a>將 MSAL 新增至您的專案

1. 在 Visual Studio 中，選取 [工具] >  [NuGet 套件管理員]>  [套件管理員主控台]。
2. 在 [套件管理器主控台] 視窗中，貼上下列 Azure PowerShell 命令：

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > 此命令會安裝 Microsoft 驗證程式庫。 MSAL 會處理使用者權杖的取得、快取及重新整理作業，而這些權杖用來存取受 Azure Active Directory v2.0 保護的 API。
    >

## <a name="register-your-application"></a>註冊您的應用程式

您可以使用兩種方式之一來註冊您的應用程式。

### <a name="option-1-express-mode"></a>選項 1：快速模式

執行下列動作，即可快速註冊您的應用程式：
1. 移至 [Azure 入口網站 - 應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)。
1. 輸入應用程式的名稱，並選取 [註冊]。
1. 依照指示按一下滑鼠，即可下載並自動設定新的應用程式。

### <a name="option-2-advanced-mode"></a>選項 2：進階模式

若要註冊您的應用程式並將應用程式註冊資訊新增到您的解決方案，請執行下列作業：
1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 瀏覽至 Microsoft 身分識別平台，以取得開發人員的[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面。
1. 選取 [新增註冊]。
   - 在 [名稱] 區段中，輸入將對應用程式使用者顯示、且有意義的應用程式名稱，例如 `Win-App-calling-MsGraph`。
   - 在 [支援的帳戶類型] 區段中，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]，例如 Skype、Xbox、Outlook.com。
   - 選取 [註冊] 以建立應用程式。
1. 在應用程式頁面清單中，選取 [驗證]。
   1. 在「重新導向 URI」清單的 [重新導向 URI] 區段中：
   1. 在 [型別] 欄中，選取 [公開用戶端/原生 (行動和傳統型)]。
   1. 在 [重新導向 URI] 資料行中輸入 `https://login.microsoftonline.com/common/oauth2/nativeclient`。
1. 選取 [註冊]。
1. 移至 Visual Studio，開啟 App.xaml.cs 檔案，然後將下列程式碼片段中的 `Enter_the_Application_Id_here` 取代為您剛剛註冊及複製的應用程式 ID。

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>新增程式碼以初始化 MSAL

在這個步驟中，您建立類別來處理和 MSAL 的互動，例如處理權杖。

1. 開啟 App.xaml.cs 檔案，然後將 MSAL 的參考新增至類別：

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. 將應用程式類別更新至下面這樣：

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>建立應用程式 UI

本節會說明應用程式如何查詢受保護的後端伺服器 (例如 Microsoft Graph)。

系統應會自動建立 MainWindow.xaml 檔案，作為專案範本的一部分。 開啟此檔案，然後將應用程式的 *\<Grid>* 節點取代為下列程式碼：

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 MSAL 取得 Microsoft Graph API 的權杖

在本節中，使用 MSAL 取得 Microsoft Graph API 的權杖。

1. 在 MainWindow.xaml.cs 檔案中，將 MSAL 的參考新增至類別：

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. 使用下列程式碼來取代 `MainWindow` 類別程式碼：

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

### <a name="more-information"></a>詳細資訊

#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖

呼叫 `AcquireTokenInteractive` 方法時會顯示一個視窗，提示使用者登入。 當使用者第一次需要存取受保護的資源時，應用程式通常會要求使用者以互動方式登入。 當取得權杖的無訊息作業失敗 (例如，使用者的密碼過期) 時，使用者也可能需要登入。

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖

`AcquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 在 `AcquireTokenInteractive` 第一次執行之後，`AcquireTokenSilent` 就會成為用來取得權杖的常用方法，以在後續呼叫中存取受保護的資源，因為系統會以無訊息方式進行呼叫來要求或更新權杖。

最後，`AcquireTokenSilent` 方法會失敗。 失敗的原因可能是使用者已經登出，或已經在其他裝置上變更其密碼。 當 MSAL 偵測到可透過要求執行互動式動作來解決問題時，就會發出一個 `MsalUiRequiredException` 例外狀況。 您的應用程式可以透過兩種方式處理此例外狀況：

* 它可以立即對 `AcquireTokenInteractive` 進行呼叫。 此呼叫會促使系統提示使用者登入。 此模式通常用於沒有離線內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例會遵循此模式，您可以在第一次執行範例時看到它運作。

* 因為沒有任何使用者用過該應用程式，所以 `PublicClientApp.Users.FirstOrDefault()` 會包含一個 null 值，而且會擲回 `MsalUiRequiredException` 例外狀況。

* 然後範例中的程式碼會透過呼叫 `AcquireTokenInteractive` 來處理例外狀況，進而提示使用者登入。

* 其可改為對使用者呈現視覺指示，讓使用者知道需要透過互動方式登入，以便選取正確的登入時機。 或者，應用程式可以稍後重試 `AcquireTokenSilent`。 此方式通常用於使用者可以使用其他應用程式功能，不需要因此中斷作業的情況，例如，應用程式中有離線內容可供使用時。 在此情況下，使用者可以決定何時登入以存取受保護的資源，或重新整理過期的資訊。 此外，當網路暫時無法使用而後還原時，應用程式可以決定是否重試 `AcquireTokenSilent`。

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>使用您剛剛取得的權杖呼叫 Microsoft Graph API

將下列新方法新增至 `MainWindow.xaml.cs`。 此方法是用來使用授權標頭針對圖形 API 提出 `GET` 要求：

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的相關詳細資訊

在這個範例應用程式中，使用 `GetHttpContentWithToken` 方法來針對受保護資源提出 HTTP `GET` 要求，那些受保護資源需要權杖才能存取，然後再將內容傳回給使用者。 此方法會在「HTTP 授權標頭」中新增取得的權杖。 對於此範例，資源為 Microsoft Graph API *me* 端點，它會顯示使用者的設定檔資訊。

## <a name="add-a-method-to-sign-out-a-user"></a>新增方法來將使用者登出

若要登出使用者，將下列方法新增到您的 `MainWindow.xaml.cs` 檔案：

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>使用者登出的詳細資訊

`SignOutButton_Click` 方法會從 MSAL 使用者快取中移除使用者，這可有效告知 MSAL 忘記目前的使用者，只有將此作業設為互動式作業，未來取得權杖的要求才能成功。

雖然此範例中的應用程式支援單一使用者，MSAL 也支援可同時登入多個帳戶的案例。 例如，使用者擁有多個帳戶的電子郵件應用程式。

## <a name="display-basic-token-information"></a>顯示基本權杖資訊

若要顯示和權杖有關的基本資訊，將下列方法新增到您的 MainWindow.xaml.cs 檔案：

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>詳細資訊

除了用來呼叫 Microsoft Graph API 的存取權杖之外，使用者登入之後，MSAL 也會取得 ID 權杖。 此權杖包含與使用者相關的一小部分資訊。 `DisplayBasicTokenInfo` 方法會顯示權杖中包含的基本資訊。 例如，它會顯示使用者的顯示名稱和 ID，以及權杖到期日期和代表存取權杖本身的字串。 您可以選取 [呼叫 Microsoft Graph API] 按鈕多次，以了解相同的權杖如何重複用於多個後續要求。 您也可以在 MSAL 決定應該要更新權杖時，看到延長的到期日期。

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>後續步驟

深入了解如何建置傳統型應用程式，以在我們的多部分案例系列中呼叫受保護的 Web API：

> [!div class="nextstepaction"]
> [案例：呼叫 Web API 的傳統型應用程式](scenario-desktop-overview.md)