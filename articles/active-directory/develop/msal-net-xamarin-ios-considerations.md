---
title: Xamarin iOS 注意事項 （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解有關將 Xamarin iOS 與 Microsoft 身份驗證庫一起使用 .NET （MSAL.NET） 的注意事項。
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262707"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>將 Xamarin iOS 與MSAL.NET
在 Xamarin iOS 上使用 .NET （MSAL.NET） 的 Microsoft 身份驗證庫時，您應該： 

- 重寫和`OpenUrl`實現 中的`AppDelegate`函數。
- 啟用鑰匙串組。
- 啟用權杖緩存共用。
- 啟用鑰匙串訪問。
- 瞭解 iOS 12 和身份驗證的已知問題。

## <a name="implement-openurl"></a>實現打開 Url

重寫`OpenUrl``FormsApplicationDelegate`派生類和調用`AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`的方法。 以下是範例：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

還要執行以下任務： 
* 定義 URL 方案。
* 需要應用調用其他應用的許可權。
* 具有重定向 URL 的特定表單。
* 在[Azure 門戶](https://portal.azure.com)中註冊重定向 URL。

### <a name="enable-keychain-access"></a>啟用鑰匙串訪問

要啟用鑰匙串訪問，請確保應用程式具有鑰匙串訪問組。 使用 API 創建應用程式時，`WithIosKeychainSecurityGroup()`可以設置鑰匙串訪問組。

要從緩存和單一登入 （SSO） 中受益，請在所有應用程式中將鑰匙串訪問組設置為相同的值。

此設置的此示例使用 MSAL 4.x：
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

還啟用`Entitlements.plist`檔中的鑰匙串訪問。 使用以下訪問組或您自己的訪問組。

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

使用 API`WithIosKeychainSecurityGroup()`時，MSAL 會自動將安全性群組追加到應用程式*的團隊 ID* （）`AppIdentifierPrefix`的末尾。 MSAL 會添加安全性群組，因為當您在 Xcode 中構建應用程式時，它將執行相同的操作。 這就是為什麼`Entitlements.plist`檔中的權利需要在鑰匙串訪問組之前包含`$(AppIdentifierPrefix)`。

有關詳細資訊，請參閱[iOS 權利文檔](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)。 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>跨 iOS 應用程式啟用權杖緩存共用

從 MSAL 2.x 開始，可以指定一個鑰匙串訪問組，以在多個應用程式中保留權杖緩存。 此設置使您能夠在具有相同鑰匙串訪問組的多個應用程式之間共用權杖緩存。 您可以在[ADAL.NET](https://aka.ms/adal-net)應用程式、MSAL.NET Xamarin.iOS 應用程式以及[ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc)或[MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)中開發的本機 iOS 應用程式中共用權杖現金。

通過共用權杖緩存，允許在使用同一鑰匙串訪問組的所有應用程式之間單一登入 （SSO）。

要啟用此緩存共用，請使用`WithIosKeychainSecurityGroup()`方法將鑰匙串訪問組設置為共用同一緩存的所有應用程式中的相同值。 本文的第一個代碼示例演示如何使用 方法。

在本文前面，您瞭解到，每當使用 API`$(AppIdentifierPrefix)`時，`WithIosKeychainSecurityGroup()`您都會添加 MSAL。 MSAL 添加此元素，因為團隊`AppIdentifierPrefix`ID 可確保只有同一發行者製作的應用程式才能共用鑰匙串存取權限。

> [!NOTE]
> 屬性`KeychainSecurityGroup`已棄用。
> 
> 從 MSAL 2.x 開始，開發人員在使用`TeamId``KeychainSecurityGroup`該屬性時被迫包括首碼。 但是，從 MSAL 2.7.x 開始，當您`iOSKeychainSecurityGroup`使用新屬性時，MSAL`TeamId`在運行時解析首碼。 使用此屬性時，不要在值中包括`TeamId`首碼。 首碼不是必需的。
>
> 由於屬性`KeychainSecurityGroup`已過時，請使用 屬性`iOSKeychainSecurityGroup`。

### <a name="use-microsoft-authenticator"></a>使用微軟身份驗證器

您的應用程式可以使用 Microsoft 身份驗證器作為代理來啟用：

- **SSO：** 啟用 SSO 時，使用者無需登錄到每個應用程式。
- **設備標識**：使用設備標識通過訪問設備證書進行身份驗證。 此證書在設備加入工作區時在設備上創建。 如果租戶管理員啟用與設備相關的條件訪問，則應用程式將準備就緒。
- **應用程式標識驗證**：當應用程式調用代理時，它將傳遞其重定向 URL。 代理驗證重定向 URL。

有關如何啟用代理的詳細資訊，請參閱[在 Xamarin iOS 和 Android 應用程式上使用微軟身份驗證器或微軟 Intune 公司門戶](msal-net-use-brokers-with-xamarin-apps.md)。

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 和身份驗證的已知問題
Microsoft 發佈了有關 iOS 12 與某些類型的身份驗證不相容[的安全警告](https://github.com/aspnet/AspNetCore/issues/4647)。 不相容會破壞社交、WSFed 和 OIDC 登錄。安全諮詢可説明開發人員瞭解如何從應用程式中刪除ASP.NET安全限制，使其與 iOS 12 相容。  

當您在 Xamarin iOS 上開發MSAL.NET應用程式時，當您嘗試從 iOS 12 登錄網站時，您可能會看到無限迴圈。 此行為與此[ADAL 問題](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)類似。 

您可能還會看到 ASP.NET使用 iOS 12 Safari 進行核心 OIDC 身份驗證的中斷。 有關詳細資訊，請參閱此[WebKit 問題](https://bugs.webkit.org/show_bug.cgi?id=188165)。

## <a name="next-steps"></a>後續步驟

有關 Xamarin iOS 屬性的資訊，請參閱以下示例README.md檔中特定于[iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations)的注意事項段落：

範例 | Platform | 描述
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS， 安卓， 通用 Windows 平臺 （UWP） | 一個簡單的 Xamarin 表單應用，演示如何使用 MSAL 通過 Azure AD 2.0 終結點對 Microsoft 個人帳戶和 Azure AD 進行身份驗證。 該應用程式還演示如何使用生成的權杖訪問 Microsoft 圖形。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->