---
title: Cookie 定義
titleSuffix: Azure AD B2C
description: 提供 Azure Active Directory B2C 中使用之 cookie 的定義。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189509"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Azure AD B2C 的 cookie 定義

下列各節提供 Azure Active Directory B2C （Azure AD B2C）中使用之 cookie 的相關資訊。

## <a name="samesite"></a>SameSite

Microsoft Azure AD B2C 服務與 SameSite 瀏覽器設定相容，包括支援具有 `Secure` 屬性的 `SameSite=None`。

為了保護網站的存取權，網頁瀏覽器會引進新的安全預設模型，假設除非另有指定，否則所有 cookie 都應該受到保護，以避免外部存取。 Chrome 瀏覽器是執行這項變更的第一個，從[2020 年2月的 chrome 80](https://www.chromium.org/updates/same-site)開始。 如需有關準備 Chrome 變更的詳細資訊，請參閱[開發人員：準備開始新的 SameSite = None;保護](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)Chromium 的網路上的 Cookie 設定。

開發人員必須使用新的 cookie 設定 `SameSite=None`，來指定跨網站存取的 cookie。 當 `SameSite=None` 屬性存在時，必須使用額外的 `Secure` 屬性，因此跨網站 cookie 只能透過 HTTPS 連線來存取。 驗證並測試您所有的應用程式，包括使用 Azure AD B2C 的應用程式。

如需詳細資訊，請參閱

* [處理 Chrome 瀏覽器中的 SameSite cookie 變更](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [對客戶網站和 Chrome 80 版或更新版本中的 Microsoft 服務和產品的影響](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

下表列出 Azure AD B2C 中使用的 cookie。

| 名稱 | 網域 | 到期 | 目的 |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | [瀏覽器會話](session-behavior.md)結束 | 跨租使用者保存使用者成員資格資料。 使用者為其成員的租使用者，以及成員資格的層級（系統管理員或使用者）。 |
| `x-ms-cpim-slice` | b2clogin.com、login.microsoftonline.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用來將要求路由傳送至適當的生產實例。 |
| `x-ms-cpim-trans` | b2clogin.com、login.microsoftonline.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於追蹤交易（Azure AD B2C 的驗證要求數目）和目前的交易。 |
| `x-ms-cpim-sso:{Id}` | b2clogin.com、login.microsoftonline.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於維護 SSO 會話。 |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com、login.microsoftonline.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束，驗證成功 | 用於維護要求狀態。 |
| `x-ms-cpim-csrf` | b2clogin.com、login.microsoftonline.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於 CRSF 保護的跨網站要求偽造 token。 |
| `x-ms-cpim-dc` | b2clogin.com、login.microsoftonline.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於 Azure AD B2C 網路路由。 |
| `x-ms-cpim-ctx` | b2clogin.com、login.microsoftonline.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 內容 |
| `x-ms-cpim-rp` | b2clogin.com、login.microsoftonline.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於儲存資源提供者租使用者的成員資格資料。 |
| `x-ms-cpim-rc` | b2clogin.com、login.microsoftonline.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於儲存轉送 cookie。 |
