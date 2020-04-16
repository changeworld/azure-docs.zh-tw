---
title: Microsoft 身分識別平台 UWP 使用者入門 | Azure
description: 通用 Windows 平台應用程式 (UWP) 可以呼叫需要由 Microsoft 身分識別平台端點存取權杖的 API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 205f4a27a7903bc5a1da3fc12e3d4a02b23f58cf
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990917"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>自通用 Windows 平台應用程式 (XAML) 呼叫 Microsoft Graph API

> [!div renderon="docs"]

本指南說明原生通用 Windows 平台 (UWP) 應用程式如何要求存取權杖。 應用程式會接著呼叫 Microsoft Graph API。 此指南也適用於需要來自 Microsoft 身分識別平台端點之存取權杖的其他 API。

在本指南結尾，您的應用程式會使用個人帳戶呼叫受保護的 API。 例如，outlook.com、live.com 等。 您的應用程式也會從具有 Azure Active Directory (Azure AD) 的任何公司或組織呼叫工作和學校帳戶。

>[!NOTE]
> 本指南需安裝具備通用 Windows 平台開發功能的 Visual Studio。 如需下載及設定 Visual Studio 以開發通用 Windows 平台應用程式的說明，請參閱[開始設定](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)。

>[!NOTE]
> 如果您不熟悉 Microsoft 身分識別平台，建議您從[從通用 Windows 平台 (UWP) 應用程式快速入門呼叫 Microsoft Graph API](quickstart-v2-uwp.md) 開始了解。

## <a name="how-this-guide-works"></a>本指南使用方式

