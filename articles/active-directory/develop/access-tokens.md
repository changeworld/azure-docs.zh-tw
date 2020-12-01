---
title: Microsoft 身分識別平台存取權杖 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Azure AD v1.0 和 Microsoft 身分識別平台 (v2.0) 端點所發出的存取權杖。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: b1ce076befc325fef7717c0404b31dadff952af6
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433286"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft 身分識別平台存取權杖

存取權杖可讓用戶端安全地呼叫受保護的 web Api，並由 web Api 用來執行驗證和授權。 根據 OAuth 規格，存取權杖是沒有設定格式的不透明字串-某些身分識別提供者 (Idp) 使用 Guid，其他則使用加密的 blob。 Microsoft 身分識別平臺會根據接受權杖的 API 設定，使用各種不同的存取權杖格式。 開發人員在 Microsoft 身分識別平臺上[註冊的自訂 api](quickstart-configure-app-expose-web-apis.md)可以選擇兩種不同格式的 JSON Web 權杖 (jwt) （稱為 "v1" 和 "v2"），以及 microsoft 開發的 api （例如 Azure 中的 Microsoft Graph 或 api）有其他專屬的權杖格式。 這些專屬格式可能是加密的權杖、Jwt，或是不會驗證的特殊 JWT 權杖。

