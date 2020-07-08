---
title: 在自訂原則中定義 OpenID Connect 技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自訂原則中定義 OpenID Connect 技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5a83a8ab608e685f43056debe45877965e0c53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85203990"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自訂原則中定義 OpenID Connect 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）提供[OpenID connect](https://openid.net/2015/04/17/openid-connect-certification-program/)通訊協定識別提供者的支援。 OpenID Connect 1.0 會定義 OAuth 2.0 上的身分識別層，是先進新式驗證通訊協定的代表。 使用 OpenID Connect 技術設定檔，您可以與 OpenID Connect 型識別提供者同盟，例如 Azure AD。 與身分識別提供者聯盟可讓使用者使用其現有的社交或企業身分識別登入。

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `OpenIdConnect`。 例如，**MSA-OIDC** 技術設定檔的通訊協定是 `OpenIdConnect`：

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>輸入宣告

**InputClaims** 和 **InputClaimsTransformations** 元素不是必要項目。 但您可能會想要將其他參數傳送至您的識別提供者。 下列範例會將 **domain_hint** 查詢字串參數 (包含 `contoso.com` 的值) 新增至授權要求。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>輸出宣告

**OutputClaims**元素包含 OpenID connect 識別提供者所傳回的宣告清單。 您可能需要將原則中定義的宣告名稱對應至識別提供者中定義的名稱。 只要設定了 `DefaultValue` 屬性，也可以加入識別提供者未傳回的宣告。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

下列範例顯示 Microsoft 帳戶識別提供者傳回的宣告：

- 對應至**issuerUserId**宣告的**子**宣告。
- 對應至 **displayName** 宣告的 **name** 宣告。
- **email** 無名稱對應。

技術設定檔也會傳回識別提供者未傳回的宣告：

- 包含識別提供者名稱的 **identityProvider** 宣告。
- 具有 **socialIdpAuthentication** 預設值的 **authenticationSource** 宣告。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| client_id | Yes | 識別提供者的應用程式識別碼。 |
| IdTokenAudience | No | id_token 的對象。 如果指定，Azure AD B2C 會檢查身分 `aud` 識別提供者所傳回之權杖中的宣告是否等於 IdTokenAudience 中繼資料中指定的宣告。  |
| METADATA | Yes | 指向 OpenID Connect 識別提供者設定檔的 URL，也稱為 OpenID 知名設定端點。 URL 可以包含 `{tenant}` 運算式，其會以租使用者名稱取代。  |
| authorization_endpoint | No | 指向 OpenID Connect 識別提供者設定授權端點的 URL。 Authorization_endpoint 中繼資料的值優先于 `authorization_endpoint` OpenID 知名設定端點中指定的。 URL 可以包含 `{tenant}` 運算式，其會以租使用者名稱取代。 |
| 簽發者 | No | OpenID Connect 識別提供者的唯一識別碼。 簽發者中繼資料的值優先于 `issuer` OpenID 知名設定端點中指定的。  如果指定，Azure AD B2C `iss` 會檢查識別提供者所傳回之權杖中的宣告是否等於簽發者中繼資料中指定的宣告。 |
| ProviderName | No | 識別提供者的名稱。  |
| response_types | No | 根據 OpenID Connect Core 1.0 規格的回應類型。 可能的值：`id_token`、`code` 或 `token`。 |
| response_mode | No | 識別提供者用來將結果傳送回 Azure AD B2C 的方法。 可能的值：`query`、`form_post` (預設值) 或 `fragment`。 |
| scope | No | 根據 OpenID Connect Core 1.0 規格定義的要求範圍。 例如，`openid`、`profile` 和 `email`。 |
| HttpBinding | No | 繫結至存取權杖和宣告權杖端點的預期 HTTP。 可能的值：`GET` 或 `POST`。  |
| ValidTokenIssuerPrefixes | No | 此金鑰可在使用多租戶識別提供者 (例如 Azure Active Directory) 時，用於登入每個租用戶。 |
| UsePolicyInRedirectUri | No | 表明在建構重新導向 URI 時，是否使用原則。 在設定識別提供者中的應用程式時，需要指定重新導向 URI。 重新導向 URI 會指向 Azure AD B2C， `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` 。  如果指定 `false`，則需要為每個使用的原則新增重新導向 URI。 例如： `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp` 。 |
| MarkAsFailureOnStatusCode5xx | No | 表明如果 Http 狀態碼在 5xx 範圍內，是否應該將外部服務的要求標記為失敗。 預設值為 `false`。 |
| DiscoverMetadataByTokenIssuer | No | 表明是否應該使用 JWT 權杖中的簽發者探索 OIDC 中繼資料。 |
| IncludeClaimResolvingInClaimsHandling  | No | 針對輸入和輸出宣告，指定技術設定檔中是否包含[宣告解析](claim-resolver-overview.md)。 可能的值： `true` 、或 `false`   （預設）。 如果您想要在技術設定檔中使用宣告解析程式，請將此設定為 `true` 。 |

### <a name="ui-elements"></a>UI 元素
 
下列設定可用於設定失敗時顯示的錯誤訊息。 中繼資料應設定于 OpenID Connect 技術設定檔中。 錯誤訊息可以[當地語系化](localization-string-ids.md#sign-up-or-sign-in-error-messages)。

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | No | 在目錄中找不到具有所提供使用者名稱的帳戶時，要向使用者顯示的訊息。 |
| UserMessageIfInvalidPassword | No | 如果密碼不正確，要向使用者顯示的訊息。 |
| UserMessageIfOldPasswordUsed| No |  當使用舊密碼時，要向使用者顯示的訊息。|

## <a name="cryptographic-keys"></a>密碼編譯金鑰

**CryptographicKeys** 元素包含下列屬性：

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| client_secret | Yes | 識別提供者應用程式的用戶端密碼。 只有在 **response_types** 中繼資料設為 `code` 時，才需要密碼編譯金鑰。 在此情況下，Azure AD B2C 會進行另一次呼叫，以交換存取權杖的授權碼。 如果中繼資料設為 `id_token`，則可以省略密碼編譯金鑰。  |

## <a name="redirect-uri"></a>重新導向 Uri

在設定識別提供者的重新導向 URI 時，請輸入 `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`。 請務必將取代為 `{your-tenant-name}` 您的租使用者名稱。 重新導向 URI 必須全部小寫。

範例：

- [使用自訂原則新增 Microsoft 帳戶 (MSA) 作為識別提供者](identity-provider-microsoft-account-custom.md)
- [使用 Azure AD 帳戶來登入](identity-provider-azure-ad-single-tenant-custom.md)
- [使用自訂原則允許使用者登入多租用戶 Azure AD 識別提供者](identity-provider-azure-ad-multi-tenant-custom.md)
