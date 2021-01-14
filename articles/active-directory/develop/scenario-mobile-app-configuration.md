---
title: 設定呼叫 web Api 的行動應用程式 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何設定您的行動應用程式程式碼以呼叫 web API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e2c632de3d602fe2d3e5bfa74f78e90f48412067
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208908"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>設定可呼叫 web Api 的行動應用程式

建立應用程式之後，您將瞭解如何使用應用程式註冊參數來設定程式碼。 行動應用程式會提供一些與建立架構相關的複雜性。

## <a name="find-msal-support-for-mobile-apps"></a>尋找行動應用程式的 MSAL 支援

下列 Microsoft 驗證程式庫 (MSAL) 類型支援 mobile apps。

MSAL | 描述
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 用來開發便攜應用程式。 MSAL.NET 支援下列用來建立行動應用程式的平臺：通用 Windows 平臺 (UWP) 、Xamarin 和 Xamarin. Android。
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 用來開發原生 iOS 應用程式，使用目標-C 或 Swift。
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 用來在適用于 Android 的 JAVA 中開發原生 Android 應用程式。

## <a name="instantiate-the-application"></a>具現化應用程式

### <a name="android"></a>Android

行動應用程式會使用 `PublicClientApplication` 類別。 以下是如何將它具現化：

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

IOS 上的行動應用程式需要具現化 `MSALPublicClientApplication` 類別。 若要具現化類別，請使用下列程式碼。

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[其他 MSALPublicClientApplicationConfig 屬性](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) 可以覆寫預設授權單位、指定重新導向 URI，或變更 MSAL token 快取的行為。

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

本節說明如何具現化適用于 Xamarin、Xamarin、Android 和 UWP 應用程式的應用程式。

#### <a name="instantiate-the-application"></a>具現化應用程式

在 Xamarin 或 UWP 中，具現化應用程式的最簡單方式是使用下列程式碼。 在此程式碼中， `ClientId` 是已註冊應用程式的 GUID。

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

其他 `With<Parameter>` 方法會設定 UI 父系、覆寫預設授權單位、指定遙測的用戶端名稱和版本、指定重新導向 URI，以及指定要使用的 HTTP 處理站。 例如，您可以使用 HTTP factory 來處理 proxy，以及指定遙測和記錄。

下列各節提供有關具現化應用程式的詳細資訊。

##### <a name="specify-the-parent-ui-window-or-activity"></a>指定父 UI、視窗或活動

在 Android 上，您必須先傳遞父活動，然後再進行互動式驗證。 在 iOS 上，當您使用訊息代理程式時，您需要傳入 `ViewController` 。 在 UWP 上，您可能會想要傳入父視窗。 您會在取得權杖時將其傳入。 但是，當您建立應用程式時，您也可以將回呼指定為傳回的委派 `UIParent` 。

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，我們建議您使用 [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) 。 產生的產生器程式 `PublicClientApplication` 代碼看起來會像這個範例：

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>尋找更多應用程式建立參數

