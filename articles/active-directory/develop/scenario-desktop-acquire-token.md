---
title: 取得權杖以呼叫 Web API （桌面應用程式） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立桌面應用程式，以呼叫 web Api 來取得應用程式的權杖
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 636c7c654b98ced5f93c3ace0e4a99bfc9bf7def
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77611811"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>呼叫 web Api 的桌面應用程式：取得權杖

建立公用用戶端應用程式的實例之後，您將使用它來取得權杖，然後用來呼叫 Web API。

## <a name="recommended-pattern"></a>建議模式

Web API 是由其 `scopes`所定義。 無論您在應用程式中提供的體驗為何，使用的模式如下：

- 藉由呼叫 `AcquireTokenSilent`，有系統地嘗試從權杖快取取得權杖。
- 如果這個呼叫失敗，請使用您想要使用的 `AcquireToken` 流程，這是由 `AcquireTokenXX`在這裡表示。

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>在 MSAL.NET 中

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL

Objective-C：

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift：

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```
---

以下是在桌面應用程式中取得權杖的各種方式。

## <a name="acquire-a-token-interactively"></a>以互動方式取得權杖

下列範例顯示使用 Microsoft Graph 以互動方式取得權杖的最少程式碼，以讀取使用者的設定檔。

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>在 MSAL.NET 中

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>必要參數

`AcquireTokenInteractive` 只有一個強制參數，``scopes``，其中包含定義需要權杖之範圍的字串列舉。 如果權杖是用於 Microsoft Graph，則在名為「許可權」的區段中，您可以在每個 Microsoft Graph API 的 API 參考中找到所需的範圍。 例如，若要[列出使用者的連絡人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)，必須使用範圍「使用者. 讀取」、「連絡人. 讀取」。 如需詳細資訊，請參閱[Microsoft Graph 許可權參考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上，您也必須使用 `.WithParentActivityOrWindow`來指定父活動，如此一來，權杖就會在互動之後回到該父活動。 如果您未指定，則在呼叫 `.ExecuteAsync()`時，就會擲回例外狀況（exception）。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定選擇性參數

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI 很重要，因為它是互動式的。 `AcquireTokenInteractive` 有一個特定的選擇性參數，可以針對支援的平臺、父 UI 來指定。 在桌面應用程式中使用時，`.WithParentActivityOrWindow` 具有不同的類型，這取決於平臺。

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

備註：

- 在 .NET Standard 上，預期的 `object` 會在 Android 上 `Activity`、在 iOS 上 `UIViewController`、在 MAC 上 `NSWindow`，以及在 Windows 上 `IWin32Window` 或 `IntPr`。
- 在 Windows 上，您必須從 UI 執行緒呼叫 `AcquireTokenInteractive`，讓內嵌瀏覽器取得適當的 UI 同步處理內容。 不是從 UI 執行緒呼叫，可能會導致訊息無法使用 UI 來提取正確和鎖死的情況。 如果您不是在 UI 執行緒上，從 UI 執行緒呼叫 Microsoft 驗證程式庫（MSALs）的其中一種方式，就是在 WPF 上使用 `Dispatcher`。
- 如果您使用的是 WPF，若要從 WPF 控制項取得視窗，您可以使用 `WindowInteropHelper.Handle` 類別。 然後呼叫是來自 WPF 控制項（`this`）：

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 是用來透過指定提示來控制使用者的互動性。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

類別會定義下列常數：

- ``SelectAccount`` 強制 STS 呈現 [帳戶選取] 對話方塊，其中包含使用者具有會話的帳戶。 當應用程式開發人員想要讓使用者在不同的身分識別之間進行選擇時，這個選項很有用。 此選項會驅動 MSAL，以將 ``prompt=select_account`` 傳送至身分識別提供者。 這個選項是預設值。 它會根據可用的資訊（例如使用者的會話帳戶和出現狀態），提供最佳的可能體驗。 除非您有充分的理由，否則請不要變更它。
- ``Consent`` 可讓應用程式開發人員強制提示使用者同意，即使之前已授與同意亦然。 在此情況下，MSAL 會將 `prompt=consent` 傳送給識別提供者。 此選項可用於某些以安全性為主的應用程式，其中組織治理會要求每次使用應用程式時，都會向使用者顯示同意對話方塊。
- ``ForceLogin`` 可讓應用程式開發人員將服務提示使用者提供認證，即使可能不需要此使用者提示也一樣。 如果取得權杖失敗，此選項可讓使用者再次登入。 在此情況下，MSAL 會將 `prompt=login` 傳送給識別提供者。 有時候，它會用於以安全性為主的應用程式，組織治理會要求使用者在每次存取應用程式的特定部分時重新登入。
- ``Never`` （僅適用于 .NET 4.5 和 WinRT）不會提示使用者，而是會嘗試使用儲存在隱藏的內嵌 web 視圖中的 cookie。 如需詳細資訊，請參閱 MSAL.NET 中的 web views。 使用此選項可能會失敗。 在此情況下，`AcquireTokenInteractive` 會擲回例外狀況，以通知需要 UI 互動。 您必須使用另一個 `Prompt` 參數。
- ``NoPrompt`` 不會傳送任何提示給識別提供者。 此選項僅適用于 Azure Active Directory （Azure AD） B2C 編輯設定檔原則。 如需詳細資訊，請參閱[Azure AD B2C 細節](https://aka.ms/msal-net-b2c-specificities)。

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

這個修飾詞用於您想要讓使用者預先同意數個資源的先進案例，而且您不想要使用累加式同意，這通常與 MSAL.NET/the Microsoft 身分識別平臺搭配使用。 如需詳細資訊，請參閱將[使用者同意預先用於數個資源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web UI 是用來叫用瀏覽器的機制。 這種機制可以是專用的 UI WebBrowser 控制項或委派開啟瀏覽器的方式。
MSAL 為大部分的平臺提供 web UI，但在某些情況下，您可能會想要自行裝載瀏覽器：

- MSAL 未明確涵蓋的平臺，例如 Blazor、Unity 和在桌上型電腦上的 Mono。
- 您想要對應用程式進行 UI 測試，並使用可與 Selenium 搭配使用的自動化瀏覽器。
- 執行 MSAL 的瀏覽器和應用程式位於不同的進程中。

##### <a name="at-a-glance"></a>速覽

為了達到此目的，您會提供 MSAL `start Url`，這需要在選擇的瀏覽器中顯示，讓使用者可以輸入如使用者名稱之類的專案。
驗證完成後，您的應用程式必須傳回 MSAL `end Url`，其中包含 Azure AD 所提供的程式碼。
`end Url` 的主機一律 `redirectUri`。 若要攔截 `end Url`，請執行下列其中一項動作：

- 監視瀏覽器重新導向，直到達到 `redirect Url` 為止。
- 讓瀏覽器重新導向至您所監視的 URL。

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi 是擴充點

`WithCustomWebUi` 是擴充點，可用來在公用用戶端應用程式中提供您自己的 UI。 您也可以讓使用者通過身分識別提供者的/Authorize 端點，並讓他們登入和同意。 MSAL.NET 接著可以兌換驗證碼並取得權杖。 例如，它會在 Visual Studio 中用來讓 electrons 應用程式（例如，Visual Studio 意見反應）提供 web 互動，但讓它 MSAL.NET 來執行大部分的工作。 如果您想要提供使用者介面自動化，也可以使用它。 在公用用戶端應用程式中，MSAL.NET 會使用程式碼交換的證明金鑰（PKCE）標準，以確保遵守安全性。 只有 MSAL.NET 可以兌換程式碼。 如需詳細資訊，請參閱[RFC 7636-OAuth 公用用戶端的程式碼交換的證明金鑰](https://tools.ietf.org/html/rfc7636)。

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>使用 WithCustomWebUi

若要使用 `.WithCustomWebUI`，請遵循下列步驟。

  1. 實作 `ICustomWebUi` 介面。 如需詳細資訊，請參閱[此網站](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)。 執行一個 `AcquireAuthorizationCodeAsync`方法，並接受 MSAL.NET 所計算的授權碼 URL。 然後讓使用者完成與身分識別提供者的互動，並傳回識別提供者與授權碼一起呼叫您的執行的 URL。 如果您有任何問題，則您的執行應該會擲回 `MsalExtensionException` 例外狀況，以便與 MSAL 完美合作。
  2. 在您的 `AcquireTokenInteractive` 呼叫中，使用 `.WithCustomUI()` 修飾詞，傳遞自訂 web UI 的實例。

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>測試自動化中的 ICustomWebUi 執行範例： SeleniumWebUI

MSAL.NET 小組已重寫 UI 測試，以使用此擴充性機制。 如果您有興趣，請查看 MSAL.NET 原始程式碼中的[SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160)類別。

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>提供 SystemWebViewOptions 的絕佳體驗

從 MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)中，您可以指定：

- 要在系統網頁瀏覽器中登入或同意錯誤時，所要顯示的 URI （`BrowserRedirectError`）或 HTML 片段（`HtmlMessageError`）。
- 要在成功登入或同意的情況下，移至的 URI （`BrowserRedirectSuccess`）或要顯示的 HTML 片段（`HtmlMessageSuccess`）。
- 要執行以啟動系統瀏覽器的動作。 您可以藉由設定 `OpenBrowserAsync` 委派來提供自己的執行方式。 類別也提供兩個瀏覽器的預設執行：分別是 Chromium 的 Microsoft Edge 和[Microsoft edge](https://www.windowscentral.com/faq-edge-chromium)的 `OpenWithEdgeBrowserAsync` 和 `OpenWithChromeEdgeBrowserAsync`。

若要使用這個結構，請撰寫如下列範例所示的內容：

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>其他選擇性參數

若要深入瞭解 `AcquireTokenInteractive`的所有其他選用參數，請參閱[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 不會直接提供互動式取得權杖方法。 相反地，它會要求應用程式在其使用者互動流程的執行中傳送授權要求，以取得授權碼。 然後，可以將此程式碼傳遞給 `acquire_token_by_authorization_code` 方法，以取得權杖。

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL

Objective-C：

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift：

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>整合式 Windows 驗證

若要在網域或 Azure AD 加入的電腦上登入網域使用者，請使用整合式 Windows 驗證（IWA）。

### <a name="constraints"></a>條件約束

- 整合式 Windows 驗證僅適用于同盟 *+* 使用者，也就是在 Active Directory 建立的使用者，並由 Azure AD 支援。 直接在 Azure AD 中建立的使用者若沒有 Active Directory 支援（稱為「*受管理*使用者」），就無法使用此驗證流程。 這項限制並不會影響使用者名稱和密碼流程。
- IWA 適用于針對 .NET Framework、.NET Core 和通用 Windows 平臺（UWP）平臺所撰寫的應用程式。
- IWA 不會略過多重要素驗證（MFA）。 如果已設定 MFA，則在需要 MFA 挑戰時，IWA 可能會失敗，因為 MFA 需要使用者互動。
  > [!NOTE]
  > 這一點很棘手。 IWA 非互動式，但 MFA 需要使用者互動。 您不會控制身分識別提供者要求執行 MFA 的時間，租使用者系統管理員會這麼做。 從我們的觀察，當您從不同的國家/地區、未透過 VPN 連線到公司網路，甚至是透過 VPN 連線時，都需要 MFA。 不預期有一組具決定性的規則。 Azure AD 使用 AI 來持續瞭解是否需要 MFA。 當 IWA 失敗時，切換回使用者提示，例如互動式驗證或裝置程式碼流程。

- 傳入 `PublicClientApplicationBuilder` 的授權單位必須是：
  - `https://login.microsoftonline.com/{tenant}/`表單的租使用者，其中 `tenant` 是代表租使用者識別碼的 GUID，或與租使用者相關聯的網域。
  - 針對任何公司和學校帳戶： `https://login.microsoftonline.com/organizations/`。
  - 不支援 Microsoft 個人帳戶。 您不能使用/common 或/consumers 租使用者。

