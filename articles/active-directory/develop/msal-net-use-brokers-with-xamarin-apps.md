---
title: 使用代理程式搭配 Xamarin iOS & Android |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何設定可使用 Microsoft Authenticator 和適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 的 Xamarin iOS 應用程式。 同時瞭解如何從適用于 .NET 的 Azure AD 驗證程式庫遷移 (ADAL.NET) 至適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 。
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
ms.openlocfilehash: 10fb55cc133d5d6b39ca2809474ff20dfe500708
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017794"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 應用程式上使用 Microsoft Authenticator 或 Intune 公司入口網站

在 Android 和 iOS 上，代理程式（例如 Microsoft Authenticator 和 Android 特定的 Microsoft Intune 公司入口網站啟用：

- **單一登入 (SSO)**：使用者不需要登入每個應用程式。
- **裝置識別碼**：訊息代理程式存取裝置憑證。 當裝置加入工作場所時，會在裝置上建立此憑證。
- **應用程式識別驗證**：當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 URL。 訊息代理程式會驗證 URL。

若要啟用其中一項功能，請 `WithBroker()` 在呼叫方法時使用參數 `PublicClientApplicationBuilder.CreateApplication` 。 `.WithBroker()`預設會將參數設定為 true。

適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 的代理驗證設定會因平臺而異：

