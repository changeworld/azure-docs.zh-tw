---
title: 使用網頁瀏覽器 (MSAL.NET) |Azure
titleSuffix: Microsoft identity platform
description: 瞭解使用 Xamarin Android 搭配適用於 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 時的特定考量。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 4e62536b610595c7a53eb8333f06f147e628dec7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772041"
---
# <a name="using-web-browsers-msalnet"></a>使用網頁瀏覽器 (MSAL.NET)

互動式驗證一定會使用到網頁瀏覽器。 依照預設，MSAL.NET 支援在 Xamarin.iOS 和 Xamarin.Android 上的[系統網頁瀏覽器](#system-web-browser-on-xamarinios-xamarinandroid)。 但是，您也可以依照您的需求 (UX、需要單一登入 (SSO)、安全性等)，在 [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) 和 [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) 應用程式中，[啟用內嵌的網頁瀏覽器](#enable-embedded-webviews-on-ios-and-android)。 此外，您甚至可以根據 Chrome 的呈現方式，或支援在 Android 中具有 Chrome 自訂索引標籤的瀏覽器，選擇要以[動態方式](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)使用的網頁瀏覽器。 MSAL.NET 只支援 .NET Core 桌面應用程式中的系統瀏覽器。

## <a name="web-browsers-in-msalnet"></a>MSAL.NET 中的網頁瀏覽器

### <a name="interaction-happens-in-a-web-browser"></a>網頁瀏覽器中發生的互動

請務必瞭解，以互動方式取得權杖時，程式庫不會提供對話方塊的內容，而是由 STS (安全性權杖服務) 提供。 驗證端點會傳回一些控制互動的 HTML 和 JavaScript，並將互動轉譯於網頁瀏覽器或網頁控制項中。 允許 STS 處理 HTML 互動有許多優點：

- 若有輸入密碼，應用程式和驗證程式庫絕對不會儲存密碼。
- 可讓您重新導向至其他身分識別提供者 (例如，使用公司/學校帳戶、具有 MSAL 的個人帳戶，或具有 Azure AD B2C 的社交帳戶登入)。
- 可讓 STS 控制條件式存取，例如，讓使用者在驗證階段 (輸入 Windows Hello PIN 碼、撥打電話，或使用電話上的驗證應用程式) 進行[多重要素驗證 (MFA)](../authentication/concept-mfa-howitworks.md)。 在尚未設定所需多重要素驗證的情況下，使用者可以在相同對話方塊中及時設定。  讓使用者在輸入其行動電話號碼後，引導至安裝驗證應用程式，並以掃描 QR 標記的方式新增其帳戶。 這是絕佳的伺服器導向互動體驗！
- 讓使用者在密碼過期時，在相同對話方塊中變更密碼 (提供舊密碼和新密碼額外的欄位)。
- 啟用租用戶的商標，或是由 Azure AD 租用戶系統管理員/應用程式擁有者所控制的應用程式 (影像)。
- 讓使用者同意在驗證之後，讓應用程式存取其名稱中的資源/範圍。

### <a name="embedded-vs-system-web-ui"></a>內嵌與系統 Web UI 的比較

MSAL.NET 是多架構程式庫，且具有架構專屬程式碼，可在 UI 控制項中裝載瀏覽器 (例如，在傳統版 .NET 上，它會使用 WinForms，而在 Xamarin 上則使用原生行動控制項等等)。 此控制項稱為 `embedded`web UI。 或者，MSAL.NET 也可以啟動系統作業系統瀏覽器。

一般來說，建議您使用平台預設值，而預設值通常是系統瀏覽器。 系統瀏覽器更適合用來記住先前登入過的使用者。 如果需要變更這項行為，請使用 `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>速覽

| Framework        | 內嵌 | 系統 | 預設 |
| ------------- |-------------| -----| ----- |
| .NET 傳統     | 是 | 是^ | 內嵌 |
| .NET Core     | 否 | 是^ | 系統 |
| .NET Standard | 否 | 是^ | 系統 |
| UWP | 是 | 否 | 內嵌 |
| Xamarin.Android | 是 | 是  | 系統 |
| Xamarin.iOS | 是 | 是  | 系統 |
| Xamarin.Mac| 是 | 否 | 內嵌 |

^ 表示需要「 http://localhost 」重新導向 URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin.iOS、Xamarin.Android 上的系統網頁瀏覽器

依照預設，MSAL.NET 支援在 Xamarin.iOS、Xamarin.Android 和 .NET Core 上的系統網頁瀏覽器。 對於提供 UI 的所有平台 (非 .NET Core)，會由內嵌網頁瀏覽器控制項的程式庫提供對話方塊。 MSAL.NET 也會使用適用於 .NET 桌面的內嵌 Web 檢視和適用於 UWP 平台的 WAB。 不過，預設會針對 Xamarin iOS 和 Xamarin Android 的應用程式運用**系統網頁瀏覽器**。 在 iOS 上，甚至會根據作業系統的版本 (iOS12、iOS11 及更早版本) 選擇要使用的 Web 檢視。

使用系統瀏覽器的優點是可與其他應用程式和 Web 應用程式共用 SSO 狀態，不需訊息代理程式 (公司入口網站/驗證器)。 依照預設，系統瀏覽器會在適用 Xamarin iOS 和 Xamarin Android 平台的 MSAL.NET 中使用，因為在這些平台上，系統網頁瀏覽器會佔用整個畫面，讓使用者有更好的體驗。 系統 Web 檢視無法與對話方塊加以區別。 不過，在 iOS 上，使用者可能必須同意讓瀏覽器回呼應用程式，這一點可能有點惱人。

## <a name="system-browser-experience-on-net-core"></a>.NET Core 上的系統瀏覽器體驗

在 .NET Core 上，MSAL.NET 會以個別的處理程序啟動系統瀏覽器。 MSAL.NET 無法控制此瀏覽器，但一旦使用者完成驗證，該網頁就會以 MSAL.NET 可攔截 Uri 的方式重新導向。

您也可以透過指定下列項目，將針對 .NET 傳統版撰寫的應用程式設為使用此瀏覽器：

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET 無法偵測使用者是否瀏覽到他處，或者只是關閉瀏覽器。 建議您為使用此技術的應用程式定義逾時 (透過 `CancellationToken`)。 考慮到系統提示使用者變更密碼或執行多重要素驗證的情況，建議逾時應符合至少幾分鐘以上的時間。

### <a name="how-to-use-the-default-os-browser"></a>如何使用預設 OS 瀏覽器

MSAL.NET 必須接聽 `http://localhost:port`，並在使用者完成驗證時攔截 AAD 所傳送的程式碼 (如需詳細資訊，請參閱[授權碼](v2-oauth2-auth-code-flow.md))

若要啟用系統瀏覽器：

1. 在應用程式註冊期間，將 `http://localhost` 設定為重新導向 URI (B2C 目前不支援)
2. 當您建構 PublicClientApplication 時，請指定此重新導向 URI：

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> 如果您設定 `http://localhost`，內部 MSAL.NET 會尋找隨機開啟的連接埠並加以使用。

### <a name="linux-and-mac"></a>Linux 和 MAC

在 Linux 上，MSAL.NET 會使用 xdg-open 工具開啟預設 OS 瀏覽器。 若要進行疑難排解，請從終端機執行此工具，例如 `xdg-open "https://www.bing.com"`。 在 Mac 上，透過叫用 `open <url>` 開啟瀏覽器。

### <a name="customizing-the-experience"></a>自訂體驗

> [!NOTE]
> 自訂項目適用於 MSAL.NET 4.1.0 或更新版本。

當系統收到權杖或發生錯誤時，MSAL.NET 能夠以 HTTP 訊息回應。 您可以顯示 HTML 訊息，或重新導向至您選擇的 URL：

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

### <a name="opening-a-specific-browser-experimental"></a>開啟特定瀏覽器 (實驗性)

您可以自訂 MSAL.NET 開啟瀏覽器的方式。 例如，您可以強制開啟特定瀏覽器，而非使用預設的任何瀏覽器：

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP 不會使用系統 Web 檢視

不過對於桌面應用程式，啟動系統 Web 檢視會造成標準以下的使用者體驗，因為當使用者看到瀏覽器時，可能已經開啟其他索引標籤。 當驗證發生時，使用者會看到要求他們關閉此視窗的分頁。 如果使用者一不注意，就會關閉整個流程 (包括與驗證無關的其他索引標籤)。 利用桌面上的系統瀏覽器也需要開啟本機連接埠並在其上接聽，這可能需要應用程式的進階存取權限。 身為開發人員、使用者或系統管理員的您可能不願意擔心這項需求。

## <a name="enable-embedded-webviews-on-ios-and-android"></a>啟用 iOS 和 Android 上的內嵌 Web 檢視

您也可以啟用 Xamarin.iOS 和 Xamarin.Android 應用程式中的內嵌 Web 檢視。 從 MSAL.NET 2.0.0-preview 開始，MSAL.NET 也支援使用**內嵌** Web 檢視選項。 對於 ADAL.NET 而言，內嵌 Web 檢視是唯一支援的選項。

身為使用 MSAL.NET 並以 Xamarin 為目標的開發人員，您可以選擇使用內嵌 Web 檢視或系統瀏覽器。 端看您想要作為目標的使用者經驗和安全性考量而做出的選擇而定。

目前 MSAL.NET 尚未支援 Android 和 iOS 訊息代理程式。 因此，如果您需要提供單一登入 (SSO)，則系統瀏覽器可能仍是較佳的選項。 使用內嵌網頁瀏覽器的支援訊息代理程式會位於 MSAL.NET 待處理項目上。

### <a name="differences-between-embedded-webview-and-system-browser"></a>內嵌 Web 檢視與系統瀏覽器之間的差異
在 MSAL.NET 中，內嵌 Web 檢視與系統瀏覽器之間有一些視覺上的差異。

**使用內嵌 Web 檢視並以 MSAL.NET 進行互動式登入：**

![內嵌](media/msal-net-web-browsers/embedded-webview.png)

**使用系統瀏覽器並以 MSAL.NET 進行互動式登入：**

![系統瀏覽器](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>開發人員選項

身為使用 MSAL.NET 的開發人員，您在顯示來自 STS 的互動式對話方塊時有數種選項：

- **系統瀏覽器：** 程式庫中預設已設定的系統瀏覽器。 如果使用 Android，請閱讀[系統瀏覽器](msal-net-system-browser-android-considerations.md)，以取得支援驗證的瀏覽器特定資訊。 在 Android 中使用系統瀏覽器時，建議裝置為具有支援 Chrome 自訂索引標籤的瀏覽器。  否則，驗證就可能會失敗。
- **內嵌Web 檢視：** 若要在 MSAL.NET 中僅用內嵌的 Web 檢視，`AcquireTokenInteractively` 參數產生器會包含 `WithUseEmbeddedWebView()` 方法。

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>在 Xamarin.iOS 上的內嵌網頁瀏覽器或系統瀏覽器之間選擇

在 iOS 應用程式的 `AppDelegate.cs` 中，您可以將 `ParentWindow` 初始化為 `null`。 此參數不適用於 iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>在 Xamarin.Android上的內嵌網頁瀏覽器或系統瀏覽器之間選擇

在 Android 應用程式的 `MainActivity.cs` 中，您可以設定父活動，以便傳回驗證結果：

```csharp
 App.ParentWindow = this;
```

然後在 `MainPage.xaml.cs` 中：

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>偵測 Xamarin.Android 上的自訂索引標籤是否存在

如果您想要使用系統網頁瀏覽器，使其能與在瀏覽器中執行的應用程式啟用 SSO，但又擔心 Android 裝置不具有支援自訂索引標籤的瀏覽器而影響使用者體驗，您可以選擇在 `IPublicClientApplication` 中呼叫 `IsSystemWebViewAvailable()` 方法以決定。 如果 PackageManager 偵測到自訂索引標籤，則此方法會傳回 `true`，若在裝置上未偵測到索引標籤則會傳回 `false`。

依據此方法所傳回的值及您的需求，您可以進行決策：

- 您可以將自訂錯誤訊息傳回給使用者。 例如：「請安裝 Chrome 以繼續驗證」，或者
- 可切換回內嵌 Web 檢視選項，並以內嵌 Web 檢視的形式啟動 UI。

下列程式碼顯示內嵌 Web 檢視選項：

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core 不支援使用內嵌瀏覽器的互動式驗證

對於 .NET Core，僅可透過系統網頁瀏覽器以互動方式取得權杖，而非內嵌 Web 檢視。 事實上，.NET Core 也尚未提供 UI。
如果您想要使用系統網頁瀏覽器自訂瀏覽體驗，您可以實作 [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) 介面，甚至是提供您自己的瀏覽器。
