---
title: Microsoft 身分識別平臺識別碼權杖 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 Azure AD v1.0 和 Microsoft 身分識別平臺（v2.0）端點所發出的 id_tokens。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: e0e327d169c246d023be1aca27d6844b9b92f03e
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926709"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft 身分識別平臺識別碼權杖

`id_tokens`會當做[OpenID connect](v2-protocols-oidc.md) （OIDC）流程的一部分，傳送至用戶端應用程式。 這些權杖可以一併傳送或代替存取權杖，並由用戶端用來驗證使用者。

## <a name="using-the-id_token"></a>使用 id_token

識別碼權杖應該用來驗證使用者是否為其所宣稱的身分，並取得其相關的其他實用資訊-不應將其用於授權來取代[存取權杖](access-tokens.md)。 它所提供的宣告可用於應用程式內的 UX、做為資料庫中的索引鍵，以及提供用戶端應用程式的存取權。  建立資料庫的金鑰時，不`idp`應使用，因為它會混亂來賓案例。  金鑰應單獨完成（ `sub`一律是唯一的），並`tid`在需要時用於路由。  如果您需要在服務之間共用資料， `oid` + `sub` + `tid`將可使用，因為多個服務全都`oid`取得相同的。

## <a name="claims-in-an-id_token"></a>id_token 中的宣告

