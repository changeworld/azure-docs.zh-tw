---
title: 設定呼叫 web Api 的行動應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫 web Api 的行動應用程式（應用程式的程式碼設定）
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
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 61d3a7b7f06ffdb7f39f80f7f3bf19e9007945d2
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702108"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>呼叫 web Api 的行動應用程式-程式碼設定

建立應用程式之後，您將瞭解如何使用應用程式註冊參數來設定程式碼。 行動應用程式也有一些複雜的細節，這必須配合用來建立這些應用程式的架構進行調整

## <a name="msal-libraries-supporting-mobile-apps"></a>支援 mobile apps 的 MSAL 程式庫

支援行動裝置應用程式的 Microsoft 程式庫包括：

  MSAL 程式庫 | 說明
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 開發便攜應用程式。 MSAL.NET 支援的平臺來建立行動應用程式，包括 UWP、Xamarin 和 Xamarin。
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 若要使用目標-C 或 Swift 開發原生 iOS 應用程式
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 在適用于 Android 的 JAVA 中開發原生 Android 應用程式

## <a name="instantiating-the-application"></a>將應用程式具現化

### <a name="android"></a>Android

行動應用程式會使用 `PublicClientApplication` 類別。 以下是具現化的方式：

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

IOS 上的行動應用程式需要具現化 `MSALPublicClientApplication` 類別。

Objective-C：

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift：
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

還有[其他 MSALPublicClientApplicationConfig 屬性](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)可以覆寫預設的授權單位、指定重新導向 URI 或變更 MSAL token 快取行為。 

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

下列段落說明如何具現化 Xamarin. iOS、Xamarin 和 UWP 應用程式的應用程式。

#### <a name="instantiating-the-application"></a>將應用程式具現化

在 Xamarin 或 UWP 中，將應用程式具現化的最簡單方式如下所示，其中 `ClientId` 是已註冊應用程式的 Guid。

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

還有一些*參數*方法可設定 UI 父系、覆寫預設授權單位、指定用戶端名稱和版本（適用于遙測）、指定重新導向 URI、指定要使用的 Http 處理站（例如，用來處理 proxy、指定遙測和記錄）。 這是下列段落的主題。

##### <a name="specifying-the-parent-uiwindowactivity"></a>指定父 UI/視窗/活動

