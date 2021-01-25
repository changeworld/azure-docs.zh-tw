---
title: OAuth 2.0 隱含授與流程-Microsoft 身分識別平臺 |蔚藍
description: 使用 Microsoft 身分識別平臺隱含流程保護單一頁面應用程式。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 97f4642d69d4a432b823bd1cd7cdbdd9fc7f270d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752741"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft 身分識別平臺和隱含授與流程

Microsoft 身分識別平臺支援 oauth [2.0 規格](https://tools.ietf.org/html/rfc6749#section-4.2)中所述的 Oauth 2.0 隱含授與流程。 隱含授與的定義特性是，權杖 (識別碼權杖或存取權杖) 會直接從/authorize 端點（而不是/token 端點）傳回。 這通常用來做為 [授權碼流程](v2-oauth2-auth-code-flow.md)的一部分，在所謂的「混合式流程」中，也就是在/authorize 要求上抓取識別碼權杖以及授權碼。

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>偏好使用驗證碼流程

有了 [要從瀏覽器中移除協力廠商 cookie](reference-third-party-cookies-spas.md)的計畫， **隱含授與流程不再是合適的驗證方法**。  隱含流程的 [無訊息 SSO 功能](#getting-access-tokens-silently-in-the-background) 無法在沒有協力廠商 cookie 的情況下運作，而導致應用程式在嘗試取得新權杖時中斷。 強烈建議所有新的應用程式都使用現在支援單一頁面應用程式的 [授權碼流程](v2-oauth2-auth-code-flow.md) 來取代隱含流程，而且現有的 [單一頁面應用程式也會開始遷移至授權碼流程](migrate-spa-implicit-to-auth-code.md) 。

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 隱含授與的適用案例

隱含授與只適用于您登入流程的初始互動部分，其中缺少 [協力廠商 cookie](reference-third-party-cookies-spas.md) 不會影響您的應用程式。 這項限制表示您應該將它專門作為混合式流程的一部分，您的應用程式會在其中要求程式碼以及來自授權端點的權杖。 這可確保您的應用程式收到可兌換重新整理權杖的程式碼，藉此確保您的應用程式登入會話在一段時間內仍然有效。

## <a name="protocol-diagram"></a>通訊協定圖表

下圖顯示整個隱含登入流程的概況，接下來的各節會更詳細地說明每個步驟。

![顯示隱含登入流程的圖表](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>傳送登入要求

若要一開始將使用者登入您的應用程式，您可以傳送 [OpenID Connect](v2-protocols-oidc.md) 的驗證要求，並 `id_token` 從 Microsoft 身分識別平臺取得。

> [!IMPORTANT]
> 若要成功要求識別碼權杖及/或存取權杖， [Azure 入口網站應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面中的應用程式註冊必須啟用對應的隱含授與流程，方法是選取 [**識別碼權杖**] 和，或在 **隱含授** 與區段下 **存取權杖**。 如果未啟用，則 `unsupported_response` 會傳回錯誤： **此用戶端不允許為輸入參數 ' response_type ' 提供的值。預期的值為 ' code '**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 若要使用隱含流程測試登入，請按一下<a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. 。</a>登入之後，您的瀏覽器應重新導向至 `https://localhost/myapp/` `id_token` 網址列中的。
>

| 參數 | 類型 | 描述 |
| --- | --- | --- |
| `tenant` | required |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | required | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面指派給您應用程式的應用程式 (用戶端) 識別碼。 |
| `response_type` | 必要 |必須包含 OpenID Connect 登入的 `id_token` 。 它也可能包含 response_type `token`。 這裡使用 `token` ，讓您的應用程式能夠立即從授權端點接收存取權杖，而不需要向授權端點進行第二次要求。 如果您使用 `token` response_type， `scope` 參數必須包含範圍，以指出要為 (發出權杖的資源，例如，在 Microsoft Graph) 上讀取。 它也可以包含 `code` 取代以提供授權碼，以便用於 `token` [授權碼流程](v2-oauth2-auth-code-flow.md)。 此 id_token + 程式碼回應有時稱為混合式流程。  |
| `redirect_uri` | 建議使用 |應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。 |
| `scope` | 必要 |以空格分隔的 [範圍](v2-permissions-and-consent.md)清單。 針對 OpenID Connect (id_tokens) ，它必須包含範圍，該範圍會轉譯 `openid` 為同意 UI 中的「登入」許可權。 （選擇性）您也可能想要包含 `email` 和範圍，以 `profile` 取得其他使用者資料的存取權。 如果要求存取權杖，您也可以在此要求中包含其他範圍來要求同意各種資源。 |
| `response_mode` | 選用 |指定應該用來將所產生權杖傳回給應用程式的方法。 預設只會查詢存取權杖，但如果要求包含 id_token，則為片段。 |
| `state` | 建議使用 |同樣會隨權杖回應傳回之要求中所包含的值。 其可以是您想要之任何內容的字串。 隨機產生的唯一值通常用於 [防止跨站台要求偽造攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此狀態也可用來在驗證要求發生之前，將使用者狀態的相關資訊編碼，例如他們所在的頁面或檢視。 |
| `nonce` | 必要 |包含在要求中的值 (由應用程式產生)，該值將會以宣告的形式包含在產生的 id_token 中。 然後，應用程式可以驗證此值，以減輕權杖重新執行所造成的攻擊。 此值通常是隨機的唯一字串，可以用來識別要求的來源。 只有在要求 id_token 時才需要。 |
| `prompt` | 選用 |表示必要的使用者互動類型。 目前的有效值只有 'login'、'none'、'select_account' 和 'consent'。 `prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。 `prompt=none` 相反-它會確保不會對使用者顯示任何互動式提示。 如果要求無法透過單一登入以無訊息方式完成，Microsoft 身分識別平臺將會傳回錯誤。 `prompt=select_account` 會將使用者傳送至帳戶選擇器，工作階段中記下的所有帳戶都會出現在當中。 `prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| `login_hint`  |選用 |如果您事先知道使用者的使用者名稱，可以用此項目來預先填入使用者登入頁面上的使用者名稱/電子郵件地址欄位。 通常，應用程式會在重新驗證期間使用此參數，並已使用宣告將使用者名稱從先前的登入解壓縮 `preferred_username` 。|
| `domain_hint` | 選用 |如果包含，它會略過使用者在登入頁面上經歷的電子郵件式探索程式，進而讓使用者體驗稍微簡化。 此參數通常用於在單一租使用者中操作的企業營運應用程式，在該租使用者中會提供指定租使用者內的功能變數名稱，並將使用者轉送至該租使用者的同盟提供者。  請注意，此提示會防止來賓登入此應用程式，並限制使用雲端認證（例如 FIDO）。  |

此時，系統會要求使用者輸入其認證並完成驗證。 Microsoft 身分識別平臺也會確保使用者已同意查詢參數中所指出的許可權 `scope` 。 如果使用者 **完全未** 同意這些權限的任一項，其會要求使用者同意必要權限。 如需詳細資訊，請參閱[權限、同意及多租用戶應用程式](v2-permissions-and-consent.md)。

一旦使用者驗證並授與同意，Microsoft 身分識別平臺將會 `redirect_uri` 使用參數中指定的方法，將回應傳回給您的應用程式 `response_mode` 。

#### <a name="successful-response"></a>成功的回應

使用 `response_mode=fragment` 及 `response_type=id_token+code` 的成功回應如下所示 (內含分行符號以利閱讀)：

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 參數 | 描述 |
| --- | --- |
| `code` | 如果 `response_type` 包含 `code` 則納入。 這是適合用於 [授權碼流程](v2-oauth2-auth-code-flow.md)的授權碼。  |
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖。 存取權杖不應解碼或檢查，它應該被視為不透明的字串。 |
| `token_type` |如果 `response_type` 包含 `token` 則納入。 一律為 `Bearer`。 |
| `expires_in`|如果 `response_type` 包含 `token` 則納入。 指出權杖有效的秒數，以供快取之用。 |
| `scope` |如果 `response_type` 包含 `token` 則納入。 表示 access_token 適用的範圍。 可能不會包含所有要求的範圍（如果這些範圍不適用於使用者 (，因為當使用個人帳戶登入) 時，會要求僅限 Azure AD 範圍的情況。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取並顯示這些值，但不應依賴這些值來取得任何授權或安全性界限。 如需有關 id_token 的詳細資訊，請參閱 [`id_token reference`](id-tokens.md)。 <br> **注意：** 只有在 `openid` 要求和包含範圍時才會提供 `response_type` `id_tokens` 。 |
| `state` |如果要求中包含 state 參數，則回應中應該會出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理：

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
| --- | --- |
| `error` |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

## <a name="getting-access-tokens-silently-in-the-background"></a>在背景中以無訊息方式取得存取權杖

> [!Important]
> 由於 [預設會移除協力廠商 cookie](reference-third-party-cookies-spas.md)，因此隱含流程的這個部分不太可能針對您的應用程式運作，因為它會在不同的瀏覽器上使用。  雖然這目前仍適用于不在 Incognito 中 Chromium 為基礎的瀏覽器，但開發人員應該重新考慮使用此部分的流程。 在不支援協力廠商 cookie 的瀏覽器中，您會收到錯誤，指出沒有任何使用者登入，因為瀏覽器已移除登入頁面的會話 cookie。 

現在您已將使用者登入您的單一頁面應用程式，您可以透過無訊息方式取得存取權杖，以呼叫受 Microsoft 身分識別平臺保護的 web Api，例如 [Microsoft Graph](https://developer.microsoft.com/graph)。 即使您已經收到使用 `token` response_type 的權杖，仍可使用此方法來取得其他資源的權杖，而不需重新導向使用者進行再次登入。

在一般的 OpenID Connect/OAuth 流程中，您可以向 Microsoft 身分識別平臺端點提出要求來完成這項作業 `/token` 。 您可以在隱藏 iframe 中提出要求，以取得其他 web Api 的新權杖：

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

如需 URL 中查詢參數的詳細資料，請參閱[傳送登入要求](#send-the-sign-in-request)。

> [!TIP]
> 請嘗試將下列要求貼至瀏覽器！ (請務必以您使用者的正確值取代 `login_hint` 值)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> 請注意，即使在沒有協力廠商 cookie 支援的瀏覽器中，這也可以運作，因為您是直接輸入瀏覽器列，而不是在 iframe 內開啟。 

由於 `prompt=none` 參數，此要求會立即成功或失敗，並且傳回給您的應用程式。 回應會 `redirect_uri` 使用參數中指定的方法，在指定的位置傳送至您的應用程式 `response_mode` 。

#### <a name="successful-response"></a>成功回應

使用 `response_mode=fragment` 的成功回應如下所示：

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| 參數 | 描述 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖，在此案例中為 Microsoft Graph 的存取權杖。 存取權杖不應解碼或檢查，它應該被視為不透明的字串。 |
| `token_type` | 一律為 `Bearer`。 |
| `expires_in` | 指出權杖有效的秒數，以供快取之用。 |
| `scope` | 表示 access_token 適用的範圍。 可能不會包含所有要求的範圍（如果這些範圍不適用於使用者 (，因為當使用個人帳戶登入) 時，會要求僅限 Azure AD 範圍的情況。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 如果 `response_type` 包含 `id_token` 則納入。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取並顯示這些值，但不應依賴這些值來取得任何授權或安全性界限。 如需 id_tokens 的詳細資訊，請參閱[ `id_token` 參考](id-tokens.md)。 <br> **注意：** 只在要求 `openid` 範圍時提供。 |
| `state` |如果要求中包含 state 參數，則回應中應該會出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理。 如果是 `prompt=none`，預期的錯誤為：

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 參數 | 描述 |
| --- | --- |
| `error` |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

如果您在 iframe 要求中收到此錯誤，使用者必須再次以互動方式登入以擷取新的權杖。 您可以選擇對於您的應用程式合理的任何方式處理這種情況。

## <a name="refreshing-tokens"></a>重新整理權杖

隱含授與不提供重新整理權杖。 `id_token` 和 `access_token` 馬上就會到期，因此您的應用程式必須準備好定期重新整理這些權杖。 若要重新整理任一類型的權杖，您可以使用 `prompt=none` 參數來控制身分識別平臺的行為，以執行上述的相同隱藏 iframe 要求。 如果您想要接收新的 `id_token` ，請務必 `id_token` 在和中使用 `response_type` `scope=openid` ，以及 `nonce` 參數。

在不支援協力廠商 cookie 的瀏覽器中，這會導致錯誤，指出沒有任何使用者登入。 

## <a name="send-a-sign-out-request"></a>傳送登出要求

此 OpenID Connect `end_session_endpoint` 可讓您的應用程式將要求傳送至 microsoft 身分識別平臺，以結束使用者的會話，並清除 Microsoft 身分識別平臺所設定的 cookie。 若要將使用者從 Web 應用程式完全登出，您的應用程式應結束自己和使用者之間的工作階段 (通常是透過清除權杖快取或卸除 Cookie)，然後將瀏覽器重新導向至：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 參數 | 類型 | 描述 |
| --- | --- | --- |
| `tenant` |required |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `post_logout_redirect_uri` | 建議使用 | 使用者在完成登出之後應該要返回的 URL。 這個值必須符合為應用程式註冊的其中一個重新導向 URI。 如果未包含，Microsoft 身分識別平臺將會顯示一般訊息給使用者。 |

## <a name="next-steps"></a>後續步驟

* 瀏覽所有 [MSAL JS 範例](sample-v2-code.md)以開始撰寫程式碼。
* 請檢查 [授權碼流程](v2-oauth2-auth-code-flow.md) ，作為隱含授與的較新、更好的替代方案。 
