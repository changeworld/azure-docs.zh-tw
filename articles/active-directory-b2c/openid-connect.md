---
title: 使用 OpenID 連接進行 Web 登錄 - Azure 活動目錄 B2C
description: 使用 Azure 活動目錄 B2C 中的 OpenID 連接身份驗證協定構建 Web 應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6640ab1660e6499a97a8c990a0001d5fbae4e997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264384"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中利用 OpenID Connect 的 Web 登入

OpenID Connect 是建置在 OAuth 2.0 之上的驗證通訊協定，可用來將使用者安全地登入 Web 應用程式。 藉由使用 OpenID Connect 的 Azure Active Directory B2C (Azure AD B2C) 實作，您就能將 Web 應用程式中的註冊、登入及其他識別管理體驗外包給 Azure Active Directory (Azure AD)。 本指南會以與語言無關的方式示範如何執行此動作。 而是在說明如何傳送和接收 HTTP 訊息，但不使用我們的任何開放原始碼程式庫。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 擴充 OAuth 2.0 的*授權*通訊協定來做為*驗證*通訊協定。 此身份驗證協定允許您執行單一登入。 它引入了*ID 權杖*的概念，它允許用戶端驗證使用者的身份並獲取有關使用者的基本設定檔資訊。

因為它擴展OAuth 2.0，它也使應用程式能夠安全地獲取*訪問權杖*。 您可以使用存取權杖，來存取受到[授權伺服器](protocols-overview.md)保護的資源。 如果要構建託管在伺服器上並通過瀏覽器訪問的 Web 應用程式，則建議使用 OpenID 連接。 有關權杖的詳細資訊，請參閱[Azure 活動目錄 B2C 中的權杖概述](tokens-overview.md)

Azure AD B2C 擴充標準的 OpenID Connect 通訊協定，功能更強大，而不僅止於簡單的驗證和授權。 它引入了[使用者流參數](user-flow-overview.md)，它使您能夠使用 OpenID Connect 向應用程式添加使用者體驗，例如註冊、登錄和設定檔管理。

## <a name="send-authentication-requests"></a>傳送驗證要求

當 Web 應用程式需要對使用者進行身份驗證並運行使用者流時，它可以將使用者定向到`/authorize`終結點。 使用者根據使用者流執行操作。

