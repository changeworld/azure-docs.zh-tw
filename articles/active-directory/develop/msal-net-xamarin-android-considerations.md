---
title: Xamarin Android 程式碼設定和疑難排解 (MSAL.NET) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解搭配使用 Xamarin Android 與適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 的考慮。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 256bee26ec6aef464d9ee838ffca4d8c1ecbbb4e
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047781"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Xamarin Android with MSAL.NET 的設定需求和疑難排解秘訣

使用 Xamarin Android 搭配適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 時，您需要在程式碼中進行幾項設定變更。 下列各節將說明所需的修改，後面接著 [疑難排解](#troubleshooting) 章節，協助您避免一些最常見的問題。

## <a name="set-the-parent-activity"></a>設定父活動

在 Xamarin Android 上，設定父活動，讓權杖在互動之後傳回：

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

在 MSAL.NET 4.2 和更新版本中，您也可以在 [PublicClientApplication][PublicClientApplication]層級設定這項功能。 若要這樣做，請使用回呼：

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

如果您使用 [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)，則產生器程式 [`PublicClientApplication`][PublicClientApplication] 代碼看起來應該類似下列程式碼片段：

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>確定控制權返回 MSAL

當驗證流程的互動式部分結束時，藉由覆寫來將控制權傳回給 MSAL [`Activity`][Activity] 。[`OnActivityResult()`][OnActivityResult] 方法。

在您的覆寫中，呼叫 MSAL。NET `AuthenticationContinuationHelper` 。`SetAuthenticationContinuationEventArgs()` 方法，在驗證流程的互動式部分結束時，將控制權傳回給 MSAL。

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>更新 Android 資訊清單

*AndroidManifest.xml*檔案應包含下列值：

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
  </activity>
```

以您在 Azure 入口網站中註冊的套件名稱取代 `android:host=` 值。 以您在 Azure 入口網站中註冊的金鑰雜湊來取代 `android:path=` 值。 簽章雜湊 *不* 應以 URL 編碼。 確定前置正斜線 (`/`) 出現在簽章雜湊的開頭。

或者， [在程式碼中建立活動](/xamarin/android/platform/android-manifest#the-basics) ，而不是手動編輯 *AndroidManifest.xml*。 若要在程式碼中建立活動，請先建立包含 `Activity` 屬性和屬性的類別 `IntentFilter` 。

以下是代表 XML 檔案值的類別範例：

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Xamarin 4.3. x 資訊清單

Xamarin 4.3. x 會產生將 `package` 屬性設為 `com.companyname.{appName}` *AndroidManifest.xml*中的程式碼。 如果您使用 `DataScheme` as `msal{client_id}` ，則可能會想要將值變更為符合命名空間的值 `MainActivity.cs` 。

## <a name="use-the-embedded-web-view-optional"></a>使用內嵌的 web view (選用) 

根據預設，MSAL.NET 會使用系統網頁瀏覽器。 此瀏覽器可讓您使用 web 應用程式和其他應用程式， (SSO) 來取得單一登入。 在某些罕見的情況下，您可能會想要讓系統使用內嵌的 web view。

這個程式碼範例會示範如何設定內嵌的 web view：

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

如需詳細資訊，請參閱針對 MSAL.NET 和[Xamarin Android 系統瀏覽器考慮](msal-net-system-browser-android-considerations.md)[使用網頁瀏覽器](msal-net-web-browsers.md)。

## <a name="troubleshooting"></a>疑難排解

### <a name="general-tips"></a>一般提示

- 將現有的 MSAL.NET NuGet 套件更新為 [最新版本的 MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/)。
- 確認 [Xamarin] 為最新版本。
- 確認支援的版本是最新版本。
- 確定所有的 Xamarin. 支援套件都以最新版本為目標。
- 清除或重建應用程式。
- 在 Visual Studio 中，請嘗試將平行專案組建的最大數目設定為 **1**。 若要這樣做，請選取 [**選項**  >  **專案] 和 [方案**  >  **建立] 和 [執行**  >  **平行專案組建的最大數目**]。
- 如果您是從命令列建立，而您的命令使用 `/m` ，請嘗試從命令中移除此元素。

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>錯誤： AuthenticationContinuationHelper 名稱不存在目前的內容中

如果錯誤指出 `AuthenticationContinuationHelper` 目前內容中不存在，Visual Studio 可能會不正確地更新*Android .csproj \* *檔案。 有時元素中的檔案路徑不 `<HintPath>` 正確地包含 `netstandard13` 而不是 `monoandroid90` 。

此範例包含正確的檔案路徑：

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [使用 Microsoft 身分識別平臺的 Xamarin 行動應用程式](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)範例。 下表摘要說明讀我檔案中的相關資訊。

| 範例 | 平台 | 描述 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS、Android、UWP | 簡單的 Xamarin. Forms 應用程式，示範如何使用 MSAL 來驗證 Microsoft 個人帳戶，以及透過 Azure AD 2.0 端點 Azure AD。 應用程式也會顯示如何存取 Microsoft Graph，並顯示所產生的權杖。 <br>![驗證流程的圖表](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
