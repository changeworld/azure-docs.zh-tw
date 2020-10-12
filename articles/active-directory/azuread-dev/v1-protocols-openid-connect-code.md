---
title: 使用 OpenID Connect & Azure AD 授權 web 應用程式存取 |Microsoft Docs
description: 本文章說明如何使用 HTTP 訊息來使用 Azure Active Directory 和 OpenID Connect 授權存取您的租用戶中的 Web 應用程式和 Web API。
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: b719e866852d2e865c16c62fddd8c549ae505b7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85551559"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>使用 OpenID Connect 和 Azure Active Directory 授權存取 Web 應用程式

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 是以 OAuth 2.0 通訊協定為建置基礎的簡單身分識別層。 OAuth 2.0 定義的機制可以取得及使用[**存取權杖**](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)來存取受保護的資源，但它們不會定義提供身分識別資訊的標準方法。 OpenID Connect 實作驗證來做為 OAuth 2.0 的授權程序的擴充。 它會以的形式提供終端使用者的相關資訊 [`id_token`](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) ，以驗證使用者的身分識別，並提供使用者的基本設定檔資訊。

如果您要建置的 Web 應用程式是裝載於伺服器且透過瀏覽器存取，建議使用 OpenID Connect。

## <a name="register-your-application-with-your-ad-tenant"></a>向 AD 租用戶註冊應用程式
首先，向您的 Azure Active Directory (Azure AD) 租使用者註冊您的應用程式。 這會讓應用程式獲得應用程式識別碼，以及讓它可以接收權杖。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
   
1. 在頁面右上角選取您的帳戶來選擇您的 Azure AD 租使用者，然後選取 [ **切換目錄** ] 流覽，然後選取適當的租使用者。 
   - 如果您的帳戶下只有一個 Azure AD 租使用者，或是您已選取適當的 Azure AD 租使用者，請略過此步驟。
   
1. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]。
   
1. 在 **Azure Active Directory** 左側功能表中，選取 [ **應用程式註冊**]，然後選取 [ **新增註冊**]。
   
1. 遵循提示並建立新的應用程式。 在本教學課程中，它是 web 應用程式還是公用用戶端 (行動 & 桌面) 應用程式並不重要，但如果您想要 web 應用程式或公用用戶端應用程式的特定範例，請查看我們的 [快速入門](v1-overview.md)。
   
   - [名稱]**** 為應用程式名稱，並能向使用者描述您的應用程式。
   - 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶及個人的 Microsoft 帳戶]。
   - 提供重新 **導向 URI**。 針對 web 應用程式，這是使用者可以登入之應用程式的基底 URL。  例如： `http://localhost:12345` 。 若為公用用戶端 (mobile & desktop) ，Azure AD 會使用它來傳回權杖回應。 輸入應用程式特定的值。  例如： `http://MyFirstAADApp` 。
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. 當您完成註冊之後，Azure AD 會為應用程式指派一個唯一的用戶端識別碼， (**應用程式** 識別碼) 。 您會在後續章節中用到這個值，所以請從應用程式頁面中複製此值。
   
1. 若要在 Azure 入口網站中尋找您的應用程式，請選取 [ **應用程式註冊**]，然後選取 [ **查看所有應用程式**]。

## <a name="authentication-flow-using-openid-connect"></a>使用 OpenID Connect 驗證流程

最基本的登入流程包含下列步驟 - 以下將詳細說明每一個步驟。

