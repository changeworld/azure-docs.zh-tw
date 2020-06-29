---
title: 在自訂原則中定義 SAML 簽發者的技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自訂原則中定義安全性聲明標記語言權杖 (SAML) 簽發者的技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f7beccde92030d1e01633f4e4044849d7e91d05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229947"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自訂原則中定義 SAML 權杖簽發者的技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 會在處理每個驗證流程時發出數種安全性權杖。 SAML 權杖簽發者其技術設定檔會發出要傳回至信賴憑證者應用程式 (服務提供者) 的 SAML 權杖。 此技術設定檔通常是使用者旅程圖中的最後一個協調流程步驟。

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `None`。 請將 **OutputTokenFormat** 元素設定為 `SAML2`。

下列範例顯示 `Saml2AssertionIssuer` 的技術設定檔：

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>輸入、輸出和保存宣告

**InputClaims**、**OutputClaims** 和 **PersistClaims** 元素是空的或不存在。 **InutputClaimsTransformations** 和 **OutputClaimsTransformations** 元素也不存在。

## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| IssuerUri | 否 | 出現在 SAML 回應中的簽發者名稱。 此值應該與信賴憑證者應用程式中所設定的名稱相同。 |

## <a name="cryptographic-keys"></a>密碼編譯金鑰

CryptographicKeys 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| MetadataSigning | 是 | 用來簽署 SAML 中繼資料的 X509 憑證 (RSA 金鑰組)。 Azure AD B2C 會使用此金鑰來簽署中繼資料。 |
| SamlMessageSigning| 是| 指定用來簽署 SAML 訊息的 X509 憑證 (RSA 金鑰組)。 Azure AD B2C 使用此金鑰來簽署傳送至信賴憑證者的回應 `<samlp:Response>`。|

## <a name="session-management"></a>工作階段管理

若要在信賴憑證者應用程式 (`UseTechnicalProfileForSessionManagement` 項目的屬性) 之間設定 Azure AD B2C SAML 工作階段，請參考 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO 工作階段。

## <a name="next-steps"></a>後續步驟

如需使用 SAML簽發者技術設定檔的範例，請參閱下列文章：

- [在 Azure AD B2C 中註冊 SAML 應用程式](connect-with-saml-service-providers.md)












