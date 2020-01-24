---
title: 使用 OAuth 2.0 代理者流程進行服務對服務驗證 |Microsoft Docs
description: 本文說明如何使用 HTTP 訊息，以搭配 OAuth2.0 代理者流程實作服務對服務驗證。
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9c02611ea6f083a6ce8f95844e52429fc1152f90
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701003"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>使用代理者流程中委派使用者身分識別的服務對服務呼叫

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth 2.0 代理者 (OBO) 流程能讓叫用服務或 Web API 的應用程式將使用者驗證傳遞至另一個服務或 Web API。 OBO 流程會透過要求鏈傳播委派的使用者身分識別和權限。 中介層服務若要向下游服務提出已驗證的要求，便必須代表使用者從 Azure Active Directory (Azure AD) 取得存取權杖。

> [!IMPORTANT]
> 從 2018 年 5 月起，`id_token` 已無法用於代理者流程。  單頁應用程式 (SPA) 必須將存取權杖傳遞至中介層機密用戶端，才能執行 OBO 流程。 如需有哪些用戶端可以執行代理者呼叫的詳細資料，請參閱[限制](#client-limitations)。

## <a name="on-behalf-of-flow-diagram"></a>代理者流程圖

OBO 流程會在使用者已在使用 [OAuth 2.0 授權碼授與流程](v1-protocols-oauth-code.md)的應用程式上取得驗證之後開始。 此時，應用程式會將存取權杖 (權杖 A) 傳送至包含使用者宣告及同意的中介層 Web API (API A) 以存取 API A。接下來，API A 會向下游 Web API (API B) 發出已驗證的要求。

這些步驟會構成代理者流程： ![顯示 OAuth 2.0 代理者流程中的步驟](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. 用戶端應用程式使用權杖 A 向 API A 提出要求。
1. API A會向 Azure AD 權杖發行端點進行驗證，並要求存取 API B 的權杖。
1. Azure AD 權杖發行端點以權杖 A 驗證 API A 的認證，並發出 API B 的存取權杖 (權杖 B)。
1. 對 API B 的要求，會在授權標頭中包含權杖 B。
1. API B 傳回來自受保護資源的資料。

>[!NOTE]
>用來針對下游服務要求權杖之存取權杖中的對象宣告，必須是發出 OBO 要求之服務的識別碼。 該權杖同時也必須使用 Azure Active Directory 全域簽署金鑰進行簽署 (這是透過入口網站中的 [應用程式註冊] 註冊之應用程式的預設值)。

## <a name="register-the-application-and-service-in-azure-ad"></a>在 Azure AD 中註冊應用程式和服務

在 Azure AD 中同時註冊中介層服務和用戶端應用程式。

### <a name="register-the-middle-tier-service"></a>註冊中介層服務

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端列上選取您的帳戶，然後在 [目錄] 清單底下，選取適用於您應用程式的 Active Directory 租用戶。
1. 選取左側窗格中的 [更多服務]，然後選擇 [Azure Active Directory]。
1. 依序選取 [**應用程式註冊**] 和 [**新增註冊**]。
1. 為應用程式輸入易記名稱，然後選取應用程式類型。
1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
1. 將 [重新導向 URI] 設定為基底 URL。
1. 選取 [註冊] 以建立應用程式。
1. 在結束 Azure 入口網站之前產生用戶端祕密。
1. 在 Azure 入口網站中，選擇您的應用程式，然後選取 **憑證 & 秘密**。
1. 選取 [**新增用戶端密碼**]，並新增一年或兩年期間的秘密。
1. 當您儲存此頁面時，Azure 入口網站會顯示秘密值。 將秘密值複製並儲存在安全的位置。

> [!IMPORTANT]
> 您需要密碼，才能在您的執行中設定應用程式設定。 這個秘密值不會再次顯示，也無法透過任何其他方式來加以抓取。 在它顯示於 Azure 入口網站中時，請儘快將它記錄下來。

### <a name="register-the-client-application"></a>註冊用戶端應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端列上選取您的帳戶，然後在 [目錄] 清單底下，選取適用於您應用程式的 Active Directory 租用戶。
1. 選取左側窗格中的 [更多服務]，然後選擇 [Azure Active Directory]。
1. 依序選取 [**應用程式註冊**] 和 [**新增註冊**]。
1. 為應用程式輸入易記名稱，然後選取應用程式類型。
1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
1. 將 [重新導向 URI] 設定為基底 URL。
1. 選取 [註冊] 以建立應用程式。
1. 設定應用程式的權限。 在 [ **API 許可權**] 中，選取 [**新增許可權**] 和 [**我的 api**]。
1. 在文字欄位中輸入中介層服務的名稱。
1. 選擇 **選取許可權**，然後選取 **存取 \<服務名稱 >** 。

### <a name="configure-known-client-applications"></a>設定已知的用戶端應用程式

在此案例中，中介層服務需要取得使用者同意，以在沒有使用者互動的情況下存取下游 API。 授與權限以存取下游 API 的選項，必須在驗證期間作為同意步驟的一部分清楚顯示。

請遵循下列步驟以明確地將 Azure AD 中用戶端應用程式的註冊繫結至中介層服務的註冊。 此作業會將用戶端與中介層所需的同意合併成單一的對話方塊。

1. 移至中介層服務註冊，並選取 [資訊清單] 以開啟資訊清單編輯器。
1. 找到 `knownClientApplications` 陣列屬性，並將用戶端應用程式的用戶端識別碼新增為元素。
1. 選取 [儲存] 來儲存資訊清單。

## <a name="service-to-service-access-token-request"></a>服務對服務存取權杖要求

若要要求存取權杖，請對租用戶特定的 Azure AD 端點搭配下列參數提出 HTTP POST：

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

用戶端應用程式會受到共用祕密或憑證的保護。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一種情況︰使用共用密碼的存取權杖要求

使用共用密碼時，服務對服務存取權杖要求包含下列參數：

| 參數 |  | 說明 |
| --- | --- | --- |
| grant_type |required | 權杖要求的類型。 OBO 要求會使用 JSON Web 權杖 (JWT)，所以值必須是 **urn:ietf:params:oauth:grant-type:jwt-bearer**。 |
| assertion |required | 要求中使用的存取權杖值。 |
| client_id |required | 在向 Azure AD 註冊期間指派給呼叫服務的應用程式識別碼。 若要在 Azure 入口網站中尋找應用程式識別碼，請選取 [Active Directory]，選擇目錄，然後選取應用程式名稱。 |
| client_secret |required | 在 Azure AD 中註冊之呼叫端服務的金鑰。 此值應該在註冊期間記下來。 |
| resource |required | 接收端服務 (受保護的資源) 的應用程式識別碼 URI。 若要在 Azure 入口網站中尋找應用程式識別碼 URI，請選取 [Active Directory]，然後選擇目錄。 選取應用程式名稱，選擇 [所有設定]，然後選取 [屬性]。 |
| requested_token_use |required | 指定應該如何處理要求。 在代理者流程中，此值必須是 **on_behalf_of**。 |
| scope |required | 權杖要求範圍的清單，各項目之間以空格分隔。 若為 OpenID connect，則必須指定 **openid** 範圍。|

#### <a name="example"></a>範例

下列 HTTP POST 會要求提供 https://graph.windows.net Web API 的存取權杖。 `client_id` 會識別要求存取權杖的服務。

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>第二種情況︰使用憑證的存取權杖要求

使用憑證的服務對服務存取權杖要求包含下列參數：

| 參數 |  | 說明 |
| --- | --- | --- |
| grant_type |required | 權杖要求的類型。 OBO 要求會使用 JWT 存取權杖，所以值必須是 **urn:ietf:params:oauth:grant-type:jwt-bearer**。 |
| assertion |required | 要求中使用的權杖值。 |
| client_id |required | 在向 Azure AD 註冊期間指派給呼叫服務的應用程式識別碼。 若要在 Azure 入口網站中尋找應用程式識別碼，請選取 [Active Directory]，選擇目錄，然後選取應用程式名稱。 |
| client_assertion_type |required |值必須是 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |required | 您所建立並使用註冊為您應用程式之認證的憑證簽署的 JSON Web 權杖。 請參閱[憑證認證](active-directory-certificate-credentials.md)以了解判斷提示格式，以及如何註冊您的憑證。|
| resource |required | 接收端服務 (受保護的資源) 的應用程式識別碼 URI。 若要在 Azure 入口網站中尋找應用程式識別碼 URI，請選取 [Active Directory]，然後選擇目錄。 選取應用程式名稱，選擇 [所有設定]，然後選取 [屬性]。 |
| requested_token_use |required | 指定應該如何處理要求。 在代理者流程中，此值必須是 **on_behalf_of**。 |
| scope |required | 權杖要求範圍的清單，各項目之間以空格分隔。 若為 OpenID connect，則必須指定 **openid** 範圍。|

這些參數與共用祕密的要求幾乎相同，唯一的不同之處在於 `client_secret parameter` 會被下列兩個參數取代：`client_assertion_type` 和 `client_assertion`。

#### <a name="example"></a>範例

下列 HTTP POST 會使用憑證要求提供 https://graph.windows.net Web API 的存取權杖。 `client_id` 會識別要求存取權杖的服務。

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>服務對服務存取權杖回應

成功的回應是包含下列參數的 JSON OAuth 2.0 回應：

| 參數 | 說明 |
| --- | --- |
| token_type |表示權杖類型值。 Azure AD 唯一支援的類型是 [持有人]。 如需持有人權杖的詳細資訊，請參閱 [OAuth 2.0 授權架構︰持有人權杖用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| scope |在權杖中授與的存取範圍。 |
| expires_in |存取權杖的有效時間長度 (以秒為單位)。 |
| expires_on |存取權杖的到期時間。 日期會表示為從 1970-01-01T0:0:0Z UTC 至到期時間的秒數。 這個值用來判斷快取權杖的存留期。 |
| resource |接收端服務 (受保護的資源) 的應用程式識別碼 URI。 |
| access_token |所要求的存取權杖。 呼叫端服務可以使用此權杖來向接收端服務進行驗證。 |
| id_token |所要求的識別碼權杖。 呼叫端服務可以使用此權杖來確認使用者的身分識別，然後開始與使用者的工作階段。 |
| refresh_token |所要求之存取權杖的重新整理權杖。 呼叫端服務可以使用這個權杖，在目前的存取權杖過期之後，要求其他的存取權杖。 |

### <a name="success-response-example"></a>成功回應範例

下列範例顯示 https://graph.windows.net Web API 存取權杖要求的成功回應。

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>錯誤回應範例

當 Azure AD token 端點嘗試取得使用條件式存取原則（例如多重要素驗證）所設定之下游 API 的存取權杖時，會傳回錯誤回應。 仲介層服務應將此錯誤呈現給用戶端應用程式，讓用戶端應用程式可以提供使用者互動，以滿足條件式存取原則。

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>使用存取權杖來存取受保護資源

中介層服務可以使用取得的存取權杖，透過在 `Authorization` 標頭中設定權杖，來向下游 Web API 提出已驗證的要求。

### <a name="example"></a>範例

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>搭配 OAuth2.0 OBO 流程取得的 SAML 判斷提示

某些 OAuth 型 Web 服務需要存取會在非互動流程中接受 SAML 判斷提示的其他 Web 服務 API。 Azure Active Directory 可以提供 SAML 判斷提示，以回應使用 SAML 型 SAML Web 服務作為目標資源的代理者流程。

>[!NOTE]
>這是針對 OAuth 2.0 代理者流程的非標準擴充，能允許 OAuth2 型應用程式存取會耗用 SAML 權杖的 Web 服務 API 端點。

> [!TIP]
> 當您從前端 Web 應用程式呼叫受 SAML 保護的 Web 服務時，只需呼叫 API 並搭配使用者現有的工作階段起始一般互動驗證流程。 當服務對服務呼叫需要 SAML 權杖以提供使用者內容時，您只需要使用 OBO 流程。

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>使用具有共用祕密的 OBO 要求來取得 SAML 權杖

適用於 SAML 判斷提示的服務對服務要求包含下列參數：

| 參數 |  | 說明 |
| --- | --- | --- |
| grant_type |required | 權杖要求的類型。 針對使用 JWT 的要求，此值必須是 **urn:ietf:params:oauth:grant-type:jwt-bearer**。 |
| assertion |required | 要求中使用的存取權杖值。|
| client_id |required | 在向 Azure AD 註冊期間指派給呼叫服務的應用程式識別碼。 若要在 Azure 入口網站中尋找應用程式識別碼，請選取 [Active Directory]，選擇目錄，然後選取應用程式名稱。 |
| client_secret |required | 在 Azure AD 中註冊之呼叫端服務的金鑰。 此值應該在註冊期間記下來。 |
| resource |required | 接收端服務 (受保護的資源) 的應用程式識別碼 URI。 這是 SAML 權杖對象的資源。 若要在 Azure 入口網站中尋找應用程式識別碼 URI，請選取 [Active Directory]，然後選擇目錄。 選取應用程式名稱，選擇 [所有設定]，然後選取 [屬性]。 |
| requested_token_use |required | 指定應該如何處理要求。 在代理者流程中，此值必須是 **on_behalf_of**。 |
| requested_token_type | required | 指定要求權杖的類型。 值可以是 **urn:ietf:params:oauth:token-type:saml2** 或 **urn:ietf:params:oauth:token-type:saml1**，取決於所存取之資源的需求。 |

回應會包含以 UTF8 及 Base64url 編碼的 SAML 權杖。

- **源自 OBO 呼叫之 SAML 判斷提示的 SubjectConfirmationData**：如果目標應用程式需要 **SubjectConfirmationData** 中的收件者值，該值必須是資源應用程式設定中的非萬用字元回覆 URL。
- **SubjectConfirmationData 節點**：此節點不能包含 **InResponseTo** 屬性，因為它不屬於 SAML 回應。 接收 SAML 權杖的應用程式必須能夠在沒有 **InResponseTo** 屬性的情況下接受 SAML 判斷提示。

- **同意**：必須被授與同意，才能在 OAuth 流程上接收包含使用者資料的 SAML 權杖。 如需權限和取得系統管理員同意的相關資訊，請參閱 [Azure Active Directory v1.0 端點中的權限和同意](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent)。

### <a name="response-with-saml-assertion"></a>使用 SAML 判斷提示進行回應

| 參數 | 說明 |
| --- | --- |
| token_type |表示權杖類型值。 Azure AD 唯一支援的類型是 [持有人]。 如需持有人權杖的詳細資訊，請參閱 [OAuth 2.0 授權架構︰持有人權杖用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| scope |在權杖中授與的存取範圍。 |
| expires_in |存取權杖的有效時間長度 (以秒為單位)。 |
| expires_on |存取權杖的到期時間。 日期會表示為從 1970-01-01T0:0:0Z UTC 至到期時間的秒數。 這個值用來判斷快取權杖的存留期。 |
| resource |接收端服務 (受保護的資源) 的應用程式識別碼 URI。 |
| access_token |傳回 SAML 判斷提示的參數。 |
| refresh_token |重新整理權杖。 呼叫端服務可以使用這個權杖，在目前的 SAML 判斷提示過期之後，要求其他的存取權杖。 |

- token_type：Bearer
- expires_in：3296
- ext_expires_in：0
- expires_on：1529627844
- resource：`https://api.contoso.com`
- access_token：\<SAML 判斷提示\>
- issued_token_type：urn:ietf:params:oauth:token-type:saml2
- refresh_token：\<重新整理權杖\>

## <a name="client-limitations"></a>用戶端限制

含有萬用字元回覆 URL 的公用用戶端無法針對 OBO 流程使用 `id_token`。 不過，機密用戶端仍可兌換透過隱含授與流程取得的**存取**權杖，即使公用用戶端已註冊萬用字元重新導向 URL 亦然。

## <a name="next-steps"></a>後續步驟

深入了解 OAuth 2.0 通訊協定，以及另一種使用用戶端認證來執行服務對服務驗證的方式：

* [使用 Azure AD 中授與的 OAuth 2.0 用戶端認證來進行服務對服務驗證](v1-oauth2-client-creds-grant-flow.md)
* [Azure AD 中的 OAuth 2.0](v1-protocols-oauth-code.md)