![示範本教學課程所產生的應用程式範例如何運作](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

本指南會建立可查詢 Microsoft Graph API 的範例 UWP 應用程式。 針對這個案例，系統會透過使用 Authorization 標頭將一個權杖新增到 HTTP 要求。 Microsoft 驗證程式庫 (MSAL) 會處理權杖取得和更新作業。

## <a name="nuget-packages"></a>NuGet 套件

本指南會使用以下 NuGet 套件：

|程式庫|描述|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft 驗證程式庫|

## <a name="set-up-your-project"></a>設定專案

本節提供整合 Windows 傳統型 .NET 應用程式 (XAML) 與「使用 Microsoft 登入」的逐步指示。 然後，應用程式可以查詢需要權杖的 Web API，例如 Microsoft Graph API。

本指南所建立的應用程式會顯示一個可查詢圖形 API 的按鈕，以及一個用來登出的按鈕。它也會顯示包含呼叫結果的文字方塊。

> [!NOTE]
> 您想要下載此範例的 Visual Studio 專案而非加以建立嗎？ [下載專案](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)並跳到[應用程式註冊](#register-your-application "應用程式註冊步驟")步驟，以在執行之前先設定程式碼範例。

### <a name="create-your-application"></a>建立您的應用程式

1. 開啟 Visual Studio，然後選取 [建立新專案]  。
1. 在 [建立新專案]  中，針對 C# 選擇 [空白應用程式 (通用 Windows)]  ，然後選取 [下一步]  。
1. 在 [設定新的專案]  中，為應用程式命名，然後選取 [建立]  。
1. 如果出現提示，請在 [新增通用 Windows 平台專案]  中，為 [目標]  和 [最低]  版本選取任一版本，然後選取 [確定]  。

   ![最低和目標版本](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>將 Microsoft 驗證程式庫新增至您的專案

1. 在 Visual Studio 中，選取 [工具]   >  [NuGet 套件管理員]   >  [套件管理員主控台]  。
1. 在 [套件管理器主控台]  視窗中，複製並貼上下列命令：

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > 此命令會安裝 [Microsoft 驗證程式庫](https://aka.ms/msal-net)。 MSAL 會取得、快取並重新整理使用者權杖，以存取受 Microsoft 身分識別平台保護的 API。

### <a name="create-your-applications-ui"></a>建立您應用程式的 UI

Visual Studio 會在專案範本中建立 *MainPage.xaml*。 開啟此檔案，然後將應用程式的 **Grid** 節點取代為下列程式碼：

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>使用 MSAL 取得 Microsoft Graph API 的權杖

本節說明如何使用 MSAL 取得 Microsoft Graph API 的權杖。 變更 *MainPage.xaml.cs* 檔案。

1. 在 *MainPage.xaml.cs* 中新增下列參考：

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. 以下列程式碼取代 `MainPage` 類別：

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖<a name="more-information"></a>

`AcquireTokenInteractive` 方法會顯示一個視窗，提示使用者登入。 當使用者第一次需要存取受保護的資源時，應用程式通常會要求使用者以互動方式登入。 當取得權杖的無訊息作業失敗時，使用者也可能需要登入。 例如，使用者的密碼過期時。

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖

`AcquireTokenSilent` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 第一次執行 `AcquireTokenInteractive`，並提示使用者提供認證時，請使用 `AcquireTokenSilent` 方法來要求權杖，以供稍後呼叫。 該方法會以無訊息方式取得權杖。 MSAL 會處理權杖快取和更新作業。

最後，`AcquireTokenSilent` 方法會失敗。 失敗的原因包括使用者已登出，或已在其他裝置上變更其密碼。 當 MSAL 偵測到需要互動式動作的問題時，就會擲回 `MsalUiRequiredException` 例外狀況。 您的應用程式可以透過兩種方式處理此例外狀況：

* 您的應用程式會立即呼叫 `AcquireTokenInteractive`。 此呼叫會促使系統提示使用者登入。 一般而言，將此方法用於沒有離線內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例會遵循該模式。 您可以在第一次執行範例時查看其運作方式。

   因為沒有任何使用者用過該應用程式，所以 `accounts.FirstOrDefault()` 會包含一個 null 值，並擲回 `MsalUiRequiredException` 例外狀況。

   然後範例中的程式碼會透過呼叫 `AcquireTokenInteractive` 來處理例外狀況。 此呼叫會促使系統提示使用者登入。

* 您的應用程式會向需要登入的使用者呈現視覺指示。 然後，使用者可以選取正確的登入時機。 應用程式可以稍後重試 `AcquireTokenSilent`。 當使用者可以在不需要中斷的情況下使用其他應用程式功能時，請使用此方法。 例如，應用程式中有離線內容可供使用時。 在此情況下，使用者可以決定何時要登入。 在網路暫時無法使用後，應用程式可以重試 `AcquireTokenSilent`。

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>使用您剛剛取得的權杖呼叫 Microsoft Graph API

將下列新方法新增至 *MainPage.xaml.cs*：

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
           // Add the token in Authorization header
           request.Headers.Authorization = 
             new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

 這個方法會使用 `Authorization` 標頭，從圖形 API 提出 `GET` 要求。

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的詳細資訊

在此範例應用程式中，`GetHttpContentWithToken` 方法會對需要權杖的受保護資源提出 HTTP `GET` 要求。 然後，該方法會將內容傳回給呼叫端。 此方法會在 **HTTP 授權**標頭中新增取得的權杖。 對於此範例，資源為 Microsoft Graph API **me** 端點，它會顯示使用者的設定檔資訊。

### <a name="add-a-method-to-sign-out-the-user"></a>新增方法來將使用者登出

將下列方法新增至 *MainPage.xaml.cs* 來將使用者登出：

   ```csharp
   /// <summary>
   /// Sign out the current user
   /// </summary>
   private async void SignOutButton_Click(object sender, RoutedEventArgs e)
   {
       IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
       IAccount firstAccount = accounts.FirstOrDefault();

       try
       {
           await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
               ResultText.Text = "User has signed out";
               this.CallGraphButton.Visibility = Visibility.Visible;
                   this.SignOutButton.Visibility = Visibility.Collapsed;
               });
           }
           catch (MsalException ex)
           {
               ResultText.Text = $"Error signing out user: {ex.Message}";
           }
       }
   ```

> [!NOTE]
> MSAL.NET 會使用非同步方法來取得權杖或管理帳戶。 您必須在 UI 執行緒中支援 UI 動作。 這就是 `Dispatcher.RunAsync` 呼叫的原因，以及呼叫 `ConfigureAwait(false)` 的預防措施。

#### <a name="more-information-about-signing-out"></a>有關登出的詳細資訊<a name="more-information-on-sign-out"></a>

`SignOutButton_Click` 方法會從 MSAL 使用者快取中移除使用者。 此方法會有效告知 MSAL 忘記目前的使用者。 只有互動方式，未來取得權杖的要求才能成功。

此範例中的應用程式支援單一使用者。 MSAL 支援使用者可在多個帳戶上登入的案例。 例如，使用者擁有數個帳戶的電子郵件應用程式。

### <a name="display-basic-token-information"></a>顯示基本權杖資訊

將下列方法新增至 *MainPage.xaml.cs*，以顯示和權杖相關的基本資訊：

   ```csharp
   /// <summary>
   /// Display basic information contained in the token. Needs to be called from the UI thread.
   /// </summary>
   private void DisplayBasicTokenInfo(AuthenticationResult authResult)
   {
       TokenInfoText.Text = "";
       if (authResult != null)
       {
           TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
           TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
       }
   }
   ```

#### <a name="more-information"></a>詳細資訊<a name="more-information-1"></a>

使用 **OpenID Connect** 取得的識別碼權杖也包含一小部分與使用者有關的資訊。 `DisplayBasicTokenInfo` 會顯示權杖中包含的基本資訊。 此資訊包括使用者的顯示名稱和識別碼。 還包括權杖的到期日，以及代表存取權杖本身的字串。 如果您選取 [呼叫 Microsoft Graph API]  按鈕多次，您就會了解相同的權杖如何重複用於後續要求。 您也可以在 MSAL 決定應該要更新權杖時，看到延長的到期日。

### <a name="display-message"></a>顯示訊息

將下列新方法新增至 *MainPage.xaml.cs*：

   ```csharp
   /// <summary>
   /// Displays a message in the ResultText. Can be called from any thread.
   /// </summary>
   private async Task DisplayMessageAsync(string message)
   {
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
            () =>
            {
                ResultText.Text = message;
            });
        }
   ```

## <a name="register-your-application"></a>註冊您的應用程式

您現在需要註冊您的應用程式：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [Azure Active Directory]   > [應用程式註冊]  。
1. 選取 [新增註冊]  。 輸入將對應用程式使用者顯示且有意義的應用程式名稱，例如 *UWP-App-calling-MSGraph*。
1. 在 [支援的帳戶類型]  之下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶 (例如 Skype、Xbox)]  ，然後選取 [註冊]  繼續執行。
1. 在概觀頁面上，尋找 [應用程式 (用戶端) 識別碼]  值並加以複製。 返回 Visual Studio，開啟 *MainPage.xaml.cs*，然後使用此值取代 `ClientId` 的值。

設定應用程式的驗證：

1. 回到 [Azure 入口網站](https://portal.azure.com)，在 [管理]  之下選取 [驗證]  。
1. 在 [重新導向 URI]   | [公用用戶端 (行動、傳統型) 的建議重新導向 URI]  區段，然後核取 **https://login.microsoftonline.com/common/oauth2/nativeclient** 。
1. 選取 [儲存]  。

設定應用程式的 API 權限：

1. 在 [管理]  之下選取 [API 權限]  。
1. 選取 [新增權限]  ，然後確定您已選取 [Microsoft API]  。
1. 選取 [Microsoft Graph]  。
1. 選取 [委派權限]  ，搜尋 *User.Read* 並確認已選取 **User.Read**。
1. 如果您進行了任何變更，請選取 [新增權限]  來儲存變更。

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>啟用同盟網域上的整合式驗證 (選擇性)

搭配 Azure AD 同盟網域使用時，若要啟用 Windows 整合式驗證，應用程式資訊清單必須啟用其他功能。 在 Visual Studio 中回到您的應用程式。

1. 開啟 *Package.appxmanifest*。
1. 選取 [功能]  並啟用下列設定：

   * **企業驗證**
   * **私人網路 (用戶端和伺服器)**
   * **共用使用者憑證**

> [!IMPORTANT]
> 在此範例中，預設不會設定[整合式 Windows 驗證](https://aka.ms/msal-net-iwa)。 要求 `Enterprise Authentication` 或 `Shared User Certificates` 功能的應用程式需要更高的 Windows 市集驗證層級。 此外，並非所有開發人員都需要執行更高的驗證層級。 只有當您需要具有 Azure AD 同盟網域的 Windows 整合式驗證時，才啟用此設定。

## <a name="test-your-code"></a>測試您的程式碼

若要測試您的應用程式，請選取 F5 以在 Visual Studio 中執行專案。 您的主視窗隨即出現：

![應用程式的使用者介面](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

當您準備好進行測試時，請選取**呼叫 Microsoft Graph API**。 然後，使用 Azure AD 組織帳戶或 Microsoft 帳戶 (例如 live.com 或 outlook.com) 登入。 使用者第一次執行時，應用程式會顯示一個視窗，要求使用者登入。

### <a name="consent"></a>同意

第一次登入應用程式時，系統會顯示如下所示的類似同意畫面。 選取 [是]  以明確同意存取：

![存取同意畫面](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>預期的結果

您會在 [API 呼叫結果]  畫面上看到由 Microsoft Graph API 呼叫傳回的使用者設定檔資訊：

![API 呼叫結果畫面](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

您也會在 [權杖資訊]  方塊中，看到透過 `AcquireTokenInteractive` 或 `AcquireTokenSilent` 取得之權杖的相關基本資訊：

|屬性  |[格式]  |描述 |
|---------|---------|---------|
|`Username` |`user@domain.com` |識別使用者的使用者名稱。|
|`Token Expires` |`DateTime` |權杖的到期時間。 MSAL 會視需要更新權杖來延展到期日。|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>與範圍和委派的權限有關的詳細資訊

Microsoft Graph API 需要 `user.read` 範圍以讀取使用者的設定檔。 根據預設，在應用程式註冊入口網站註冊的每個應用程式中都會新增此範圍。 Microsoft Graph 的其他 API 與您後端伺服器的自訂 API 一樣，需要其他範圍。 例如，Microsoft Graph API 需要 `Calendars.Read` 範圍才能列出使用者的行事曆。

為了在應用程式內容中存取使用者的行事曆，請將 `Calendars.Read` 委派權限新增至應用程式註冊資訊。 接著，將 `Calendars.Read` 範圍新增至 `acquireTokenSilent` 呼叫。

> [!NOTE]
> 系統可能會在您增加範圍數目時，提示使用者同意其他事項。

## <a name="known-issues"></a>已知問題

### <a name="issue-1"></a>問題 1

當您在 Azure AD 同盟網域中登入應用程式時，您會收到下列錯誤訊息：

* 在要求中找不到有效的用戶端憑證。
* 在使用者的憑證存放區中找不到有效的憑證。
* 請選擇不同的驗證方法後再試一次。

原因：企業和憑證功能未啟用。

解決方案：依照[啟用同盟網域上的整合式驗證 (選擇性)](#enable-integrated-authentication-on-federated-domains-optional)中的步驟操作。

### <a name="issue-2"></a>問題 2

您啟用[同盟網域上的整合式驗證](#enable-integrated-authentication-on-federated-domains-optional)，並嘗試在 Windows 10 電腦上使用 Windows Hello 來登入設定多重要素驗證的環境。 憑證清單隨即顯示。 不過，如果您選擇使用 PIN，絕不會顯示 [PIN] 視窗。

原因：此問題是在 Windows 10 桌上型電腦上執行的 UWP 應用程式中之 Web 驗證訊息代理程式的已知限制。 它在 Windows 10 行動裝置版上會正常運作。

因應措施：選取 [使用其他選項登入]  。 然後選取 [以您的使用者名稱和密碼登入]  。 選取 [提供您的密碼]  。 然後完成電話驗證程序。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
