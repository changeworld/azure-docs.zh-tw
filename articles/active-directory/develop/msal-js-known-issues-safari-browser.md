---
title: 已知的 Safari 瀏覽器問題（MSAL） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解使用適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL）與 Safari 瀏覽器時的已知問題。
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696107"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>使用 MSAL 的 Safari 瀏覽器已知問題 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Safari 12 和 ITP 2.0 上的無訊息權杖更新

Apple iOS 12 和 MacOS 10.14 作業系統包含[Safari 12 瀏覽器](https://developer.apple.com/safari/whats-new/)的版本。 基於安全性和隱私權的目的，Safari 12 包含[智慧型追蹤防護 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)。 這基本上會導致瀏覽器卸載所設定的協力廠商 cookie。 ITP 2.0 也會將身分識別提供者所設定的 cookie 視為協力廠商 cookie。

### <a name="impact-on-msaljs"></a>對 MSAL 的影響

MSAL 會使用隱藏的 Iframe 來執行無訊息的權杖取得和更新，做為 `acquireTokenSilent` 呼叫的一部分。 無訊息權杖要求依賴 Iframe 可存取由 Azure AD 所設定之 cookie 所表示的已驗證使用者會話。 透過 ITP 2.0 防止存取這些 cookie，MSAL 無法以無訊息方式取得和更新權杖，這會導致 `acquireTokenSilent` 失敗。

目前沒有解決此問題的解決方案，我們正在評估具有標準社區的選項。

### <a name="work-around"></a>解決方法

根據預設，會在 Safari 瀏覽器上啟用 ITP 設定。 您可以藉由流覽至 [**喜好**設定] -> [**隱私權**]，並取消核取 [**防止跨網站追蹤**] 選項來停用此設定

![safari 設定](./media/msal-js-known-issue-safari-browser/safari.png)

您將需要使用互動式取得權杖呼叫來處理 `acquireTokenSilent` 失敗，這會提示使用者登入。
若要避免重複登入，您可以執行的方法是處理 `acquireTokenSilent` 失敗，並提供使用者一個選項來停用 Safari 中的 ITP 設定，然後再繼續進行互動式呼叫。 停用設定後，後續的無訊息權杖更新應該就會成功。
