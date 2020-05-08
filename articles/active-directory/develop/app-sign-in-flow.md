---
title: 使用 Microsoft 身分識別平臺的應用程式登入流程 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解 Microsoft 身分識別平臺（v2.0）中 web、桌面和行動應用程式的登入流程。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 7b326e17611b5f4b9520d8218a28a67afe9a851a
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584346"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>使用 Microsoft 身分識別平臺的應用程式登入流程

本主題討論使用 Microsoft 身分識別平臺的 web、桌面和行動應用程式的基本登入流程。 若要瞭解 Microsoft 身分識別平臺支援的登入案例，請參閱[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)。

## <a name="web-app-sign-in-flow"></a>Web 應用程式登入流程

當使用者在瀏覽器中導覽至 web 應用程式時，將會發生下列情況：

* Web 應用程式會決定使用者是否經過驗證。
* 如果使用者未經過驗證，web 應用程式會委派給 Azure AD 以登入使用者。 該登入將與組織的原則相容，這可能表示要求使用者輸入其認證、使用多重要素驗證，或完全不使用密碼（例如使用 Windows Hello）。
* 系統會要求使用者同意用戶端應用程式所需的存取權。 這就是為什麼用戶端應用程式需要向 Azure AD 註冊，因此 Microsoft 身分識別平臺可以提供權杖，代表使用者已同意的存取權。

當使用者成功通過驗證時：

* Microsoft 身分識別平臺會將權杖傳送至 web 應用程式。
* 儲存的 cookie 會與 Azure AD 的網域相關聯，其中包含瀏覽器 cookie jar 中的使用者身分識別。 下次應用程式使用瀏覽器流覽至 Microsoft 身分識別平臺授權端點時，瀏覽器會顯示 cookie，讓使用者不必再次登入。 這也是 SSO 的達成方式。 Cookie 是由 Azure AD 所產生，而且只能由 Azure AD 瞭解。
* 然後，web 應用程式會驗證權杖。 如果驗證成功，web 應用程式會顯示受保護的頁面，並將會話 cookie 儲存在瀏覽器的 cookie jar 中。 當使用者流覽至另一個頁面時，web 應用程式會知道使用者是根據會話 cookie 進行驗證。

下列順序圖表摘要說明此互動：

![web 應用程式驗證流程](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Web 應用程式如何判斷使用者是否已通過驗證

Web 應用程式開發人員可以指出所有或只是特定頁面都需要驗證。 例如，在 ASP.NET/ASP.NET Core 中，這是藉由將`[Authorize]`屬性新增至控制器動作來完成。

這個屬性會讓 ASP.NET 檢查會話 cookie 是否存在，其中包含使用者的身分識別。 如果 cookie 不存在，ASP.NET 會將驗證重新導向至指定的識別提供者。 如果 Azure AD 識別提供者，web 應用程式會將驗證重新`https://login.microsoftonline.com`導向至，以顯示登入對話方塊。

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Web 應用程式如何將登入委派給 Microsoft 身分識別平臺並取得權杖

使用者驗證會透過瀏覽器進行。 OpenID 通訊協定會使用標準的 HTTP 通訊協定訊息。

* Web 應用程式會將 HTTP 302 （重新導向）傳送至瀏覽器，以使用 Microsoft 身分識別平臺。
* 當使用者通過驗證時，Microsoft 身分識別平臺會透過瀏覽器使用重新導向，將權杖傳送至 web 應用程式。
* 重新導向是由 web 應用程式以重新導向 URI 的形式提供。 此重新導向 URI 會向 Azure AD 應用程式物件註冊。 可能會有數個重新導向 Uri，因為應用程式可以部署在數個 Url 上。 因此，web 應用程式也需要指定要使用的重新導向 URI。
* Azure AD 確認 web 應用程式所傳送的重新導向 URI 是應用程式的其中一個已註冊的重新導向 uri。

## <a name="desktop-and-mobile-app-sign-in-flow"></a>桌面和行動應用程式登入流程

上述流程適用于桌上型電腦和行動應用程式，並稍微不同。

桌面和行動應用程式可以使用內嵌的 Web 控制項或系統瀏覽器來進行驗證。 下圖顯示桌面或行動應用程式如何使用 Microsoft 驗證程式庫（MSAL）來取得存取權杖和呼叫 web Api。

![桌面應用程式的外觀](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL 會使用瀏覽器來取得權杖。 就像 web 應用程式一樣，驗證會委派給 Microsoft 身分識別平臺。

由於 Azure AD 會在瀏覽器中儲存與 web 應用程式相同的身分識別 cookie，因此，如果原生或行動應用程式使用系統瀏覽器，它會立即取得 SSO 與對應的 web 應用程式。

根據預設，MSAL 會使用系統瀏覽器。 例外狀況是 .NET Framework 桌面應用程式，其中內嵌的控制項可用來提供更整合的使用者體驗。

## <a name="next-steps"></a>後續步驟

其他涵蓋驗證和授權基本概念的主題：

* 若要瞭解 Microsoft 身分識別平臺中驗證和授權的基本概念，請參閱[驗證與授權](authentication-vs-authorization.md)。
* 請參閱[安全性權杖](security-tokens.md)，以瞭解如何在驗證和授權中使用存取權杖、重新整理權杖和識別碼權杖。
* 請參閱[應用程式模型](application-model.md)，以瞭解註冊應用程式以與 Microsoft 身分識別平臺整合的過程。

若要深入瞭解應用程式登入流程：

* 請參閱[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)，以深入瞭解驗證 Microsoft 身分識別平臺支援的使用者的其他案例。
* 請參閱[MSAL 程式庫](msal-overview.md)，以瞭解可協助您開發使用 microsoft 帳戶的應用程式、Azure AD 帳戶，以及 Azure AD B2C 使用者全都在單一、簡化的程式設計模型中的 microsoft 程式庫。
