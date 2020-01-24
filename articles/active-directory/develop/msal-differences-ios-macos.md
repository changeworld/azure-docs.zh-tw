---
title: MSAL for iOS & macOS 差異 |Azure
titleSuffix: Microsoft identity platform
description: 描述 iOS 與 macOS 之間的 Microsoft Authentication Library （MSAL）使用方式差異。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a17739151c56fec92a9697e1e04c39cb44da5088
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696226"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>適用於 iOS 和 macOS 的 Microsoft Authentication Library 差異

本文說明適用于 iOS 和 macOS 的 Microsoft 驗證程式庫（MSAL）之間的功能差異。

> [!NOTE]
> 在 Mac 上，MSAL 只支援 macOS 應用程式。

## <a name="general-differences"></a>一般差異

MSAL for macOS 是適用于 iOS 的功能子集。

MSAL for macOS 不支援：

- 不同的瀏覽器類型，例如 `ASWebAuthenticationSession`、`SFAuthenticationSession``SFSafariViewController`。
- macOS 不支援透過 Microsoft Authenticator 應用程式進行代理驗證。

相同發行者的應用程式之間的 Keychain 共用，在 macOS 10.14 和更早版本上會受到限制。 使用[存取控制清單](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc)來指定應該共用 keychain 之應用程式的路徑。 使用者可能會看到其他 keychain 提示。

在 macOS 10.15 + 上，MSAL 的行為在 iOS 和 macOS 之間是相同的。 MSAL 會使用[keychain 存取群組](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)來進行 keychain 共用。 

### <a name="conditional-access-authentication-differences"></a>條件式存取驗證差異

針對條件式存取案例，當您使用適用于 iOS 的 MSAL 時，會有較少的使用者提示。 這是因為 iOS 會使用 broker 應用程式（Microsoft Authenticator），這在某些情況下會否定提示使用者的需求。

### <a name="project-setup-differences"></a>專案設定差異

**macOS**

- 當您在 macOS 上設定專案時，請確定您的應用程式已使用有效的開發或實際執行憑證進行簽署。 MSAL 仍可在不帶正負號的模式中運作，但其行為會因快取持續性而有所不同。 應用程式應該只在不帶正負號的情況中執行，以進行調試。 如果您將應用程式散發為不帶正負號，它會：
1. 在10.14 和更早版本中，MSAL 會在每次重新開機應用程式時，提示使用者輸入 keychain 密碼。
2. 在 10.15 + 上，MSAL 會提示使用者提供每個權杖取得的認證。 

- macOS 應用程式不需要執行 AppDelegate 呼叫。

**iOS**

- 有額外的步驟可設定您的專案，以支援驗證訊息代理程式流程。 這些步驟會在教學課程中加以呼叫。
- iOS 專案必須在 plist 中註冊自訂配置。 這在 macOS 上不是必要的。
