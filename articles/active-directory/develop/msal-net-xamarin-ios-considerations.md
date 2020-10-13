---
title: Xamarin iOS 考慮 (MSAL.NET) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解搭配使用 Xamarin iOS 與適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 的考慮。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 70ab4a151fe73b59663fd8fa16170b2e507c2511
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258054"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>搭配使用 Xamarin iOS 與 MSAL.NET 的考慮

當您在 Xamarin iOS 上使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 時，您應該：

- 覆寫並 `OpenUrl` 在中執行函數 `AppDelegate` 。
- 啟用 keychain 群組。
- 啟用權杖快取共用。
- 啟用 keychain 存取。
- 瞭解 iOS 12 和 iOS 13 和驗證的已知問題。

## <a name="implement-openurl"></a>執行 OpenUrl

覆寫 `OpenUrl` `FormsApplicationDelegate` 衍生類別的方法，並呼叫 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` 。 以下為範例：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

此外，請執行下列工作：

* 定義重新導向 URI 配置。
* 需要應用程式的許可權，才能呼叫另一個應用程式。
* 有特定的重新導向 URI 形式。
* 在 Azure 入口網站中[註冊重新導向 URI](quickstart-register-app.md#add-a-redirect-uri) 。

### <a name="enable-keychain-access"></a>啟用 keychain 存取

若要啟用 keychain 存取，請確定您的應用程式具有 keychain 存取群組。 當您使用 API 建立應用程式時，可以設定 keychain 存取群組 `WithIosKeychainSecurityGroup()` 。

若要從快取和單一登入獲益 (SSO) ，請在您的所有應用程式中，將 keychain 存取群組設定為相同的值。

此安裝程式的範例會使用 MSAL 4.x：

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

也請在檔案中啟用 keychain 存取 `Entitlements.plist` 。 請使用下列存取群組或您自己的存取群組。

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

當您使用 `WithIosKeychainSecurityGroup()` API 時，MSAL 會自動將您的安全性群組附加至應用程式 *小組識別碼* 的結尾 (`AppIdentifierPrefix`) 。 MSAL 會新增您的安全性群組，因為當您在 Xcode 中建立應用程式時，它會相同。 這就是為什麼檔案中的權利 `Entitlements.plist` 必須包含在 `$(AppIdentifierPrefix)` keychain 存取群組之前的原因。

如需詳細資訊，請參閱 [iOS 權利檔](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)。

### <a name="enable-token-cache-sharing-across-ios-applications"></a>在 iOS 應用程式之間啟用權杖快取共用

從 MSAL 2.x 開始，您可以指定 keychain 存取群組，以在多個應用程式之間保存權杖快取。 這項設定可讓您在多個具有相同 keychain 存取群組的應用程式之間共用權杖快取。 您可以在 [ADAL.NET](https://aka.ms/adal-net) 應用程式、MSAL.NET Xamarin ios 應用程式，以及在 [ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 或 [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)中開發的原生 iOS 應用程式之間共用權杖快取。

藉由共用權杖快取，您可以在所有使用相同 keychain 存取群組的應用程式中，允許單一登入 (SSO) 。

若要啟用此快取共用，請使用方法，將 `WithIosKeychainSecurityGroup()` keychain 存取群組設定為共用相同快取的所有應用程式中的相同值。 本文的第一個程式碼範例說明如何使用方法。

稍早在本文中，您已瞭解 MSAL 會在 `$(AppIdentifierPrefix)` 您每次使用 API 時新增 `WithIosKeychainSecurityGroup()` 。 MSAL 會加入此元素，因為小組識別碼可 `AppIdentifierPrefix` 確保只有相同發行者所建立的應用程式可以共用 keychain 存取權。

> [!NOTE]
> `KeychainSecurityGroup`屬性已被取代。 請改用 `iOSKeychainSecurityGroup` 屬性。 `TeamId`當您使用時，不需要前置詞 `iOSKeychainSecurityGroup` 。

### <a name="use-microsoft-authenticator"></a>使用 Microsoft Authenticator

您的應用程式可以使用 Microsoft Authenticator 作為訊息代理程式來啟用：

- **Sso**：當您啟用 sso 時，您的使用者不需要登入每個應用程式。
- **裝置識別**：使用裝置識別來透過存取裝置憑證進行驗證。 當裝置加入工作場所時，會在裝置上建立此憑證。 如果租使用者系統管理員啟用與裝置相關的條件式存取，您的應用程式將會準備就緒。
- **應用程式識別驗證**：當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 URL。 訊息代理程式會驗證重新導向 URL。

如需有關如何啟用 broker 的詳細資訊，請參閱 [使用 Microsoft Authenticator 或 Microsoft Intune Xamarin iOS 和 Android 應用程式上的公司入口網站](msal-net-use-brokers-with-xamarin-apps.md)。

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12 和驗證的已知問題

Microsoft 發行了有關 iOS 12 和某些驗證類型之間不相容性的資訊 [安全諮詢](https://github.com/aspnet/AspNetCore/issues/4647) 。 不相容會中斷社交、WSFed 和 OIDC 登入。安全性諮詢可協助您瞭解如何從應用程式移除 ASP.NET 安全性限制，使其與 iOS 12 相容。

當您在 Xamarin iOS 上開發 MSAL.NET 應用程式時，當您嘗試從 iOS 12 登入網站時，可能會看到無限迴圈。 這類行為類似于 GitHub 上的這個 ADAL 問題： [嘗試從 iOS 12 登入網站時，有無限迴圈的 #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)。

您也可能會在 ASP.NET Core OIDC authentication 與 iOS 12 Safari 中看到中斷。 如需詳細資訊，請參閱此 [WebKit 問題](https://bugs.webkit.org/show_bug.cgi?id=188165)。

## <a name="known-issues-with-ios-13-and-authentication"></a>IOS 13 和驗證的已知問題

如果您的應用程式需要條件式存取或憑證驗證支援，請讓您的應用程式與 Microsoft Authenticator broker 應用程式進行通訊。 然後，MSAL 會負責處理應用程式和 Microsoft Authenticator 之間的要求和回應。

在 iOS 13 上，Apple 藉由移除應用程式從外部應用程式透過自訂 URL 配置接收回應時的讀取來源應用程式功能，來進行重大的 API 變更。

Apple 的 [UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) 狀態檔：

> *如果要求是來自屬於您小組的另一個應用程式，UIKit 會將此金鑰的值設定為該應用程式的識別碼。如果原始應用程式的小組識別碼與目前應用程式的小組識別碼不同，則索引鍵的值為 nil。*

這項變更會因為 MSAL `UIApplication.SharedApplication.OpenUrl` 而依賴于驗證 MSAL 與 Microsoft Authenticator 應用程式之間的通訊，所以會中斷。

此外，在 iOS 13 上，開發人員必須在使用時提供簡報控制器 `ASWebAuthenticationSession` 。

如果您是使用 Xcode 11 建立，而您使用的是 iOS broker 或，則會影響您的應用程式 `ASWebAuthenticationSession` 。

在這種情況下，請使用 [MSAL.NET 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) 來啟用驗證成功。

### <a name="additional-requirements"></a>其他需求

- 使用最新的 MSAL 程式庫時，請確定裝置上已安裝 **Microsoft Authenticator 版本 6.3.19 +** 。
- 更新為 MSAL.NET 4.4.0 + 時，請在 plist 檔案中更新您的， `LSApplicationQueriesSchemes` 然後*Info.plist*新增 `msauthv3` ：

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    若 `msauthv3` 要偵測支援 iOS 13 的裝置上是否有最新的 Microsoft Authenticator 應用程式，則必須新增至 *Info. plist* 。

## <a name="report-an-issue"></a>回報問題

如果您有任何問題，或想要報告您在 MSAL.NET 中發現的問題，請在 GitHub 上的 [AzureAD/microsoft 驗證程式庫 dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) 存放庫中開啟問題。

## <a name="next-steps"></a>後續步驟

如需 Xamarin iOS 屬性的相關資訊，請參閱下列範例 README.md 檔的 [iOS 特定考慮](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) 段落：

範例 | 平台 | 描述
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、通用 Windows 平臺 (UWP)  | 簡單的 Xamarin Forms 應用程式，示範如何使用 MSAL 透過 Azure AD 2.0 端點驗證 Microsoft 個人帳戶和 Azure AD。 應用程式也會顯示如何使用產生的權杖來存取 Microsoft Graph。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
