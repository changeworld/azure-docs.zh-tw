---
title: 針對 SSL 問題進行疑難排解（MSAL iOS/macOS） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解搭配 MSAL 使用 SSL 憑證的各種問題。目標-C 程式庫。
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: e278b928cc7f1e7f830ba246545ea52944a2e252
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084357"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-ssl-issues"></a>如何：針對 iOS 和 macOS SSL 問題的 MSAL 進行疑難排解

本文提供的資訊可協助您疑難排解在使用[適用于 iOS 和 macOS 的 Microsoft 驗證程式庫（MSAL）](reference-v2-libraries.md)時可能會遇到的問題

## <a name="network-issues"></a>網路問題

**錯誤-1200**：「發生 SSL 錯誤，無法建立與伺服器的安全連線。」

此錯誤表示連接不安全。 當憑證無效時，就會發生此錯誤。 如需詳細資訊（包括 SSL 檢查失敗的伺服器），請參閱錯誤物件之 `userInfo` 字典中的 `NSURLErrorFailingURLErrorKey`。

此錯誤來自 Apple 的網路程式庫。 NSURL 錯誤碼的完整清單位於 macOS 和 iOS Sdk 的 NSURLError 中。 如需有關此錯誤的詳細資訊，請參閱[URL 載入系統錯誤碼](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)。

## <a name="certificate-issues"></a>憑證問題

如果提供無效憑證的 URL 連線到您想要在驗證流程中使用的伺服器，則診斷問題的好起點是使用 SSL 驗證服務（例如[QUALYS Ssl Labs Analyzer](https://www.ssllabs.com/ssltest/analyze.html)）來測試 url。 它會針對各種案例和瀏覽器測試伺服器，並檢查是否有許多已知的弱點。

根據預設，Apple 的新[應用程式傳輸安全性（ATS）](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)功能會將更嚴格的安全性原則套用至使用 SSL 憑證的應用程式。 某些作業系統和網頁瀏覽器預設已開始強制執行其中一些原則。 基於安全性理由，建議您不要停用 ATS。

使用 SHA-1 雜湊的憑證具有已知的弱點。 大部分的新式網頁瀏覽器不允許具有 SHA-1 雜湊的憑證。

## <a name="captive-portals"></a>驗證入口網站

當使用者第一次存取 Wi-fi 網路，但尚未被授與該網路的存取權時，會向該網頁呈現網頁。 它會攔截其網際網路流量，直到使用者符合入口網站的需求為止。 網路錯誤，因為使用者必須透過入口網站連線，否則無法連線到網路資源。

## <a name="next-steps"></a>後續步驟

瞭解如何驗證[入口網站](https://en.wikipedia.org/wiki/Captive_portal)和 Apple 的新[應用程式傳輸安全性（ATS）](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)功能。
