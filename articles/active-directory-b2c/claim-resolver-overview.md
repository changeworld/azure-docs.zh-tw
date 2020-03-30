---
title: 自訂策略中的聲明解析器
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure 活動目錄 B2C 中的自訂策略中使用聲明解析器。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cba97b84f77bd4e2d4cfd97601fa4f8637105eb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051415"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>關於 Azure Active Directory B2C 自訂原則中的宣告解析程式

Azure 活動目錄 B2C （Azure AD B2C）[自訂策略](custom-policy-overview.md)中的聲明解析器提供有關授權請求的上下文資訊，例如策略名稱、請求關聯 ID、使用者介面語言等。

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
| [OIDC：密碼]| [資源擁有者密碼憑據流](ropc-custom.md)使用者的密碼。| 密碼1| 
| {OIDC:Prompt} | `prompt` 查詢字串參數。 | login |
| [OIDC：重定向Uri] |`redirect_uri` 查詢字串參數。 | https://jwt.ms |
| {OIDC:Resource} |`resource` 查詢字串參數。 | N/A |
| {OIDC:scope} |`scope` 查詢字串參數。 | openid |
| [OIDC：使用者名]| [資源擁有者密碼憑據流](ropc-custom.md)使用者的使用者名。| emily@contoso.com| 

### <a name="context"></a>Context

| 宣告 | 描述 | 範例 |
| ----- | ----------- | --------|
| {Context:BuildNumber} | 身分識別體驗架構版本 (組建編號)。  | 1.0.507.0 |
| {Context:CorrelationId} | 相互關連識別碼。  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |日期時間 (UTC)。  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |原則部署模式。  | Production |
| {Context:IPAddress} | 使用者 IP 位址。 | 11.111.111.11 |
| [上下文：KMSI] | 指示是否選中["保持我登錄](custom-policy-keep-me-signed-in.md)"核取方塊。 |  true |

### <a name="non-protocol-parameters"></a>非通訊協定參數

OIDC 或 OAuth2 要求中所包含的任何參數名稱均可對應至使用者旅程圖中的宣告。 例如，來自應用程式的要求可能包含名稱為 `app_session`、`loyalty_number` 或任何自訂查詢字串的查詢字串參數。

| 宣告 | 描述 | 範例 |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | 查詢字串參數。 | Hawaii |
| {OAUTH-KV:app_session} | 查詢字串參數。 | A3C5R |
| {OAUTH-KV:loyalty_number} | 查詢字串參數。 | 1234 |
| {OAUTH-KV:任何自訂查詢字串} | 查詢字串參數。 | N/A |

### <a name="oauth2"></a>OAuth2

| 宣告 | 描述 | 範例 |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | 存取權杖。 | N/A |


### <a name="saml"></a>SAML

| 宣告 | 描述 | 範例 |
| ----- | ----------- | --------|
| [SAML：AuthnCoNtextClass引用] | SAML`AuthnContextClassRef`請求中的元素值。 | urn：oasis：名稱：tc：SAML：2.0：ac：類：密碼保護傳輸 |
| [SAML：名稱Id政策格式] | 屬性`Format`，`NameIDPolicy`來自 SAML 請求的元素。 | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| [SAML：發行人] |  SAML`Issuer`請求的 SAML 元素值。| `https://contoso.com` |
| [SAML：允許創建] | 屬性`AllowCreate`值，來自 SAML 請求`NameIDPolicy`的元素。 | True |
| [薩姆·斯圖爾：強制奧特恩] | 屬性`ForceAuthN`值，來自 SAML 請求`AuthnRequest`的元素。 | True |
| [SAML：供應商名稱] | 屬性`ProviderName`值，來自 SAML 請求`AuthnRequest`的元素。| Contoso.com |

## <a name="using-claim-resolvers"></a>使用聲明解析器

您可以將聲明解析器與以下元素一起使用：

| Item | 元素 | 設定 |
| ----- | ----------------------- | --------|
|Application Insights 技術設定檔 |`InputClaim` | |
|[Azure 活動目錄](active-directory-technical-profile.md)技術設定檔| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md)技術簡介| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID 連接](openid-connect-technical-profile.md)技術設定檔| `InputClaim`, `OutputClaim`| 1, 2|
|[索賠轉換](claims-transformation-technical-profile.md)技術概況| `InputClaim`, `OutputClaim`| 1, 2|
|[RESTful 供應商](restful-technical-profile.md)技術設定檔| `InputClaim`| 1, 2|
|[SAML2](saml-technical-profile.md)技術設定檔| `OutputClaim`| 1, 2|
|[自我斷言](self-asserted-technical-profile.md)的技術設定檔| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[依託方](relyingparty.md#technicalprofile)技術概況| `OutputClaim`| 2 |

設定：
1. 中繼資料`IncludeClaimResolvingInClaimsHandling`必須設置為`true`。
1. 必須將輸入或輸出聲明`AlwaysUseDefaultValue`屬性設置為`true`。

## <a name="claim-resolvers-samples"></a>聲明解析器示例

### <a name="restful-technical-profile"></a>RESTful 技術設定檔

在 [RESTful](restful-technical-profile.md) 技術設定檔中，您可以傳送使用者語言、原則名稱、範圍和用戶端識別碼。 根據聲明，REST API 可以運行自訂業務邏輯，並在必要時引發當地語系化的錯誤訊息。

下面的示例顯示了具有此方案的 RESTful 技術設定檔：

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

Azure AD B2C 使您能夠將查詢字串參數傳遞給 HTML 內容定義終結點，以動態呈現頁面內容。 例如，這允許根據從 Web 或移動應用程式傳遞的自訂參數修改 Azure AD B2C 註冊或登錄頁上的背景圖像。 如需詳細資訊，請參閱[使用 Azure Active Directory B2C 中的自訂原則動態設定 UI](custom-policy-ui-customization.md)。 您也可以根據語言參數將 HTML 網頁當地語系化，也可以根據用戶端識別碼來變更內容。

以下示例傳遞名為**campaignId**的查詢字串參數中，其值`Hawaii`為**language**`en-US`，和 表示用戶端 ID**的語言**代碼和應用：

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

因此，Azure AD B2C 將上述參數發送到 HTML 內容頁：

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>內容定義

在[Content定義中](contentdefinitions.md)`LoadUri`，您可以根據所使用的參數發送聲明解析器從不同位置提取內容。

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

### <a name="relying-party-policy"></a>依靠方政策

在["依賴方](relyingparty.md)"策略技術設定檔中，您可能希望將租戶 ID 或相關 ID 發送到 JWT 中的依賴方應用程式。

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
