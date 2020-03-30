---
title: 自訂瀏覽器& WebViews （MSAL iOS/macOS） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何自訂 MSAL iOS/macOS 瀏覽器體驗以登錄使用者。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085916"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>如何：為 iOS/macOS 自訂瀏覽器和 Web 視圖

互動式身份驗證需要 Web 瀏覽器。 在 iOS 上，Microsoft 身份驗證庫 （MSAL） 預設使用系統 Web 瀏覽器（可能顯示在應用頂部）執行互動式身份驗證以登錄使用者。 使用系統瀏覽器的優點是與其他應用程式和 Web 應用程式共用單一登入 （SSO） 狀態。

您可以通過將配置自訂為用於顯示 Web 內容的其他選項來更改體驗，例如：

僅適用于 iOS：

- [ASWeb 身份驗證會話](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SF 認證會話](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariView控制器](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

對於 iOS 和 macOS：

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

macOS 的 MSAL`WKWebView`僅支援 。

## <a name="system-browsers"></a>系統瀏覽器

對於`ASWebAuthenticationSession`iOS， `SFAuthenticationSession`，`SFSafariViewController`和 被視為系統瀏覽器。 通常，系統瀏覽器與 Safari 瀏覽器應用程式共用 Cookie 和其他網站資料。

預設情況下，MSAL 將動態檢測 iOS 版本，並選擇該版本中可用的系統瀏覽器。 在 iOS 12+`ASWebAuthenticationSession`上，它將是 。 

| 版本 | Web 瀏覽器 |
|:-------------:|:-------------:|
| iOS 12+ | ASWeb 身份驗證會話 |
| iOS 11 | SF 認證會話 |
| iOS 10 | SFSafariView控制器 |

開發人員還可以為 MSAL 應用選擇不同的系統瀏覽器：

- `SFAuthenticationSession`是 iOS 11`ASWebAuthenticationSession`版本的 。
- `SFSafariViewController`更通用，並提供一個介面流覽網頁，也可用於登錄目的。 在 iOS 9 和 10 中，Cookie 和其他網站資料與 Safari 共用，但不會在 iOS 11 及更高版本中共用。

## <a name="in-app-browser"></a>應用內瀏覽器

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)是一種顯示 Web 內容的應用內瀏覽器。 它不與其他**WKWebView**實例或 Safari 瀏覽器共用 Cookie 或網站資料。 WKWebView 是一種跨平臺瀏覽器，適用于 iOS 和 macOS。

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Cookie 共用和單一登入 （SSO） 含義

您使用的瀏覽器會影響 SSO 體驗，因為它們如何共用 Cookie。 下表總結了每個瀏覽器的 SSO 體驗。

| 技術    | 瀏覽器類型  | iOS 可用性 | macOS 可用性 | 共用 Cookie 和其他資料  | MSAL 可用性 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWeb 身份驗證會話](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | 系統 | iOS12 及上 | macOS 10.15 及上 | 是 | 僅限 iOS | w/ Safari 實例
| [SF 認證會話](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | 系統 | iOS11 及上 | N/A | 是 | 僅限 iOS |  w/ Safari 實例
| [SFSafariView控制器](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | 系統 | iOS11 及上 | N/A | 否 | 僅限 iOS | 否**
| **SFSafariView控制器** | 系統 | iOS10 | N/A | 是 | 僅限 iOS |  w/ Safari 實例
| **WKWebView**  | 應用內 | iOS8 及上 | macOS 10.10 及上 | 否 | iOS 和 macOS | 否**

• 要使 SSO 正常工作，需要在應用之間共用權杖。 這需要權杖緩存或代理應用程式，如適用于 iOS 的 Microsoft 身份驗證器。

## <a name="change-the-default-browser-for-the-request"></a>變更要求的預設瀏覽器

您可以根據 UX 要求使用應用內瀏覽器或特定系統瀏覽器，在 中`MSALWebviewParameters`更改以下屬性：

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>每個互動式請求的更改

每個請求都可以配置為在將屬性傳遞給 API 之前更改`MSALInteractiveTokenParameters.webviewParameters.webviewType`該屬性來`acquireTokenWithParameters:completionBlock:`覆蓋預設瀏覽器。

此外，MSAL 支援通過設置`WKWebView``MSALInteractiveTokenParameters.webviewParameters.customWebView`屬性傳入自訂項。

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

如果使用自訂 Webview，則通知用於指示顯示的 Web 內容的狀態，例如：

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

MSAL 支援的所有 Web 瀏覽器類型都在[MSALWebviewType enum 中](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)聲明

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
