---
title: 使用網頁瀏覽器（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）使用 Xamarin Android 時的特定考慮。
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262733"
---
# <a name="using-web-browsers-msalnet"></a>使用網頁瀏覽器（MSAL.NET）

需要 Web 瀏覽器才能進行互動式驗證。 根據預設，MSAL.NET 支援 Xamarin 和 Xamarin 上的[系統網頁瀏覽器](#system-web-browser-on-xamarinios-xamarinandroid)。 但您也可以根據您的需求（UX、需要單一登入（SSO）、安全性）在[xamarin](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios)和[xamarin](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)應用程式中[啟用內嵌的網頁瀏覽器](#enable-embedded-webviews-on-ios-and-android)。 此外，您甚至可以根據 Chrome 是否存在，或在 Android 中支援 Chrome 自訂索引標籤的瀏覽器，[動態地選擇](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)要使用的網頁瀏覽器。 MSAL.NET 只支援 .NET Core 桌面應用程式中的系統瀏覽器。

## <a name="web-browsers-in-msalnet"></a>MSAL.NET 中的網頁瀏覽器

### <a name="interaction-happens-in-a-web-browser"></a>Web 瀏覽器中發生的互動

請務必瞭解，以互動方式取得權杖時，程式庫不會提供對話方塊的內容，而是由 STS （安全性權杖服務）提供。 驗證端點會傳回一些 HTML 和 JavaScript，以控制在網頁瀏覽器或 web 控制項中呈現的互動。 允許 STS 處理 HTML 互動有許多優點：

- 應用程式（或驗證程式庫）絕不會儲存密碼（如果有輸入的話）。
- 可讓您重新導向至其他身分識別提供者（例如，使用工作學校帳戶登入，或使用 MSAL 的個人帳戶或具有 Azure AD B2C 的社交帳戶）。
- 可讓 STS 控制條件式存取，例如在驗證階段期間讓使用者執行多重要素驗證（MFA）（輸入 Windows Hello pin 碼，或在電話上呼叫，或在電話上的驗證應用程式上呼叫）。 在所需的多重要素驗證尚未設定的情況下，使用者可以在同一個對話方塊中及時設定。  使用者輸入其行動電話號碼，並引導您安裝驗證應用程式，並掃描 QR 標記以新增其帳戶。 此伺服器驅動的互動是絕佳的體驗！
- 讓使用者在密碼過期時，在這個相同的對話方塊中變更其密碼（提供舊密碼和新密碼的其他欄位）。
- 啟用租使用者的商標，或由 Azure AD 租使用者系統管理員/應用程式擁有者所控制的應用程式（映射）。
- 讓使用者同意在驗證之後，讓應用程式存取其名稱中的資源/範圍。

### <a name="embedded-vs-system-web-ui"></a>內嵌 vs System Web UI

MSAL.NET 是多架構程式庫，而且具有架構專屬的程式碼，可在 UI 控制項中裝載瀏覽器（例如，在 .Net 傳統上，它會使用 WinForms，而在 Xamarin 上會使用原生行動控制項等等）。 此控制項稱為 `embedded` web UI。 或者，MSAL.NET 也可以啟動系統作業系統瀏覽器。

一般來說，建議您使用平臺預設值，這通常是系統瀏覽器。 系統瀏覽器更適合用來記住先前登入的使用者。 如果您需要變更此行為，請使用 `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>速覽

| Framework        | 內嵌 | 系統 | 預設 |
| ------------- |-------------| -----| ----- |
| .NET 傳統     | 是 | 是 ^ | 內嵌 |
| .NET Core     | 否 | 是 ^ | 系統 |
| .NET Standard | 否 | 是 ^ | 系統 |
| UWP | 是 | 否 | 內嵌 |
| Xamarin.Android | 是 | 是  | 系統 |
| Xamarin.iOS | 是 | 是  | 系統 |
| Xamarin. Mac| 是 | 否 | 內嵌 |

^ 需要 "http://localhost" 重新導向 URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin 上的系統網頁瀏覽器、Xamarin. Android

根據預設，MSAL.NET 支援 Xamarin、Xamarin 和 .NET Core 上的系統網頁瀏覽器。 對於提供 UI 的所有平臺（也就是不是 .NET Core），對話方塊是由內嵌 Web 瀏覽器控制項的程式庫所提供。 MSAL.NET 也會針對適用于 UWP 平臺的 .NET 桌面和 WAB 使用內嵌的 web view。 不過，它會針對 Xamarin iOS 和 Xamarin Android 應用程式，利用預設的**系統網頁瀏覽器**。 在 iOS 上，它甚至會根據作業系統的版本（iOS12、iOS11 和更早版本）選擇要使用的 web view。

使用 [系統瀏覽器] 的優點是與其他應用程式和 web 應用程式共用 SSO 狀態，而不需要 broker （公司入口網站/驗證器）。 根據預設，系統瀏覽器在 Xamarin iOS 和 Xamarin Android 平臺的 MSAL.NET 中使用，因為在這些平臺上，系統網頁瀏覽器會佔用整個畫面，而使用者體驗會更好。 系統 web view 無法與對話方塊區別。 不過，在 iOS 上，使用者可能必須同意讓瀏覽器回呼應用程式，這可能會令人討厭。

## <a name="system-browser-experience-on-net-core"></a>.NET Core 上的系統瀏覽器體驗

在 .NET Core 上，MSAL.NET 會以個別的進程啟動系統瀏覽器。 MSAL.NET 無法控制此瀏覽器，但一旦使用者完成驗證，就會以 MSAL.NET 可攔截 Uri 的方式重新導向該網頁。

您也可以藉由指定下列方式，設定針對 .NET 傳統撰寫的應用程式來使用此瀏覽器

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET 無法偵測使用者是否流覽離開，或只是關閉瀏覽器。 建議使用這項技術的應用程式定義超時（透過 `CancellationToken`）。 我們建議至少有幾分鐘的時間，將提示使用者變更密碼或執行多重要素驗證的情況納入考慮。

### <a name="how-to-use-the-default-os-browser"></a>如何使用預設的 OS 瀏覽器

MSAL.NET 必須接聽 `http://localhost:port` 並攔截當使用者完成驗證時，AAD 所傳送的程式碼（如需詳細資訊，請參閱[授權碼](v2-oauth2-auth-code-flow.md)）

若要啟用系統瀏覽器：

1. 在應用程式註冊期間，將 `http://localhost` 設定為重新導向 uri （B2C 目前不支援）
2. 當您建立 PublicClientApplication 時，請指定此重新導向 uri：

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> 如果您設定 `http://localhost`，內部 MSAL.NET 會尋找隨機開啟的埠並加以使用。

### <a name="linux-and-mac"></a>Linux 和 MAC

在 Linux 上，MSAL.NET 會使用 xdg 開啟工具來開啟預設的 OS 瀏覽器。 若要進行疑難排解，請從終端機執行此工具，例如，`xdg-open "https://www.bing.com"`  
在 Mac 上，藉由叫用來開啟瀏覽器 `open <url>`

### <a name="customizing-the-experience"></a>自訂體驗

> [!NOTE]
> 自訂適用于 MSAL.NET 4.1.0 或更新版本。

當收到權杖或發生錯誤時，MSAL.NET 能夠以 HTTP 訊息回應。 您可以顯示 HTML 訊息，或重新導向至您選擇的 url：

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

### <a name="opening-a-specific-browser-experimental"></a>開啟特定瀏覽器（實驗性）

您可以自訂 MSAL.NET 開啟瀏覽器的方式。 例如，您可以強制開啟特定的瀏覽器，而不是使用預設的任何瀏覽器：

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP 不會使用系統的 Web 工作

不過，針對桌面應用程式，啟動系統 Web 工作會導致欠佳的使用者體驗，因為使用者會看到瀏覽器，其中可能已經開啟其他索引標籤。 當驗證發生時，使用者會收到一頁要求他們關閉此視窗。 如果使用者不注意，他們可以關閉整個程式（包括與驗證無關的其他索引標籤）。 利用桌上型電腦上的系統瀏覽器也需要開啟本機埠並在其上接聽，這可能需要應用程式的「高級」許可權。 身為開發人員、使用者或系統管理員的您，可能不願意擔心這項需求。

## <a name="enable-embedded-webviews-on-ios-and-android"></a>啟用 iOS 和 Android 上的內嵌網站

您也可以啟用 Xamarin. iOS 和 Xamarin Android 應用程式中的內嵌網站。 從 MSAL.NET 2.0.0-preview 開始，MSAL.NET 也支援使用**內嵌**的 web 流覽選項。 針對 ADAL.NET，內嵌的 web 工作是唯一支援的選項。

身為使用 MSAL.NET 以 Xamarin 為目標的開發人員，您可以選擇使用內嵌的網站或系統瀏覽器。 這是您選擇的選項，視您想要作為目標的使用者經驗和安全性考慮而定。

目前，MSAL.NET 尚不支援 Android 和 iOS 代理程式。 因此，如果您需要提供單一登入（SSO），則系統瀏覽器可能仍然是較好的選項。 使用內嵌的網頁瀏覽器支援訊息代理程式位於 MSAL.NET 待處理專案。

### <a name="differences-between-embedded-webview-and-system-browser"></a>內嵌的 web 流覽與系統瀏覽器之間的差異
在 MSAL.NET 中，內嵌的 web 流覽與系統瀏覽器之間有一些視覺上的差異。

**使用內嵌的 Web 工作，以 MSAL.NET 進行互動式登入：**

![內嵌](media/msal-net-web-browsers/embedded-webview.png)

**使用系統瀏覽器以 MSAL.NET 進行互動式登入：**

![系統瀏覽器](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>開發人員選項

身為使用 MSAL.NET 的開發人員，您有數個選項可顯示 STS 的互動式對話方塊：

- **系統瀏覽器。** 預設會在程式庫中設定系統瀏覽器。 如果使用 Android，請參閱[系統流覽](msal-net-system-browser-android-considerations.md)器，以取得支援驗證之瀏覽器的特定資訊。 在 Android 中使用系統瀏覽器時，我們建議裝置具有支援 Chrome 自訂索引標籤的瀏覽器。  否則，驗證可能會失敗。
- **內嵌的 web，** 若只要在 MSAL.NET 中使用內嵌的 web 工作，`AcquireTokenInteractively` 參數產生器會包含 `WithUseEmbeddedWebView()` 方法。

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>在 Xamarin 的 embedded web 瀏覽器或系統瀏覽器之間選擇

在 iOS 應用程式的 `AppDelegate.cs` 中，您可以初始化 `null`的 `ParentWindow`。 不適用於 iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>在 Xamarin 上的內嵌網頁瀏覽器或系統瀏覽器之間選擇

在 Android 應用程式的 `MainActivity.cs` 中，您可以設定父活動，以便驗證結果會回到它：

```csharp
 App.ParentWindow = this;
```

然後在 `MainPage.xaml.cs`：

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>偵測 Xamarin 上的自訂索引標籤是否存在

如果您想要使用系統 web 瀏覽器，讓應用程式能與在瀏覽器中執行的應用程式啟用 SSO，但擔心 Android 裝置的使用者體驗不具有自訂索引標籤支援的瀏覽器，您可以選擇在 `IPublicClientApplication`中呼叫 `IsSystemWebViewAvailable()` 方法來決定。 如果 PackageManager 偵測到自訂索引標籤，並 `false` 未在裝置上偵測到，則此方法會傳回 `true`。

根據此方法所傳回的值，以及您的需求，您可以進行決策：

- 您可以將自訂錯誤訊息傳回給使用者。 例如：「請安裝 Chrome 以繼續驗證」-或-
- 您可以切換回內嵌的 [web 工作] 選項，並以內嵌的 web 程式形式啟動 UI。

下列程式碼顯示內嵌的 web 程式選項：

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core 不支援使用內嵌瀏覽器的互動式驗證

針對 .NET Core，以互動方式取得權杖只會透過系統網頁瀏覽器（而不是內嵌的 web views）提供。 事實上，.NET Core 尚未提供 UI。
如果您想要使用系統網頁瀏覽器自訂流覽體驗，您可以執行[IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui)介面，甚至提供您自己的瀏覽器。
