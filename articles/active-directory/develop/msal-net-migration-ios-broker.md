---
title: 使用訊息代理程式將 Xamarin 應用程式遷移到 MSAL.NET
titleSuffix: Microsoft identity platform
description: 瞭解如何將使用 Microsoft 身份驗證器的 Xamarin iOS 應用從ADAL.NET遷移到MSAL.NET。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185836"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>使用 Microsoft 身份驗證器的 iOS 應用程式從ADAL.NET遷移到MSAL.NET

您一直在使用 Azure 活動目錄身份驗證庫，用於 .NET （ADAL.NET） 和 iOS 代理。 現在是時候遷移到[微軟身份驗證庫](msal-overview.md)為 .NET （MSAL.NET），它支援從版本 4.3 開始的 iOS 上的代理。 

你應該從哪裡開始？ 本文可説明您將 Xamarin iOS 應用從 ADAL 遷移到 MSAL。

## <a name="prerequisites"></a>Prerequisites
本文假定您已經擁有了與 iOS 代理集成的 Xamarin iOS 應用程式。 如果沒有，則直接移動到MSAL.NET並開始代理實現。 有關如何使用新應用程式調用 MSAL.NET iOS 代理的資訊，請參閱[本文檔](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)。

## <a name="background"></a>背景

### <a name="what-are-brokers"></a>什麼是經紀人？

經紀商是微軟在Android和iOS上提供的應用程式。 （請參閱 iOS 和 Android 上的[Microsoft 身份驗證器](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)應用，以及 Android 上的 Intune 公司門戶應用。 

它們支援：

- 單一登入。
- 設備標識，這是某些[條件訪問策略](../conditional-access/overview.md)所必需的。 有關詳細資訊，請參閱[裝置管理](../conditional-access/concept-conditional-access-conditions.md#device-platforms)。
- 應用程式標識驗證，在某些企業方案中也需要驗證。 有關詳細資訊，請參閱[Intune 移動應用程式管理 （MAM）。](https://docs.microsoft.com/intune/mam-faq)

## <a name="migrate-from-adal-to-msal"></a>從 ADAL 遷移到 MSAL

### <a name="step-1-enable-the-broker"></a>第 1 步：啟用代理

<table>
<tr><td>當前 ADAL 代碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
在ADAL.NET，基於每個身份驗證上下文啟用了代理支援。 此功能預設為停用。 你必須設置一個 

`useBroker`在建構函式中`PlatformParameters`標記為 true，以調用代理：

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
此外，在特定于平臺的代碼中，在此示例中，在 iOS 的頁面呈現器中，設置`useBroker` 
標誌為 true：
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

然後，在獲取權杖調用中包括參數：
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
在MSAL.NET，基於每個公共用戶端應用程式啟用代理支援。 此功能預設為停用。 要啟用它，請使用 

`WithBroker()`參數（預設情況下設置為 true），以便調用代理：

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
在獲取權杖調用中：
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>第 2 步：設置 UIView 控制器（）
在 ADAL.NET 中，您作為 中的一部分`PlatformParameters`傳入 UIViewController。 （請參閱步驟 1 中的示例。在MSAL.NET，為了給開發人員更大的靈活性，使用了物件視窗，但在常規的 iOS 使用中不需要它。 要使用代理，請設置物件視窗以發送和接收來自代理的回應。 
<table>
<tr><td>當前 ADAL 代碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
UIView控制器被傳遞到 

`PlatformParameters`在 iOS 特定的平臺上。

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
在MSAL.NET中，您可以執行兩項操作來設置 iOS 的物件視窗：

1. 在`AppDelegate.cs`中`App.RootViewController`，設置為新`UIViewController()`。 此分配可確保有一個 UIViewController 與調用代理。 如果未正確設置，您可能會收到此錯誤：`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. 在獲取權杖互動式調用上，使用`.WithParentActivityOrWindow(App.RootViewController)`並傳遞對要使用的物件視窗的引用。

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
在獲取權杖調用中：
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>第 3 步：更新應用委託以處理回檔
ADAL 和 MSAL 都調用代理，而代理反過來又通過`OpenUrl``AppDelegate`類的方法回檔您的應用程式。 有關詳細資訊，請參閱[本文檔](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)。

ADAL.NET和MSAL.NET之間沒有變化。

### <a name="step-4-register-a-url-scheme"></a>第 4 步：註冊 URL 方案
ADAL.NET和MSAL.NET使用 URL 調用代理並將代理回應返回到應用。 在應用的`Info.plist`檔中註冊 URL 方案，如下所示：

<table>
<tr><td>當前 ADAL 代碼：</td><td>MSAL 對應：</td></tr>
<tr><td>
URL 方案對應用是唯一的。
</td><td>
此 

`CFBundleURLSchemes`名稱必須包括 

`msauth.`

作為首碼，後跟`CFBundleURLName`

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
> 此 URL 方案將成為重定向 URI 的一部分，用於在應用收到來自代理的回應時唯一標識應用。

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步驟 5：將代理識別碼添加到 LS應用程式查詢計劃部分

ADAL.NET和MSAL.NET都用於`-canOpenURL:`檢查代理是否安裝在設備上。 將 iOS 代理的正確識別碼添加到 info.plist 檔的 LS應用程式查詢方案部分，如下所示：

<table>
<tr><td>當前 ADAL 代碼：</td><td>MSAL 對應：</td></tr>
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

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>第 6 步：在門戶中註冊重定向 URI

ADAL.NET和MSAL.NET都增加了重定向 URI 的額外要求，當它針對代理時。 在門戶中將重定向 URI 與應用程式註冊。
<table>
<tr><td>當前 ADAL 代碼：</td><td>MSAL 對應：</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

範例： 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

範例：

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

有關如何在門戶中註冊重定向 URI 的詳細資訊，請參閱[在 Xamarin.iOS 應用程式中利用代理](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app)。

## <a name="next-steps"></a>後續步驟

瞭解[與MSAL.NET一起針對 Xamarin iOS 的注意事項](msal-net-xamarin-ios-considerations.md)。 
