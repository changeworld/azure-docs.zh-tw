---
title: 取得權杖以呼叫 Web API （行動應用程式） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立會呼叫 web Api 的行動應用程式。 （取得應用程式的權杖）。
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
ms.openlocfilehash: 2be074c457eaadd1fb6467cbcfdd45a2e7745613
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098895"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>為呼叫 web Api 的行動應用程式取得權杖

在您的應用程式可以呼叫受保護的 web Api 之前，它需要存取權杖。 本文會逐步引導您完成使用 Microsoft 驗證程式庫（MSAL）取得權杖的程式。

## <a name="define-a-scope"></a>定義範圍

當您要求權杖時，您必須定義一個範圍。 範圍會決定您的應用程式可以存取的資料。

定義範圍最簡單的方式，就是將所需的 Web API `App ID URI`與範圍`.default`結合。 此定義會告訴 Microsoft 身分識別平臺，您的應用程式需要在入口網站中設定的所有範圍。

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

MSAL 可讓應用程式以無訊息和互動方式取得權杖。 當您呼叫`AcquireTokenSilent()`或`AcquireTokenInteractive()`時，MSAL 會傳回所要求範圍的存取權杖。 正確的模式是提出無訊息要求，然後切換回互動式要求。

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

第一次嘗試以無訊息方式取得權杖：

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

如果 MSAL 傳回`MSALErrorInteractionRequired`，請嘗試以互動方式取得權杖：

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

適用于 iOS 和 macOS 的 MSAL 支援各種修飾詞，以互動或無訊息方式取得權杖：
* [取得權杖的一般參數](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [取得互動式 token 的參數](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [取得無訊息標記的參數](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

下列範例顯示以互動方式取得權杖的最少程式碼。 此範例會使用 Microsoft Graph 來讀取使用者的設定檔。

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

`AcquireTokenInteractive`只有一個強制參數： `scopes`。 `scopes`參數會列舉字串，其會定義需要權杖的範圍。 如果權杖是用於 Microsoft Graph，您可以在每個 Microsoft Graph API 的 API 參考中找到所需的範圍。 在參考中，移至「許可權」一節。

例如，若要[列出使用者的連絡人](https://docs.microsoft.com/graph/api/user-list-contacts)，請使用「使用者. 讀取」、「連絡人. 讀取」的範圍。 如需詳細資訊，請參閱[Microsoft Graph 許可權參考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上，您可以在使用`PublicClientApplicationBuilder`建立應用程式時指定父活動。 如果您在該時間未指定父活動，稍後可以使用`.WithParentActivityOrWindow`來指定它，如下一節所示。 如果您指定父活動，則權杖會在互動之後回到該父活動。 如果您未指定，則`.ExecuteAsync()`呼叫會擲回例外狀況。

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定選擇性參數

下列各節說明 MSAL.NET 中的選擇性參數。

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()`參數會藉由指定提示來控制與使用者的互動。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

類別會定義下列常數：

- `SelectAccount`強制 Security Token Service （STS）出示 [帳戶選取] 對話方塊。 此對話方塊包含使用者具有會話的帳戶。 當您想要讓使用者在不同的身分識別之間進行選擇時，可以使用此選項。 此選項會驅動 MSAL 以`prompt=select_account`傳送給識別提供者。

    `SelectAccount`常數是預設值，它會根據可用的資訊，有效地提供最佳的體驗。 可用的資訊可能包括帳戶、使用者的會話是否存在等等。 請不要變更此預設值，除非您有很好的理由要這麼做。
- `Consent`可讓您提示使用者同意，即使之前已授與同意亦然。 在此情況下，MSAL `prompt=consent`會將傳送至身分識別提供者。

    您可能想要在以`Consent`安全性為主的應用程式中使用常數，其中組織治理會要求使用者在每次使用應用程式時看到同意對話方塊。
- `ForceLogin`讓服務即使不需要提示，也會提示使用者提供認證。

    如果權杖取得失敗，而您想要讓使用者重新登入，此選項會很有用。 在此情況下，MSAL `prompt=login`會將傳送至身分識別提供者。 您可能會想要在以安全性為主的應用程式中使用此選項，組織治理會要求使用者在每次存取應用程式的特定部分時登入。
- `Never`僅適用于 .NET 4.5 和 Windows 執行階段（WinRT）。 這個常數不會提示使用者，但會嘗試使用儲存在隱藏的內嵌 web 視圖中的 cookie。 如需詳細資訊，請參閱搭配[使用網頁瀏覽器與 MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)。

    如果此選項失敗，則`AcquireTokenInteractive`會擲回例外狀況，通知您需要 UI 互動。 接著，您必須使用另`Prompt`一個參數。
- `NoPrompt`不會傳送提示給識別提供者。

    此選項僅適用于 Azure Active Directory B2C 中的編輯設定檔原則。 如需詳細資訊，請參閱[B2C 細節](https://aka.ms/msal-net-b2c-specificities)。

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

在您`WithExtraScopeToConsent`想要讓使用者提前同意數個資源的 advanced 案例中使用修飾詞。 當您不想要使用累加式同意時，可以使用這個修飾詞，這通常與 MSAL.NET 或 Microsoft 身分識別平臺2.0 搭配使用。 如需詳細資訊，請參閱將[使用者同意預先用於數個資源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

以下是程式碼範例：

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>其他選擇性參數

若要瞭解的其他選擇性參數`AcquireTokenInteractive`，請參閱 AcquireTokenInteractiveParameterBuilder 的[參考檔](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)。

### <a name="acquire-tokens-via-the-protocol"></a>透過通訊協定取得權杖

我們不建議直接使用通訊協定來取得權杖。 如果您這樣做，應用程式將不會支援涉及單一登入（SSO）、裝置管理和條件式存取的某些案例。

當您使用通訊協定來取得行動應用程式的權杖時，請提出兩個要求：

* 取得授權碼。
* 交換權杖的程式碼。

#### <a name="get-an-authorization-code"></a>取得授權碼

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>取得存取權並重新整理權杖

```HTTP
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
