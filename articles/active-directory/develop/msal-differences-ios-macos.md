---
title: 適用于 iOS & macOS 差異的 MSAL |蔚藍
titleSuffix: Microsoft identity platform
description: 描述 Microsoft 驗證程式庫 (MSAL) iOS 與 macOS 之間的使用差異。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 59e4111b6dda386777e820c9be16ace9ff01135c
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064910"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>適用於 iOS 和 macOS 的 Microsoft Authentication Library 差異

本文說明適用于 iOS 和 macOS 的 Microsoft 驗證程式庫 (MSAL) 之間的功能差異。

> [!NOTE]
> 在 Mac 上，MSAL 僅支援 macOS apps。

## <a name="general-differences"></a>一般差異

適用于 macOS 的 MSAL 是適用于 iOS 的功能子集。

適用于 macOS 的 MSAL 不支援：

- 不同的瀏覽器類型 `ASWebAuthenticationSession` ，例如、 `SFAuthenticationSession` 和 `SFSafariViewController` 。
- macOS 不支援透過 Microsoft Authenticator 應用程式進行代理驗證。

從相同發行者的應用程式之間 Keychain 共用，在 macOS 10.14 及更早版本上會有更多的限制。 使用 [存取控制清單](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) 來指定應該共用 keychain 的應用程式路徑。 使用者可能會看到其他 keychain 提示。

在 macOS 10.15 + 上，iOS 和 macOS 之間的 MSAL 行為相同。 MSAL 使用 [keychain 存取群組](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) 進行 keychain 共用。 

### <a name="conditional-access-authentication-differences"></a>條件式存取驗證差異

在條件式存取案例中，當您使用 MSAL for iOS 時，會有較少的使用者提示。 這是因為 iOS 會使用訊息代理程式應用程式 (Microsoft Authenticator) 在某些情況下不需要提示使用者。

### <a name="project-setup-differences"></a>專案設定差異

**macOS**

- 當您在 macOS 上設定專案時，請確定您的應用程式已使用有效的開發或生產憑證進行簽署。 MSAL 仍可在不帶正負號的模式下運作，但在快取持續性方面的行為會有所不同。 應用程式應該只執行未簽署，以進行偵測。 如果您散發未簽署的應用程式，它將會：
1. 在10.14 及更早版本中，MSAL 會在每次重新開機應用程式時提示使用者輸入 keychain 密碼。
2. 在 10.15 + 上，MSAL 會提示使用者提供每個取得權杖的認證。 

- macOS 應用程式不需要執行 AppDelegate 呼叫。

**iOS**

- 有額外的步驟可設定您的專案以支援驗證 broker 流程。 這些步驟會在教學課程中呼叫。
- iOS 專案需要在 plist 中註冊自訂配置。 這在 macOS 上並不是必要的。
