---
title: Xamarin Android 考慮（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）使用 Xamarin Android 時的特定考慮。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 54df91d38541fbe17a28c9ae083ae0e7d0c9d88d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063657"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>MSAL.NET 的 Xamarin Android 特定考慮
本文討論使用 Xamarin Android 搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）時的特定考慮。

## <a name="set-the-parent-activity"></a>設定父活動

在 Xamarin 上，您必須設定父活動，讓權杖在互動發生後返回。

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
您也可以在 PublicClientApplication 層級（在 MSAL 4.2 + 中）透過回呼來設定此項。

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

建議您在[這裡](https://github.com/jamesmontemagno/CurrentActivityPlugin)使用 CurrentActivityPlugin。  接著，您的 PublicClientApplication 產生器程式碼看起來會像這樣：

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>確保在驗證流程的互動部分結束後，控制權回到 MSAL
在 Android 上，您必須覆寫 `Activity` 的 `OnActivityResult` 方法，並呼叫 AuthenticationContinuationHelper MSAL 類別的 SetAuthenticationContinuationEventArgs 方法。

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
這一行可確保在驗證流程的互動部分結束後，控制項會回到 MSAL。

## <a name="update-the-android-manifest"></a>更新 Android 資訊清單
`AndroidManifest.xml` 應包含下列值：
```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash"/>
         </intent-filter>
</activity>
```
將 `android:host=` 值取代為您在 Azure 入口網站中註冊的套件名稱。 將 `android:path=` 值取代為您在 Azure 入口網站中註冊的索引鍵雜湊。 簽章雜湊**不應**進行 URL 編碼。 請確定簽章雜湊的開頭處有前置的 `/`。

或者，您可以[在程式碼中建立活動](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics)，而不是手動編輯 `AndroidManifest.xml`。 為此，您必須建立具有 [`Activity`] 和 [`IntentFilter`] 屬性的類別。 代表上述 xml 相同值的類別為：

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

### <a name="xamarinforms-43x-manifest"></a>XamarinForms 4.3. X 資訊清單

XamarinForms 4.3. x 所產生的程式碼會將 `package` 屬性設定為 `AndroidManifest.xml`中的 `com.companyname.{appName}`。 如果您使用 `DataScheme` 作為 `msal{client_id}`，您可能會想要將此值變更為與 `MainActivity.cs` 命名空間相同。

## <a name="use-the-embedded-web-view-optional"></a>使用內嵌的 web view （選擇性）

根據預設，MSAL.NET 會使用系統 web 瀏覽器，這可讓您取得 Web 應用程式和其他應用程式的 SSO。 在某些罕見的情況下，您可能會想要指定要使用內嵌的 web view。 如需詳細資訊，請參閱[MSAL.NET 使用網頁瀏覽器](msal-net-web-browsers.md)和[Android 系統瀏覽器](msal-net-system-browser-android-considerations.md)。

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>疑難排解
如果您建立新的 Xamarin Forms 應用程式，並新增對 MSAL.Net NuGet 套件的參考，這會是可行的。
不過，如果您想要將現有的 Xamarin Forms 應用程式升級為 MSAL.NET preview 1.1.2 或更新版本，可能會遇到組建問題。

若要疑難排解這些問題，您應該：
- 將現有的 MSAL.NET NuGet 套件更新為 MSAL.NET preview 1.1.2 或更新版本
- 檢查 Xamarin 是否自動更新為版本2.5.0.122203 （如果不是，則更新為此版本）
- 檢查是否已自動將支援更新為版本25.4.0.2 （如果不是，則更新為此版本）
- 所有的 Xamarin. 支援套件都應該以版本25.4.0.2 為目標
- 清除/重建
- 嘗試在 Visual Studio （選項-> 專案和方案-> 組建和執行-> 平行專案組建的最大數目）中，將最大平行專案組建設定為1。
- 或者，如果您是從命令列建立，請嘗試從命令中移除/m （如果您使用它的話）。


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>錯誤：名稱 ' AuthenticationContinuationHelper ' 不存在於目前的內容中

這可能是因為 Visual Studio 未正確更新 Android .csproj * 檔案。 有時候， **\<HintPath >** filepath 不正確地包含 netstandard13，而不是**monoandroid90**。

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>後續步驟

如需更多詳細資料和範例，請在下列範例的 readme.md 檔案的[Android 特定考慮](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)段落中提供：

| 範例 | 平台 | 描述 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 簡單的 Xamarin Forms 應用程式展示如何使用 MSAL 來驗證 MSA 和透過新增 v2.0 端點的 Azure AD，並使用產生的權杖來存取 Microsoft Graph。 <br>![拓撲](media/msal-net-xamarin-android-considerations/topology.png) |
