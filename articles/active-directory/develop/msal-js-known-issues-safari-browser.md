---
title: 已知的 Safari 瀏覽器問題 （MSAL.js） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解使用適用于 Safari 瀏覽器的 Microsoft 身份驗證庫 （MSAL.js） 時的問題。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696107"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Safari 瀏覽器上已知問題與 MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Safari 12 和 ITP 2.0 上的靜默權杖續訂

蘋果iOS 12和MacOS10.14作業系統包括[Safari12瀏覽器](https://developer.apple.com/safari/whats-new/)的發佈。 出於安全和隱私的目的，Safari 12 包括[智慧跟蹤防護 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)。 這實質上會導致瀏覽器刪除正在設置的協力廠商 Cookie。 ITP 2.0 還將標識供應商設置的 Cookie 視為協力廠商 Cookie。

### <a name="impact-on-msaljs"></a>對 MSAL.js 的影響

MSAL.js 使用隱藏的 Iframe 作為`acquireTokenSilent`調用的一部分執行靜默權杖獲取和續訂。 靜默權杖請求依賴于 Iframe 有權訪問 Azure AD 設置的 Cookie 表示的經過身份驗證的使用者會話。 由於 ITP 2.0 阻止訪問這些 Cookie，MSAL.js 無法靜默獲取和續訂權杖，從而導致`acquireTokenSilent`失敗。

目前還沒有解決這個問題的解決方案，我們正在與標準社區一起評估各種選項。

### <a name="work-around"></a>四處工作

預設情況下，在 Safari 瀏覽器上啟用 ITP 設置。 您可以通過導航到 **"首選項** -> **隱私**"並取消選中 **"防止跨網站跟蹤**"選項來禁用此設置。

![野生動物園設置](./media/msal-js-known-issue-safari-browser/safari.png)

您需要使用互動式獲取權杖調用`acquireTokenSilent`來處理故障，這提示使用者登錄。
為了避免重複登錄，您可以實現的一種方法是處理`acquireTokenSilent`故障，並在繼續互動式調用之前為使用者提供禁用 Safari 中的 ITP 設置的選項。 禁用設置後，後續靜默權杖續訂應成功。
