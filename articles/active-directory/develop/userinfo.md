---
title: Microsoft 身分識別平臺使用者資訊端點 |蔚藍
titleSuffix: Microsoft identity platform
description: 深入瞭解 Microsoft 身分識別平臺上的使用者資訊端點。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752735"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft 身分識別平臺使用者資訊端點

使用者資訊端點是 [OpenID Connect standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) 的一部分，其設計目的是要傳回已驗證使用者的相關宣告。  針對 Microsoft 身分識別平臺，使用者資訊端點裝載于 Microsoft Graph (上 https://graph.microsoft.com/oidc/userinfo) 。 

## <a name="find-the-well-known-configuration-endpoint"></a>尋找. 知名的設定端點

您可以使用 OpenID Connect 探索檔，以程式設計的方式探索使用者資訊端點 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` 。 它會列在 `userinfo_endpoint` 欄位中，而此模式可以跨雲端使用，以協助指向正確的端點。  我們不建議在您的應用程式中硬式編碼使用者資訊端點–請改用 OIDC 探索檔，在執行時間尋找此端點。

做為 OpenID Connect 規格的一部分， [OIDC 相容](https://openid.net/developers/certified/)  的程式庫通常會自動呼叫使用者名稱的端點，以取得使用者的相關資訊。  如果沒有裝載這類端點，Microsoft 身分識別平臺將不符合標準，而且某些程式庫會失敗。  在 [OIDC 標準中識別的宣告清單](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) 中，我們會產生名稱宣告、主體宣告和電子郵件（如果有的話），並同意。  

## <a name="consider-use-an-id-token-instead"></a>請考慮：改用識別碼權杖

您的應用程式可以接收的識別碼權杖中所提供的資訊，是它可以從使用者資訊端點取得的資訊超集合。  因為您可以在取得權杖以呼叫使用者資訊端點時取得識別碼權杖，所以建議您使用該識別碼權杖來取得使用者的相關資訊，而不是呼叫使用者資訊端點。  使用識別碼權杖將會從您的應用程式啟動中排除一個到兩個網路要求，以降低應用程式的延遲。

如果您需要更多有關使用者的詳細資料，您應該呼叫 [Microsoft Graph `/user` API](/graph/api/user-get) 來取得 office 號碼或職稱之類的資訊。   您也可以使用 [選擇性宣告](active-directory-optional-claims.md) ，在識別碼和存取權杖中包含額外的使用者資訊。

## <a name="calling-the-userinfo-endpoint"></a>呼叫使用者資訊端點

使用者名稱是標準的 OAuth 持有人權杖 API，如同使用取得權杖 Microsoft Graph 的權杖時所收到的存取權杖，與任何其他 Microsoft Graph API 相同。 它會傳回 JSON 回應，其中包含使用者的相關宣告。

### <a name="permissions"></a>權限

使用下列 [OIDC 許可權](v2-permissions-and-consent.md#openid-connect-scopes) 來呼叫使用者資訊 API。 `openid` 是必要的，而且 `profile` 和 `email` 範圍可確保回應中會提供其他資訊。

|權限類型      | 權限    |
|:--------------------|:---------------------------------------------------------|
|委派 (的公司或學校帳戶)  | openid (必要的) 、設定檔、電子郵件 |
|委派 (個人 Microsoft 帳戶)  | openid (必要的) 、設定檔、電子郵件 |
|應用程式 | 不適用 |

> [!TIP]
> 在您的瀏覽器中複製此 URL，以取得使用者資訊端點的權杖以及 [識別碼權杖](id-tokens.md) ，並以您自己的識別碼取代用戶端識別碼和重新導向 URI。 請注意，它只會要求 OpenID 或 Graph 範圍的範圍，而不是任何其他專案。  這是必要的，因為您無法在相同的權杖要求中要求兩個不同資源的許可權。
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> 您可以在下一節中使用此存取權杖。

如同任何其他 Microsoft Graph token，您在此處收到的權杖可能不是 JWT。 如果您已 Microsoft 帳戶使用者登入，則會是加密的權杖格式。 這是因為 Microsoft Graph 有特殊的權杖發行模式。 這不會影響您使用存取權杖來呼叫使用者資訊端點的能力。

### <a name="calling-the-api"></a>呼叫 API

使用者資訊 API 會根據 OIDC 規格支援 GET 和 POST。

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>使用者資訊回應

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

此處所列的宣告是使用者資訊端點可以傳回的所有宣告。  這些是應用程式在簽發給應用程式的 [識別碼權杖](id-tokens.md) 中所看到的相同值。  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>使用者資訊端點的注意事項和注意事項

* 如果您想要呼叫此使用者資訊端點，您必須使用 v2.0 端點。  如果您使用 v1.0 端點，您將會取得 v1.0 使用者端點的權杖，其裝載于 login.microsoftonline.com 上。  建議所有符合 OIDC 規範的應用程式和程式庫都使用 v2.0 端點，以確保相容性。
* 無法自訂使用者資訊端點的回應。  如果您想要自訂宣告，請使用 [宣告對應]( active-directory-claims-mapping.md) 來編輯權杖中傳回的資訊。
* 無法將使用者資訊端點的回應新增至。  如果您想要取得使用者的其他宣告，請使用 [選擇性宣告]( active-directory-optional-claims.md) 將新的宣告新增至權杖。

## <a name="next-steps"></a>後續步驟

* [檢查識別碼權杖的內容](id-tokens.md)
* [使用選擇性宣告自訂識別碼權杖的內容](active-directory-optional-claims.md)
* [使用 OAuth2 通訊協定要求存取權杖和識別碼權杖](v2-protocols-oidc.md)
