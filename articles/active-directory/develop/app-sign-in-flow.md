---
title: 使用 Microsoft 身分識別平臺的應用程式登入流程 |蔚藍
titleSuffix: Microsoft identity platform
description: 深入瞭解 Microsoft 身分識別平臺中的 web、桌面和行動應用程式的登入流程。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1f9f330ab140fa66b5a66a112c47ca2a68ba56bf
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755704"
---
# <a name="app-sign-in-flow-with-the-microsoft-identity-platform"></a>使用 Microsoft 身分識別平臺的應用程式登入流程

本主題討論使用 Microsoft 身分識別平台的 Web、傳統型和行動應用程式的基本登入流程。 若要了解 Microsoft 身分識別平台支援的登入案例，請參閱[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)。

## <a name="web-app-sign-in-flow"></a>Web 應用程式登入流程

當使用者在瀏覽器中瀏覽至 Web 應用程式時，將會發生下列情況：

* Web 應用程式會判斷使用者是否經過驗證。
* 如果使用者未經過驗證，Web 應用程式會委派給 Azure AD 以登入使用者。 該登入將與組織的原則相容，這可能表示要求使用者輸入其認證、使用[多重要素驗證](../authentication/concept-mfa-howitworks.md) (有時稱為「雙因素驗證」或 2FA)，或完全不使用密碼 (例如使用 Windows Hello)。
* 系統會要求使用者同意用戶端應用程式所需的存取權。 這就是為什麼需要向 Azure AD 註冊用戶端應用程式的原因，讓 Microsoft 身分識別平臺可以提供權杖，代表使用者已同意的存取權。

成功驗證使用者之後：

* Microsoft 身分識別平臺會將權杖傳送至 web 應用程式。
* 系統會儲存與 Azure AD 網域相關聯的 Cookie，其中包含瀏覽器 Cookie Jar 中使用者的身分識別。 當應用程式下次使用瀏覽器流覽至 Microsoft 身分識別平臺授權端點時，瀏覽器會顯示 cookie，讓使用者不必再次登入。 這也是達成 SSO 的方式。 Cookie 是由 Azure AD 所產生，而且只能由 Azure AD 了解。
* 然後 Web 應用程式會驗證權杖。 如果驗證成功，Web 應用程式會顯示受保護的頁面，並將工作階段 Cookie 儲存在瀏覽器的 Cookie Jar 中。 當使用者瀏覽至另一個頁面時，Web 應用程式會知道使用者是根據工作階段 Cookie 進行驗證。

以下序列圖表摘要說明此互動：

![Web 應用程式驗證程序](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Web 應用程式如何判斷使用者已經過驗證

Web 應用程式開發人員可以指出所有或只有特定頁面需要驗證。 例如，在 ASP.NET/ASP.NET Core 中，這是藉由將 `[Authorize]` 屬性新增至控制器動作來完成。

這個屬性會讓 ASP.NET 檢查其中包含使用者身分識別的工作階段 Cookie 是否存在。 如果 Cookie 不存在，ASP.NET 會將驗證重新導向至指定的識別提供者。 如果識別提供者是 Azure AD，則 Web 應用程式會將驗證重新導向至 `https://login.microsoftonline.com`，這會顯示登入對話方塊。

### <a name="how-a-web-app-delegates-sign-in-to-the-microsoft-identity-platform-and-obtains-a-token"></a>Web 應用程式如何將登入委派給 Microsoft 身分識別平臺並取得權杖

使用者驗證會透過瀏覽器進行。 OpenID 通訊協定會使用標準的 HTTP 通訊協定訊息。

* Web 應用程式會將 HTTP 302 (重新導向) 傳送至瀏覽器，以使用 Microsoft 身分識別平台。
* 當使用者經過驗證後，Microsoft 身分識別平臺會透過瀏覽器使用重新導向，將權杖傳送至 web 應用程式。
* 重新導向是由 Web 應用程式以重新導向 URI 的形式提供。 此重新導向 URI 會向 Azure AD 應用程式物件註冊。 可能會有數個重新導向 URI，因為應用程式可以部署在數個 URL 上。 因此，Web 應用程式也需要指定要使用的重新導向 URI。
* Azure AD 會確認 Web 應用程式所傳送的重新導向 URI，是應用程式的其中一個已註冊重新導向 URI。

## <a name="desktop-and-mobile-app-sign-in-flow"></a>傳統型和行動應用程式登入流程

上述流程適用於傳統型和行動應用程式，只有些微不同。

傳統型和行動應用程式可以使用內嵌的 Web 控制項或系統瀏覽器來進行驗證。 下圖顯示傳統型或行動應用程式如何使用 Microsoft 驗證程式庫 (MSAL) 來取得存取權杖和呼叫 Web API。

![傳統型應用程式的外觀](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL 會使用瀏覽器來取得權杖。 就像 Web 應用程式一樣，驗證會委派給 Microsoft 身分識別平台。

因為 Azure AD 會如同對 Web 應用程式所執行的動作，在瀏覽器中儲存相同的身分識別 Cookie，所以如果原生或行動應用程式使用系統瀏覽器，則會立即以對應的 Web 應用程式取得 SSO。

根據預設，MSAL 會使用系統瀏覽器。 例外狀況是 .NET Framework 傳統型應用程式，其中內嵌控制項可用來提供更整合的使用者體驗。

## <a name="next-steps"></a>後續步驟

如需涵蓋驗證和授權基本概念的其他主題：

* 若要了解 Microsoft 身分識別平台中驗證和授權的基本概念，請參閱[驗證與授權](authentication-vs-authorization.md)。
* 若要了解存取權杖、重新整理權杖和識別碼權杖如何用於驗證和授權，請參閱[安全性權杖](security-tokens.md)。
* 若要了解註冊應用程式以與 Microsoft 身分識別平台整合的程序，請參閱[應用程式模型](application-model.md)。

若要深入了解應用程式登入流程：

* 若要了解 Microsoft 身分識別平台支援的其他驗證使用者案例，請參閱[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)。
* 若要了解可協助您開發應用程式的 Microsoft 程式庫，其全都在單一、簡化的程式設計模型中使用 Microsoft 帳戶、Azure AD 帳戶和 Azure AD B2C 使用者，請參閱 [MSAL 程式庫](msal-overview.md)。
