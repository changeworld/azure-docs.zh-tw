---
title: 針對 TLS/SSL 問題進行疑難排解 (MSAL iOS/macOS) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 TLS/SSL 憑證搭配 MSAL 來處理各種問題。目標-C 程式庫。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80881072"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>如何：針對 iOS 和 macOS TLS/SSL 問題進行 MSAL 疑難排解

本文提供的資訊可協助您疑難排解使用[Microsoft 驗證程式庫 (MSAL) 適用于 iOS 和 macOS](reference-v2-libraries.md)時可能會遇到的問題

## <a name="network-issues"></a>網路問題

**錯誤-1200**：「發生 SSL 錯誤，無法建立與伺服器的安全連線。」

此錯誤表示連接不安全。 當憑證無效時，就會發生此錯誤。 如需詳細資訊，包括哪些伺服器無法進行 TLS 檢查，請參閱 `NSURLErrorFailingURLErrorKey` `userInfo` error 物件的字典中的。

此錯誤來自 Apple 的網路程式庫。 NSURL 錯誤碼的完整清單在 macOS 和 iOS Sdk 的 NSURLError 中。 如需此錯誤的詳細資訊，請參閱 [URL 載入系統錯誤碼](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)。

## <a name="certificate-issues"></a>憑證問題

如果提供無效憑證的 URL 連接到您想要在驗證流程中使用的伺服器，則診斷問題的好辦法是使用 SSL 驗證服務（例如 [Ssl 伺服器測試](https://www.ssllabs.com/ssltest/analyze.html)）來測試 URL。 它會針對各式各樣的案例和瀏覽器來測試伺服器，並檢查是否有許多已知的弱點。

根據預設，Apple 的新 [應用程式傳輸安全性 (ATS) ](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 功能會對使用 TLS/SSL 憑證的應用程式套用更嚴格的安全性原則。 某些作業系統和網頁瀏覽器預設已開始強制執行某些原則。 基於安全性理由，建議您不要停用 ATS。

使用 SHA-1 雜湊的憑證有已知的弱點。 大多數新式網頁瀏覽器不允許具有 SHA-1 雜湊的憑證。

## <a name="captive-portals"></a>驗證入口網站

當使用者第一次存取 Wi-Fi 網路，但尚未獲得該網路的存取權時，網頁驗證入口網站會向使用者呈現網頁。 它會攔截其網際網路流量，直到使用者滿足入口網站的需求為止。 網路錯誤，因為使用者無法連線到網路資源，直到使用者透過入口網站連接為止。

## <a name="next-steps"></a>後續步驟

深入瞭解如何 (ATS) 功能，來瞭解如何驗證 [入口網站](https://en.wikipedia.org/wiki/Captive_portal) 和 Apple 的新 [應用程式傳輸安全性 ](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 。
