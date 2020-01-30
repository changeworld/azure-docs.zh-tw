---
title: 自訂原則中的宣告解析程式
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure Active Directory B2C 的自訂原則中使用宣告解析程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bc8dbfd315702f666d6b811e855d6bcd99df938e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836043"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>關於 Azure Active Directory B2C 自訂原則中的宣告解析程式

Azure Active Directory B2C （Azure AD B2C）[自訂原則](custom-policy-overview.md)中的宣告解析程式會提供有關授權要求的內容資訊，例如原則名稱、要求相互關聯識別碼、使用者介面語言等等。

若要在輸入或輸出宣告中使用宣告解析程式，您必須在 [ClaimsSchema](claimsschema.md) 元素下方定義字串 **ClaimType**，然後在輸入或輸出宣告元素中設定宣告解析程式的 **DefaultValue**。 Azure AD B2C 會讀取宣告解析程式的值，並在技術設定檔中使用該值。

在下列範例中，會定義名為 `correlationId`、**DataType** 為 `string` 的宣告類型。

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

在技術設定檔中，將宣告解析程式對應至宣告類型。 Azure AD B2C 會將宣告解析程式 `{Context:CorrelationId}` 的值填入宣告 `correlationId` 中，然後將宣告傳送至技術設定檔。

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>宣告解析程式類型

以下各節會列出可用的宣告解析程式。

### <a name="culture"></a>文化特性

| 宣告 | 說明 | 範例 |
| ----- | ----------- | --------|
| {Culture:LanguageName} | 語言的雙字母 ISO 代碼。 | en |
| {Culture:LCID}   | 語言代碼的 LCID。 | 1033 |
| {Culture:RegionName} | 區域的雙字母 ISO 代碼。 | 美國 |
| {Culture:RFC5646} | RFC5646 語言代碼。 | zh-TW |

### <a name="policy"></a>原則

| 宣告 | 說明 | 範例 |
| ----- | ----------- | --------|
| {Policy:PolicyId} | 信賴憑證者原則名稱。 | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | 信賴憑證者原則的租用戶識別碼。 | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | 信賴憑證者原則的租用戶物件識別碼。 | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | 信任架構的租用戶識別碼。 | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| 宣告 | 說明 | 範例 |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |`acr_values` 查詢字串參數。 | N/A |
| {OIDC:ClientId} |`client_id` 查詢字串參數。 | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |`domain_hint` 查詢字串參數。 | facebook.com |
| {OIDC:LoginHint} |  `login_hint` 查詢字串參數。 | someone@contoso.com |
| {OIDC:MaxAge} | `max_age`。 | N/A |
| {OIDC:Nonce} |`Nonce` 查詢字串參數。 | defaultNonce |
| {OIDC:Prompt} | `prompt` 查詢字串參數。 | login |
| {OIDC:Resource} |`resource` 查詢字串參數。 | N/A |
| {OIDC:scope} |`scope` 查詢字串參數。 | openid |

### <a name="context"></a>Context

| 宣告 | 說明 | 範例 |
| ----- | ----------- | --------|
| {Context:BuildNumber} | 身分識別體驗架構版本 (組建編號)。  | 1.0.507.0 |
| {Context:CorrelationId} | 相互關連識別碼。  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |日期時間 (UTC)。  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |原則部署模式。  | 生產環境 |
| {Context:IPAddress} | 使用者 IP 位址。 | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>非通訊協定參數

OIDC 或 OAuth2 要求中所包含的任何參數名稱均可對應至使用者旅程圖中的宣告。 例如，來自應用程式的要求可能包含名稱為 `app_session`、`loyalty_number` 或任何自訂查詢字串的查詢字串參數。

| 宣告 | 說明 | 範例 |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | 查詢字串參數。 | hawaii |
| {OAUTH-KV:app_session} | 查詢字串參數。 | A3C5R |
| {OAUTH-KV:loyalty_number} | 查詢字串參數。 | 1234 |
| {OAUTH-KV:任何自訂查詢字串} | 查詢字串參數。 | N/A |

### <a name="oauth2"></a>OAuth2

| 宣告 | 說明 | 範例 |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | 存取權杖。 | N/A |

## <a name="how-to-use-claim-resolvers"></a>如何使用宣告解析程式

### <a name="restful-technical-profile"></a>RESTful 技術設定檔

在 [RESTful](restful-technical-profile.md) 技術設定檔中，您可以傳送使用者語言、原則名稱、範圍和用戶端識別碼。 REST API 可根據這些宣告執行自訂商務邏輯，並在必要時引發當地語系化的錯誤訊息。

下列範例顯示的是 RESTful 技術設定檔：

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>直接登入

透過宣告解析程式，您可以預先填入登入名稱或直接登入至特定的社交識別提供者，例如 Facebook、LinkedIn 或 Microsoft 帳戶。 如需詳細資訊，請參閱[使用 Azure Active Directory B2C 設定直接登入](direct-signin.md)。

### <a name="dynamic-ui-customization"></a>動態 UI 自訂

Azure AD B2C 可讓您將查詢字串參數傳至 HTML 內容定義端點，以便您動態轉譯頁面內容。 例如，您可以根據從 Web 或行動裝置應用程式傳遞的自訂參數，變更 Azure AD B2C 註冊或登入頁面的背景影像。 如需詳細資訊，請參閱[使用 Azure Active Directory B2C 中的自訂原則動態設定 UI](custom-policy-ui-customization-dynamic.md)。 您也可以根據語言參數將 HTML 網頁當地語系化，也可以根據用戶端識別碼來變更內容。

下列範例會在查詢字串中傳入名為 **campaignId** (值為 `hawaii`) 的參數、**語言**代碼 `en-US`，和代表用戶端識別碼的 **app**：

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

因此，Azure AD B2C 會將上述參數傳送至 HTML 內容頁面：

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Application Insights 技術設定檔

透過 Azure Application Insights 和宣告解析程式，您可以取得關於使用者行為的深入解析。 在 Application Insights 技術設定檔中，您可以將保存的輸入宣告傳送至 Azure Application Insights。 如需詳細資訊，請參閱[使用 Application Insights 在 Azure AD B2C 旅程圖內追蹤使用者行為](analytics-with-application-insights.md)。 下列範例會將原則識別碼、相互關聯識別碼、語言和用戶端識別碼傳送至 Azure Application Insights。

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```
