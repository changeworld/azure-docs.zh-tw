---
title: 使用訊息代理程式將 Xamarin 應用程式遷移到 MSAL.NET
titleSuffix: Microsoft identity platform
description: 瞭解如何將使用 Microsoft Authenticator 的 Xamarin iOS 應用程式從 ADAL.NET 遷移至 MSAL.NET。
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b4eff5910ff5230902d497b55b2afbe6d605365a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89177426"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>將使用 Microsoft Authenticator 的 iOS 應用程式從 ADAL.NET 遷移至 MSAL.NET

您已使用適用于 .NET (ADAL.NET) 和 iOS broker 的 Azure Active Directory Authentication Library。 現在可以開始遷移至適用于 .NET 的 [Microsoft 驗證程式庫](msal-overview.md) (MSAL.NET) ，它支援 iOS 上的 broker （從4.3 版開始）。

您該從何處著手？ 本文可協助您將 Xamarin iOS 應用程式從 ADAL 遷移至 MSAL。

## <a name="prerequisites"></a>必要條件
本文假設您已經有整合了 iOS broker 的 Xamarin iOS 應用程式。 如果您沒有這麼做，請直接移至 MSAL.NET，並在該處開始 broker 執行。 如需如何使用新的應用程式在 MSAL.NET 中叫用 iOS broker 的詳細資訊，請參閱 [此檔](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)。

## <a name="background"></a>背景

### <a name="what-are-brokers"></a>什麼是訊息代理程式？

訊息代理程式是 Microsoft 在 Android 和 iOS 上提供的應用程式。  (查看 iOS 和 Android 上的 [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) 應用程式，以及 Android 上的 Intune 公司入口網站應用程式。 ) 

他們會啟用：

- 單一登入。
- 某些 [條件式存取原則](../conditional-access/overview.md)所需的裝置識別。 如需詳細資訊，請參閱 [裝置管理](../conditional-access/concept-conditional-access-conditions.md#device-platforms)。
- 應用程式識別驗證，在某些企業案例中也是必要的。 如需詳細資訊，請參閱 [Intune 行動應用程式管理 (MAM) ](/intune/mam-faq)。

## <a name="migrate-from-adal-to-msal"></a>從 ADAL 遷移至 MSAL

### <a name="step-1-enable-the-broker"></a>步驟1：啟用訊息代理程式

<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
在 ADAL.NET 中，每次驗證內容都啟用了 broker 支援。 此功能預設為停用。 您必須設定

`useBroker` 在用來呼叫訊息代理程式的函式中，將旗標設為 true `PlatformParameters` ：

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
此外，在此範例中的平臺特定程式碼中，于 iOS 的頁面轉譯器中，設定 `useBroker`
將旗標設為 true：
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

然後，將參數包含在取得權杖呼叫中：
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
在 MSAL.NET 中，會以每個 PublicClientApplication 為基礎啟用 broker 支援。 此功能預設為停用。 若要啟用它，請使用

`WithBroker()` 依預設，參數 (設定為 true) ，以便呼叫 broker：

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

### <a name="step-2-set-a-uiviewcontroller"></a>步驟2：設定 UIViewController ( # A1
在 ADAL.NET 中，您會在中傳入 UIViewController `PlatformParameters` 。  (請參閱 MSAL.NET 中的步驟 ) 1 中的範例，為開發人員提供更大的彈性，使用物件視窗，但在一般的 iOS 使用方式下則不需要。 若要使用訊息代理程式，請設定物件視窗，以便從 broker 傳送和接收回應。
<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
UIViewController 會傳入

`PlatformParameters` 在 iOS 特定平臺中。

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
在 MSAL.NET 中，您可以執行兩項作業來設定 iOS 的物件視窗：

1. 在中 `AppDelegate.cs` ，將設定 `App.RootViewController` 為新的 `UIViewController()` 。
此指派可確保有 UIViewController 與 broker 的呼叫。 如果設定不正確，您可能會收到此錯誤： `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. 在 AcquireTokenInteractive 呼叫上，使用 `.WithParentActivityOrWindow(App.RootViewController)` ，並傳入您將使用之物件視窗的參考。

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
ADAL 和 MSAL 都會呼叫訊息代理程式，而 broker 接著會透過類別的方法，回呼至您的應用程式 `OpenUrl` `AppDelegate` 。 如需詳細資訊，請參閱 [此檔](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)。

ADAL.NET 與 MSAL.NET 之間沒有任何變更。

### <a name="step-4-register-a-url-scheme"></a>步驟4：註冊 URL 配置
ADAL.NET 和 MSAL.NET 會使用 Url 叫用訊息代理程式，並將訊息代理程式回應傳回給應用程式。 在應用程式的檔案中註冊 URL 配置， `Info.plist` 如下所示：

<table>
<tr><td>目前的 ADAL 程式碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
URL 配置對您的應用程式而言是唯一的。
</td><td>
必須提供

`CFBundleURLSchemes` 名稱必須包含

`msauth.`

作為前置詞，後面接著 `CFBundleURLName`

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
> 此 URL 配置會成為重新導向 URI 的一部分，當應用程式收到來自訊息代理程式的回應時，它會用來唯一識別該應用程式。

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步驟5：將 broker 識別碼新增至 LSApplicationQueriesSchemes 區段

ADAL.NET 和 MSAL.NET 都 `-canOpenURL:` 是用來檢查訊息代理程式是否已安裝在裝置上。 將正確的 iOS broker 識別碼新增至 plist 檔案的 LSApplicationQueriesSchemes 區段，如下所示：

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

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>步驟6：在 Azure 入口網站中註冊您的重新導向 URI

ADAL.NET 和 MSAL.NET 會在以訊息代理程式為目標時，對重新導向 URI 新增額外的需求。 在 Azure 入口網站中向您的應用程式註冊重新導向 URI。
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

如需如何在 Azure 入口網站中註冊重新導向 URI 的詳細資訊，請參閱 [步驟7：將重新導向 Uri 新增至您的應用程式註冊](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration)。

### <a name="step-7-set-the-entitlementsplist"></a>**步驟7：設定權利. plist**

在 *plist* 檔案中啟用 keychain 存取：

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

如需啟用 keychain 存取的詳細資訊，請參閱 [啟用 keychain 存取](msal-net-xamarin-ios-considerations.md#enable-keychain-access)。

## <a name="next-steps"></a>後續步驟

瞭解 [MSAL.NET 的 Xamarin iOS 特定考慮](msal-net-xamarin-ios-considerations.md)。