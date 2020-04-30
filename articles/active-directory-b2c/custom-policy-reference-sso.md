---
title: 使用自訂原則的單一登入會話管理
titleSuffix: Azure AD B2C
description: 了解如何在 Azure AD B2C 中使用自訂原則管理 SSO 工作階段。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f2887ab23dd89f1a3e1e3112ce3713ef1139de8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229675"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的單一登入工作階段管理

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）中的[單一登入（SSO）會話](session-overview.md)管理可讓系統管理員在使用者已通過驗證後，控制與使用者的互動。 例如，系統管理員可以控制是否顯示選取的身分識別提供者，或是否需要再次輸入帳戶詳細資料。 本文說明如何設定 Azure AD B2C 的 SSO 設定。

SSO 工作階段管理使用相同的語意，作為自訂原則中的其他任何技術設定檔。 執行協調流程步驟時，會查詢與步驟建立關聯的技術設定檔是否有 `UseTechnicalProfileForSessionManagement` 參考。 如果有的話，接著會檢查參考的 SSO 工作階段提供者，以查看使用者是否為工作階段參與者。 如果是這樣，則會使用 SSO 工作階段提供者來重新填入工作階段。 同樣地，執行協調流程步驟完成時，如果已指定 SSO 工作階段提供者，則會使用提供者來將資訊儲存在工作階段中。

Azure AD B2C 已定義許多可使用的 SSO 工作階段提供者：

|會話提供者  |影響範圍  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  無       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C 內部會話管理員。      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Azure AD B2C 和 OAuth1、OAuth2 或 OpenId Connect 識別提供者之間。        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | 在 OAuth2 或 OpenId connect 信賴憑證者應用程式和 Azure AD B2C 之間。        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Azure AD B2C 和 SAML 識別提供者之間。 在 SAML 服務提供者（信賴憑證者應用程式）與 Azure AD B2C 之間。  |        




SSO 管理類別是使用技術設定檔的 `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` 項目所指定。

## <a name="input-claims"></a>輸入宣告

元素`InputClaims`是空的或不存在。

## <a name="persisted-claims"></a>保存的宣告

需要傳回給應用程式或後續步驟中的前置條件所使用的宣告，應該儲存在會話中，或藉由從目錄中的使用者設定檔讀取來增加。 使用持續性宣告可確保您的驗證旅程在遺失的宣告時不會失敗。 若要在工作階段中新增宣告，請使用技術設定檔的 `<PersistedClaims>` 項目。 使用提供者重新擴展工作階段時，會將持續性的宣告新增至宣告包。

## <a name="output-claims"></a>輸出宣告

`<OutputClaims>`是用來從會話中抓取宣告。

## <a name="session-providers"></a>會話提供者

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

顧名思議，此提供者不會執行任何作業。 此提供者可以用於隱藏特定技術設定檔的 SSO 行為。 下列`SM-Noop`技術設定檔包含在[自訂原則入門套件](custom-policy-get-started.md#custom-policy-starter-pack)中。

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

此提供者可以用於儲存工作階段中的宣告。 此提供者通常會在用來管理本機和同盟帳戶的技術設定檔中參考。 下列`SM-AAD`技術設定檔包含在[自訂原則入門套件](custom-policy-get-started.md#custom-policy-starter-pack)中。

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


下列`SM-MFA`技術設定檔包含在[自訂原則入門套件](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`中。 此技術設定檔會管理多因素驗證會話。

```XML
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

此提供者是用來隱藏 [選擇識別提供者] 畫面，以及從同盟識別提供者登出。 通常會在為同盟識別提供者（例如 Facebook）或 Azure Active Directory 設定的技術設定檔中參考它。 下列`SM-SocialLogin`技術設定檔包含在[自訂原則入門套件](custom-policy-get-started.md#custom-policy-starter-pack)中。

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | 否 | 目前未使用，可以忽略。 |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

此提供者是用來管理 OAuth2 或 OpenId Connect 信賴憑證者與 Azure AD B2C 之間的 Azure AD B2C 會話。

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

此提供者是用來管理信賴憑證者應用程式或同盟 SAML 識別提供者之間的 Azure AD B2C SAML 會話。 使用 SSO 提供者來儲存 SAML 識別提供者會話時， `RegisterServiceProviders`必須將設定為。 `false` 下列`SM-Saml-idp`技術設定檔是由[SAML 識別提供者技術設定檔](saml-identity-provider-technical-profile.md)所使用。

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

使用提供者來儲存 B2C SAML 會話時， `RegisterServiceProviders`必須將設定為。 `true` SAML 工作階段登出需要 `SessionIndex` 和 `NameID` 才能完成。

`SM-Saml-issuer` [SAML 簽發者技術設定檔](saml-issuer-technical-profile.md)會使用下列技術設定檔

```XML
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述|
| --- | --- | --- |
| IncludeSessionIndex | 否 | 目前未使用，可以忽略。|
| RegisterServiceProviders | 否 | 指出提供者應該註冊所有已發行判斷提示的 SAML 服務提供者。 可能的值：`true` (預設) 或 `false`。|


## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure AD B2C 的會話](session-overview.md)。
