---
title: Microsoft 身分識別平臺存取權杖參考 |Azure
description: 瞭解 Azure AD v1.0 和 Microsoft 身分識別平臺（v2.0）端點所發出的存取權杖。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/2/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 752e8745a1cf2965d2dc88eaeee9404cf596547a
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669315"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft 身分識別平臺存取權杖

存取權杖可讓用戶端安全地呼叫受 Azure 保護的 API。 Microsoft 身分識別平臺存取權杖是由 Azure 所簽署的[jwt](https://tools.ietf.org/html/rfc7519)、Base64 編碼的 JSON 物件。 用戶端應將存取權杖視為不透明字串，因為權杖的內容僅會用於存取資源。 若要用於驗證及偵錯，開發人員可以使用 [jwt.ms](https://jwt.ms) 等網站來解碼 JWT。 您的用戶端可以使用各種通訊協定，從 v1.0 端點或 v2.0 端點取得存取權杖。

當您的用戶端要求存取權杖時，Azure AD 也會傳回一些關於應用程式耗用量存取權杖的中繼資料。 此資訊包括存取權杖的到期時間以及其有效範圍。 此資料可讓應用程式執行存取權杖的智慧型快取，而不需剖析存取權杖本身。

如果您的應用程式是用戶端可以要求存取的資源 (Web API)，存取權杖會提供可用於驗證和授權的資訊，例如使用者、用戶端、簽發者和權限等等。

請參閱下列各節以了解資源可以如何驗證及使用存取權杖內部的宣告。

> [!IMPORTANT]
> 存取權杖是根據權杖的*物件*來建立，這表示在權杖中擁有範圍的應用程式。  這是[應用程式資訊清單](reference-app-manifest.md#manifest-reference)中 `accessTokenAcceptedVersion` 資源設定以 `2` 的方式，可讓呼叫 v1.0 端點的用戶端接收 v2.0 存取權杖。  同樣地，這也是為什麼針對您的用戶端變更存取權杖[選擇性宣告](active-directory-optional-claims.md)的原因，並不會變更在要求權杖時所收到的存取權杖，這是資源所擁有的 `user.read`。
> 基於相同原因，使用個人帳戶（例如 hotmail.com 或 outlook.com）測試您的用戶端應用程式時，您可能會發現用戶端所收到的存取權杖是不透明的字串。 這是因為正在存取的資源已要求舊版 MSA （Microsoft 帳戶）票證，而且用戶端無法加以瞭解。

## <a name="sample-tokens"></a>權杖範例

v1.0 和 v2.0 權杖看起來類似，而且包含許多相同的宣告。 其各自的範例如下所示。

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

請在 [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd) 中檢視此 v1.0 權杖。

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

請在 [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt) 中檢視此 v2.0 權杖。

## <a name="claims-in-access-tokens"></a>存取權杖中的宣告

JWT 分成三個部分：

* **標頭** - 提供如何[驗證權杖](#validating-tokens)的相關資訊，包括權杖類型和簽署方式的相關資訊。
* **承載** - 對於所有嘗試呼叫您服務的使用者或應用程式，其相關重要資料都包含在此。
* **簽章** - 用來驗證權杖的原始資料。

各部分會以句點分隔 (`.`)，並各別以 Base64 編碼。

如果有值可填入宣告時，宣告才會存在。 因此，您的應用程式不應該相依于目前存在的宣告。 範例包括 `pwd_exp` （並非每個租使用者都需要密碼過期）或 `family_name` （用戶端認證（[v1.0、v2.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) [）流程](v2-oauth2-client-creds-grant-flow.md)代表不含名稱的應用程式。 用於驗證存取權杖的宣告一律會存在。

> [!NOTE]
> 如果要重複使用權杖，某些宣告會用來協助 Azure AD 保護權杖。 這些宣告會在「不透明」描述中標示為不公開使用。 這些宣告可能會或可能不會出現在權杖中，而且可能會在沒有通知的情況下新增宣告。

### <a name="header-claims"></a>標頭宣告

|宣告 | [格式] | 描述 |
|--------|--------|-------------|
| `typ` | 字串 - 一律為 "JWT" | 表示權杖是 JWT。|
| `nonce` | String | 唯一識別碼，用來防範權杖重新執行攻擊。 您的資源可以記錄此值，以防止重新執行。 |
| `alg` | String | 表示用來簽署權杖的演算法，例如 "RS256" |
| `kid` | String | 為用來簽署此權杖的公開金鑰指定指紋。 v1.0 和 v2.0 存取權杖中都會發出此宣告。 |
| `x5t` | String | 與 `kid` 的功能一樣 (用法和值)。 `x5t` 是只在 v1.0 存取權杖中發出的舊版宣告，僅針對相容性使用。 |

### <a name="payload-claims"></a>承載宣告

| 宣告 | [格式] | 描述 |
|-----|--------|-------------|
| `aud` | 字串，應用程式識別碼 URI | 識別權杖的預定接收者。 在 [識別碼權杖] 中，物件是您的應用程式識別碼，指派給您在 Azure 入口網站中的應用程式。 您的應用程式應驗證此值，並拒絕值不相符的權杖。 |
| `iss` | 字串，STS URI | 識別建構並傳回權杖的 Security Token Service (STS)，以及在其中驗證使用者的 Azure AD 租用戶。 如果發出的權杖是 v2.0 權杖 (請參閱`ver`宣告)，URI 的結尾會是 `/v2.0`。 指出使用者是來自 Microsoft 帳戶之取用者使用者的 GUID 是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 您的應用程式應該使用宣告的 GUID 部分來限制可登入應用程式的租用戶集合 (如果有的話)。 |
|`idp`| 字串，通常是 STS URI | 記錄驗證權杖主體的身分識別提供者。 除非使用者帳戶與簽發者不在同一租用戶中，否則此值與簽發者宣告的值相同。 如果宣告不存在，則表示可以改為使用 `iss` 的值。  針對在組織內容中使用的個人帳戶 (例如獲邀使用 Azure AD 租用戶的個人帳戶)，`idp` 宣告可能會是 'live.com' 或包含 Microsoft 帳戶租用戶 `9188040d-6c67-4c5b-b112-36a304b66dad` 的 STS URI。 |
| `iat` | 整數，UNIX 時間戳記 | 「發出時間 (Issued At)」表示此權杖進行驗證的時間。 |
| `nbf` | 整數，UNIX 時間戳記 | "nbf" (生效時間) 宣告會識別生效時間，在此時間之前不可接受 JWT 以進行處理。 |
| `exp` | 整數，UNIX 時間戳記 | "exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不可接受 JWT 以進行處理。 請務必注意，資源也可能在此時間之前拒絕權杖，例如，需要變更驗證或偵測到權杖已撤銷時。 |
| `aio` | 不透明字串 | Azure AD 用來記錄資料的內部宣告，以便重複使用權杖。 資源不應該使用此宣告。 |
| `acr` | 字串，"0" 或 "1" | 只存在於 v1.0 權杖中。 「驗證內容類別」宣告。 值為 "0" 表示使用者驗證不符合 ISO/IEC 29115 的需求。 |
| `amr` | 字串的 JSON 陣列 | 只存在於 v1.0 權杖中。 識別如何驗證權杖的主體。 請參閱 [amr 宣告](#the-amr-claim)一節，以瞭解更多詳細資訊。 |
| `appid` | 字串，GUID | 只存在於 v1.0 權杖中。 使用權杖的用戶端應用程式識別碼。 應用程式代表本身或使用者行事。 應用程式識別碼通常代表應用程式物件，但也可以代表 Azure AD 中的服務主體物件。 |
| `appidacr` | "0"、"1" 或 "2" | 只存在於 v1.0 權杖中。 指出如何驗證用戶端。 若為公用用戶端，此值為 "0"。 如果使用用戶端識別碼和用戶端密碼，此值為 "1"。 如果已使用用戶端憑證進行驗證，則值為 "2"。 |
| `azp` | 字串，GUID | 僅存在於 v2.0 權杖中，`appid`的取代。 使用權杖的用戶端應用程式識別碼。 應用程式代表本身或使用者行事。 應用程式識別碼通常代表應用程式物件，但也可以代表 Azure AD 中的服務主體物件。 |
| `azpacr` | "0"、"1" 或 "2" | 僅存在於 v2.0 權杖中，`appidacr`的取代。 指出如何驗證用戶端。 若為公用用戶端，此值為 "0"。 如果使用用戶端識別碼和用戶端密碼，此值為 "1"。 如果已使用用戶端憑證進行驗證，則值為 "2"。 |
| `preferred_username` | String | 代表使用者的主要使用者名稱。 它可以是電子郵件地址、電話號碼或未指定格式的一般使用者名稱。 其值是可變動的，並且可能隨著時間改變。 因為此值會變動，請勿用在授權決策。  但可以將它用於使用者名稱提示。 需要 `profile` 範圍才能接收此宣告。 |
| `name` | String | 提供人類看得懂的值，用以識別權杖的主體。 此值不保證是唯一值，它是可變動的，並且在設計上僅用於顯示。 需要 `profile` 範圍才能接收此宣告。 |
| `scp` | 字串，範圍的空格分隔清單 | 由您應用程式公開的範圍集合，用戶端應用程式已針對此集合要求 (和接收) 同意。 您的應用程式應確認這些範圍是由應用程式公開的有效範圍，並根據這些範圍的值做出授權決策。 僅包含於[使用者權杖](#user-and-application-tokens)中。 |
| `roles` | 字串陣列，許可權清單 | 您的應用程式所公開的一組許可權，要求的應用程式或使用者已獲得呼叫的許可權。 針對[應用程式權杖](#user-and-application-tokens)，這會在用戶端認證[流程（v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) [、v2.0](v2-oauth2-client-creds-grant-flow.md)）中用來取代使用者範圍。  對於[使用者權杖](#user-and-application-tokens)，這會填入使用者指派給目標應用程式的角色。 |
| `wids` | [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) guid 的陣列 | 代表指派給此使用者的租使用者範圍角色，從[[管理員角色] 頁面](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids)中的角色區段。  此宣告是透過[應用程式資訊清單](reference-app-manifest.md)的 `groupMembershipClaims` 屬性，以每個應用程式為基礎進行設定。  必須將它設定為 "All" 或 "DirectoryRole"。  可能不會出現在因權杖長度考慮而透過隱含流程取得的權杖中。 |
| `groups` | GUID 的 JSON 陣列 | 提供代表主體群組成員資格的物件識別碼。 這些值都是唯一的 (請參閱「物件識別碼」)，而且可安全地用來管理存取權，例如強制授權以存取資源。 群組宣告中包含的群組會透過`groupMembershipClaims`應用程式資訊清單[的 ](reference-app-manifest.md) 屬性，針對每個應用程式進行設定。 Null 值將會排除所有群組，"SecurityGroup" 值只會包含 Active Directory 安全性群組成員資格，而 "All" 值將會包含安全性群組和 Office 365 通訊群組清單。 <br><br>請參閱下面的 `hasgroups` 宣告，以取得使用 `groups` 宣告搭配隱含授與的詳細資訊。 <br>對於其他流程，如果使用者所屬的群組數目超過限制（SAML 為150，JWT 為200），則會將超額宣告新增至指向包含使用者群組清單 Microsoft Graph 端點的宣告來源。 |
| `hasgroups` | Boolean | 如果有的話，一律為 `true`，表示使用者在至少一個群組中。 如果完整 groups 宣告會將 URI 片段延伸超出 URL 長度限制 (目前為 6 個或更多群組)，則使用於取代隱含授與流程中 JWT 的 `groups` 宣告。 表示用戶端應該使用 Microsoft Graph API 來判斷使用者的群組（`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`）。 |
| `groups:src1` | JSON 物件 | 若為沒有長度限制 (請參閱上面的 `hasgroups`) 但是對權杖而言仍然太大的權杖要求，則會包含使用者的完整 groups 清單連結。 在 JWT 中以分散式宣告形式取代 `groups` 宣告，在 SAML 中則以新宣告形式取代。 <br><br>**範例 JWT 值**： <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | 字串，GUID | 權杖判斷提示其相關資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 它可用來安全地執行授權檢查 (例如當權杖用於存取資源時)，並可做為資料庫資料表中的索引鍵。 由於主體一律是存在於 Azure AD 所簽發的權杖中，因此建議您在一般用途的授權系統中使用此值。 不過，主體是成對識別碼，對於特定應用程式識別碼來說，主體是唯一的。 因此，如果單一使用者使用兩個不同的用戶端識別碼登入兩個不同的應用程式，這些應用程式會收到兩個不同的主體宣告值。 視您的架構和隱私權需求而定，這不一定是您想要的。 另請參閱 `oid` 宣告（這在租使用者內的應用程式中保持不變）。 |
| `oid` | 字串，GUID | 物件在 Microsoft 身分識別平台中的不可變識別碼，在此案例為使用者帳戶。 它也可用來安全地執行授權檢查，以及做為資料庫資料表中的索引鍵。 此識別碼可跨應用程式唯一識別使用者，同一位使用者登入兩個不同的應用程式會在 `oid` 宣告中收到相同的值。 因此，在對 Microsoft 線上服務 (例如 Microsoft Graph) 進行查詢時可使用 `oid`。 Microsoft Graph 會傳回此識別碼做為指定[使用者帳戶](/graph/api/resources/user)的 `id` 屬性。 因為 `oid` 可讓多個應用程式相互關聯使用者，因此需要 `profile` 範圍才能接收此宣告。 請注意，如果單一使用者存在於多個租用戶，使用者將會在每個租用戶中包含不同的物件識別碼，它們會被視為不同帳戶，即使使用者使用相同認證來登入各個帳戶也是如此。 |
| `tid` | 字串，GUID | 代表使用者是來自哪個 Azure AD 租用戶。 就工作和學校帳戶而言，GUID 是使用者所屬組織的不可變租用戶識別碼。 就個人帳戶而言，此值會是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 需要 `profile` 範圍才能接收此宣告。 |
| `unique_name` | String | 只存在於 v1.0 權杖中。 提供人類看得懂的值，用以識別權杖的主體。 此值不保證是租用戶中的唯一值，而且應僅用於顯示目的。 |
| `uti` | 不透明字串 | Azure 用來重新驗證權杖的內部宣告。 資源不應該使用此宣告。 |
| `rh` | 不透明字串 | Azure 用來重新驗證權杖的內部宣告。 資源不應該使用此宣告。 |
| `ver` | 字串，`1.0` 或 `2.0` | 表示存取權杖的版本。 |

**群組超額宣告**

為確保權杖大小不會超過 HTTP 標頭大小限制，Azure AD 會限制在群組宣告中包含的物件識別碼數目。 如果使用者隸屬于超額限制的群組（SAML 權杖為150，JWT 權杖為200），則 Azure AD 不會在權杖中發出群組宣告。 相反地，它會在權杖中包含超額宣告，指示應用程式查詢 Microsoft Graph API 以取得使用者的群組成員資格。

```JSON
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

您可以使用 [[應用程式建立腳本](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts)] 資料夾中提供的 `BulkCreateGroups.ps1` 來協助測試超額案例。

#### <a name="v10-basic-claims"></a>v1.0 基本宣告

下列宣告將會包含在 v1.0 權杖中（如果適用的話），但預設不會包含在 v2.0 權杖中。 如果您使用 v2.0，並且需要這些宣告的其中一個，請使用[選擇性宣告](active-directory-optional-claims.md)來要求它們。

| 宣告 | [格式] | 描述 |
|-----|--------|-------------|
| `ipaddr`| String | 從中進行使用者驗證的 IP 位址。 |
| `onprem_sid`| 字串，採 [SID 格式](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | 如果使用者具有內部部署驗證，此宣告會提供其 SID。 您可以使用 `onprem_sid` 取得繼承應用程式中的授權。|
| `pwd_exp`| 整數，UNIX 時間戳記 | 表示使用者密碼到期的時間。 |
| `pwd_url`| String | 可傳送給使用者以重設其密碼的 URL。 |
| `in_corp`| boolean | 指出用戶端是否是從公司網路登入的。 如果不是，則不包含宣告。 |
| `nickname`| String | 使用者的額外名稱 (有別於名字或姓氏)。|
| `family_name` | String | 提供使用者的姓氏 (如使用者物件中所定義)。 |
| `given_name` | String | 提供使用者的名字 (如使用者物件上所設定)。 |
| `upn` | String | 使用者的使用者名稱。 可能是電話號碼、電子郵件地址或未格式化的字串。 應只用於顯示用途，並在重新驗證情節中提供使用者名稱提示。 |

#### <a name="the-amr-claim"></a>`amr` 宣告

Microsoft 身分識別可透過不同的方式進行驗證，這可能與您的應用程式相關。 `amr` 宣告為陣列，其中可包含多個項目，例如 `["mfa", "rsa", "pwd"]`，適用於同時使用密碼和驗證器應用程式的驗證。

| 值 | 描述 |
|-----|-------------|
| `pwd` | 密碼驗證，可以是使用者的 Microsoft 密碼或應用程式的用戶端祕密。 |
| `rsa` | 驗證會以 RSA 金鑰證明為基礎，例如使用 [Microsoft Authenticator 應用程式](https://aka.ms/AA2kvvu)。 這包括驗證是否由具有服務擁有之 X509 憑證的自我簽署 JWT 完成。 |
| `otp` | 使用電子郵件或簡訊的單次密碼。 |
| `fed` | 已使用同盟驗證判斷提示 (例如 JWT 或 SAML)。 |
| `wia` | Windows 整合式驗證 |
| `mfa` | 已使用多重要素驗證。 此值存在時，其他驗證方法也會包含在內。 |
| `ngcmfa` | 相當於`mfa`，用來佈建的某些進階的認證類型。 |
| `wiaormfa`| 使用者已使用 Windows 或 MFA 認證來進行驗證。 |
| `none` | 未執行任何驗證。 |

## <a name="validating-tokens"></a>驗證權杖

若要驗證 id_token 或 access_token，您的應用程式應該驗證權杖的簽章和宣告。 若要驗證存取權杖，您的應用程式也應該驗證簽發者、物件和簽署權杖。 這些都需要對 OpenID 探索文件中的值進行驗證。 例如，租使用者獨立版的檔位於[https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)。

Azure AD 中介軟體已內建驗證存取權杖的功能，您可以瀏覽我們的[範例](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples)，以找到您所選擇語言的範例。

我們會提供程式庫和程式碼範例，以示範如何處理權杖驗證。 下列資訊專門提供給想要了解基礎程序的人。 另外還有數個協力廠商開放原始碼程式庫可用於 JWT 驗證-幾乎每個平臺和語言都有至少一個選項。 如需有關 Azure AD 驗證程式庫和程式碼範例的詳細資訊，請參閱 [v1.0 驗證程式庫](../azuread-dev/active-directory-authentication-libraries.md)和 [v2.0 驗證程式庫](reference-v2-libraries.md)。

### <a name="validating-the-signature"></a>驗證簽章

JWT 包含三個區段 (以 `.` 字元分隔)。 第一個區段稱為**標頭**、第二個稱為**主體**，而第三個稱為**簽章**。 簽章區段可用來驗證權杖的真實性，您的應用程式才得以信任。

Azure AD 所簽發的權杖是使用業界標準非對稱式加密演算法（例如 RS256）來簽署。 JWT 標頭包含用來簽署權杖的金鑰和加密方法相關資訊：

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` 宣告表示用來簽署權杖的演算法，而 `kid` 宣告則表示用來驗證權杖的特定公開金鑰。

在任何指定的時間點，Azure AD 可能會使用一組特定公開-私密金鑰組的其中一個金鑰組來簽署 id_token。 Azure AD 會定期替換一組可能的金鑰，所以應將您的應用程式撰寫成自動處理這些金鑰變更。 檢查 Azure AD 所用公開金鑰的更新的合理頻率為每 24 小時。

您可以使用位於下列位置的[OpenID connect 元資料檔案](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)，取得驗證簽章所需的簽署金鑰資料：

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 在瀏覽器中嘗試此 [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration)！

此中繼資料文件：

* 是 JSON 物件，其中包含數個有用的資訊片段，例如執行 OpenID Connect 驗證所需的各種端點位置。
* 包含 `jwks_uri`，其提供用來簽署權杖的公用金鑰組位置。 位於 `jwks_uri` 的 JSON Web 金鑰（JWK）包含在該特定時間點使用的所有公開金鑰資訊。  JWK 格式如[RFC 7517](https://tools.ietf.org/html/rfc7517)中所述。  您的應用程式可以使用 JWT 標頭中的 `kid` 宣告選取本文件中已用來簽署特定權杖的公開金鑰。 然後，它可以使用正確的公開金鑰和指定的演算法來執行簽章驗證。

> [!NOTE]
> v1.0 端點會傳回 `x5t` 和 `kid` 宣告，而 v2.0 端點只會以 `kid` 宣告進行回應。 往後，建議您使用 `kid` 宣告來驗證權杖。

簽章驗證不在本檔的討論範圍內-有許多開放原始碼程式庫可用來協助您在必要時執行此動作。  不過，Microsoft 身分識別平臺具有標準-自訂簽署金鑰的一個權杖簽署延伸模組。

如果您的應用程式具有自訂簽署金鑰做為使用[宣告對應](active-directory-claims-mapping.md)功能的結果，您必須附加包含應用程式識別碼的 `appid` 查詢參數，才能取得指向應用程式簽署金鑰資訊的 `jwks_uri`，這應該用於驗證。 例如： `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 包含 `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`的 `jwks_uri`。

### <a name="claims-based-authorization"></a>以宣告為基礎的授權

您應用程式的商務邏輯會左右此步驟，以下列出一些常見的授權方法。

* 檢查 `scp` 或 `roles` 宣告，以確認所有現有的範圍都符合您的 API 所公開的範圍，並允許用戶端執行要求的動作。
* 請確定呼叫用戶端可以使用 `appid` 宣告來呼叫您的 API。
* 使用 `appidacr` 驗證呼叫用戶端的驗證狀態-如果公用用戶端不允許呼叫您的 API，則不應為0。
* 檢查過去 `nonce` 宣告的清單，以確認權杖不會重新執行。
* 檢查 `tid` 是否符合允許呼叫您 API 的租用戶。
* 使用 `acr` 宣告確認使用者已執行 MFA。 這應該使用條件式[存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)來強制執行。
* 如果您已要求存取權杖中的 `roles` 或 `groups` 宣告，請確認使用者是否位於允許執行此動作的群組中。
  * 針對使用隱含流程擷取的權杖，您可能需要查詢此資料的 [Microsoft Graph](https://developer.microsoft.com/graph/)，因為這通常會太大，而無法放入權杖。

## <a name="user-and-application-tokens"></a>使用者和應用程式權杖

您的應用程式可能會代表使用者（一般[流程）或](v2-oauth2-client-creds-grant-flow.md)直接從應用程式（透過用戶端認證流程（[v1.0、v2.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)））接收權杖。 這些應用程式專用的權杖表示此呼叫來自應用程式，而且沒有使用者的支援。 這些權杖的處理大部分都相同，但其間有些差異：

* 僅限應用程式的權杖不會有 `scp` 宣告，而可能會有 `roles` 宣告。 這是會記錄應用程式權限 (而不是委派的權限) 的位置。 如需委派和應用程式許可權的詳細資訊，請參閱許可權和[同意（v1.0](../azuread-dev/v1-permissions-consent.md) [、v2.0](v2-permissions-and-consent.md)）。
* 許多人為特定的宣告都將遺失，例如 `name` 或 `upn`。
* `sub` 和 `oid` 宣告會相同。

## <a name="token-revocation"></a>權杖撤銷

重新整理權杖可能會因為不同原因而隨時失效或撤銷。 這主要可分成兩大類：逾時和撤銷。

### <a name="token-timeouts"></a>權杖逾時

使用[權杖存留期](active-directory-configurable-token-lifetimes.md)設定，可以更改重新整理權杖的存留期。  這是正常的，而且在某些權杖不使用的情況下（例如，使用者沒有在3個月內開啟應用程式），因此會過期。  應用程式會遇到登入伺服器因為其存留期而拒絕重新整理權杖的案例。 

* MaxInactiveTime：如果在 MaxInactiveTime 規定的時間內未使用重新整理權杖，重新整理權杖將不再有效。
* MaxSessionAge：如果 MaxAgeSessionMultiFactor 或 MaxAgeSessionSingleFactor 設為其預設值 (直到撤銷為止) 以外的值，則在 MaxAgeSession* 中設定的時間經過之後，將必須重新驗證。
* 範例：
  * 租使用者的 MaxInactiveTime 為五天，而使用者在一周內開始休假，因此 Azure AD 在7天內未向使用者顯示新的權杖要求。 下次使用者要求新的權杖時，他們會發現其重新整理權杖已被撤銷，必須再次輸入其認證。
  * 敏感性應用程式的 MaxAgeSessionSingleFactor 為一天。 如果使用者在星期一登入，而且在星期二（經過25小時之後），則需要重新驗證。

### <a name="revocation"></a>撤銷

重新整理權杖可由伺服器撤銷，因為認證變更，或因為使用或系統管理動作。  重新整理權杖可分為兩個類別：發給機密用戶端（最右邊的資料行），以及發行給公用用戶端（所有其他資料行）的類別。   

|   | 密碼型 Cookie | 密碼型權杖 | 非密碼型 Cookie | 非密碼型權杖 | 機密用戶端權杖 |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| 密碼到期 | 保持運作 | 保持運作 | 保持運作 | 保持運作 | 保持運作 |
| 使用者已變更密碼 | 已撤銷 | 已撤銷 | 保持運作 | 保持運作 | 保持運作 |
| 使用者進行 SSPR | 已撤銷 | 已撤銷 | 保持運作 | 保持運作 | 保持運作 |
| 管理員重設密碼 | 已撤銷 | 已撤銷 | 保持運作 | 保持運作 | 保持運作 |
| 使用者[透過 PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) \(英文\) 撤銷他們的重新整理權杖 | 已撤銷 | 已撤銷 | 已撤銷 | 已撤銷 | 已撤銷 |
| 管理員[透過 PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) \(英文\) 撤銷租用戶的所有重新整理權杖 | 已撤銷 | 已撤銷 |已撤銷 | 已撤銷 | 已撤銷 |
| Web 上的單一登出[（v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out)、 [v2.0](v2-protocols-oidc.md#single-sign-out) ） | 已撤銷 | 保持運作 | 已撤銷 | 保持運作 | 保持運作 |

> [!NOTE]
> 「非密碼型」登入是讓使用者不需輸入密碼就能取得它的方式。 例如，使用您的臉部搭配 Windows Hello、FIDO2 金鑰或 PIN。
>
> Windows 10 上的主要重新整理權杖（PRT）會根據認證進行隔離。 例如，Windows Hello 和 password 具有各自的 PRTs，彼此隔離。 當使用者使用 Hello 認證（PIN 或生物識別）登入，然後變更密碼時，先前取得的密碼 PRT 將會撤銷。 以密碼重新登入會使舊的 PRT，並要求新的。
>
> 當重新整理權杖用來提取新的存取權杖和重新整理權杖時，不會使其失效或撤銷。  不過，您的應用程式應該在使用時捨棄舊的帳戶，並將其取代為新權杖，因為新權杖在其中有新的到期時間。 

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure AD 中的 `id_tokens`](id-tokens.md)。
* 瞭解許可權和[同意（v1.0](../azuread-dev/v1-permissions-consent.md) [、v2.0](v2-permissions-and-consent.md)）。