如需中可用的所有方法清單 `PublicClientApplicationBuilder` ，請參閱 [方法清單](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。

如需中公開之所有選項的說明 `PublicClientApplicationOptions` ，請參閱 [參考檔](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS 的工作

如果您在 Xamarin iOS 上使用 MSAL.NET，請執行下列工作。

* [覆寫並 `OpenUrl` 在中執行函數 `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [啟用 keychain 群組](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [啟用權杖快取共用](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [啟用 keychain 存取](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

如需詳細資訊，請參閱 [Xamarin iOS 考慮](msal-net-xamarin-ios-considerations.md)。

## <a name="tasks-for-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL 工作

當您使用適用于 iOS 和 macOS 的 MSAL 時，必須執行下列工作：

* [執行 `openURL` 回呼](#brokered-authentication-for-msal-for-ios-and-macos)
* [啟用 keychain 存取群組](howto-v2-keychain-objc.md)
* [自訂瀏覽器和 WebViews](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>適用于 Xamarin 的工作

如果您使用的是 Xamarin，請執行下列工作：

- [確定在驗證流程的互動式部分結束之後，控制權回到 MSAL](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [更新 Android 資訊清單](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用內嵌的 web view (選用) ](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [視需要進行疑難排解](msal-net-xamarin-android-considerations.md#troubleshooting)

如需詳細資訊，請參閱 [Xamarin. Android 考慮](msal-net-xamarin-android-considerations.md)。

如需有關 Android 上的瀏覽器的考慮，請參閱 [Xamarin. android 特定的考慮 MSAL.NET](msal-net-system-browser-android-considerations.md)。

#### <a name="tasks-for-uwp"></a>UWP 的工作

在 UWP 上，您可以使用公司網路。 下列各節說明您應該在公司案例中完成的工作。

如需詳細資訊，請參閱 [UWP 特有的 MSAL.NET 考慮](msal-net-uwp-considerations.md)。

## <a name="configure-the-application-to-use-the-broker"></a>設定應用程式以使用訊息代理程式

在 Android 和 iOS 上，訊息代理程式會啟用：

- **單一登入 (sso)**：您可以針對使用 Azure Active Directory (Azure AD) 註冊的裝置使用 sso。 當您使用 SSO 時，您的使用者不需要登入每個應用程式。
- **裝置識別**：此設定可啟用與 Azure AD 裝置相關的條件式存取原則。 驗證程式會使用裝置加入工作場所時所建立的裝置憑證。
- **應用程式識別驗證**：當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 URL。 然後，訊息代理程式會驗證它。

### <a name="enable-the-broker-on-xamarin"></a>啟用 Xamarin 上的訊息代理程式

若要啟用 Xamarin 上的訊息代理程式，請 `WithBroker()` 在呼叫方法時使用參數 `PublicClientApplicationBuilder.CreateApplication` 。 依預設， `.WithBroker()` 會設定為 true。

若要啟用適用于 Xamarin 的代理驗證，請遵循本文中的 [ [ios] 一節](#enable-brokered-authentication-for-xamarin-ios) 中的步驟。

### <a name="enable-the-broker-for-msal-for-android"></a>針對 Android 啟用 broker for MSAL

如需有關在 Android 上啟用 broker 的詳細資訊，請參閱 [android 上](msal-android-single-sign-on.md)的代理驗證。

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>啟用適用于 iOS 和 macOS 的 MSAL 訊息代理程式

針對 MSAL 中的 iOS 和 macOS Azure AD 案例，預設會啟用代理驗證。

下列各節提供的指示可讓您將應用程式設定為適用于 MSAL for MSAL for iOS 和 macOS 的代理驗證支援。 在這兩組指示中，部分步驟不同。

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>啟用 Xamarin iOS 的代理驗證

遵循本節中的步驟，讓您的 Xamarin iOS 應用程式與 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 應用程式交談。

#### <a name="step-1-enable-broker-support"></a>步驟1：啟用訊息代理程式支援

Broker 支援預設為停用。 您可以針對個別類別啟用它 `PublicClientApplication` 。 `WithBroker()`當您透過建立類別時，請使用參數 `PublicClientApplication` `PublicClientApplicationBuilder` 。 `WithBroker()`預設會將參數設定為 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步驟2：更新 AppDelegate 以處理回呼

當 MSAL.NET 呼叫訊息代理程式時，訊息代理程式會接著回呼您的應用程式。 它會使用方法呼叫回來 `AppDelegate.OpenUrl` 。 因為 MSAL 會等候來自訊息代理程式的回應，所以您的應用程式必須合作來呼叫 MSAL.NET 回來。 您可以藉由更新檔案來覆 `AppDelegate.cs` 寫方法來設定此行為，如下列程式碼所示。

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

每次啟動應用程式時，就會叫用這個方法。 這是處理來自訊息代理程式的回應，以及完成 MSAL.NET 開始之驗證程式的機會。

#### <a name="step-3-set-a-uiviewcontroller"></a>步驟3：設定 UIViewController ( # A1

若是 Xamarin iOS，您通常不需要設定物件視窗。 但是在此情況下，您應該將它設定為可傳送和接收來自訊息代理程式的回應。 若要設定物件視窗，請在中 `AppDelegate.cs` 設定 `ViewController` 。

若要設定物件視窗，請遵循下列步驟：

1. 在中 `AppDelegate.cs` ，將設定 `App.RootViewController` 為新的 `UIViewController()` 。 這種設定可確保對 broker 的呼叫包含 `UIViewController` 。 如果設定不正確，您可能會收到此錯誤：

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. 在 `AcquireTokenInteractive` 呼叫上，使用 `.WithParentActivityOrWindow(App.RootViewController)` 。 傳入您將使用之物件視窗的參考。 以下為範例：

    在 `App.cs` 中：
    ```csharp
       public static object RootViewController { get; set; }
    ```
    在 `AppDelegate.cs` 中：
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    在 `AcquireToken` 呼叫中：
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

#### <a name="step-4-register-a-url-scheme"></a>步驟4：註冊 URL 配置

MSAL.NET 使用 Url 叫用訊息代理程式，然後將訊息代理程式回應傳回給您的應用程式。 若要完成來回行程，請在檔案中註冊應用程式的 URL 配置 `Info.plist` 。

若要註冊應用程式的 URL 配置，請遵循下列步驟：

1. 前置 `CFBundleURLSchemes` 詞 `msauth` 。
1. 加入 `CFBundleURLName` 至結尾。 遵循此模式：

   `$"msauth.(BundleId)"`

   在這裡，可 `BundleId` 唯一識別您的裝置。 例如，如果 `BundleId` 是 `yourcompany.xforms` ，則您的 URL 配置為 `msauth.com.yourcompany.xforms` 。

  
      此 URL 配置將成為重新導向 URI 的一部分，可在接收到訊息代理程式的回應時，唯一識別您的應用程式。

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

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>步驟5：加入至 LSApplicationQueriesSchemes 區段

MSAL `–canOpenURL:` 會使用檢查訊息代理程式是否已安裝在裝置上。 在 iOS 9 中，Apple 已鎖定應用程式可以查詢的配置。

`msauthv2`在檔案的 `LSApplicationQueriesSchemes` 區段中加入 `Info.plist` ，如下列程式碼範例所示：

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL 代理驗證

預設會針對 Azure AD 案例啟用代理驗證。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>步驟1：更新 AppDelegate 以處理回呼

當 MSAL iOS 和 macOS 呼叫訊息代理程式時，訊息代理程式會使用方法呼叫回您的應用程式 `openURL` 。 由於 MSAL 會等候來自訊息代理程式的回應，因此您的應用程式必須合作以回呼 MSAL。 藉由更新檔案以覆寫方法來設定這項功能 `AppDelegate.m` ，如下列程式碼範例所示。

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

如果您 `UISceneDelegate` 在 iOS 13 或更新版本上採用，請改為將 MSAL 回呼放入 `scene:openURLContexts:` 的 `UISceneDelegate` 。 `handleMSALResponse:sourceApplication:`每個 URL 都只能呼叫一次 MSAL。

如需詳細資訊，請參閱 [Apple 檔](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)。

#### <a name="step-2-register-a-url-scheme"></a>步驟2：註冊 URL 配置

適用于 iOS 和 macOS 的 MSAL 會使用 Url 叫用訊息代理程式，然後將訊息代理程式回應傳回給您的應用程式。 若要完成來回行程，請在檔案中註冊應用程式的 URL 配置 `Info.plist` 。

若要為您的應用程式註冊配置：

1. 使用自訂 URL 配置的前置詞 `msauth` 。

1. 將您的套件組合識別碼新增至配置的結尾。 遵循此模式：

   `$"msauth.(BundleId)"`

   在這裡，可 `BundleId` 唯一識別您的裝置。 例如，如果 `BundleId` 是 `yourcompany.xforms` ，則您的 URL 配置為 `msauth.com.yourcompany.xforms` 。

    此 URL 配置將成為重新導向 URI 的一部分，可在接收到訊息代理程式的回應時，唯一識別您的應用程式。 請確定已在 `msauth.(BundleId)://auth` [Azure 入口網站](https://portal.azure.com)中為您的應用程式註冊格式的重新導向 URI。

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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>步驟3：新增 LSApplicationQueriesSchemes

新增 `LSApplicationQueriesSchemes` 以允許對 Microsoft Authenticator 應用程式的呼叫（如果已安裝的話）。

> [!NOTE]
> `msauthv3`使用 Xcode 11 和更新版本編譯您的應用程式時，需要配置。

以下是如何新增的範例 `LSApplicationQueriesSchemes` ：

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>適用于 Xamarin 的代理驗證

如需有關在 Android 上啟用 broker 的詳細資訊，請參閱 [Xamarin 上](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android)的代理驗證。

## <a name="next-steps"></a>後續步驟

請移至本案例的下一篇文章，以取得 [權杖](scenario-mobile-acquire-token.md)。