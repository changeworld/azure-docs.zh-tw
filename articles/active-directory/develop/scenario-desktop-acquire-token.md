---
title: 取得權杖以呼叫 Web API(桌面應用) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何建構 Web API 取得應用權杖的桌面應用程式
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a5942a9d614bbb06fadb1d4b16d4c68c007434c7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885313"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>呼叫 Web API 的桌面應用:取得權杖

建構公共用戶端應用程式的實體後,您將使用它獲取權杖,然後用於呼叫 Web API。

## <a name="recommended-pattern"></a>推薦模式

Web API`scopes`由 其定義。 無論您在應用程式中提供何種體驗,要使用的模式是:

- 通過調用`AcquireTokenSilent`從權杖緩存中系統地嘗試獲取權杖。
- 如果此呼叫失敗,請使用要使用的`AcquireToken`串流,此處由`AcquireTokenXX`表示 。

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>在MSAL.NET

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

### <a name="in-msal-for-ios-and-macos"></a>在適用於 iOS 和 macOS 的 MSAL 中

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

以下是在桌面應用程式中獲取權杖的各種方法。

## <a name="acquire-a-token-interactively"></a>以互動方式取得權杖

下面的範例顯示用於使用 Microsoft 圖形以互動方式獲取權杖的最小程式碼。

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>在MSAL.NET

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