`id_tokens`Microsoft 身分識別的[jwt](https://tools.ietf.org/html/rfc7519) （JSON Web 權杖），表示它們是由標頭、承載和簽章部分所組成。 您可以使用標頭和簽章來驗證權杖的真確性，而承載則包含您用戶端所要求使用者的相關資訊。 除非另有說明，此處所列的所有 JWT 宣告都會同時出現在 v1.0 和 v2.0 權杖中。

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

請在 [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q) 中檢視此 v1.0 範例權杖。

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

請在 [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw) 中檢視此 v2.0 範例權杖。

### <a name="header-claims"></a>標頭宣告

|宣告 | [格式] | 描述 |
|-----|--------|-------------|
|`typ` | 字串 - 一律為 "JWT" | 指出權杖是 JWT 權杖。|
|`alg` | String | 指出用來簽署權杖的演算法。 範例："RS256" |
|`kid` | String | 用來簽署此權杖的公開金鑰指紋。 v1.0 和 v2.0 `id_tokens` 中都會發出此宣告。 |
|`x5t` | String | 與 `kid` 相同 (用法和值)。 不過，這是為了達到相容性，只在 v1.0 `id_tokens` 中發出的舊版宣告。 |

### <a name="payload-claims"></a>承載宣告

此清單會顯示預設為最 id_tokens 的 JWT 宣告（除非另有注明）。  不過，您的應用程式可以使用[選擇性宣告](active-directory-optional-claims.md)來要求 id_token 中的其他 JWT 宣告。  這些可以從`groups`宣告的範圍，到使用者名稱的相關資訊。

|宣告 | [格式] | 描述 |
|-----|--------|-------------|
|`aud` |  字串，應用程式識別碼 URI | 識別權杖的預定接收者。 在 `id_tokens` 中，對象是在 Azure 入口網站中指派給應用程式的應用程式識別碼。 您的應用程式應驗證此值，並拒絕值不相符的權杖。 |
|`iss` |  字串，STS URI | 識別建構並傳回權杖的 Security Token Service (STS)，以及在其中驗證使用者的 Azure AD 租用戶。 如果是由 v2.0 端點發出的權杖，URI 的結尾會是 `/v2.0`。  指出使用者是來自 Microsoft 帳戶之取用者使用者的 GUID 是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 您的應用程式應該使用宣告的 GUID 部分來限制可登入應用程式的租用戶集合 (如果有的話)。 |
|`iat` |  整數，UNIX 時間戳記 | 「發出時間 (Issued At)」表示此權杖進行驗證的時間。  |
|`idp`|字串，通常是 STS URI | 記錄驗證權杖主體的身分識別提供者。 除非使用者帳戶與簽發者不在同一租用戶中，否則此值與簽發者宣告的值相同。 如果宣告不存在，則表示`iss`可以改為使用的值。  針對在組織內容中使用的個人帳戶 (例如獲邀使用 Azure AD 租用戶的個人帳戶)，`idp` 宣告可能會是 'live.com' 或包含 Microsoft 帳戶租用戶 `9188040d-6c67-4c5b-b112-36a304b66dad` 的 STS URI。 |
|`nbf` |  整數，UNIX 時間戳記 | "nbf" (生效時間) 宣告會識別生效時間，在此時間之前不得接受 JWT 以進行處理。|
|`exp` |  整數，UNIX 時間戳記 | "exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不得接受 JWT 以進行處理。  請務必注意，資源可能會在這段時間之前拒絕權杖，例如，需要變更驗證或偵測到權杖撤銷。 |
| `c_hash`| String |只有當識別碼權杖是隨著 OAuth 2.0 授權碼一起簽發時，代碼雜湊才會包含在識別碼權杖中。 它可用來驗證授權碼的真實性。 如需有關執行此驗證的詳細資料，請參閱 [OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)。 |
|`at_hash`| String |只有當識別碼權杖是隨著 OAuth 2.0 存取權杖一起簽發時，存取權杖雜湊才會包含在識別碼權杖中。 它可用來驗證存取權杖的真實性。 如需有關執行此驗證的詳細資料，請參閱 [OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)。 |
|`aio` | 不透明字串 | Azure AD 用來記錄資料的內部宣告，以便重複使用權杖。 應該予以忽略。|
|`preferred_username` | String | 代表使用者的主要使用者名稱。 它可以是電子郵件地址、電話號碼或未指定格式的一般使用者名稱。 其值是可變動的，並且可能隨著時間改變。 因為此值會變動，請勿用在授權決策。 需要`profile`範圍才能接收此宣告。|
|`email` | String | 擁有電子郵件地址的來賓帳戶預設會顯示 `email` 宣告。  應用程式可以使用 `email` [選擇性宣告](active-directory-optional-claims.md)來為受管理的使用者 (其來源租用戶和資源相同的使用者) 要求電子郵件宣告。  在 v2.0 端點上，應用程式也可以要求 `email` OpenID Connect 範圍 - 您不需要同時要求選擇性宣告和用來取得宣告的範圍。  電子郵件宣告僅支援來自使用者設定檔資訊的可定址郵件。 |
|`name` | String | `name` 宣告會提供人類看得懂的值，用以識別權杖的主體。 此值不保證是唯一的，它是可變動的，而且設計成僅供顯示之用。 需要`profile`範圍才能接收此宣告。 |
|`nonce`| String | Nonce 符合對 IDP 的原始 /authorize 要求中包含的參數。 如果不符，您的應用程式應該拒絕權杖。 |
|`oid` | 字串，GUID | 物件在 Microsoft 身分識別系統中的不可變識別碼，在此案例為使用者帳戶。 此識別碼可跨應用程式唯一識別使用者，同一位使用者登入兩個不同的應用程式會在 `oid` 宣告中收到相同的值。 Microsoft Graph 會傳回這個識別碼做為指定使用者帳戶的 `id` 屬性。 因為`oid`允許多個應用程式將使用者相互關聯`profile` ，所以必須要有範圍才能接收此宣告。 請注意，如果單一使用者存在於多個租使用者中，使用者將會在每個租使用者中包含不同的物件識別碼-它們會被視為不同的帳戶，即使使用者使用相同的認證登入每個帳戶也是一樣。 `oid`宣告是 GUID，無法重複使用。 |
|`roles`| 字串陣列 | 已指派給正在登入之使用者的一組角色。 |
|`rh` | 不透明字串 |Azure 用來重新驗證權杖的內部宣告。 應該予以忽略。 |
|`sub` | 字串，GUID | 權杖判斷提示其相關資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 主體是成對識別碼，對於特定應用程式識別碼來說，主體是唯一的。 如果單一使用者使用兩個不同的用戶端識別碼登入兩個不同的應用程式，這些應用程式將會收到兩個不同的主體宣告值。 視您的架構和隱私權需求而定，這不一定會需要。 |
|`tid` | 字串，GUID | 代表使用者是來自哪個 Azure AD 租用戶的 GUID。 就工作和學校帳戶而言，GUID 是使用者所屬組織的不可變租用戶識別碼。 就個人帳戶而言，此值會是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 需要`profile`範圍才能接收此宣告。 |
|`unique_name` | String | 提供人類看得懂的值，用以識別權杖的主體。 這個值在任何指定的時間點都是唯一的，但是當電子郵件和其他識別碼可以重複使用時，這個值可以重新出現在其他帳戶上，因此只能用於顯示用途。 僅在 v1.0 `id_tokens` 中發出。 |
|`uti` | 不透明字串 | Azure 用來重新驗證權杖的內部宣告。 應該予以忽略。 |
|`ver` | 字串，1.0 或 2.0 | 表示 id_token 的版本。 |

> [!NOTE]
> V1 和 v2 id_token 與上述範例中所見的資訊數量有所差異。 版本基本上會指定發出它的 Azure AD 平臺端點。 [Azure AD 的 Oauth 實行](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform)已演變到數年。 目前，我們有兩個不同的 oAuth 端點可用於 AzureAD 應用程式。 您可以使用任何分類為 v2 的新端點，或稱為 v1 的舊端點。 這兩者的 Oauth 端點都不同。 V2 端點較新，我們正嘗試遷移 v1 端點的所有功能，並建議新的開發人員使用 V2 端點。
>
> - V1： Azure Active Directory 端點：`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2： Microsoft 身分識別平臺端點：`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>驗證 id_token

`id_token`驗證與[驗證存取權杖](access-tokens.md#validating-tokens)的第一個步驟類似-您的用戶端應該驗證正確的簽發者是否已送回權杖，而且它尚未遭到篡改。 因為`id_tokens`一律是 JWT 權杖，所以有許多程式庫都是用來驗證這些權杖，我們建議您使用其中一個，而不是自行執行。

若要手動驗證權杖，請參閱[驗證存取權杖](access-tokens.md#validating-tokens)中的步驟詳細資料。 驗證權杖上的簽章之後，應在 id_token 中驗證下列 JWT 宣告（也可以透過您的權杖驗證程式庫來完成）：

* 時間戳記：`iat``nbf` 和 `exp` 時間戳記應該全部落在目前時間的前後 (視情況而定)。
* 對象：`aud` 宣告應符合您應用程式的應用程式識別碼。
* Nonce：承載中的 `nonce` 宣告必須符合在初始要求期間傳入 /authorize 端點的 nonce 參數。

## <a name="next-steps"></a>後續步驟

* 瞭解[存取權杖](access-tokens.md)
* 使用[選擇性宣告](active-directory-optional-claims.md)，在您的 id_token 中自訂 JWT 宣告。
