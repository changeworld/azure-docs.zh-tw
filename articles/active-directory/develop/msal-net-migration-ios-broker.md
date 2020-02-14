---
title: 使用訊息代理程式將 Xamarin 應用程式遷移到 MSAL.NET
titleSuffix: Microsoft identity platform
description: 瞭解如何將使用 ADAL.NET Microsoft Authenticator 的 Xamarin iOS 應用程式遷移至 MSAL.NET。
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
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185836"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>將使用 Microsoft Authenticator 從 ADAL.NET 到 MSAL.NET 的 iOS 應用程式遷移

您已使用適用于 .NET （ADAL.NET）和 iOS 訊息代理程式的 Azure Active Directory Authentication Library。 現在可以開始遷移至適用于 .NET 的[Microsoft 驗證程式庫](msal-overview.md)（MSAL.NET），這可從發行4.3 開始支援 iOS 上的訊息代理程式。 

您應該從何處著手？ 本文可協助您將 Xamarin iOS 應用程式從 ADAL 遷移至 MSAL。

## <a name="prerequisites"></a>必要條件
本文假設您已經有與 iOS broker 整合的 Xamarin iOS 應用程式。 如果您沒有這麼做，請直接移至 MSAL.NET，並在該處開始執行代理程式。 如需如何使用新的應用程式在 MSAL.NET 中叫用 iOS broker 的詳細資訊，請參閱[此檔](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)。

## <a name="background"></a>背景

### <a name="what-are-brokers"></a>什麼是訊息代理程式？

訊息代理程式是由 Microsoft 在 Android 和 iOS 上提供的應用程式。 （請參閱 iOS 和 Android 上的[Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)應用程式和 android 上的 Intune 公司入口網站應用程式）。 

它們會啟用：

- 單一登入。
- 某些[條件式存取原則](../conditional-access/overview.md)所需的裝置識別。 如需詳細資訊，請參閱[裝置管理](../conditional-access/concept-conditional-access-conditions.md#device-platforms)。
- 應用程式識別驗證，在某些企業案例中也是必要的。 如需詳細資訊，請參閱[Intune 行動應用程式管理（MAM）](https://docs.microsoft.com/intune/mam-faq)。

## <a name="migrate-from-adal-to-msal"></a>從 ADAL 遷移至 MSAL

### <a name="step-1-enable-the-broker"></a>步驟1：啟用訊息代理程式

<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
在 ADAL.NET 中，已根據每個驗證內容來啟用 broker 支援。 此功能預設為停用。 您必須設定 

在 `PlatformParameters` 的函式中，`useBroker` 旗標設為 true，以呼叫訊息代理程式：

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
此外，在此範例的平臺特定程式碼中，于 iOS 的頁面轉譯器中設定 `useBroker` 
旗標設為 true：
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

然後，在取得權杖呼叫中包含參數：
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
在 MSAL.NET 中，會以每個 PublicClientApplication 為基礎啟用訊息代理程式支援。 此功能預設為停用。 若要啟用它，請使用 

`WithBroker()` 參數（預設為 true），以便呼叫訊息代理程式：

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
在取得權杖呼叫中：
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>步驟2：設定 UIViewController （）
在 ADAL.NET 中，您是以 `PlatformParameters`的一部分傳入 UIViewController。 （請參閱步驟1中的範例）。在 MSAL.NET 中，為了讓開發人員有更大的彈性，會使用物件視窗，但是一般 iOS 使用方式並不需要。 若要使用訊息代理程式，請設定 [物件] 視窗，以便傳送和接收來自訊息代理程式的回應。 
<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
UIViewController 會傳入 

在 iOS 特定平臺中 `PlatformParameters`。

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
在 MSAL.NET 中，您會執行兩件事來設定 iOS 的物件視窗：

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

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>步驟3：更新 AppDelegate 以處理回呼
ADAL 和 MSAL 都會呼叫 broker，而訊息代理程式會透過 `AppDelegate` 類別的 `OpenUrl` 方法，再次呼叫您的應用程式。 如需詳細資訊，請參閱[這份文件](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)。

ADAL.NET 與 MSAL.NET 之間沒有任何變更。

### <a name="step-4-register-a-url-scheme"></a>步驟4：註冊 URL 配置
ADAL.NET 和 MSAL.NET 會使用 Url 叫用訊息代理程式，並將 broker 回應傳回給應用程式。 在應用程式的 `Info.plist` 檔案中註冊 URL 配置，如下所示：

<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
URL 配置對您的應用程式而言是唯一的。
</td><td>
該 

`CFBundleURLSchemes` 名稱必須包含 

`msauth.`

做為前置詞，後面接著您的 `CFBundleURLName`

例如： `$"msauth.(BundleId")`

```csharp
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

> [!NOTE]
> 此 URL 配置會成為重新導向 URI 的一部分，當它收到來自訊息代理程式的回應時，它會用來唯一識別該應用程式。

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步驟5：將 broker 識別碼新增至 LSApplicationQueriesSchemes 區段

ADAL.NET 和 MSAL.NET 都使用 `-canOpenURL:` 來檢查訊息代理程式是否已安裝在裝置上。 將 iOS 代理程式的正確識別碼新增至 plist 檔案的 LSApplicationQueriesSchemes 區段，如下所示：

<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
使用 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
使用 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>步驟6：在入口網站中註冊您的重新導向 URI

ADAL.NET 和 MSAL.NET 會在以訊息代理程式為目標時，對重新導向 URI 增加額外的需求。 在入口網站中向您的應用程式註冊重新導向 URI。
<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

範例： 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

範例：

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

如需如何在入口網站中註冊重新導向 URI 的詳細資訊，請參閱在[Xamarin iOS 應用程式中運用訊息代理程式](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app)。

## <a name="next-steps"></a>後續步驟

瞭解有關[MSAL.NET 的 Xamarin iOS 特定考慮](msal-net-xamarin-ios-considerations.md)。 
