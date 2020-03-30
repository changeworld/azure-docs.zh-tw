---
title: 使用 Web 瀏覽器 （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 在將 Xamarin Android 與 .NET （MSAL.NET）的 Microsoft 身份驗證庫一起使用時，瞭解具體注意事項。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262733"
---
# <a name="using-web-browsers-msalnet"></a>使用 Web 瀏覽器 （MSAL.NET）

互動式身份驗證需要 Web 瀏覽器。 預設情況下，MSAL.NET支援 Xamarin.iOS 和 Xamarin.Android 上的[系統 Web 瀏覽器](#system-web-browser-on-xamarinios-xamarinandroid)。 但是[，您也可以](#enable-embedded-webviews-on-ios-and-android)根據您的要求（UX、[需要 Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios)和[Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)應用程式中的單一登入 （SSO） 安全性啟用嵌入式 Web 瀏覽器。 您甚至可以根據 Chrome 或支援 Android 中 Chrome 自訂標籤的瀏覽器的存在[動態選擇](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)要使用的網路瀏覽器。 MSAL.NET僅支援 .NET Core 桌面應用程式中的系統瀏覽器。

## <a name="web-browsers-in-msalnet"></a>MSAL.NET中的 Web 瀏覽器

### <a name="interaction-happens-in-a-web-browser"></a>交互在 Web 瀏覽器中發生

請務必瞭解，在以對話模式獲取權杖時，對話方塊的內容不是由庫提供，而是由 STS（安全權杖服務）提供的。 身份驗證終結點發送回一些 HTML 和 JavaScript，它們控制交互，這些交互在 Web 瀏覽器或 Web 控制項中呈現。 允許 STS 處理 HTML 交互有許多優點：

- 密碼（如果鍵入）永遠不會由應用程式存儲，也不會由身份驗證庫存儲。
- 啟用重定向到其他標識提供程式（例如，使用工作學校帳戶或 MSAL 的個人帳戶登錄，或使用 Azure AD B2C 的社交帳戶登錄）。
- 例如，讓 STS 控制條件訪問，例如，讓使用者在身份驗證階段執行多重要素驗證 （MFA）（輸入 Windows Hello 引腳，或在其手機上或在其手機上被調用）。 如果尚未設置所需的多重要素驗證，使用者可以在同一對話方塊中及時設置它。  使用者輸入其手機號碼，並引導使用者安裝身份驗證應用程式並掃描 QR 標記以添加其帳戶。 這種伺服器驅動的交互是一個偉大的體驗！
- 允許使用者在密碼過期時在此對話方塊中更改其密碼（為舊密碼和新密碼提供其他欄位）。
- 啟用租戶或由 Azure AD 租戶管理員/應用程式擁有者控制的應用程式（映射）的品牌。
- 允許使用者同意在身份驗證後允許應用程式以其名稱訪問資源/作用域。

### <a name="embedded-vs-system-web-ui"></a>嵌入式 vs 系統 Web UI

MSAL.NET是一個多框架庫，具有特定于框架的代碼，用於在 UI 控制項中託管瀏覽器（例如，在 .Net Classic 上，它使用 WinForms，在 Xamarin 上使用本機移動控制項等）。 此控制項稱為`embedded`Web UI。 或者，MSAL.NET也能夠啟動系統作業系統瀏覽器。

通常，建議您使用平臺預設值，這通常是系統瀏覽器。 系統瀏覽器更善於記住以前登錄的使用者。 如果需要更改此行為，請使用`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>速覽

| Framework        | 內嵌 | 系統 | 預設 |
| ------------- |-------------| -----| ----- |
| .NET 經典     | 是 | 是* | 內嵌 |
| .NET Core     | 否 | 是* | 系統 |
| .NET Standard | 否 | 是* | 系統 |
| UWP | 是 | 否 | 內嵌 |
| Xamarin.Android | 是 | 是  | 系統 |
| Xamarin.iOS | 是 | 是  | 系統 |
| Xamarin.Mac| 是 | 否 | 內嵌 |

• 需要http://localhost""重定向 URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin.iOS 的系統 Web 瀏覽器，Xamarin.安卓

預設情況下，MSAL.NET支援 Xamarin.iOS、Xamarin.Android 和 .NET Core 上的系統 Web 瀏覽器。 對於提供 UI 的所有平臺（即 .NET Core），嵌入 Web 瀏覽器控制項的庫提供對話方塊。 MSAL.NET還對 UWP 平臺的 .NET 桌面和 WAB 使用嵌入式 Web 視圖。 但是，預設情況下，它利用 Xamarin iOS 和 Xamarin Android 應用程式的**系統 Web 瀏覽器**。 在 iOS 上，它甚至根據作業系統的版本（iOS12、iOS11 和更早版本）選擇要使用的 Web 視圖。

使用系統瀏覽器具有與其他應用程式和 Web 應用程式共用 SSO 狀態的顯著優勢，而無需代理（公司門戶/身份驗證器）。 預設情況下，系統瀏覽器在 MSAL.NET中用於 Xamarin iOS 和 Xamarin Android 平臺，因為這些平臺上的系統 Web 瀏覽器佔據了整個螢幕，使用者體驗更好。 系統 Web 視圖與對話方塊無法區分。 但是，在 iOS 上，使用者可能必須同意瀏覽器回電應用程式，這可能會令人討厭。

## <a name="system-browser-experience-on-net-core"></a>.NET 核心上的系統瀏覽器體驗

在 .NET Core 上，MSAL.NET將啟動系統瀏覽器作為單獨的進程。 MSAL.NET無法控制此瀏覽器，但一旦使用者完成身份驗證，網頁就會重定向，以便MSAL.NET攔截 Uri。

您還可以通過指定

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET無法檢測使用者是導航離開還是簡單地關閉瀏覽器。 鼓勵使用此技術的應用定義超時（通過`CancellationToken`）。 我們建議超時至少幾分鐘，以考慮提示使用者更改密碼或執行多重要素驗證的情況。

### <a name="how-to-use-the-default-os-browser"></a>如何使用預設作業系統瀏覽器

MSAL.NET需要偵聽`http://localhost:port`並攔截使用者進行身份驗證時 AAD 發送的代碼（有關詳細資訊，請參閱[授權代碼](v2-oauth2-auth-code-flow.md)）

要啟用系統瀏覽器：

1. 在應用註冊期間，`http://localhost`配置為重定向 uri（當前不受 B2C 支援）
2. 構造 PublicClient應用程式時，請指定此重定向 uri：

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> 如果配置`http://localhost`，則內部MSAL.NET將找到一個隨機打開的埠並使用它。

### <a name="linux-and-mac"></a>Linux 和 MAC

在 Linux 上，MSAL.NET將使用 xdg 打開工具打開預設作業系統瀏覽器。 要進行故障排除，例如從終端運行該工具，`xdg-open "https://www.bing.com"`  
在 Mac 上，瀏覽器通過調用`open <url>`

### <a name="customizing-the-experience"></a>定制體驗

> [!NOTE]
> 自訂可在 MSAL.NET 4.1.0 或更高版本中提供。

MSAL.NET能夠在收到權杖或出現錯誤時使用 HTTP 消息進行回應。 您可以顯示 HTML 消息或重定向到您選擇的 URL：

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>打開特定的瀏覽器（實驗）

您可以自訂MSAL.NET打開瀏覽器的方式。 例如，您可以強制打開特定的瀏覽器，而不是使用預設的任何瀏覽器：

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP 不使用系統 Webview

但是，對於桌面應用程式，啟動 System Webview 會導致低於同等的使用者體驗，因為使用者看到瀏覽器，他們可能已經打開了其他選項卡。 當身份驗證發生時，使用者會收到一個頁面，要求他們關閉此視窗。 如果使用者不注意，他們可以關閉整個過程（包括與身份驗證無關的其他選項卡）。 利用桌面上的系統瀏覽器還需要打開本地埠並偵聽它們，這可能需要應用程式的高級許可權。 作為開發人員、使用者或管理員，您可能不願意滿足此要求。

## <a name="enable-embedded-webviews-on-ios-and-android"></a>在 iOS 和 Android 上啟用嵌入式網路視圖

您還可以在 Xamarin.iOS 和 Xamarin.Android 應用程式中啟用嵌入式 Web 視圖。 從 MSAL.NET 2.0.0 預覽開始，MSAL.NET還支援使用**嵌入式**Webview 選項。 對於ADAL.NET，嵌入式 Webview 是唯一支援的選項。

作為使用針對 Xamarin MSAL.NET的開發人員，您可以選擇使用嵌入式 Web 視圖或系統瀏覽器。 這是您的選擇，具體取決於要針對的使用者體驗和安全問題。

目前，MSAL.NET不支援 Android 和 iOS 代理。 因此，如果您需要提供單一登入 （SSO），系統瀏覽器可能仍是一個更好的選項。 支援具有嵌入式 Web 瀏覽器的代理位於MSAL.NET積壓工作上。

### <a name="differences-between-embedded-webview-and-system-browser"></a>嵌入式網路視圖和系統瀏覽器之間的差異
嵌入式 Webview 和系統瀏覽器在MSAL.NET存在一些視覺差異。

**使用嵌入式 Webview 使用MSAL.NET進行互動式登入：**

![內嵌](media/msal-net-web-browsers/embedded-webview.png)

**使用系統瀏覽器使用MSAL.NET進行互動式登入：**

![系統瀏覽器](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>開發人員選項

作為使用MSAL.NET的開發人員，您可以通過多個選項來顯示來自 STS 的互動式對話方塊：

- **系統瀏覽器。** 預設情況下，系統瀏覽器在庫中設置。 如果使用 Android，請閱讀[系統瀏覽器](msal-net-system-browser-android-considerations.md)，瞭解有關哪些瀏覽器受支援進行身份驗證的特定資訊。 在 Android 中使用系統瀏覽器時，我們建議該設備具有支援 Chrome 自訂標籤選項卡的瀏覽器。  否則，身份驗證可能會失敗。
- **嵌入式網頁視圖。** 要在MSAL.NET中使用嵌入式 Webview，`AcquireTokenInteractively`參數產生器包含一個`WithUseEmbeddedWebView()`方法。

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android：

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>在 Xamarin.iOS 上的嵌入式 Web 瀏覽器或系統瀏覽器之間進行選擇

在 iOS 應用中，`AppDelegate.cs`您可以在 中初始化`ParentWindow``null`到 。 它不在 iOS 中使用

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>在 Xamarin.Android 上的嵌入式 Web 瀏覽器或系統瀏覽器之間進行選擇

在 Android 應用中，`MainActivity.cs`您可以在 中設置父活動，以便身份驗證結果返回：

```csharp
 App.ParentWindow = this;
```

然後在 中`MainPage.xaml.cs`。

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>檢測 Xamarin.Android 上是否存在自訂選項卡

如果要使用系統 Web 瀏覽器啟用 SSO，應用在瀏覽器中運行，但擔心 Android 設備的使用者體驗沒有自訂選項卡支援的瀏覽器，則可以選擇通過在 中`IsSystemWebViewAvailable()``IPublicClientApplication`調用 方法來決定。 如果 PackageManager 檢測到自訂選項卡，並且`true``false`設備上未檢測到它們，則此方法將返回。

根據此方法返回的值和您的要求，您可以做出以下決定：

- 您可以向使用者返回自訂錯誤訊息。 例如："請安裝 Chrome 以繼續認證" -OR-
- 您可以回退到嵌入式 Webview 選項，並將 UI 作為嵌入式 Webview 啟動。

下面的代碼顯示了嵌入式 Webview 選項：

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core 不支援使用嵌入式瀏覽器進行互動式身份驗證

對於 .NET Core，僅通過系統 Web 瀏覽器（而不是嵌入式 Web 視圖）以對話模式獲取權杖。 事實上，.NET Core 尚未提供 UI。
如果要使用系統 Web 瀏覽器自訂流覽體驗，可以實現[IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui)介面，甚至提供您自己的瀏覽器。
