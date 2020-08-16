---
title: Microsoft 身分識別平台和 OAuth 2.0 授權碼流程 |Azure
titleSuffix: Microsoft identity platform
description: 使用 OAuth 2.0 驗證通訊協定的 Microsoft 身分識別平台實作來建置 Web 應用程式。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6cf9f7a005a80ab34e05ee293c20209e9d0b3f01
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258577"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft 身分識別平台和 OAuth 2.0 授權碼流程

OAuth 2.0 授權碼授與可用於裝置上所安裝的應用程式中，以存取受保護的資源，例如 Web API。 透過 Microsoft身分識別平台實作 OAuth 2.0，您可以將登入及 API 存取新增至您的行動應用程式和桌面應用程式。

本文說明如何在您的應用程式中使用任何語言直接針對通訊協定進行程式設計。  建議您盡可能改為使用支援的 Microsoft 驗證程式庫 (MSAL)，以[取得權杖並呼叫受保護的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另請參閱[使用 MSAL 的範例應用程式](sample-v2-code.md)。

如需 OAuth 2.0 授權碼流程的說明，請參閱 [OAuth 2.0 規格的 4.1 節](https://tools.ietf.org/html/rfc6749)。 其用來在大部分的應用程式類型 (包括[單頁應用程式](v2-app-types.md#single-page-apps-javascript)、[Web 應用程式](v2-app-types.md#web-apps)和[原生安裝的應用程式](v2-app-types.md#mobile-and-native-apps)) 中執行驗證和授權。 這個流程可讓應用程式安全地取得 access_tokens，以便用來存取受 Microsoft 身分識別平台端點保護的資源，而且取得重新整理權杖以取得額外的 access_tokens，並取得已登入使用者的識別碼權杖。

## <a name="protocol-diagram"></a>通訊協定圖表

概括而言，應用程式的整個驗證流程看起來有點像這樣：

![OAuth 授權碼流程](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="redirect-uri-setup-required-for-single-page-apps"></a>單一頁面應用程式所需的重新導向 URI 設定

單頁應用程式的授權碼流程需要一些額外的設定。  依照指示來 [建立單一頁面應用程式](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) ，以正確地將重新導向 URI 標示為已針對 CORS 啟用。 若要更新現有的重新導向 URI 以啟用 CORS，請開啟資訊清單編輯器，並 `type` `spa` 在區段中將重新導向 uri 的欄位設定為 `replyUrlsWithType` 。 您也可以在 [驗證] 索引標籤的 [Web] 區段中，按一下 [重新導向 URI]，然後選取您想要使用授權碼流程遷移到的 Uri。

重新 `spa` 導向類型與隱含流程具有回溯相容性。 目前使用隱含流程來取得權杖的應用程式，可以移至重新 `spa` 導向 URI 類型而不會發生問題，並繼續使用隱含流程。

如果您嘗試使用授權碼流程，並看到此錯誤：

`access to XMLHttpRequest at 'https://login.microsoftonline.com/common/v2.0/oauth2/token' from origin 'yourApp.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.`

那麼您必須造訪您的應用程式註冊，並更新應用程式的重新導向 URI 為 `spa` 類型。

## <a name="request-an-authorization-code"></a>要求授權碼

授權碼流程始於用戶端將使用者導向 `/authorize` 端點。 在此要求中，用戶端會向使用者要求 `openid`、`offline_access`和 `https://graph.microsoft.com/mail.read ` 權限。  某些權限是由系統管理員限制，例如使用 `Directory.ReadWrite.All` 將資料寫入組織的目錄。 如果您的應用程式向組織使用者要求這其中一個權限的存取權，使用者將會收到錯誤訊息，指出他們未獲授權而無法對您應用程式的權限表示同意。 若要要求存取受管理員限制的範圍，您應該直接向公司系統管理員要求。  如需詳細資訊，請參閱[系統管理員限制的權限](v2-permissions-and-consent.md#admin-restricted-permissions)。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
&code_challenge=YTFjNjI1OWYzMzA3MTI4ZDY2Njg5M2RkNmVjNDE5YmEyZGRhOGYyM2IzNjdmZWFhMTQ1ODg3NDcxY2Nl
&code_challenge_method=S256
```

> [!TIP]
> 按一下下面的連結以執行此要求！ 登入之後，您的瀏覽器應重新導向至在位址列中有 `code` 的 `https://localhost/myapp/`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 參數    | 必要條件/選擇性 | 描述 |
|--------------|-------------|--------------|
| `tenant`    | required    | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。  |
| `client_id`   | required    | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗指派給您應用程式的**應用程式 (用戶端) 識別碼**。  |
| `response_type` | required    | 授權碼流程必須包含 `code`。       |
| `redirect_uri`  | required | 應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。 對於原生和行動應用程式，請使用 `https://login.microsoftonline.com/common/oauth2/nativeclient` 的預設值。   |
| `scope`  | required    | 您要使用者同意的 [範圍](v2-permissions-and-consent.md) 空格分隔清單。  在要求的 `/authorize` 階段中，這可以涵蓋多項資源，讓您的應用程式對於您想要呼叫的多個 Web API 徵得同意。 |
| `response_mode`   | 建議使用 | 指定將產生的權杖送回到應用程式所應該使用的方法。 可以是下列其中一項：<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` 會提供程式碼，以作為重新導向 URI 的查詢字串參數。 如果您要求可使用隱含流程的識別碼權杖，就無法使用 [OpenID 規格](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)中指定的 `query`。如果您只要求程式碼，您可以使用 `query`、`fragment` 或 `form_post`。 `form_post` 會執行 POST，其中包含您重新導向 URI 的程式碼。 如需詳細資訊，請參閱 [OpenID Connect 通訊協定](../azuread-dev/v1-protocols-openid-connect-code.md)。  |
| `state`                 | 建議使用 | 同樣會隨權杖回應傳回之要求中所包含的值。 其可以是您想要之任何內容的字串。 隨機產生的唯一值通常用於 [防止跨站台要求偽造攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此值也可以將驗證要求發生前使用者在應用程式中的狀態相關資訊 (例如他們所在的網頁或檢視) 編碼。 |
| `prompt`  | 選用    | 表示需要的使用者互動類型。 此時唯有 `login`、`none` 及 `consent` 是有效值。<br/><br/>- `prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。<br/>- `prompt=none` 則相反，其會確保使用者不會看到任何互動式提示。 如果無法透過單一登入以無訊息方式完成要求，Microsoft 身分識別平台端點將傳回 `interaction_required` 錯誤。<br/>- `prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。<br/>- `prompt=select_account` 將中斷單一登入，其中會列出工作階段或任何已記住的帳戶中的所有帳戶，或提供選擇使用不同帳戶的選項。<br/> |
| `login_hint`  | 選用    | 如果您事先知道其使用者名稱，可用來預先填入使用者登入頁面的使用者名稱/電子郵件地址欄位。 通常應用程式會在重新驗證期間使用此參數，已經使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。   |
| `domain_hint`  | 選用    | 如果包含，它會略過使用者在登入頁面上以電子郵件為基礎的探索程序，藉以提供稍微更有效率的使用者體驗，例如，將使用者傳送到同盟識別提供者。 通常應用程式會在重新驗證 (擷取上一次登入的 `tid` ) 期間使用此參數。 如果 `tid` 宣告值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，您應該使用 `domain_hint=consumers`。 否則，使用 `domain_hint=organizations`。  |
| `code_challenge`  | 建議 / 必要 | 用來透過「代碼交換的證明金鑰」(PKCE) 保護授權碼授與。 如果包含 `code_challenge_method`，則為必要參數。 如需詳細資訊，請參閱 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 現在建議所有的應用程式類型，包括原生應用程式、SPA 和機密用戶端 (例如 Web 應用程式)。 |
| `code_challenge_method` | 建議 / 必要 | 用來為 `code_challenge` 參數編碼 `code_verifier` 的方法。 這 *應該* 是 `S256` ，但 `plain` 如果基於某些原因而導致用戶端無法支援 SHA256，則此規格允許使用。 <br/><br/>如果排除，則當包含 `code_challenge` 時，會假設 `code_challenge` 是純文字。 Microsoft 身分識別平台同時支援 `plain` 和 `S256`。 如需詳細資訊，請參閱 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 這是使用[授權碼流程單頁應用程式](reference-third-party-cookies-spas.md)所需的必要條件。|


此時，會要求使用者輸入其認證並完成驗證。 Microsoft 身分識別平台端點也會確認使用者已經同意 `scope` 查詢參數所指出的權限。 如果使用者未曾同意這些權限的任何一項，就會要求使用者同意要求的權限。 [這裡提供權限、同意與多租用戶應用程式](v2-permissions-and-consent.md)的詳細資料。

一旦使用者驗證並同意，Microsoft 身分識別平台端點就會使用 `response_mode` 參數中指定的方法，將回應傳回至位於指定 `redirect_uri` 所在的應用程式。

#### <a name="successful-response"></a>成功回應

使用 `response_mode=query` 的成功回應如下所示：

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 參數 | 描述  |
|-----------|--------------|
| `code` | 應用程式要求的 authorization_code。 應用程式可以使用授權碼要求目標資源的存取權杖。 Authorization_codes 的有效期很短，通常會在大約 10 分鐘後到期。 |
| `state` | 如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

如果您要求一個存取權杖和識別碼權杖，並在您的應用程式註冊中啟用隱含授與，則您也可以接收這些存取權杖和識別碼權杖。  這有時稱為「混合式流程」，而且是由 ASP.NET 之類的架構所使用。

#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理：

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述  |
|----------|------------------|
| `error`  | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>授權端點錯誤的錯誤碼

下表說明各種可能在錯誤回應的 `error` 參數中傳回的錯誤碼。

| 錯誤碼  | 描述    | 用戶端動作   |
|-------------|----------------|-----------------|
| `invalid_request` | 通訊協定錯誤，例如遺漏必要的參數。 | 修正並重新提交要求。 這是通常會在初始測試期間擷取到的開發錯誤。 |
| `unauthorized_client` | 不允許用戶端應用程式要求授權碼。 | 這個錯誤通常會在用戶端應用程式未在 Azure AD 中註冊，或未加入至使用者的 Azure AD 租用戶時發生。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| `access_denied`  | 資源擁有者拒絕同意  | 用戶端應用程式可以通知使用者除非使用者同意，否則其無法繼續進行。 |
| `unsupported_response_type` | 授權伺服器不支援要求中的回應類型。 | 修正並重新提交要求。 這是通常會在初始測試期間擷取到的開發錯誤。  |
| `server_error`  | 伺服器發生非預期的錯誤。| 重試要求。 這些錯誤可能是由暫時性狀況所引起。 用戶端應用程式可能會向使用者解釋其回應因暫時性錯誤而延遲。 |
| `temporarily_unavailable`   | 伺服器暫時過於忙碌而無法處理要求。 | 重試要求。 用戶端應用程式可能會向使用者解釋其回應因暫時性狀況而延遲。 |
| `invalid_resource`  | 目標資源無效，因為它不存在、Azure AD 無法找到它，或是它並未正確設定。 | 此錯誤表示尚未在租用戶中設定資源 (如果存在)。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| `login_required` | 找到太多使用者或找不到使用者 | 用戶端已要求無訊息驗證 (`prompt=none`)，但找不到單一使用者。 這可能表示工作階段中有多個作用中使用者，或沒有任何使用者。 這會將選擇的租用戶列入考慮 (例如，如果有兩個使用中的 Azure AD 帳戶和一個 Microsoft 帳戶，且已選擇 `consumers`，無訊息驗證即可運作)。 |
| `interaction_required` | 要求需要使用者互動。 | 必須進行其他驗證步驟或同意。 請在不使用 `prompt=none` 的情況下重試要求。 |

## <a name="request-an-access-token"></a>要求存取權杖

取得 authorization_code 並獲得使用者授權之後，現在即可兌換所需資源之 `access_token` 的 `code`。 做法是將 `POST` 要求傳送給 `/token` 端點：

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&code_verifier=ThisIsntRandomButItNeedsToBe43CharactersLong 
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> 嘗試在 Postman 中執行這項要求！ (別忘了取代 `code`) [![請嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| 參數  | 必要條件/選擇性 | 描述     |
|------------|-------------------|----------------|
| `tenant`   | required   | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。  |
| `client_id` | required  | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面指派給您應用程式的應用程式 (用戶端) 識別碼。 |
| `grant_type` | required   | 必須是授權碼流程的 `authorization_code` 。   |
| `scope`      | 選用   | 以空格分隔的範圍清單。 範圍必須全部來自單一資源，以及 OIDC 範圍 (`profile`、`openid`、`email`)。 如需範圍的詳盡說明，請參閱 [權限、同意和範圍](v2-permissions-and-consent.md)。 這是授權碼流程的 Microsoft 擴充功能，目的是要允許應用程式在權杖兌換期間宣告他們想要權杖的資源。|
| `code`          | required  | 您在流程的第一個階段中取得的 authorization_code。 |
| `redirect_uri`  | required  | 用來取得 authorization_code 的相同 redirect_uri 值。 |
| `client_secret` | 機密 Web 應用程式所需 | 您在應用程式註冊入口網站中為應用程式建立的應用程式密碼。 您不應該在原生應用程式或單頁應用程式中使用應用程式密碼，因為 client_secrets 無法可靠地儲存在裝置或網頁上。 這是 Web 應用程式和 Web API 的必要參數，能夠將 client_secret 安全地儲存在伺服器端。  用戶端密碼必須在傳送之前先進行 URL 編碼。 如需有關 URI 編碼的詳細資訊，請參閱 [URI 一般語法規格](https://tools.ietf.org/html/rfc3986#page-12)。 |
| `code_verifier` | 建議使用  | 用來取得 authorization_code 的相同 code_verifier。 如果在授權碼授與要求中已使用 PKCE，則為必要參數。 如需詳細資訊，請參閱 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 |

### <a name="successful-response"></a>成功回應

成功的權杖回應看起來會像這樣：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 參數     | 描述   |
|---------------|------------------------------|
| `access_token`  | 所要求的存取權杖。 應用程式可以使用這個權杖驗證受保護的資源，例如 Web API。  |
| `token_type`    | 表示權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 |
| `expires_in`    | 存取權杖的有效期 (以秒為單位)。 |
| `scope`         | access_token 有效的範圍。 選擇性-這不是標準的，而且如果省略，則權杖將會用於流程初始階段所要求的範圍。 |
| `refresh_token` | OAuth 2.0 重新整理權杖。 應用程式可以使用這個權杖，在目前的存取權杖過期之後，取得其他的存取權杖。 Refresh_token 的有效期很長，而且可以用來延長保留資源存取權的時間。 如需有關重新整理存取權杖的詳細資訊，請參閱[下一節](#refresh-the-access-token)。 <br> **注意：** 只在要求 `offline_access` 範圍時提供。 |
| `id_token`      | JSON Web 權杖 (JWT)。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取值並加以顯示，而機密用戶端可以將其用於授權。 如需有關 id_token 的詳細資訊，請參閱 [`id_token reference`](id-tokens.md)。 <br> **注意：** 只在要求 `openid` 範圍時提供。 |

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

| 參數         | 描述    |
|-------------------|----------------|
| `error`       | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |
| `error_codes` | 有助於診斷的 STS 特定錯誤碼清單。  |
| `timestamp`   | 發生錯誤的時間。 |
| `trace_id`    | 有助於診斷的要求唯一識別碼。 |
| `correlation_id` | 有助於跨元件診斷的要求唯一識別碼。 |

### <a name="error-codes-for-token-endpoint-errors"></a>權杖端點錯誤的錯誤碼

| 錯誤碼         | 描述        | 用戶端動作    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 通訊協定錯誤，例如遺漏必要的參數。 | 修正要求或應用程式註冊，然後重新提交要求   |
| `invalid_grant`    | 授權碼或 PKCE 代碼驗證器無效或已過期。 | 嘗試向 `/authorize` 端點提出新的要求，並確認 code_verifier 參數正確。  |
| `unauthorized_client` | 未授權驗證用戶端使用此授權授與類型。 | 這通常會在用戶端應用程式未在 Azure AD 中註冊，或未加入至使用者的 Azure AD 租用戶時發生。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| `invalid_client` | 用戶端驗證失敗。  | 用戶端認證無效。 若要修正，應用程式系統管理員必須更新認證。   |
| `unsupported_grant_type` | 授權伺服器不支援授權授與類型。 | 變更要求中的授與類型。 這種類型的錯誤應該只會在開發期間發生，並且會在初始測試期間偵測出來。 |
| `invalid_resource` | 目標資源無效，因為它不存在、Azure AD 無法找到它，或是它並未正確設定。 | 這表示尚未在租用戶中設定資源 (如果存在)。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。  |
| `interaction_required` | 非標準，因為 OIDC 規格只會在端點上呼叫此 `/authorize` 。要求需要使用者互動。 例如，必須進行其他驗證步驟。 | 請 `/authorize` 使用相同的範圍重試要求。 |
| `temporarily_unavailable` | 伺服器暫時過於忙碌而無法處理要求。 | 請在短暫的延遲後重試要求。 用戶端應用程式可能會向使用者解釋其回應因暫時性狀況而延遲。 |
|`consent_required` | 要求需要使用者同意。 此錯誤並非標準，因為它通常只會在 `/authorize` 每個 OIDC 規格的端點上傳回。 在 `scope` 用戶端應用程式沒有許可權要求的程式碼兌換流程上使用參數時傳回。  | 用戶端應將使用者傳回 `/authorize` 具有正確範圍的端點，才能觸發同意。 |

> [!NOTE]
> 單頁應用程式可能會收到 `invalid_request` 錯誤，指出只允許「單頁應用程式」用戶端類型的跨原始來源權杖兌換。  這表示用來要求權杖的重新導向 URI 尚未標示為 `spa` 重新導向 URI。  關於如何啟用此流程，請參閱[應用程式註冊步驟](#redirect-uri-setup-required-for-single-page-apps)。

## <a name="use-the-access-token"></a>使用存取權杖

既然您已經成功取得 `access_token`，您就可以透過在 `Authorization` 標題中包含權杖，在 Web API 的要求中使用權杖：

> [!TIP]
> 在 Postman 中執行這項要求！ (先取代 `Authorization` 標題) [![嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>重新整理存取權杖

Access_token 有效期很短，且您必須在其到期後重新整理，才能繼續存取資源。 方法是：向 `/token` 端點送出另一個 `POST` 要求，這次提供 `refresh_token`，而不提供 `code`。  重新整理權杖對用戶端已同意的所有權限都有效，因此，對 `scope=mail.read` 要求所發出的重新整理權杖可用於向 `scope=api://contoso.com/api/UseResource` 要求新的存取權杖。

Web 應用程式和原生應用程式的重新整理權杖未指定存留期。 一般而言，重新整理權杖的存留期相當長。 不過，在某些情況下，重新整理權杖會過期、遭到撤銷或對要執行的動作缺乏足夠的權限。 應用程式必須預期並正確處理[權杖發行端點所傳回的錯誤](#error-codes-for-token-endpoint-errors)。 不過，單頁應用程式會取得具有 24小 時存留期的權杖，每日需要新的驗證。  這項作業可以在協力廠商 Cookie 已啟用時在 iframe 中以無訊息的方式完成，但必須在沒有協力廠商 Cookie 的瀏覽器 (例如 Safari) 中的最上層框架 (完整頁面瀏覽或快顯) 中執行。

雖然重新整理權杖不會在用來取得新存取權杖時撤銷，但您仍應捨棄舊的重新整理權杖。 [OAuth 2.0 規格](https://tools.ietf.org/html/rfc6749#section-6)指出：「授權伺服器『可能』會發出新的重新整理權杖，在此情況下，用戶端『必須』捨棄舊的重新整理權杖，並以新的重新整理權杖取代。 授權伺服器『可能』會在對用戶端發出新的重新整理權杖之後，撤銷舊的重新整理權杖。」

>[!IMPORTANT]
> 針對向註冊為 `spa` 的重新導向 URI 傳送的重新整理權杖，重新整理權杖會在 24 小時後到期。 使用初始重新整理權杖取得的其他重新整理權杖將會延續到該到期時間，因此應用程式必須準備好使用互動式驗證來重新執行授權碼流程，以每隔 24 小時取得新的重新整理權杖。 使用者不需要輸入其認證，而且通常甚至不會看到任何 UX，只是應用程式重新載入，而瀏覽器必須造訪最上層框架中的登入頁面，才能看到登入工作階段。  這是因為瀏覽器的[隱私權功能](reference-third-party-cookies-spas.md)封鎖協力廠商 Cookie。

```HTTP

// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> 嘗試在 Postman 中執行這項要求！ (別忘了取代 `refresh_token`) [![請嘗試在 Postman 中執行此要求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| 參數     | 類型           | 描述        |
|---------------|----------------|--------------------|
| `tenant`        | required     | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。   |
| `client_id`     | required    | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)體驗指派給您應用程式的**應用程式 (用戶端) 識別碼**。 |
| `grant_type`    | required    | 必須是授權碼流程此階段的 `refresh_token` 。 |
| `scope`         | required    | 範圍的空格分隔清單。 在此階段中要求的範圍必須相當於或為原始 authorization_code 要求階段中所要求的範圍子集。 如果這個要求中指定的範圍遍及多個資源伺服器，Microsoft 身分識別平台端點就會傳回第一個範圍中所指定資源的權杖。 如需範圍的詳盡說明，請參閱 [權限、同意和範圍](v2-permissions-and-consent.md)。 |
| `refresh_token` | required    | 您在流程的第二個階段中取得的 refresh_token。 |
| `client_secret` | Web Apps 所需 | 您在應用程式註冊入口網站中為應用程式建立的應用程式密碼。 此參數不應用於原生應用程式中，因為 client_secrets 無法可靠地儲存在裝置上。 這是 Web 應用程式和 Web API 的必要參數，能夠將 client_secret 安全地儲存在伺服器端。 此密碼必須經過 URL 編碼。 如需詳細資訊，請參閱 [URI 一般語法規格](https://tools.ietf.org/html/rfc3986#page-12)。 |

#### <a name="successful-response"></a>成功回應

成功的權杖回應看起來會像這樣：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 參數     | 描述         |
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

| 參數         | 描述                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` | 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。           |
| `error_codes` |有助於診斷的 STS 特定錯誤碼清單。 |
| `timestamp` | 發生錯誤的時間。 |
| `trace_id` | 有助於診斷的要求唯一識別碼。 |
| `correlation_id` | 有助於跨元件診斷的要求唯一識別碼。 |

如需錯誤碼及建議的用戶端動作的說明，請參閱[權杖端點錯誤的錯誤碼](#error-codes-for-token-endpoint-errors)。
