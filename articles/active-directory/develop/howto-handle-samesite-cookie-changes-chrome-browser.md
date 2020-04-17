---
title: 如何處理 Chrome 瀏覽器中的同一網站 Cookie 更改 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何在 Chrome 瀏覽器中處理 SameSite Cookie 更改。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: f28d3722d56582bd925d31b43b4a0219bca2ae30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534596"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>處理 Chrome 瀏覽器中的 SameSite Cookie 變更

## <a name="what-is-samesite"></a>什麼是同一網站?

`SameSite`是可在 HTTP Cookie 中設定的屬性,以防止 Web 應用程式中的跨網站請求偽造 (CSRF) 攻擊:

- 當`SameSite`設置為**Lax**時,Cookie 會在同一網站中的請求中發送,並在其他網站的 GET 請求中發送。 它不在跨域的 GET 請求中發送。
- 「**嚴格」** 的值可確保 Cookie 僅在同一網站中發送請求。

預設情況下,`SameSite`該值不會在瀏覽器中設置,這就是為什麼在請求中發送 Cookie 沒有限制的原因。 應用程式需要選擇加入 CSRF 保護,根據其要求設置**Lax**或 **「嚴格**」 。

## <a name="samesite-changes-and-impact-on-authentication"></a>同一網站改變與對身份驗證的影響

[SameSite 上標準的最新更新](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)建議通過將"不將任何值設置為`SameSite`Lax 時"的默認行為來保護應用。 此緩解意味著 Cookie 將限制 HTTP 請求,但從其他網站進行的 GET 除外。 此外,引入 **「無」** 值以取消對發送 Cookie 的限制。 這些更新將很快在即將推出的 Chrome 瀏覽器版本中發佈。

當 Web 應用使用回應模式「 form_post」使用 Microsoft 識別平台進行身份驗證時,登錄伺服器將使用 HTTP POST 回應應用程式以發送權杖或身份驗證代碼。 由於此請求是跨域請求(例如,從`login.microsoftonline.com`您的域 -`https://contoso.com/auth`例如 ),由你的應用設置的 Cookie 現在屬於 Chrome 中的新規則。 需要在跨網站方案中使用的 Cookie 是保存*狀態*和*nonce*值的 Cookie,這些值也在登錄請求中發送。 Azure AD 會刪除其他 Cookie 以舉行作業階段。

如果不更新 Web 應用,此新行為將導致身份驗證失敗。

## <a name="mitigation-and-samples"></a>緩解和範例

為了克服身份驗證失敗,使用 Microsoft 標識平台進行身份驗證的 Web`SameSite`應用可以`None`將 屬性設置為 用於在 Chrome 瀏覽器上運行跨域方案中使用的 Cookie。
其他瀏覽器(請參閱[此處](https://www.chromium.org/updates/same-site/incompatible-clients)的完整清單)遵循`SameSite`以前的`SameSite=None`行為,如果已設置,則不包括 Cookie。
因此,要支援多個瀏覽器 Web 應用的身份驗證`SameSite``None`,必須僅在 Chrome 上將該值設置為該值,並在其他瀏覽器上保留該值為空。

下面的代碼示例演示了此方法。

# <a name="net"></a>[.NET](#tab/dotnet)

下表介紹了有關我們ASP.NET和ASP.NET核心範例中 SameSite 更改的拉取請求。

| 範例 | 提取要求 |
| ------ | ------------ |
|  [ASP.NET核心 Web 應用程式增量教學](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [同一網站 Cookie 修復#261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC Web 應用程式範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [同一網站 Cookie 修復#35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [動作目錄點-管理-限制範圍-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [同一網站 Cookie 修復#28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

有關如何在ASP.NET和ASP.NET核心中處理 SameSite Cookie 的詳細資訊,請參閱:

- [在ASP.NET核心中使用同一網站 Cookie。](https://docs.microsoft.com/aspnet/core/security/samesite)
- [ASP.NET博客同一網站問題](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| 範例 |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| 範例 | 提取要求 |
| ------ | ------------ |
|  [ms-身份-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [同一網站 Cookie 修復#24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-身份-java-Webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [同一網站 Cookie 修復#4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>後續步驟

瞭解有關 SameSite 和 Web 應用方案的更多內容:

> [!div class="nextstepaction"]
> [谷歌Chrome在同一網站上的常見問題解答](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [鉻同網站頁面](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [專案:在使用者中簽名的 Web 應用](scenario-web-app-sign-user-overview.md)