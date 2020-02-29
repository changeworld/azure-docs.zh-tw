---
title: 使用 OpenID Connect 的 Web 登入-Azure Active Directory B2C
description: 使用 Azure Active Directory B2C 中的 OpenID Connect 驗證通訊協定來建立 web 應用程式。
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183987"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中利用 OpenID Connect 的 Web 登入

OpenID Connect 是建置在 OAuth 2.0 之上的驗證通訊協定，可用來將使用者安全地登入 Web 應用程式。 藉由使用 OpenID Connect 的 Azure Active Directory B2C (Azure AD B2C) 實作，您就能將 Web 應用程式中的註冊、登入及其他識別管理體驗外包給 Azure Active Directory (Azure AD)。 本指南會以與語言無關的方式示範如何執行此動作。 而是在說明如何傳送和接收 HTTP 訊息，但不使用我們的任何開放原始碼程式庫。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 擴充 OAuth 2.0 的*授權*通訊協定來做為*驗證*通訊協定。 此驗證通訊協定可讓您執行單一登入。 它引進了*識別碼權杖*的概念，可讓用戶端驗證使用者的身分識別，並取得有關使用者的基本設定檔資訊。

因為它會擴充 OAuth 2.0，所以也可讓應用程式安全地取得*存取權杖*。 您可以使用存取權杖，來存取受到[授權伺服器](protocols-overview.md)保護的資源。 如果您要建立裝載于伺服器上且透過瀏覽器存取的 web 應用程式，建議使用 OpenID Connect。 如需有關權杖的詳細資訊，請參閱[中的權杖總覽 Azure Active Directory B2C](tokens-overview.md)

Azure AD B2C 擴充標準的 OpenID Connect 通訊協定，功能更強大，而不僅止於簡單的驗證和授權。 它引進[使用者流程參數](user-flow-overview.md)，可讓您使用 OpenID connect 將使用者體驗新增至您的應用程式，例如註冊、登入和設定檔管理。

## <a name="send-authentication-requests"></a>傳送驗證要求

當您的 web 應用程式需要驗證使用者並執行使用者流程時，它可以將使用者導向 `/authorize` 端點。 使用者會根據使用者流程採取動作。

在此要求中，用戶端會在 `scope` 參數中指出它需要從使用者取得的許可權，並指定要執行的使用者流程。 若要瞭解要求的運作方式，請嘗試將要求貼入瀏覽器並加以執行。 以您的租用戶名稱取代 `{tenant}`。 將 `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` 取代為您先前在租使用者中註冊之應用程式的應用程式識別碼。 也請將原則名稱（`{policy}`）變更為您在租使用者中擁有的原則名稱，例如 `b2c_1_sign_in`。

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

