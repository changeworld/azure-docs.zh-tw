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
ms.date: 09/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f06ae55dc48152c2c10183cc60cb098b6c3786fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89433750"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自訂原則中定義 OpenID Connect 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 提供 [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) 通訊協定身分識別提供者的支援。 OpenID Connect 1.0 會定義 OAuth 2.0 上的身分識別層，是先進新式驗證通訊協定的代表。 使用 OpenID Connect 技術設定檔，您可以與 OpenID Connect 型身分識別提供者（例如 Azure AD）建立同盟。 與身分識別提供者同盟，可讓使用者使用其現有的社交或企業身分識別登入。

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

**OutputClaims**元素包含 OpenID Connect 識別提供者所傳回的宣告清單。 您可能需要將原則中定義的宣告名稱對應至識別提供者中定義的名稱。 只要設定了 `DefaultValue` 屬性，也可以加入識別提供者未傳回的宣告。

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
| client_id | 是 | 識別提供者的應用程式識別碼。 |
| IdTokenAudience | 否 | id_token 的對象。 如果有指定，Azure AD B2C 會檢查 `aud` 識別提供者所傳回之權杖中的宣告是否等於 IdTokenAudience 中繼資料中指定的宣告。  |
| METADATA | 是 | 指向 OpenID Connect 身分識別提供者設定檔的 URL，也稱為 OpenID 知名的設定端點。 URL 可以包含 `{tenant}` 運算式，該運算式會取代為租使用者名稱。  |
| authorization_endpoint | 否 | 指向 OpenID Connect 身分識別提供者設定授權端點的 URL。 Authorization_endpoint 中繼資料的值會優先于 `authorization_endpoint` OpenID 知名設定端點中的指定。 URL 可以包含 `{tenant}` 運算式，該運算式會取代為租使用者名稱。 |
| 簽發者 | 否 | OpenID Connect 識別提供者的唯一識別碼。 簽發者中繼資料的值會優先于 `issuer` OpenID 知名設定端點中的指定。  如果有指定，Azure AD B2C 會檢查 `iss` 識別提供者所傳回之權杖中的宣告是否等於簽發者中繼資料中指定的宣告。 |
| ProviderName | 否 | 識別提供者的名稱。  |
| response_types | 否 | 根據 OpenID Connect Core 1.0 規格的回應類型。 可能的值：`id_token`、`code` 或 `token`。 |
| response_mode | 否 | 識別提供者用來將結果傳送回 Azure AD B2C 的方法。 可能的值：`query`、`form_post` (預設值) 或 `fragment`。 |
| scope | 否 | 根據 OpenID Connect Core 1.0 規格定義之要求的範圍。 例如，`openid`、`profile` 和 `email`。 |
| HttpBinding | 否 | 繫結至存取權杖和宣告權杖端點的預期 HTTP。 可能的值：`GET` 或 `POST`。  |
| ValidTokenIssuerPrefixes | 否 | 此金鑰可在使用多租戶識別提供者 (例如 Azure Active Directory) 時，用於登入每個租用戶。 |
| UsePolicyInRedirectUri | 否 | 表明在建構重新導向 URI 時，是否使用原則。 在設定識別提供者中的應用程式時，需要指定重新導向 URI。 重新導向 URI 會指向 Azure AD B2C、 `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` 。  如果指定 `false`，則需要為每個使用的原則新增重新導向 URI。 例如： `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp` 。 |
| MarkAsFailureOnStatusCode5xx | 否 | 表明如果 Http 狀態碼在 5xx 範圍內，是否應該將外部服務的要求標記為失敗。 預設為 `false`。 |
| DiscoverMetadataByTokenIssuer | 否 | 表明是否應該使用 JWT 權杖中的簽發者探索 OIDC 中繼資料。 |
| IncludeClaimResolvingInClaimsHandling  | 否 | 針對輸入和輸出宣告，指定技術設定檔中是否包含 [宣告解析](claim-resolver-overview.md) 。 可能的值： `true` 或 `false`   (預設) 。 如果您想要在技術設定檔中使用宣告解析程式，請將此設定為 `true` 。 |
|token_endpoint_auth_method| 否| 指定 Azure AD B2C 將驗證標頭傳送至權杖端點的方式。 可能的值： `client_secret_post` (預設) 和 `client_secret_basic` (公開預覽) 。 如需詳細資訊，請參閱 [OpenID Connect 用戶端驗證一節](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)。 |


```xml
<Metadata>
  <Item Key="ProviderName">https://login.live.com</Item>
  <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
  <Item Key="response_types">code</Item>
  <Item Key="response_mode">form_post</Item>
  <Item Key="scope">openid profile email</Item>
  <Item Key="HttpBinding">POST</Item>
  <Item Key="UsePolicyInRedirectUri">0</Item>
  <Item Key="client_id">Your Microsoft application client ID</Item>
</Metadata>
```

### <a name="ui-elements"></a>UI 元素
 
您可以使用下列設定來設定失敗時所顯示的錯誤訊息。 中繼資料應該在 OpenID Connect 技術設定檔中設定。 錯誤訊息可以[當地語系化](localization-string-ids.md#sign-up-or-sign-in-error-messages)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | 否 | 如果在目錄中找不到具有所提供使用者名稱的帳戶，則為使用者顯示的訊息。 |
| UserMessageIfInvalidPassword | 否 | 如果密碼不正確，要向使用者顯示的訊息。 |
| UserMessageIfOldPasswordUsed| 否 |  如果使用舊密碼，要向使用者顯示的訊息。|

## <a name="cryptographic-keys"></a>密碼編譯金鑰

**CryptographicKeys** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| client_secret | 是 | 識別提供者應用程式的用戶端密碼。 只有在 **response_types** 中繼資料設為 `code` 時，才需要密碼編譯金鑰。 在此情況下，Azure AD B2C 會進行另一次呼叫，以交換存取權杖的授權碼。 如果中繼資料設為 `id_token`，則可以省略密碼編譯金鑰。  |

## <a name="redirect-uri"></a>重新導向 Uri

在設定識別提供者的重新導向 URI 時，請輸入 `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`。 請務必將取代為 `{your-tenant-name}` 您的租使用者名稱。 重新導向 URI 必須全部小寫。

範例：

- [使用自訂原則新增 Microsoft 帳戶 (MSA) 作為識別提供者](identity-provider-microsoft-account-custom.md)
- [使用 Azure AD 帳戶來登入](identity-provider-azure-ad-single-tenant-custom.md)
- [使用自訂原則允許使用者登入多租用戶 Azure AD 識別提供者](identity-provider-azure-ad-multi-tenant-custom.md)
