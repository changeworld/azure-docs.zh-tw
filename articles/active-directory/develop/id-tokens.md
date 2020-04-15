---
title: 微軟識別平台 ID 權杖參考
description: 瞭解如何使用 Azure AD v1.0 和 Microsoft 標識平臺 (v2.0) 終結點發出的id_tokens。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: 23ff71bf24c3acbce3d27276981739305e1d074a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309544"
---
# <a name="microsoft-identity-platform-id-tokens"></a>微軟識別平台 ID 權杖

`id_tokens` 會在 [OpenID Connect](v2-protocols-oidc.md) 流程中傳送至用戶端應用程式。 這些權杖可以一併傳送或代替存取權杖，並由用戶端用來驗證使用者。

## <a name="using-the-id_token"></a>使用 id_token

ID 權用此應用程式, 用於驗證使用者是否屬於他們表示的身份,並取得有關它們的其他有用資訊 -它不應用於授權以代替[存取權杖](access-tokens.md)。 它提供的聲明可用於應用程式內的 UX,作為資料庫中的密鑰,並提供對用戶端應用程式的訪問。  為資料庫創建金鑰時,`idp`不應使用,因為它會破壞來賓方案。  鍵控應`sub`單獨完成(始終是唯一的),如果需要`tid`, 用於路由。  如果`oid`+`sub`需要+跨服務共享資料,將工作,因為多個服務都得到相同`oid`的 。`tid`

## <a name="claims-in-an-id_token"></a>id_token 中的宣告

