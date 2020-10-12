---
title: 如何處理 Chrome 瀏覽器中的 SameSite cookie 變更 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何處理 Chrome 瀏覽器中的 SameSite cookie 變更。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 5742ddf9553c3ac9187dbef93fc7927564cbc095
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116966"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>處理 Chrome 瀏覽器中的 SameSite Cookie 變更

## <a name="what-is-samesite"></a>什麼是 SameSite？

`SameSite` 是可在 HTTP cookie 中設定的屬性，以防止在 web 應用程式中 (CSRF) 攻擊的跨網站要求偽造：

- 當 `SameSite` 設定為 [不 **嚴格**] 時，會在相同網站內的要求和其他網站的 GET 要求中傳送 cookie。 它不會在跨網域的 GET 要求中傳送。
- **Strict**的值可確保 cookie 只會在相同的網站內傳送到要求中。

根據預設， `SameSite` 瀏覽器中不會設定此值，因此在要求中傳送的 cookie 沒有限制。 應用程式必須根據其需求設定不 **嚴格** 或 **嚴格** ，以加入宣告 CSRF 保護。

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite 變更和對驗證的影響

[SameSite 上標準](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)的最新更新， `SameSite` 會在沒有值設定為不嚴格的情況下，藉由建立的預設行為來提議保護應用程式。 此緩和措施表示 cookie 會限制在 HTTP 要求之外，但從其他網站進行時除外。 此外，也引進了「 **無** 」值來移除傳送 cookie 的限制。 這些更新很快就會在即將推出的 Chrome 瀏覽器版本中發行。

當 web 應用程式使用回應模式 "form_post" 向 Microsoft 身分識別平臺進行驗證時，登入伺服器會使用 HTTP POST 來回應應用程式，以傳送權杖或驗證碼。 因為此要求是跨網域要求 (從您的 `login.microsoftonline.com` 網域（例如 `https://contoso.com/auth`) ），所以您的應用程式所設定的 cookie 現在會落在 Chrome 中的新規則下。 跨網站案例中所需使用的 cookie 是保存 *狀態* 和 *nonce* 值的 cookie，也會在登入要求中傳送。 Azure AD 會捨棄其他 cookie 來保存會話。

如果您未更新 web 應用程式，這個新行為會導致驗證失敗。

## <a name="mitigation-and-samples"></a>風險降低與範例

為了克服驗證失敗，使用 Microsoft 身分識別平臺進行驗證的 web 應用程式可以 `SameSite` `None` 針對在 Chrome 瀏覽器上執行時，用於跨網域案例中的 cookie，將屬性設為。
其他瀏覽器 (在 [此](https://www.chromium.org/updates/same-site/incompatible-clients) 看到完整清單) 遵循先前的行為 `SameSite` ，如果設定，則不會包含 cookie `SameSite=None` 。
因此，若要在多個瀏覽器 web 應用程式上支援驗證，則必須 `SameSite` 在 Chrome 上將值設定為， `None` 並在其他瀏覽器上將值保留為空白。

下列程式碼範例會示範這種方法。

# <a name="net"></a>[.NET](#tab/dotnet)

下表顯示在我們的 ASP.NET 和 ASP.NET Core 範例中，處理 SameSite 變更的提取要求。

| 範例 | 提取要求 |
| ------ | ------------ |
|  [ASP.NET Core web 應用程式增量教學課程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [相同的網站 cookie 修正 #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC web 應用程式範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [相同的網站 cookie 修正 #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active directory-dotnet-系統管理-限制-範圍-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [相同的網站 cookie 修正 #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

如需如何處理 ASP.NET 和 ASP.NET Core 中 SameSite cookie 的詳細資訊，請參閱：

- [在 ASP.NET Core 中使用 SameSite cookie](/aspnet/core/security/samesite) 。
- [SameSite 問題的 ASP.NET Blog](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| 範例 |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| 範例 | 提取要求 |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [相同的網站 cookie 修正 #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [相同的網站 cookie 修正 #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>後續步驟

深入瞭解 SameSite 與 Web 應用程式案例：

> [!div class="nextstepaction"]
> [Google Chrome 在 SameSite 上的常見問題](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Chromium SameSite 頁面](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [案例：登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)