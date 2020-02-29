---
title: 使用自訂原則的單一登入會話管理
titleSuffix: Azure AD B2C
description: 了解如何在 Azure AD B2C 中使用自訂原則管理 SSO 工作階段。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b905591266b90e5bba83e7c74b27e7f6b3cab610
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912540"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的單一登入工作階段管理

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）中的單一登入（SSO）會話管理可讓系統管理員在使用者已通過驗證後，控制與使用者的互動。 例如，系統管理員可以控制是否顯示選取的身分識別提供者，或是否需要再輸入一次本機帳戶詳細資料。 本文說明如何設定 Azure AD B2C 的 SSO 設定。

SSO 工作階段管理有兩個部分。 第一個是處理使用者與 Azure AD B2C 的直接互動，另一個則是處理使用者與外部合作對象 (例如 Facebook) 的互動。 Azure AD B2C 不會覆寫或略過外部合作對象可能保留的 SSO 工作階段。 會「記住」透過 Azure AD B2C 到達外部合作對象的路徑，這樣就不需要重新提示使用者選取其社交或企業身分識別提供者。 最終 SSO 決策是由外部合作對象所決定。

SSO 工作階段管理使用相同的語意，作為自訂原則中的其他任何技術設定檔。 執行協調流程步驟時，會查詢與步驟建立關聯的技術設定檔是否有 `UseTechnicalProfileForSessionManagement` 參考。 如果有的話，接著會檢查參考的 SSO 工作階段提供者，以查看使用者是否為工作階段參與者。 如果是這樣，則會使用 SSO 工作階段提供者來重新填入工作階段。 同樣地，執行協調流程步驟完成時，如果已指定 SSO 工作階段提供者，則會使用提供者來將資訊儲存在工作階段中。

Azure AD B2C 已定義許多可使用的 SSO 工作階段提供者：

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

SSO 管理類別是使用技術設定檔的 `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` 項目所指定。

## <a name="input-claims"></a>輸入宣告

`InputClaims` 元素是空的或不存在。 

## <a name="persisted-claims"></a>保存的宣告

需要傳回給應用程式或後續步驟中的前置條件所使用的宣告，應該儲存在會話中，或藉由從目錄中的使用者設定檔讀取來增加。 使用持續性宣告可確保您的驗證旅程在遺失的宣告時不會失敗。 若要在工作階段中新增宣告，請使用技術設定檔的 `<PersistedClaims>` 項目。 使用提供者重新擴展工作階段時，會將持續性的宣告新增至宣告包。 

## <a name="output-claims"></a>輸出宣告

`<OutputClaims>` 是用來從會話中抓取宣告。

## <a name="session-providers"></a>會話提供者

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

顧名思議，此提供者不會執行任何作業。 此提供者可以用於隱藏特定技術設定檔的 SSO 行為。 下列 `SM-Noop` 技術設定檔包含在[自訂原則入門套件](custom-policy-get-started.md#custom-policy-starter-pack)中。  

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

此提供者可以用於儲存工作階段中的宣告。 此提供者通常是在用於管理本機帳戶的技術設定檔中進行參照。 下列 `SM-AAD` 技術設定檔包含在[自訂原則入門套件](custom-policy-get-started.md#custom-policy-starter-pack)中。 

```XML
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Mananagement Provider</DisplayName>
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

下列 `SM-MFA` 技術設定檔包含在[自訂原則入門套件](custom-policy-get-started.md#custom-policy-starter-pack)`SocialAndLocalAccountsWithMfa`中。 此技術設定檔會管理多因素驗證會話。 

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

此提供者是用來隱藏 [選擇識別提供者] 畫面。 它通常是在針對外部身分識別提供者 (例如 Facebook) 所設定的技術設定檔中所參照。 下列 `SM-SocialLogin` 技術設定檔包含在[自訂原則入門套件](custom-policy-get-started.md#custom-policy-starter-pack)中。

```XML
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Mananagement Provider</DisplayName>
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

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

此提供者是用來管理信賴憑證者應用程式或同盟 SAML 識別提供者之間的 Azure AD B2C SAML 會話。 使用 SSO 提供者來儲存 SAML 識別提供者會話時，`IncludeSessionIndex` 和 `RegisterServiceProviders` 必須設定為 [`false`]。 [SAML 技術設定檔](saml-technical-profile.md)會使用下列 `SM-Saml-idp` 技術設定檔。

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IncludeSessionIndex">false</Item>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

使用提供者來儲存 B2C SAML 會話時，`IncludeSessionIndex` 和 `RegisterServiceProviders` 必須設定為 [`true`]。 SAML 工作階段登出需要 `SessionIndex` 和 `NameID` 才能完成。
 
[SAML 簽發者技術設定檔](connect-with-saml-service-providers.md)會使用下列 `SM-Saml-idp` 技術設定檔

```XML
<TechnicalProfile Id="SM-Saml-sp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```
#### <a name="metadata"></a>中繼資料
        
| 屬性 | 必要 | 描述|
| --- | --- | --- |
| IncludeSessionIndex | 否 | 指出應該儲存工作階段索引的提供者。 可能的值：`true` (預設) 或 `false`。|
| RegisterServiceProviders | 否 | 指出提供者應該註冊所有已發行判斷提示的 SAML 服務提供者。 可能的值：`true` (預設) 或 `false`。|



