---
title: OAuth 2.0 隱含授與流程-Microsoft 身分識別平臺 |Azure
description: 使用 Microsoft 身分識別平臺隱含流程保護單一頁面應用程式。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: fbe74b62352babf7a1fdd93bf19a6e1475e3f032
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85553585"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft 身分識別平臺和隱含授與流程

透過 Microsoft 身分識別平臺端點，您可以使用 Microsoft 的個人和公司或學校帳戶，將使用者登入單一頁面應用程式。 主要於瀏覽器中執行的單頁及 JavaScript 應用程式在驗證時會面臨一些有趣的挑戰：

* 這些應用程式的安全性特性與傳統伺服器型 Web 應用程式大不相同。
* 許多授權伺服器及識別提供者不支援 CORS 要求。
* 重新導向離開應用程式的完整網頁瀏覽器變得對使用者經驗特別有侵入性。

針對這些應用程式（角度、Ember.js、React.js 等等），Microsoft 身分識別平臺支援 OAuth 2.0 隱含授與流程。 隱含流程相關說明，請參閱 [OAuth 2.0 規格](https://tools.ietf.org/html/rfc6749#section-4.2)。 其主要優點是它可讓應用程式從 Microsoft 身分識別平臺取得權杖，而不需要執行後端伺服器認證交換。 這可讓應用程式在用戶端 JavaScript 程式碼內將使用者登入、維護工作階段，並取得其他 Web API 的權杖。 使用隱含流程時需注意幾個重要的安全性考量，特別是[用戶端](https://tools.ietf.org/html/rfc6749#section-10.3)及[使用者模擬](https://tools.ietf.org/html/rfc6749#section-10.3)。

本文說明如何在您的應用程式中直接針對通訊協定進行程式設計。  可能的話，建議您改為使用支援的 Microsoft 驗證程式庫 (MSAL)，以[取得權杖並呼叫受保護的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另請參閱[使用 MSAL 的範例應用程式](sample-v2-code.md)。

但如果您不想在單頁應用程式中使用程式庫，也不想自行傳送通訊協定訊息，請遵循下列一般步驟。

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 隱含授與的適用案例

OAuth2 規格聲明設計出隱含授與是為了實現使用者代理程式應用程式，也就是在瀏覽器內執行的 JavaScript 應用程式。 這類應用程式的定義特性，就是使用 JavaScript 程式碼來存取伺服器資源（通常是 Web API），並據以更新應用程式使用者體驗。 請想想 Gmail 或 Outlook Web Access 之類的應用程式︰當您選取收件匣中的訊息時，只有訊息視覺化面板會變更以顯示新的選取內容，該頁面的其餘部分則維持不變。 這個特徵明顯不同於傳統的重新導向型 Web 應用程式，在後者中，每個使用者互動都會造成整頁回傳，並針對整頁轉譯做出新的伺服器回應。

採用極端 JavaScript 型方法的應用程式稱為單一頁面應用程式，或 SPA。 其概念是，這些應用程式只會提供初始的 HTML 網頁和相關聯的 JavaScript，而所有後續的互動都是由透過 JavaScript 所執行的 Web API 呼叫所驅動。 不過，應用程式大多是由回傳所驅動，但偶爾會執行 JS 呼叫的混合式方法也並非罕見；關於隱含流程使用的討論也與這些方法有關。

重新導向型應用程式通常會透過 Cookie 來保護其要求，不過，這種方法並非也適用於 JavaScript 應用程式。 Cookie 只會針對其產生網域發生作用，但 JavaScript 呼叫卻可能導向到其他網域。 事實上，情況往往是如此︰請想想叫用 Microsoft Graph API、Office API、Azure API 的應用程式，這些應用程式全都位於提供應用程式的網域之外。 JavaScript 應用程式的成長趨勢是完全沒有後端，依賴協力廠商 web Api 的100% 來執行其商務功能。

目前，保護 Web API 呼叫的慣用方法是使用 OAuth2 持有人權杖方法，其中每個呼叫都會伴隨 OAuth2 存取權杖。 Web API 會檢查傳入存取權杖，如果它在其中找到必要的範圍，就會授與要求作業的存取權。 隱含流程提供便利的機制，讓 JavaScript 應用程式取得 Web API 的存取權杖，並提供與 cookie 相關的許多優點：

* 可以可靠地取得權杖而不需要跨原始來源呼叫 – 強制註冊權杖要傳回到的重新導向 URI 可保證權杖不會移到其他位置
* JavaScript 應用程式可以取得所需數量的存取權杖，適用于其目標數目最多的 web Api，而不受網域限制
* 工作階段或本機儲存體等 HTML5 功能可授與權杖快取和存留期管理的完全控制權，但是應用程式則無法處理 Cookie 管理
* 存取權杖不容易遭受跨網站偽造要求（CSRF）攻擊

隱含授與流程不會簽發重新整理權杖，其原因大多是安全性考量。 重新整理權杖的範圍不會縮小為存取權杖，因此，授與更多的能力，因此 inflicting 會因為洩漏而有更大的損害。在隱含流程中，權杖會在 URL 中傳遞，因此攔截的風險會高於授權碼授與。

不過，JavaScript 應用程式可以選擇使用另一種機制來更新存取權杖，而不會重複提示使用者提供認證。 應用程式可以使用隱藏的 iframe 來針對 Azure AD 的授權端點執行新的權杖要求︰只要瀏覽器仍有針對 Azure AD 網域作用的工作階段 (read: 有工作階段 Cookie)，驗證要求就可以順利執行而不需要使用者互動。

此模型會對 JavaScript 應用程式授與能力，使其能夠獨立更新存取權杖，甚至是取得新 API 的新存取權杖 (前提是使用者已同意)。 這可避免取得、維護和保護高價值構件 (例如重新整理權杖) 的額外負擔。 讓無訊息更新得以實現的成品 (Azure AD 工作階段 Cookie) 是在應用程式之外進行管理的。 這種方法的另一個優點是使用者可以使用任何已登入 Azure AD，並在任何瀏覽器索引標籤中執行的應用程式，從 Azure AD 登出。 這會導致 Azure AD 工作階段 Cookie 遭到刪除，而且 JavaScript 應用程式會自動失去為已登出使用者更新權杖的能力。

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>我的應用程式適用隱含授與嗎？

隱含授與比其他授與帶來更多風險，您需要注意的區域已有詳細記錄 (例如，[在隱含流程中誤用存取權杖來模擬資源擁有者][OAuth2-Spec-Implicit-Misuse]和 [OAuth 2.0 威脅模型和安全性考量][OAuth2-Threat-Model-And-Security-Implications])。 不過，風險概況之所以較高，主要是因為它要啟用執行作用中程式碼的應用程式，並由遠端資源提供給瀏覽器。 如果您要規劃 SPA 架構、沒有後端元件，或想要透過 JavaScript 叫用 Web API，建議使用隱含流程來取得權杖。

如果您的應用程式是原生用戶端，則隱含流程並不適合。 原生用戶端環境中沒有Azure AD 工作階段 Cookie，將會讓應用程式沒有辦法維持長時間執行的工作階段。 這表示應用程式在取得新資源的存取權杖時會重複提示使用者。

如果您要開發含有後端的 Web 應用程式，並從它的後端程式碼取用 API，則也不適用隱含流程。 其他授與可提供您更多權力。 例如，OAuth2 用戶端認證授與可讓您取得權杖以反映指派給應用程式本身 (而不是使用者委派) 的權限。 這表示用戶端可在使用者不積極參與工作階段的情況下也能維護資源的程式設計存取等等。 不只如此，這類授與也能提供較高的安全性保證。 比方說，存取權杖永遠不會透過使用者瀏覽器傳輸，也不會有儲存在瀏覽器歷程記錄中的風險，依此類推。 用戶端應用程式也可以在要求權杖時執行增強式驗證。

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>通訊協定圖表

下圖顯示整個隱含登入流程的概況，接下來的各節會更詳細地說明每個步驟。

![顯示隱含登入流程的圖表](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>傳送登入要求

若要一開始將使用者登入您的應用程式，您可以傳送[OpenID connect](v2-protocols-oidc.md)驗證要求，並 `id_token` 從 Microsoft 身分識別平臺端點取得。

> [!IMPORTANT]
> 若要成功要求識別碼權杖和（或）存取權杖，在 [ [Azure 入口網站應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)] 頁面中的應用程式註冊必須已啟用對應的隱含授與流程，方法是選取 [**識別碼權杖**] 和 [] 或 [**隱含授**與] 區段下的 [**存取權杖**]。 如果未啟用，則 `unsupported_response` 會傳回錯誤：**此用戶端不允許輸入參數 ' Response_type ' 所提供的值。預期的值為 ' code '**

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
> 若要使用隱含流程來測試登入，請按一下<a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. 。</a>登入之後，您的瀏覽器應該會重新導向至 `https://localhost/myapp/` `id_token` 網址列中的。
>

| 參數 | 類型 | 描述 |
| --- | --- | --- |
| `tenant` | required |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | required | [Azure 入口網站 - 應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁面指派給您應用程式的應用程式 (用戶端) 識別碼。 |
| `response_type` | 必要 |必須包含 OpenID Connect 登入的 `id_token` 。 它也可能包含 response_type `token`。 這裡使用 `token` ，讓您的應用程式能夠立即從授權端點接收存取權杖，而不需要向授權端點進行第二次要求。 如果您使用 `token` response_type， `scope` 參數必須包含範圍，以指出要對哪個資源發出權杖（例如，使用者. 讀取 Microsoft Graph 上的）。  |
| `redirect_uri` | 建議使用 |應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。 |
| `scope` | 必要 |以空格分隔的[範圍](v2-permissions-and-consent.md)清單。 針對 OpenID Connect （id_tokens），它必須包含範圍 `openid` ，這會轉譯為同意 UI 中的「登入」許可權。 （選擇性）您也可以包含 `email` 和範圍，以 `profile` 取得其他使用者資料的存取權。 如果要求存取權杖，您也可以在此要求中包含其他範圍，要求同意各種資源。 |
| `response_mode` | 選用 |指定應該用來將所產生權杖傳回給應用程式的方法。 預設為僅查詢存取權杖，但如果要求包含 id_token，則為片段。 |
| `state` | 建議使用 |同樣會隨權杖回應傳回之要求中所包含的值。 其可以是您想要之任何內容的字串。 隨機產生的唯一值通常用於 [防止跨站台要求偽造攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此狀態也可用來在驗證要求發生之前，將使用者狀態的相關資訊編碼，例如他們所在的頁面或檢視。 |
| `nonce` | 必要 |包含在要求中的值 (由應用程式產生)，該值將會以宣告的形式包含在產生的 id_token 中。 然後，應用程式可以驗證此值，以減輕權杖重新執行所造成的攻擊。 此值通常是隨機的唯一字串，可以用來識別要求的來源。 只有在要求 id_token 時才需要。 |
| `prompt` | 選用 |表示需要的使用者互動類型。 目前的有效值只有 'login'、'none'、'select_account' 和 'consent'。 `prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。 `prompt=none` 則相反，其會確保使用者不會看到任何互動式提示。 如果要求無法透過單一登入以無訊息方式完成，Microsoft 身分識別平臺端點會傳回錯誤。 `prompt=select_account` 會將使用者傳送至帳戶選擇器，工作階段中記下的所有帳戶都會出現在當中。 `prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| `login_hint`  |選用 |如果您事先知道使用者的使用者名稱，可以用此項目來預先填入使用者登入頁面上的使用者名稱/電子郵件地址欄位。 通常應用程式會在重新驗證期間使用此參數，已經使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。|
| `domain_hint` | 選用 |如果包含，它會略過使用者在登入頁面上經歷的以電子郵件為基礎的探索程式，進而提升使用者體驗的效率。 這通常用於在單一租使用者中運作的企業營運應用程式，在此情況下，它們會在指定的租使用者內提供功能變數名稱。  這會將使用者轉送至該租使用者的同盟提供者。  請注意，這會導致來賓無法登入此應用程式。  |

此時，會要求使用者輸入其認證並完成驗證。 Microsoft 身分識別平台端點也會確認使用者已經同意 `scope` 查詢參數所指出的權限。 如果使用者**完全未**同意這些權限的任一項，其會要求使用者同意必要權限。 如需詳細資訊，請參閱[權限、同意及多租用戶應用程式](v2-permissions-and-consent.md)。

一旦使用者驗證並同意，Microsoft 身分識別平台端點就會使用 `response_mode` 參數中指定的方法，將回應傳回至位於指定 `redirect_uri` 所在的應用程式。

#### <a name="successful-response"></a>成功回應

使用 `response_mode=fragment` 及 `response_type=id_token+token` 的成功回應如下所示 (內含分行符號以利閱讀)：

```HTTP
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 參數 | 描述 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖。 存取權杖不應解碼或檢查，應視為不透明的字串。 |
| `token_type` |如果 `response_type` 包含 `token` 則納入。 一律為 `Bearer`。 |
| `expires_in`|如果 `response_type` 包含 `token` 則納入。 指出權杖有效的秒數，以供快取之用。 |
| `scope` |如果 `response_type` 包含 `token` 則納入。 表示 access_token 適用的範圍。 可能不會包含所有要求的範圍（如果它們不適用使用者）（如果是使用個人帳戶登入時要求的僅限 Azure AD 範圍）。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取並顯示值，但不應依賴它們來取得任何授權或安全性界限。 如需有關 id_token 的詳細資訊，請參閱 [`id_token reference`](id-tokens.md)。 <br> **注意：** 只在要求 `openid` 範圍時提供。 |
| `state` |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

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

現在您已將使用者登入單頁應用程式，您可以用無訊息方式取得存取權杖，以呼叫受 Microsoft 身分識別平臺保護的 web Api，例如[Microsoft Graph](https://developer.microsoft.com/graph)。 即使您已經收到使用 `token` response_type 的權杖，仍可使用此方法來取得其他資源的權杖，而不需重新導向使用者進行再次登入。

在一般 OpenID Connect/OAuth 流程中，您可以藉由向 Microsoft 身分識別平臺端點提出要求來執行此動作 `/token` 。 不過，Microsoft 身分識別平臺端點不支援 CORS 要求，因此，取得和重新整理權杖的 AJAX 呼叫不會發生問題。 相反地，您可以在隱藏的 iframe 中使用隱含流程，為其他 Web API 取得新權杖：

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

由於 `prompt=none` 參數，此要求會立即成功或失敗，並且傳回給您的應用程式。 會使用 `response_mode` 參數中指定的方法，將成功的回應傳送至指定的 `redirect_uri` 給您的應用程式。

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
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖，在此案例中為 Microsoft Graph 的存取權杖。 存取權杖不應解碼或檢查，應視為不透明的字串。 |
| `token_type` | 一律為 `Bearer`。 |
| `expires_in` | 指出權杖有效的秒數，以供快取之用。 |
| `scope` | 表示 access_token 適用的範圍。 可能不會包含所有要求的範圍（如果它們不適用使用者）（如果是使用個人帳戶登入時要求的僅限 Azure AD 範圍）。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 如果 `response_type` 包含 `id_token` 則納入。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取並顯示值，但不應依賴它們來取得任何授權或安全性界限。 如需 id_tokens 的詳細資訊，請參閱[ `id_token` 參考](id-tokens.md)。 <br> **注意：** 只在要求 `openid` 範圍時提供。 |
| `state` |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

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

隱含授與不提供重新整理權杖。 `id_token` 和 `access_token` 馬上就會到期，因此您的應用程式必須準備好定期重新整理這些權杖。 若要重新整理任一類型的權杖，您可以使用參數來執行相同的隱藏 iframe 要求， `prompt=none` 以控制身分識別平臺的行為。 如果您想要接收新的 `id_token` ，請務必 `id_token` 在和中使用 `response_type` `scope=openid` ，以及 `nonce` 參數。

## <a name="send-a-sign-out-request"></a>傳送登出要求

OpenID Connect `end_session_endpoint` 可讓您的應用程式將要求傳送至 microsoft 身分識別平臺端點，以結束使用者的會話，並清除由 Microsoft 身分識別平臺端點設定的 cookie。 若要將使用者從 Web 應用程式完全登出，您的應用程式應結束自己和使用者之間的工作階段 (通常是透過清除權杖快取或卸除 Cookie)，然後將瀏覽器重新導向至：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 參數 | 類型 | Description |
| --- | --- | --- |
| `tenant` |required |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `post_logout_redirect_uri` | 建議使用 | 使用者在完成登出之後應該要返回的 URL。 這個值必須符合為應用程式註冊的其中一個重新導向 URI。 如果未包含，則會由 Microsoft 身分識別平臺端點向使用者顯示一般訊息。 |

## <a name="next-steps"></a>後續步驟

* 瀏覽所有 [MSAL JS 範例](sample-v2-code.md)以開始撰寫程式碼。

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
