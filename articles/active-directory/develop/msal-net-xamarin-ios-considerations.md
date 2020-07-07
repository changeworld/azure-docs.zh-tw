---
title: Xamarin iOS 考慮（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解搭配使用 Xamarin iOS 與適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）的考慮。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7125559dd39e1626634dae7c45b0744bfff57d8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82652664"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>使用 Xamarin iOS 搭配 MSAL.NET 的考慮
當您在 Xamarin iOS 上使用適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）時，您應該： 

- 在中覆寫並執行 `OpenUrl` 函數 `AppDelegate` 。
- 啟用 keychain 群組。
- 啟用權杖快取共用。
- 啟用 keychain 存取。
- 瞭解 iOS 12 和驗證的已知問題。

## <a name="implement-openurl"></a>執行 OpenUrl

覆寫 `OpenUrl` `FormsApplicationDelegate` 衍生類別的方法，並呼叫 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` 。 以下是範例：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

同時執行下列工作： 
* 定義 URL 配置。
* 需要您應用程式的許可權，才能呼叫另一個應用程式。
* 具有特定形式的重新導向 URL。
* 在[Azure 入口網站](https://portal.azure.com)中註冊重新導向 URL。

### <a name="enable-keychain-access"></a>啟用 keychain 存取

若要啟用 keychain 存取，請確定您的應用程式具有 keychain 存取群組。 當您使用 API 建立應用程式時，可以設定 keychain 存取群組 `WithIosKeychainSecurityGroup()` 。

若要受益于快取和單一登入（SSO），請在您的所有應用程式中，將 keychain 存取群組設定為相同的值。

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

當您使用 `WithIosKeychainSecurityGroup()` API 時，MSAL 會自動將您的安全性群組附加至應用程式*小組識別碼*（）的結尾 `AppIdentifierPrefix` 。 MSAL 會新增您的安全性群組，因為當您在 Xcode 中建立應用程式時，它會執行相同的動作。 這就是為什麼檔案中的權利 `Entitlements.plist` 必須包含在 `$(AppIdentifierPrefix)` keychain 存取群組之前的原因。

如需詳細資訊，請參閱[iOS 權利檔](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)。 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>啟用跨 iOS 應用程式的權杖快取共用

從 MSAL 2.x 開始，您可以指定 keychain 存取群組，在多個應用程式中保存權杖快取。 此設定可讓您在具有相同 keychain 存取群組的數個應用程式之間共用權杖快取。 您可以在[ADAL.NET](https://aka.ms/adal-net)應用程式、MSAL.NET Xamarin iOS 應用程式，以及在[ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc)或[MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)中開發的原生 iOS 應用程式之間共用權杖現金。

藉由共用權杖快取，您可以在所有使用相同 keychain 存取群組的應用程式之間，允許單一登入（SSO）。

若要啟用此快取共用，請使用 `WithIosKeychainSecurityGroup()` 方法，在共用相同快取的所有應用程式中，將 keychain 存取群組設定為相同的值。 本文中的第一個程式碼範例會示範如何使用方法。

稍早在本文中，您已瞭解 MSAL 會在 `$(AppIdentifierPrefix)` 您每次使用 API 時新增 `WithIosKeychainSecurityGroup()` 。 MSAL 會新增此元素，因為小組識別碼 `AppIdentifierPrefix` 可確保只有相同發行者所建立的應用程式可以共用 keychain 存取權。

> [!NOTE]
> `KeychainSecurityGroup`屬性已被取代。
> 
> 從 MSAL 2.x 開始，開發人員在 `TeamId` 使用屬性時，會強制包含前置詞 `KeychainSecurityGroup` 。 但從 MSAL 2.7. x 開始，當您使用新的 `iOSKeychainSecurityGroup` 屬性時，MSAL 會在執行時間解析 `TeamId` 前置詞。 當您使用此屬性時，請不要在 `TeamId` 值中包含前置詞。 不需要前置詞。
>
> 因為 `KeychainSecurityGroup` 屬性已過時，請使用 `iOSKeychainSecurityGroup` 屬性。

### <a name="use-microsoft-authenticator"></a>使用 Microsoft Authenticator

您的應用程式可以使用 Microsoft Authenticator 做為 broker 來啟用：

- **Sso**：當您啟用 sso 時，您的使用者不需要登入每個應用程式。
- **裝置識別**：藉由存取裝置憑證，使用裝置識別來進行驗證。 此憑證會在加入工作場所的裝置上建立。 如果租使用者系統管理員啟用與裝置相關的條件式存取，您的應用程式就會準備就緒。
- **應用程式識別驗證**：當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 URL。 訊息代理程式會驗證重新導向 URL。

如需如何啟用訊息代理程式的詳細資訊，請參閱[在 Xamarin iOS 和 Android 應用程式上使用 Microsoft Authenticator 或 Microsoft Intune 公司入口網站](msal-net-use-brokers-with-xamarin-apps.md)。

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12 和驗證的已知問題
Microsoft 發行了有關 iOS 12 與某些驗證類型之間不相容性的[安全性摘要報告](https://github.com/aspnet/AspNetCore/issues/4647)。 不相容會中斷社交、WSFed 和 OIDC 的登入。安全性摘要報告可協助開發人員瞭解如何從其應用程式移除 ASP.NET 的安全性限制，使其與 iOS 12 相容。  

當您在 Xamarin iOS 上開發 MSAL.NET 應用程式時，當您嘗試從 iOS 12 登入網站時，可能會看到無限迴圈。 這種行為與此[ADAL 問題](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)類似。 

您也可能會看到 ASP.NET Core OIDC authentication 與 iOS 12 Safari 的中斷。 如需詳細資訊，請參閱此[WebKit 問題](https://bugs.webkit.org/show_bug.cgi?id=188165)。

## <a name="next-steps"></a>後續步驟

如需 Xamarin iOS 屬性的相關資訊，請參閱下列範例 README.md 檔案的[iOS 特有考慮](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations)段落：

範例 | 平台 | 描述
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、通用 Windows 平臺（UWP） | 簡單的 Xamarin Forms 應用程式，示範如何使用 MSAL 來驗證 Microsoft 個人帳戶，並透過 Azure AD 2.0 端點 Azure AD。 應用程式也會顯示如何使用產生的權杖來存取 Microsoft Graph。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->