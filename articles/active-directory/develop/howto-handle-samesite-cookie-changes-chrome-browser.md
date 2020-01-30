---
title: 如何處理 Chrome 瀏覽器中的 SameSite cookie 變更 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何處理 Chrome 瀏覽器中的 SameSite cookie 變更。
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 8fc1fab89a89fbf7e20414f292a1b02f77ac7907
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776361"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>處理 Chrome 瀏覽器中的 SameSite Cookie 變更

## <a name="what-is-samesite"></a>什麼是 SameSite？

`SameSite` 是可以在 HTTP cookie 中設定的屬性，以防止 web 應用程式中的跨網站要求偽造（CSRF）攻擊：

- 當 `SameSite` 設定為 [**寬鬆**] 時，cookie 會在相同網站內的要求中，以及從其他網站的 GET 要求中傳送。 它不會在跨網域的 GET 要求中傳送。
- **Strict**值會確保 cookie 只會在相同網站內的要求中傳送。

根據預設，不會在瀏覽器中設定 `SameSite` 值，這就是為什麼在要求中傳送 cookie 不受限制的原因。 應用程式必須根據其需求設定**寬鬆**或**嚴格**，以加入宣告 CSRF 保護。

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite 變更和對驗證的影響

最新[的 SameSite 標準更新](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)會藉由在沒有值設定為不嚴格的情況下，提供 `SameSite` 的預設行為，來建議保護應用程式。 這種緩和措施表示 cookie 會受到限制，但不包括從其他網站取得的 HTTP 要求。 此外，引進的值為 [**無**]，以移除所傳送 cookie 的限制。 這些更新很快就會在即將推出的 Chrome 瀏覽器版本中發行。

當 web 應用程式使用回應模式 "form_post" 向 Microsoft 身分識別平臺進行驗證時，登入伺服器會使用 HTTP POST 來回應應用程式，以傳送權杖或授權碼。 因為此要求是跨網域的要求（從 `login.microsoftonline.com` 到您的網域（例如 https://contoso.com/auth) ），所以您的應用程式所設定的 cookie 現在會落在 Chrome 的新規則之下。 需要在跨網站案例中使用的 cookie 是保存*狀態*和*nonce*值的 cookie，也會在登入要求中傳送。 Azure AD 會捨棄其他的 cookie 來存放會話。

如果您未更新 web 應用程式，這個新行為會導致驗證失敗。

## <a name="mitigation-and-samples"></a>風險降低和範例

若要克服驗證失敗，使用 Microsoft 身分識別平臺進行驗證的 web 應用程式可以將 `SameSite` 屬性設定為在 Chrome 瀏覽器上執行時用於跨網域案例的 cookie `None`。
其他瀏覽器（如需完整清單，請參閱[這裡](https://www.chromium.org/updates/same-site/incompatible-clients)）遵循先前的 `SameSite` 行為，如果已設定 `SameSite=None` 則不會包含 cookie。
因此，若要在多個瀏覽器上支援驗證，web 應用程式必須將 `SameSite` 值設定為僅在 Chrome 上 `None`，並在其他瀏覽器上將此值保留空白。

下面的程式碼範例會示範這個方法。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

下表顯示在我們的 ASP.NET 和 ASP.NET Core 範例中，針對 SameSite 變更進行處理的提取要求。

| 範例 | 提取要求 |
| ------ | ------------ |
|  [ASP.NET Core Web 應用程式累加式教學課程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [相同的網站 cookie 修正 #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC Web 應用程式範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [相同的網站 cookie 修正 #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active directory-dotnet-受管理員限制-範圍-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [相同的網站 cookie 修正 #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

如需如何在 ASP.NET 和 ASP.NET Core 中處理 SameSite cookie 的詳細資訊，請參閱：

- [在 ASP.NET Core 中使用 SameSite cookie](https://docs.microsoft.com/aspnet/core/security/samesite) 。
- [SameSite 問題的 ASP.NET Blog](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="pythontabpython"></a>[Python](#tab/python)

| 範例 |
| ------ |
|  [毫秒-身分識別-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="javatabjava"></a>[Java](#tab/java)

| 範例 | 提取要求 |
| ------ | ------------ |
|  [ms-身分識別-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [相同的網站 cookie 修正 #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-身分識別-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [相同的網站 cookie 修正 #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>後續步驟

深入瞭解 SameSite 和 Web 應用程式案例：

> [!div class="nextstepaction"]
> [Google Chrome 在 SameSite 上的常見問題](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Chromium SameSite 頁面](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [案例：登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)