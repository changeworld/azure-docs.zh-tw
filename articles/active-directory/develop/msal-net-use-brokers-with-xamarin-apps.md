---
title: 使用經紀人與Xamarin iOS&安卓 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何設置 Xamarin iOS 應用程式，這些應用程式可以使用 Microsoft 身份驗證器和 Microsoft 身份驗證庫進行 .NET （MSAL.NET）。 還瞭解如何從 .NET （ADAL.NET） 的 Azure AD 身份驗證庫遷移到 .NET （MSAL.NET） 的 Microsoft 身份驗證庫。
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
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262785"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 應用程式中使用 Microsoft 身份驗證器或 Intune 公司門戶

在 Android 和 iOS 上，微軟身份驗證器和特定于 Android 的 Microsoft Intune 公司門戶等代理支援：

- **單一登入 （SSO）：** 使用者不需要登錄到每個應用程式。
- **設備標識**：代理訪問設備證書。 此證書在設備加入工作區時在設備上創建。
- **應用程式標識驗證**：當應用程式調用代理時，它將傳遞其重定向 URL。 代理驗證 URL。

要啟用這些功能之一，`WithBroker()`請調用 方法時使用`PublicClientApplicationBuilder.CreateApplication`參數。 預設情況下`.WithBroker()`，參數設置為 true。 

