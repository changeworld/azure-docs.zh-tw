---
title: 夏馬林安卓考慮 （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解有關將 Xamarin Android 與 Microsoft 身份驗證庫配合 .NET （MSAL.NET） 的注意事項。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132505"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>使用 Xamarin Android 與MSAL.NET的注意事項
本文討論了在將 Xamarin Android 與 Microsoft 身份驗證庫結合為 .NET （MSAL.NET） 時應考慮的事項。

## <a name="set-the-parent-activity"></a>設置父活動

在 Xamarin Android 上，設置父活動，以便權杖在交互後返回。 以下是程式碼範例：

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

在 MSAL 4.2 及更高版本中，您還可以將此功能設置為`PublicClientApplication`的級別。 為此，請使用回檔：

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

如果使用[CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)，則`PublicClientApplication`產生器代碼如下所示。

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>確保控制項返回到 MSAL 
當身份驗證流的互動式部分結束時，請確保控制項返回 MSAL。 在 Android 上`OnActivityResult`，重寫`Activity`方法。 然後調用`SetAuthenticationContinuationEventArgs``AuthenticationContinuationHelper`MSAL 類的方法。 

以下是範例：

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

此行可確保控制項在身份驗證流的交互部分結束時返回到 MSAL。

## <a name="update-the-android-manifest"></a>更新 Android 清單
*AndroidManifest.xml*檔應包含以下值：

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
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

替換您在 Azure 門戶中註冊的包名稱的值`android:host=`。 替換在 Azure 門戶中註冊的鍵雜湊值`android:path=`。 簽名雜湊*不應*對 URL 進行編碼。 確保前斜杠 （`/`） 出現在簽名雜湊的開頭。

或者，[在代碼中創建活動，](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics)而不是手動編輯*AndroidManifest.xml*。 要在代碼中創建活動，請先創建包含`Activity`屬性和屬性的`IntentFilter`類。 

下面是表示 XML 檔值的類的示例：

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin.表格 4.3.X 清單

Xamarin.forms 4.3.x 生成代碼，`package`將屬性`com.companyname.{appName}`設置在*AndroidManifest.xml*中。 如果使用 為`DataScheme``msal{client_id}`，則可能需要更改該值以匹配`MainActivity.cs`命名空間的值。

## <a name="use-the-embedded-web-view-optional"></a>使用嵌入式 Web 視圖（可選）

預設情況下，MSAL.NET使用系統 Web 瀏覽器。 此瀏覽器使您能夠使用 Web 應用程式和其他應用程式獲得單一登入 （SSO）。 在某些極少數情況下，您可能希望系統使用嵌入式 Web 視圖。 

此代碼示例演示如何設置嵌入式 Web 視圖：

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

有關詳細資訊，請參閱使用[web 瀏覽器MSAL.NET](msal-net-web-browsers.md)和[Xamarin Android 系統瀏覽器注意事項](msal-net-system-browser-android-considerations.md)。


## <a name="troubleshoot"></a>疑難排解
您可以創建新的 Xamarin.Forms 應用程式，並添加對 nuGet 包MSAL.NET的引用。
但是，如果將現有的 Xamarin.Forms 應用程式升級到預覽版 1.1.2 或更高版本MSAL.NET，則可能存在生成問題。

要解決生成問題：：

- 將現有MSAL.NET NuGet 包更新為預覽 1.1.2 或更高版本MSAL.NET。
- 檢查 Xamarin.表單自動更新到版本 2.5.0.122203。 如有必要，將 Xamarin.Forms 更新為此版本。
- 檢查 Xamarin.Android.Support.v4 自動更新到版本 25.4.0.2。 如有必要，請更新至版本 25.4.0.2。
- 確保所有 Xamarin.Android.支援包目標版本 25.4.0.2。
- 清理或重建應用程式。
- 在 Visual Studio 中，嘗試將並行專案生成的最大數量設置為 1。 為此，請選擇**選項** > **專案和解決方案** > **生成和運行** > **最大數量的並行專案生成**。
- 如果要從命令列生成，並且命令使用`/m`，請嘗試從 命令中刪除此元素。

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>錯誤：名稱身份驗證延續協助程式在當前上下文中不存在

如果錯誤指示當前上下文中`AuthenticationContinuationHelper`不存在，Visual Studio 可能錯誤地更新了 Android.csproj® 檔。 有時*\<HintPath>* 檔路徑錯誤地包含*netstandard13*而不是*monoandroid90*。

此示例包含正確的檔路徑：

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱[使用 Microsoft 標識平臺的 Xamarin 移動應用程式](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)的示例。 下表總結了 README 檔中的相關資訊。

| 範例 | Platform | 描述 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS， 安卓， UWP | 一個簡單的 Xamarin.Forms 應用，演示如何使用 MSAL 通過 Azure AD 2.0 終結點對 Microsoft 個人帳戶和 Azure AD 進行身份驗證。 該應用程式還顯示如何訪問 Microsoft 圖形並顯示生成的權杖。 <br>![拓撲](media/msal-net-xamarin-android-considerations/topology.png) |