| 參數 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| 出租 | 是 | Azure AD B2C 租使用者的名稱 |
| 策略 | 是 | 要執行的使用者流程。 指定您在 Azure AD B2C 租使用者中建立的使用者流程名稱。 例如： `b2c_1_sign_in`、`b2c_1_sign_up`或 `b2c_1_edit_profile`。 |
| client_id | 是 | [Azure 入口網站](https://portal.azure.com/)指派給應用程式的應用程式識別碼。 |
| nonce | 是 | 包含在要求中的值（由應用程式所產生），以宣告形式包含在產生的識別碼權杖中。 然後，應用程式可以驗證此值，以減輕權杖重新執行攻擊。 此值通常是隨機的唯一字串，可用以識別要求的來源。 |
| response_type | 是 | 必須包含 OpenID Connect 的識別碼權杖。 如果您的 web 應用程式也需要權杖來呼叫 Web API，您可以使用 `code+id_token`。 |
| 範圍 | 是 | 範圍的空格分隔清單。 `openid` 範圍指示使用識別碼權杖形式的權限，以登入使用者及取得使用者相關資料。 `offline_access` 範圍對於 web 應用程式而言是選擇性的。 這表示您的應用程式將需要重新整理*權杖*，才能延伸資源的存取權。 |
| prompt | 否 | 需要的使用者互動類型。 此時唯一有效的值是 `login`，可強制使用者針對該要求輸入其認證。 |
| redirect_uri | 否 | 應用程式的 `redirect_uri` 參數，您的應用程式可以在其中傳送及接收驗證回應。 它必須完全符合您在 Azure 入口網站中註冊的其中一個 `redirect_uri` 參數，不同之處在于它必須以 URL 編碼。 |
| response_mode | 否 | 用來將產生的授權碼傳回給應用程式的方法。 它可以是 `query`、`form_post` 或 `fragment`。  如需最佳安全性，建議使用 `form_post` 回應模式。 |
| state | 否 | 包含在要求中的值，也會在權杖回應中傳回。 它可以是您所想要內容中的字串。 隨機產生的唯一值通常用於防止跨站台要求偽造攻擊。 此狀態也用來在驗證要求發生之前，將使用者狀態的相關資訊編碼，例如他們所在的頁面。 |

此時，系統會要求使用者完成工作流程。 使用者可能必須輸入使用者名稱和密碼、以社交身分識別登入，或註冊目錄。 視使用者流程的定義方式而定，可能會有其他任何數目的步驟。

使用者完成使用者流程之後，會使用在 `response_mode` 參數中指定的方法，在指定的 `redirect_uri` 參數上將回應傳回給您的應用程式。 上述每個案例的回應都相同，與使用者流程無關。

使用 `response_mode=fragment` 的成功回應如下所示：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| --------- | ----------- |
| id_token | 應用程式所要求的識別碼權杖。 您可以使用識別碼權杖來確認使用者的身分識別，然後開始與使用者的工作階段。 |
| 代碼 | 如果您使用 `response_type=code+id_token`，則為應用程式要求的授權碼。 應用程式可以使用授權碼來要求目標資源的存取權杖。 授權碼通常會在大約10分鐘後到期。 |
| state | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應確認要求和回應中的 `state` 值是否相同。 |

錯誤回應也可以傳送至 `redirect_uri` 參數，讓應用程式可以適當地處理：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| --------- | ----------- |
| 錯誤 | 可以用來分類所發生錯誤類型的程式碼。 |
| error_description | 可協助識別驗證錯誤根本原因的特定錯誤訊息。 |
| state | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應確認要求和回應中的 `state` 值是否相同。 |

## <a name="validate-the-id-token"></a>驗證識別碼權杖

只收到識別碼權杖並不足以驗證使用者。 驗證識別碼權杖的簽章，並根據您的應用程式需求確認權杖中的宣告。 Azure AD B2C 使用 [JSON Web Tokens (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公開金鑰加密編譯來簽署權杖及驗證其是否有效。 視您偏好的語言而定，有許多針對驗證 JWT 的開放原始碼程式庫可用。 我們建議您探索這些程式庫，而不是實作您自己的驗證邏輯。

Azure AD B2C 具有 OpenID Connect 中繼資料端點，可讓應用程式在執行時間取得 Azure AD B2C 的相關資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 您的 B2C 租用戶中的每個使用者流程都有一份 JSON 中繼資料文件。 例如，`b2c_1_sign_in` 中適用於 `fabrikamb2c.onmicrosoft.com` 使用者流程的中繼資料文件位於：

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

此設定文件的屬性之一是 `jwks_uri`，就上述使用者流程而言，它的值是：

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

若要判斷使用哪個使用者流程來簽署識別碼權杖（以及從何處取得中繼資料），您有兩個選項。 首先，使用者流程名稱包含於識別碼權杖的 `acr` 宣告中。 另一個選項是當您發出要求時在 `state` 參數的值中將使用者流程編碼，然後將它解碼以判斷使用了哪個使用者流程。 任一種方法都有效。

從 OpenID Connect 中繼資料端點取得元資料檔案之後，您可以使用 RSA 256 公用金鑰來驗證識別碼權杖的簽章。 此端點上可能會列出多個金鑰，每個索引鍵都是由 `kid` 宣告所識別。 識別碼權杖的標頭也會包含 `kid` 宣告，指出簽署該識別碼權杖所使用的金鑰。

若要驗證 Azure AD B2C 的權杖，您必須使用指數（e）和模數（n）來產生公開金鑰。 您需要決定如何以個別的程式設計語言來執行這項操作。 您可以在這裡找到有關使用 RSA 通訊協定產生之公用金鑰的官方檔： https://tools.ietf.org/html/rfc3447#section-3.1

當您驗證識別碼權杖的簽章之後，必須驗證數個宣告。 例如：

- 驗證 `nonce` 宣告，以防止權杖重新執行攻擊。 其值應該是您在登入要求中所指定的內容。
- 驗證 `aud` 宣告，以確保已針對您的應用程式發出 ID 權杖。 其值應該是您應用程式的應用程式識別碼。
- 驗證 `iat` 並 `exp` 宣告，以確保識別碼權杖尚未過期。

另外還有數個您應該執行的驗證。 [OpenID Connect 核心規格](https://openid.net/specs/openid-connect-core-1_0.html)中會詳細說明驗證。視您的案例而定，您可能也會想要驗證其他宣告。 一些常見的驗證包括：

- 確保使用者/組織已註冊應用程式。
- 確保使用者有適當的授權/權限。
- 確保驗證方式有一定的強度，例如 Azure Multi-Factor Authentication。

驗證識別碼權杖之後，您就可以開始與使用者的會話。 您可以使用識別碼權杖中的宣告來取得應用程式中使用者的相關資訊。 這項資訊的用途包括顯示、記錄和授權。

## <a name="get-a-token"></a>取得權杖

如果您的 web 應用程式只需要執行使用者流程，您可以略過接下來的幾節。 這些區段僅適用于需要對 Web API 進行已驗證的呼叫，而且也受到 Azure AD B2C 保護的 web 應用程式。

您可以藉由將 `response_type=code+id_token` 要求傳送至 `POST` 端點，將取得 (使用 `/token`) 的權杖授權碼兌換成所需的資源。 在 Azure AD B2C 中，您可以藉由在要求中指定其範圍，以一般方式[要求其他 api 的存取權杖](access-tokens.md#request-a-token)。

您也可以針對應用程式本身的後端 Web API 要求存取權杖，方法是使用應用程式的用戶端識別碼作為要求的範圍（這會產生具有該用戶端識別碼的存取權杖做為「物件」）：

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 參數 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| 出租 | 是 | Azure AD B2C 租使用者的名稱 |
| 策略 | 是 | 用來取得授權碼的使用者流程。 您無法在此要求中使用不同的使用者流程。 將此參數新增至查詢字串，而不是 POST 主體。 |
| client_id | 是 | [Azure 入口網站](https://portal.azure.com/)指派給應用程式的應用程式識別碼。 |
| client_secret | 是，在 Web Apps | 在[Azure 入口網站](https://portal.azure.com/)中產生的應用程式密碼。 在此流程中，用戶端密碼會用於 Web 應用程式案例，讓用戶端可以安全地儲存用戶端密碼。 針對原生應用程式（公用用戶端）案例，無法安全地儲存用戶端密碼，此流程上未使用 threfore。 如果使用用戶端密碼，請定期進行變更。 |
| 代碼 | 是 | 您在使用者流程開頭取得的授權碼。 |
| grant_type | 是 | 授與的類型，針對授權碼流程來說，必須是 `authorization_code` 。 |
| redirect_uri | 是 | 應用程式的 `redirect_uri` 參數，您會在此處收到授權碼。 |
| 範圍 | 否 | 範圍的空格分隔清單。 `openid` 範圍指示使用 id_tokens 參數形式的權限，以登入使用者及取得使用者相關資料。 它可以用來取得應用程式本身後端 Web API 的權杖，這是由與用戶端相同的應用程式識別碼所表示。 `offline_access` 範圍表示您的應用程式需要重新整理權杖，才能延伸資源的存取權。 |

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
| token_type | 權杖類型值。 `Bearer` 是唯一支援的類型。 |
| access_token | 您所要求已簽署的 JWT 權杖。 |
| 範圍 | 權杖有效的範圍。 |
| expires_in | 存取權杖的有效時間長度 (以秒為單位)。 |
| refresh_token | OAuth 2.0 重新整理權杖。 應用程式可以使用此權杖來取得目前權杖過期後的其他權杖。 重新整理權杖可用於長期保留資源的存取權。 `offline_access` 的範圍必須同時用於授權和權杖要求中，才能接收重新整理權杖。 |

錯誤回應看起來如下：

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| --------- | ----------- |
| 錯誤 | 可以用來分類發生的錯誤類型的程式碼。 |
| error_description | 可協助識別驗證錯誤根本原因的訊息。 |

## <a name="use-the-token"></a>使用權杖

既然已成功取得存取權杖，在對後端 Web API 發出的要求中，您可以將權杖加入 `Authorization` 標頭中，即可使用權杖：

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>重新整理權杖

識別碼權杖會在短時間內過期。 在權杖到期後重新整理它們，以繼續存取資源。 您可以藉由向 `/token` 端點提交另一個 `POST` 要求，來重新整理權杖。 此時，請提供 `refresh_token` 參數，而不是 `code` 參數：

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 參數 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| 出租 | 是 | Azure AD B2C 租使用者的名稱 |
| 策略 | 是 | 用來取得原始重新整理權杖的使用者流程。 您無法在此要求中使用不同的使用者流程。 將此參數新增至查詢字串，而不是 POST 主體。 |
| client_id | 是 | [Azure 入口網站](https://portal.azure.com/)指派給應用程式的應用程式識別碼。 |
| client_secret | 是，在 Web Apps | 在[Azure 入口網站](https://portal.azure.com/)中產生的應用程式密碼。 在此流程中，用戶端密碼會用於 Web 應用程式案例，讓用戶端可以安全地儲存用戶端密碼。 針對原生應用程式（公用用戶端）案例，無法安全地儲存用戶端密碼，threfore 不會用於此呼叫。 如果使用用戶端密碼，請定期進行變更。 |
| grant_type | 是 | 授與的類型，必須是授權碼流程中這個部分的重新整理權杖。 |
| refresh_token | 是 | 在流程的第二個部分中取得的原始重新整理權杖。 必須同時在授權和權杖要求中使用 `offline_access` 範圍，才能接收重新整理權杖。 |
| redirect_uri | 否 | 應用程式的 `redirect_uri` 參數，您會在此處收到授權碼。 |
| 範圍 | 否 | 範圍的空格分隔清單。 `openid` 範圍指示使用識別碼權杖形式的權限，以登入使用者及取得使用者相關資料。 它可用來將權杖傳送到您應用程式本身的後端 Web API，這是由與用戶端相同的應用程式識別碼來表示。 `offline_access` 範圍表示您的應用程式需要重新整理權杖，才能延伸資源的存取權。 |

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
| token_type | 權杖類型值。 `Bearer` 是唯一支援的類型。 |
| access_token | 要求的已簽署 JWT 權杖。 |
| 範圍 | 權杖有效的範圍。 |
| expires_in | 存取權杖的有效時間長度 (以秒為單位)。 |
| refresh_token | OAuth 2.0 重新整理權杖。 應用程式可以使用此權杖來取得目前權杖過期後的其他權杖。 重新整理權杖可用於長期保留資源的存取權。 |

錯誤回應看起來如下：

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| --------- | ----------- |
| 錯誤 | 可以用來分類發生的錯誤類型的程式碼。 |
| error_description | 可協助識別驗證錯誤根本原因的訊息。 |

## <a name="send-a-sign-out-request"></a>傳送登出要求

當您想要將使用者登出應用程式時，並不足以清除應用程式的 cookie 或結束使用者的會話。 將使用者重新導向至 Azure AD B2C 進行登出。如果您無法這麼做，使用者可能可以重新驗證您的應用程式，而不需要再次輸入其認證。

若要登出使用者，請將使用者重新導向至先前所述的 OpenID Connect 元資料檔案中所列的 `end_session` 端點：

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| 參數 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| 出租 | 是 | Azure AD B2C 租使用者的名稱 |
| 策略 | 是 | 您想要用來將使用者登出應用程式的使用者流程。 |
| id_token_hint| 否 | 先前發行的識別碼權杖，用來傳遞至登出端點，做為與用戶端目前已驗證的會話相關的提示。 `id_token_hint` 可確保 `post_logout_redirect_uri` 在您的 Azure AD B2C 應用程式設定中是已註冊的回復 URL。 |
| client_id | 否* | [Azure 入口網站](https://portal.azure.com/)指派給應用程式的應用程式識別碼。<br><br>\*在*使用 `Application` 隔離 SSO 設定，且 [登出要求中_需要識別碼權杖_] 設定為 [`No`] 時，這是必要的。* |
| post_logout_redirect_uri | 否 | 使用者在成功登出後應重新導向至的 URL。如果未包含，Azure AD B2C 會向使用者顯示一般訊息。 除非您提供 `id_token_hint`，否則不應在您的 Azure AD B2C 應用程式設定中，將此 URL 註冊為 [回復 URL]。 |
| state | 否 | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應確認要求和回應中的 `state` 值是否相同。 |

### <a name="secure-your-logout-redirect"></a>保護您的登出重新導向

登出之後，使用者會被重新導向到 `post_logout_redirect_uri` 參數中指定的 URI，而不論已為應用程式指定的回復 Url。 不過，如果傳遞有效的 `id_token_hint`，Azure AD B2C 會先驗證 `post_logout_redirect_uri` 的值是否符合應用程式的其中一個已設定的重新導向 Uri，再執行重新導向。 如果沒有為應用程式設定相符的回復 URL，則會顯示錯誤訊息，而且不會重新導向使用者。

### <a name="external-identity-provider-sign-out"></a>外部識別提供者登出

將使用者導向 `end_session` 端點，會使用 Azure AD B2C 來清除部分使用者的單一登入狀態，但不會將使用者登出其社交識別提供者（IDP）會話。 如果使用者在後續登入時選取相同的 IDP，則會重新驗證，而不需要輸入其認證。 如果使用者想要登出應用程式，則不一定表示他們想要登出其 Facebook 帳戶。 不過，如果使用本機帳戶，使用者的會話就會正確結束。
