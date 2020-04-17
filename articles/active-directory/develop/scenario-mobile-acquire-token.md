---
title: 取得權杖以呼叫 Web API(行動應用程式) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何建構調用 Web API 的行動應用。 (獲取應用的權杖。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 5750f4a5aa62b33c7d793b3e0c34f304ce1b187e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535922"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>取得呼叫 Web API 的移動應用的權杖

在應用可以調用受保護的 Web API 之前,它需要訪問權杖。 本文將引導您完成使用 Microsoft 身份驗證庫 (MSAL) 獲取權杖的過程。

## <a name="define-a-scope"></a>定義範圍

請求權杖時,需要定義作用域。 範圍確定應用可以訪問哪些數據。

定義作用域的最簡單方法是將所需的 Web`App ID URI`API`.default`與作用域 相結合。 此定義告訴 Microsoft 標識平臺,你的應用需要在門戶中設置的所有作用域。

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>取得權杖

### <a name="acquire-tokens-via-msal"></a>透過 MSAL 取得權杖

MSAL 允許應用以靜默和交互方式獲取權杖。 呼叫`AcquireTokenSilent()``AcquireTokenInteractive()`或 時,MSAL 傳回請求作用網域的存取權杖。 正確的模式是發出靜默請求,然後回退到互動式請求。

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

首先嘗試以靜默方式取得權杖:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

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

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

如果 MSAL`MSALErrorInteractionRequired`傳回 ,則嘗試以互動方式取得權杖:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
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

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

適用於 iOS 和 macOS 的 MSAL 支援各種修改器以互動或靜默方式取得權杖:
* [取得權杖的參數](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [取得互動式權杖的參數](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [取得靜默權杖的參數](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

下面的範例顯示了以互動方式獲取權杖的最小代碼。 該示例使用 Microsoft 圖形讀取使用者的配置檔。

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

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET中的必要參數

`AcquireTokenInteractive`只有一個必要參數: `scopes`. 參數`scopes`列舉定義需要權杖的範圍的字串。 如果權杖用於Microsoft圖形,則可以在每個Microsoft圖形API的API引用中找到所需的作用域。 在引用中,轉到"許可權"部分。

例如,要[列出使用者的聯繫人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts),請使用"使用者.閱讀","連絡人.閱讀"的範圍。 有關詳細資訊,請參閱[Microsoft 圖形權限參考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上,您可以使用 建立應用程式時`PublicClientApplicationBuilder`指定父活動 。 如果當時未指定父活動,則稍後可以使用以下部分中操作來指定它`.WithParentActivityOrWindow`。 如果指定父活動,則令牌在交互後將返回該父活動。 如果不指定它,`.ExecuteAsync()`則呼叫將引發異常。

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET中的特定可選參數

以下各節解釋MSAL.NET中的可選參數。

##### <a name="withprompt"></a>帶提示

參數`WithPrompt()`通過指定提示控制與使用者的互動性。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

類別定義以下常數:

- `SelectAccount`強制安全令牌服務 (STS) 顯示帳戶選擇對話方塊。 該對話框包含使用者具有會話的帳戶。 如果要讓使用者在不同的標識中選擇,則可以使用此選項。 此選項驅動 MSAL`prompt=select_account`發送到標識提供程式。

    常`SelectAccount`量是預設值,它根據可用資訊有效地提供了最佳體驗。 可用資訊可能包括帳戶、使用者會話的狀態等。 除非有充分理由更改此預設值,否則不要更改此預設值。
- `Consent`使您能夠提示使用者同意,即使之前已授予同意。 在這種情況下,MSAL 會發送`prompt=consent`到 標識提供程式。

    您可能希望在以安全為中心的應用程式中`Consent`使用常量,其中組織治理要求使用者每次使用該應用程式時都看到同意對話方塊。
- `ForceLogin`使服務能夠提示使用者輸入憑據,即使不需要提示。

    如果權杖取得失敗,並且您希望讓使用者重新登錄,則此選項非常有用。 在這種情況下,MSAL 會發送`prompt=login`到 標識提供程式。 您可能希望在以安全為中心的應用程式中使用此選項,其中組織治理要求使用者每次訪問應用程式的特定部分時登錄。
- `Never`僅適用於 .NET 4.5 和 Windows 運行時 (WinRT)。 此常量不會提示使用者,但它將嘗試使用存儲在隱藏嵌入 Web 檢視中的 Cookie。 有關詳細資訊,請參閱將[Web 瀏覽器與 MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)一起使用。

    如果這個選項失敗,則`AcquireTokenInteractive`引發異常以通知您需要 UI 互動。 然後,您需要使用另一`Prompt`個參數。
- `NoPrompt`不會向標識提供程式發送提示。

    此選項僅適用於 Azure 活動目錄 B2C 中的編輯設定檔策略。 有關詳細資訊,請參閱[B2C 詳細資訊](https://aka.ms/msal-net-b2c-specificities)。

##### <a name="withextrascopetoconsent"></a>具有超範圍同意

在進`WithExtraScopeToConsent`階方案中使用修改器,您希望使用者向多個資源提供預先同意。 當不想使用增量同意時,可以使用此修改器,增量同意通常用於MSAL.NET或Microsoft標識平臺 2.0。 有關詳細資訊,請參閱讓[使用者預先同意多個資源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

以下是程式碼範例：

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>其他可選擇參數

要瞭解的其他可選擇參數,`AcquireTokenInteractive`請參考[權碼互動式參數產生器 的參考文件](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)。

### <a name="acquire-tokens-via-the-protocol"></a>透過協定抓取權杖

我們不建議直接使用該協議獲取權杖。 如果這樣做,則應用將不支援某些涉及單一登錄 (SSO)、設備管理和條件訪問的方案。

當您使用該協定取得行動應用的權杖時,請發出兩個請求:

* 獲取授權代碼。
* 將代碼交換為權杖。

#### <a name="get-an-authorization-code"></a>取得授權碼

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>取得存取權限並刷新權杖

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-mobile-call-api.md)