在此請求中，用戶端指示需要從`scope`參數中的使用者獲取的許可權，並指定要運行的使用者流。 要瞭解請求的工作原理，請嘗試將請求粘貼到瀏覽器中並運行它。 以您的租用戶名稱取代 `{tenant}`。 替換為`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`以前在租戶中註冊的應用程式的應用 ID。 還將策略名稱 （`{policy}`更改為租戶中的策略名稱，例如`b2c_1_sign_in`。

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| 參數 | 必要 | 描述 |
| --------- | -------- | ----------- |
| [租戶] | 是 | Azure AD B2C 租戶的名稱 |
| [政策] | 是 | 要運行的使用者流。 指定在 Azure AD B2C 租戶中創建的使用者流的名稱。 例如： `b2c_1_sign_in`、`b2c_1_sign_up`或`b2c_1_edit_profile`。 |
| client_id | 是 | [Azure 門戶](https://portal.azure.com/)分配給應用程式的應用程式 ID。 |
| nonce | 是 | 請求中包含的值（由應用程式生成），作為聲明包含在生成的 ID 權杖中。 然後，應用程式可以驗證此值，以減輕權杖重播攻擊。 此值通常是隨機的唯一字串，可用以識別要求的來源。 |
| response_type | 是 | 必須包含 OpenID 連接的 ID 權杖。 如果 Web 應用程式還需要權杖來調用 Web API，則可以使用`code+id_token`。 |
| scope | 是 | 範圍的空格分隔清單。 `openid` 範圍指示使用識別碼權杖形式的權限，以登入使用者及取得使用者相關資料。 該`offline_access`作用域對於 Web 應用程式是可選的。 它指示應用程式將需要*refresh 權杖*才能擴展對資源的訪問。 |
| prompt | 否 | 需要的使用者互動類型。 此時唯一有效的值是 `login`，可強制使用者針對該要求輸入其認證。 |
| redirect_uri | 否 | 應用程式的`redirect_uri`參數，其中身份驗證回應可以由應用程式發送和接收。 它必須與您在 Azure 門戶`redirect_uri`中註冊的參數之一完全符合，只不過它必須是 URL 編碼的。 |
| response_mode | 否 | 用於將生成的授權代碼發送回應用程式的方法。 它可以是 `query`、`form_post` 或 `fragment`。  如需最佳安全性，建議使用 `form_post` 回應模式。 |
| state | 否 | 請求中包含的值，該值也返回于權杖回應中。 它可以是您所想要內容中的字串。 隨機產生的唯一值通常用於防止跨站台要求偽造攻擊。 該狀態還用於在身份驗證請求發生之前對應用程式中有關使用者狀態的資訊進行編碼，例如它們所打開的頁面。 |

此時，使用者被要求完成工作流。 使用者可能必須輸入其使用者名和密碼、使用社交身份登錄或註冊目錄。 根據定義使用者流的方式，可能還有其他步驟數。

使用者完成使用者流後，通過使用`redirect_uri``response_mode`參數中指定的方法，在指定的參數上返回回應給應用程式。 上述每個情況的回應都相同，與使用者流無關。

使用 `response_mode=fragment` 的成功回應如下所示：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| --------- | ----------- |
| id_token | 應用程式請求的 ID 權杖。 您可以使用識別碼權杖來確認使用者的身分識別，然後開始與使用者的工作階段。 |
| 代碼 | 如果使用 ，則應用程式請求的授權代碼`response_type=code+id_token`。 應用程式可以使用授權代碼請求目標資源的訪問權杖。 授權代碼通常在大約 10 分鐘後過期。 |
| state | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應驗證請求和回應`state`中的值是否相同。 |

錯誤回應也可以發送到參數，`redirect_uri`以便應用程式可以適當地處理它們：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| --------- | ----------- |
| error | 可用於對發生的錯誤類型進行分類的代碼。 |
| error_description | 有助於識別身份驗證錯誤的根本原因的特定錯誤訊息。 |
| state | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應驗證請求和回應`state`中的值是否相同。 |

## <a name="validate-the-id-token"></a>驗證識別碼權杖

只收到識別碼權杖並不足以驗證使用者。 驗證 ID 權杖的簽名，並根據應用程式的要求驗證權杖中的聲明。 Azure AD B2C 使用 [JSON Web Tokens (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公開金鑰加密編譯來簽署權杖及驗證其是否有效。 視您偏好的語言而定，有許多針對驗證 JWT 的開放原始碼程式庫可用。 我們建議您探索這些程式庫，而不是實作您自己的驗證邏輯。

Azure AD B2C 具有 OpenID 連接中繼資料終結點，允許應用程式在運行時獲取有關 Azure AD B2C 的資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 您的 B2C 租用戶中的每個使用者流程都有一份 JSON 中繼資料文件。 例如，`fabrikamb2c.onmicrosoft.com` 中適用於 `b2c_1_sign_in` 使用者流程的中繼資料文件位於：

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

此設定文件的屬性之一是 `jwks_uri`，就上述使用者流程而言，它的值是：

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

要確定在簽名 ID 權杖時使用哪個使用者流（以及從何處獲取中繼資料），有兩個選項。 首先，使用者流程名稱包含於識別碼權杖的 `acr` 宣告中。 另一個選項是當您發出要求時在 `state` 參數的值中將使用者流程編碼，然後將它解碼以判斷使用了哪個使用者流程。 任一種方法都有效。

從 OpenID Connect 中繼資料終結點獲取中繼資料文檔後，可以使用 RSA 256 公開金鑰驗證 ID 權杖的簽名。 此終結點上可能列出了多個金鑰，每個金鑰都由`kid`聲明標識。 識別碼權杖的標頭也會包含 `kid` 宣告，指出簽署該識別碼權杖所使用的金鑰。

要驗證 Azure AD B2C 中的權杖，您需要使用指數（e） 和模量 （n） 生成公開金鑰。 您需要確定如何相應地使用各自的程式設計語言執行此操作。 有關使用 RSA 協定生成公開金鑰的官方文檔，請參閱此處：https://tools.ietf.org/html/rfc3447#section-3.1

當您驗證識別碼權杖的簽章之後，必須驗證數個宣告。 例如：

- 驗證 `nonce` 宣告，以防止權杖重新執行攻擊。 其值應該是您在登入要求中所指定的內容。
- 驗證`aud`聲明以確保為應用程式頒發 ID 權杖。 其值應為應用程式的應用程式 ID。
- 驗證`iat`和`exp`聲明以確保 ID 權杖尚未過期。

另外還有數個您應該執行的驗證。 驗證在[OpenID 連接核心規範](https://openid.net/specs/openid-connect-core-1_0.html)中進行了詳細介紹。您可能還需要驗證其他聲明，具體取決於您的方案。 一些常見的驗證包括：

- 確保使用者/組織已註冊應用程式。
- 確保使用者有適當的授權/權限。
- 確保驗證方式有一定的強度，例如 Azure Multi-Factor Authentication。

驗證 ID 權杖後，可以開始與使用者會話。 您可以使用 ID 權杖中的聲明來獲取有關應用程式中使用者的資訊。 這項資訊的用途包括顯示、記錄和授權。

## <a name="get-a-token"></a>取得權杖

如果需要 Web 應用程式僅運行使用者流，可以跳過接下來的幾個部分。 這些部分僅適用于需要對 Web API 進行身份驗證調用並受 Azure AD B2C 保護的 Web 應用程式。

您可以藉由將 `POST` 要求傳送至 `/token` 端點，將取得 (使用 `response_type=code+id_token`) 的權杖授權碼兌換成所需的資源。 在 Azure AD B2C 中，可以通過在請求中指定其他 API 的範圍，像往常一樣[請求其他 API 的訪問權杖](access-tokens.md#request-a-token)。

還可以按照使用應用的用戶端 ID 作為請求的範圍（這將導致具有該用戶端 ID 的訪問權杖為"訪問"）的慣例，請求應用自己的後端 Web API 的訪問權杖：

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 參數 | 必要 | 描述 |
| --------- | -------- | ----------- |
| [租戶] | 是 | Azure AD B2C 租戶的名稱 |
| [政策] | 是 | 用來取得授權碼的使用者流程。 在此請求中不能使用不同的使用者流。 將此參數添加到查詢字串，而不是 POST 正文。 |
| client_id | 是 | [Azure 門戶](https://portal.azure.com/)分配給應用程式的應用程式 ID。 |
| client_secret | 是，在 Web 應用中 | 在[Azure 門戶](https://portal.azure.com/)中生成的應用程式金鑰。 用戶端機密用於 Web 應用方案，用戶端可以安全地存儲用戶端機密。 對於本機應用（公共用戶端）方案，用戶端機密無法安全地存儲，因此在此流中不使用。 如果使用用戶端金鑰，請定期更改它。 |
| 代碼 | 是 | 您在使用者流開始時獲取的授權代碼。 |
| grant_type | 是 | 授與的類型，針對授權碼流程來說，必須是 `authorization_code` 。 |
| redirect_uri | 是 | 應用程式的 `redirect_uri` 參數，您會在此處收到授權碼。 |
| scope | 否 | 範圍的空格分隔清單。 `openid` 範圍指示使用 id_tokens 參數形式的權限，以登入使用者及取得使用者相關資料。 它可用於將權杖獲取到應用程式自己的後端 Web API，該 API 由與用戶端相同的應用程式 ID 表示。 該`offline_access`範圍指示應用程式需要刷新權杖才能擴展對資源的訪問。 |

成功的權杖回應看起來如下：

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 參數 | 描述 |
| --------- | ----------- |
| not_before | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token_type | 權杖類型值。 `Bearer`是唯一支援的類型。 |
| access_token | 您所要求已簽署的 JWT 權杖。 |
| scope | 權杖有效的範圍。 |
| expires_in | 存取權杖的有效時間長度 (以秒為單位)。 |
| refresh_token | OAuth 2.0 重新整理權杖。 應用程式可以使用此權杖在當前權杖過期後獲取其他權杖。 刷新權杖可用於長時間保留對資源的存取權限。 為了接收`offline_access`刷新權杖，必須在授權請求和權杖請求中都使用作用域。 |

錯誤回應看起來如下：

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| --------- | ----------- |
| error | 可用於對發生的錯誤類型進行分類的代碼。 |
| error_description | 有助於識別身份驗證錯誤的根本原因的消息。 |

## <a name="use-the-token"></a>使用權杖

既然已成功取得存取權杖，在對後端 Web API 發出的要求中，您可以將權杖加入 `Authorization` 標頭中，即可使用權杖：

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>重新整理權杖

ID 權杖在短時間內過期。 權杖過期後刷新權杖，以繼續訪問資源。 您可以通過向`POST``/token`終結點提交另一個請求來刷新權杖。 此時，請提供 `refresh_token` 參數，而不是 `code` 參數：

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 參數 | 必要 | 描述 |
| --------- | -------- | ----------- |
| [租戶] | 是 | Azure AD B2C 租戶的名稱 |
| [政策] | 是 | 用來取得原始重新整理權杖的使用者流程。 在此請求中不能使用不同的使用者流。 將此參數添加到查詢字串，而不是 POST 正文。 |
| client_id | 是 | [Azure 門戶](https://portal.azure.com/)分配給應用程式的應用程式 ID。 |
| client_secret | 是，在 Web 應用中 | 在[Azure 門戶](https://portal.azure.com/)中生成的應用程式金鑰。 用戶端機密用於 Web 應用方案，用戶端可以安全地存儲用戶端機密。 對於本機應用（公共用戶端）方案，用戶端機密無法安全地存儲，因此在此調用中不使用用戶端金鑰。 如果使用用戶端金鑰，請定期更改它。 |
| grant_type | 是 | 授予的類型，它必須是授權代碼流的這一部分的刷新權杖。 |
| refresh_token | 是 | 在流的第二部分獲取的原始刷新權杖。 在`offline_access`授權請求和權杖請求中必須使用作用域才能接收刷新權杖。 |
| redirect_uri | 否 | 應用程式的 `redirect_uri` 參數，您會在此處收到授權碼。 |
| scope | 否 | 範圍的空格分隔清單。 `openid` 範圍指示使用識別碼權杖形式的權限，以登入使用者及取得使用者相關資料。 它可用於將權杖發送到應用程式自己的後端 Web API，該 API 由與用戶端相同的應用程式 ID 表示。 該`offline_access`範圍指示應用程式需要刷新權杖才能擴展對資源的訪問。 |

成功的權杖回應看起來如下：

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 參數 | 描述 |
| --------- | ----------- |
| not_before | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token_type | 權杖類型值。 `Bearer`是唯一支援的類型。 |
| access_token | 請求的已簽名的 JWT 權杖。 |
| scope | 權杖有效的作用域。 |
| expires_in | 存取權杖的有效時間長度 (以秒為單位)。 |
| refresh_token | OAuth 2.0 重新整理權杖。 應用程式可以使用此權杖在當前權杖過期後獲取其他權杖。 刷新權杖可用於長時間保留對資源的存取權限。 |

錯誤回應看起來如下：

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| --------- | ----------- |
| error | 可用於對發生的錯誤類型進行分類的代碼。 |
| error_description | 有助於識別身份驗證錯誤的根本原因的消息。 |

## <a name="send-a-sign-out-request"></a>傳送登出要求

當您要將使用者登出應用程式時，清除應用程式的 Cookie 或結束與使用者的會話是不夠的。 將使用者重定向到 Azure AD B2C 以登出。如果未能執行此操作，使用者可能無需再次輸入其憑據即可重新對應用程式進行身份驗證。

要登出使用者，請將使用者重定向到前面描述的 OpenID Connect 中繼資料文檔中列出的`end_session`終結點：

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| 參數 | 必要 | 描述 |
| --------- | -------- | ----------- |
| [租戶] | 是 | Azure AD B2C 租戶的名稱 |
| [政策] | 是 | 您想要用來將使用者登出應用程式的使用者流程。 |
| id_token_hint| 否 | 以前頒發的 ID 權杖，用於傳遞到登出終結點，作為有關最終使用者當前身份驗證與用戶端會話的提示。 `id_token_hint`確保 在`post_logout_redirect_uri`Azure AD B2C 應用程式設定中註冊了答覆 URL。 |
| client_id | 否* | [Azure 門戶](https://portal.azure.com/)分配給應用程式的應用程式 ID。<br><br>\**當使用`Application`隔離 SSO 配置時，這是必需的，登出請求中的 _"需要 ID 權杖_"設置為 。 `No`* |
| post_logout_redirect_uri | 否 | 成功登出後，應重定向到使用者的 URL。如果未包含，Azure AD B2C 會向使用者顯示一條通用消息。 除非提供 ，`id_token_hint`否則不應在 Azure AD B2C 應用程式設定中註冊此 URL 作為回復 URL。 |
| state | 否 | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應驗證請求和回應`state`中的值是否相同。 |

### <a name="secure-your-logout-redirect"></a>保護您的登出重定向

登出後，無論為應用程式指定的回復 URL，使用者都將重定向到`post_logout_redirect_uri`參數中指定的 URI。 但是，如果傳遞有效`id_token_hint`許可權，Azure AD B2C 會在執行重定向之前驗證`post_logout_redirect_uri`該值是否與應用程式佈建的重定向 URI 之一匹配。 如果未為應用程式佈建匹配的回復 URL，則會顯示一條錯誤訊息，並且不會重定向使用者。

### <a name="external-identity-provider-sign-out"></a>外部標識提供程式登出

將使用者定向到`end_session`終結點會清除使用 Azure AD B2C 的使用者的一些單一登入狀態，但不會將使用者從其社交標識提供程式 （IDP） 會話中登出。 如果使用者在後續登錄期間選擇相同的 IDP，則無需輸入憑據即可重新驗證。 如果使用者想要登出應用程式，並不一定意味著他們想要登出其 Facebook 帳戶。 但是，如果使用本地帳戶，則使用者的會話將正確結束。
