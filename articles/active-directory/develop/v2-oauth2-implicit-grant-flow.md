---
title: OAuth 2.0 隱式授予流 - 微軟身份平臺 |蔚藍
description: 使用 Microsoft 標識平臺隱式流保護單頁應用。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 6e3f021fd888bbb408fa66964c54d22f0d68e84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297686"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>微軟標識平臺和隱式授予流

使用 Microsoft 標識平臺終結點，您可以使用 Microsoft 的個人和工作帳戶或學校帳戶將使用者登錄到單頁應用。 主要於瀏覽器中執行的單頁及 JavaScript 應用程式在驗證時會面臨一些有趣的挑戰：

* 這些應用程式的安全性特性與傳統伺服器型 Web 應用程式大不相同。
* 許多授權伺服器及識別提供者不支援 CORS 要求。
* 重新導向離開應用程式的完整網頁瀏覽器變得對使用者經驗特別有侵入性。

對於這些應用程式（AngularJS、Ember.js、React.js 等），Microsoft 標識平臺支援 OAuth 2.0 隱式授予流。 隱含流程相關說明，請參閱 [OAuth 2.0 規格](https://tools.ietf.org/html/rfc6749#section-4.2)。 它的主要好處是，它允許應用從 Microsoft 標識平臺獲取權杖，而無需執行後端伺服器憑據交換。 這可讓應用程式登入使用者、維護工作階段，並且取得用戶端 JavaScript 程式碼中所有其他 Web API 的權杖。 使用隱含流程時需注意幾個重要的安全性考量，特別是[用戶端](https://tools.ietf.org/html/rfc6749#section-10.3)及[使用者模擬](https://tools.ietf.org/html/rfc6749#section-10.3)。

本文介紹如何直接針對應用程式中的協定進行程式設計。  如果可能，我們建議您使用受支援的 Microsoft 身份驗證庫 （MSAL） 來[獲取權杖並調用安全的 Web API。](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)  也看看[使用MSAL的應用程式範例](sample-v2-code.md)。

但如果您不想在單頁應用程式中使用程式庫，也不想自行傳送通訊協定訊息，請遵循下列一般步驟。

> [!NOTE]
> 並非所有 Azure 活動目錄 （Azure AD） 方案和功能都由 Microsoft 標識平臺終結點提供支援。 要確定是否應使用 Microsoft 標識平臺終結點，請閱讀有關[Microsoft 標識平臺限制](active-directory-v2-limitations.md)。

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 隱含授與的適用案例

OAuth2 規格聲明設計出隱含授與是為了實現使用者代理程式應用程式，也就是在瀏覽器內執行的 JavaScript 應用程式。 這類應用程式的鮮明特徵是，JavaScript 程式碼可用於存取伺服器資源 (通常是 Web API)，並據以更新應用程式使用者體驗。 請想想 Gmail 或 Outlook Web Access 之類的應用程式︰當您選取收件匣中的訊息時，只有訊息視覺化面板會變更以顯示新的選取內容，該頁面的其餘部分則維持不變。 這個特徵明顯不同於傳統的重新導向型 Web 應用程式，在後者中，每個使用者互動都會造成整頁回傳，並針對整頁轉譯做出新的伺服器回應。

採用極端 JavaScript 型方法的應用程式稱為單一頁面應用程式，或 SPA。 其概念是，這些應用程式只會提供初始的 HTML 網頁和相關聯的 JavaScript，至於所有後續的互動，則由透過 JavaScript 所執行的 Web API 呼叫來推動。 不過，應用程式大多是由回傳所驅動，但偶爾會執行 JS 呼叫的混合式方法也並非罕見；關於隱含流程使用的討論也與這些方法有關。

重新導向型應用程式通常會透過 Cookie 來保護其要求，不過，這種方法並非也適用於 JavaScript 應用程式。 Cookie 只會針對其產生網域發生作用，但 JavaScript 呼叫卻可能導向到其他網域。 事實上，情況往往是如此︰請想想叫用 Microsoft Graph API、Office API、Azure API 的應用程式，這些應用程式全都位於提供應用程式的網域之外。 JavaScript 應用程式的發展趨勢是完全沒有後端，全部依靠協力廠商 Web API 來實作其商務功能。

目前，保護 Web API 呼叫的慣用方法是使用 OAuth2 持有人權杖方法，在此方法中，每個呼叫都會伴隨一個 OAuth2 存取權杖。 Web API 會檢查傳入的存取權杖，而且如果它在權杖中找到所需的範圍，便會授與所要求作業的存取權。 隱含流程提供了方便的機制供 JavaScript 應用程式取得 Web API 的存取權杖，並提供很多關於 Cookie 的優點︰

* 可以可靠地取得權杖而不需要跨原始來源呼叫 – 強制註冊權杖要傳回到的重新導向 URI 可保證權杖不會移到其他位置
* JavaScript 應用程式可以針對任意數量的鎖定 Web API 取得所需數量的存取權杖 – 不限網域
* 工作階段或本機儲存體等 HTML5 功能可授與權杖快取和存留期管理的完全控制權，但是應用程式則無法處理 Cookie 管理
* 存取權杖不容易遭受跨網站偽造要求 (CSRF) 攻擊

隱含授與流程不會簽發重新整理權杖，其原因大多是安全性考量。 刷新權杖不像訪問權杖那樣狹窄，因此授予更多的權力，因此在洩露出去時會造成更大的傷害。在隱式流中，權杖在 URL 中傳遞，因此攔截的風險高於授權代碼授予的風險。

不過，JavaScript 應用程式可以選擇使用另一種機制來更新存取權杖，而不會重複提示使用者提供認證。 應用程式可以使用隱藏的 iframe 來針對 Azure AD 的授權端點執行新的權杖要求︰只要瀏覽器仍有針對 Azure AD 網域作用的工作階段 (read: 有工作階段 Cookie)，驗證要求就可以順利執行而不需要使用者互動。

此模型會對 JavaScript 應用程式授與能力，使其能夠獨立更新存取權杖，甚至是取得新 API 的新存取權杖 (前提是使用者已同意)。 這可避免取得、維護和保護高價值構件 (例如重新整理權杖) 的額外負擔。 讓無訊息更新得以實現的成品 (Azure AD 工作階段 Cookie) 是在應用程式之外進行管理的。 這種方法的另一個優點是使用者可以使用任何已登入 Azure AD，並在任何瀏覽器索引標籤中執行的應用程式，從 Azure AD 登出。 這會導致 Azure AD 工作階段 Cookie 遭到刪除，而且 JavaScript 應用程式會自動失去為已登出使用者更新權杖的能力。

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>我的應用程式適用隱含授與嗎？

隱含授與比其他授與帶來更多風險，您需要注意的區域已有詳細記錄 (例如，[在隱含流程中誤用存取權杖來模擬資源擁有者][OAuth2-Spec-Implicit-Misuse]和 [OAuth 2.0 威脅模型和安全性考量][OAuth2-Threat-Model-And-Security-Implications])。 不過，風險概況之所以較高，主要是因為它要啟用執行作用中程式碼的應用程式，並由遠端資源提供給瀏覽器。 如果您正在規劃 SPA 架構，沒有後端元件或想要透過 JavaScript 叫用 Web API，則建議使用隱含流程來取得權杖。

如果應用程式是原生用戶端，則不適用隱含流程。 原生用戶端環境中沒有Azure AD 工作階段 Cookie，將會讓應用程式沒有辦法維持長時間執行的工作階段。 這表示應用程式在取得新資源的存取權杖時會重複提示使用者。

如果您要開發含有後端的 Web 應用程式，並從它的後端程式碼取用 API，則也不適用隱含流程。 其他授與可提供您更多權力。 例如，OAuth2 用戶端認證授與可讓您取得權杖以反映指派給應用程式本身 (而不是使用者委派) 的權限。 這表示用戶端可在使用者不積極參與工作階段的情況下也能維護資源的程式設計存取等等。 不只如此，這類授與也能提供較高的安全性保證。 例如，存取權杖永遠不會透過使用者瀏覽器來傳輸，它們不會因為儲存在瀏覽器歷程記錄中而發生危險等等。 用戶端應用程式也可以在要求權杖時執行增強式驗證。

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>通訊協定圖表

下圖說明整個隱含登入流程，而後續幾節會更詳細地說明每個步驟。

![顯示隱式登錄流的圖表](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>傳送登入要求

要最初將使用者登錄到你的應用，可以發送[OpenID 連接](v2-protocols-oidc.md)身份驗證請求並從 Microsoft`id_token`標識平臺終結點獲取 。

> [!IMPORTANT]
> 要成功請求 ID 權杖和/或訪問權杖[，Azure 門戶中的應用註冊 - 應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁必須啟用相應的隱式授予流，請在 **"隱式授予**"部分下選擇**ID 權杖**和.或**訪問權杖**。 如果未啟用，將返回錯誤`unsupported_response`：**此用戶端不允許輸入參數"response_type"的提供值。預期值為"代碼"**

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
> 要使用隱式流測試登錄，請按一下<a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize..。</a>登錄後，您的瀏覽器應重定向到`https://localhost/myapp/`網址列中的 。 `id_token`
>

| 參數 |  | 描述 |
| --- | --- | --- |
| `tenant` | required |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | required | Azure 門戶的應用程式（用戶端）ID - 分配給應用[的應用註冊](https://go.microsoft.com/fwlink/?linkid=2083908)頁。 |
| `response_type` | required |必須包含 OpenID Connect 登入的 `id_token` 。 它也可能包含 response_type `token`。 這裡使用 `token` ，讓您的應用程式能夠立即從授權端點接收存取權杖，而不需要向授權端點進行第二次要求。 如果使用response_type，`token``scope`則參數必須包含一個作用域，指示要為其頒發權杖（例如，在 Microsoft 圖形上讀取使用者.read）。  |
| `redirect_uri` | 建議使用 |應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。 |
| `scope` | required |[空間分隔的範圍](v2-permissions-and-consent.md)清單。 對於 OpenID 連接（id_tokens），它必須包括範圍`openid`，該範圍將轉換為"登錄"許可權在同意 UI 中。 或者，您可能還希望包括 用於獲取其他使用者`email`資料`profile`存取權限的 和 作用域。 如果請求訪問權杖，則在此請求中還可以包括請求同意各種資源的其他作用域。 |
| `response_mode` | 選用 |指定將產生的權杖送回到應用程式所應該使用的方法。 預設僅查詢訪問權杖，但如果請求包含id_token，則進行片段查詢。 |
| `state` | 建議使用 |同樣會隨權杖回應傳回之要求中所包含的值。 其可以是您想要之任何內容的字串。 隨機生成的唯一值通常用於[防止跨網站請求偽造攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此狀態也用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| `nonce` | required |由應用程式產生且包含在要求中的值，會以宣告方式包含在產生的 id_token 中。 應用程式接著便可確認此值，以減少權杖重新執行攻擊。 此值通常是隨機的唯一字串，可用以識別要求的來源。 只有要求 id_token 時，才需要此值。 |
| `prompt` | 選用 |表示需要的使用者互動類型。 目前的有效值只有 'login'、'none'、'select_account' 和 'consent'。 `prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。 `prompt=none`正好相反 - 它將確保使用者不會收到任何互動式提示。 如果無法通過單一登入以靜默方式完成請求，則 Microsoft 標識平臺終結點將返回錯誤。 `prompt=select_account` 會將使用者傳送至帳戶選擇器，工作階段中記下的所有帳戶都會出現在當中。 `prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| `login_hint`  |選用 |如果您事先知道其使用者名稱，可用來預先填入使用者登入頁面的使用者名稱/電子郵件地址欄位。 通常應用程式會在重新驗證期間使用此參數，已經使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。|
| `domain_hint` | 選用 |如果包括，它將跳過使用者在登錄頁面上經歷的基於電子郵件的發現過程，從而獲得稍微簡化的使用者體驗。 這通常用於在單個租戶中運行的業務線應用，這些應用將在給定租戶中提供功能變數名稱。  這將將使用者轉發到該租戶的聯合提供程式。  請注意，這將阻止來賓登錄到此應用程式。  |

此時，會要求使用者輸入其認證並完成驗證。 Microsoft 標識平臺終結點還將確保使用者已同意`scope`查詢參數中指示的許可權。 如果使用者**完全未**同意這些權限的任一項，其會要求使用者同意必要權限。 如需詳細資訊，請參閱[權限、同意及多租用戶應用程式](v2-permissions-and-consent.md)。

使用者進行身份驗證並授予同意後，Microsoft 標識平臺終結點將使用參數中指定的方法在指定的`redirect_uri`處返回對應用的`response_mode`回應。

#### <a name="successful-response"></a>成功回應

使用 `response_mode=fragment` 及 `response_type=id_token+token` 的成功回應如下所示 (內含分行符號以利閱讀)：

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 參數 | 描述 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖。 不應解碼或以其他方式檢查訪問權杖，應將其視為不透明字串。 |
| `token_type` |如果 `response_type` 包含 `token` 則納入。 一律為 `Bearer`。 |
| `expires_in`|如果 `response_type` 包含 `token` 則納入。 表示權杖有效的秒數 (針對快取目的)。 |
| `scope` |如果 `response_type` 包含 `token` 則納入。 表示在 access_token 的有效範圍。 如果請求的所有作用域不適用於使用者（在使用個人帳戶登錄時請求 Azure AD 作用域的情況下），則可能不包括所請求的所有作用域。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用可以緩存值並顯示它們，但它不應依賴它們來達到任何授權或安全邊界。 有關id_tokens的詳細資訊，請參閱 。 [`id_token reference`](id-tokens.md) <br> **注意：** 只在要求 `openid` 範圍時提供。 |
| `state` |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理：

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
| --- | --- |
| `error` |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

## <a name="getting-access-tokens-silently-in-the-background"></a>在後臺靜默獲取訪問權杖

現在，您已將使用者登錄到單頁應用，您可以靜默獲取用於調用 Microsoft 標識平臺（如[Microsoft Graph](https://developer.microsoft.com/graph)）保護的 Web API 的訪問權杖。 即使您已經收到使用 `token` response_type 的權杖，仍可使用此方法來取得其他資源的權杖，而不需重新導向使用者進行再次登入。

在正常的 OpenID 連接/OAuth 流中，可以通過向 Microsoft 標識平臺`/token`終結點發出請求來執行此操作。 但是，Microsoft 標識平臺終結點不支援 CORS 請求，因此，進行 AJAX 調用以獲取和刷新權杖是不可能的。 相反地，您可以在隱藏的 iframe 中使用隱含流程，為其他 Web API 取得新權杖： 

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

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| 參數 | 描述 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖，在此案例中為 Microsoft Graph 的存取權杖。 不應解碼或以其他方式檢查訪問權杖，應將其視為不透明字串。 |
| `token_type` | 一律為 `Bearer`。 |
| `expires_in` | 表示權杖有效的秒數 (針對快取目的)。 |
| `scope` | 表示在 access_token 的有效範圍。 如果請求的所有作用域不適用於使用者（在使用個人帳戶登錄時請求 Azure AD 作用域的情況下），則可能不包括所請求的所有作用域。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 如果 `response_type` 包含 `id_token` 則納入。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用可以緩存值並顯示它們，但它不應依賴它們來達到任何授權或安全邊界。 有關id_tokens的詳細資訊，請參閱[`id_token`引用](id-tokens.md)。 <br> **注意：** 只在要求 `openid` 範圍時提供。 |
| `state` |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理。 如果是 `prompt=none`，預期的錯誤為：

```
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

隱含授與不提供重新整理權杖功能。 `id_token` 和 `access_token` 馬上就會到期，因此您的應用程式必須準備好定期重新整理這些權杖。 要刷新任一類型的權杖，可以使用`prompt=none`參數從上面執行相同的隱藏 iframe 請求來控制標識平臺的行為。 `id_token`如果要接收新 ，請確保在`id_token``response_type`和`scope=openid`以及`nonce`參數中使用。

## <a name="send-a-sign-out-request"></a>傳送登出要求

OpenID 連接`end_session_endpoint`允許你的應用向 Microsoft 標識平臺終結點發送請求，以結束使用者的會話，並清除由 Microsoft 標識平臺終結點設置的 Cookie。 若要將使用者從 Web 應用程式完全登出，您的應用程式應結束自己和使用者之間的工作階段 (通常是透過清除權杖快取或卸除 Cookie)，然後將瀏覽器重新導向至：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 參數 |  | 描述 |
| --- | --- | --- |
| `tenant` |required |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `post_logout_redirect_uri` | 建議使用 | 使用者在完成登出之後應該要返回的 URL。 這個值必須符合為應用程式註冊的其中一個重新導向 URI。 如果未包括，則 Microsoft 標識平臺終結點將顯示一條通用消息。 |

## <a name="next-steps"></a>後續步驟

* 瀏覽所有 [MSAL JS 範例](sample-v2-code.md)以開始撰寫程式碼。