- 因為整合式 Windows 驗證是無訊息流程：
  - 您應用程式的使用者必須先同意使用應用程式。
  - 或者，租使用者系統管理員必須先前已同意租使用者中的所有使用者，才能使用該應用程式。
  - 換句話說：
    - 身為開發人員，您可以自行選取 Azure 入口網站中的 [**授**與] 按鈕。
    - 或者，租使用者系統管理員已在應用程式註冊的 [ **API 許可權**] 索引標籤上，選取 [**授與/撤銷系統管理員同意**] 按鈕。 如需詳細資訊，請參閱[新增存取 Web api 的許可權](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)。
    - 或者，您已經為使用者提供同意應用程式的方式。 如需詳細資訊，請參閱[要求個別使用者同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)。
    - 或者，您也提供了一種方法，讓租使用者系統管理員同意應用程式。 如需詳細資訊，請參閱系統[管理員同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)。

- 此流程已針對 .NET desktop、.NET Core 和 UWP 應用程式啟用。

如需有關同意的詳細資訊，請參閱[Microsoft 身分識別平臺許可權和同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)。

### <a name="learn-how-to-use-it"></a>了解其使用方式

# <a name="net"></a>[.NET](#tab/dotnet)

在 MSAL.NET 中，您必須使用：

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

您通常只需要一個參數（`scopes`）。 根據您的 Windows 系統管理員設定原則的方式而定，您的 Windows 電腦上的應用程式可能不允許查詢已登入的使用者。 在此情況下，請使用第二個方法，`.WithUsername()`，並以 UPN 格式傳入登入使用者的使用者名稱，例如 `joe@contoso.com`。 在 .NET Core 上，只有取得使用者名稱的多載可供使用，因為 .NET Core 平臺無法向 OS 要求使用者名稱。

