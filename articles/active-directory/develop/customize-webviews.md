---
title: 自訂瀏覽器 & 網站（MSAL iOS/macOS） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何自訂 MSAL iOS/macOS 瀏覽器體驗來登入使用者。
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
ms.openlocfilehash: 759f61860c62bcb668db6844df28c52fa28eac80
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085916"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>How to：自訂 iOS/macOS 的瀏覽器和網站

互動式驗證需要 web 瀏覽器。 在 iOS 上，Microsoft 驗證程式庫（MSAL）預設會使用系統 web 瀏覽器（可能會出現在您的應用程式頂端），以進行互動式驗證以登入使用者。 使用 [系統瀏覽器] 的優點是可以與其他應用程式和 web 應用程式共用單一登入（SSO）狀態。

您可以將設定自訂為其他顯示 web 內容的選項來變更體驗，例如：

僅適用于 iOS：

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

針對 iOS 和 macOS：

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc)。

MSAL for macOS 僅支援 `WKWebView`。

## <a name="system-browsers"></a>系統瀏覽器

針對 iOS，`ASWebAuthenticationSession`、`SFAuthenticationSession`和 `SFSafariViewController` 視為系統瀏覽器。 一般而言，系統瀏覽器會與 Safari 瀏覽器應用程式共用 cookie 和其他網站資料。

根據預設，MSAL 會動態偵測 iOS 版本，並選取該版本上所提供的建議系統瀏覽器。 在 iOS 12 上，將會 `ASWebAuthenticationSession`。 

| 版本 | Web 瀏覽器 |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

開發人員也可以針對 MSAL 應用程式選取不同的系統瀏覽器：

- `SFAuthenticationSession` 是 `ASWebAuthenticationSession`的 iOS 11 版本。
- `SFSafariViewController` 是較一般的用途，並提供用於流覽 web 的介面，也可用於登入目的。 在 iOS 9 和10中，cookie 和其他網站資料會與 Safari 共用，但不會在 iOS 11 和更新版本中。

## <a name="in-app-browser"></a>應用程式內瀏覽器

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)是顯示 web 內容的應用程式內瀏覽器。 它不會與其他**WKWebView**實例或使用 Safari 瀏覽器共用 cookie 或網站資料。 WKWebView 是適用于 iOS 和 macOS 的跨平臺瀏覽器。

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Cookie 共用和單一登入（SSO）的含意

您使用的瀏覽器會影響 SSO 體驗，因為它們共用 cookie 的方式。 下表摘要說明每個瀏覽器的 SSO 體驗。

| 技術    | 瀏覽器類型  | iOS 可用性 | macOS 可用性 | 共用 cookie 和其他資料  | MSAL 可用性 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | 系統 | iOS12 和 up | macOS 10.15 及更新的 | 是 | 僅限 iOS | w/Safari 實例
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | 系統 | iOS11 和 up | N/A | 是 | 僅限 iOS |  w/Safari 實例
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | 系統 | iOS11 和 up | N/A | 否 | 僅限 iOS | 否**
| **SFSafariViewController** | 系統 | iOS10 | N/A | 是 | 僅限 iOS |  w/Safari 實例
| **WKWebView**  | 應用程式內 | iOS8 和 up | macOS 10.10 及更新的 | 否 | iOS 和 macOS | 否**

\* * 若要讓 SSO 正常執行，必須在應用程式之間共用權杖。 這需要權杖快取或 broker 應用程式，例如適用于 iOS 的 Microsoft Authenticator。

## <a name="change-the-default-browser-for-the-request"></a>變更要求的預設瀏覽器

您可以在 `MSALWebviewParameters`中變更下列屬性，以根據您的 UX 需求使用應用程式內瀏覽器或特定的系統瀏覽器：

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>變更每個互動式要求

您可以設定每個要求，藉由變更 `MSALInteractiveTokenParameters.webviewParameters.webviewType` 屬性來覆寫預設瀏覽器，然後再將它傳遞給 `acquireTokenWithParameters:completionBlock:` API。

此外，MSAL 支援藉由設定 `MSALInteractiveTokenParameters.webviewParameters.customWebView` 屬性來傳入自訂 `WKWebView`。

例如：

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

如果您使用自訂的 web 工作，則會使用通知來指出所顯示的網頁內容狀態，例如：

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>選項。

所有 MSAL 支援的網頁瀏覽器類型都會在[MSALWebviewType 列舉](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)中宣告

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