在 Android 上，您必須先傳遞父活動，然後才進行互動式驗證。 在 iOS 上，使用訊息代理程式時，您必須傳入 ViewController。 在 UWP 上，您可能會想要傳入父視窗。 當您取得權杖時，這是可行的，但也可以在應用程式建立時指定回呼，此委派會傳回 UIParent。

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，建議您在[這裡](https://github.com/jamesmontemagno/CurrentActivityPlugin)使用 `CurrentActivityPlugin`。  接下來，您的 `PublicClientApplication` 產生器程式碼看起來像這樣：

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>更多應用程式建立參數

- 如需 `PublicClientApplicationBuilder`上可用的所有修飾詞清單，請參閱參考檔[PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 如需 `PublicClientApplicationOptions` 中公開之所有選項的說明，請參閱參考檔中的[PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

## <a name="xamarin-ios-specific-considerations"></a>Xamarin iOS 特定考慮

在 Xamarin iOS 上，當您使用 MSAL.NET 時，必須考慮幾個事項：

1. [覆寫並在 `AppDelegate` 中執行 `OpenUrl` 函式](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [啟用 Keychain 群組](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [啟用權杖快取共用](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [啟用 Keychain 存取](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

在[Xamarin iOS 考慮](msal-net-xamarin-ios-considerations.md)中提供詳細資料

## <a name="msal-for-ios-and-macos-specific-considerations"></a>適用于 iOS 和 macOS 特定考慮的 MSAL

使用適用于 iOS 和 macOS 的 MSAL 時，適用類似的考慮：

1. [執行 `openURL` 回呼](#brokered-authentication-for-msal-for-ios-and-macos)
2. [啟用 keychain 存取群組](howto-v2-keychain-objc.md)
3. [自訂瀏覽器和網站](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Xamarin Android 特定考慮

以下是 Xamarin Android 細節：

- [確保在驗證流程的互動部分結束後，控制權回到 MSAL](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [更新 Android 資訊清單](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用內嵌的 web view （選擇性）](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [疑難排解](msal-net-xamarin-android-considerations.md#troubleshooting)

在[Xamarin Android 考慮](msal-net-xamarin-android-considerations.md)中提供詳細資料

最後，有一些 specificities 可以知道 Android 上的瀏覽器。 它們會在[MSAL.NET 的 Xamarin Android 特定考慮](msal-net-system-browser-android-considerations.md)中說明

#### <a name="uwp-specific-considerations"></a>UWP 特定考慮

在 UWP 上，您可以使用公司網路。 如需搭配使用 MSAL 程式庫與 UWP 的詳細資訊，請參閱[MSAL.NET 的通用 Windows 平臺特定考慮](msal-net-uwp-considerations.md)。

## <a name="configuring-the-application-to-use-the-broker"></a>設定應用程式以使用 broker

### <a name="why-use-brokers-in-ios-and-android-applications"></a>為何要在 iOS 和 Android 應用程式中使用訊息代理程式？

在 Android 和 iOS 上，訊息代理程式會啟用：

- 當裝置向 AAD 註冊時，單一登入（SSO）。 您的使用者不需要登入每個應用程式。
- 裝置識別。 藉由存取已加入工作場所的裝置上建立的裝置憑證，啟用 Azure AD 裝置相關的條件式存取原則。
- 應用程式識別驗證。 當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 url，而 broker 會進行驗證。

### <a name="enable-the-broker-on-xamarin"></a>在 Xamarin 上啟用訊息代理程式

若要啟用其中一項功能，請在呼叫 `PublicClientApplicationBuilder.CreateApplication` 方法時使用 `WithBroker()` 參數。 預設會將 `.WithBroker()` 設為 true。 依照下列步驟進行[Xamarin. iOS](#brokered-authentication-for-xamarinios)。

### <a name="enable-the-broker-for-msal-for-android"></a>啟用 MSAL for Android 的 broker

如需有關在 Android 上啟用訊息代理程式的詳細資訊，請參閱[android 中](brokered-auth.md)的代理驗證。 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>啟用適用于 iOS 和 macOS 的 MSAL 訊息代理程式

針對 iOS 和 macOS 的 MSAL 中的 AAD 案例，預設會啟用代理驗證。 請遵循下列步驟，將您的應用程式設定為適用于[iOS 和 macOS 的 MSAL](#brokered-authentication-for-msal-for-ios-and-macos)代理驗證支援。 請注意， [MSAL For Xamarin](#brokered-authentication-for-xamarinios)和[MSAL （適用于 ios 和 macOS](#brokered-authentication-for-msal-for-ios-and-macos)）之間的一些步驟不同。

### <a name="brokered-authentication-for-xamarinios"></a>適用于 Xamarin 的代理驗證

請遵循下列步驟來啟用您的 Xamarin iOS 應用程式，以與[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)應用程式通訊。

#### <a name="step-1-enable-broker-support"></a>步驟1：啟用訊息代理程式支援

代理程式支援是以每個`PublicClientApplication` 為基礎來啟用。 此功能預設為停用。 透過 `PublicClientApplicationBuilder`建立 `PublicClientApplication` 時，您必須使用 `WithBroker()` 參數（預設設定為 true）。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步驟2：更新 AppDelegate 以處理回呼

當 MSAL.NET 呼叫 broker 時，訊息代理程式會接著透過 `AppDelegate.OpenUrl` 方法回呼您的應用程式。 由於 MSAL 會等待來自訊息代理程式的回應，因此您的應用程式需要合作以呼叫 MSAL.NET 回來。 若要這麼做，請更新 `AppDelegate.cs` 檔案以覆寫下列方法。

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

每次啟動應用程式時，就會叫用這個方法，並且用來處理來自 broker 的回應，並完成 MSAL.NET 起始的驗證程式。

#### <a name="step-3-set-a-uiviewcontroller"></a>步驟3：設定 UIViewController （）

在 Xamarin iOS 中，您通常不需要設定 [物件] 視窗，但在此情況下，您會在傳送和接收來自訊息代理程式的回應。 仍然在 `AppDelegate.cs`中，設定 ViewController。

執行下列動作以設定 [物件] 視窗：

1) 在 `AppDelegate.cs`中，將 `App.RootViewController` 設定為新的 `UIViewController()`。 這會確保呼叫訊息代理程式時有 `UIViewController`。 如果設定不正確，您可能會收到此錯誤： `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) 在 AcquireTokenInteractive 呼叫上，使用 `.WithParentActivityOrWindow(App.RootViewController)`，並傳入您將使用之物件視窗的參考。

**例如：**

在 `App.cs` 中：
```csharp
   public static object RootViewController { get; set; }
```
在 `AppDelegate.cs` 中：
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
在取得權杖呼叫中：
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>步驟4：註冊 URL 配置

MSAL.NET 會使用 Url 來叫用訊息代理程式，然後將 broker 回應傳回給您的應用程式。 若要完成來回行程，您必須在 `Info.plist` 檔案中註冊應用程式的 URL 配置。

在 `CFBundleURLSchemes` 前面加上 `msauth`。 然後將 `CFBundleURLName` 新增至結尾。

`$"msauth.(BundleId)"`

例如 **：** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 配置會成為 RedirectUri 的一部分，用來在接收來自訊息代理程式的回應時，用來唯一識別您的應用程式。

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>步驟5： LSApplicationQueriesSchemes

MSAL 會使用 `–canOpenURL:` 來檢查代理程式是否已安裝在裝置上。 在 iOS 9 中，Apple 已鎖定應用程式可以查詢的配置。

**將** **`msauthv2`** 新增至 `Info.plist` 檔案的 [`LSApplicationQueriesSchemes`] 區段。

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL 代理驗證

根據預設，AAD 案例會啟用代理驗證。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>步驟1：更新 AppDelegate 以處理回呼

當 iOS 和 macOS 的 MSAL 呼叫訊息代理程式時，訊息代理程式會接著透過 `openURL` 方法回呼您的應用程式。 由於 MSAL 會等待來自訊息代理程式的回應，因此您的應用程式需要合作以呼叫 MSAL 回來。 若要這麼做，請更新 `AppDelegate.m` 檔案以覆寫下列方法。

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
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

請注意，如果您在 iOS 13 + 上採用 UISceneDelegate，MSAL 回呼必須改為放入 UISceneDelegate 的 `scene:openURLContexts:` （請參閱[Apple 檔](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)）。 每個 URL 都只能呼叫 MSAL `handleMSALResponse:sourceApplication:` 一次。

#### <a name="step-2-register-a-url-scheme"></a>步驟2：註冊 URL 配置

適用于 iOS 和 macOS 的 MSAL 會使用 Url 叫用訊息代理程式，然後將 broker 回應傳回給您的應用程式。 若要完成來回行程，您必須在 `Info.plist` 檔案中註冊應用程式的 URL 配置。

在您的自訂 URL 配置前面加上 `msauth`。 然後將**您**的套件組合識別碼新增至結尾。

`msauth.(BundleId)`

例如 **：** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 配置會成為 RedirectUri 的一部分，用來在接收來自訊息代理程式的回應時，用來唯一識別您的應用程式。 請確定已在[Azure 入口網站](https://portal.azure.com)中為您的應用程式註冊 `msauth.(BundleId)://auth` 格式的 RedirectUri。

```XML
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

#### <a name="step-3-lsapplicationqueriesschemes"></a>步驟3： LSApplicationQueriesSchemes

**新增 `LSApplicationQueriesSchemes`** 以允許呼叫 Microsoft Authenticator （如果已安裝）。
請注意，使用 Xcode 11 和更新版本編譯您的應用程式時，需要「msauthv3」配置。 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>適用于 Xamarin 的代理驗證

MSAL.NET 尚不支援 Android 的代理程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得權杖](scenario-mobile-acquire-token.md)