用戶端必須將存取權杖視為不透明字串，因為權杖的內容僅適用于 (API) 的資源。 若 *只* 進行驗證和偵錯工具，開發人員可以使用 [jwt.ms](https://jwt.ms)之類的網站來解碼 jwt。 不過請注意，您為 Microsoft API 接收的權杖可能不一定是 JWT，且您永遠不能將其解碼。

如需存取權杖內部內容的詳細資訊，用戶端應該使用隨存取權杖傳回的權杖回應資料給您的用戶端。 當您的用戶端要求存取權杖時，Microsoft 身分識別平臺也會傳回一些有關存取權杖的中繼資料，以供您應用程式的耗用量使用。 此資訊包括存取權杖的到期時間以及其有效範圍。 此資料可讓應用程式執行存取權杖的智慧型快取，而不需剖析存取權杖本身。

請參閱下列各節，以瞭解您的 API 如何驗證和使用存取權杖內的宣告。  

> [!NOTE]
> 本頁面上的所有檔（注明注明）僅適用于您已註冊之 Api 所發出的權杖。  它並不適用于 Microsoft 擁有的 Api 所發出的權杖，也不會使用這些權杖來驗證 Microsoft 身分識別平臺將如何為您建立的 API 發出權杖。  

## <a name="token-formats-and-ownership"></a>權杖格式和擁有權

### <a name="v10-and-v20"></a>v1.0 和 v2。0 

Microsoft 身分識別平臺提供兩種版本的存取權杖：1.0 版和 v2.0 版。  這些版本會管理權杖中的宣告，以確保 web API 可以控制其權杖的外觀。 在僅限 Azure AD 的應用程式，以及支援取用者帳戶的應用程式2.0 版中，Web Api 已選取其中一個作為預設值。  這可讓應用程式使用 `accessTokenAcceptedVersion` [應用程式資訊清單](reference-app-manifest.md#manifest-reference)中的設定來控制，其中 `null` 並產生 v1.0 `1` 權杖，並 `2` 產生 v2.0 權杖。

### <a name="what-app-is-a-token-for"></a>哪個應用程式是 "for" 的權杖？

存取權杖要求中牽涉到兩個合作物件：用戶端、要求權杖的用戶端，以及在呼叫 API 時接受權杖的 API) 資源 (。 `aud`權杖中的宣告表示權杖用於 (其 *物件*) 的資源。 用戶端會使用權杖，但不應該瞭解或嘗試進行剖析。 資源會接受權杖。  

Microsoft 身分識別平臺支援從任何版本端點發行任何權杖版本-它們並不相關。 這就是為什麼資源設定 `accessTokenAcceptedVersion` `2` 表示呼叫 v1.0 端點的用戶端取得該 API 的權杖將會收到 v2.0 存取權杖。  資源永遠擁有其權杖 (其宣告 `aud`) ，而且是唯一可以變更權杖詳細資料的應用程式。 這就是為什麼變更 *用戶端* 的存取權杖 [選擇性宣告](active-directory-optional-claims.md)時，不會變更要求 `user.read` Microsoft Graph 資源所擁有的權杖時所收到的存取權杖。

### <a name="sample-tokens"></a>權杖範例

v1.0 和 v2.0 權杖看起來類似，而且包含許多相同的宣告。 其各自的範例如下所示。 不過，這些範例權杖將不會 [驗證](#validating-tokens)，因為金鑰在發行前已輪替，且已從這些金鑰移除個人資訊。

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

請在 [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd) 中檢視此 v1.0 權杖。

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

請在 [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt) 中檢視此 v2.0 權杖。

## <a name="claims-in-access-tokens"></a>存取權杖中的宣告

JWT (JSON Web 權杖) 分成三個部分：

* **標頭** - 提供如何 [驗證權杖](#validating-tokens)的相關資訊，包括權杖類型和簽署方式的相關資訊。
* **承載** - 對於所有嘗試呼叫您服務的使用者或應用程式，其相關重要資料都包含在此。
* **簽章** - 用來驗證權杖的原始資料。

各部分會以句點分隔 (`.`)，並各別以 Base64 編碼。

如果有值可填入宣告時，宣告才會存在。 您的應用程式不應該相依于存在的宣告。 範例包括 `pwd_exp` (並非每個租使用者都需要密碼過期) 和 `family_name` ( [用戶端認證] (v2-oauth2-client-creds-grant-flow.md) 流程代表沒有名稱) 的應用程式。 用於驗證存取權杖的宣告一律會存在。

如果要重複使用權杖，某些宣告會用來協助 Azure AD 保護權杖。 這些宣告會在「不透明」描述中標示為不公開使用。 這些宣告可能會或可能不會出現在權杖中，而且可能會在沒有通知的情況下新增宣告。

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
| `aud` | 字串、應用程式識別碼 URI 或 GUID | 識別權杖的預定收件者-其物件。  如果值不相符，您的 API 應該驗證此值，並拒絕權杖。 在 v2.0 權杖中，這一律是 API 的用戶端識別碼，而在 v1.0 權杖中，它可以是用戶端識別碼或要求中使用的資源 URI （視用戶端要求權杖的方式而定）。|
| `iss` | 字串，STS URI | 識別建構並傳回權杖的 Security Token Service (STS)，以及在其中驗證使用者的 Azure AD 租用戶。 如果發出的權杖是 v2.0 權杖 (請參閱`ver`宣告)，URI 的結尾會是 `/v2.0`。 指出使用者是來自 Microsoft 帳戶之取用者使用者的 GUID 是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 您的應用程式可以使用宣告的 GUID 部分來限制可登入應用程式的租使用者集合（如果有的話）。 |
|`idp`| 字串，通常是 STS URI | 記錄驗證權杖主體的身分識別提供者。 除非使用者帳戶與簽發者不在同一租用戶中，否則此值與簽發者宣告的值相同。 如果宣告不存在，這代表可改用 `iss` 的值。  針對在組織內容中使用的個人帳戶 (例如獲邀使用 Azure AD 租用戶的個人帳戶)，`idp` 宣告可能會是 'live.com' 或包含 Microsoft 帳戶租用戶 `9188040d-6c67-4c5b-b112-36a304b66dad` 的 STS URI。 |
| `iat` | 整數，UNIX 時間戳記 | 「發出時間 (Issued At)」表示此權杖進行驗證的時間。 |
| `nbf` | 整數，UNIX 時間戳記 | "nbf" (生效時間) 宣告會識別生效時間，在此時間之前不可接受 JWT 以進行處理。 |
| `exp` | 整數，UNIX 時間戳記 | "exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不可接受 JWT 以進行處理。 請務必注意，資源也可能在此時間之前拒絕權杖，例如，需要變更驗證或偵測到權杖已撤銷時。 |
| `aio` | 不透明字串 | Azure AD 用來記錄資料的內部宣告，以便重複使用權杖。 資源不應該使用此宣告。 |
| `acr` | 字串，"0" 或 "1" | 只存在於 v1.0 權杖中。 「驗證內容類別」宣告。 值為 "0" 表示使用者驗證不符合 ISO/IEC 29115 的需求。 |
| `amr` | 字串的 JSON 陣列 | 只存在於 v1.0 權杖中。 識別如何驗證權杖的主體。 請參閱 [amr 宣告](#the-amr-claim)一節，以瞭解更多詳細資訊。 |
| `appid` | 字串，GUID | 只存在於 v1.0 權杖中。 使用權杖的用戶端應用程式識別碼。 應用程式代表本身或使用者行事。 應用程式識別碼通常代表應用程式物件，但也可以代表 Azure AD 中的服務主體物件。 |
| `azp` | 字串，GUID | 僅存在於 v2.0 權杖中，為 `appid` 的取代項目。 使用權杖的用戶端應用程式識別碼。 應用程式代表本身或使用者行事。 應用程式識別碼通常代表應用程式物件，但也可以代表 Azure AD 中的服務主體物件。 |
| `appidacr` | "0"、"1" 或 "2" | 只存在於 v1.0 權杖中。 指出如何驗證用戶端。 若為公用用戶端，此值為 "0"。 如果使用用戶端識別碼和用戶端密碼，此值為 "1"。 如果已使用用戶端憑證進行驗證，則值為 "2"。 |
| `azpacr` | "0"、"1" 或 "2" | 僅存在於 v2.0 權杖中，為 `appidacr` 的取代項目。 指出如何驗證用戶端。 若為公用用戶端，此值為 "0"。 如果使用用戶端識別碼和用戶端密碼，此值為 "1"。 如果已使用用戶端憑證進行驗證，則值為 "2"。 |
| `preferred_username` | String | 代表使用者的主要使用者名稱。 它可以是電子郵件地址、電話號碼或未指定格式的一般使用者名稱。 其值是可變動的，並且可能隨著時間改變。 因為此值會變動，請勿用在授權決策。  但是，它可用於使用者名稱提示，並在人類可讀取的 UI 中做為使用者名稱。 需要 `profile` 範圍才能接收此宣告。 只出現在 v2.0 權杖中。 |
| `name` | String | 提供人類看得懂的值，用以識別權杖的主體。 此值不保證是唯一值，它是可變動的，並且在設計上僅用於顯示。 需要 `profile` 範圍才能接收此宣告。 |
| `scp` | 字串，範圍的空格分隔清單 | 由您應用程式公開的範圍集合，用戶端應用程式已針對此集合要求 (和接收) 同意。 您的應用程式應確認這些範圍是由應用程式公開的有效範圍，並根據這些範圍的值做出授權決策。 僅包含於[使用者權杖](#user-and-application-tokens)中。 |
| `roles` | 字串陣列、權限清單 | 由您應用程式公開的權限集合，而要求應用程式或使用者已獲得用於呼叫的權限。 若為[應用程式權杖](#user-and-application-tokens)，這會在用戶端認證流程 ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)、[v2.0](v2-oauth2-client-creds-grant-flow.md)) 期間用來取代使用者範圍。  若為[使用者權杖](#user-and-application-tokens)，這會填入目標應用程式上指派給使用者的角色。 |
| `wids` | [RoleTemplateID](../roles/permissions-reference.md#role-template-ids) GUID 的陣列 | 代表從[系統管理員角色頁面](../roles/permissions-reference.md#role-template-ids)中的角色區段，指派給此使用者的全租用戶角色。  此宣告會透過[應用程式資訊清單](reference-app-manifest.md)的 `groupMembershipClaims` 屬性，針對每個應用程式進行設定。  必須將其設定為 "All" 或 "DirectoryRole"。  可能不會出現在因權杖長度考量而透過隱含流程取得的權杖中。 |
| `groups` | GUID 的 JSON 陣列 | 提供代表主體群組成員資格的物件識別碼。 這些值都是唯一的 (請參閱「物件識別碼」)，而且可安全地用來管理存取權，例如強制授權以存取資源。 群組宣告中包含的群組會透過[應用程式資訊清單](reference-app-manifest.md)的 `groupMembershipClaims` 屬性，針對每個應用程式進行設定。 Null 值會排除所有群組，"SecurityGroup" 的值只會包含 Active Directory 的安全性群組成員資格，而 "All" 值會包含安全性群組和 Microsoft 365 通訊群組清單。 <br><br>請參閱下面的 `hasgroups` 宣告，以取得使用 `groups` 宣告搭配隱含授與的詳細資訊。 <br>針對其他流程，如果使用者所屬的群組數目超過限制 (SAML 為 150，JWT 為 200)，則會將超額宣告新增至指向 Microsoft Graph 端點 (包含使用者群組清單) 的宣告來源。 |
| `hasgroups` | Boolean | 如果有的話，一律為 `true`，表示使用者在至少一個群組中。 如果完整 groups 宣告會將 URI 片段延伸超出 URL 長度限制 (目前為 6 個或更多群組)，則使用於取代隱含授與流程中 JWT 的 `groups` 宣告。 表示用戶端應該使用 Microsoft Graph API 來判斷使用者的群組 (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`)。 |
| `groups:src1` | JSON 物件 | 若為沒有長度限制 (請參閱上面的 `hasgroups`) 但是對權杖而言仍然太大的權杖要求，則會包含使用者的完整 groups 清單連結。 在 JWT 中以分散式宣告形式取代 `groups` 宣告，在 SAML 中則以新宣告形式取代。 <br><br>**範例 JWT 值**： <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | String | 權杖判斷提示其相關資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 它可用來安全地執行授權檢查 (例如當權杖用於存取資源時)，並可做為資料庫資料表中的索引鍵。 由於主體一律是存在於 Azure AD 所簽發的權杖中，因此建議您在一般用途的授權系統中使用此值。 不過，主體是成對識別碼，對於特定應用程式識別碼來說，主體是唯一的。 因此，如果單一使用者使用兩個不同的用戶端識別碼登入兩個不同的應用程式，這些應用程式會收到兩個不同的主體宣告值。 視您的架構和隱私權需求而定，這不一定是您想要的。 另請參閱 `oid` 宣告 (這確實會在租用戶內的應用程式中保持不變)。 |
| `oid` | 字串，GUID | 物件在 Microsoft 身分識別平台中的不可變識別碼，在此案例為使用者帳戶。 它也可用來安全地執行授權檢查，以及做為資料庫資料表中的索引鍵。 此識別碼可跨應用程式唯一識別使用者，同一位使用者登入兩個不同的應用程式會在 `oid` 宣告中收到相同的值。 因此，在對 Microsoft 線上服務 (例如 Microsoft Graph) 進行查詢時可使用 `oid`。 Microsoft Graph 會傳回這個識別碼作為指定[使用者帳戶](/graph/api/resources/user)的 `id` 屬性。 因為 `oid` 可讓多個應用程式相互關聯使用者，因此需要 `profile` 範圍才能接收此宣告。 請注意，如果單一使用者存在於多個租用戶，使用者將會在每個租用戶中包含不同的物件識別碼，它們會被視為不同帳戶，即使使用者使用相同認證來登入各個帳戶也是如此。 |
| `tid` | 字串，GUID | 代表使用者是來自哪個 Azure AD 租用戶。 就工作和學校帳戶而言，GUID 是使用者所屬組織的不可變租用戶識別碼。 就個人帳戶而言，此值會是 `9188040d-6c67-4c5b-b112-36a304b66dad`。 需要 `profile` 範圍才能接收此宣告。 |
| `unique_name` | String | 只存在於 v1.0 權杖中。 提供人類看得懂的值，用以識別權杖的主體。 此值不保證是租用戶中的唯一值，而且應僅用於顯示目的。 |
| `uti` | 不透明字串 | Azure 用來重新驗證權杖的內部宣告。 資源不應該使用此宣告。 |
| `rh` | 不透明字串 | Azure 用來重新驗證權杖的內部宣告。 資源不應該使用此宣告。 |
| `ver` | 字串，`1.0` 或 `2.0` | 表示存取權杖的版本。 |

**群組超額宣告**

為確保權杖大小不會超過 HTTP 標頭大小限制，Azure AD 會限制在群組宣告中包含的物件識別碼數目。 如果使用者是多個群組的成員，而這些群組的成員數目超過 (150 的 SAML 權杖、200用於 JWT 權杖，而且只有6（如果透過隱含流程) 發出，則 Azure AD 不會在權杖中發出群組宣告。 相反地，其會在權杖中包含超額宣告，以指示應用程式查詢 Microsoft Graph API 來取得使用者的群組成員資格。

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

您可使用[應用程式建立指令碼](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts)資料夾中提供的 `BulkCreateGroups.ps1` 來協助測試超額案例。

#### <a name="v10-basic-claims"></a>v1.0 基本宣告

下列宣告將會包含在 v1.0 權杖中 (如果有的話)，但在 v2.0 權杖中，預設為不包含這些宣告。 如果您使用 v2.0 且需要這些宣告的其中一個，則可使用[選擇性宣告](active-directory-optional-claims.md)來要求這些宣告。

| 宣告 | [格式] | 描述 |
|-----|--------|-------------|
| `ipaddr`| String | 從中進行使用者驗證的 IP 位址。 |
| `onprem_sid`| 字串，採 [SID 格式](/windows/desktop/SecAuthZ/sid-components) | 如果使用者具有內部部署驗證，此宣告會提供其 SID。 您可以使用 `onprem_sid` 取得繼承應用程式中的授權。|
| `pwd_exp`| 整數，UNIX 時間戳記 | 表示使用者密碼到期的時間。 |
| `pwd_url`| String | 可傳送給使用者以重設其密碼的 URL。 |
| `in_corp`| boolean | 指出用戶端是否是從公司網路登入的。 如果不是，則不包含宣告。 |
| `nickname`| String | 使用者的額外名稱 (有別於名字或姓氏)。|
| `family_name` | String | 提供使用者的姓氏 (如使用者物件中所定義)。 |
| `given_name` | String | 提供使用者的名字 (如使用者物件上所設定)。 |
| `upn` | String | 使用者的使用者名稱。 可能是電話號碼、電子郵件地址或未格式化的字串。 應只用於顯示用途，並在重新驗證情節中提供使用者名稱提示。 |

#### <a name="the-amr-claim"></a>`amr` 宣告

Microsoft 身分識別可透過不同的方式來驗證，這些方式可能與應用程式相關。 `amr` 宣告為陣列，其中可包含多個項目，例如 `["mfa", "rsa", "pwd"]`，適用於同時使用密碼和驗證器應用程式的驗證。

| 值 | 描述 |
|-----|-------------|
| `pwd` | 密碼驗證，可以是使用者的 Microsoft 密碼或應用程式的用戶端祕密。 |
| `rsa` | 驗證會以 RSA 金鑰證明為基礎，例如使用 [Microsoft Authenticator 應用程式](https://aka.ms/AA2kvvu)。 如果驗證是由自我簽署 JWT 搭配擁有 X509 憑證的服務來執行，則會包括此值。 |
| `otp` | 使用電子郵件或簡訊的單次密碼。 |
| `fed` | 已使用同盟驗證判斷提示 (例如 JWT 或 SAML)。 |
| `wia` | Windows 整合式驗證 |
| `mfa` | 已使用[多重要素驗證](../authentication/concept-mfa-howitworks.md)。 此值存在時，其他驗證方法也會包含在內。 |
| `ngcmfa` | 相當於`mfa`，用來佈建的某些進階的認證類型。 |
| `wiaormfa`| 使用者已使用 Windows 或 MFA 認證來進行驗證。 |
| `none` | 未執行任何驗證。 |

## <a name="validating-tokens"></a>驗證權杖

並非所有應用程式都應該驗證權杖。 只有在特定情況下，應用程式才會驗證權杖：

* [Web api](quickstart-configure-app-expose-web-apis.md) 必須驗證用戶端傳送給這些權杖的存取權杖。  它們必須只接受包含其宣告的權杖 `aud` 。
* 機密 web 應用程式（例如 ASP.NET Core）必須先驗證透過混合式流程中使用者的瀏覽器傳送給他們的識別碼權杖，然後才允許存取使用者的資料或建立會話。

如果上述狀況都不適用，您的應用程式就無法驗證權杖，而且可能會在根據權杖的有效性進行決策時提供安全性和可靠性風險。  公用用戶端（例如原生應用程式或 Spa）不會受益于驗證權杖-應用程式會直接與 IDP 通訊，因此 SSL 保護可確保權杖有效。

 Api 和 web 應用程式只能驗證具有 `aud` 符合其應用程式之宣告的權杖; 其他資源可能會有自訂權杖驗證規則。 例如，Microsoft Graph 的權杖將不會根據這些規則的專屬格式進行驗證。 驗證和接受適用于另一個資源的權杖是一個 [混淆的 deputy](https://cwe.mitre.org/data/definitions/441.html) 問題範例。

如果您的應用程式需要根據上述方式驗證 id_token 或 access_token，您的應用程式應該先驗證權杖的簽章和簽發者是否符合 OpenID 探索檔中的值。 例如，租用戶獨立版的文件位於 [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)。

下列資訊是針對想要瞭解基礎程式的人員所提供。 Azure AD 中介軟體已內建驗證存取權杖的功能，您可以瀏覽我們的[範例](sample-v2-code.md)，以找到您所選擇語言的範例。 另外還有數個協力廠商開放原始碼程式庫可用於 JWT 驗證-幾乎每個平臺和語言都有至少一個選項。 如需 Azure AD 驗證程式庫和程式碼範例的詳細資訊，請參閱 [驗證程式庫](reference-v2-libraries.md)。

### <a name="validating-the-signature"></a>驗證簽章

JWT 包含三個區段 (以 `.` 字元分隔)。 第一個區段稱為 **標頭**、第二個稱為 **主體**，而第三個稱為 **簽章**。 簽章區段可用來驗證權杖的真實性，您的應用程式才得以信任。

Azure AD 所簽發的權杖是使用業界標準非對稱式加密演算法 (例如 RS256) 進行簽署。 JWT 標頭包含用來簽署權杖的金鑰和加密方法相關資訊：

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` 宣告指示用來簽署權杖的演算法，而 `kid` 宣告則指示用來驗證權杖的特定公開金鑰。

在任何指定的時間點，Azure AD 可能會使用一組特定公開-私密金鑰組的其中一個金鑰組來簽署 id_token。 Azure AD 會定期替換一組可能的金鑰，所以應將您的應用程式撰寫成自動處理這些金鑰變更。 檢查 Azure AD 所用公開金鑰的更新的合理頻率為每 24 小時。

您可使用位於下列位置的 [OpenID Connect 中繼資料文件](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)來取得驗證簽章所需簽署金鑰資料：

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 在瀏覽器中嘗試此 [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration)！

此中繼資料文件：

* 為 JSON 物件，內含幾項實用的資訊，例如執行 OpenID Connect 驗證所需的各種端點位置。
* 包含 `jwks_uri`，其提供用來簽署權杖的公用金鑰組位置。 位於 `jwks_uri` 的 JSON Web 金鑰 (JWK) 包含在該特定時間點使用的所有公開金鑰資訊。  [RFC 7517](https://tools.ietf.org/html/rfc7517) 中有 JWK 格式的相關說明。  您的應用程式可以使用 JWT 標頭中的 `kid` 宣告選取本文件中已用來簽署特定權杖的公開金鑰。 接著可使用正確的公開金鑰和所指定演算法來執行簽章驗證。

> [!NOTE]
> 我們建議使用 `kid` 宣告來驗證您的權杖。 雖然 v1.0 權杖同時包含 `x5t` 和 `kid` 宣告，但 v2.0 權杖只包含宣告 `kid` 。

執行簽章驗證已超出本文件的範圍，有許多開放原始碼程式庫可協助這麼做 (如有必要)。  不過，Microsoft 身分識別平臺對標準自訂簽署金鑰有一個權杖簽署延伸模組。

如果應用程式因使用 [claims-mapping](active-directory-claims-mapping.md) 功能而具有自訂簽署金鑰，則必須附加包含應用程式識別碼的 `appid` 查詢參數，以取得指向應用程式簽署金鑰資訊 (這應該用於驗證) 的 `jwks_uri`。 例如：`https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 包含內容為 `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 的 `jwks_uri`。

### <a name="claims-based-authorization"></a>以宣告為基礎的授權

您應用程式的商務邏輯會左右此步驟，以下列出一些常見的授權方法。

* 檢查 `scp` 或 `roles` 宣告，以確認所有出現範圍符合 API 公開的範圍，並允許用戶端執行要求的動作。
* 請確定呼叫用戶端可以使用 `appid` 宣告來呼叫您的 API。
* 使用 `appidacr` 來確認呼叫用戶端的驗證狀態；如果公用用戶端不允許呼叫 API，則這不應是 0。
* 檢查過去的 `nonce` 宣告清單，以確認權杖不會重新執行。
* 檢查 `tid` 是否符合允許呼叫您 API 的租用戶。
* 使用 `acr` 宣告確認使用者已執行 MFA。 這應該使用[條件式存取](../conditional-access/overview.md)來強制執行。
* 如果您已在存取權杖中要求 `roles` 或 `groups` 宣告，請確認使用者位在允許執行此動作的群組中。
  * 針對使用隱含流程擷取的權杖，您可能需要查詢此資料的 [Microsoft Graph](https://developer.microsoft.com/graph/)，因為這通常會太大，而無法放入權杖。

## <a name="user-and-application-tokens"></a>使用者和應用程式權杖

您的應用程式可能會接收使用者的權杖， (通常會) 或直接從應用程式 (透過 [用戶端認證流程](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)) 討論的流程。 這些應用程式專用的權杖表示此呼叫來自應用程式，而且沒有使用者的支援。 這些權杖的處理方式大多相同：

* 使用 `roles` 以查看已授與權杖主體的許可權。
* 使用 `oid` 或 `sub` 驗證呼叫的服務主體是否為預期的服務主體。

如果您的應用程式需要區分僅限應用程式存取權杖和使用者的存取權杖，請使用 `idtyp` [選擇性](active-directory-optional-claims.md)宣告。  藉由將宣告新增 `idtyp` 至 `accessToken` 欄位，並檢查值 `app` ，您可以偵測僅限應用程式的存取權杖。  使用者的識別碼權杖和存取權杖將不會包含此宣告 `idtyp` 。

## <a name="token-revocation"></a>權杖撤銷

重新整理權杖可能會因為不同原因而隨時失效或撤銷。 這主要可分成兩大類：逾時和撤銷。

### <a name="token-timeouts"></a>權杖逾時

使用[權杖存留期設定](active-directory-configurable-token-lifetimes.md)，可改變重新整理權杖的存留期。  正常情況下，不會使用某些權杖 (例如，使用者未在 3 個月內開啟應用程式)，因此這些權杖會過期。  應用程式將會遇到登入伺服器因其存留期而拒絕重新整理權杖的狀況。

* MaxInactiveTime：如果未在 MaxInactiveTime 所指定時間內使用重新整理權杖，重新整理權杖將不再有效。
* MaxSessionAge：如果 MaxAgeSessionMultiFactor 或 MaxAgeSessionSingleFactor 設為其預設值 (直到撤銷為止) 以外的值，則在 MaxAgeSession* 中設定的時間經過之後，將必須重新驗證。
* 範例：
  * 租用戶的 MaxInactiveTime 為五天，且使用者去渡假一週，因此 Azure AD 已有 7 天未看見來自該使用者的新權杖要求。 下次使用者要求新的權杖時，將會發現其重新整理權杖已撤銷，且必須重新輸入其認證。
  * 某個敏感性應用程式將 MaxAgeSessionSingleFactor 設為一天。 如果使用者在星期一登入，則到星期二 (經過 25 小時之後)，其將需要重新驗證。

### <a name="revocation"></a>撤銷

伺服器可能因認證變更，或因使用或系統管理動作而撤銷重新整理權杖。  重新整理權杖可分為兩個類別：簽發給機密用戶端的類別 (最右側的資料行)，以及簽發給公用用戶端的類別 (所有其他資料行)。

| 變更 | 密碼型 Cookie | 密碼型權杖 | 非密碼型 Cookie | 非密碼型權杖 | 機密用戶端權杖 |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| 密碼到期 | 保持運作 | 保持運作 | 保持運作 | 保持運作 | 保持運作 |
| 使用者已變更密碼 | 已撤銷 | 已撤銷 | 保持運作 | 保持運作 | 保持運作 |
| 使用者進行 SSPR | 已撤銷 | 已撤銷 | 保持運作 | 保持運作 | 保持運作 |
| 管理員重設密碼 | 已撤銷 | 已撤銷 | 保持運作 | 保持運作 | 保持運作 |
| 使用者[透過 PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) \(英文\) 撤銷他們的重新整理權杖 | 已撤銷 | 已撤銷 | 已撤銷 | 已撤銷 | 已撤銷 |
| 系統管理員[透過 PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) 撤銷使用者的所有重新整理權杖 | 已撤銷 | 已撤銷 |已撤銷 | 已撤銷 | 已撤銷 |
| Web 上的單一登出 ([v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out)、[v2.0](v2-protocols-oidc.md#single-sign-out)) | 已撤銷 | 保持運作 | 已撤銷 | 保持運作 | 保持運作 |

#### <a name="non-password-based"></a>非密碼型

以 *非密碼為基礎* 的登入是指使用者未輸入密碼來取得密碼的登入。 以非密碼為基礎的登入範例包括：

- 使用您的臉部搭配 Windows Hello
- FIDO2 索引鍵
- sms
- 語音
- PIN 碼

> [!NOTE]
> Windows 10 上的主要重新整理權杖 (PRT) 會根據認證進行區隔。 例如，Windows Hello 和密碼具有各自的 PRT，彼此隔離。 當使用者使用 Hello 認證 (PIN 或生物特徵辨識) 登入，然後變更密碼時，將會撤銷先前取得的密碼型 PRT。 以密碼重新登入會使舊的 PRT 失效，並要求新的 PRT。
>
> 使用重新整理權杖來擷取新的存取權杖和重新整理權杖時，不會使該權杖失效或撤銷。  不過，應用程式應該在使用舊權杖後立即加以捨棄，並將其取代為新權杖，因為新權杖中有新的到期時間。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure AD 中的 `id_tokens`](id-tokens.md)。
* 了解權限與同意 ([v1.0](../azuread-dev/v1-permissions-consent.md)、[v2.0](v2-permissions-and-consent.md))。
