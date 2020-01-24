---
title: 取得權杖以呼叫 Web API （行動應用程式） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立會呼叫 web Api 的行動應用程式（取得應用程式的權杖）
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 2a86e8352958524bc51b185712d6b60ec347b98e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702091"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>呼叫 web Api 的行動應用程式-取得權杖

您的應用程式必須先有存取權杖，才能開始呼叫受保護的 web Api。 本文會逐步引導您使用 Microsoft 驗證程式庫（MSAL）來取得權杖。

## <a name="scopes-to-request"></a>要求的範圍

當您要求權杖時，您必須定義一個範圍。 範圍會決定您的應用程式可以存取的資料。  

最簡單的方法是將所需的 Web API 的 `App ID URI` 與範圍 `.default`結合。 這麼做會告訴 Microsoft 身分識別平臺，您的應用程式需要在入口網站中設定所有範圍。

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>取得權杖

### <a name="acquire-tokens-via-msal"></a>透過 MSAL 取得權杖

MSAL 可讓應用程式以無訊息和互動方式取得權杖。 只要呼叫這些方法，MSAL 就會傳回所要求之範圍的存取權杖。 正確的模式是執行無訊息要求，並切換回互動式要求。

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
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**第一次嘗試以無訊息方式取得權杖：**

Objective-C：

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
            
        // Access token to call the Web API
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
 
Swift：

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
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**然後，如果 MSAL 傳回 `MSALErrorInteractionRequired`，請嘗試以互動方式取得權杖：**

Objective-C：

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
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

Swift：

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
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

MSAL for iOS 和 macOS 在以互動或無訊息方式取得權杖時，支援各種修飾詞。
* [取得權杖時的一般參數](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [取得互動式權杖的參數](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [取得無訊息權杖的參數](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

下列範例顯示使用 Microsoft Graph 以互動方式取得權杖的最少程式碼，以讀取使用者的設定檔。

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

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET 中的必要參數

`AcquireTokenInteractive` 只有一個強制參數 ``scopes``，其中包含定義需要權杖之範圍的字串列舉。 如果權杖適用于 Microsoft Graph，則在名為「許可權」的區段中，您可以在每個 Microsoft Graph API 的 api 參考中找到所需的範圍。 例如，若要[列出使用者的連絡人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)，則必須使用「使用者. 讀取」、「連絡人」等範圍。 另請參閱[Microsoft Graph 許可權參考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

如果您在建立應用程式時未指定它，則在 Android 上，您也必須指定父活動（使用 `.WithParentActivityOrWindow`，請參閱下方），讓權杖在互動之後回到該父活動。 如果您未指定，則在呼叫 `.ExecuteAsync()`時，將會擲回例外狀況（exception）。

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定選擇性參數

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 可用來透過指定提示來控制使用者的互動性

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

類別會定義下列常數：

- ``SelectAccount``：將強制 STS 顯示帳戶選取對話方塊，其中包含使用者具有會話的帳戶。 當應用程式開發人員想要讓使用者在不同的身分識別之間進行選擇時，這個選項非常有用。 此選項會驅動 MSAL，以將 ``prompt=select_account`` 傳送至身分識別提供者。 此選項是預設值，而且可以根據可用的資訊（帳戶、使用者的會話是否存在等等）提供最佳的體驗。 ...).除非您有充分的理由，否則請不要變更它。
- ``Consent``：讓應用程式開發人員強制系統提示使用者同意，即使之前已授與同意亦然。 在此情況下，MSAL 會將 `prompt=consent` 傳送給識別提供者。 此選項可用於某些安全性焦點應用程式，其中組織治理會要求使用者在每次使用應用程式時呈現同意對話方塊。
- ``ForceLogin``：讓應用程式開發人員可以讓使用者透過服務提示認證，即使不需要此使用者提示也一樣。 如果取得權杖失敗，此選項會很有用，讓使用者重新登入。 在此情況下，MSAL 會將 `prompt=login` 傳送給識別提供者。 同樣地，我們已看到它用於某些安全性焦點應用程式，組織治理會要求使用者在每次存取應用程式的特定部分時 relogs。
- ``Never`` （僅適用于 .NET 4.5 和 WinRT）不會提示使用者，而是會嘗試使用儲存在隱藏的內嵌 web 視圖中的 cookie （請參閱以下： MSAL.NET 中的 Web Views）。 使用這個選項可能會失敗，在此情況下 `AcquireTokenInteractive` 會擲回例外狀況，以通知需要 UI 互動，而且您必須使用另一個 `Prompt` 參數。
- ``NoPrompt``：不會將任何提示傳送給識別提供者。 此選項僅適用于 Azure AD B2C 編輯設定檔原則（請參閱[B2C 細節](https://aka.ms/msal-net-b2c-specificities)）。

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

這個修飾詞用於您想要讓使用者預先同意數個資源的先進案例（而不想要使用累加式同意，這通常與 MSAL.NET/Microsoft 身分識別平臺 v2.0 搭配使用）。 如需詳細資訊，請參閱[如何：將使用者同意預先用於數個資源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>其他選擇性參數

從[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)的參考檔深入瞭解 `AcquireTokenInteractive` 的所有其他選擇性參數

### <a name="acquire-tokens-via-the-protocol"></a>透過通訊協定取得權杖

我們不建議您直接使用此通訊協定。 如果您這樣做，應用程式將不會支援某些單一登入（SSO）、裝置管理和條件式存取案例。

當您使用通訊協定來取得行動應用程式的權杖時，您必須提出兩個要求：取得授權碼並交換權杖。

#### <a name="get-authorization-code"></a>取得授權碼

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>取得存取權和重新整理權杖

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
