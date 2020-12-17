---
title: Microsoft 身分識別平臺識別碼權杖 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 Azure AD v1.0 和 Microsoft 身分識別平臺 (v2.0) 端點所發出的 id_tokens。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: eec24ca19af8c41a842b1db3db4b9e16f748f029
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653243"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Microsoft 身分識別平臺識別碼權杖

`id_tokens` 會以 [OpenID Connect](v2-protocols-oidc.md) (OIDC) 流程的一部分傳送至用戶端應用程式。 這些使用者可以與存取權杖一起傳送或傳送，而不是由用戶端用來驗證使用者。

## <a name="using-the-id_token"></a>使用 id_token

識別碼權杖應該用來驗證使用者是否為其所宣稱的身分，並取得有關它們的其他實用資訊-它不應該用於授權來取代 [存取權杖](access-tokens.md)。 它提供的宣告可用於應用程式內的 UX、做為 [資料庫中](#using-claims-to-reliably-identify-a-user-subject-and-object-id)的索引鍵，以及提供用戶端應用程式的存取權。  

## <a name="claims-in-an-id_token"></a>id_token 中的宣告

`id_tokens`[jwt](https://tools.ietf.org/html/rfc7519) () 的 JSON Web 權杖，這表示它們是由標頭、承載和簽章部分所組成。 您可以使用標頭和簽章來驗證權杖的真確性，而承載則包含您用戶端所要求使用者的相關資訊。 除非有注明，此處所列的所有 JWT 宣告都會出現在 v1.0 和 v2.0 權杖中。

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

依預設，此清單會顯示最 id_tokens 的 JWT 宣告 (但) 注明。  不過，您的應用程式可以使用 [選擇性宣告](active-directory-optional-claims.md) 來要求 id_token 中的額外 JWT 宣告。  這些範圍可以從宣告 `groups` 到使用者名稱的相關資訊。

|宣告 | [格式] | 描述 |
|-----|--------|-------------|
|`aud` |  字串，應用程式識別碼 URI | 識別權杖的預定接收者。 在 `id_tokens` 中，對象是在 Azure 入口網站中指派給應用程式的應用程式識別碼。 您的應用程式應驗證此值，並拒絕值不相符的權杖。 |
|`iss` |  字串，STS URI | 識別建構並傳回權杖的 Security Token Service (STS)，以及在其中驗證使用者的 Azure AD 租用戶。 如果是由 v2.0 端點發出的權杖，URI 的結尾會是 `/v2.0`。  指出使用者是來自 Microsoft 帳戶之取用者使用者的 GUID 是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 您的應用程式應該使用宣告的 GUID 部分來限制可登入應用程式的租用戶集合 (如果有的話)。 |
|`iat` |  整數，UNIX 時間戳記 | 「發出時間 (Issued At)」表示此權杖進行驗證的時間。  |
|`idp`|字串，通常是 STS URI | 記錄驗證權杖主體的身分識別提供者。 除非使用者帳戶與簽發者不在同一租用戶中，否則此值與簽發者宣告的值相同。 如果宣告不存在，這代表可改用 `iss` 的值。  針對在組織內容中使用的個人帳戶 (例如獲邀使用 Azure AD 租用戶的個人帳戶)，`idp` 宣告可能會是 'live.com' 或包含 Microsoft 帳戶租用戶 `9188040d-6c67-4c5b-b112-36a304b66dad` 的 STS URI。 |
|`nbf` |  整數，UNIX 時間戳記 | "nbf" (生效時間) 宣告會識別生效時間，在此時間之前不得接受 JWT 以進行處理。|
|`exp` |  整數，UNIX 時間戳記 | "exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不得接受 JWT 以進行處理。  請務必注意，資源可能會在這段時間之前拒絕權杖，例如，需要進行驗證變更或偵測到權杖撤銷。 |
| `c_hash`| String |只有當識別碼權杖是隨著 OAuth 2.0 授權碼一起簽發時，代碼雜湊才會包含在識別碼權杖中。 它可用來驗證授權碼的真實性。 如需有關執行此驗證的詳細資料，請參閱 [OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)。 |
|`at_hash`| String |只有在 `/authorize` 使用 OAuth 2.0 存取權杖從端點發出識別碼權杖時，存取權杖雜湊才會包含在識別碼權杖中。 它可用來驗證存取權杖的真實性。 如需有關執行此驗證的詳細資料，請參閱 [OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken)。 端點上的識別碼權杖不會傳回此識別碼 `/token` 。 |
|`aio` | 不透明字串 | Azure AD 用來記錄資料的內部宣告，以便重複使用權杖。 應該予以忽略。|
|`preferred_username` | String | 代表使用者的主要使用者名稱。 它可以是電子郵件地址、電話號碼或未指定格式的一般使用者名稱。 其值是可變動的，並且可能隨著時間改變。 因為此值會變動，請勿用在授權決策。 `profile`需要範圍才能接收此宣告。|
|`email` | String | 擁有電子郵件地址的來賓帳戶預設會顯示 `email` 宣告。  應用程式可以使用 `email` [選擇性宣告](active-directory-optional-claims.md)來為受管理的使用者 (其來源租用戶和資源相同的使用者) 要求電子郵件宣告。  在 v2.0 端點上，應用程式也可以要求 `email` OpenID Connect 範圍 - 您不需要同時要求選擇性宣告和用來取得宣告的範圍。  電子郵件宣告僅支援來自使用者設定檔資訊的可定址郵件。 |
|`name` | String | `name` 宣告會提供人類看得懂的值，用以識別權杖的主體。 此值不保證是唯一的，而是可變動的，而且是設計成僅供顯示之用。 `profile`需要範圍才能接收此宣告。 |
|`nonce`| String | Nonce 符合對 IDP 的原始 /authorize 要求中包含的參數。 如果不符，您的應用程式應該拒絕權杖。 |
|`oid` | 字串，GUID | 物件在 Microsoft 身分識別系統中的不可變識別碼，在此案例為使用者帳戶。 此識別碼可跨應用程式唯一識別使用者，同一位使用者登入兩個不同的應用程式會在 `oid` 宣告中收到相同的值。 Microsoft Graph 會傳回這個識別碼做為指定使用者帳戶的 `id` 屬性。 因為 `oid` 可讓多個應用程式與使用者相互關聯，所以 `profile` 必須要有範圍才能接收此宣告。 請注意，如果有單一使用者存在於多個租使用者中，使用者將會在每個租使用者中包含不同的物件識別碼-系統會將它們視為不同的帳戶，即使使用者以相同的認證登入每個帳戶也是一樣。 宣告 `oid` 是 GUID，無法重複使用。 |
|`roles`| 字串陣列 | 指派給登入之使用者的一組角色。 |
|`rh` | 不透明字串 |Azure 用來重新驗證權杖的內部宣告。 應該予以忽略。 |
|`sub` | String | 權杖判斷提示其相關資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 主體是成對識別碼，對於特定應用程式識別碼來說，主體是唯一的。 如果單一使用者使用兩個不同的用戶端識別碼登入兩個不同的應用程式，則這些應用程式將會收到兩個不同的主體宣告值。 視您的架構和隱私權需求而定，可能也不會想要這樣做。 |
|`tid` | 字串，GUID | 代表使用者是來自哪個 Azure AD 租用戶的 GUID。 就工作和學校帳戶而言，GUID 是使用者所屬組織的不可變租用戶識別碼。 就個人帳戶而言，此值會是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 `profile`需要範圍才能接收此宣告。 |
|`unique_name` | String | 提供人類看得懂的值，用以識別權杖的主體。 這個值在任何給定的時間點都是唯一的，但是當電子郵件和其他識別碼可以重複使用時，這個值可能會重新出現在其他帳戶上，因此只能用於顯示用途。 僅在 v1.0 `id_tokens` 中發出。 |
|`uti` | 不透明字串 | Azure 用來重新驗證權杖的內部宣告。 應該予以忽略。 |
|`ver` | 字串，1.0 或 2.0 | 表示 id_token 的版本。 |
|`hasgroups`|Boolean|如果有的話，一律為 true，表示使用者至少在一個群組中。 在隱含授與流程中用來取代 Jwt 的群組宣告，如果完整群組宣告會將 URI 片段延伸到超過 URL 長度限制 (目前) 的6個或更多群組。 表示用戶端應該使用 Microsoft Graph API 來判斷使用者的群組 (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`)。|
|`groups:src1`|JSON 物件 | 若為沒有長度限制 (請參閱上面的 `hasgroups`) 但是對權杖而言仍然太大的權杖要求，則會包含使用者的完整 groups 清單連結。 在 JWT 中以分散式宣告形式取代 `groups` 宣告，在 SAML 中則以新宣告形式取代。 <br><br>**範例 JWT 值**： <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> 如需詳細資訊，請參閱 [群組超額](#groups-overage-claim)宣告。|

> [!NOTE]
> V1.0 和 v2.0 id_token 在上述範例中所見的資訊數量有所差異。 版本是根據其要求所在的端點。 雖然現有的應用程式可能會使用 Azure AD 端點，但新的應用程式應該使用 v2.0 「Microsoft 身分識別平臺」端點。
>
> - 1.0 版： Azure AD 端點： `https://login.microsoftonline.com/common/oauth2/authorize`
> - 2.0 版： Microsoft 身分識別平臺端點： `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>使用宣告來可靠地識別使用者 (主體和物件識別碼) 

找出使用者 (說，在資料庫中進行查閱，或決定他們) 的許可權時，請務必使用在一段時間內保持不變且獨一無二的資訊。 繼承應用程式有時會使用電子郵件地址、電話號碼或 UPN 等欄位。  這些都可以隨著時間而變更，也可以在一段時間內重複使用-當員工變更其名稱時，或將員工的電子郵件地址與先前的電子郵件地址相符，就不會再出現員工) 。 因此，您的應用程式不一定要使用人類看得懂的資料來識別使用者-人類可讀取的資料， **而是要** 變更它。 相反地，請使用 OIDC 標準所提供的宣告，或由 Microsoft 提供的擴充宣告 `sub` 和 `oid` 宣告。

若要正確地儲存每位使用者的資訊，請使用 `sub` 或 `oid` 單獨 (因為 guid 是唯一的) ，並在 `tid` 需要時用於路由或分區化。  如果您需要在服務之間共用資料， `oid` + `tid` 最好是讓所有應用程式取得相同的 `oid` 和 `tid` 宣告給指定的使用者。  `sub`Microsoft 身分識別平臺中的宣告為「成對」-它是根據權杖收件者、租使用者和使用者的組合而定。  因此，針對指定使用者要求識別碼權杖的兩個應用程式將會收到不同的 `sub` 宣告，但該 `oid` 使用者的宣告相同。

>[!NOTE]
> 請勿使用宣告 `idp` 來儲存使用者的相關資訊，以嘗試跨租使用者將使用者相互關聯。  它將無法在租 `oid` `sub` 使用者間依設計變更使用者的和宣告，以確保應用程式無法跨租使用者追蹤使用者。  
>
> 來賓案例（使用者位於某個租使用者中，並在另一個租使用者中進行驗證）應將使用者視為服務的新使用者。  您的 Contoso 租使用者中的檔和許可權不應套用於 Fabrikam 租使用者中。 這一點很重要，可避免跨租使用者意外資料洩漏。

### <a name="groups-overage-claim"></a>群組超額宣告
為確保權杖大小不超過 HTTP 標頭大小限制，Azure AD 會限制在宣告中包含的物件識別碼數目 `groups` 。 如果使用者隸屬於超出超額限制 (SAML 權杖為 150，JWT 權杖為 200) 的群組，則 Azure AD 不會在權杖中發出群組宣告。 相反地，其會在權杖中包含超額宣告，以指示應用程式查詢 Microsoft Graph API 來取得使用者的群組成員資格。

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="validating-an-id_token"></a>驗證 id_token

驗證的 `id_token` 方式類似于 [驗證存取權杖](access-tokens.md#validating-tokens) 的第一個步驟-您的用戶端可以驗證正確的簽發者是否已傳回權杖，而且該權杖並未遭到篡改。 因為 `id_tokens` 一律為 JWT 權杖，所以有許多程式庫可以驗證這些權杖，我們建議您使用其中一種程式庫，而不是自行進行。  請注意，只有機密用戶端 (具有秘密) 的用戶端應該驗證識別碼權杖。  公用應用程式 (程式碼完全在您無法控制的裝置或網路上執行，例如，使用者的瀏覽器或其家用網路) 不會因為驗證識別碼權杖而受益，因為惡意使用者可以攔截和編輯用於驗證權杖的金鑰。

若要手動驗證權杖，請參閱[驗證存取權杖](access-tokens.md#validating-tokens)中的步驟詳細資料。 在驗證權杖上的簽章之後，應在 id_token 中驗證下列 JWT 宣告 (這些宣告也可以由您的權杖驗證程式庫) 進行：

* 時間戳記：`iat``nbf` 和 `exp` 時間戳記應該全部落在目前時間的前後 (視情況而定)。
* 對象：`aud` 宣告應符合您應用程式的應用程式識別碼。
* Nonce：承載中的 `nonce` 宣告必須符合在初始要求期間傳入 /authorize 端點的 nonce 參數。

## <a name="next-steps"></a>後續步驟

* 瞭解 [存取權杖](access-tokens.md)
* 使用 [選擇性宣告](active-directory-optional-claims.md)自訂您 id_token 中的 JWT 宣告。
