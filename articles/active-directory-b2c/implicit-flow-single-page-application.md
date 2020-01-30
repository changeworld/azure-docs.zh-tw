---
title: 使用隱含流程的單一頁面登入
titleSuffix: Azure AD B2C
description: 瞭解如何搭配 Azure Active Directory B2C 使用 OAuth 2.0 隱含流程來新增單一頁面登入。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: efef55ab42313854ab27d323824a76488d520ad1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847377"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用 OAuth 2.0 隱含流程的單一頁面登入

許多現代化應用程式都有單一頁面應用程式前端，主要是以 JavaScript 撰寫。 應用程式通常會使用回應、角度或 Vue 之類的架構來撰寫。 主要在瀏覽器上執行的單一頁面應用程式和其他 JavaScript 應用程式，在驗證時會面臨一些額外的挑戰：

- 這些應用程式的安全性特性與傳統的以伺服器為基礎的 web 應用程式不同。
- 許多授權伺服器與識別提供者不支援跨原始來源資源共用 (CORS) 要求。
- 從應用程式重新導向的整頁瀏覽器可能會侵入使用者體驗。

為了支援這些應用程式，Azure Active Directory B2C (Azure AD B2C) 使用 OAuth 2.0 隱含流程。 如需 OAuth 2.0 授權隱含授權流程的說明，請參閱 [OAuth 2.0 規格的 4.2 節](https://tools.ietf.org/html/rfc6749) 。 在隱含流程中，應用程式會直接從 Azure Active Directory (Azure AD) 授權端點接收權杖，而不需執行任何伺服器對伺服器交換。 所有驗證邏輯和會話處理都是透過頁面重新導向或快顯方塊，完全在 JavaScript 用戶端中完成。

Azure AD B2C 會擴充標準的 OAuth 2.0 隱含流程，功能更強大，而不僅止於簡單的驗證與授權。 Azure AD B2C 導入了[原則參數](user-flow-overview.md)。 利用原則參數，您可以使用 OAuth 2.0 來為應用程式新增原則，例如註冊、登入和設定檔管理使用者流程。 在本文的範例 HTTP 要求中，會使用 **{tenant}. onmicrosoft**作為範例。 將 `{tenant}` 取代為您的租使用者名稱（如果有的話），並同時建立使用者流程。

隱含登入流程看起來像下圖。 本文稍後將詳細說明每個步驟。

![顯示 OpenID Connect 隱含流程的泳道樣式圖表](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>傳送驗證要求

當您的 web 應用程式需要驗證使用者並執行使用者流程時，它可以將使用者導向 `/authorize` 端點。 使用者會根據使用者流程採取動作。

在此要求中，用戶端會在 `scope` 參數和要執行的使用者流程中，指出它需要從使用者取得的許可權。 若要瞭解要求的運作方式，請嘗試將要求貼入瀏覽器並加以執行。 將 `{tenant}` 取代為您的 Azure AD B2C 租用戶名稱。 將 `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` 取代為您先前在租使用者中註冊之應用程式的應用程式識別碼。 以您在租使用者中建立的原則名稱取代 `{policy}`，例如 `b2c_1_sign_in`。

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| 參數 | 必要項 | 說明 |
| --------- | -------- | ----------- |
|出租| 是 | Azure AD B2C 租使用者的名稱|
|策略| 是| 要執行的使用者流程。 指定您在 Azure AD B2C 租使用者中建立的使用者流程名稱。 例如： `b2c_1_sign_in`、`b2c_1_sign_up`或 `b2c_1_edit_profile`。 |
| client_id | 是 | [Azure 入口網站](https://portal.azure.com/)指派給應用程式的應用程式識別碼。 |
| response_type | 是 | 必須包含 OpenID Connect 登入的 `id_token` 。 它也可能包含回應類型 `token`。 如果您使用 `token`，您的應用程式就能立即從授權端點接收存取權杖，而不需向授權端點進行第二次要求。  如果您使用 `token` 回應類型，`scope` 參數就必須包含範圍，以指出要對哪個資源發出權杖。 |
| redirect_uri | 否 | 應用程式的重新導向 URI，您的應用程式可在此傳送及接收驗證回應。 它必須與您在入口網站中註冊的其中一個重新導向 URI 完全相符，不過必須是 URL 編碼格式。 |
| response_mode | 否 | 指定用來將產生的權杖送回應用程式的方法。  針對隱含流程，請使用 `fragment`。 |
| scope | 是 | 範圍的空格分隔清單。 向 Azure AD 指出要求兩個權限的單一範圍值。 `openid` 範圍指示使用識別碼權杖形式的權限，以登入使用者及取得使用者相關資料。 對於 Web 應用程式， `offline_access` 範圍是選擇性。 它指出您的應用程式需要重新整理權杖，才能長久存取資源。 |
| state | 否 | 包含於也會隨權杖回應傳回之要求中的值。 它可以是您想要使用之任何內容的字串。 通常會使用隨機產生的唯一值來防止跨網站偽造要求攻擊。 驗證要求出現前，也會先使用此狀態來為使用者在應用程式中的狀態相關資訊編碼，例如他們先前所在的網頁。 |
| nonce | 是 | 要求中所含的值 (由應用程式產生)，它會以宣告形式包含於產生的識別碼權杖中。 應用程式接著便可確認此值，以減少權杖重新執行攻擊。 此值通常是隨機的唯一字串，可用以識別要求的來源。 |
| prompt | 否 | 需要的使用者互動類型。 目前唯一支援的值為 `login`。 此參數會強制使用者在該要求上輸入其認證。 單一登入不會生效。 |

此時會要求使用者完成原則的工作流程。 使用者可能必須輸入使用者名稱和密碼、以社交身分識別登入、註冊目錄，或是其他任何數目的步驟。 使用者動作取決於使用者流程的定義方式。

當使用者完成使用者流程之後，Azure AD 會透過您用於 `redirect_uri` 的值，將回應傳回給您的應用程式。 它會使用 `response_mode` 參數中指定的方法。 對於每個使用者動作情節來說，回應完全相同，與已執行的使用者流程無關。

### <a name="successful-response"></a>成功回應
使用 `response_mode=fragment` 和 `response_type=id_token+token` 的成功回應如下所示 (內含換行符號以利閱讀)：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| 參數 | 說明 |
| --------- | ----------- |
| access_token | 應用程式要求的存取權杖。 |
| token_type | 權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 |
| expires_in | 存取權杖的有效時間長度 (以秒為單位)。 |
| scope | 權杖有效的範圍。 您也可以使用範圍來快取權杖，以供稍後使用。 |
| id_token | 應用程式所要求的識別碼權杖。 您可以使用識別碼權杖來確認使用者的身分識別，然後開始與使用者的工作階段。 如需識別碼權杖及其內容的詳細資料，請參閱 [Azure AD B2C 權杖參考](tokens-overview.md)。 |
| state | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應該驗證要求和回應中的 `state` 值完全相同。 |

### <a name="error-response"></a>錯誤回應
錯誤回應也能傳送到重新導向 URI，以便讓應用程式能夠適當地處理它們：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 說明 |
| --------- | ----------- |
| error | 用來分類發生的錯誤類型的程式碼。 |
| error_description | 可協助您識別驗證錯誤根本原因的特定錯誤訊息。 |
| state | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應該驗證要求和回應中的 `state` 值完全相同。|

## <a name="validate-the-id-token"></a>驗證識別碼權杖

收到識別碼權杖並不足以驗證使用者。 驗證識別碼權杖的簽章，並根據您的應用程式需求確認權杖中的宣告。 Azure AD B2C 使用 [JSON Web Tokens (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公開金鑰加密編譯來簽署權杖及驗證其是否有效。

視您偏好使用的語言而定，有許多開放原始碼程式庫可用來驗證 JWT。 建議您探索可用的開放原始碼程式庫，而不是實作您自己的驗證邏輯。 您可以使用本文中的資訊，以協助了解如何適當使用這些程式庫。

Azure AD B2C 具有 OpenID Connect 中繼資料端點。 應用程式可以使用此端點，在執行階段擷取 Azure AD B2C 的相關資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 您的 Azure AD B2C 租用戶中的每個使用者流程都有一份 JSON 中繼資料文件。 例如，fabrikamb2c.onmicrosoft.com 租用戶中 b2c_1_sign_in 使用者流程的中繼資料文件位於：

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

此設定文件的屬性之一是 `jwks_uri`。 相同使用者流程的值會是：

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

若要判斷使用了哪個使用者流程來簽署識別碼權杖 (以及可從何處擷取中繼資料)，您有兩個選項。 首先，使用者流程名稱包含於 `id_token` 的 `acr` 宣告中。 如需如何剖析識別碼權杖中的宣告相關資訊，請參閱 [Azure AD B2C 權杖參考](tokens-overview.md)。 另一個選項是當您發出要求時，在 `state` 參數的值中將使用者流程編碼。 然後將 `state` 參數解碼，以判斷使用了哪個使用者流程。 任一種方法都有效。

當您從 OpenID Connect 中繼資料端點取得中繼資料文件之後，就可以使用 RSA-256 公開金鑰 (位於此端點) 來驗證識別碼權杖的簽章。 此端點可能隨時會列出多個金鑰，每個都由 `kid` 所識別。 `id_token` 的標頭也包含 `kid` 宣告。 它指出使用了這其中哪個金鑰來簽署識別碼權杖。 如需詳細資訊 (包括了解如何[驗證權杖](tokens-overview.md))，請參閱 [Azure AD B2C 權杖參考](tokens-overview.md)。
<!--TODO: Improve the information on this-->

驗證識別碼權杖的簽章之後，也需要驗證數個宣告。 例如：

* 驗證 `nonce` 宣告，以防止權杖重新執行攻擊。 其值應該是您在登入要求中所指定的內容。
* 驗證 `aud` 宣告，以確保已針對您的應用程式簽發識別碼權杖。 這個值就是您應用程式的應用程式識別碼。
* 驗證 `iat` 與 `exp` 宣告，以確保識別碼權杖沒有過期。

[OpenID Connect 核心規格](https://openid.net/specs/openid-connect-core-1_0.html)中會詳細說明您應該執行的更多驗證。視您的案例而定，您可能也會想要驗證其他宣告。 一些常見的驗證包括：

* 確保使用者或組織已為應用程式註冊。
* 確保使用者有適當的授權與權限。
* 確保驗證方式有一定的強度，例如使用 Azure Multi-Factor Authentication。

如需識別碼權杖中宣告的詳細資訊，請參閱 [Azure AD B2C 權杖參考](tokens-overview.md)。

當您驗證過識別碼權杖之後，便可開始關於該使用者的工作階段。 在應用程式中，使用識別碼權杖中的宣告來取得使用者的相關資訊。 這項資訊可以用於顯示、記錄、授權等等。

## <a name="get-access-tokens"></a>取得存取權杖
如果您的 Web 應用程式只需要執行使用者流程，則可以略過接下來的幾節。 下列各節中的資訊僅適用于需要對 Web API 進行已驗證的呼叫，並受到 Azure AD B2C 保護的 web 應用程式。

現在您已將使用者登入單一頁面應用程式，您可以取得存取權杖來呼叫受 Azure AD 保護的 web Api。 即使您已經使用 `token` 回應類型收到權杖，仍可使用此方法來取得其他資源的權杖，而不需重新導向使用者進行再次登入。

在一般 web 應用程式流程中，您會對 `/token` 端點提出要求。 不過，端點不支援 CORS 要求，因此無法選擇進行 AJAX 呼叫來取得重新整理權杖。 相反地，您可以在隱藏的 HTML iframe 元素中使用隱含流程，為其他 Web API 取得新權杖。 以下是範例 (內含換行符號以利閱讀)：

```HTTP
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| 參數 | 必要項？ | 說明 |
| --- | --- | --- |
|出租| 必要項 | Azure AD B2C 租使用者的名稱|
策略| 必要項| 要執行的使用者流程。 指定您在 Azure AD B2C 租使用者中建立的使用者流程名稱。 例如： `b2c_1_sign_in`、`b2c_1_sign_up`或 `b2c_1_edit_profile`。 |
| client_id |必要項 |在 [Azure 入口網站](https://portal.azure.com)中指派給應用程式的應用程式識別碼。 |
| response_type |必要項 |必須包含 OpenID Connect 登入的 `id_token` 。  它也可能包含回應類型 `token`。 如果您在這裡使用 `token`，應用程式就能立即從授權端點接收存取權杖，而不需向授權端點進行第二次要求。 如果您使用 `token` 回應類型，`scope` 參數就必須包含範圍，以指出要對哪個資源發出權杖。 |
| redirect_uri |建議 |應用程式的重新導向 URI，您的應用程式可在此傳送及接收驗證回應。 它必須與您在入口網站中註冊的其中一個重新導向 URI 完全相符，不過必須是 URL 編碼格式。 |
| scope |必要項 |範圍的空格分隔清單。  若要取得權杖，請包含您針對所需資源要求的所有範圍。 |
| response_mode |建議 |指定將產生之權杖送回到應用程式要使用的方法。 針對隱含流程，請使用 `fragment`。 您可以指定兩個其他模式，`query` 和 `form_post`，但無法在隱含流程中使用。 |
| state |建議 |會隨權杖回應傳回之要求中所包含的值。  它可以是您想要使用之任何內容的字串。  通常會使用隨機產生的唯一值來防止跨網站偽造要求攻擊。  在驗證要求出現之前，也會使用此狀態將應用程式中使用者狀態的相關資訊編碼。 例如，使用者所在的網頁或檢視。 |
| nonce |必要項 |要求中所含的值 (由應用程式產生)，它會以宣告形式包含於產生的識別碼權杖中。  應用程式接著便可確認此值，以減少權杖重新執行攻擊。 此值通常是隨機的唯一字串，可用以識別要求的來源。 |
| prompt |必要項 |若要重新整理並取得隱藏 iframe 中的權杖，請使用 `prompt=none` 以確保 iframe 不會停滯在登入頁面上，並立即返回。 |
| login_hint |必要項 |若要重新整理並取得隱藏 iframe 中的權杖，請在此提示中包含使用者的使用者名稱，以區分使用者在特定時間點可能具有的多個工作階段。 您可以使用 `preferred_username` 宣告，從先前的登入中解壓縮使用者名稱（必須要有 `profile` 範圍，才能接收 `preferred_username` 宣告）。 |
| domain_hint |必要項 |可以是 `consumers` 或 `organizations`。  若要重新整理並取得隱藏 iframe 中的權杖，請在要求中包含 `domain_hint` 值。  從先前登入的識別碼權杖中解壓縮 `tid` 宣告，以判斷要使用的值（必須有 `profile` 範圍，才能接收 `tid` 宣告）。 如果 `tid` 宣告值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，請使用 `domain_hint=consumers`。  否則，使用 `domain_hint=organizations`。 |

一旦設定 `prompt=none` 參數，此要求就會立即成功或失敗，並返回您的應用程式。  藉由使用 `response_mode` 參數中指定的方法，以指定的重新導向 URI 將成功的回應傳送至您的應用程式。

### <a name="successful-response"></a>成功回應
使用 `response_mode=fragment` 的成功回應如下列範例所示：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| 參數 | 說明 |
| --- | --- |
| access_token |應用程式要求的權杖。 |
| token_type |權杖類型一律為持有人。 |
| state |如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應該驗證要求和回應中的 `state` 值完全相同。 |
| expires_in |存取權杖的有效期 (以秒為單位)。 |
| scope |存取權杖有效的範圍。 |

### <a name="error-response"></a>錯誤回應
錯誤回應也能傳送到重新導向 URI，以便讓應用程式能夠適當地處理它們。  針對 `prompt=none`，預期的錯誤如下列範例所示：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 參數 | 說明 |
| --- | --- |
| error |可用於將發生的錯誤分類的錯誤碼字串。 您也可以使用此字串對錯誤做出反應。 |
| error_description |可協助您識別驗證錯誤根本原因的特定錯誤訊息。 |

如果您在 iframe 要求中收到此錯誤，使用者必須再次以互動方式登入以擷取新的權杖。

## <a name="refresh-tokens"></a>重新整理權杖
識別碼權杖和存取權杖都會在短期內過期。 您的應用程式必須準備好定期重新整理這些權杖。  若要重新整理任一種類型的權杖，可使用 `prompt=none` 參數來控制 Azure AD 步驟，藉以執行我們在上述範例中所使用的相同隱藏 iframe 要求。  若要接收新的 `id_token` 值，請務必使用 `response_type=id_token` 和 `scope=openid`，以及 `nonce` 參數。

## <a name="send-a-sign-out-request"></a>傳送登出要求
當您想要將使用者登出應用程式時，請將使用者重新導向至 Azure AD 來登出。如果您不將使用者重新導向，他們可能會重新驗證您的應用程式，而不需要再次輸入其認證，因為他們與 Azure AD 具有有效的單一登入會話。

您只要將使用者重新導向至 `end_session_endpoint` (列於[驗證識別碼權杖](#validate-the-id-token)中所述的相同 OpenID Connect 中繼資料文件中) 即可。 例如：

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 參數 | 必要項 | 說明 |
| --------- | -------- | ----------- |
| 出租 | 是 | Azure AD B2C 租使用者的名稱 |
| 策略 | 是 | 您想要用來將使用者登出應用程式的使用者流程。 |
| post_logout_redirect_uri | 否 | 使用者在成功登出後應重新導向至的 URL。如果未包含，Azure AD B2C 會向使用者顯示一般訊息。 |
| state | 否 | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應確認要求和回應中的 `state` 值是否相同。 |


> [!NOTE]
> 將使用者導向至 `end_session_endpoint`，會利用 Azure AD B2C 清除使用者的部分單一登入狀態。 不過，它不會將使用者登出使用者的社交身分識別提供者工作階段。 如果使用者在後續登入時選取相同的識別提供者，則會重新驗證使用者，而不需要輸入其認證。 舉例來說，如果使用者想要登出您的 Azure AD B2C 應用程式，不一定代表他們想要完全登出自己的 Facebook 帳戶。 不過，如果使用本機帳戶，使用者的工作階段將會正確地結束。
>

## <a name="next-steps"></a>後續步驟

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>程式碼範例：使用 Azure AD B2C 的 hello .js

[以 Azure AD B2C （GitHub）上建于 node.js 的單一頁面應用程式][github-hello-js-example]

GitHub 上的此範例旨在協助您開始使用以[node.js][github-hello-js]建立的簡單 web 應用程式中的 Azure AD B2C，並使用快顯樣式驗證。

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-hellojs-singlepageapp
[github-hello-js]: https://github.com/MrSwitch/hello.js
