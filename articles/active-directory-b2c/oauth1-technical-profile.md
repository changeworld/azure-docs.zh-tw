---
title: 在自訂原則中定義 OAuth1 技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自訂原則中定義 OAuth 1.0 技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d570ddbcf974936bbaa78be5799e7bd42fa6d514
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204076"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自訂原則中定義 OAuth1 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）提供[OAuth 1.0 通訊協定](https://tools.ietf.org/html/rfc5849)識別提供者的支援。 本文會說明技術設定檔的詳細規格，其可和支援此標準化通訊協定的宣告提供者互動。 透過 OAuth1 技術設定檔，您可以與以 OAuth1 為基礎的身分識別提供者（例如 Twitter）聯盟。 與身分識別提供者聯盟可讓使用者使用其現有的社交或企業身分識別登入。

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `OAuth1`。 例如，**Twitter-OAUTH1** 技術設定檔的通訊協定是 `OAuth1`。

```xml
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>輸入宣告

**InputClaims** 和 **InputClaimsTransformations** 元素是空的或不存在。

## <a name="output-claims"></a>輸出宣告

**OutputClaims** 元素包含 OAuth1 識別提供者傳回的宣告清單。 您可能需要將原則中定義的宣告名稱對應至識別提供者中定義的名稱。 只要設定 **DefaultValue** 屬性，您也可以包含識別提供者未傳回的宣告。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

下列範例顯示 Twitter 識別提供者傳回的宣告：

- 對應至**issuerUserId**宣告的**user_id**宣告。
- 對應至 **displayName** 宣告的 **screen_name** 宣告。
- **email** 宣告沒有名稱對應。

技術設定檔也會傳回識別提供者未傳回的宣告：

- 包含識別提供者名稱的 **identityProvider** 宣告。
- 具有預設值 `socialIdpAuthentication` 的 **authenticationSource** 宣告。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| client_id | Yes | 識別提供者的應用程式識別碼。 |
| ProviderName | No | 識別提供者的名稱。 |
| request_token_endpoint | Yes | 依據 RFC 5849 的要求權杖端點 URL。 |
| authorization_endpoint | Yes | 依據 RFC 5849 的授權端點 URL。 |
| access_token_endpoint | Yes | 依據 RFC 5849 的權杖端點 URL。 |
| ClaimsEndpoint | No | 使用者資訊端點的 URL。 |
| ClaimsResponseFormat | No | 宣告回應格式。|

## <a name="cryptographic-keys"></a>密碼編譯金鑰

**CryptographicKeys** 元素包含下列屬性：

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| client_secret | Yes | 識別提供者應用程式的用戶端密碼。   |

## <a name="redirect-uri"></a>重新導向 URI

當您設定識別提供者的重新導向 URL 時，請輸入 `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`。 請務必將 **tenant** 取代為您的租用戶名稱 (例如 contosob2c.onmicrosoft.com)，並將 **policyId** 取代為原則的識別碼 (例如 b2c_1a_policy)。 重新導向 URI 必須全部小寫。 為所有使用身分識別提供者登入的原則新增重新導向 URL。

如果使用的是 **b2clogin.com** 網域，而非使用 **login.microsoftonline.com**，請務必使用 b2clogin.com，而非使用 login.microsoftonline.com。

範例：

- [使用自訂原則新增 Twitter 作為 OAuth1 識別提供者](identity-provider-twitter-custom.md)













