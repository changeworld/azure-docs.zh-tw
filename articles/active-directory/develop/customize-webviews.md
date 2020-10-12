---
title: 自訂瀏覽器 & Web 檢視 (MSAL iOS/macOS) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何自訂 MSAL iOS/macOS 瀏覽器體驗以登入使用者。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: a8486ec87b5198231a33b1dab382ba457c8c8066
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478122"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>如何：自訂 iOS/macOS 的瀏覽器和 Web 檢視

互動式驗證需要網頁瀏覽器。 在 iOS 和 macOS 10.15 + 上，Microsoft 驗證程式庫 (MSAL) 預設會使用系統網頁瀏覽器 (可能會顯示在您的應用程式最上層，) 進行互動式驗證以登入使用者。 使用系統瀏覽器有一個優點，就是共用單一登入 (SSO) 狀態與其他應用程式和 web 應用程式。

您可以藉由自訂設定至其他顯示 web 內容的選項，來變更體驗，例如：

僅適用于 iOS：

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

針對 iOS 和 macOS：

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc)。

適用于 macOS 的 MSAL 僅支援 `WKWebView` 較舊的作業系統版本。 `ASWebAuthenticationSession` 僅在 macOS 10.15 和更新版本上支援。 

## <a name="system-browsers"></a>系統瀏覽器

若是 iOS， `ASWebAuthenticationSession` 、 `SFAuthenticationSession` 和 `SFSafariViewController` 會被視為系統瀏覽器。 若為 macOS， `ASWebAuthenticationSession` 則只能使用。 一般而言，系統瀏覽器會與 Safari 瀏覽器應用程式共用 cookie 和其他網站資料。

根據預設，MSAL 會動態偵測 iOS 版本，並選取該版本上可用的建議系統瀏覽器。 在 iOS 12 + 上，它將會是 `ASWebAuthenticationSession` 。 

### <a name="default-configuration-for-ios"></a>IOS 的預設設定

| 版本 | 網頁瀏覽器 |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>MacOS 的預設設定

| 版本 | 網頁瀏覽器 |
|:-------------:|:-------------:|
| macOS 10.15 + | ASWebAuthenticationSession |
| 其他版本 | WKWebView |

開發人員也可以針對 MSAL apps 選取不同的系統瀏覽器：

- `SFAuthenticationSession` 是的 iOS 11 版 `ASWebAuthenticationSession` 。
- `SFSafariViewController` 是更常見的用途，並提供流覽 web 的介面，也可用於登入用途。 在 iOS 9 和10中，cookie 和其他網站資料會與 Safari 共用，但不會在 iOS 11 和更新版本中。

## <a name="in-app-browser"></a>應用程式內瀏覽器

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) 是可顯示 web 內容的應用程式內瀏覽器。 它不會與其他 **WKWebView** 實例或 Safari 瀏覽器共用 cookie 或網站資料。 WKWebView 是適用于 iOS 和 macOS 的跨平臺瀏覽器。

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Cookie 共用和單一登入 (SSO) 含意

您使用的瀏覽器會影響 SSO 體驗，因為它們共用 cookie 的方式。 下表摘要說明每個瀏覽器的 SSO 體驗。

| 技術    | 瀏覽器類型  | iOS 可用性 | macOS 可用性 | 共用 cookie 和其他資料  | MSAL 可用性 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | 系統 | iOS12 和 up | macOS 10.15 及更新的 | 是 | iOS 和 macOS 10.15 + | w/Safari 實例
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | 系統 | iOS11 和 up | N/A | 是 | 僅限 iOS |  w/Safari 實例
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | 系統 | iOS11 和 up | N/A | 否 | 僅限 iOS | 否**
| **SFSafariViewController** | 系統 | Ios10 執行 | N/A | 是 | 僅限 iOS |  w/Safari 實例
| **WKWebView**  | 應用程式內 | iOS8 和 up | macOS 10.10 及更新的 | 否 | iOS 和 macOS | 否**

* * 若要讓 SSO 能夠運作，您必須在應用程式之間共用權杖。 這需要權杖快取或訊息代理程式應用程式，例如 iOS 的 Microsoft Authenticator。

## <a name="change-the-default-browser-for-the-request"></a>變更要求的預設瀏覽器

您可以藉由變更中的下列屬性，使用應用程式內瀏覽器或特定的系統瀏覽器（視 UX 需求而定） `MSALWebviewParameters` ：

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>針對每個互動式要求進行變更

每個要求都可以設定為覆寫預設瀏覽器，方法是 `MSALInteractiveTokenParameters.webviewParameters.webviewType` 在將屬性傳遞至 API 之前變更屬性 `acquireTokenWithParameters:completionBlock:` 。

此外，MSAL 支援藉 `WKWebView` 由設定屬性來傳入自訂 `MSALInteractiveTokenParameters.webviewParameters.customWebView` 。

例如：

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

如果您使用自訂的 web 程式，通知會用來指出顯示的 web 內容狀態，例如：

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

所有 MSAL 支援的網頁瀏覽器類型都在[MSALWebviewType 列舉](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)中宣告

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
