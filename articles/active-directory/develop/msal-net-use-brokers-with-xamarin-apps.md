---
title: 搭配 Xamarin、iOS、& Android 使用訊息代理程式 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何將可從 .NET 的 Azure AD 驗證程式庫（ADAL.NET）使用 Microsoft Authenticator 的 Xamarin iOS 應用程式遷移至適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 839f62660096aaf3d7954acc45443f04d9ace77d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695138"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 應用程式上使用 Microsoft Authenticator 或 Microsoft Intune 公司入口網站

在 Android 和 iOS 上，像是 Microsoft Authenticator 或 Microsoft Intune 公司入口網站啟用的代理程式（僅限 Android）：

- 單一登入（SSO）。 您的使用者不需要登入每個應用程式。
- 裝置識別。 訊息代理程式會存取裝置憑證，這是在工作場所加入裝置時所建立的。
- 應用程式識別驗證。 當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 URL，而 broker 會進行驗證。

若要啟用其中一項功能，應用程式開發人員必須在呼叫 `PublicClientApplicationBuilder.CreateApplication` 方法時，使用 `WithBroker()` 參數。 預設會將 `.WithBroker()` 設為 true。 開發人員也需要遵循適用于[iOS](#brokered-authentication-for-ios)或[Android](#brokered-authentication-for-android)應用程式的步驟。

## <a name="brokered-authentication-for-ios"></a>適用于 iOS 的代理驗證

請遵循下列步驟來啟用您的 Xamarin iOS 應用程式，以與[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)應用程式通訊。

### <a name="step-1-enable-broker-support"></a>步驟1：啟用訊息代理程式支援
代理程式支援是以每個 PublicClientApplication 為基礎來啟用。 此功能預設為停用。 當您透過 PublicClientApplicationBuilder 建立 PublicClientApplication 時，請使用 `WithBroker()` 參數（預設設定為 true）。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>步驟2：啟用 keychain 存取

若要啟用 keychain 存取，您的應用程式必須具有 keychain 存取群組。 當您建立應用程式時，可以使用 `WithIosKeychainSecurityGroup()` API 來設定您的 keychain 存取群組：

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

如需詳細資訊，請參閱[Enable keychain access](msal-net-xamarin-ios-considerations.md#enable-keychain-access)。

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>步驟3：更新 AppDelegate 以處理回呼
當適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）呼叫訊息代理程式時，訊息代理程式會透過 `AppDelegate` 類別的 `OpenUrl` 方法，再次呼叫您的應用程式。 因為 MSAL 會等待訊息代理程式的回應，所以您的應用程式需要合作以呼叫 MSAL.NET 回來。 若要啟用這項合作，請更新 `AppDelegate.cs` 檔案以覆寫下列方法。

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

每次啟動應用程式時，就會叫用這個方法。 它是用來處理來自訊息代理程式之回應的機會，並完成 MSAL.NET 所起始的驗證程式。

### <a name="step-4-set-a-uiviewcontroller"></a>步驟4：設定 UIViewController （）
仍然在 `AppDelegate.cs`中，您必須設定 [物件] 視窗。 一般來說，在 Xamarin iOS 中，您不需要設定 [物件] 視窗。 若要傳送和接收來自訊息代理程式的回應，您需要物件視窗。 

若要這麼做，您要做兩件事。 
1. 在 `AppDelegate.cs`中，將 `App.RootViewController` 設定為新的 `UIViewController()`。 此指派可確保有 UIViewController 與訊息代理程式的呼叫。 如果設定不正確，您可能會收到此錯誤： `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. 在 AcquireTokenInteractive 呼叫上，使用 `.WithParentActivityOrWindow(App.RootViewController)`，並傳入您將使用之物件視窗的參考。

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

### <a name="step-5-register-a-url-scheme"></a>步驟5：註冊 URL 配置
MSAL.NET 會使用 Url 來叫用訊息代理程式，然後將 broker 回應傳回給您的應用程式。 若要完成來回行程，請在 `Info.plist` 檔案中註冊應用程式的 URL 配置。

`CFBundleURLSchemes` 名稱必須包含 `msauth.` 做為前置詞，後面接著您的 `CFBundleURLName`。

`$"msauth.(BundleId)"`

**例如：**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 配置會成為重新導向 URI 的一部分，當它收到來自訊息代理程式的回應時，它會用來唯一識別您的應用程式。

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步驟6：將 broker 識別碼新增至 LSApplicationQueriesSchemes 區段
MSAL 會使用 `–canOpenURL:` 來檢查代理程式是否已安裝在裝置上。 在 iOS 9 中，Apple 已鎖定應用程式可以查詢的配置。 

將 `msauthv2` 新增至 `Info.plist` 檔案的 [`LSApplicationQueriesSchemes`] 區段。

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>步驟7：在應用程式入口網站中註冊重新導向 URI
使用訊息代理程式會在您的重新導向 URI 上增加額外的需求。 重新導向 URI 的格式_必須_如下：
```csharp
$"msauth.{BundleId}://auth"
```
**例如：**
```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
請注意，重新導向 URI 會符合您在 `Info.plist` 檔案中包含的 `CFBundleURLSchemes` 名稱。

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>步驟8：確認重新導向 URI 已向您的應用程式註冊

此重新導向 URI 必須在應用程式註冊入口網站上註冊（ https://portal.azure.com) 為應用程式的有效重新導向 URI。 

入口網站有新的體驗應用程式註冊入口網站，可協助您從組合識別碼計算代理的回復 URI。

1. 在應用程式註冊中，選擇 [**驗證**]，然後選取 **[試用新的體驗**]。

   ![試用新的應用程式註冊體驗](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. 選取 [**新增平臺**]。

   ![新增平臺](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 當支援平臺清單時，請選取 [ **iOS**]。

   ![設定 iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 依要求輸入您的配套識別碼，然後選取 [**設定**]。

   ![輸入套件組合識別碼](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. 系統會為您計算重新導向 URI。

   ![複製重新導向 URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>適用于 Android 的代理驗證

MSAL.NET 目前僅支援 Xamarin iOS 平臺。 它尚不支援 Xamarin. Android 平臺的代理程式。

MSAL Android 原生程式庫已支援它。 如需詳細資訊，請參閱[在 Android 中](brokered-auth.md)進行代理驗證

## <a name="next-steps"></a>後續步驟

瞭解[MSAL.NET 的通用 Windows 平臺特定考慮](msal-net-uwp-considerations.md)。
