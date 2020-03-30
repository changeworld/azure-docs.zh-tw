---
title: ADAL 到 MSAL 遷移指南 （MSAL iOS/macOS） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解 iOS/macOS 的 MSAL 和用於目標 C 的 Azure AD 身份驗證庫 （ADAL） 之間的差異。ObjC）以及如何遷移到 MSAL 的 iOS/macOS。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085170"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>將應用程式遷移到 MSAL 以用於 iOS 和 macOS

Azure 活動目錄身份驗證庫 （[ADAL 目標 C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)） 創建是為了通過 v1.0 終結點使用 Azure 活動目錄帳戶。

用於 iOS 和 macOS （MSAL） 的 Microsoft 身份驗證庫旨在通過 Microsoft 標識平臺（正式）與所有 Microsoft 標識（如 Azure 活動目錄 （Azure AD） 帳戶、個人 Microsoft 帳戶和 Azure AD B2C 帳戶配合使用Azure AD v2.0 終結點）。

Microsoft 標識平臺與 Azure 活動目錄 v1.0 存在一些關鍵差異。 本文重點介紹這些差異，並提供將應用從 ADAL 遷移到 MSAL 的指導。

## <a name="adal-and-msal-app-capability-differences"></a>ADAL 和 MSAL 應用功能差異

### <a name="who-can-sign-in"></a>誰可以登入

* ADAL 僅支援工作帳戶和學校帳戶，也稱為 Azure AD 帳戶。
* MSAL 支援個人 Microsoft 帳戶 （MSA 帳戶），如Hotmail.com、Outlook.com和Live.com。
* MSAL 支援工作帳戶和學校帳戶以及 Azure AD B2C 帳戶。

### <a name="standards-compliance"></a>標準合規性

* Microsoft 標識平臺終結點遵循 OAuth 2.0 和 OpenId 連接標準。

### <a name="incremental-and-dynamic-consent"></a>增量和動態同意

* Azure 活動目錄 v1.0 終結點要求在應用程式註冊期間提前聲明擁有權限。 這意味著這些許可權是靜態的。
* Microsoft 標識平臺允許您動態請求許可權。 應用只能根據需要請求許可權，並在應用需要時請求更多許可權。

