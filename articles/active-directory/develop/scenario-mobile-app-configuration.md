---
title: 配置調用 Web API 的移動應用 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何構建調用 Web API 的移動應用（應用的代碼配置）
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132494"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>配置調用 Web API 的移動應用

創建應用程式後，您將學習如何使用應用註冊參數配置代碼。 移動應用程式在安裝到其創建框架中提出了一些複雜性。

## <a name="find-msal-support-for-mobile-apps"></a>查找移動應用的 MSAL 支援

以下 Microsoft 身份驗證庫 （MSAL） 類型支援移動應用。

MSAL | 描述
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 用於開發可擕式應用程式。 MSAL.NET支援以下構建移動應用程式的平臺：通用 Windows 平臺 （UWP）、Xamarin.iOS 和 Xamarin.Android。
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 用於使用目標 C 或 Swift 開發本機 iOS 應用程式。
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 用於在 JAVA 中為 Android 開發本機 Android 應用程式。

## <a name="instantiate-the-application"></a>具現化應用程式

### <a name="android"></a>Android

移動應用程式使用 類`PublicClientApplication`。 下面瞭解如何具現化：

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

iOS 上的移動應用程式需要具現化`MSALPublicClientApplication`類。 要具現化類，請使用以下代碼。 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[其他 MSALPublicClientClientConfig 屬性](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)可以覆蓋預設許可權、指定重定向 URI 或更改 MSAL 權杖緩存的行為。 

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

本節介紹如何具現化 Xamarin.iOS、Xamarin.Android 和 UWP 應用程式的應用程式。

#### <a name="instantiate-the-application"></a>具現化應用程式

在 Xamarin 或 UWP 中，具現化應用程式的最簡單方法是使用以下代碼。 在此代碼中，`ClientId`是已註冊應用的 GUID。

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

其他`With<Parameter>`方法設置 UI 父級，覆蓋預設許可權，為遙測指定用戶端名稱和版本，指定重定向 URI，並指定要使用的 HTTP 工廠。 例如，可以使用 HTTP 工廠來處理代理並指定遙測和日誌記錄。 

以下各節提供有關具現化應用程式的詳細資訊。

##### <a name="specify-the-parent-ui-window-or-activity"></a>指定父 UI、視窗或活動

