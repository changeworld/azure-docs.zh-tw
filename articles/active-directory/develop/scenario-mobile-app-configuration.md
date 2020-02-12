---
title: 設定呼叫 web Api 的行動應用程式 |Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132494"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>設定會呼叫 web Api 的行動應用程式

建立應用程式之後，您將瞭解如何使用應用程式註冊參數來設定程式碼。 行動應用程式呈現與建立架構相關的一些複雜性。

## <a name="find-msal-support-for-mobile-apps"></a>尋找行動裝置應用程式的 MSAL 支援

下列 Microsoft 驗證程式庫（MSAL）類型支援行動應用程式。

MSAL | 描述
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 用來開發便攜應用程式。 MSAL.NET 支援下列平臺來建立行動應用程式：通用 Windows 平臺（UWP）、Xamarin. iOS 和 Xamarin. Android。
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 用來開發原生 iOS 應用程式，方法是使用目標-C 或 Swift。
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 用來在適用于 Android 的 JAVA 中開發原生 Android 應用程式。

## <a name="instantiate-the-application"></a>將應用程式具現化

### <a name="android"></a>Android

行動應用程式會使用 `PublicClientApplication` 類別。 以下說明如何將它具現化：

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

[其他 MSALPublicClientApplicationConfig 屬性](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)可以覆寫預設授權單位、指定重新導向 URI，或變更 MSAL token 快取的行為。 

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

本節說明如何具現化適用于 Xamarin. iOS、Xamarin 和 UWP 應用程式的應用程式。

#### <a name="instantiate-the-application"></a>將應用程式具現化

在 Xamarin 或 UWP 中，將應用程式具現化的最簡單方式是使用下列程式碼。 在這段程式碼中，`ClientId` 是已註冊應用程式的 GUID。

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

其他 `With<Parameter>` 方法會設定 UI 父系、覆寫預設授權、指定遙測的用戶端名稱和版本、指定重新導向 URI，並指定要使用的 HTTP factory。 例如，您可以使用 HTTP factory 來處理 proxy，以及指定遙測和記錄。 

下列各節提供有關具現化應用程式的詳細資訊。

##### <a name="specify-the-parent-ui-window-or-activity"></a>指定父 UI、視窗或活動

