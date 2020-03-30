---
title: 設定 keychain
titleSuffix: Microsoft identity platform
description: 瞭解如何配置鑰匙串，以便應用可以在鑰匙串中緩存權杖。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085859"
---
# <a name="configure-keychain"></a>設定 keychain

當[適用于 iOS 和 macOS](msal-overview.md) （MSAL） 的 Microsoft 身份驗證庫在使用者中簽名或刷新權杖時，它會嘗試在鑰匙串中緩存權杖。 鑰匙串中的緩存權杖允許 MSAL 在同一 Apple 開發人員分發的多個應用之間提供靜默單一登入 （SSO）。 SSO 是通過鑰匙串訪問組功能實現的。 有關詳細資訊，請參閱 Apple[的鑰匙串專案文檔](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)。

本文介紹如何配置應用授權，以便 MSAL 可以寫入 iOS 和 macOS 鑰匙串緩存的權杖。

## <a name="default-keychain-access-group"></a>預設鑰匙串訪問組

### <a name="ios"></a>iOS

預設情況下，iOS`com.microsoft.adalcache`上的 MSAL 使用訪問組。 這是 MSAL 和 Azure AD 身份驗證庫 （ADAL） SDK 使用的共用訪問組，可確保同一發行者中的多個應用之間獲得最佳的單一登入 （SSO） 體驗。

在 iOS 上`com.microsoft.adalcache`，在 **"專案設置** > **功能** > **鑰匙串共用**"下的 XCode 中將鑰匙串組添加到應用的權利

### <a name="macos"></a>macOS

預設情況下，macOS 上的`com.microsoft.identity.universalstorage`MSAL 使用訪問組。

由於 macOS 鑰匙串的限制，MSAL`access group`不會直接轉換為 macOS 10.14 及更早版本的鑰匙串訪問組屬性（請參閱[kSecAttrAccessGroup）。](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc) 但是，從 SSO 的角度來看，它的行為類似，可確保由同一 Apple 開發人員分發的多個應用程式可以具有靜默的 SSO。

在 macOS 10.15 起（macOS Catalina），MSAL 使用鑰匙串訪問組屬性實現靜默 SSO，類似于 iOS。

## <a name="custom-keychain-access-group"></a>自訂鑰匙串訪問組

如果要使用其他鑰匙串訪問組，可以在創建`MSALPublicClientApplicationConfig``MSALPublicClientApplication`之前傳遞自訂群組，如下所示：

# <a name="objective-c"></a>[目標C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>禁用鑰匙串共用

如果您不想在多個應用之間共用 SSO 狀態，或使用任何鑰匙串訪問組，請通過將應用程式捆綁包 ID 作為鑰匙串組來禁用鑰匙串共用：

# <a name="objective-c"></a>[目標C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>控制碼 -34018 錯誤（無法將專案設置為鑰匙串）

錯誤 -34018 通常意味著鑰匙串配置不正確。 確保已在 MSAL 中配置的鑰匙串訪問組與授權中配置的金鑰串訪問組匹配。

## <a name="ensure-your-application-is-properly-signed"></a>確定您的應用程式已正確簽署

在 macOS 上，應用程式無需開發人員簽名即可執行。 雖然 MSAL 的大部分功能將繼續工作，但通過鑰匙串訪問 SSO 需要對應用程式進行簽名。 如果您遇到多個鑰匙串提示，請確保應用程式的簽名有效。

## <a name="next-steps"></a>後續步驟

在 Apple 在[應用集合中共用對鑰匙串專案的存取權限](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)中瞭解有關鑰匙串訪問組的更多資訊。
