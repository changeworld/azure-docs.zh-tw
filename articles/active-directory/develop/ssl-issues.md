---
title: 排除 TLS/SSL 問題（MSAL iOS/macOS） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用帶有 MSAL 的 TLS/SSL 憑證解決各種問題。目標 C 庫。
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
ms.openlocfilehash: 42bd016a9d0882cc97eaa800235226b2de9569a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369405"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>如何：針對 iOS 和 macOS TLS/SSL 問題排除 MSAL 故障

本文提供的資訊可説明您解決在使用[適用于 iOS 和 macOS 的 Microsoft 身份驗證庫 （MSAL）](reference-v2-libraries.md)時可能遇到的問題

## <a name="network-issues"></a>網路問題

**錯誤 -1200**："發生了 SSL 錯誤，無法與伺服器建立安全連線。

此錯誤表示連接不安全。 當證書無效時，就會發生這種情況。 有關詳細資訊（包括哪個伺服器未通過 TLS 檢查），請參閱`NSURLErrorFailingURLErrorKey`錯誤物件的`userInfo`字典。

此錯誤來自 Apple 的網路庫。 NSURL 錯誤代碼的完整清單位於 macOS 和 iOS SDK 中的 NSURLError.h 中。 有關此錯誤的詳細資訊，請參閱[URL 載入系統錯誤代碼](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)。

## <a name="certificate-issues"></a>憑證問題

如果提供無效證書的 URL 連接到您打算用作身份驗證流的一部分的伺服器，則診斷問題的一個好開始是使用 SSL 驗證服務（如[SSL 伺服器測試](https://www.ssllabs.com/ssltest/analyze.html)）測試 URL。 它根據各種方案和瀏覽器測試伺服器，並檢查許多已知的漏洞。

預設情況下，Apple 新的[應用傳輸安全 （ATS）](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)功能對使用 TLS/SSL 憑證的應用應用更嚴格的安全性原則。 預設情況下，某些作業系統和 Web 瀏覽器已開始強制實施其中一些策略。 出於安全原因，我們建議您不要禁用 ATS。

使用 SHA-1 雜湊的證書具有已知的漏洞。 大多數現代 Web 瀏覽器不允許使用 SHA-1 雜湊的證書。

## <a name="captive-portals"></a>捕獲門戶

強制門戶在使用者首次訪問 Wi-Fi 網路且尚未被授予對該網路的存取權限時向使用者顯示網頁。 它攔截他們的互聯網流量，直到使用者滿足門戶的要求。 網路錯誤，因為使用者無法連接到網路資源，在使用者通過門戶進行連接之前，應進行網路錯誤。

## <a name="next-steps"></a>後續步驟

瞭解[捕獲門戶](https://en.wikipedia.org/wiki/Captive_portal)和 Apple 新的[應用程式傳輸安全 （ATS）](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)功能。
