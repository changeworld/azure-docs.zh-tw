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
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1734b063530f9e8a8f0429111c4c39d628bfad4e
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251765"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>關於 Azure Active Directory B2C 自訂原則中的宣告解析程式

Azure Active Directory B2C （Azure AD B2C）[自訂原則](custom-policy-overview.md)中的宣告解析程式會提供有關授權要求的內容資訊，例如原則名稱、要求相互關聯識別碼、使用者介面語言等等。

若要在輸入或輸出宣告中使用宣告解析程式，您必須在 **ClaimsSchema** 元素下方定義字串 [ClaimType](claimsschema.md)，然後在輸入或輸出宣告元素中設定宣告解析程式的 **DefaultValue**。 Azure AD B2C 會讀取宣告解析程式的值，並在技術設定檔中使用該值。

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

| 宣告 | 描述 | 範例 |
| ----- | ----------- | --------|
| {Culture:LanguageName} | 語言的雙字母 ISO 代碼。 | en |
| {Culture:LCID}   | 語言代碼的 LCID。 | 1033 |
| {Culture:RegionName} | 區域的雙字母 ISO 代碼。 | US |
| {Culture:RFC5646} | RFC5646 語言代碼。 | zh-TW |

### <a name="policy"></a>原則

| 宣告 | 描述 | 範例 |
| ----- | ----------- | --------|
| {Policy:PolicyId} | 信賴憑證者原則名稱。 | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | 信賴憑證者原則的租用戶識別碼。 | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | 信賴憑證者原則的租用戶物件識別碼。 | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | 信任架構的租用戶識別碼。 | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| 宣告 | 描述 | 範例 |
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

| 宣告 | 描述 | 範例 |
| ----- | ----------- | --------|
| {Context:BuildNumber} | 身分識別體驗架構版本 (組建編號)。  | 1.0.507.0 |
| {Context:CorrelationId} | 相互關連識別碼。  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |日期時間 (UTC)。  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |原則部署模式。  | Production |
| {Context:IPAddress} | 使用者 IP 位址。 | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>非通訊協定參數

OIDC 或 OAuth2 要求中所包含的任何參數名稱均可對應至使用者旅程圖中的宣告。 例如，來自應用程式的要求可能包含名稱為 `app_session`、`loyalty_number` 或任何自訂查詢字串的查詢字串參數。

| 宣告 | 描述 | 範例 |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | 查詢字串參數。 | hawaii |
| {OAUTH-KV:app_session} | 查詢字串參數。 | A3C5R |
| {OAUTH-KV:loyalty_number} | 查詢字串參數。 | 1234 |
| {OAUTH-KV:任何自訂查詢字串} | 查詢字串參數。 | N/A |

### <a name="oauth2"></a>OAuth2

| 宣告 | 描述 | 範例 |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | 存取權杖。 | N/A |

## <a name="using-claim-resolvers"></a>使用宣告解析程式 

您可以使用宣告解析程式搭配下列元素： 

| Item | 元素 | 設定 |
| ----- | ----------------------- | --------|
|Application Insights 技術設定檔 |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md)技術設定檔| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md)技術設定檔| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID connect](openid-connect-technical-profile.md)技術設定檔| `InputClaim`, `OutputClaim`| 1, 2|
|[宣告轉換](claims-transformation-technical-profile.md)技術設定檔| `InputClaim`, `OutputClaim`| 1, 2|
|[RESTful 提供者](restful-technical-profile.md)技術設定檔| `InputClaim`| 1, 2|
|[SAML2](saml-technical-profile.md)技術設定檔| `OutputClaim`| 1, 2|
|[自我](self-asserted-technical-profile.md)判斷技術設定檔| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile)技術設定檔| `OutputClaim`| 2 |

設定： 
1. `IncludeClaimResolvingInClaimsHandling` 中繼資料必須設定為 `true`。
1. `AlwaysUseDefaultValue` 的輸入或輸出宣告屬性必須設定為 `true`。

## <a name="claim-resolvers-samples"></a>宣告解析程式範例

### <a name="restful-technical-profile"></a>RESTful 技術設定檔

在 [RESTful](restful-technical-profile.md) 技術設定檔中，您可以傳送使用者語言、原則名稱、範圍和用戶端識別碼。 REST API 可根據這些宣告執行自訂商務邏輯，並在必要時引發當地語系化的錯誤訊息。

下列範例顯示 RESTful 技術設定檔與此案例：

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>直接登入

透過宣告解析程式，您可以預先填入登入名稱或直接登入至特定的社交識別提供者，例如 Facebook、LinkedIn 或 Microsoft 帳戶。 如需詳細資訊，請參閱[使用 Azure Active Directory B2C 設定直接登入](direct-signin.md)。

### <a name="dynamic-ui-customization"></a>動態 UI 自訂

Azure AD B2C 可讓您將查詢字串參數傳遞至您的 HTML 內容定義端點，以動態呈現頁面內容。 例如，這可讓您根據從 web 或行動應用程式傳遞的自訂參數，修改 Azure AD B2C 註冊或登入頁面上的背景影像。 如需詳細資訊，請參閱[使用 Azure Active Directory B2C 中的自訂原則動態設定 UI](custom-policy-ui-customization-dynamic.md)。 您也可以根據語言參數將 HTML 網頁當地語系化，也可以根據用戶端識別碼來變更內容。

下列範例會傳入名為**campaignId**的查詢字串參數，其值為 `hawaii`、`en-US`的**語言**代碼，以及代表用戶端識別碼的**應用程式**：

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

### <a name="content-definition"></a>內容定義

在[ContentDefinition](contentdefinitions.md) `LoadUri`中，您可以根據所使用的參數，傳送宣告解析程式以從不同的位置提取內容。 

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
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

### <a name="relying-party-policy"></a>信賴憑證者原則

在[信賴](relyingparty.md)憑證者原則的技術設定檔中，您可能會想要將租使用者識別碼或相互關聯識別碼傳送至 JWT 內的信賴憑證者應用程式。 

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
