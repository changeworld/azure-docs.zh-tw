---
title: 適用于 iOS & macOS 差異的 MSAL |蔚藍
titleSuffix: Microsoft identity platform
description: 描述 iOS 和 macOS 之間的 Microsoft 身份驗證庫 （MSAL） 使用差異。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084975"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>適用於 iOS 和 macOS 的 Microsoft Authentication Library 差異

本文介紹了適用于 iOS 和 macOS 的 Microsoft 身份驗證庫 （MSAL） 之間的功能差異。

> [!NOTE]
> 在 Mac 上，MSAL 僅支援 macOS 應用。

## <a name="general-differences"></a>一般差異

macOS 的 MSAL 是可用於 iOS 的功能的子集。

macOS 的 MSAL 不支援：

- 不同的瀏覽器類型，如`ASWebAuthenticationSession` `SFAuthenticationSession`。 `SFSafariViewController`
- macOS 不支援通過 Microsoft 身份驗證器應用進行代理身份驗證。

在同一發行者的應用之間的鑰匙串共用在 macOS 10.14 和更早版本上更為有限。 使用[存取控制清單](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc)指定應共用鑰匙串的應用的路徑。 使用者可能會看到其他鑰匙串提示。

在 macOS 10.15+ 上，MSAL 的行為在 iOS 和 macOS 之間是相同的。 MSAL 使用[鑰匙串訪問組](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)進行鑰匙串共用。 

### <a name="conditional-access-authentication-differences"></a>條件訪問身份驗證差異

對於條件訪問方案，當您將 MSAL 用於 iOS 時，使用者提示將會減少。 這是因為 iOS 使用代理應用程式（Microsoft 身份驗證器），這在某些情況下無需提示使用者。

### <a name="project-setup-differences"></a>專案設置差異

**macOS**

- 在 macOS 上設置專案時，請確保應用程式已使用有效的開發或生產證書進行簽名。 MSAL 仍然在無符號模式下工作，但在緩存持久性方面，其行為會有所不同。 應用應僅出於調試目的無符號運行。 如果分發無符號應用，它將：
1. 在 10.14 及更早版本，MSAL 將在使用者每次重新開機應用時提示使用者輸入鑰匙串密碼。
2. 在 10.15+上，MSAL 將提示使用者獲取每次權杖採集的憑據。 

- macOS 應用不需要實現 AppDelegate 調用。

**iOS**

- 還有其他步驟可以設置專案以支援身份驗證代理流。 本教程中調用了這些步驟。
- iOS 專案需要在 info.plist 中註冊自訂方案。 這在 macOS 上不是必需的。