下列範例會呈現最新的案例，並說明您可以取得的例外狀況種類及其緩和措施。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

如需 AcquireTokenByIntegratedWindowsAuthentication 上可能的修飾詞清單，請參閱[AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

這是來自[MSAL JAVA dev 範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)的摘錄。

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 尚不支援此流程。

# <a name="macos"></a>[MacOS](#tab/macOS)

此流程不適用於 MacOS。

---

## <a name="username-and-password"></a>使用者名稱和密碼

您也可以藉由提供使用者名稱和密碼來取得權杖。 此流程受到限制且不建議，但仍有必要的使用案例。

### <a name="this-flow-isnt-recommended"></a>不建議使用此流程

*不建議使用*此流程，因為讓應用程式詢問使用者密碼並不安全。 如需詳細資訊，請參閱[不斷增加的密碼問題的解決方法？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 在已加入網域的 Windows 電腦上以無訊息方式取得權杖的慣用流程是[整合式 Windows 驗證](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)。 您也可以使用[裝置程式碼流程](https://aka.ms/msal-net-device-code-flow)。

> [!NOTE]
> 在某些情況下，使用使用者名稱和密碼會很有用，例如 DevOps 案例。 但是，如果您想要在您提供自己的 UI 的互動式案例中使用使用者名稱和密碼，請考慮如何將它移開。 藉由使用使用者名稱和密碼，您就會產生許多事項：
>
> - 現代化身分識別的核心原則。 密碼可能會誘騙並重新執行，因為共用密碼可以被攔截。 它與無密碼不相容。
> - 因為沒有互動，所以需要進行 MFA 的使用者無法登入。
> - 使用者無法執行單一登入（SSO）。

### <a name="constraints"></a>條件約束

下列條件約束也適用：

- 使用者名稱和密碼流程與條件式存取和多重要素驗證不相容。 因此，如果您的應用程式在租使用者系統管理員需要多重要素驗證的 Azure AD 租使用者中執行，您就無法使用此流程。 許多組織都會這麼做。
- 它僅適用于公司和學校帳戶（而非 MSA）。
- 此流程適用于 .NET desktop 和 .NET Core，但不適用於 UWP。

### <a name="b2c-specifics"></a>B2C 細節

如需詳細資訊，請參閱[使用 B2C 的資源擁有者密碼認證（ROPC）](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)。

### <a name="use-it"></a>使用它

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`包含 `AcquireTokenByUsernamePassword`的方法。

下列範例呈現簡化的案例。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

下列範例會呈現最新的案例，並說明您可以取得的例外狀況種類及其緩和措施。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

如需可套用至 `AcquireTokenByUsernamePassword`之所有修飾詞的詳細資訊，請參閱[AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

以下是來自[MSAL JAVA dev 範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)的摘錄。

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

這是來自[MSAL Python dev 範例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)的摘錄。

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[MacOS](#tab/macOS)

MSAL for macOS 不支援此流程。

---

## <a name="command-line-tool-without-a-web-browser"></a>不使用網頁瀏覽器的命令列工具

### <a name="device-code-flow"></a>裝置程式碼流程

如果您要撰寫的命令列工具沒有 web 控制項，而且您不能或不想使用先前的流程，則需要使用裝置程式碼流程。

使用 Azure AD 的互動式驗證需要網頁瀏覽器。 如需詳細資訊，請參閱[web 瀏覽器的使用](https://aka.ms/msal-net-uses-web-browser)方式。 若要在不提供網頁瀏覽器的裝置或作業系統上驗證使用者，裝置程式碼流程可讓使用者使用另一部裝置（例如電腦或行動電話）以互動方式登入。 藉由使用裝置程式碼流程，應用程式會透過針對這些裝置或作業系統所設計的兩個步驟進程來取得權杖。 這類應用程式的範例是在 iOT 或命令列工具（CLI）上執行的應用程式。 其概念如下：

1. 每當需要使用者驗證時，應用程式會為使用者提供代碼。 系統會要求使用者使用另一部裝置（例如網際網路連線的 smartphone）來移至 URL，例如 `https://microsoft.com/devicelogin`。 然後，系統會提示使用者輸入程式碼。 如此一來，網頁會引導使用者完成一般驗證體驗，其中包括同意提示和多重要素驗證（如有需要）。

2. 成功驗證之後，命令列應用程式會透過後端通道接收所需的權杖，並使用它們來執行所需的 Web API 呼叫。

### <a name="use-it"></a>使用它

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`包含名為 `AcquireTokenWithDeviceCode`的方法。

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

這個方法會使用做為參數：

- 要為其要求存取權杖的 `scopes`。
- 接收 `DeviceCodeResult`的回呼。

  ![DeviceCodeResult 屬性](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

下列範例程式碼提供最新的案例，並說明您可以取得的例外狀況種類及其緩和措施。

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

這是來自[MSAL JAVA dev 範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)的摘錄。

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

這是來自[MSAL Python dev 範例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)的摘錄。

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[MacOS](#tab/macOS)

此流程不適用於 MacOS。

---

## <a name="file-based-token-cache"></a>以檔案為基礎的權杖快取

在 MSAL.NET 中，預設會提供記憶體內部權杖快取。

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>序列化可自訂于 Windows 桌面應用程式和 web 應用程式或 web Api

在 .NET Framework 和 .NET Core 的案例中，如果您未執行任何額外的作業，記憶體中的權杖快取會持續在應用程式的持續時間內。 若要瞭解為何不提供現成的序列化，請記住，MSAL .NET desktop 或 .NET Core 應用程式可以是主控台或 Windows 應用程式（其可存取檔案系統），*以及*web 應用程式或 web api。 這些 web 應用程式和 web Api 可能會使用一些特定的快取機制，例如資料庫、分散式快取和 Redis 快取。 若要在 .NET desktop 或 .NET Core 中擁有持續性權杖快取應用程式，您必須自訂序列化。

與權杖快取序列化相關的類別和介面為下列類型：

- ``ITokenCache``，其定義訂閱權杖快取序列化要求的事件，以及用來序列化或還原序列化各種格式之快取（ADAL v3.0、MSAL 2.x 和 MSAL 3.x = ADAL 5.0 版）的方法。
- ``TokenCacheCallback`` 是傳遞至事件的回呼，以便您處理序列化。 系統將會透過 ``TokenCacheNotificationArgs`` 類型的引數進行呼叫。
- ``TokenCacheNotificationArgs`` 只會提供應用程式 ``ClientId`` 以及可使用權杖之使用者的參考。

  ![權杖快取序列化圖表](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET 會為您建立權杖快取，並且在您呼叫應用程式的 `IToken` 和 `UserTokenCache` 屬性時提供 `AppTokenCache` 快取。 您不應該自行執行介面。 當您實作自訂權杖快取序列化時，您的責任是：
>
> - 回應 `BeforeAccess` 和 `AfterAccess` 事件，或其*非同步*對應。 `BeforeAccess` 委派負責還原序列化快取。 `AfterAccess` 委派負責序列化快取。
> - 瞭解這些事件的一部分會儲存或載入 blob，並透過事件引數傳遞至您想要的任何儲存體。

根據您是針對公用用戶端應用程式（例如桌面）或機密用戶端應用程式（例如 web 應用程式或 Web API 或 daemon 應用程式）撰寫權杖快取序列化，這些策略會有所不同。

自 MSAL v2. x 之後，您有數個選項。 您的選擇取決於您是否要將快取序列化為 MSAL.NET 格式，這是 MSAL 常見的統一格式快取，也是跨平臺。 或者，您也可能想要支援 ADAL v3 的[舊版](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)權杖快取序列化。

ADAL.NET 3.x、ADAL.NET 5.x 和 MSAL.NET 之間共用 SSO 狀態的權杖快取序列化自訂會在範例[active directory-dotnet-v1 至 v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)中說明。

### <a name="simple-token-cache-serialization-msal-only"></a>簡單權杖快取序列化 (僅限 MSAL)

下列範例是針對桌面應用程式的權杖快取自訂序列化的簡單執行。 在這裡，使用者 token 快取與應用程式位於相同資料夾的檔案中。

建立應用程式之後，您可以藉由呼叫 ``TokenCacheHelper.EnableSerialization()`` 並傳遞應用程式 `UserTokenCache`來啟用序列化。

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

此 helper 類別如下列程式碼片段所示：

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

您可以從[Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal)開放原始碼程式庫，取得適用于 Windows、Mac 和 Linux 上執行的桌面應用程式之以產品品質權杖快取檔案為基礎之序列化程式的預覽。 您可以從下列 NuGet 套件將它包含在您的應用程式中： [Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

> [!NOTE]
> 免責聲明： Msal 程式庫是透過 MSAL.NET 的延伸模組。 這些程式庫中的類別可能會在未來 MSAL.NET，也就是透過重大變更來進行。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>雙重權杖快取序列化（MSAL 整合快取 + ADAL v3）

您可能想要使用統一快取格式來執行權杖快取序列化。 這種格式通用於 ADAL.NET 4.x 和 MSAL.NET 2.x，而且在相同的平臺上具有相同世代或更舊版本的其他 MSALs。 透過下列程式碼取得靈感：

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

這次 helper 類別看起來會像下列程式碼：

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [從桌面應用程式呼叫 Web API](scenario-desktop-call-api.md)
