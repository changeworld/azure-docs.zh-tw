---
title: 搭配使用重新導向 URI 與 MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: 了解適用於 ObjectiveC 的 Microsoft 驗證程式庫 (適用於 iOS 和 macOS 的 MSAL) 與適用於 ObjectiveC 的 Azure AD 驗證程式庫 (ADAL.ObjC) 之間的差異，以及如何在兩者之間遷移。
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: c36c6b1b1b08de6d2db9a7f7f9ebd3b162c02383
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79215886"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>使用重新導向 URI 搭配適用於 iOS 和 macOS 的 Microsoft 驗證程式庫

當使用者進行驗證時，Azure Active Directory (Azure AD) 會使用向 Azure AD 應用程式註冊的重新導向 URI，將權杖傳送至應用程式。

Microsoft 驗證程式庫 (MSAL) 要求以特定格式向 Azure AD 應用程式註冊重新導向 URI。 如果您未指定重新導向 URI，MSAL 會使用預設值。 格式為 `msauth.[Your_Bundle_Id]://auth`。

預設重新導向 URI 格式適用於大部分的應用程式和案例，包括代理驗證和系統網頁檢視。 可能的話，請使用預設格式。

不過，您可能需要變更進階案例的重新導向 URI，如下所述。

## <a name="scenarios-that-require-a-different-redirect-uri"></a>需要不同重新導向 URI 的案例

### <a name="cross-app-single-sign-on-sso"></a>跨應用程式單一登入 (SSO)

為了讓 Microsoft 身分識別平台能跨應用程式共用權杖，每個應用程式都必須有相同的用戶端識別碼或應用程式識別碼。 這是您在入口網站中註冊應用程式時所提供的唯一識別碼 (而不是您依應用程式向 Apple 註冊的應用程式套件組合識別碼)。

每個 iOS 應用程式的重新導向 URI 必須不同。 這可讓 Microsoft 識別服務唯一識別共用應用程式識別碼的不同應用程式。 每個應用程式可以在 Azure 入口網站中註冊多個重新導向 URI。 組件中的每個應用程式將會有不同的重新導向 URI。 例如：

假設在 Azure 入口網站中進行下列應用程式註冊：

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 使用重新導向 `msauth.com.contoso.app1://auth` App2 使用 `msauth.com.contoso.app2://auth` App3 使用 `msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>從 ADAL 遷移至 MSAL

將使用 Azure AD 驗證程式庫 (ADAL) 的程式碼遷移至 MSAL 時，您可能已經有針對應用程式設定的重新導向 URI。 只要您的 ADAL 應用程式已設定為支援代理案例，且您的重新導向 URI 符合 MSAL 重新導向 URI 格式需求，您就可以繼續使用相同的重新導向 URI。

## <a name="msal-redirect-uri-format-requirements"></a>MSAL 重新導向 URI 格式需求

* MSAL 重新導向 URI 必須是 `<scheme>://host` 格式

    其中 `<scheme>` 是可識別您應用程式的唯一字串。 其主要是根據應用程式的套件組合識別碼來保證唯一性。 例如，如果您的應用程式套件組合識別碼為 `com.contoso.myapp`，則重新導向 URI 的格式會是：`msauth.com.contoso.myapp://auth`。

    如果您從 ADAL 進行遷移，則重新導向 URI 的格式可能會是：`<scheme>://[Your_Bundle_Id]`，其中 `scheme` 是唯一的字串。 當您使用 MSAL 時，此格式會繼續作用。

* `<scheme>` 必須登錄於您應用程式的 Info.plist 中 (在 `CFBundleURLTypes > CFBundleURLSchemes` 之下)。  在此範例中，Info. plist 已開啟為原始程式碼：

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

MSAL 會驗證您的重新導向 URI 是否正確註冊，如果為否，則會傳回錯誤。
    
* 如果您想使用通用連結做為重新導向 URI，`<scheme>` 必須是 `https`，而且不需要在 `CFBundleURLSchemes` 中進行宣告。 然而，請依照 Apple 的指示在[適用於開發人員的通用連結](https://developer.apple.com/ios/universal-links/)設定應用程式和網域，並且在您的應用程式透過通用連結開啟時，呼叫 `handleMSALResponse:sourceApplication:` 的 `MSALPublicClientApplication` 方法。

## <a name="use-a-custom-redirect-uri"></a>使用自訂重新導向 URI

若要使用自訂重新導向 URI，請將 `redirectUri` 參數傳遞至 `MSALPublicClientApplicationConfig`，並在初始化物件時將該物件傳遞至 `MSALPublicClientApplication`。 如果重新導向 URI 無效，則初始設定式會傳回 `nil` 並以其他資訊設定 `redirectURIError`。  例如：

Objective-C：

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift：

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>處理 URL 開啟的事件

當您的應用程式透過 URL 配置或通用連結接收任何回應時，其應該呼叫 MSAL。 當您的應用程式開啟時，呼叫 `handleMSALResponse:sourceApplication:` 的 `MSALPublicClientApplication` 方法。 自訂配置的範例如下︰

Objective-C：

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift：

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
