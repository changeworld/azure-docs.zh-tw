---
title: 在 macOS 和 iOS 上配置 SSO
titleSuffix: Microsoft identity platform
description: 瞭解如何在 macOS 和 iOS 上配置單一登錄 (SSO)。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881243"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>如何:在 macOS 和 iOS 上配置 SSO

適用於 macOS 和 iOS 的 Microsoft 身份驗證庫 (MSAL) 支援 macOS/iOS 應用和瀏覽器之間的單一登錄 (SSO)。 本文介紹以下 SSO 方案:

- [多個應用程式之間的靜音 SSO](#silent-sso-between-apps)

這種類型的 SSO 適用於同一 Apple 開發人員分發的多個應用程式。 它通過讀取由鑰匙串中其他應用編寫的刷新權杖,並靜默地交換這些權杖,從而提供靜默 SSO(即不會提示用戶獲取憑據)。  

- [透過認證代理程式進行 SSO](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> 此流在 macOS 上不可用。

Microsoft 提供稱為代理的應用,只要行動裝置註冊到 Azure 活動目錄 (AAD),可在來自不同供應商的應用程式之間啟用 SSO。 這種類型的 SSO 要求在使用者的設備上安裝代理應用程式。

- **MSAL 和 Safari 之間的 SSO**

SSO 是透過[ASWeb 身份驗證會話](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)類實現的。 它使用來自其他應用和 Safari 瀏覽器的現有登錄狀態。 它並不限於由同一 Apple 開發人員分發的應用,但它需要一些使用者交互。

如果您在應用中使用預設 Web 檢視登錄使用者,則在基於 MSAL 的應用程式和 Safari 之間將自動獲取 SSO。 要瞭解有關 MSAL 支援的 Web 檢視的更多內容,請造[訪自訂瀏覽器和 WebViews](customize-webviews.md)。

> [!IMPORTANT]
> 這種類型的 SSO 目前不在 macOS 上。 macOS 上的 MSAL 僅支援 WKWebView,而 WKWebView 對 Safari 沒有 SSO 支援。 

- **ADAL 和 MSAL macOS/iOS 應用程式之間的靜音 SSO**

MSAL 目標 C 支援基於 ADAL 目標 C 的應用的遷移和 SSO。 應用必須由同一 Apple 開發人員分發。

有關 ADAL 和基於 MSAL 的應用之間的跨應用 SSO 的說明,請參閱[macOS 和 iOS 上的 ADAL 和 MSAL 應用之間的 SSO。](sso-between-adal-msal-apps-macos-ios.md)

## <a name="silent-sso-between-apps"></a>套用之間的靜音 SSO

MSAL 支援透過 iOS 鑰匙串存取組進行 SSO 共用。

要跨應用程式啟用 SSO,您需要執行以下步驟,下面將對此進行更詳細的說明:

1. 確保所有應用程式使用相同的用戶端 ID 或應用程式 ID。
1. 請確定您所有的應用程式共用來自 Apple 的相同簽署憑證，以便您可以共用金鑰鏈。
1. 要求每個應用程式的相同金鑰鏈權利。
1. 如果共享鑰匙串與預設鑰匙串不同,請告訴 MSAL SDK 您希望我們使用的共享鑰匙串。

### <a name="use-the-same-client-id-and-application-id"></a>使用相同的客戶端 ID 和應用程式識別碼

對於 Microsoft 標識平臺要知道哪些應用程式可以共用權杖,這些應用程式需要共用相同的用戶端 ID 或應用程式 ID。 這是您在入口網站中註冊第一個應用程式時提供給您的唯一識別碼。

Microsoft 標識平台告訴應用程式,除了使用相同的應用程式 ID,其重定向 URI**的方式**是。 每個應用程式可以在上架的入口網站中註冊多個重新導向 URI。 組件中的每個應用程式將會有不同的重新導向 URI。 例如：

App1 重新導向 URI： `msauth.com.contoso.mytestapp1://auth`  
App2 重新導向 URI： `msauth.com.contoso.mytestapp2://auth`  
App3 重新導向 URI： `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> 重定向 URI 的格式必須與 MSAL 支援的格式相容,該格式在[MSAL 重定向 URI 格式要求](redirect-uris-ios.md#msal-redirect-uri-format-requirements)中記錄。

### <a name="setup-keychain-sharing-between-applications"></a>設定應用程式之間的鑰匙串共用

請參閱 Apple 的[「添加功能」](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)一文,實現鑰匙串共用。 重要的是,您決定要調用什麼鑰匙串,並將該功能添加到 SSO 中涉及的所有應用程式中。

正確設定權限後,您將在項目目錄中看到`entitlements.plist`包含 類似範例的檔案:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>新增鑰匙序列

新增專案**功能**新增新的鍵串組。 鑰匙串組應為:
* `com.microsoft.adalcache`在 iOS 上 
* `com.microsoft.identity.universalstorage`在macOS上。

![鑰匙串範例](media/single-sign-on-macos-ios/keychain-example.png)

有關詳細資訊,請參閱[鑰匙串組](howto-v2-keychain-objc.md)。

## <a name="configure-the-application-object"></a>設定應用程式物件

在每個應用程式中啟用了鑰匙串授權,並且可以使用 SSO 後,請使用鑰匙串存取`MSALPublicClientApplication`組進行配置 ,如以下範例所示:

Objective-C：

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift：

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> 當您跨應用程式共用鑰匙串時,任何應用程式都可以刪除整個應用程式的使用者甚至所有令牌。
> 如果您的應用程式依賴於令牌執行後台工作,則此功能尤其影響。
> 共用鑰匙串意味著,當應用使用 Microsoft 標識 SDK 刪除操作時,必須非常小心。

就這麼簡單！ Microsoft 標識 SDK 現在將在所有應用程式中共用憑據。 帳戶清單也將跨應用程式實例共用。

## <a name="sso-through-authentication-broker-on-ios"></a>透過 iOS 上的身份驗證代理程式進行 SSO

MSAL 為使用 Microsoft 身份驗證器提供代理身份驗證的支援。 Microsoft 身份驗證器為 AAD 註冊設備提供 SSO,還可説明您的應用程式遵循條件存取策略。

以下步驟是如何使用應用程式的身分驗證代理啟用 SSO:

1. 在應用的 Info.plist 中註冊應用程式的代理相容重定向 URI 格式。 代理相容重定到 URI`msauth.<app.bundle.id>://auth`格式為 。 將"<app.bundle.id>"替換為應用程式的捆綁 ID。 例如：

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. 在以下項目下`LSApplicationQueriesSchemes`向應用程式的 Info.plist 加入以下專案:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. 將以下內容加入檔案中`AppDelegate.m`以處理回檔:

    Objective-C：
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
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
    
**如果使用 Xcode 11**,則應將`SceneDelegate`MSAL 回調放在 檔案中。
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

## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)