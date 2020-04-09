---
title: 排除 TLS/SSL 問題(MSAL iOS/macOS) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用帶有 MSAL 的 TLS/SSL 憑證解決各種問題。目標 C 庫。
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881072"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>如何:針對 iOS 和 macOS TLS/SSL 問題排除 MSAL 故障

本文提供的資訊可協助您解決在使用[適用於 iOS 和 macOS 的 Microsoft 身份驗證函式庫 (MSAL)](reference-v2-libraries.md)時可能遇到的問題

## <a name="network-issues"></a>網路問題

**錯誤 -1200**:"發生了 SSL 錯誤,無法與伺服器建立安全連接。

此錯誤表示連接不安全。 當證書無效時,就會發生這種情況。 有關詳細資訊(包括哪個伺服器未通過 TLS 檢查),請`NSURLErrorFailingURLErrorKey`參閱`userInfo`錯誤物件的 字典。

此錯誤來自 Apple 的網路庫。 NSURL 錯誤代碼的完整清單位於 macOS 和 iOS SDK 中的 NSURLError.h 中。 關於此錯誤的詳細資訊,請參考[網址載入系統錯誤程式](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)。

## <a name="certificate-issues"></a>憑證問題

如果提供無效憑證的 URL 連接到您打算用作身份驗證流的一部分的伺服器,則診斷問題的一個好開始是使用 SSL 驗證服務(如[SSL 伺服器測試](https://www.ssllabs.com/ssltest/analyze.html))測試 URL。 它根據各種方案和瀏覽器測試伺服器,並檢查許多已知的漏洞。

默認情況下,Apple 新的[應用傳輸安全 (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)功能對使用 TLS/SSL 憑證的應用應用更嚴格的安全策略。 默認情況下,某些作業系統和 Web 瀏覽器已開始強制實施其中一些策略。 出於安全原因,我們建議您不要禁用 ATS。

使用 SHA-1 哈希的證書具有已知的漏洞。 大多數現代 Web 瀏覽器不允許使用 SHA-1 哈希的證書。

## <a name="captive-portals"></a>捕獲門戶

強制門戶在使用者首次訪問 Wi-Fi 網路且尚未被授予對該網路的訪問許可權時向使用者顯示網頁。 它攔截他們的互聯網流量,直到用戶滿足門戶的要求。 網路錯誤,因為使用者無法連接到網路資源,在使用者通過門戶進行連接之前,應進行網路錯誤。

## <a name="next-steps"></a>後續步驟

瞭解[捕獲門戶](https://en.wikipedia.org/wiki/Captive_portal)和 Apple 新的[應用程式傳輸安全 (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)功能。
