---
title: Microsoft 身分識別平臺和 OAuth 2.0 代理者流程 |Azure
description: 本文說明如何使用 HTTP 訊息，以利用 OAuth2.0 代理者流程實作服務對服務驗證。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 57497c7bd8cd1d0b46c40b6977079f4a6a2d876f
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82689559"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft 身分識別平臺和 OAuth 2.0 代理者流程


OAuth 2.0 代理者流程 (OBO) 可處理應用程式叫用服務/Web API，而該 API 又需要呼叫另一個服務/Web API 的使用案例。 其概念是透過要求鏈傳播委派的使用者身分識別和權限。 若要讓仲介層服務向下游服務提出已驗證的要求，它需要代表使用者保護來自 Microsoft 身分識別平臺的存取權杖。

本文說明如何在您的應用程式中直接針對通訊協定進行程式設計。  可能的話，建議您改用支援的 Microsoft 驗證程式庫（MSAL）來[取得權杖，並呼叫受保護的 Web api](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另請參閱[使用 MSAL 的範例應用程式](sample-v2-code.md)。

> [!NOTE]
> 從 2018 年 5 月起，部分隱含流程衍生 `id_token` 無法用於 OBO 流程。 單頁應用程式 (SPA) 應該將**存取**權杖傳遞至中介層機密用戶端，才能改為執行 OBO 流程。 若要進一步了解哪些用戶端可執行 OBO 呼叫，請參閱[限制](#client-limitations)。

## <a name="protocol-diagram"></a>通訊協定圖表

假設使用者已在使用[OAuth 2.0 授權碼授與流程](v2-oauth2-auth-code-flow.md)或其他登入流程的應用程式上進行驗證。 此時，應用程式有一個*適用于 API A*的存取權杖（權杖 a），其中包含使用者的宣告，並同意存取仲介層 WEB API （API A）。 現在，API A 必須向下游 Web API (API B) 提出已驗證的要求。

接下來的步驟會構成 OBO 流程，我們會透過下圖的協助加以說明。

![顯示 OAuth 2.0 代理者流程](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. 用戶端應用程式使用權杖 A (含 API A 的 `aud` 宣告) 向 API A 提出要求。
1. API A 會向 Microsoft 身分識別平臺權杖發行端點進行驗證，並要求權杖以存取 API B。
1. Microsoft 身分識別平臺權杖發行端點會驗證 API A 的認證以及權杖 A，並將 API B （權杖 B）的存取權杖發行至 API A。
1. 權杖 B 是由 api B 要求的授權標頭中的 API A 所設定。
1. 來自受保護資源的資料會由 API B 傳回至 API A，然後從該處傳回至用戶端。

> [!NOTE]
> 在此案例中，中介層服務不會利用使用者互動來取得使用者的下游 API 存取同意。 因此，在驗證期間必須先呈現授與存取下游 API 的選項，作為同意步驟的一部分。 若要深入了解如何為您的應用程式進行這個設定，請參閱[取得中介層應用程式的同意](#gaining-consent-for-the-middle-tier-application)。

## <a name="service-to-service-access-token-request"></a>服務對服務存取權杖要求

若要要求存取權杖，請使用下列參數，對租使用者特定的 Microsoft 身分識別平臺權杖端點進行 HTTP POST。

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

有兩種情況，取決於用戶端應用程式是選擇透過共用密碼或憑證來保護。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一個案例：含有共用祕密的存取權杖要求

使用共用祕密時，服務對服務存取權杖要求會包含下列參數：

| 參數 |  | 描述 |
| --- | --- | --- |
| `grant_type` | 必要 | 權杖要求的類型。 對於使用 JWT 的要求，值必須是 `urn:ietf:params:oauth:grant-type:jwt-bearer`。 |
| `client_id` | 必要 | [Azure 入口網站應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面已指派給您應用程式的應用程式（用戶端）識別碼。 |
| `client_secret` | 必要 | 您在 [Azure 入口網站應用程式註冊] 頁面中為應用程式產生的用戶端密碼。 |
| `assertion` | 必要 | 在要求中使用的權杖值。  此權杖必須具有應用程式的物件，以進行此 OBO 要求（由`client-id`欄位表示的應用程式）。 |
| `scope` | 必要 | 權杖要求範圍的空格分隔清單。 如需詳細資訊，請參閱[範圍](v2-permissions-and-consent.md)。 |
| `requested_token_use` | 必要 | 指定應該如何處理要求。 在 OBO 流程中，此值必須設定為 `on_behalf_of`。 |

#### <a name="example"></a>範例

下列 HTTP POST 會要求 https://graph.microsoft.com Web API 之 `user.read` 範圍的存取權杖與重新整理權杖。

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>第二個案例：含有憑證的存取權杖要求

含有憑證的服務對服務存取權杖要求會包含下列參數：

| 參數 |  | 描述 |
| --- | --- | --- |
| `grant_type` | 必要 | 權杖要求的類型。 對於使用 JWT 的要求，值必須是 `urn:ietf:params:oauth:grant-type:jwt-bearer`。 |
| `client_id` | 必要 |  [Azure 入口網站應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面已指派給您應用程式的應用程式（用戶端）識別碼。 |
| `client_assertion_type` | 必要 | 值必須是 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`。 |
| `client_assertion` | 必要 | 您需要建立並使用憑證 (已註冊為應用程式認證) 來簽署的判斷提示 (JSON Web 權杖)。 若要深入了解如何註冊您的憑證與判斷提示的格式，請參閱[憑證認證](active-directory-certificate-credentials.md)。 |
| `assertion` | 必要 | 在要求中使用的權杖值。 |
| `requested_token_use` | 必要 | 指定應該如何處理要求。 在 OBO 流程中，此值必須設定為 `on_behalf_of`。 |
| `scope` | 必要 | 權杖要求範圍的清單，各項目之間以空格分隔。 如需詳細資訊，請參閱[範圍](v2-permissions-and-consent.md)。|

請注意，在透過共用密碼要求的情況中，參數幾乎相同，不同之處在於使用下列兩個參數來取代 `client_secret` 參數：`client_assertion_type` 和 `client_assertion`。

#### <a name="example"></a>範例

下列 HTTP POST 會使用憑證要求 https://graph.microsoft.com Web API 之 `user.read` 範圍的存取權杖。

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>服務對服務的存取權杖回應

成功回應為包含下列參數的 JSON OAuth 2.0 回應。

| 參數 | 描述 |
| --- | --- |
| `token_type` | 指出權杖類型的值。 Microsoft 身分識別平臺唯一支援的類型是`Bearer`。 如需有關持有人權杖的詳細資訊，請參閱[OAuth 2.0 授權架構：持有人權杖使用方式（RFC 6750）](https://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| `scope` | 在權杖中授與的存取範圍。 |
| `expires_in` | 存取權杖的有效時間 (以秒為單位)。 |
| `access_token` | 要求的存取權杖。 呼叫服務可以使用此權杖來驗證接收服務。 |
| `refresh_token` | 所要求存取權杖的重新整理權杖。 目前的存取權杖到期後，呼叫服務可以使用此權杖來要求另一個存取權杖。 只有要求 `offline_access` 範圍時，才會提供重新整理權杖。 |

### <a name="success-response-example"></a>成功的回應範例

下列範例顯示 https://graph.microsoft.com Web API 存取權杖要求的成功回應。

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> 上述存取權杖是 v1.0 格式的權杖。 這是因為權杖是根據所存取的**資源**所提供。 Microsoft Graph 已設定為接受 v1.0 權杖，因此當用戶端要求 Microsoft Graph 的權杖時，Microsoft 身分識別平臺會產生 v1.0 存取權杖。 只有應用程式應該檢查存取權杖。 用戶端**不得**檢查它們。

### <a name="error-response-example"></a>錯誤回應範例

嘗試取得下游 API 的存取權杖時，如果下游 API 已設定條件式存取原則（例如多重要素驗證），則權杖端點會傳回錯誤回應。 仲介層服務應將此錯誤呈現給用戶端應用程式，讓用戶端應用程式可以提供使用者互動，以滿足條件式存取原則。

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

現在，中介層服務可以使用取得的權杖，在 `Authorization` 標頭中設定權杖，並向下游 Web API 提出已驗證的要求。

### <a name="example"></a>範例

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>取得中介層應用程式的同意

根據您應用程式的架構或使用方式而定，您可以考慮不同的策略，以確保 OBO 流程成功。 在所有情況下，最終目標是要確保給予適當的同意，讓用戶端應用程式可以呼叫仲介層應用程式，而仲介層應用程式則具有呼叫後端資源的許可權。

> [!NOTE]
> 先前 Microsoft 帳戶系統（個人帳戶）不支援 [已知用戶端應用程式] 欄位，也不會顯示合併的同意。  這已經加入，而且 Microsoft 身分識別平臺中的所有應用程式都可以使用已知的用戶端應用程式方法來同意 OBO 呼叫。

### <a name="default-and-combined-consent"></a>/.預設和合併的同意

中介層應用程式會將用戶端新增至已知用戶端應用程式清單的資訊清單中，然後用戶端可以針對自己本身與中介層應用程式觸發合併的同意流程。 在 Microsoft 身分識別平臺端點上，這是使用[ `/.default`範圍](v2-permissions-and-consent.md#the-default-scope)來完成。 當使用已知的用戶端應用程式和`/.default`來觸發同意畫面時，同意畫面會顯示**兩個**用戶端對仲介層 api 的許可權，並要求仲介層 api 所需的任何許可權。 使用者提供兩個應用程式的同意，然後 OBO 流程就能運作。

### <a name="pre-authorized-applications"></a>已預先授權應用程式

資源可以指出給定的應用程式一律具有接收特定範圍的許可權。 這主要是用來讓前端用戶端與後端資源之間的連線更順暢。 資源可以宣告多個已預先授權應用程式，任何此類應用程式可以在 OBO 流程中要求這些權限並接收它們，不需要使用者提供同意。

### <a name="admin-consent"></a>系統管理員同意

租用戶系統管理員可以藉由提供中介層應用程式的同意，保證應用程式有呼叫其所需 API 的權限。 若要這樣做，系統管理員可以在其租用戶中尋找中介層應用程式、開啟必要的權限頁面，並選擇提供應用程式的權限。 若要深入了解系統管理員同意，請參閱[同意和權限文件](v2-permissions-and-consent.md)。

### <a name="use-of-a-single-application"></a>使用單一應用程式

在某些案例中，您可能只有中介層與前端用戶端的單一配對。 在此案例中，您可能會發現使用單一應用程式更輕鬆，不管中介層應用程式的需求。 若要在前端與 Web API 之間進行驗證，您可以使用 Cookie、id_token 或針對應用程式本身要求的存取權杖。 然後，要求從這個單一應用程式到後端資源的同意。

## <a name="client-limitations"></a>用戶端限制

如果用戶端使用隱含流程來取得 id_token，且該用戶端在回復 URL 中也有萬用字元，則該 id_token 無法用於 OBO 流程。  不過，機密用戶端仍可兌換透過隱含授與流程取得的存取權杖，即使起始用戶端已註冊萬用字元回覆 URL 亦然。

## <a name="next-steps"></a>後續步驟

進一步了解 OAuth 2.0 通訊協定，以及另一種使用用戶端認證來執行服務對服務驗證的方式。

* [Microsoft 身分識別平臺中的 OAuth 2.0 用戶端認證授與](v2-oauth2-client-creds-grant-flow.md)
* [Microsoft 身分識別平臺中的 OAuth 2.0 程式碼流程](v2-oauth2-auth-code-flow.md)
* [使用 `/.default` 範圍](v2-permissions-and-consent.md#the-default-scope)
