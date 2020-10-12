---
title: Microsoft 身分識別平台上的 OAuth 2.0 和 OpenID Connect 通訊協定 | Azure
titleSuffix: Microsoft identity platform
description: OAuth 2.0 和 OpenID Connect 通訊協定的指南，這些通訊協定受到 Microsoft 身分識別平台端點支援。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 12edbcda7354d9d6d4b03ebe32304d988b2eb579
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88751452"
---
# <a name="oauth-20-and-openid-connect-protocols-on-microsoft-identity-platform"></a>Microsoft 身分識別平台上的 OAuth 2.0 和 OpenID Connect 通訊協定

適用于身分識別即服務的 Microsoft 身分識別平臺端點會以業界標準通訊協定分別執行驗證和授權 OpenID Connect (OIDC) 和 OAuth 2.0。 雖然這是符合標準的服務，但這些通訊協定在任兩個實作之間仍會有些微差異。 若您想要透過直接傳送和處理 HTTP 要求，或使用第三方開放原始碼程式庫來撰寫程式碼，而非使用我們的其中一個[開放原始碼程式庫](reference-v2-libraries.md)，可以參考這裡提供的實用資訊。

## <a name="the-basics"></a>基本概念

幾乎在所有的 OAuth 2.0 和 OpenID Connect 流程中，都有四個參與交換的合作對象：

![顯示 OAuth 2.0 角色的圖表](./media/active-directory-v2-flows/protocols-roles.svg)

* **授權伺服器**是 Microsoft 身分識別平台端點，負責確保使用者的身分識別、授與及撤銷資源存取權，以及核發權杖。 授權伺服器也稱為身分識別提供者-它會安全地處理與使用者資訊、使用者的存取權，以及流程中合作物件之間的信任關係相關的任何動作。
* **資源擁有者**通常是使用者。 這是擁有資料的合作物件，而且具有允許用戶端存取該資料或資源的能力。
* **OAuth 用戶端**是您的應用程式，透過其應用程式識別碼加以識別。 OAuth 用戶端通常是與使用者互動的對象，而且會向授權伺服器要求權杖。 用戶端必須獲得資源擁有者授權才能存取資源。
* **資源伺服器** 是資源或資料所在位置。 其信任授權伺服器會安全地驗證及授權 OAuth 用戶端，並使用 Bearer access_token 來確保可以授與資源的存取權。

## <a name="app-registration"></a>應用程式註冊

每個想要同時接受個人和公司或學校帳戶的應用程式都必須透過 [Azure 入口網站](https://aka.ms/appregistrations)中的**應用程式註冊**體驗進行註冊，然後其才能使用 OAuth 2.0 或 OpenID Connect 簽署這些使用者。 應用程式註冊處理序會收集與指派一些值給您的應用程式：

* 可唯一識別應用程式的「應用程式識別碼」
* 可用來將回應導回至應用程式的 **重新導向 URI** (選擇性)
* 其他幾個狀況特定的值。

如需詳細資訊，請瞭解如何 [註冊應用程式](quickstart-register-app.md)。

## <a name="endpoints"></a>端點

註冊完成後，應用程式即會向 v2.0 端點傳送要求以與 Microsoft 身分識別平台通訊：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

其中 `{tenant}` 可以接受下列這四個不同值的其中一個：

| 值 | 描述 |
| --- | --- |
| `common` | 允許使用者使用個人的 Microsoft 帳戶和工作/學校帳戶，從 Azure AD 登入應用程式。 |
| `organizations` | 僅允許使用者使用工作/學校帳戶，從 Azure AD 登入應用程式。 |
| `consumers` | 僅允許使用者使用個人的 Microsoft 帳戶 (MSA) 登入應用程式。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` | 僅允許使用者使用工作/學校帳戶，從特定的 Azure AD 租用戶登入應用程式。 可以使用 Azure AD 租用戶的易記網域名稱，或是租用戶的 GUID 識別碼。 |

若要了解如何與這些端點互動，請在[通訊協定](#protocols)區段選擇特定的應用程式類型，然後遵循連結以取得更多資訊。

> [!TIP]
> 在 Azure AD 中註冊的任何應用程式都可以使用 Microsoft 身分識別平台端點，即使它們未登入個人帳戶也一樣。  如此一來，您就可以將現有應用程式遷移至 Microsoft 身分識別平台和 [MSAL](reference-v2-libraries.md)，而不需要重新建立您的應用程式。

## <a name="tokens"></a>權杖

OAuth 2.0 和 OpenID Connect 廣泛地使用 **持有人權杖**，通常是以 [Jwt (JSON Web 權杖) ](https://tools.ietf.org/html/rfc7519)來表示。 持有人權杖是輕巧型的安全性權杖，授權「持有者」存取受保護的資源。 在這種情況下，「持有人」是取得權杖複本的任何人。 雖然某一方必須先向 Microsoft 身分識別平台驗證以收到持有人權杖，但如果傳輸和儲存時未採取必要的步驟來保護權杖，它可能會被非預期的一方攔截和使用。 雖然某些安全性權杖都有內建的機制，可防止未經授權的人士使用權杖，但持有者權杖沒有這項機制，而必須以安全通道來傳輸，例如傳輸層安全性 (HTTPS)。 如果持有人權杖以未加密狀態傳輸，惡意人士就可以使用中間人攻擊來取得該權杖，並在未獲得授權的情況下用它存取受保護的資源。 儲存或快取持有者權杖供以後使用時，也適用相同的安全性原則。 務必確定您的應用程式以安全的方式傳輸和儲存持有人權杖。 關於持有者權杖的其他安全性考量，請參閱 [RFC 6750 第 5 節](https://tools.ietf.org/html/rfc6750)。

OAuth 2.0/OIDC 中使用的權杖有三種類型：

* [存取權杖](access-tokens.md) -資源伺服器從用戶端接收的權杖，包含已授與用戶端的許可權。  
* [識別碼權杖](id-tokens.md) -用戶端從授權伺服器接收的權杖，用來登入使用者並取得相關的基本資訊。
* 重新整理權杖-用戶端用來取得一段時間的新存取和識別碼權杖。  這些是不透明的字串，且只能由授權伺服器來理解。

## <a name="protocols"></a>通訊協定

如果您已經準備好查看一些範例要求，請從下列其中一個通訊協定檔開始著手。 每個教學課程皆對應至特定的驗證案例。 如果在判斷流程是否符合您時需要協助，請參閱[您可以使用 Microsoft 身分識別平台建置的應用程式類型](v2-app-types.md)。

* [使用 OAuth 2.0 建立行動裝置、原生和 web 應用程式](v2-oauth2-auth-code-flow.md)
* [使用 OpenID Connect 登入使用者](v2-protocols-oidc.md)
* [使用 OAuth 2.0 用戶端認證流程建置精靈或伺服器端處理程序](v2-oauth2-client-creds-grant-flow.md)
* [透過 OAuth 2.0 代理者流程在 Web API 中取得權杖](v2-oauth2-on-behalf-of-flow.md)
* [使用 OAuth 2.0 隱含流程建立單一頁面應用程式](v2-oauth2-implicit-grant-flow.md)