在 Android 上，您必須先傳遞父活動，然後才進行互動式驗證。 在 iOS 上，當您使用訊息代理程式時，您需要傳入 `ViewController`。 在 UWP 上，您可能會想要傳入父視窗。 當您取得權杖時，您會將它傳遞給。 但當您建立應用程式時，您也可以指定回呼做為傳回 `UIParent`的委派。

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，建議您使用[`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)。 產生的 `PublicClientApplication` 產生器程式碼如下列範例所示：

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>尋找更多應用程式建立參數

如需 `PublicClientApplicationBuilder`上可用的所有方法清單，請參閱[方法清單](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。

如需 `PublicClientApplicationOptions`中公開之所有選項的說明，請參閱[參考檔](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS 的工作

如果您在 Xamarin iOS 上使用 MSAL.NET，請執行下列工作。

* [覆寫並在 `AppDelegate` 中執行 `OpenUrl` 函式](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [啟用 keychain 群組](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [啟用權杖快取共用](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [啟用 keychain 存取](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

如需詳細資訊，請參閱[Xamarin iOS 考慮](msal-net-xamarin-ios-considerations.md)。

## <a name="tasks-for-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL 工作

當您使用適用于 iOS 和 macOS 的 MSAL 時，需要執行這些工作：

* [執行 `openURL` 回呼](#brokered-authentication-for-msal-for-ios-and-macos)
* [啟用 keychain 存取群組](howto-v2-keychain-objc.md)
* [自訂瀏覽器和網站](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>適用于 Xamarin 的工作

如果您使用的是 Xamarin，請執行下列工作：

- [確保在驗證流程的互動部分結束之後，控制權回到 MSAL](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [更新 Android 資訊清單](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用內嵌的 web view （選擇性）](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [視需要進行疑難排解](msal-net-xamarin-android-considerations.md#troubleshoot)

如需詳細資訊，請參閱 < [Xamarin. Android 考慮](msal-net-xamarin-android-considerations.md)。

如需 Android 上瀏覽器的相關考慮，請參閱[MSAL.NET 的特定 android 相關考慮](msal-net-system-browser-android-considerations.md)。

#### <a name="tasks-for-uwp"></a>UWP 的工作

在 UWP 上，您可以使用公司網路。 下列各節說明您應該在公司案例中完成的工作。

如需詳細資訊，請參閱[MSAL.NET 的 UWP 特定考慮](msal-net-uwp-considerations.md)。

## <a name="configure-the-application-to-use-the-broker"></a>設定應用程式以使用 broker

在 Android 和 iOS 上，訊息代理程式會啟用：

- **單一登入（sso）** ：您可以針對使用 Azure Active Directory （Azure AD）註冊的裝置使用 SSO。 當您使用 SSO 時，您的使用者不需要登入每個應用程式。
- **裝置識別**：此設定可啟用與 Azure AD 裝置相關的條件式存取原則。 驗證程式會使用裝置加入工作場所時所建立的裝置憑證。
- **應用程式識別驗證**：當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 URL。 然後，訊息代理程式會確認它。

### <a name="enable-the-broker-on-xamarin"></a>在 Xamarin 上啟用訊息代理程式

若要在 Xamarin 上啟用訊息代理程式，請在呼叫 `PublicClientApplicationBuilder.CreateApplication` 方法時，使用 `WithBroker()` 參數。 根據預設，`.WithBroker()` 會設定為 true。 

若要啟用 Xamarin 的代理驗證，請遵循本文中的 < [xamarin 一節](#enable-brokered-authentication-for-xamarin-ios)中的步驟。

### <a name="enable-the-broker-for-msal-for-android"></a>啟用 MSAL for Android 的 broker

如需有關在 Android 上啟用訊息代理程式的詳細資訊，請參閱[android 上](brokered-auth.md)的代理驗證。 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>啟用適用于 iOS 和 macOS 的 MSAL 訊息代理程式

針對 iOS 和 macOS 的 MSAL 中 Azure AD 案例，預設會啟用代理驗證。 

下列各節提供的指示可讓您設定應用程式，以取得適用于 MSAL for ios 或 MSAL for iOS and macOS 的代理驗證支援。 在這兩組指示中，部分步驟會有所不同。

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>啟用 Xamarin iOS 的代理驗證

請依照本節中的步驟，讓您的 Xamarin iOS 應用程式與[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)應用程式進行交談。

#### <a name="step-1-enable-broker-support"></a>步驟1：啟用訊息代理程式支援

預設會停用 Broker 支援。 您可以針對個別的 `PublicClientApplication` 類別啟用它。 當您透過 `PublicClientApplicationBuilder`建立 `PublicClientApplication` 類別時，請使用 `WithBroker()` 參數。 根據預設，`WithBroker()` 參數會設定為 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步驟2：更新 AppDelegate 以處理回呼

當 MSAL.NET 呼叫訊息代理程式時，broker 接著會回呼您的應用程式。 它會使用 `AppDelegate.OpenUrl` 方法來回呼。 因為 MSAL 會等待訊息代理程式的回應，所以您的應用程式需要合作以呼叫 MSAL.NET 回來。 您可以藉由更新 `AppDelegate.cs` 檔案來設定此行為，以覆寫方法，如下列程式碼所示。

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

每次啟動應用程式時，就會叫用這個方法。 這是處理來自訊息代理程式之回應的機會，並完成 MSAL.NET 啟動的驗證程式。

#### <a name="step-3-set-a-uiviewcontroller"></a>步驟3：設定 UIViewController （）

若是 Xamarin iOS，您通常不需要設定物件視窗。 但是在這種情況下，您應該設定它，讓您可以傳送和接收來自訊息代理程式的回應。 若要設定物件視窗，請在 `AppDelegate.cs`中設定 `ViewController`。

若要設定 [物件] 視窗，請遵循下列步驟：

1. 在 `AppDelegate.cs`中，將 `App.RootViewController` 設定為新的 `UIViewController()`。 此設定可確保代理程式的呼叫包含 `UIViewController`。 如果設定不正確，您可能會收到此錯誤：

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. 在 `AcquireTokenInteractive` 呼叫上，使用 `.WithParentActivityOrWindow(App.RootViewController)`。 傳入您將使用之物件視窗的參考。 以下是範例：

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

MSAL.NET 會使用 Url 來叫用訊息代理程式，然後將 broker 回應傳回給您的應用程式。 若要完成來回行程，請在 `Info.plist` 檔案中註冊應用程式的 URL 配置。 

若要註冊您應用程式的 URL 配置，請遵循下列步驟：

1. `msauth`的前置詞 `CFBundleURLSchemes`。 
1. 將 `CFBundleURLName` 新增至結尾。 遵循此模式： 

   `$"msauth.(BundleId)"`

   在這裡，`BundleId` 會唯一識別您的裝置。 例如，如果 `yourcompany.xforms``BundleId`，則會 `msauth.com.yourcompany.xforms`您的 URL 配置。
    
   > [!NOTE]
   > 此 URL 配置會成為重新導向 URI 的一部分，它會在收到訊息代理程式的回應時，唯一識別您的應用程式。
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>步驟5：新增至 LSApplicationQueriesSchemes 區段

MSAL 會使用 `–canOpenURL:` 來檢查代理程式是否已安裝在裝置上。 在 iOS 9 中，Apple 已鎖定應用程式可以查詢的配置。

將 `msauthv2` 新增至 `Info.plist` 檔案的 `LSApplicationQueriesSchemes` 區段中，如下列程式碼範例所示：

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL 代理驗證

預設會針對 Azure AD 案例啟用代理驗證。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>步驟1：更新 AppDelegate 以處理回呼

當 iOS 和 macOS 的 MSAL 呼叫訊息代理程式時，broker 會使用 `openURL` 方法來回呼您的應用程式。 因為 MSAL 會等待訊息代理程式的回應，所以您的應用程式需要合作以回呼 MSAL。 藉由更新 `AppDelegate.m` 檔案來設定這項功能，以覆寫方法，如下列程式碼範例所示。

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

> [!NOTE]
> 如果您在 iOS 13 或更新版本上採用 `UISceneDelegate`，請改為將 MSAL 回呼放入 `UISceneDelegate` 的 `scene:openURLContexts:`。 每個 URL 都只能呼叫 MSAL `handleMSALResponse:sourceApplication:` 一次。
>
> 如需詳細資訊，請參閱[Apple 檔](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)。

#### <a name="step-2-register-a-url-scheme"></a>步驟2：註冊 URL 配置

適用于 iOS 和 macOS 的 MSAL 會使用 Url 叫用訊息代理程式，然後將 broker 回應傳回給您的應用程式。 若要完成來回行程，請在 `Info.plist` 檔案中註冊應用程式的 URL 配置。

若要註冊應用程式的配置： 

1. 在您的自訂 URL 配置前面加上 `msauth`。 

1. 將您的套件組合識別碼新增至您的配置結尾。 遵循此模式： 

   `$"msauth.(BundleId)"`

   在這裡，`BundleId` 會唯一識別您的裝置。 例如，如果 `yourcompany.xforms``BundleId`，則會 `msauth.com.yourcompany.xforms`您的 URL 配置。
  
   > [!NOTE]
   > 此 URL 配置會成為重新導向 URI 的一部分，它會在收到訊息代理程式的回應時，唯一識別您的應用程式。 請確定已在[Azure 入口網站](https://portal.azure.com)中為您的應用程式註冊 `msauth.(BundleId)://auth` 格式的重新導向 URI。
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>步驟3：加入 LSApplicationQueriesSchemes

新增 `LSApplicationQueriesSchemes` 以允許呼叫 Microsoft Authenticator 應用程式（如果已安裝）。

> [!NOTE]
> 當您的應用程式使用 Xcode 11 和更新版本進行編譯時，需要 `msauthv3` 配置。 

以下是如何新增 `LSApplicationQueriesSchemes`的範例：

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>適用于 Xamarin 的代理驗證

MSAL.NET 不支援 Android 的代理程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得權杖](scenario-mobile-acquire-token.md)