`AcquireTokenInteractive`只有一個必需參數``scopes``,其中包含定義需要權杖的範圍的字串的枚舉。 如果權杖是Microsoft圖形,則可以在名為"許可權"部分的每個Microsoft圖形API的API引用中找到所需的作用域。 例如,要[列出使用者的聯繫人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts),必須使用"使用者.閱讀"、"連絡人.讀取"的範圍。 有關詳細資訊,請參閱[Microsoft 圖形權限參考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上,還需要`.WithParentActivityOrWindow`使用 指定 父活動,如下所示,以便令牌在交互後返回該父活動。 如果不指定它,則調用`.ExecuteAsync()`時將引發異常。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET中的特定可選參數

#### <a name="withparentactivityorwindow"></a>具有父活動或視窗

UI 很重要,因為它是互動式的。 `AcquireTokenInteractive`具有一個特定的可選參數,可以指定支援它的平臺的父 UI。 在桌面應用程式中使用時,`.WithParentActivityOrWindow`具有不同的類型,具體取決於平臺。

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

- 在 .NET`object`標準`Activity`上`UIViewController`, 預期是在`NSWindow`Android、iOS 上、MAC`IWin32Window``IntPr`上和或 Windows 上。
- 在 Windows`AcquireTokenInteractive`上, 必須從 UI 線程呼叫,以便嵌入的瀏覽器獲得相應的 UI 同步上下文。 不從 UI 線程調用可能會導致消息無法正確泵送,並且使用 UI 死鎖方案。 如果您不在 UI 線程上,則從 UI 線程呼叫 Microsoft 身份驗證庫 (MSALs) 的一種方法是`Dispatcher`使用 WPF 上。
- 如果使用 WPF,則從 WPF 控制項獲取視窗,`WindowInteropHelper.Handle`可以使用該類。 然後呼叫來自 WPF 控制`this`檔 ( ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>帶提示

`WithPrompt()`用於通過指定提示來控制與使用者的互動性。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

類別定義以下常數:

- ``SelectAccount``強制 STS 顯示包含使用者具有作業階段的帳戶的帳戶選擇對話方塊。 當應用程式開發人員希望允許使用者在不同的標識中進行選擇時,此選項非常有用。 此選項驅動 MSAL``prompt=select_account``發送到標識提供程式。 這個選項是預設值。 它很好地根據可用資訊(如使用者帳戶和會話的存在)提供最佳體驗。 除非你有充分的理由這樣做,否則不要改變它。
- ``Consent``使應用程式開發人員能夠強制用戶獲得同意,即使之前已授予同意。 在這種情況下,MSAL 會發送`prompt=consent`到 標識提供程式。 此選項可用於某些以安全為中心的應用程式,其中組織治理要求每次使用應用程式時向用戶顯示同意對話方塊。
- ``ForceLogin``使應用程式開發人員能夠讓用戶通過服務提示其憑據,即使可能不需要此使用者提示。 如果獲取權杖失敗,此選項可用於讓使用者再次登錄。 在這種情況下,MSAL 會發送`prompt=login`到 標識提供程式。 有時,它用於以安全為中心的應用程式,其中組織治理要求使用者每次訪問應用程式的特定部分時重新簽名。
- ``Never``(僅限 .NET 4.5 和 WinRT)不會提示使用者,而是嘗試使用存儲在隱藏嵌入 Web 檢視中的 Cookie。 有關詳細資訊,請參閱MSAL.NET中的 Web 檢視。 使用此選項可能會失敗。 在這種情況下,`AcquireTokenInteractive`引發異常以通知需要 UI 互動。 您需要使用其他`Prompt`參數。
- ``NoPrompt``不會向標識提供程式發送任何提示。 此選項僅適用於 Azure 活動目錄 (Azure AD) B2C 編輯設定檔策略。 有關詳細資訊,請參閱[Azure AD B2C 詳細資訊](https://aka.ms/msal-net-b2c-specificities)。

#### <a name="withextrascopetoconsent"></a>具有超範圍同意

此修改器用於高級方案,您希望使用者預先同意多個資源,並且不希望使用增量同意,這通常與MSAL.NET/the Microsoft 標識平臺一起使用。 有關詳細資訊,請參閱讓[使用者預先同意多個資源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>使用自訂 WebUi

Web UI 是調用瀏覽器的機制。 此機制可以是專用的 UI Web 瀏覽器控件,也可以是委派打開瀏覽器的一種方式。
MSAL 為大多數平臺提供 Web UI 實現,但在某些情況下,您可能希望自己託管瀏覽器:

- MSAL 未明確涵蓋的平臺,例如,桌面上的 Blazor、Unity 和 Mono。
- 您希望對應用程式進行 UI 測試,並使用可與硒一起使用的自動瀏覽器。
- 運行MSAL的瀏覽器和應用程式處於單獨的進程。

##### <a name="at-a-glance"></a>速覽

為此,您給 MSAL `start Url`,這需要顯示在選擇的瀏覽器中,以便最終使用者可以輸入諸如其使用者名等專案。
身份驗證完成後,你的應用需要傳回 MSAL ,MSAL`end Url`包含 Azure AD 提供的代碼。
主機`end Url``redirectUri`始終 為 。 要攔截`end Url`,執行以下操作之一:

- 監視瀏覽器重定向,直到`redirect Url`被擊中。
- 讓瀏覽器重定向到您監視的 URL。

##### <a name="withcustomwebui-is-an-extensibility-point"></a>使用CustomWebUi是一個擴充點

`WithCustomWebUi`是可用於在公共用戶端應用程式中提供您自己的 UI 的擴展點。 您還可以讓使用者通過標識提供程式的 /授權終結點,並讓他們登錄並同意。 然後,MSAL.NET可以兌換身份驗證代碼並獲得權杖。 例如,它在 Visual Studio 中使用電子應用程式(例如,Visual Studio 反饋)提供 Web 互動,但留給 MSAL.NET 完成大部分工作。 如果要提供 UI 自動化,也可以使用它。 在公共用戶端應用程式中,MSAL.NET使用代碼交換證明密鑰 (PKCE) 標準來確保安全性得到尊重。 只有MSAL.NET才能兌換代碼。 有關詳細資訊,請參閱[OAuth 公共用戶端代碼交換的 RFC 7636 - 代碼交換的證明金鑰](https://tools.ietf.org/html/rfc7636)。

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>與自訂 WebUi 一起使用

要使用`.WithCustomWebUI`,請按照以下步驟操作。

  1. 實作 `ICustomWebUi` 介面。 有關詳細資訊,請參閱[此網站](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)。 實現一`AcquireAuthorizationCodeAsync`種方法並接受MSAL.NET計算的授權代碼 URL。 然後,讓用戶通過與標識提供程式的交互,並返回標識提供程式將調用您的實現以及授權代碼的 URL。 如果您出現問題,您的實現應引發異常`MsalExtensionException`,以便與 MSAL 很好地協作。
  2. 在呼叫`AcquireTokenInteractive`中`.WithCustomUI()`, 使用傳遞自訂 Web UI 實體的修改器。

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>ICustomWebUi 在測試自動化中的實現範例:硒WebUI

MSAL.NET團隊重寫了UI測試以使用此擴展機制。 如果您感興趣,請查看原始程式碼中MSAL.NET的[SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160)類。

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>透過系統WebView選項提供出色的體驗

從 MSAL.NET 4.1[`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)中,您可以指定:

- 在系統 Web`BrowserRedirectError`瀏覽器中出現登入或同意`HtmlMessageError`錯誤時要 轉到的 URI 或要顯示 ( 的 HTML 片段)。
- URI 轉到`BrowserRedirectSuccess`( )`HtmlMessageSuccess`或 HTML 片段以顯示 ( ), 成功登入或同意時。
- 運行以啟動系統瀏覽器的操作。 您可以通過`OpenBrowserAsync`設置 委託來提供您自己的實現。 該類還為兩個瀏覽器提供了預設實現:`OpenWithEdgeBrowserAsync``OpenWithChromeEdgeBrowserAsync`和 微軟邊緣和[微軟邊緣鉻](https://www.windowscentral.com/faq-edge-chromium),分別。

要使用此結構,請編寫如下範例:

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

#### <a name="other-optional-parameters"></a>其他可選擇參數

要瞭解有關 的所有其他可選`AcquireTokenInteractive`參數 ,請參閱[取得權杖互動式參數產生器](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)。

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

MSAL Python 不直接提供互動式獲取權杖方法。 相反,它要求應用程式在其實現使用者交互流時發送授權請求以獲取授權代碼。 然後,可以將此代碼傳遞給`acquire_token_by_authorization_code`方法以獲取權杖。

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

### <a name="in-msal-for-ios-and-macos"></a>在適用於 iOS 和 macOS 的 MSAL 中

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

要在網域或 Azure AD 聯接電腦上登錄網域使用者,請使用整合 Windows 身份驗證 (IWA)。

### <a name="constraints"></a>條件約束

- 整合 Windows 身份驗證僅適用於*聯合使用者,* 即在活動目錄中創建並由 Azure AD 支援的使用者。 直接在 Azure AD 建立且沒有活動目錄支援的使用者(稱為*託管*使用者)不能使用此身份驗證流。 此限制不會影響使用者名和密碼流。
- IWA 適用於為 .NET 框架、.NET 核心和通用 Windows 平臺 (UWP) 平台編寫的應用。
- IWA 不會繞過多重身份驗證 (MFA)。 如果配置了 MFA,則如果需要 MFA 質詢,IWA 可能會失敗,因為 MFA 需要使用者交互。
  > [!NOTE]
  > 這一個是棘手的。 IWA 是非互動式的,但 MFA 需要使用者互動。 您不控制標識提供程式何時請求執行 MFA,租戶管理員會這樣做。 根據我們的觀察,當您從其他國家/地區登錄時,當您通過 VPN 連接到公司網路時,有時甚至通過 VPN 連接時,也需要 MFA。 不要期望有一套確定性的規則。 Azure AD 使用 AI 持續瞭解是否需要 MFA。 如果 IWA 失敗,請回退回使用者提示,如互動式身份驗證或設備代碼流。

- 傳入的權`PublicClientApplicationBuilder`限 必須是:
  - 窗體`https://login.microsoftonline.com/{tenant}/`的租戶`tenant`,其中 表示租戶 ID 的 GUID 或與租戶關聯的域。
  - 對於任何工作和學校帳戶: `https://login.microsoftonline.com/organizations/`.
  - 不支援 Microsoft 個人帳戶。 不能使用 /公共租戶或 /消費者租戶。

- 因為整合 Windows 身份驗證是一個靜默流:
  - 應用程式的用戶必須事先同意使用該應用程式。
  - 或者,租戶管理員必須事先同意租戶中的所有使用者使用該應用程式。
  - 換句話說：
    - 作為開發人員,您自己選擇了 Azure 門戶中的 **「授予**」按鈕。
    - 或者,租戶管理員在應用程式的註冊**API 許可權**選項卡上選擇了 **[租戶域] 授予/撤消管理員同意**按鈕。 有關詳細資訊,請參閱[新增存取 Web API 的權限](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)。
    - 或者,您為使用者提供了一種使用者同意應用程式的方法。 有關詳細資訊,請參閱[要求個人使用者同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)。
    - 或者,您為租戶管理員提供了一種同意應用程式的方法。 有關詳細資訊,請參閱[管理員同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)。

- 此流已為 .NET 桌面、.NET 核心和 UWP 應用啟用。

有關同意的詳細資訊,請參閱[Microsoft 標識平台權限和同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)。

### <a name="learn-how-to-use-it"></a>了解其使用方式

# <a name="net"></a>[.NET](#tab/dotnet)

在MSAL.NET,您需要使用:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

通常只需要一個參數`scopes`( 。 根據 Windows 管理員設置策略的方式,可能不允許 Windows 電腦上的應用程式查找登錄使用者。 在這種情況下,請使用第二種方法,`.WithUsername()`並將登入使用者的使用者名稱作為 UPN 格式`joe@contoso.com`傳遞,例如 。 在 .NET Core 上,只有採用使用者名的重載可用,因為 .NET Core 平台無法向作業系統詢問使用者名。

下面的示例介紹了最新的情況,並說明瞭您可以獲取的異常類型及其緩解措施。

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

有關取得權杖透過整合的 Windows 身份驗證上可能的修飾符的清單,請參考[取權杖由整合 WindowsAuth 參數產生器](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

此資料提取來自[MSAL Java 開發範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

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

MSAL Python 中尚不支援此流。

# <a name="macos"></a>[MacOS](#tab/macOS)

此流不適用於 MacOS。

---

## <a name="username-and-password"></a>使用者名稱和密碼

您還可以通過提供使用者名和密碼來獲取權杖。 此流是有限的,不建議,但仍有一些用例,這是必要的。

### <a name="this-flow-isnt-recommended"></a>不建議使用此流

*不建議*使用此流,因為讓應用程式向使用者詢問其密碼不安全。 有關詳細資訊,請參閱密碼[日益嚴重的解決方案是什麼?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) 在 Windows 網域聯接的電腦上靜默取得權杖的首選流是[整合 Windows 認證](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)。 您還可以使用[裝置碼串流](https://aka.ms/msal-net-device-code-flow)。

> [!NOTE]
> 在某些情況下,使用使用者名和密碼非常有用,例如 DevOps 方案。 但是,如果要在提供自己的 UI 的互動式方案中使用使用者名和密碼,請考慮如何遠離它。 使用使用者名稱和密碼,您放棄了許多內容:
>
> - 現代身份的核心信條。 密碼可能會被網路釣魚和重播,因為共用的機密可能會被截獲。 它與無密碼不相容。
> - 需要執行 MFA 的使用者無法登錄,因為沒有交互。
> - 用戶無法執行單一登錄 (SSO)。

### <a name="constraints"></a>條件約束

以下限制也適用於:

- 使用者名和密碼流與條件訪問和多重身份驗證不相容。 因此,如果應用在租戶管理員需要多重身份驗證的 Azure AD 租戶中運行,則不能使用此流。 許多組織都這樣做。
- 它僅適用於工作和學校帳戶(不適用於 MSA)。
- 該流在 .NET 桌面和 .NET 核心上可用,但在 UWP 上不可用。

### <a name="b2c-specifics"></a>B2C 詳情

有關詳細資訊,請參閱使用[B2C 的資源擁有者密碼認證認證認證 (ROPC)。](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)

### <a name="use-it"></a>使用它

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`包含方法`AcquireTokenByUsernamePassword`。

下面的示例提供了一個簡化的案例。

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

下面的示例介紹了最新的情況,並說明瞭您可以獲取的異常類型及其緩解措施。

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

有關可應用於`AcquireTokenByUsernamePassword`的所有修改器的詳細資訊,請參閱[獲取權杖密碼參數產生器](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

以下摘錄來自[MSAL Java 開發範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

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

此資料存取[於 MSAL Python 開發範例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)。

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

對於 macOS 的 MSAL 中不支援此流。

---

## <a name="command-line-tool-without-a-web-browser"></a>沒有網頁瀏覽器的命令列工具

### <a name="device-code-flow"></a>裝置程式碼流程

如果要編寫沒有 Web 控制件的命令列工具,並且不能或不想使用以前的流,則需要使用設備代碼流。

使用 Azure AD 進行互動式身份驗證需要 Web 瀏覽器。 有關詳細資訊,請參閱[Web 瀏覽器的用法](https://aka.ms/msal-net-uses-web-browser)。 要對不提供 Web 瀏覽器的設備或作業系統上的使用者進行身份驗證,設備代碼流允許使用者使用其他設備(如電腦或行動電話)以互動方式登錄。 通過使用設備代碼流,應用程式通過為這些設備或 OS 設計的兩步過程獲取權杖。 此類應用程式的範例是在 iOT 或命令列工具 (CLI) 上執行的應用程式。 其理念是:

1. 每當需要使用者身份驗證時,應用都會為使用者提供代碼。 例如,使用者被要求使用其他設備(如聯網的智慧手機)轉到`https://microsoft.com/devicelogin`URL。 然後提示用戶輸入代碼。 這樣做后,網頁將引導使用者完成正常的身份驗證體驗,其中包括同意提示和多重身份驗證(如有必要)。

2. 身份驗證成功後,命令行應用通過後通道接收所需的權杖,並使用它們執行所需的 Web API 調用。

### <a name="use-it"></a>使用它

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`包含名為的方法`AcquireTokenWithDeviceCode`。

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

此方法用作參數:

- `scopes`要求存取權杖的 。
- 接收的回調。 `DeviceCodeResult`

  ![裝置代碼結果屬性](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

以下示例代碼介紹了最新情況,並說明瞭您可以獲取的異常類型及其緩解措施。

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

此資料提取來自[MSAL Java 開發範例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

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

此資料存取[於 MSAL Python 開發範例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)。

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

此流不適用於 MacOS。

---

## <a name="file-based-token-cache"></a>基於檔案的權杖快取

在 MSAL.NET 中，預設會提供記憶體內部權杖快取。

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>序列化在 Windows 桌面應用程式與 Web 應用程式或 Web API 中可自訂

在 .NET 框架和 .NET Core 中,如果不執行任何額外操作,記憶體中令牌緩存將持續在應用程式的持續時間內。 要瞭解為什麼序列化不是開箱即用的,請記住 MSAL .NET 桌面或 .NET Core 應用程式可以是控制台或 Windows 應用程式(可以存取檔案系統),*也可以是*Web 應用程式或 Web API。 這些 Web 應用和 Web API 可能使用某些特定的緩存機制,如資料庫、分散式緩存和 Redis 緩存。 要在 .NET 桌面或 .NET Core 中具有持久權杖快取應用程式,您需要自訂序列化。

權杖快取序列化中涉及的類和介面類型如下:

- ``ITokenCache``定義要訂閱權杖快取序列化請求的事件,以及以各種格式序列化或反序列化緩存的方法(ADAL v3.0、MSAL 2.x 和 MSAL 3.x = ADAL v5.0)。
- ``TokenCacheCallback`` 是傳遞至事件的回呼，以便您處理序列化。 系統將會透過 ``TokenCacheNotificationArgs`` 類型的引數進行呼叫。
- ``TokenCacheNotificationArgs``僅提供應用程式``ClientId``和對權杖可用的使用者的引用。

  ![權杖快取序列化圖](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET 會為您建立權杖快取，並且在您呼叫應用程式的 `UserTokenCache` 和 `AppTokenCache` 屬性時提供 `IToken` 快取。 您不應該自己實現介面。 當您實作自訂權杖快取序列化時，您的責任是：
>
> - 回應`BeforeAccess``AfterAccess`和 事件,或其*非同步*對應項。 委託`BeforeAccess`負責取消序列化緩存。 委託`AfterAccess`序列化緩存。
> - 瞭解這些事件的一部分儲存或載入 Blob,這些 blob 通過事件參數傳遞到所需的任何儲存。

策略因是為公共用戶端應用程式(如桌面或機密用戶端應用程式(如 Web 應用或 Web API 或守護行程應用程式)編寫權杖緩存序列化而不同。

由於 MSAL v2.x,您有多種選擇。 您的選擇取決於是否要僅將緩存序列化為MSAL.NET格式,該格式是MSAL常見的統一格式緩存,但也跨平台進行。 或者,您可能還希望支援 ADAL v3[的舊令牌](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)快取序列化。

令牌緩存序列化的自定義,以便在 ADAL.NET 3.x、ADAL.NET 5.x 和 MSAL.NET 之間共用 SSO 狀態,在示例[活動-目錄點-v1 到 v2 中](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)進行了部分說明。

### <a name="simple-token-cache-serialization-msal-only"></a>簡單權杖快取序列化 (僅限 MSAL)

下面的示例是桌面應用程式令牌緩存的自定義序列化天真的實現。 在這裡,使用者令牌緩存位於與應用程式相同的資料夾中的檔中。

生成應用程式後,可以通過調``TokenCacheHelper.EnableSerialization()``用和傳遞`UserTokenCache`應用程式來啟用序列化。

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

此說明程式類類似於以下代碼段:

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

[Microsoft.Identity.Client.擴展.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal)開源庫提供了適用於在 Windows、Mac 和 Linux 上運行的桌面應用程式的公共用戶端應用程式的產品品質令牌緩存序列化器的預覽。 你可以從以下 NuGet 套件中將其包含在應用程式中[:Microsoft.身份.用戶端.擴展.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

> [!NOTE]
> 免責聲明:微軟.身份.用戶端.擴展.Msal 庫是MSAL.NET的擴展。 這些庫中的類將來可能會進入MSAL.NET,就像或隨著重大更改。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>雙權碼快取序列化 (MSAL 統一快取 + ADAL v3)

您可能希望使用統一快取格式實現權杖緩存序列化。 此格式是常見的ADAL.NET 4.x 和MSAL.NET 2.x,以及同一代或更高級的其他 MSA,在同一平臺上。 從以下代碼獲得靈感:

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

這一次,幫助器類看起來像以下代碼:

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
> [從桌面應用呼叫 Web API](scenario-desktop-call-api.md)