![OpenId Connect 驗證流程](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect 中繼資料文件

OpenID Connect 所描述的中繼資料文件包含應用程式執行登入所需的大部分資訊。 這包括要使用的 URL、服務的公開簽署金鑰位置等資訊。 可在此找到 OpenID Connect 中繼資料文件：

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
中繼資料是簡單的「JavaScript 物件標記法」(JSON) 文件。 如需範例，請參閱下列程式碼片段。 [OpenID Connect 規格](https://openid.net)中有程式碼片段內容的完整說明。 請注意，提供租使用者識別碼而不是 `common` 取代上述的 {tenant}，會導致傳回 JSON 物件中的租使用者特定 uri。

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

如果您的應用程式因使用 [claims-mapping](../develop/active-directory-claims-mapping.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 功能而具有自訂簽署金鑰，您必須附加包含應用程式識別碼的 `appid` 查詢參數，以取得指向您應用程式簽署金鑰資訊的 `jwks_uri`。 例如：`https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 包含內容為 `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 的 `jwks_uri`。

## <a name="send-the-sign-in-request"></a>傳送登入要求

當您的 Web 應用程式需要驗證使用者時，其必須將使用者導向至 `/authorize` 端點。 這個要求類似 [OAuth 2.0 授權碼流程](v1-protocols-oauth-code.md)的第一個階段，有幾個重要的區別：

* 要求必須在 `scope` 參數中包含範圍 `openid`。
* `response_type` 參數必須包含 `id_token`。
* 要求必須包含 `nonce` 參數。

因此範例要求會看起來像這樣：

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| 參數 | 類型 | 說明 |
| --- | --- | --- |
| tenant |required |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 租用戶獨立權杖允許的值為租用戶識別碼，例如 `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` 或 `common` |
| client_id |必要 |向 Azure AD 註冊應用程式時，指派給您應用程式的應用程式識別碼。 您可以在 Azure 入口網站中找到這個值。 依序按一下 [ **Azure Active Directory**]、[ **應用程式註冊**]、[應用程式] 和 [應用程式] 頁面上的應用程式識別碼。 |
| response_type |必要 |必須包含 OpenID Connect 登入的 `id_token` 。 它也可能包含其他 response_types，例如 `code` 或 `token`。 |
| scope | 建議使用 | OpenID Connect 規格需要範圍，此範圍會轉譯 `openid` 為同意 UI 中的「登入」許可權。 V1.0 端點會忽略此和其他 OIDC 範圍，但仍是標準相容用戶端的最佳做法。 |
| nonce |必要 |包含在要求中的值 (由應用程式所產生)，將會包含在所得的 `id_token` 中來做為宣告。 然後，應用程式可以驗證此值，以減輕權杖重新執行所造成的攻擊。 此值通常是隨機的唯一字串或 GUID，可用以識別要求的來源。 |
| redirect_uri | 建議使用 |應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。 如果遺失，則會將使用者代理程式傳回給應用程式所註冊的其中一個重新導向 Uri （隨機）。 最大長度為255個位元組 |
| response_mode |選用 |指定將產生的 authorization_code 傳回到應用程式所應該使用的方法。 支援的值為 `form_post` (*HTTP 表單張貼*) 和 `fragment` (*URL 片段*)。 針對 Web 應用程式，建議使用 `response_mode=form_post`，確保會以最安全的方式將權杖傳輸至您的應用程式。 包括 id_token 在內的任何流程預設值皆為 `fragment`。|
| 狀態 |建議使用 |會隨權杖回應傳回之要求中所包含的值。 其可以是您想要之任何內容的字串。 隨機產生的唯一值通常用於 [防止跨站台要求偽造攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此狀態也可用來在驗證要求發生之前，將使用者狀態的相關資訊編碼，例如他們所在的頁面或檢視。 |
| Prompt |選用 |表示必要的使用者互動類型。 目前只有 'login'、'none'、'consent' 是有效值。 `prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。 `prompt=none` 則相反 - 它會確保不會對使用者顯示任何互動式提示。 如果無法透過單一登入以無訊息方式完成要求，端點就會傳回錯誤。 `prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| login_hint |選用 |如果您事先知道使用者的使用者名稱，可以用此項目來預先填入使用者登入頁面上的使用者名稱/電子郵件地址欄位。 通常應用程式會在重新驗證期間使用此參數，並已經使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。 |

此時，系統會要求使用者輸入其認證並完成驗證。

### <a name="sample-response"></a>範例回應

在 `redirect_uri` 使用者經過驗證之後，會傳送至登入要求中指定的回應範例，如下所示：

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 參數 | 說明 |
| --- | --- |
| id_token |應用程式要求的 `id_token`。 您可以使用 `id_token` 確認使用者的身分識別，並以使用者開始工作階段。 |
| 狀態 |要求中包含的值，也會隨權杖回應傳回。 隨機產生的唯一值通常用於 [防止跨站台要求偽造攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此狀態也可用來在驗證要求發生之前，將使用者狀態的相關資訊編碼，例如他們所在的頁面或檢視。 |

### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理：

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 參數 | 說明 |
| --- | --- |
| error |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error_description |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>授權端點錯誤的錯誤碼

下表說明各種可能在錯誤回應的 `error` 參數中傳回的錯誤碼。

| 錯誤碼 | 描述 | 用戶端動作 |
| --- | --- | --- |
| invalid_request |通訊協定錯誤，例如遺漏必要的參數。 |修正並重新提交要求。 這是通常會在初始測試期間擷取到的開發錯誤。 |
| unauthorized_client |不允許用戶端應用程式要求授權碼。 |這通常會在用戶端應用程式未在 Azure AD 中註冊，或未加入至使用者的 Azure AD 租用戶時發生。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |
| access_denied |資源擁有者拒絕同意 |用戶端應用程式可以通知使用者，除非使用者同意，否則無法繼續進行。 |
| unsupported_response_type |授權伺服器不支援要求中的回應類型。 |修正並重新提交要求。 這是通常會在初始測試期間擷取到的開發錯誤。 |
| server_error |伺服器發生非預期的錯誤。 |重試要求。 這些錯誤可能是由暫時性狀況所引起。 用戶端應用程式可能會向使用者解釋，說明其回應因暫時性錯誤而延遲。 |
| temporarily_unavailable |伺服器暫時過於忙碌而無法處理要求。 |重試要求。 用戶端應用程式可能會向使用者解釋，說明其回應因暫時性狀況而延遲。 |
| invalid_resource |目標資源無效，因為它不存在、Azure AD 無法找到它，或是它並未正確設定。 |這表示尚未在租用戶中設定資源 (如果存在)。 應用程式可以對使用者提示關於安裝應用程式，並將它加入至 Azure AD 的指示。 |

## <a name="validate-the-id_token"></a>驗證 id_token

僅接收 `id_token` 不足以驗證使用者，您必須驗證簽章，並依照應用程式的需求確認 `id_token` 中的宣告。 Azure AD 端點使用 JSON Web Tokens (JWT) 和公開金鑰加密簽署權杖及驗證其是否有效。

您可以選擇驗證用戶端程式碼中的 `id_token`，但是常見的作法是將 `id_token` 傳送至後端伺服器，並且在那裡執行驗證。 

您可能也希望根據自己的案例驗證其他宣告。 一些常見的驗證包括：

* 確保使用者/組織已註冊應用程式。
* 使用或宣告確保使用者具有適當的授權/ `wids` 許可權 `roles` 。 
* 確保驗證具有特定強度，例如多重要素驗證。

驗證 `id_token` 之後，即可利用該使用者開始工作階段，並使用 `id_token` 中的宣告來取得應用程式中的使用者相關資訊。 此資訊可用於顯示、記錄、個人化等。如需和宣告的詳細資訊 `id_tokens` ，請參閱 [AAD id_tokens](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。

## <a name="send-a-sign-out-request"></a>傳送登出要求

當您想要將使用者登出應用程式時，只是清除應用程式的 Cookie 或結束使用者的工作階段還是不夠。 您也必須將使用者重新導向至 `end_session_endpoint` 以進行登出。如果您無法這麼做，使用者將可以重新驗證您的應用程式，而不需要再次輸入其認證，因為它們會與 Azure AD 端點有有效的單一登入會話。

您可以直接將使用者重新導向至 OpenID Connect 中繼資料文件中所列出的 `end_session_endpoint` ：

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| 參數 | 類型 | 說明 |
| --- | --- | --- |
| post_logout_redirect_uri |建議使用 |成功登出之後，使用者應該重新導向的 URL。 此 URL 必須符合您在應用程式註冊入口網站中為應用程式註冊的其中一個重新導向 Uri。  如果未包含 *post_logout_redirect_uri* ，則會顯示一般訊息給使用者。 |

## <a name="single-sign-out"></a>單一登出

當您將使用者重新導向至 `end_session_endpoint` 時，Azure AD 會清除瀏覽器中的使用者工作階段。 不過，使用者可能仍然登入其他使用 Azure AD 進行驗證的應用程式。 為了讓這些應用程式能同時將使用者登入，Azure AD 會將 HTTP GET 要求傳送至使用者目前登入之所有應用程式的已註冊 `LogoutUrl`。 應用程式必須藉由清除任何可識別使用者的工作階段並傳回 `200` 回應，以回應此要求。 如果您想要在應用程式中支援單一登出，您必須在應用程式的程式碼中實作這類 `LogoutUrl`。 您可以在 Azure 入口網站中設定 `LogoutUrl`：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 在頁面右上角按一下您的帳戶，以選擇您的 Active Directory。
3. 在左側導覽窗格中，依序選擇 [Azure Active Directory]****、[應用程式註冊]****，然後選取您的應用程式。
4. 依序按一下 [設定]**** 和 [屬性]****，並找到 [登出 URL]**** 文字方塊。 

## <a name="token-acquisition"></a>權杖取得
許多 Web Apps 不僅需要將使用者登入，同時需要使用 OAuth 代表使用者來存取 Web 服務。 此案例結合使用者驗證的 OpenID Connect，同時 `authorization_code` 取得可用來 `access_tokens` 使用 [OAuth 授權碼流程](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)的。

## <a name="get-access-tokens"></a>取得存取權杖
若要取得存取權杖，您需要修改上述的登入要求：

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

藉由在要求中包含權限範圍，並且使用 `response_type=code+id_token`，`authorize` 端點可確保使用者已經同意 `scope` 查詢參數中表示的權限，並且將授權碼傳回至您的應用程式以交換存取權杖。

### <a name="successful-response"></a>成功的回應

成功的回應會傳送至 `redirect_uri` 使用 `response_mode=form_post` ，如下所示：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| 參數 | 說明 |
| --- | --- |
| id_token |應用程式要求的 `id_token`。 您可以使用 `id_token` 確認使用者的身分識別，並以使用者開始工作階段。 |
| code |應用程式要求的 authorization_code。 應用程式可以使用授權碼要求目標資源的存取權杖。 authorization_code 的有效期很短，通常約 10 分鐘後即到期。 |
| State |如果要求中包含 state 參數，則回應中應該會出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 參數 | 說明 |
| --- | --- |
| error |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| error_description |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

如需可能的錯誤碼及建議的用戶端動作說明，請參閱[授權端點錯誤的錯誤碼](#error-codes-for-authorization-endpoint-errors)。

一旦取得授權 `code` 和之後 `id_token` ，您就可以將使用者登入，並代表他們取得 [存取權杖](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 。 若要將使用者登入，您必須完整地如上方所述驗證 `id_token` 。 若要取得存取權杖，您可以依照 [OAuth 程式碼流程文件](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)中＜使用授權碼來要求存取權杖＞一節中所述的步驟操作。

## <a name="next-steps"></a>接下來的步驟

* 深入了解[存取權杖](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。
* 深入瞭解[ `id_token` 和宣告](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。