有關 Azure 活動目錄 v1.0 和 Microsoft 標識平臺之間的差異的詳細資訊，請參閱[為什麼更新到 Microsoft 標識平臺 （v2.0）？](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="adal-and-msal-library-differences"></a>ADAL 和 MSAL 庫差異

MSAL 公共 API 反映了 Azure AD v1.0 和 Microsoft 標識平臺之間的幾個關鍵區別。

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSAL公共用戶端應用程式，而不是AD身份驗證上下文

`ADAuthenticationContext`是 ADAL 應用創建的第一個物件。 它代表 ADAL 的具現化。 應用為每個 Azure 活動`ADAuthenticationContext`目錄雲和租戶（許可權）組合創建一個新實例。 這同樣`ADAuthenticationContext`可用於獲取多個公共用戶端應用程式的權杖。

在 MSAL 中，主交互是通過`MSALPublicClientApplication`物件，該物件以[OAuth 2.0 公共用戶端](https://tools.ietf.org/html/rfc6749#section-2.1)為模型。 的一`MSALPublicClientApplication`個實例可用於與多個 AAD 雲和租戶進行交互，而無需為每個頒發機構創建新實例。 對於大多數應用，一個`MSALPublicClientApplication`實例就足夠了。

### <a name="scopes-instead-of-resources"></a>範圍而不是資源

在 ADAL 中，應用必須提供*資源*識別碼，`https://graph.microsoft.com`例如從 Azure 活動目錄 v1.0 終結點獲取權杖。 資源可以定義它理解的多個作用域或應用清單中的 oAuth2 許可權。 這允許用戶端應用從該資源請求權杖，以在應用註冊期間預定義的一組作用域。

在 MSAL 中，應用不是單個資源識別碼，而是每個請求提供一組作用域。 作用域是資源識別碼，後跟表單資源/許可權中的許可權名稱。 例如， `https://graph.microsoft.com/user.read`

在 MSAL 中提供作用域有兩種方法：

* 提供應用需要的擁有權限的清單。 例如： 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    在這種情況下，應用請求 和`directory.read``directory.write`許可權。 如果使用者之前未同意此應用的許可權，將請求使用者同意這些許可權。 應用程式還可能收到使用者已同意的應用程式的其他許可權。 只會提示使用者同意新的許可權或尚未授予的許可權。

* `/.default` 範圍。

這是每個應用程式的內置作用域。 它是指在註冊應用程式時配置的許可權的靜態清單。 其行為類似于 。 `resource` 這在遷移時非常有用，以確保維護一組類似的作用域和使用者體驗。

要使用`/.default`作用域，請使用資源`/.default`識別碼追加。 例如：`https://graph.microsoft.com/.default`。 如果資源以斜杠 （）`/`結尾，則仍應追加`/.default`，包括前導斜杠，從而導致具有雙正向斜杠 （）`//`的範圍。

您可以[在此處](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)閱讀有關使用"/.default"作用域的詳細資訊

### <a name="supporting-different-webview-types--browsers"></a>支援不同的 WebView 類型&瀏覽器

ADAL 僅支援適用于 iOS 的 UIWebView/WKWebView，支援 macOS 的 WebView。 iOS 的 MSAL 支援更多選項，用於在請求授權代碼時顯示 Web 內容，`UIWebView`並且不再支援 。可以改善使用者體驗和安全性。

預設情況下，iOS 上的 MSAL 使用[ASWeb 身份驗證會話](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)，這是 Apple 建議在 iOS 12+ 設備上進行身份驗證的 Web 元件。 它通過應用程式和 Safari 瀏覽器之間的 Cookie 共用提供單一登入 （SSO） 權益。

您可以選擇使用不同的 Web 元件，具體取決於應用要求和所需的最終使用者體驗。 有關詳細資訊，請參閱[支援的 Web 檢視類型](customize-webviews.md)。

從 ADAL 遷移到 MSAL`WKWebView`時，在 iOS 和 macOS 上提供與 ADAL 最相似的使用者體驗。 如果可能，我們鼓勵您遷移到`ASWebAuthenticationSession`iOS 上。 對於 macOS，我們鼓勵您使用`WKWebView`。

### <a name="account-management-api-differences"></a>帳戶管理 API 差異

調用 ADAL`acquireToken()`方法或`acquireTokenSilent()`時，您會`ADUserInformation`收到一個物件`id_token`，其中包含來自 的聲明清單，該聲明來自 表示正在身份驗證的帳戶的聲明。 此外，`ADUserInformation`返回基於`userId`聲明的`upn`。 在初始互動式權杖獲取後，ADAL 希望開發人員`userId`在所有靜默調用中提供。

ADAL 不提供用於檢索已知使用者標識的 API。 它依賴于應用程式來保存和管理這些帳戶。

MSAL 提供了一組 API，用於列出 MSAL 已知的所有帳戶，而無需獲取權杖。

與 ADAL 一樣，MSAL 返回包含 聲明清單的`id_token`帳戶資訊。 它是物件內`MSALAccount`物件的一`MSALResult`部分。

MSAL 提供了一組 API 來刪除帳戶，使應用無法訪問已刪除的帳戶。 刪除帳戶後，以後的權杖獲取調用將提示使用者執行互動式權杖獲取。 帳戶刪除僅適用于啟動帳戶的用戶端應用程式，並且不會從設備上運行的其他應用程式或系統瀏覽器中刪除帳戶。 這可確保使用者即使在登出單個應用後，也能在設備上繼續具有 SSO 體驗。

此外，MSAL 還會返回一個帳戶識別碼，該識別碼可用於以後靜默請求權杖。 但是，帳戶識別碼（通過`identifier``MSALAccount`物件中的屬性訪問）不可顯示，您無法假定它採用何種格式，也不應嘗試解釋或解析它。

### <a name="migrating-the-account-cache"></a>遷移帳戶緩存

從 ADAL 遷移時，應用通常會存儲 ADAL`userId`的 ，而 ADAL 沒有 MSAL`identifier`的要求。 作為一次性遷移步驟，應用可以使用 ADAL 的使用者 Id 使用以下 API 查詢 MSAL 帳戶：

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

此 API 讀取 MSAL 和 ADAL 的緩存，以便通過 ADAL 使用者 Id （UPN） 查找帳戶。

如果找到該帳戶，開發人員應使用該帳戶執行靜默權杖獲取。 第一次靜默權杖獲取將有效地升級帳戶，開發人員將在 MSAL 結果 （） 中獲取 MSAL`identifier`相容的帳戶識別碼 。 之後，僅`identifier`應使用以下 API 進行帳戶查找：

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

儘管可以繼續`userId`使用 ADAL 用於 MSAL 中的所有操作，但由於`userId`基於 UPN，因此會受到多種限制，從而導致使用者體驗不佳。 例如，如果 UPN 發生更改，則使用者必須重新登錄。 我們建議所有應用對所有操作使用不可顯示帳戶`identifier`。

閱讀有關[緩存狀態遷移](sso-between-adal-msal-apps-macos-ios.md)的詳細資訊。

### <a name="token-acquisition-changes"></a>權杖獲取更改

MSAL 引入了一些權杖獲取調用更改：

* 與 ADAL`acquireTokenSilent`一樣，始終會導致靜默請求。
* 與 ADAL`acquireToken`不同，始終通過 Web 視圖或 Microsoft 身份驗證器應用生成使用者可操作的 UI。 根據 Webview/Microsoft 身份驗證器內的 SSO 狀態，可能會提示使用者輸入其憑據。
* 在 ADAL `acquireToken` `AD_PROMPT_AUTO`中，首先嘗試靜默權杖獲取，並且僅在靜默請求失敗時顯示 UI。 在 MSAL 中，此邏輯可以通過首次調用`acquireTokenSilent`來實現，並且`acquireToken`僅在靜默獲取失敗時才調用來實現。 這允許開發人員在開始互動式權杖獲取之前自訂使用者體驗。

### <a name="error-handling-differences"></a>錯誤處理差異

MSAL 在應用可以處理的錯誤和需要使用者干預的錯誤之間提供了更清晰的清晰度。 開發人員必須處理的數量有限的錯誤：

* `MSALErrorInteractionRequired`：使用者必須執行互動式請求。 這可能是由於各種原因造成的，例如身份驗證會話過期、條件訪問策略已更改、刷新權杖過期或已吊銷、緩存中沒有有效權杖等。
* `MSALErrorServerDeclinedScopes`：請求未完全完成，並且某些作用域未被授予存取權限。 這可能是由使用者拒絕同意一個或多個作用域引起的。

處理[`MSALError`清單中](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)的所有其他錯誤是可選的。 您可以使用這些錯誤中的資訊來改善使用者體驗。

有關 MSAL 錯誤[處理的詳細資訊，請參閱使用 MSAL 處理異常和錯誤](msal-handling-exceptions.md)。

### <a name="broker-support"></a>經紀商支援

MSAL 從版本 0.3.0 開始，使用 Microsoft 身份驗證器應用支援代理身份驗證。 Microsoft 身份驗證器還支援條件訪問方案。 條件訪問方案的示例包括設備合規性策略，這些策略要求使用者通過 Intune 註冊設備或向 AAD 註冊以獲得權杖。 和移動應用程式管理 （MAM） 條件訪問策略，這需要驗證合規性，然後才能獲取權杖。

要為應用程式啟用代理：

1. 註冊應用程式的代理相容重定向 URI 格式。 代理相容重定向 URI 格式為`msauth.<app.bundle.id>://auth`。 替換為`<app.bundle.id>`應用程式的捆綁 ID。 如果您從 ADAL 遷移，並且您的應用程式已經具備代理功能，則無需執行任何額外操作。 您以前的重定向 URI 與 MSAL 完全相容，因此您可以跳到步驟 3。

2. 將應用程式的重定向 URI 方案添加到 info.plist 檔。 對於預設的 MSAL 重定向 URI，格式`msauth.<app.bundle.id>`為 。 例如：

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. 在 LS應用程式查詢計劃下向應用的 Info.plist 添加以下方案：

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. 將以下內容添加到 AppDelegate.m 檔以處理回檔：目標 C：
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift：
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>企業對企業 (B2B)

在 ADAL 中，您可以`ADAuthenticationContext`為每個應用為其請求權杖的租戶創建單獨的實例。 這不再是 MSAL 中的要求。 在 MSAL 中，您可以通過指定用於獲取`MSALPublicClientApplication`Token和獲取TokenSilent調用的不同許可權來創建 的 單個實例並將其用於任何 AAD 雲和組織。

## <a name="sso-in-partnership-with-other-sdks"></a>SSO 與其他 SDK 合作

適用于 iOS 的 MSAL 可通過具有以下 SDK 的統一緩存實現 SSO：

- ADAL 目標 C 2.7.x*
- MSAL.NET Xamarin 2.4.x*
- Xamarin 4.4.x+ 的ADAL.NET

SSO 是通過 iOS 鑰匙串共用實現的，並且僅在從同一 Apple 開發人員帳戶發佈的應用之間可用。

通過 iOS 鑰匙串共用的 SSO 是唯一的靜音 SSO 類型。

在 macOS 上，MSAL 可以與其他 MSAL 一起實現基於 iOS 和 macOS 的應用程式以及基於 ADAL 目標 C 的應用程式的 SSO。

iOS 上的 MSAL 還支援兩種其他類型的 SSO：

* 通過 Web 瀏覽器進行 SSO。 適用于 iOS 的`ASWebAuthenticationSession`MSAL 支援 ，它通過設備上其他應用之間共用的 Cookie 提供 SSO，特別是 Safari 瀏覽器。
* 通過身份驗證代理進行 SSO。 在 iOS 設備上，Microsoft 身份驗證器充當身份驗證代理。 它可以遵循條件訪問策略（如要求相容的設備），並為註冊設備提供 SSO。 預設情況下，以版本 0.3.0 開頭的 MSAL SDK 支援代理。

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started)支援從版本[11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)開始的 iOS 的 MSAL

## <a name="msal-and-adal-in-the-same-app"></a>MSAL 和 ADAL 在同一應用程式中

ADAL 版本 2.7.0 及以上，不能在同一應用程式中與 MSAL 共存。 主要原因是共用子模組通用代碼。 由於 Objective-C 不支援命名空間，因此，如果將 ADAL 和 MSAL 框架添加到應用程式中，則同一類將有兩個實例。 不能保證在運行時選擇哪一個。 如果兩個 SDK 都使用衝突類的相同版本，則你的應用可能仍然有效。 但是，如果是其他版本，則應用可能會遇到難以診斷的意外崩潰。

不支援在同一生產應用程式中運行 ADAL 和 MSAL。 但是，如果您只是測試和遷移您的使用者從ADAL目標C到MSAL的iOS和macOS，你可以繼續使用[ADAL目標C2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)。 它是在同一應用程式中與 MSAL 配合使用的唯一版本。 此 ADAL 版本不會有新功能更新，因此應僅用於遷移和測試目的。 你的應用不應長期依賴 ADAL 和 MSAL 共存。

不支援同一應用程式中的 ADAL 和 MSAL 共存。
完全支援多個應用程式之間的 ADAL 和 MSAL 共存。

## <a name="practical-migration-steps"></a>實際遷移步驟

### <a name="app-registration-migration"></a>應用註冊遷移

您無需更改現有的 AAD 應用程式才能切換到 MSAL 並啟用 AAD 帳戶。 但是，如果基於 ADAL 的應用程式不支援代理身份驗證，則需要為應用程式註冊新的重定向 URI，然後才能切換到 MSAL。

重定向 URI 應採用此格式： `msauth.<app.bundle.id>://auth`。 替換為`<app.bundle.id>`應用程式的捆綁 ID。 在[Azure 門戶](https://aka.ms/MobileAppReg)中指定重定向 URI。

對於僅 iOS，要支援基於證書的身份驗證，需要在應用程式和 Azure 門戶中註冊其他重定向 URI，其格式如下： `msauth://code/<broker-redirect-uri-in-url-encoded-form>`。 例如， `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

我們建議所有應用同時註冊重定向 URI。

如果要添加對增量同意的支援，請選擇應用配置為在**API 許可權**選項卡下的應用註冊中請求訪問的許可權。

如果要從 ADAL 遷移，並且希望同時支援 AAD 和 MSA 帳戶，則需要更新現有應用程式註冊以支援這兩個帳戶。 我們不建議您立即更新現有生產應用以支援 AAD 和 MSA。 相反，請創建另一個同時支援 AAD 和 MSA 進行測試的用戶端 ID，並在驗證所有方案是否正常工作後更新現有應用。

### <a name="add-msal-to-your-app"></a>將 MSAL 添加到應用

您可以使用首選的包管理工具將 MSAL SDK 添加到應用。 [在此處查看詳細說明](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)。

### <a name="update-your-apps-infoplist-file"></a>更新應用的資訊.plist 檔

僅對於 iOS，將應用程式的重定向 URI 方案添加到 info.plist 檔中。 對於 ADAL 代理相容的應用程式，它應該已經存在。 預設的 MSAL 重定向 URI 方案將以以下格式`msauth.<app.bundle.id>`進行： 。  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

在 中添加以下方案到應用的 Info.plist。 `LSApplicationQueriesSchemes`

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>更新應用授權代碼

僅對於 iOS，請向 AppDelegate.m 檔添加以下內容：

Objective-C：

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift：

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**如果使用 Xcode 11**，則應將 MSAL 回檔放在`SceneDelegate`檔中。
如果您同時支援 UISceneDelegate 和 UIApplicationDelegate 以便與舊版 iOS 相容，則必須將 MSAL 回呼放入這兩個檔案。

Objective-C：

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift：

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

這允許 MSAL 處理來自代理和 Web 元件的回應。
這在 ADAL 中是不必要的，因為它會自動"旋轉"應用委託方法。 手動添加它不太容易出錯，並且為應用程式提供了更多的控制。

### <a name="enable-token-caching"></a>啟用權杖快取

根據預設，MSAL 會快取 iOS 或 macOS 金鑰鏈中的應用程式權杖。 

啟用權杖快取：
1. 確定您的應用程式已正確簽署
2. 轉到 Xcode 專案設置>**功能"選項卡** > **啟用鑰匙串共用**
3. 按一下**+** 並輸入以下**鑰匙串組**條目：3.a 對於 iOS，`com.microsoft.adalcache`輸入 3.b 對於 macOS 輸入`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>創建 MSAL 公共用戶端應用程式並切換到其獲取權杖並獲取 Toke 靜音呼叫

您可以使用以下代碼`MSALPublicClientApplication`進行創建：

Objective-C：

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift：

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

然後調用帳戶管理 API 以查看緩存中是否有任何帳戶：

Objective-C：

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift：

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



或閱讀所有帳戶：

Objective-C：

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift：

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



如果找到帳戶，請致電 MSAL `acquireTokenSilent` API：

Objective-C：

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift：

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