Microsoft 身分識別的 `id_tokens` 為 [JWT](https://tools.ietf.org/html/rfc7519)，這表示它們是由標頭、承載和簽章所組成。 您可以使用標頭和簽章來驗證權杖的真確性，而承載則包含您用戶端所要求使用者的相關資訊。 除了特別註明，否則此處所列的所有宣告都會出現在 v1.0 和 v2.0 權杖中。

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
|`typ` | 字串 - 一律為 "JWT" | 表示權杖是 JWT。|
|`alg` | String | 指出用來簽署權杖的演算法。 範例："RS256" |
|`kid` | String | 用來簽署此權杖的公開金鑰指紋。 v1.0 和 v2.0 `id_tokens` 中都會發出此宣告。 |
|`x5t` | String | 與 `kid` 相同 (用法和值)。 不過，這是為了達到相容性，只在 v1.0 `id_tokens` 中發出的舊版宣告。 |

### <a name="payload-claims"></a>承載宣告

此清單顯示默認情況下處於大多數id_tokens的聲明(除非另有說明)。  但是,你的應用可以使用[可選聲明](active-directory-optional-claims.md)在id_token中請求其他聲明。  這些範圍從`groups`聲明到有關使用者名的資訊。

|宣告 | [格式] | 描述 |
|-----|--------|-------------|
|`aud` |  字串，應用程式識別碼 URI | 識別權杖的預定接收者。 在 `id_tokens` 中，對象是在 Azure 入口網站中指派給應用程式的應用程式識別碼。 您的應用程式應驗證此值，並拒絕值不相符的權杖。 |
|`iss` |  字串，STS URI | 識別建構並傳回權杖的 Security Token Service (STS)，以及在其中驗證使用者的 Azure AD 租用戶。 如果是由 v2.0 端點發出的權杖，URI 的結尾會是 `/v2.0`。  指出使用者是來自 Microsoft 帳戶之取用者使用者的 GUID 是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 您的應用程式應該使用宣告的 GUID 部分來限制可登入應用程式的租用戶集合 (如果有的話)。 |
|`iat` |  整數，UNIX 時間戳記 | 「發出時間 (Issued At)」表示此權杖進行驗證的時間。  |
|`idp`|字串，通常是 STS URI | 記錄驗證權杖主體的身分識別提供者。 除非使用者帳戶與簽發者不在同一租用戶中，否則此值與簽發者宣告的值相同。 如果宣告不存在,則意味著可以使用的值`iss`。  針對在組織內容中使用的個人帳戶 (例如獲邀使用 Azure AD 租用戶的個人帳戶)，`idp` 宣告可能會是 'live.com' 或包含 Microsoft 帳戶租用戶 `9188040d-6c67-4c5b-b112-36a304b66dad` 的 STS URI。 |
|`nbf` |  整數，UNIX 時間戳記 | "nbf" (生效時間) 宣告會識別生效時間，在此時間之前不得接受 JWT 以進行處理。|
|`exp` |  整數，UNIX 時間戳記 | "exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不得接受 JWT 以進行處理。  請務必注意,如果此時資源也可能拒絕令牌 -例如,如果需要更改身份驗證或檢測到權杖吊銷。 |
| `c_hash`| String |只有當識別碼權杖是隨著 OAuth 2.0 授權碼一起簽發時，代碼雜湊才會包含在識別碼權杖中。 它可用來驗證授權碼的真實性。 如需有關執行此驗證的詳細資料，請參閱 [OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)。 |
|`at_hash`| String |只有當識別碼權杖是隨著 OAuth 2.0 存取權杖一起簽發時，存取權杖雜湊才會包含在識別碼權杖中。 它可用來驗證存取權杖的真實性。 如需有關執行此驗證的詳細資料，請參閱 [OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)。 |
|`aio` | 不透明字串 | Azure AD 用來記錄資料的內部宣告，以便重複使用權杖。 應該予以忽略。|
|`preferred_username` | String | 代表使用者的主要使用者名稱。 它可以是電子郵件地址、電話號碼或未指定格式的一般使用者名稱。 其值是可變動的，並且可能隨著時間改變。 因為此值會變動，請勿用在授權決策。 接收`profile`此聲明需要作用域。|
|`email` | String | 擁有電子郵件地址的來賓帳戶預設會顯示 `email` 宣告。  應用程式可以使用 `email` [選擇性宣告](active-directory-optional-claims.md)來為受管理的使用者 (其來源租用戶和資源相同的使用者) 要求電子郵件宣告。  在 v2.0 端點上，應用程式也可以要求 `email` OpenID Connect 範圍 - 您不需要同時要求選擇性宣告和用來取得宣告的範圍。  電子郵件宣告僅支援來自使用者設定檔資訊的可定址郵件。 |
|`name` | String | `name` 宣告會提供人類看得懂的值，用以識別權杖的主體。 該值不保證是唯一的,它是可變的,並且它被設計為僅用於顯示目的。 接收`profile`此聲明需要作用域。 |
|`nonce`| String | Nonce 符合對 IDP 的原始 /authorize 要求中包含的參數。 如果不符，您的應用程式應該拒絕權杖。 |
|`oid` | 字串，GUID | 物件在 Microsoft 身分識別系統中的不可變識別碼，在此案例為使用者帳戶。 此識別碼可跨應用程式唯一識別使用者，同一位使用者登入兩個不同的應用程式會在 `oid` 宣告中收到相同的值。 Microsoft Graph 會傳回這個識別碼做為指定使用者帳戶的 `id` 屬性。 由於`oid`允許多個應用關聯使用者,`profile`因此需要作用域才能接收此聲明。 請注意,如果單個使用者存在於多個租戶中,則使用者將在每個租戶中包含不同的對象 ID - 即使使用者登錄到具有相同憑據的每個帳戶,它們也被視為不同的帳戶。 索賠`oid`是 GUID,不能重複使用。 |
|`roles`| 字串的陣列 | 分配給正在登錄的使用者的角色集。 |
|`rh` | 不透明字串 |Azure 用來重新驗證權杖的內部宣告。 應該予以忽略。 |
|`sub` | 字串，GUID | 權杖判斷提示其相關資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 主體是成對識別碼，對於特定應用程式識別碼來說，主體是唯一的。 如果單個使用者使用兩個不同的用戶端標識登錄到兩個不同的應用,則這些應用將收到主題聲明的兩個不同的值。 根據您的體系結構和隱私要求,可能不需要這樣做,也可能不需要這樣做。 |
|`tid` | 字串，GUID | 代表使用者是來自哪個 Azure AD 租用戶的 GUID。 就工作和學校帳戶而言，GUID 是使用者所屬組織的不可變租用戶識別碼。 就個人帳戶而言，此值會是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 接收`profile`此聲明需要作用域。 |
|`unique_name` | String | 提供人類看得懂的值，用以識別權杖的主體。 此值在任何給定時間點都是唯一的,但由於電子郵件和其他標識符可以重複使用,因此此值可以重新出現在其他帳戶上,因此應僅用於顯示目的。 僅在 v1.0 `id_tokens` 中發出。 |
|`uti` | 不透明字串 | Azure 用來重新驗證權杖的內部宣告。 應該予以忽略。 |
|`ver` | 字串，1.0 或 2.0 | 表示 id_token 的版本。 |


> [!NOTE]
> 從上面的例子中可以看出,v1 和 v2 id_token在所攜帶的資訊量上存在差異。 該版本基本上指定從發佈位置的 Azure AD 平台終結點。 [Azure AD Oauth 實現](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform)已經過多年的發展。 目前,我們為 AzureAD 應用程式提供了兩個不同的 oAuth 終結點。 您可以使用任何分類為 v2 的新終結點或稱為 v1 的舊終結點。 兩者中的 Oauth 終結點是不同的。 V2 終結點是較新的終結點,我們嘗試遷移 v1 終結點的所有功能,並建議新開發人員使用 v2 終結點。 
> - V1:Azure 活動目錄終結點:`https://login.microsoftonline.com/common/oauth2/authorize`
> - V2:微軟身份平台終結點:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

## <a name="validating-an-id_token"></a>驗證 id_token

驗證`id_token`與[驗證存取權杖的第一](access-tokens.md#validating-tokens)步類似 - 客戶端應驗證正確的頒發者是否已發送回權杖,並且該權杖未被篡改。 因為 `id_tokens` 一律是 JWT，所以有許多程式庫可用來驗證這些權杖 - 我們建議您使用其中一個程式庫，而不需自己執行此作業。

若要手動驗證權杖，請參閱[驗證存取權杖](access-tokens.md#validating-tokens)中的步驟詳細資料。 驗證權杖的簽章之後，應在 id_token 中驗證下列宣告 (也可由您的權杖驗證程式庫執行)：

* 時間戳記：`iat``nbf` 和 `exp` 時間戳記應該全部落在目前時間的前後 (視情況而定)。 
* 對象：`aud` 宣告應符合您應用程式的應用程式識別碼。
* Nonce：承載中的 `nonce` 宣告必須符合在初始要求期間傳入 /authorize 端點的 nonce 參數。

## <a name="next-steps"></a>後續步驟

* 存[取權杖](access-tokens.md)
* 使用[可選聲明](active-directory-optional-claims.md)自定義id_token中的聲明。