* [iOS 應用程式](#brokered-authentication-for-ios)
* [Android 應用程式](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>適用于 iOS 的代理驗證

使用下列步驟，讓您的 Xamarin iOS 應用程式與 [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) 應用程式進行通訊。 如果您的目標是 iOS 13，請考慮閱讀 [Apple 的重大 API 變更](./msal-net-xamarin-ios-considerations.md)。

### <a name="step-1-enable-broker-support"></a>步驟1：啟用訊息代理程式支援

您必須啟用的個別實例的訊息代理程式支援 `PublicClientApplication` 。 預設會停用支援。 當您透過建立時 `PublicClientApplication` `PublicClientApplicationBuilder` ， `WithBroker()` 如下列範例所示，請使用參數。 `WithBroker()`預設會將參數設定為 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>步驟2：啟用 keychain 存取

若要啟用 keychain 存取，您的應用程式必須有 keychain 存取群組。 `WithIosKeychainSecurityGroup()`當您建立應用程式時，您可以使用 API 來設定 keychain 存取群組：

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

如需詳細資訊，請參閱 [啟用 keychain 存取](msal-net-xamarin-ios-considerations.md#enable-keychain-access)。

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>步驟3：更新 AppDelegate 以處理回呼

當 MSAL.NET 呼叫訊息代理程式時，訊息代理程式會透過類別的方法，呼叫回您的應用程式 `OpenUrl` `AppDelegate` 。 因為 MSAL 會等候 broker 的回應，所以您的應用程式必須合作來呼叫 MSAL.NET 回來。 若要啟用這項合作，請更新 *AppDelegate.cs* 檔案以覆寫下列方法。

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

每次啟動應用程式時，就會叫用這個方法。 它是用來處理來自訊息代理程式之回應的機會，並完成 MSAL.NET 開始的驗證程式。

### <a name="step-4-set-uiviewcontroller"></a>步驟4：設定 UIViewController ( # A1

仍在 *AppDelegate.cs* 檔案中，您必須設定物件視窗。 您通常不需要設定 Xamarin iOS 的物件視窗，但您需要物件視窗來傳送和接收來自訊息代理程式的回應。

若要設定物件視窗：

1. 在 *AppDelegate.cs* 檔案中，將設定 `App.RootViewController` 為新的 `UIViewController()` 。 此指派可確保對 broker 的呼叫包含 `UIViewController` 。 如果未正確指派此設定，您可能會收到此錯誤：

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. 在 `AcquireTokenInteractive` 呼叫上，使用 `.WithParentActivityOrWindow(App.RootViewController)` ，然後傳入您將使用之物件視窗的參考。

    在 *App.cs* 中：

    ```csharp
       public static object RootViewController { get; set; }
    ```

    在 *AppDelegate.cs* 中：

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    在 `AcquireToken` 呼叫中：

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>步驟5：註冊 URL 配置

MSAL.NET 使用 Url 叫用訊息代理程式，然後將訊息代理程式回應傳回給您的應用程式。 若要完成來回行程，請在 *plist* 檔案中為您的應用程式註冊 URL 配置。

`CFBundleURLSchemes`名稱必須包含 `msauth.` 做為前置詞。 在前置詞後面加上 `CFBundleURLName` 。

在 URL 配置中，可 `BundleId` 唯一識別應用程式： `$"msauth.(BundleId)"` 。 因此 `BundleId` ，如果是 `com.yourcompany.xforms` ，則 URL 配置為 `msauth.com.yourcompany.xforms` 。

> [!NOTE]
> 此 URL 配置會成為重新導向 URI 的一部分，可在接收來自訊息代理程式的回應時，唯一識別您的應用程式。

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

MSAL `–canOpenURL:` 會使用檢查訊息代理程式是否已安裝在裝置上。 在 iOS 9 中，Apple 已鎖定應用程式可以查詢的配置。

新增 `msauthv2` 至 plist 檔案的 `LSApplicationQueriesSchemes` 區段 ，如下列範例所示：

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>步驟7：將重新導向 URI 新增至您的應用程式註冊

當您使用訊息代理程式時，您的重新導向 URI 會有額外的需求。 重新導向 URI 的格式 _必須_ 如下：

```csharp
$"msauth.{BundleId}://auth"
```

以下為範例：

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

請注意，重新導向 URI 符合 `CFBundleURLSchemes` 您在 *plist* 檔中所包含的名稱。

將重新導向 URI 新增至 [Azure 入口網站](https://portal.azure.com)中的應用程式註冊。 若要產生格式正確的重新導向 URI，請使用 Azure 入口網站中的 **應用程式註冊** ，從套件組合識別碼產生代理的重新導向 uri。

**若要產生重新導向 URI：**

1. 登入<a href="https://portal.azure.com/" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
1. 選取 **Azure Active Directory**  >  **應用程式註冊**> 您註冊的應用程式
1. 選取 **驗證**  >  **新增平臺**  >  **iOS/macOS**
1. 輸入您的套件組合識別碼，然後選取 [ **設定**]。

    複製 [重新 **導向 uri** ] 文字方塊中所產生的重新導向 uri，以包含在您的程式碼中：

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="Azure 入口網站中產生的重新導向 URI 的 iOS 平臺設定":::
1. 選取 [ **完成** ] 以完成重新導向 URI 的產生。

## <a name="brokered-authentication-for-android"></a>適用于 Android 的代理驗證

### <a name="step-1-enable-broker-support"></a>步驟1：啟用訊息代理程式支援

Broker 支援是以每一基礎啟用 `PublicClientApplication` 。 此功能預設為停用。 在 `WithBroker()` 透過建立時，請使用 (預設設定為 true) 的參數 `IPublicClientApplication` `PublicClientApplicationBuilder` 。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步驟2：更新 AppDelegate 以處理回呼

當 MSAL.NET 呼叫訊息代理程式時，訊息代理程式會接著使用方法回呼您的應用程式 `OnActivityResult()` 。 由於 MSAL 會等候來自訊息代理程式的回應，因此您的應用程式需要將結果路由傳送至 MSAL.NET。

藉由覆寫方法將結果路由傳送至 `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` 方法， `OnActivityResult()` 如下所示：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

每次啟動訊息代理程式應用程式時，就會叫用這個方法，並做為處理來自訊息代理程式之回應的機會，並完成 MSAL.NET 所啟動的驗證程式。

### <a name="step-3-set-an-activity"></a>步驟3：設定活動

若要啟用代理驗證，請設定活動，讓 MSAL 可以傳送和接收來自訊息代理程式的回應。 若要這樣做，請提供活動 (通常是 `MainActivity` `WithParentActivityOrWindow(object parent)` 父物件的) 。

例如，在的呼叫中 `AcquireTokenInteractive()` ：

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>步驟4：將重新導向 URI 新增至您的應用程式註冊

MSAL 使用 Url 叫用訊息代理程式，然後返回您的應用程式。 若要完成該往返，您必須使用 [Azure 入口網站](https://portal.azure.com)為您的應用程式註冊重新 **導向 URI** 。

應用程式的重新導向 URI 格式取決於用來簽署 APK 的憑證。 例如：

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI 的最後一個部分 `hgbUYHVBYUTvuvT&Y6tr554365466=` 是 APK 所簽署之簽章的 Base64 編碼版本。 在 Visual Studio 中開發您的應用程式時，如果您要在不使用特定憑證來簽署 APK 的情況下，對程式碼進行偵錯工具，Visual Studio 會為您簽署 APK，以供您進行調試。 當 Visual Studio 以此方式為您簽署 APK 時，它會為其建立所在的電腦提供唯一的簽章。 因此，每當您在不同的電腦上建立應用程式時，您必須更新應用程式程式碼中的重新導向 URI，並在 Azure 入口網站中更新應用程式的註冊，才能使用 MSAL 進行驗證。

在進行偵錯工具時，您可能會遇到 MSAL 例外狀況 (或記錄訊息) 指出提供的重新導向 URI 不正確。 **例外狀況或記錄訊息也會指出您應該** 搭配目前進行調試的電腦使用的重新導向 URI。 只要更新程式碼中的重新導向 URI，並將提供的重新導向 URI 新增至 Azure 入口網站中的應用程式註冊，您就可以使用提供的重新導向 URI 繼續開發應用程式。

當您準備好要完成程式碼時，請更新程式碼中的重新導向 URI 和 Azure 入口網站中的應用程式註冊，以使用您用來簽署 APK 的憑證簽章。

在實務上，這表示您應該考慮為開發小組的每個成員新增重新導向 URI， *以及* APK 的生產簽署版本的重新導向 uri。

您可以自行計算簽章，類似于 MSAL 的運作方式：

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

您也可以選擇使用 **keytool** 搭配下列命令來取得封裝的簽章：

* Windows：
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS：
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>步驟 5 (選用) ：切換回系統瀏覽器

如果 MSAL 設定為使用訊息代理程式，但未安裝訊息代理程式，則 MSAL 會切換回使用 (瀏覽器) 的 web view。 MSAL 將嘗試使用裝置上的預設系統瀏覽器進行驗證，因為重新導向 URI 是針對訊息代理程式所設定，而系統瀏覽器不知道如何使用它來流覽回 MSAL。 若要避免失敗，您可以使用步驟4中所用的 broker 重新導向 URI 來設定 *意圖篩選* 。

修改應用程式的資訊清單以新增意圖篩選：

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

例如，如果您有的重新導向 URI `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` ，您的資訊清單看起來應該類似下列 XML 程式碼片段。

值中簽章前面的正斜線 (`/`) `android:path` 為 **必要** 項。

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

或者，您可以設定 MSAL 切換回內嵌瀏覽器，而不依賴重新導向 URI：

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Android 代理驗證的疑難排解秘訣

以下是在 Android 上執行代理驗證時避免問題的一些秘訣：

- 重新 **導向 uri** -將重新導向 uri 新增至 [Azure 入口網站](https://portal.azure.com/)中的應用程式註冊。 遺失或不正確的重新導向 URI 是開發人員遇到的常見問題。
- **Broker 版本** -安裝 broker 應用程式的最小必要版本。 這兩個應用程式中的任一個都可用於 Android 上的代理驗證。
  - [Intune 公司入口網站](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (5.0.4689.0 版或更高版本) 
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (版本6.2001.0140 或更高的) 。
- **Broker 優先順序** -MSAL 在安裝多個訊息代理程式時，會與安裝在裝置上的 *第一個 Broker* 進行通訊。

    範例：如果您第一次安裝 Microsoft Authenticator 然後安裝 Intune 公司入口網站，則 *只* 會在 Microsoft Authenticator 上進行代理驗證。
- **記錄** -如果您遇到代理驗證的問題，則查看訊息代理程式的記錄可能有助於診斷原因。
  - 查看 Microsoft Authenticator 記錄：

    1. 選取應用程式右上角的 [功能表] 按鈕。
    1. 選取 **[** 說明  >  **傳送記錄**]  >  **視圖記錄**。
    1. 選取 [ **全部複製** ]，將訊息代理程式記錄檔複製到裝置的剪貼簿。

    使用這些記錄進行偵錯工具的最佳方式是以電子郵件傳送給自己，並在您的開發電腦上加以查看。 您可能會發現，您可以更輕鬆地剖析電腦上的記錄，而不是裝置本身。 您也可以使用 Android 上的測試編輯器，將記錄檔儲存為文字檔，然後使用 USB 纜線將檔案複製到電腦。

  - 查看 Intune 公司入口網站記錄：

    1. 選取應用程式左上角的功能表按鈕
    1. 選取 **設定**  >  **診斷資料**
    1. 選取 [ **複製記錄** ]，將訊息代理程式記錄檔複製到裝置的 SD 記憶卡。
    1. 使用 USB 纜線將裝置連接到電腦，以在您的開發電腦上查看記錄。

    擁有記錄之後，您可以透過相互關聯識別碼來搜尋這些記錄，以進行驗證嘗試。 相互關聯識別碼會附加至每個驗證要求。 若要尋找 Microsoft 身分識別平臺驗證端點傳回的錯誤，請搜尋 `AADSTS` 。

## <a name="next-steps"></a>後續步驟

瞭解搭配 [MSAL.NET 使用通用 Windows 平臺的考慮](msal-net-uwp-considerations.md)。