還使用以下部分中的說明為[iOS](#brokered-authentication-for-ios)應用程式或[Android](#brokered-authentication-for-android)應用程式設定代理身份驗證。

## <a name="brokered-authentication-for-ios"></a>iOS 的代理身份驗證

使用以下步驟使 Xamarin.iOS 應用能夠與[Microsoft 身份驗證器](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)應用對話。

### <a name="step-1-enable-broker-support"></a>第 1 步：啟用代理支援
您必須對`PublicClientApplication`的各個實例啟用代理支援。 預設情況下，支援處於禁用狀態。 創建`PublicClientApplication`時，`PublicClientApplicationBuilder`請使用`WithBroker()`參數，如下例所示。 預設情況下`WithBroker()`，參數設置為 true。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>第 2 步：啟用鑰匙串訪問

要啟用鑰匙串訪問，您必須為應用程式設定鑰匙串訪問組。 在創建應用程式時`WithIosKeychainSecurityGroup()`，可以使用 API 設置鑰匙串訪問組：

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

有關詳細資訊，請參閱[啟用鑰匙串訪問](msal-net-xamarin-ios-considerations.md#enable-keychain-access)。

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>第 3 步：更新應用委託以處理回檔
當 .NET （MSAL.NET） 的 Microsoft 身份驗證庫調用代理時，代理會通過`OpenUrl``AppDelegate`類的方法回檔您的應用程式。 由於 MSAL 等待代理的回應，因此您的應用程式需要合作來回撥MSAL.NET。 要啟用此合作，`AppDelegate.cs`請更新檔以重寫以下方法。

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

每次啟動應用程式時都會調用此方法。 它用作處理來自代理的回應並完成MSAL.NET啟動的身份驗證過程的機會。

### <a name="step-4-set-uiviewcontroller"></a>第 4 步：設置 UIView 控制器（）
仍然在`AppDelegate.cs`檔中，您需要設置一個物件視窗。 通常，對於 Xamarin iOS，您不需要設置物件視窗。 但是，您確實需要一個物件視窗來發送和接收來自代理的回應。 

要設置物件視窗： 
1. 在檔中`AppDelegate.cs`，設置為`App.RootViewController`新的`UIViewController()`。 此分配可確保對代理的調用包括`UIViewController`。 如果此設置分配不正確，您可能會收到此錯誤：

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. 在通話`AcquireTokenInteractive`時，使用`.WithParentActivityOrWindow(App.RootViewController)`並傳遞對要使用的物件視窗的引用。

    在 `App.cs` 中：

    ```csharp
       public static object RootViewController { get; set; }
    ```

    在 `AppDelegate.cs` 中：

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    在呼叫`AcquireToken`中：

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>第 5 步：註冊 URL 方案
MSAL.NET使用 URL 調用代理，然後將代理回應返回到你的應用。 要完成往返行程，請在檔中註冊應用的`Info.plist`URL 方案。

名稱`CFBundleURLSchemes`必須作為前`msauth.`綴包含。 按照 首碼`CFBundleURLName`一起。 

在 URL 方案中`BundleId`，唯一標識應用： `$"msauth.(BundleId)"`。 因此，`BundleId`如果是`com.yourcompany.xforms`，則 URL`msauth.com.yourcompany.xforms`方案為 。

> [!NOTE]
> 此 URL 方案將成為重定向 URI 的一部分，該 URI 在收到來自代理的回應時唯一標識應用。

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步驟 6：將代理識別碼添加到 LS應用程式查詢計劃部分

MSAL`–canOpenURL:`用於檢查代理是否安裝在設備上。 在 iOS 9 中，Apple 鎖定了應用程式可以查詢的方案。 

添加到`msauthv2`檔的`LSApplicationQueriesSchemes`部分，`Info.plist`如以下示例所示：

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>第 7 步：在應用程式門戶中註冊重定向 URI

當您使用代理時，重定向 URI 有額外的要求。 重定向 URI_必須_具有以下格式：

```csharp
$"msauth.{BundleId}://auth"
```

以下是範例：

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

請注意，重定向 URI 與您`CFBundleURLSchemes`包含在`Info.plist`檔中的名稱匹配。

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>第 8 步：確保重定向 URI 已註冊到應用

重定向 URI 需要在[應用註冊門戶](https://portal.azure.com)上註冊為應用程式的有效重定向 URI。 

應用註冊門戶提供了新的體驗，可説明您從捆綁包 ID 計算代理回復 URI。 

要計算重定向 URI：

1. 在應用註冊門戶中，選擇 **"身份驗證** > **試用"新體驗**。

   ![試用新的應用註冊體驗](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. 選擇 **"添加平臺**"。

   ![添加平臺](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 當支援平臺清單時，請選擇**iOS**。

   ![配置 iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 按請求輸入捆綁包 ID，然後選擇 **"配置**"。

   ![輸入捆綁包 ID](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

完成這些步驟後，將為您計算重定向 URI。

![複製重定向 URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Android 的代理身份驗證

### <a name="step-1-enable-broker-support"></a>第 1 步：啟用代理支援

基於每個公共用戶端應用程式啟用代理支援。 此功能預設為停用。 在創建`WithBroker()``IPublicClientApplication`通過`PublicClientApplicationBuilder`時，使用 參數（預設情況下設置為 true）。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>第 2 步：更新應用委託以處理回檔

當MSAL.NET調用代理時，代理將反過來使用 OnActivityResult（） 方法回電回您的應用程式。 由於 MSAL 將等待來自代理的回應，因此應用程式需要將結果路由到MSAL.NET。
這可以通過重寫 OnActivityResult（）`SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)`方法將結果路由到 實現，如下所示

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

每次啟動代理應用程式時都會調用此方法，並用作處理代理回應並完成由 MSAL.NET 啟動的身份驗證過程的機會。

### <a name="step-3-set-an-activity"></a>第 3 步：設置活動

要使代理身份驗證正常工作，您需要設置活動，以便 MSAL 可以從代理髮送和接收回應。

為此，您需要將活動（通常是 MainActivity）提供給 作為父物件的 活動`WithParentActivityOrWindow(object parent)`。 

**例如：**

在獲取權杖調用中：

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>第 4 步：在應用程式門戶中註冊重定向 Uri

MSAL 使用 URL 調用代理，然後返回到你的應用。 要完成該往返行程，您需要為應用註冊 URL 方案。 此重定向 URI 需要在 Azure AD 應用註冊門戶上註冊為應用程式的有效重定向 URI。


應用程式所需的重定向 URI 取決於用於對 APK 進行簽名的證書。

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI`hgbUYHVBYUTvuvT&Y6tr554365466=`的最後一部分是 APK 簽名的簽名，base64 編碼。
但是，在使用 Visual Studio 的應用程式開發階段，如果您調試代碼而不使用特定證書對 apk 進行簽名，Visual Studio 將為您簽名以進行調試，為 APK 提供機器，它是建立在。 因此，每次在不同的電腦上構建應用時，都需要更新應用程式代碼中的重定向 URI 以及應用程式在 Azure 門戶中的註冊，以便對 MSAL 進行身份驗證。 

調試時，可能會遇到 MSAL 異常（或日誌消息），指出提供的重定向 URI 不正確。 **此異常還將為您提供與**正在調試的當前電腦一起使用的重定向 URI。 您可以使用此重定向 URI 繼續開發。

準備好完成代碼後，請確保在代碼中更新重定向 URI，並在應用程式在 Azure 門戶中的註冊中更新，以使用要簽名 APK 的證書的簽名。

實際上，這意味著您必須為團隊的每個成員註冊重定向 URI，並為 APK 的生產簽名版本註冊重定向 URI。

您也可以自己計算此簽名，類似于 MSAL 如何計算： 

```CSharp
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

您還可以選擇使用帶有以下命令的 keytool 獲取包的簽名：

對於 Windows：`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

對於 Mac：`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>後續步驟

瞭解[將通用 Windows 平臺與MSAL.NET 的注意事項](msal-net-uwp-considerations.md)。
