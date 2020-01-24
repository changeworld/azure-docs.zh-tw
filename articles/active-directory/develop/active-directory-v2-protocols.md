---
title: OAuth 2.0 和 OpenID Connect 通訊協定-Microsoft 身分識別平臺 |Azure
description: Microsoft 身分識別平臺端點所支援的 OAuth 2.0 和 OpenID Connect 通訊協定指南。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: d7ed4dbbc3fddb2e21ed3cf5292ebd80fe1e3e23
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698521"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Microsoft 身分識別平臺上的 OAuth 2.0 和 OpenID Connect 通訊協定

適用于身分識別即服務的 Microsoft 身分識別平臺端點，具有業界標準通訊協定、OpenID Connect 和 OAuth 2.0。 雖然這是符合標準的服務，但這些通訊協定在任兩個實作之間仍會有些微差異。 若您想要透過直接傳送和處理 HTTP 要求，或使用第三方開放原始碼程式庫來撰寫程式碼，而非使用我們的其中一個[開放原始碼程式庫](reference-v2-libraries.md)，可以參考這裡提供的實用資訊。

> [!NOTE]
> Microsoft 身分識別平臺端點並非支援所有的 Azure AD 案例和功能。 若要判斷您是否應該使用 Microsoft 身分識別平臺端點，請參閱[microsoft 身分識別平臺限制](active-directory-v2-limitations.md)。

## <a name="the-basics"></a>基本概念

幾乎在所有的 OAuth 2.0 和 OpenID Connect 流程中，都有四個參與交換的合作對象：

![顯示 OAuth 2.0 角色的圖表](./media/active-directory-v2-flows/protocols-roles.svg)

* **授權伺服器**是 Microsoft 身分識別平臺端點，負責確保使用者的身分識別、授與及撤銷資源的存取權，以及發行權杖。 授權伺服器也稱為識別提供者：安全地處理與使用者資訊、使用者存取權，以及流程中合作對象彼此間信任關係有關的任何項目。
* **資源擁有者**通常是使用者。 其是擁有資料的一方，而且有權允許第三方存取該資料或資源。
* **OAuth 用戶端**是您的應用程式，透過其應用程式識別碼加以識別。 OAuth 用戶端通常是與使用者互動的對象，而且會向授權伺服器要求權杖。 用戶端必須獲得資源擁有者授權才能存取資源。
* **資源伺服器** 是資源或資料所在位置。 它會信任授權伺服器，以安全地驗證和授權 OAuth 用戶端，並使用持有人存取權杖來確保能夠授與資源的存取權。

## <a name="app-registration"></a>應用程式註冊

每個想要接受個人和公司或學校帳戶的應用程式都必須透過[Azure 入口網站](https://aka.ms/appregistrations)中的**應用程式註冊**體驗來註冊，才能使用 OAuth 2.0 或 OpenID connect 將這些使用者簽入。 應用程式註冊處理序會收集與指派一些值給您的應用程式：

* 可唯一識別應用程式的「應用程式識別碼」
* 可用於將回應導向回到應用程式的重新**導向 URI** （選擇性）
* 其他幾個狀況特定的值。

如需詳細資訊，請瞭解如何 [註冊應用程式](quickstart-register-app.md)。

## <a name="endpoints"></a>端點

註冊之後，應用程式會將要求傳送至端點，以與 Microsoft 身分識別平臺進行通訊：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

其中 `{tenant}` 可以接受下列這四個不同值的其中一個：

| 值 | 說明 |
| --- | --- |
| `common` | 允許使用者使用個人的 Microsoft 帳戶和工作/學校帳戶，從 Azure AD 登入應用程式。 |
| `organizations` | 僅允許使用者使用工作/學校帳戶，從 Azure AD 登入應用程式。 |
| `consumers` | 僅允許使用者使用個人的 Microsoft 帳戶 (MSA) 登入應用程式。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` | 僅允許使用者使用工作/學校帳戶，從特定的 Azure AD 租用戶登入應用程式。 可以使用 Azure AD 租用戶的易記網域名稱，或是租用戶的 GUID 識別碼。 |

若要了解如何與這些端點互動，請在[通訊協定](#protocols)區段選擇特定的應用程式類型，然後遵循連結以取得更多資訊。

> [!TIP]
> 在 Azure AD 中註冊的任何應用程式都可以使用 Microsoft 身分識別平臺端點，即使他們未登入個人帳戶也一樣。  如此一來，您就可以將現有的應用程式遷移至 Microsoft 身分識別平臺和[MSAL](reference-v2-libraries.md) ，而不需要重新建立您的應用程式。  

## <a name="tokens"></a>權杖

OAuth 2.0 和 OpenID Connect 的 Microsoft 身分識別平臺執行會大量使用持有人權杖，包括以 Jwt 表示的持有人權杖。 持有人權杖是輕巧型的安全性權杖，授權「持有者」存取受保護的資源。 從這個意義上說，「持有者」是可出示權杖的任何一方。 雖然某一方必須先向 Microsoft 身分識別平臺驗證，才能接收持有人權杖，但如果未採取必要的步驟來保護傳輸和儲存中的權杖，則可能會被非預期的一方攔截和使用。 雖然某些安全性權杖都有內建的機制，可防止未經授權的人士使用權杖，但持有者權杖沒有這項機制，而必須以安全通道來傳輸，例如傳輸層安全性 (HTTPS)。 如果持有人權杖以未加密狀態傳輸，惡意人士就可以使用中間人攻擊來取得該權杖，並在未獲得授權的情況下用它存取受保護的資源。 儲存或快取持有者權杖供以後使用時，也適用相同的安全性原則。 務必確定您的應用程式以安全的方式傳輸和儲存持有人權杖。 關於持有者權杖的其他安全性考量，請參閱 [RFC 6750 第 5 節](https://tools.ietf.org/html/rfc6750)。

如需 Microsoft 身分識別平臺端點中所用不同權杖類型的詳細資訊，請[參閱 microsoft 身分識別平臺端點權杖參考](v2-id-and-access-tokens.md)。

## <a name="protocols"></a>通訊協定

若您準備好查看部分範例要求，請開始使用以下的其中一個教學課程。 每個教學課程皆對應至特定的驗證案例。 如果您需要協助來判斷哪一個是正確的流程，請參閱[您可以使用 Microsoft 身分識別平臺來建立的應用程式類型](v2-app-types.md)。

* [使用 OAuth 2.0 建置行動與原生應用程式](v2-oauth2-auth-code-flow.md)
* [使用 OpenID Connect 建置 Web 應用程式](v2-protocols-oidc.md)
* [使用 OAuth 2.0 隱含流程建置單一頁面應用程式](v2-oauth2-implicit-grant-flow.md)
* [使用 OAuth 2.0 用戶端認證流程建置精靈或伺服器端處理程序](v2-oauth2-client-creds-grant-flow.md)
* [透過 OAuth 2.0 代理者流程在 Web API 中取得權杖](v2-oauth2-on-behalf-of-flow.md)
