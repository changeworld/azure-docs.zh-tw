---
title: 授權碼流程 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何使用 Azure AD B2C 和 OpenID Connect 的驗證通訊協定來建置 web 應用程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 8248ca0abb1d633786b09b894bcd6b1089ab2d8c
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671789"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 OAuth 2.0 授權碼流程

在安裝於裝置上的應用程式中，您可以使用 OAuth 2.0 授權碼授與來存取受保護的資源，例如 Web API。 您可以使用 Azure Active Directory B2C (Azure AD B2C) 的 OAuth 2.0 實作，將註冊、登入及其他身分識別管理工作新增至行動及桌面應用程式。 這篇文章是與語言無關。 在本文中，我們將說明如何傳送及接收 HTTP 訊息，但不使用任何開放原始碼程式庫。

如需 OAuth 2.0 授權碼流程的說明，請參閱 [OAuth 2.0 規格的 4.1 節](https://tools.ietf.org/html/rfc6749)。 在大多數[應用程式類型](application-types.md) (包括 Web 應用程式和原生安裝的應用程式) 中，您都能利用它來執行驗證及授權作業。 您可以使用 OAuth 2.0 授權碼流程，安全地為您的應用程式取得存取權杖和重新整理權杖，而這些存取權杖可用來存取[授權伺服器](protocols-overview.md)所保護的資源。  一旦存取權杖到期 (通常在一小時後)，重新整理權杖即可讓用戶端取得新的存取 (和重新整理) 權杖。

本文著重在**公開用戶端** OAuth 2.0 授權碼流程。 公開用戶端是指不可信任會安全地維護密碼完整性的任何用戶端應用程式。 這包括行動應用程式、桌面應用程式，以及基本上任何在裝置上執行且需要取得存取權杖的應用程式。

> [!NOTE]
> 若要使用 Azure AD B2C 將身分識別管理新增至 Web 應用程式，請使用 [OpenID Connect](openid-connect.md)，而不是 OAuth 2.0。

Azure AD B2C 擴充標準的 OAuth 2.0 流程，功能更強大，而不僅止於簡單的驗證和授權。 它會引進[使用者流程](user-flow-overview.md)。 透過使用者流程，您可以利用 OAuth 2.0 來將使用者體驗新增至應用程式，例如註冊、登入和設定檔管理。 使用 OAuth 2.0 通訊協定的身分識別提供者包括[Amazon](identity-provider-amazon.md)、[Azure Active Directory](identity-provider-azure-ad-single-tenant.md)、[Facebook](identity-provider-facebook.md)、[GitHub](identity-provider-github.md)、[Google](identity-provider-google.md) 和 [LinkedIn](identity-provider-linkedin.md)。

若要嘗試本文中的 HTTP 要求：

1. 將 `{tenant}` 取代為您的 Azure AD B2C 租用戶名稱。
1. 將 `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` 取代為您先前在 Azure AD B2C 租使用者中註冊之應用程式的應用程式識別碼。
1. 以您在租使用者中建立的原則名稱取代 `{policy}`，例如 `b2c_1_sign_in`。

## <a name="1-get-an-authorization-code"></a>1. 取得授權碼
授權碼流程始於用戶端將使用者導向 `/authorize` 端點。 這是使用者在流程中會採取動作的互動部分。 在此要求中，用戶端會在 `scope` 參數中指出必須向使用者索取的權限。 以下三個範例 (插入換行以提高可讀性) 各使用不同的使用者流程。


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| 參數 | 必要項？ | 描述 |
| --- | --- | --- |
|出租| 必要 | Azure AD B2C 租使用者的名稱|
| 策略 | 必要 | 要執行的使用者流程。 指定您在 Azure AD B2C 租使用者中建立的使用者流程名稱。 例如： `b2c_1_sign_in`、`b2c_1_sign_up`或 `b2c_1_edit_profile`。 |
| client_id |必要 |在 [Azure 入口網站](https://portal.azure.com)中指派給應用程式的應用程式識別碼。 |
| response_type |必要 |回應類型，必須針對授權碼流程來加入 `code`。 |
| redirect_uri |必要 |應用程式的重新導向 URI，您的應用程式會在此處傳送及接收驗證回應。 它必須與您在入口網站中註冊的其中一個重新導向 URI 完全相符，不過必須是 URL 編碼格式。 |
| scope |必要 |範圍的空格分隔清單。 單一範圍值向 Azure Active Directory (Azure AD) 指出正在要求的兩個權限。 使用用戶端識別碼作為範圍時，表示您的應用程式需要可針對您自己的服務或 Web API 使用的存取權杖 (以相同的用戶端識別碼表示)。  `offline_access` 範圍表示您的應用程式需要重新整理權杖，才能長久存取資源。 您也可以使用 `openid` 範圍從 Azure AD B2C 要求識別碼權杖。 |
| response_mode |建議 |用來將產生的授權碼傳回至應用程式的方法。 可以是 `query`、`form_post` 或 `fragment`。 |
| state |建議 |包含在要求中的值，可以是您想要使用的任何內容字串。 通常會使用隨機產生的唯一值，以防止跨網站偽造要求攻擊。 在驗證要求出現之前，也會使用此狀態將應用程式中使用者狀態的相關資訊編碼。 例如，使用者所在的頁面，或正在執行的使用者流程。 |
| prompt |選用 |需要的使用者互動類型。 目前，唯一有效的值是 `login`，可強制使用者針對該要求輸入其認證。 單一登入將沒有作用。 |

此時會要求使用者完成使用者流程的工作流程。 這可能會牽涉到讓使用者輸入自己的使用者名稱及密碼、以社交身分識別登入、註冊目錄，或是其他任何數目的步驟。 使用者動作取決於使用者流程的定義方式。

當使用者完成使用者流程之後，Azure AD 會透過您用於 `redirect_uri` 的值，將回應傳回給您的應用程式。 它會使用 `response_mode` 參數中指定的方法。 對於每個使用者動作情節來說，回應完全相同，與已執行的使用者流程無關。

使用 `response_mode=query` 的成功回應如下所示：

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 參數 | 描述 |
| --- | --- |
| 代碼 |應用程式所要求的授權碼。 應用程式可以使用授權碼來要求目標資源的存取權杖。 授權碼的存留期很短。 通常會在大約 10 分鐘後過期。 |
| state |如需完整說明，請參閱上一節的表格。 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應該驗證要求和回應中的 `state` 值完全相同。 |

錯誤回應也能傳送到重新導向 URI，以便讓應用程式能夠適當地處理它們：

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| --- | --- |
| error |可用於將發生的錯誤類型分類的錯誤碼字串。 您也可以使用此字串對錯誤做出反應。 |
| error_description |可協助您識別驗證錯誤根本原因的特定錯誤訊息。 |
| state |如需完整說明，請參閱前一個表格。 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應該驗證要求和回應中的 `state` 值完全相同。 |

## <a name="2-get-a-token"></a>2. 取得權杖
既然已取得授權碼，您可以將 POST 要求傳送至 `code` 端點，針對所需的資源來兌換權杖的 `/token`。 在 Azure AD B2C 中，您可以藉由在要求中指定其範圍，以一般方式[要求其他 API 的存取權杖](access-tokens.md#request-a-token)。

您也可以針對應用程式本身的後端 Web API 要求存取權杖，方法是使用應用程式的用戶端識別碼作為要求的範圍（這會產生具有該用戶端識別碼的存取權杖做為「物件」）：

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| 參數 | 必要項？ | 描述 |
| --- | --- | --- |
|出租| 必要 | Azure AD B2C 租使用者的名稱|
|策略| 必要| 用來取得授權碼的使用者流程。 您無法在此要求中使用不同的使用者流程。 |
| client_id |必要 |在 [Azure 入口網站](https://portal.azure.com)中指派給應用程式的應用程式識別碼。|
| client_secret | 是，在 Web Apps | 在[Azure 入口網站](https://portal.azure.com/)中產生的應用程式密碼。 在此流程中，用戶端密碼會用於 Web 應用程式案例，讓用戶端可以安全地儲存用戶端密碼。 針對原生應用程式（公用用戶端）案例，無法安全地儲存用戶端密碼，因此不會在此呼叫中使用。 如果您使用用戶端密碼，請定期進行變更。 |
| grant_type |必要 |授與類型。 在授權碼流程中，授與類型必須的 `authorization_code`。 |
| scope |建議 |範圍的空格分隔清單。 向 Azure AD 指出要求兩個權限的單一範圍值。 使用用戶端識別碼作為範圍時，表示您的應用程式需要可針對您自己的服務或 Web API 使用的存取權杖 (以相同的用戶端識別碼表示)。  `offline_access` 範圍表示您的應用程式需要重新整理權杖，才能長久存取資源。  您也可以使用 `openid` 範圍從 Azure AD B2C 要求識別碼權杖。 |
| 代碼 |必要 |您在流程的第一個階段中取得的授權碼。 |
| redirect_uri |必要 |應用程式的重新導向 URI，您已在此處收到授權碼。 |

成功的權杖回應如下所示：

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
| --- | --- |
| not_before |權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token_type |權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 |
| access_token |您所要求的已簽署 JSON Web 權杖 (JWT)。 |
| scope |權杖有效的範圍。 您也可以使用範圍來快取權杖，以供稍後使用。 |
| expires_in |權杖的有效時間長度 (以秒為單位)。 |
| refresh_token |OAuth 2.0 重新整理權杖。 應用程式在目前的權杖過期之後，可以使用這個權杖來取得其他權杖。 重新整理權杖是長期權杖。 您可以使用它們來長時間持續存取資源。 如需詳細資訊，請參閱 [Azure AD B2C 權杖參考](tokens-overview.md)。 |

錯誤回應如下所示：

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| --- | --- |
| error |可用於將發生的錯誤類型分類的錯誤碼字串。 您也可以使用此字串對錯誤做出反應。 |
| error_description |可協助您識別驗證錯誤根本原因的特定錯誤訊息。 |

## <a name="3-use-the-token"></a>3. 使用權杖
既然已成功取得存取權杖，在對後端 Web API 發出的要求中，您可以將權杖加入 `Authorization` 標頭中，即可使用權杖：

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. 重新整理權杖
存取權杖和識別碼權杖的存留期短暫。 權杖過期之後，您必須重新整理權杖，才能繼續存取資源。 若要這樣做，請向 `/token` 端點提交另一個 POST 要求。 但這次要提供 `refresh_token`，而不是 `code`：

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 參數 | 必要項？ | 描述 |
| --- | --- | --- |
|出租| 必要 | Azure AD B2C 租使用者的名稱|
|策略 |必要 |用來取得原始重新整理權杖的使用者流程。 您無法在此要求中使用不同的使用者流程。 |
| client_id |必要 |在 [Azure 入口網站](https://portal.azure.com)中指派給應用程式的應用程式識別碼。 |
| client_secret | 是，在 Web Apps | 在[Azure 入口網站](https://portal.azure.com/)中產生的應用程式密碼。 在此流程中，用戶端密碼會用於 Web 應用程式案例，讓用戶端可以安全地儲存用戶端密碼。 針對原生應用程式（公用用戶端）案例，無法安全地儲存用戶端密碼，因此不會在此呼叫中使用。 如果您使用用戶端密碼，請定期進行變更。 |
| grant_type |必要 |授與類型。 在授權碼流程的這個階段中，授與類型必須是 `refresh_token`。 |
| scope |建議 |範圍的空格分隔清單。 向 Azure AD 指出要求兩個權限的單一範圍值。 使用用戶端識別碼作為範圍時，表示您的應用程式需要可針對您自己的服務或 Web API 使用的存取權杖 (以相同的用戶端識別碼表示)。  `offline_access` 範圍表示您的應用程式需要重新整理權杖，才能長久存取資源。  您也可以使用 `openid` 範圍從 Azure AD B2C 要求識別碼權杖。 |
| redirect_uri |選用 |應用程式的重新導向 URI，您已在此處收到授權碼。 |
| refresh_token |必要 |您在流程的第二個階段中取得的原始重新整理權杖。 |

成功的權杖回應如下所示：

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
| --- | --- |
| not_before |權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 |
| token_type |權杖類型值。 Azure AD 唯一支援的類型是 Bearer。 |
| access_token |您所要求的已簽署 JWT。 |
| scope |權杖有效的範圍。 您也可以使用範圍來快取權杖，以供稍後使用。 |
| expires_in |權杖的有效時間長度 (以秒為單位)。 |
| refresh_token |OAuth 2.0 重新整理權杖。 應用程式在目前的權杖過期之後，可以使用這個權杖來取得其他權杖。 重新整理權杖的有效期很長，而且可以用來長期保留資源存取權。 如需詳細資訊，請參閱 [Azure AD B2C 權杖參考](tokens-overview.md)。 |

錯誤回應如下所示：

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| --- | --- |
| error |可用於將發生的錯誤類型分類的錯誤碼字串。 您也可以使用此字串對錯誤做出反應。 |
| error_description |可協助您識別驗證錯誤根本原因的特定錯誤訊息。 |

## <a name="use-your-own-azure-ad-b2c-directory"></a>使用您自己的 Azure AD B2C 目錄
若要自行嘗試這些要求，請完成下列步驟。 使用您自己的值取代我們在本文中使用的範例值。

1. [建立 Azure AD B2C 目錄](tutorial-create-tenant.md)。 在要求中使用您的目錄名稱。
2. [建立應用程式](tutorial-register-applications.md)來取得應用程式識別碼和重新導向 URI。 在您的應用程式中包含原生用戶端。
3. [建立您的使用者流程](user-flow-overview.md)以取得您的使用者流程名稱。