在 Android 上，您需要在進行互動式身份驗證之前傳遞父活動。 在 iOS 上，當您使用代理時，您需要轉接`ViewController`。 在 UWP 上，您可能想要傳遞父視窗。 獲取權杖時將其傳遞。 但是，在創建應用時，還可以指定回檔作為返回`UIParent`的委託。

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上，我們建議您使用[`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)。 生成的`PublicClientApplication`產生器代碼如下所示：

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>查找更多應用構建參數

有關 上`PublicClientApplicationBuilder`所有可用的方法的清單，請參閱[方法清單](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。

有關 中`PublicClientApplicationOptions`公開的所有選項的說明，請參閱[參考文檔](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS 的任務

如果您在 Xamarin iOS 上使用MSAL.NET，則執行以下任務。

* [覆蓋和實現`OpenUrl`函數`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [啟用鑰匙串組](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [啟用權杖緩存共用](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [啟用鑰匙串訪問](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

有關詳細資訊，請參閱[Xamarin iOS 注意事項](msal-net-xamarin-ios-considerations.md)。

## <a name="tasks-for-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL 任務

當您將 MSAL 用於 iOS 和 macOS 時，這些任務是必需的：

* [實現`openURL`回檔](#brokered-authentication-for-msal-for-ios-and-macos)
* [啟用鑰匙串訪問組](howto-v2-keychain-objc.md)
* [自訂瀏覽器和 WebViews](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Xamarin.安卓的任務

如果您使用 Xamarin.Android，則執行以下任務：

- [確保在身份驗證流的交互部分結束後將控制返回 MSAL](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [更新 Android 清單](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用嵌入式 Web 視圖（可選）](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [根據需要進行故障排除](msal-net-xamarin-android-considerations.md#troubleshoot)

有關詳細資訊，請參閱[Xamarin.安卓注意事項](msal-net-xamarin-android-considerations.md)。

有關 Android 上的瀏覽器的注意事項，請參閱[Xamarin.Android 特定于MSAL.NET的注意事項](msal-net-system-browser-android-considerations.md)。

#### <a name="tasks-for-uwp"></a>UWP 的任務

在 UWP 上，您可以使用商業網路。 以下各節介紹了應在公司方案中完成的任務。

有關詳細資訊，請參閱有關[MSAL.NET 的特定于 UWP 的注意事項](msal-net-uwp-considerations.md)。

## <a name="configure-the-application-to-use-the-broker"></a>將應用程式佈建為使用代理

在 Android 和 iOS 上，經紀商支援：

- **單一登入 （SSO）：** 對於在 Azure 活動目錄 （Azure AD） 中註冊的設備，可以使用 SSO。 使用 SSO 時，使用者無需登錄到每個應用程式。
- **設備標識**：此設置啟用與 Azure AD 設備相關的條件訪問策略。 身份驗證過程使用設備加入工作區時創建的設備證書。
- **應用程式標識驗證**：當應用程式調用代理時，它將傳遞其重定向 URL。 然後，經紀人驗證它。

### <a name="enable-the-broker-on-xamarin"></a>啟用 Xamarin 上的代理

要啟用 Xamarin 上的代理，`WithBroker()`在調用`PublicClientApplicationBuilder.CreateApplication`方法時使用 參數。 預設情況下，`.WithBroker()`設置為 true。 

要為 Xamarin.iOS 啟用代理身份驗證，請按照本文中的[Xamarin.iOS 部分](#enable-brokered-authentication-for-xamarin-ios)中的步驟操作。

### <a name="enable-the-broker-for-msal-for-android"></a>為 Android 啟用 MSAL 的代理

有關在 Android 上啟用代理的資訊，請參閱[Android 上的代理身份驗證](brokered-auth.md)。 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>為 iOS 和 macOS 啟用 MSAL 的代理

預設情況下，對於適用于 iOS 和 macOS 的 MSAL 中的 Azure AD 方案啟用代理身份驗證。 

以下各節提供了用於為 Xamarin.iOS 或 msAL 配置應用程式的指令，用於 Xamarin.iOS 的 MSAL 或針對 iOS 和 macOS 的 MSAL。 在兩組說明中，某些步驟不同。

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>為 Xamarin iOS 啟用代理身份驗證

按照本節中的步驟，使 Xamarin.iOS 應用能夠與 Microsoft[身份驗證器](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)應用對話。

#### <a name="step-1-enable-broker-support"></a>第 1 步：啟用代理支援

預設情況下禁用代理支援。 為單個`PublicClientApplication`類啟用它。 通過`WithBroker()`創建`PublicClientApplication`類時使用 參數`PublicClientApplicationBuilder`。 預設情況下`WithBroker()`，參數設置為 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>第 2 步：更新應用委託以處理回檔

當MSAL.NET調用代理時，代理然後調用回您的應用程式。 它使用`AppDelegate.OpenUrl`方法回電。 由於 MSAL 等待來自代理的回應，因此您的應用程式需要合作來回撥MSAL.NET。 通過更新`AppDelegate.cs`檔以重寫方法來設置此行為，如下代碼所示。

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

每次啟動應用程式時都會調用此方法。 這是一個處理來自代理的回應並完成MSAL.NET啟動的身份驗證過程的機會。

#### <a name="step-3-set-a-uiviewcontroller"></a>第 3 步：設置 UIView 控制器（）

對於 Xamarin iOS，您通常不需要設置物件視窗。 但是在這種情況下，您應該設置它，以便您可以發送和接收來自代理的回應。 要在 中`AppDelegate.cs`設置物件視窗，在 中`ViewController`設置 。

要設置物件視窗，請按照以下步驟操作：

1. 在`AppDelegate.cs`中，`App.RootViewController`將 設置為`UIViewController()`新的 。 此設置可確保對代理的調用包括`UIViewController`。 如果未正確設置，您可能會收到此錯誤：

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. 在通話`AcquireTokenInteractive`中，使用`.WithParentActivityOrWindow(App.RootViewController)`。 將引用傳遞給要使用的物件視窗。 以下是範例：

    在 `App.cs` 中：
    ```csharp
       public static object RootViewController { get; set; }
    ```
    在 `AppDelegate.cs` 中：
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    在呼叫`AcquireToken`中：
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>第 4 步：註冊 URL 方案

MSAL.NET使用 URL 調用代理，然後將代理回應返回到你的應用。 要完成往返行程，請在`Info.plist`檔中註冊應用的 URL 方案。 

要註冊應用的 URL 方案，請按照以下步驟操作：

1. 前`CFBundleURLSchemes`綴`msauth`為 。 
1. 添加到`CFBundleURLName`末尾。 遵循以下模式： 

   `$"msauth.(BundleId)"`

   在這裡，`BundleId`唯一標識您的設備。 例如，如果`BundleId`為`yourcompany.xforms`，則 URL`msauth.com.yourcompany.xforms`方案為 。
    
   > [!NOTE]
   > 此 URL 方案將成為重定向 URI 的一部分，該 URI 在收到代理的回應時唯一標識應用。
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>第 5 步：添加到 LS 應用程式查詢計劃部分

MSAL`–canOpenURL:`用於檢查代理是否安裝在設備上。 在 iOS 9 中，Apple 鎖定了應用程式可以查詢的方案。

添加到`msauthv2`檔的`LSApplicationQueriesSchemes`部分，`Info.plist`如以下代碼示例所示：

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>針對 iOS 和 macOS 的 MSAL 的代理身份驗證

預設情況下，為 Azure AD 方案啟用代理身份驗證。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>第 1 步：更新應用委託以處理回檔

當適用于 iOS 和 macOS 的 MSAL 調用代理時，代理使用`openURL`方法回檔您的應用程式。 由於 MSAL 等待來自代理的回應，因此您的應用程式需要合作來回撥 MSAL。 通過更新`AppDelegate.m`檔以覆蓋該方法來設置此功能，如下代碼示例所示。

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
> 如果您在 iOS 13 或更高版本中採用`UISceneDelegate`，則將 MSAL 回檔`scene:openURLContexts:`放在`UISceneDelegate` 對於每個`handleMSALResponse:sourceApplication:`URL，只能調用一次 MSAL。
>
> 有關詳細資訊，請參閱 Apple[文檔](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)。

#### <a name="step-2-register-a-url-scheme"></a>第 2 步：註冊 URL 方案

適用于 iOS 和 macOS 的 MSAL 使用 URL 調用代理，然後將代理回應返回到你的應用。 要完成往返行程，請在檔中註冊應用的`Info.plist`URL 方案。

要為應用註冊方案，請： 

1. 使用`msauth`首碼自訂 URL 方案。 

1. 將捆綁包識別碼添加到方案的末尾。 遵循以下模式： 

   `$"msauth.(BundleId)"`

   在這裡，`BundleId`唯一標識您的設備。 例如，如果`BundleId`為`yourcompany.xforms`，則 URL`msauth.com.yourcompany.xforms`方案為 。
  
   > [!NOTE]
   > 此 URL 方案將成為重定向 URI 的一部分，該 URI 在收到代理的回應時唯一標識應用。 確保格式`msauth.(BundleId)://auth`中的重定向 URI 已為應用程式在[Azure 門戶](https://portal.azure.com)中註冊。
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>第 3 步：添加 LS 應用程式查詢方案

添加`LSApplicationQueriesSchemes`以允許對 Microsoft 身份驗證器應用的調用（如果已安裝）。

> [!NOTE]
> 使用`msauthv3`Xcode 11 及更高版本編譯應用時，需要該方案。 

下面是如何添加`LSApplicationQueriesSchemes`的示例：

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Xamarin.安卓的代理身份驗證

MSAL.NET不支援安卓的經紀人。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [獲取權杖](scenario-mobile-acquire-token.md)
