---
title: Cookie 定義
titleSuffix: Azure AD B2C
description: 為 Azure 活動目錄 B2C 中使用的 Cookie 提供定義。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189509"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Azure AD B2C 的 Cookie 定義

以下各節提供有關 Azure 活動目錄 B2C（Azure AD B2C） 中使用的 Cookie 的資訊。

## <a name="samesite"></a>同一網站

Microsoft Azure AD B2C 服務與 SameSite 瀏覽器配置相容，`SameSite=None`包括對`Secure`屬性的支援。

為了保護對網站的訪問，Web 瀏覽器將引入一種新的按預設安全模式，假定所有 Cookie 都應受到保護，不受外部訪問，除非另有說明。 Chrome瀏覽器是第一個實施此更改，從 Chrome [80 于 2020 年 2 月開始](https://www.chromium.org/updates/same-site)。 有關 Chrome 中更改準備的詳細資訊，請參閱[開發人員：為新的相同網站做好準備。"無";在](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)鉻博客上保護 Cookie 設置。

開發人員必須使用新的 Cookie 設置`SameSite=None`，才能指定 Cookie 進行跨網站訪問。 當存在`SameSite=None`該屬性時，必須使用`Secure`其他屬性，以便只能通過 HTTPS 連接訪問跨網站 Cookie。 驗證和測試所有應用程式，包括使用 Azure AD B2C 的應用程式。

如需詳細資訊，請參閱

* [處理 Chrome 瀏覽器中的 SameSite Cookie 變更](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [對 Chrome 版本 80 或更高版本的客戶網站和 Microsoft 服務和產品的影響](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookie

下表列出了 Azure AD B2C 中使用的 Cookie。

| 名稱 | 網域 | 到期 | 目的 |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | [瀏覽器會話](session-behavior.md)結束 | 跨租戶保存使用者成員身份資料。 使用者是 成員的成員和級別的成員（管理員或使用者）的租戶。 |
| `x-ms-cpim-slice` | b2clogin.com、login.microsoftonline.com、品牌功能變數名稱 | [瀏覽器會話](session-behavior.md)結束 | 用於將請求路由到相應的生產實例。 |
| `x-ms-cpim-trans` | b2clogin.com、login.microsoftonline.com、品牌功能變數名稱 | [瀏覽器會話](session-behavior.md)結束 | 用於跟蹤事務（對 Azure AD B2C 的身份驗證請求數）和當前事務。 |
| `x-ms-cpim-sso:{Id}` | b2clogin.com、login.microsoftonline.com、品牌功能變數名稱 | [瀏覽器會話](session-behavior.md)結束 | 用於維護 SSO 會話。 |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com、login.microsoftonline.com、品牌功能變數名稱 | [瀏覽器會話](session-behavior.md)結束，成功身份驗證 | 用於維護請求狀態。 |
| `x-ms-cpim-csrf` | b2clogin.com、login.microsoftonline.com、品牌功能變數名稱 | [瀏覽器會話](session-behavior.md)結束 | 用於 CRSF 保護的跨網站請求偽造權杖。 |
| `x-ms-cpim-dc` | b2clogin.com、login.microsoftonline.com、品牌功能變數名稱 | [瀏覽器會話](session-behavior.md)結束 | 用於 Azure AD B2C 網路路由。 |
| `x-ms-cpim-ctx` | b2clogin.com、login.microsoftonline.com、品牌功能變數名稱 | [瀏覽器會話](session-behavior.md)結束 | Context |
| `x-ms-cpim-rp` | b2clogin.com、login.microsoftonline.com、品牌功能變數名稱 | [瀏覽器會話](session-behavior.md)結束 | 用於存儲資來源提供者租戶的成員資格資料。 |
| `x-ms-cpim-rc` | b2clogin.com、login.microsoftonline.com、品牌功能變數名稱 | [瀏覽器會話](session-behavior.md)結束 | 用於存儲繼電器 Cookie。 |
