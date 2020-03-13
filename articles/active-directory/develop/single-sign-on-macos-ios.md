---
title: 在 macOS 和 iOS 上設定 SSO
titleSuffix: Microsoft identity platform
description: 瞭解如何在 macOS 和 iOS 上設定單一登入（SSO）。
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 91a55520b37c549c8f1d94ba6cf08ecd24db85b5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262447"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>如何：在 macOS 和 iOS 上設定 SSO

適用于 macOS 和 iOS 的 Microsoft 驗證程式庫（MSAL）支援 macOS/iOS 應用程式與瀏覽器之間的單一登入（SSO）。 本文涵蓋下列 SSO 案例：

- [多個應用程式之間的無訊息 SSO](#silent-sso-between-apps)

這種類型的 SSO 適用于由相同的 Apple 開發人員散發的多個應用程式。 它提供無訊息 SSO （也就是不會提示使用者輸入認證），方法是讀取 keychain 中其他應用程式所寫入的重新整理權杖，並以無訊息方式交換存取權杖。  

- [透過驗證訊息代理程式的 SSO](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> 此流程無法在 macOS 上使用。

Microsoft 提供的應用程式稱為「訊息代理程式」，只要使用 Azure Active Directory （AAD）註冊行動裝置，就能在不同廠商的應用程式之間啟用 SSO。 這種類型的 SSO 需要在使用者的裝置上安裝 broker 應用程式。

- **MSAL 與 Safari 之間的 SSO**

SSO 是透過[ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)類別來達成。 它會使用來自其他應用程式和 Safari 瀏覽器的現有登入狀態。 它不限於相同的 Apple 開發人員所散發的應用程式，但需要一些使用者互動。

如果您使用應用程式中的預設 web view 來登入使用者，您會在 MSAL 架構的應用程式與 Safari 之間取得自動 SSO。 若要深入瞭解 MSAL 支援的 web views，請造訪[自訂瀏覽器和網站](customize-webviews.md)。

> [!IMPORTANT]
> 此類型的 SSO 目前無法在 macOS 上使用。 MSAL on macOS 僅支援不具有 Safari 的 SSO 支援的 WKWebView。 

- **ADAL 與 MSAL macOS/iOS 應用程式之間的無訊息 SSO**

MSAL 目標-C 支援以 ADAL 目標-C 為基礎的應用程式進行遷移和 SSO。 應用程式必須由相同的 Apple 開發人員散發。

如需 ADAL 與 MSAL 型應用程式之間的跨應用程式 SSO 的指示，請參閱[macOS 和 iOS 上的 adal 與 MSAL 應用程式之間的 sso](sso-between-adal-msal-apps-macos-ios.md) 。

## <a name="silent-sso-between-apps"></a>應用程式之間的無訊息 SSO

MSAL 支援透過 iOS keychain 存取群組進行 SSO 共用。

若要在您的應用程式中啟用 SSO，您必須執行下列步驟，如下所示詳細說明：

1. 請確定您的所有應用程式都使用相同的用戶端識別碼或應用程式識別碼。
1. 請確定您所有的應用程式共用來自 Apple 的相同簽署憑證，以便您可以共用金鑰鏈。
1. 要求每個應用程式的相同金鑰鏈權利。
1. 告訴 MSAL Sdk 您想要我們使用的共用 keychain （如果它與預設值不同的話）。

### <a name="use-the-same-client-id-and-application-id"></a>使用相同的用戶端識別碼和應用程式識別碼

若要讓 Microsoft 身分識別平臺知道哪些應用程式可以共用權杖，那些應用程式必須共用相同的用戶端識別碼或應用程式識別碼。 這是您在入口網站中註冊第一個應用程式時提供給您的唯一識別碼。

Microsoft 身分識別平臺會告訴使用相同應用程式識別碼的應用程式，其方式就是重新**導向 uri**。 每個應用程式可以在上架的入口網站中註冊多個重新導向 URI。 組件中的每個應用程式將會有不同的重新導向 URI。 例如：

App1 重新導向 URI： `msauth.com.contoso.mytestapp1://auth`  
App2 重新導向 URI： `msauth.com.contoso.mytestapp2://auth`  
App3 重新導向 URI： `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> 重新導向 uri 的格式必須與 MSAL 支援的格式相容，如[MSAL 重新導向 URI 格式需求](redirect-uris-ios.md#msal-redirect-uri-format-requirements)中所述。

### <a name="setup-keychain-sharing-between-applications"></a>設定應用程式之間的 keychain 共用

請參閱 Apple 的[新增功能](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)文章，以啟用 keychain 共用。 重要的是，您決定要呼叫 keychain 的內容，並將該功能新增至所有將包含在 SSO 中的應用程式。

當您正確設定權利時，您會在專案目錄中看到 `entitlements.plist` 檔案，其中包含如下列範例所示的內容：

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

#### <a name="add-a-new-keychain-group"></a>新增 keychain 群組

將新的 keychain 群組新增至您的專案**功能**。 Keychain 群組應該是：
* iOS 上的 `com.microsoft.adalcache` 
* 在 macOS 上 `com.microsoft.identity.universalstorage`。

![keychain 範例](media/single-sign-on-macos-ios/keychain-example.png)

如需詳細資訊，請參閱[keychain groups](howto-v2-keychain-objc.md)。

## <a name="configure-the-application-object"></a>設定應用程式物件

一旦您已在每個應用程式中啟用 keychain 權利，而且您已準備好使用 SSO，請使用您的 keychain 存取群組來設定 `MSALPublicClientApplication`，如下列範例所示：

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
> 當您跨應用程式共用 keychain 時，任何應用程式都可以刪除使用者，或甚至是整個應用程式的所有權杖。
> 如果您的應用程式依賴權杖來執行背景工作，這就特別影響力。
> 共用 keychain 表示當您的應用程式使用 Microsoft 身分識別 SDK 移除作業時，您必須非常小心。

就這麼簡單！ Microsoft 身分識別 SDK 現在將會在您所有的應用程式之間共用認證。 帳戶清單也會在應用程式實例之間共用。

## <a name="sso-through-authentication-broker-on-ios"></a>透過 iOS 上的驗證代理程式進行 SSO

MSAL 可支援 Microsoft Authenticator 的代理驗證。 Microsoft Authenticator 為 AAD 註冊的裝置提供 SSO，同時也可協助您的應用程式遵循條件式存取原則。

下列步驟說明如何使用您應用程式的驗證代理人來啟用 SSO：

1. 在應用程式的資訊中，為應用程式註冊 broker 相容的重新導向 URI 格式。 plist。 Broker 相容的重新導向 URI 格式為 `msauth.<app.bundle.id>://auth`。 以您應用程式的套件組合識別碼取代 ' < app.config. id > ' '。 例如：

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. 將下列配置新增至您應用程式的資訊. plist 底下的 `LSApplicationQueriesSchemes`：

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. 將下列內容新增至您的 `AppDelegate.m` 檔案以處理回呼：

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
    
**如果您使用的是 Xcode 11**，則應該改為將 MSAL 回呼放入 `SceneDelegate` 檔案。
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