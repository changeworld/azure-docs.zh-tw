---
title: OAuth 授權碼流程-Microsoft 身分識別平臺 |Azure
description: 使用 OAuth 2.0 驗證通訊協定的 Microsoft 身分識別平臺執行來建立 web 應用程式。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 367f6253f228e963edb7b178f8b25da259a5e2c7
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700561"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft 身分識別平臺和 OAuth 2.0 授權碼流程

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

OAuth 2.0 授權碼授與可用於裝置上所安裝的應用程式中，以存取受保護的資源，例如 Web API。 使用 OAuth 2.0 的 Microsoft 身分識別平臺執行，您可以將登入及 API 存取新增至您的行動和桌面應用程式。 本指南不限於特定語言，其中說明如何在不使用任何 [Azure 開放原始碼驗證程式庫](reference-v2-libraries.md)的情況下，傳送和接收 HTTP 訊息。

本文說明如何在您的應用程式中直接針對通訊協定進行程式設計。  可能的話，建議您改用支援的 Microsoft 驗證程式庫（MSAL）來[取得權杖，並呼叫受保護的 Web api](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另請參閱[使用 MSAL 的範例應用程式](sample-v2-code.md)。

> [!NOTE]
> 並非所有 Azure Active Directory 案例都是由 Microsoft 身分識別平臺端點支援 & 功能。 若要判斷您是否應該使用 Microsoft 身分識別平臺端點，請參閱[microsoft 身分識別平臺限制](active-directory-v2-limitations.md)。

如需 OAuth 2.0 授權碼流程的說明，請參閱 [OAuth 2.0 規格的 4.1 節](https://tools.ietf.org/html/rfc6749)。 它是用來在大部分的應用程式類型（包括[web 應用程式](v2-app-types.md#web-apps)和原生[安裝的應用程式](v2-app-types.md#mobile-and-native-apps)）中執行驗證和授權。 此流程可讓應用程式安全地取得 access_tokens，以用來存取由 Microsoft 身分識別平臺端點保護的資源。

## <a name="protocol-diagram"></a>通訊協定圖表

概括而言，原生/行動應用程式的整個驗證流程看起來像是這樣：

![OAuth 授權碼流程](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>要求授權碼

授權碼流程始於用戶端將使用者導向 `/authorize` 端點。 在這項要求中，用戶端會指出必須向使用者索取的權限：

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345
```

> [!TIP]
> 按一下下面的連結以執行此要求！ 登入之後，您的瀏覽器應重新導向至在位址列中有 `code` 的 `https://localhost/myapp/`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 參數    | 必要/選用 | 說明 |
|--------------|-------------|--------------|
| `tenant`    | required    | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。  |
| `client_id`   | required    | **應用程式（用戶端）識別碼**， [Azure 入口網站](https://go.microsoft.com/fwlink/?linkid=2083908)指派給您應用程式的應用程式註冊體驗。  |
| `response_type` | required    | 授權碼流程必須包含 `code`。       |
| `redirect_uri`  | required | 應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。 對於原生和行動應用程式，請使用 `https://login.microsoftonline.com/common/oauth2/nativeclient` 的預設值。   |
| `scope`  | required    | 您要使用者同意的 [範圍](v2-permissions-and-consent.md) 空格分隔清單。  在要求的 `/authorize` 階段中，這可以涵蓋多項資源，讓您的應用程式取得您想要呼叫之多個 web Api 的同意。 |
| `response_mode`   | 建議使用 | 指定將產生的權杖送回到應用程式所應該使用的方法。 可以是下列其中一項：<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` 會提供程式碼，以作為重新導向 URI 的查詢字串參數。 如果您要使用隱含流程來要求識別碼權杖，就無法使用[OpenID 規格](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)中指定的 `query`。如果您只是要求程式碼，您可以使用 `query`、`fragment`或 `form_post`。 `form_post` 會執行 POST，其中包含您重新導向 URI 的程式碼。 如需詳細資訊，請參閱 [OpenID Connect 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)。  |
| `state`                 | 建議使用 | 同樣會隨權杖回應傳回之要求中所包含的值。 其可以是您想要之任何內容的字串。 隨機產生的唯一值通常用於 [防止跨站台要求偽造攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此值也可以將驗證要求發生前使用者在應用程式中的狀態相關資訊 (例如他們所在的網頁或檢視) 編碼。 |
| `prompt`  | 選用    | 表示需要的使用者互動類型。 此時唯有 `login`、`none` 及 `consent` 是有效值。<br/><br/>- `prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。<br/>- `prompt=none` 相反，它會確保使用者不會看到任何互動式提示。 如果要求無法透過單一登入以無訊息方式完成，Microsoft 身分識別平臺端點會傳回 `interaction_required` 錯誤。<br/>- `prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| `login_hint`  | 選用    | 如果您事先知道其使用者名稱，可用來預先填入使用者登入頁面的使用者名稱/電子郵件地址欄位。 通常應用程式會在重新驗證期間使用此參數，已經使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。   |
| `domain_hint`  | 選用    | 可以是 `consumers` 或 `organizations` 其中一個。<br/><br/>如果包含，它會略過使用者在登入頁面上經歷的以電子郵件為基礎的探索程式，以更有效率的方式提供更流暢的使用者體驗。 通常應用程式會在重新驗證 (擷取上一次登入的 `tid` ) 期間使用此參數。 如果 `tid` 宣告值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，您應該使用 `domain_hint=consumers`。 否則，使用 `domain_hint=organizations`。  |
| `code_challenge_method` | 選用    | 用來為 `code_challenge` 參數編碼 `code_verifier` 的方法。 可以是下列其中一個值：<br/><br/>- `plain` <br/>- `S256`<br/><br/>如果排除，則當包含 `code_challenge` 時，會假設 `code_challenge` 是純文字。 Microsoft 身分識別平臺同時支援 `plain` 和 `S256`。 如需詳細資訊，請參閱 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 |
| `code_challenge`  | 選用 | 用來透過來自原生用戶端的「代碼交換的證明金鑰」(PKCE) 保護授權碼授與。 如果包含 `code_challenge_method`，則為必要參數。 如需詳細資訊，請參閱 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 |

此時，會要求使用者輸入其認證並完成驗證。 Microsoft 身分識別平臺端點也會確保使用者已同意 `scope` 查詢參數中指出的許可權。 如果使用者未曾同意這些權限的任何一項，就會要求使用者同意要求的權限。 [這裡提供權限、同意與多租用戶應用程式](v2-permissions-and-consent.md)的詳細資料。

一旦使用者驗證並授與同意，Microsoft 身分識別平臺端點就會使用 `response_mode` 參數中指定的方法，在指定的 `redirect_uri`傳回您應用程式的回應。

#### <a name="successful-response"></a>成功回應

使用 `response_mode=query` 的成功回應如下所示：

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 參數 | 說明  |
|-----------|--------------|
| `code` | 應用程式要求的 authorization_code。 應用程式可以使用授權碼要求目標資源的存取權杖。 Authorization_codes 是短期的，通常會在大約10分鐘後到期。 |
| `state` | 如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理：

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 說明  |
|----------|------------------|
| `error`  | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>授權端點錯誤的錯誤碼

下表說明各種可能在錯誤回應的 `error` 參數中傳回的錯誤碼。

| 錯誤碼  | 說明    | 用戶端動作   |
|-------------|----------------|-----------------|
| `invalid_request` | 通訊協定錯誤，例如遺漏必要的參數。 | 修正並重新提交要求。 這是通常會在初始測試期間擷取到的開發錯誤。 |
| `unauthorized_client` | 不允許用戶端應用程式要求授權碼。 | 當用戶端應用程式未在 Azure AD 中註冊，或未新增至使用者的 Azure AD 租使用者時，通常會發生此錯誤。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| `access_denied`  | 資源擁有者拒絕同意  | 用戶端應用程式可以通知使用者，除非使用者同意，否則無法繼續。 |
| `unsupported_response_type` | 授權伺服器不支援要求中的回應類型。 | 修正並重新提交要求。 這是通常會在初始測試期間擷取到的開發錯誤。  |
| `server_error`  | 伺服器發生非預期的錯誤。| 重試要求。 這些錯誤可能是由暫時性狀況所引起。 用戶端應用程式可能會向使用者解釋其回應因暫時性錯誤而延遲。 |
| `temporarily_unavailable`   | 伺服器暫時過於忙碌而無法處理要求。 | 重試要求。 用戶端應用程式可能會向使用者解釋，其回應因為暫時性狀況而延遲。 |
| `invalid_resource`  | 目標資源無效，因為它不存在、Azure AD 找不到，或未正確設定。 | 此錯誤表示尚未在租用戶中設定資源 (如果存在)。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| `login_required` | 找到太多使用者或找不到使用者 | 用戶端已要求無訊息驗證 (`prompt=none`)，但找不到單一使用者。 這可能表示工作階段中有多個作用中使用者，或沒有任何使用者。 這會將選擇的租使用者納入考慮（例如，如果有兩個 Azure AD 帳戶使用中，而一個 Microsoft 帳戶，而且選擇 `consumers`，無訊息驗證就會生效）。 |
| `interaction_required` | 要求需要使用者互動。 | 必須進行其他驗證步驟或同意。 請在不使用 `prompt=none` 的情況下重試要求。 |

## <a name="request-an-access-token"></a>要求存取權杖

取得 authorization_code 並獲得使用者授權之後，現在即可兌換所需資源之 `access_token` 的 `code`。 做法是將 `POST` 要求傳送給 `/token` 端點：

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> 嘗試在 Postman 中執行這項要求！ （別忘了取代 `code`）[![嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| 參數  | 必要/選用 | 說明     |
|------------|-------------------|----------------|
| `tenant`   | required   | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。  |
| `client_id` | required  | Azure 入口網站的應用程式（用戶端）識別碼，[應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面指派給您的應用程式。 |
| `grant_type` | required   | 必須是授權碼流程的 `authorization_code` 。   |
| `scope`      | required   | 範圍的空格分隔清單。 在此階段中要求的範圍必須相當於或為第一個階段中所要求的範圍子集。 範圍必須全部來自單一資源，以及 OIDC 範圍（`profile`、`openid`、`email`）。 如需範圍的詳盡說明，請參閱 [權限、同意和範圍](v2-permissions-and-consent.md)。 |
| `code`          | required  | 您在流程的第一個階段中取得的 authorization_code。 |
| `redirect_uri`  | required  | 用來取得 authorization_code 的相同 redirect_uri 值。 |
| `client_secret` | Web Apps 所需 | 您在應用程式註冊入口網站中為應用程式建立的應用程式密碼。 您不應該在原生應用程式中使用應用程式密碼，因為 client_secrets 無法可靠地儲存在裝置上。 這是 web 應用程式和 web Api 的必要功能，能夠將 client_secret 安全地儲存在伺服器端。  用戶端密碼必須在傳送之前先進行 URL 編碼。  |
| `code_verifier` | 選用  | 用來取得 authorization_code 的相同 code_verifier。 如果在授權碼授與要求中已使用 PKCE，則為必要參數。 如需詳細資訊，請參閱 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 |

### <a name="successful-response"></a>成功回應

成功的權杖回應如下：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 參數     | 說明   |
|---------------|------------------------------|
| `access_token`  | 所要求的存取權杖。 應用程式可以使用這個權杖驗證受保護的資源，例如 Web API。  |
| `token_type`    | 表示權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 |
| `expires_in`    | 存取權杖的有效期 (以秒為單位)。 |
| `scope`         | access_token 有效的範圍。 |
| `refresh_token` | OAuth 2.0 重新整理權杖。 應用程式可以使用這個權杖，在目前的存取權杖過期之後，取得其他的存取權杖。 Refresh_token 的有效期很長，而且可以用來延長保留資源存取權的時間。 如需有關重新整理存取權杖的詳細資訊，請參閱[下一節](#refresh-the-access-token)。 <br> **注意：** 只在要求 `offline_access` 範圍時提供。 |
| `id_token`      | JSON Web 權杖 (JWT)。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取並顯示值，但不應依賴這些值來取得任何授權或安全性界限。 如需有關 id_token 的詳細資訊，請參閱 [`id_token reference`](id-tokens.md)。 <br> **注意：** 只在要求 `openid` 範圍時提供。 |

### <a name="error-response"></a>錯誤回應

錯誤回應格式如下：

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 參數         | 說明    |
|-------------------|----------------|
| `error`       | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |
| `error_codes` | 有助於診斷的 STS 特定錯誤碼清單。  |
| `timestamp`   | 發生錯誤的時間。 |
| `trace_id`    | 有助於診斷的要求唯一識別碼。 |
| `correlation_id` | 有助於跨元件診斷的要求唯一識別碼。 |

### <a name="error-codes-for-token-endpoint-errors"></a>權杖端點錯誤的錯誤碼

| 錯誤碼         | 說明        | 用戶端動作    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 通訊協定錯誤，例如遺漏必要的參數。 | 修正並重新提交要求   |
| `invalid_grant`    | 授權碼或 PKCE 代碼驗證器無效或已過期。 | 嘗試向 `/authorize` 端點提出新的要求，並確認 code_verifier 參數正確。  |
| `unauthorized_client` | 已驗證的用戶端未獲授權，無法使用此授權授與類型。 | 當用戶端應用程式未在 Azure AD 中註冊，或未新增至使用者的 Azure AD 租使用者時，通常就會發生這種情況。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| `invalid_client` | 用戶端驗證失敗。  | 用戶端認證無效。 若要修正，應用程式系統管理員必須更新認證。   |
| `unsupported_grant_type` | 授權伺服器不支援授權授與類型。 | 變更要求中的授與類型。 這種類型的錯誤應該只會在開發期間發生，並且會在初始測試期間偵測出來。 |
| `invalid_resource` | 目標資源無效，因為它不存在、Azure AD 找不到，或未正確設定。 | 這表示尚未在租用戶中設定資源 (如果存在)。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。  |
| `interaction_required` | 要求需要使用者互動。 例如，必須進行其他驗證步驟。 | 以相同資源重試要求。  |
| `temporarily_unavailable` | 伺服器暫時過於忙碌而無法處理要求。 | 重試要求。 用戶端應用程式可能會向使用者解釋，其回應因為暫時性狀況而延遲。 |

## <a name="use-the-access-token"></a>使用存取權杖

既然您已經成功取得 `access_token`，您就可以透過在 `Authorization` 標頭中包含權杖，在 Web API 的要求中使用權杖：

> [!TIP]
> 在 Postman 中執行這項要求！ （先取代 `Authorization` 標頭）[![嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>重新整理存取權杖

Access_token 有效期很短，且您必須在其到期後重新整理，才能繼續存取資源。 方法是：向 `/token` 端點送出另一個 `POST` 要求，這次提供 `refresh_token`，而不提供 `code`。  重新整理權杖對用戶端已同意的所有權限都有效，因此，對 `scope=mail.read` 要求所發出的重新整理權杖可用於向 `scope=api://contoso.com/api/UseResource` 要求新的存取權杖。  

重新整理權杖並沒有指定的存留期。 一般而言，重新整理權杖的存留期相當長。 不過，在某些情況下，重新整理權杖會過期、遭到撤銷或對要執行的動作缺乏足夠的權限。 應用程式必須預期並正確處理[權杖發行端點所傳回的錯誤](#error-codes-for-token-endpoint-errors)。 

雖然重新整理權杖不會在用來取得新的存取權杖時撤銷，但您仍應捨棄舊的重新整理權杖。 [OAuth 2.0 規格](https://tools.ietf.org/html/rfc6749#section-6)指出：「授權伺服器可能會發出新的重新整理權杖，在此情況下，用戶端必須捨棄舊的重新整理權杖，並以新的重新整理權杖取代。 授權伺服器可能會在發出新的重新整理權杖給用戶端之後，撤銷舊的重新整理權杖。」  

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> 嘗試在 Postman 中執行這項要求！ （別忘了取代 `refresh_token`）[![嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
> 

| 參數     |                | 說明        |
|---------------|----------------|--------------------|
| `tenant`        | required     | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。   |
| `client_id`     | required    | **應用程式（用戶端）識別碼**， [Azure 入口網站](https://go.microsoft.com/fwlink/?linkid=2083908)指派給您應用程式的應用程式註冊體驗。 |
| `grant_type`    | required    | 必須是授權碼流程此階段的 `refresh_token` 。 |
| `scope`         | required    | 範圍的空格分隔清單。 在此階段中要求的範圍必須相當於或為原始 authorization_code 要求階段中所要求的範圍子集。 如果此要求中指定的範圍跨越多個資源伺服器，則 Microsoft 身分識別平臺端點會傳回第一個範圍中所指定資源的權杖。 如需範圍的詳盡說明，請參閱 [權限、同意和範圍](v2-permissions-and-consent.md)。 |
| `refresh_token` | required    | 您在流程的第二個階段中取得的 refresh_token。 |
| `client_secret` | Web Apps 所需 | 您在應用程式註冊入口網站中為應用程式建立的應用程式密碼。 不應在原生應用程式中使用，因為 client_secrets 無法可靠地儲存在裝置上。 這是 web 應用程式和 web Api 的必要功能，能夠將 client_secret 安全地儲存在伺服器端。 |

#### <a name="successful-response"></a>成功回應

成功的權杖回應如下：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fuser.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 參數     | 說明         |
|---------------|-------------------------------------------------------------|
| `access_token`  | 所要求的存取權杖。 應用程式可以使用這個權杖驗證受保護的資源，例如 Web API。 |
| `token_type`    | 表示權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 |
| `expires_in`    | 存取權杖的有效期 (以秒為單位)。   |
| `scope`         | access_token 有效的範圍。    |
| `refresh_token` | 新的 OAuth 2.0 重新整理權杖。 您應該用新取得的重新整理權杖取代舊的重新整理權杖，以確定盡可能保持重新整理權杖有效的時間。 <br> **注意：** 只在要求 `offline_access` 範圍時提供。|
| `id_token`      | 不帶正負號的 JSON Web Token (JWT)。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取並顯示值，但不應依賴這些值來取得任何授權或安全性界限。 如需有關 id_token 的詳細資訊，請參閱 [`id_token reference`](id-tokens.md)。 <br> **注意：** 只在要求 `openid` 範圍時提供。 |

#### <a name="error-response"></a>錯誤回應

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 參數         | 說明                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。           |
| `error_codes` |有助於診斷的 STS 特定錯誤碼清單。 |
| `timestamp` | 發生錯誤的時間。 |
| `trace_id` | 有助於診斷的要求唯一識別碼。 |
| `correlation_id` | 有助於跨元件診斷的要求唯一識別碼。 |

如需錯誤碼及建議的用戶端動作的說明，請參閱[權杖端點錯誤的錯誤碼](#error-codes-for-token-endpoint-errors)。
