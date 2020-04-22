---
title: RelyingParty - Azure Active Directory B2C | Microsoft Docs
description: 指定 Azure Active Directory B2C 中自訂原則的 RelyingParty 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 733a33881fe3acc962aeda4b05a1b01be4e148ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680366"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**「依賴方」** 元素指定使用者旅程,以便強制執行當前對 Azure 活動目錄 B2C (Azure AD B2C) 的請求。 它也會指定信賴憑證者 (RP) 應用程式需要用來作為所發出權杖一部分的宣告清單。 RP 應用程式 (例如 Web、行動或傳統型應用程式) 會呼叫 RP 原則檔。 RP 原則檔會執行特定工作，例如登入、重設密碼或編輯設定檔。 多個應用程式可以使用相同的 RP 原則，而單一應用程式可以使用多個原則。 所有的 RP 應用程式都會透過宣告接收相同的權杖，而使用者會經歷相同的使用者旅程圖。

下列範例顯示 *B2C_1A_signup_signin* 原則檔中的 **RelyingParty** 元素：

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

選擇性的 **RelyingParty** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | RP 應用程式的預設使用者旅程圖。 |
| UserJourneyBehaviors | 0:1 | 使用者旅程圖行為的範圍。 |
| TechnicalProfile | 1:1 | RP 應用程式所支援的技術設定檔。 技術設定檔會提供適用於 RP 應用程式的合約來連絡 Azure AD B2C。 |

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney` 元素會指定對使用者旅程圖識別碼的參考，通常定義於基本或擴充原則中。 下列範例顯示 **RelyingParty** 元素中所指定的註冊或登入使用者旅程圖：

*B2C_1A_signup_signin* 原則：

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* 或 *B2C_1A_TrustFrameworkExtensionPolicy*：

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

**DefaultUserJourney** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 原則中使用者旅程圖的識別碼。 如需詳細資訊，請參閱[使用者旅程圖](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

**UserJourneyBehaviors** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | 使用者旅程圖之單一登入 (SSO) 工作階段行為的範圍。 |
| SessionExpiryType |0:1 | 工作階段的驗證行為。 可能的值：`Rolling` 或 `Absolute`。 `Rolling` 值 (預設) 表示，只要使用者在應用程式中持續為作用中狀態，該使用者就會保持登入。 `Absolute` 值表示在經過應用程式工作階段存留期所指定的期間之後，就會強制使用者進行重新驗證。 |
| SessionExpiryInSeconds | 0:1 | Azure AD B2C 工作階段 Cookie 的存留期，會以成功驗證時儲存於使用者瀏覽器上的整數來指定。 |
| JourneyInsights | 0:1 | 要使用的 Azure Application Insights 檢測金鑰。 |
| ContentDefinitionParameters | 0:1 | 要附加至內容定義負載 URI 的金鑰值組清單。 |
|ScriptExecution| 0:1| 支援的[JavaScript](javascript-samples.md)執行模式。 可能的值:`Allow``Disallow`或 (預設值)。

### <a name="singlesignon"></a>SingleSignOn

**SingleSignOn** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 影響範圍 | 是 | 單一登入行為的範圍。 可能的值：`Suppressed`、`Tenant`、`Application` 或 `Policy`。 該`Suppressed`值指示行為被抑制,並且始終提示用戶選擇標識提供程式。  `Tenant` 值表示會將行為套用到租用戶中的所有原則。 例如，如果使用者瀏覽租用戶的兩個原則旅程圖，系統不會提示該使用者選取識別提供者。 `Application` 值表示會將行為套用到適用於提出要求之應用程式的所有原則。 例如，如果使用者瀏覽應用程式的兩個原則旅程圖，系統不會提示該使用者選取識別提供者。 `Policy` 值表示只會將行為套用到某個原則。 例如，如果使用者瀏覽信任架構的兩個原則旅程圖，系統會在該使用者於原則之間進行切換時提示其選取識別提供者。 |
| KeepAliveInDays | 是 | 會控制使用者保持登入的時間長度。 將值設為 0 會關閉 KMSI 功能。 如需詳細資訊，請參閱[讓我保持登入](custom-policy-keep-me-signed-in.md)。 |
|強制執行 IdTokenhintonLogout| 否|  強制將以前頒發的 ID 權碼傳遞給登出終結點,作為有關最終使用者當前與用戶端的身份驗證會話的提示。 可能的值：`false` (預設) 或 `true`。 關於詳細資訊,請參閱使用[OpenID 連線進行 Web 登入](openid-connect.md)。  |


## <a name="journeyinsights"></a>JourneyInsights

**JourneyInsights** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| TelemetryEngine | 是 | 值必須是 `ApplicationInsights`。 |
| InstrumentationKey | 是 | 字串，其中包含 Application Insights 元素的檢測金鑰。 |
| DeveloperMode | 是 | 可能的值：`true` 或 `false`。 如果是 `true`，Application Insights 就會透過處理管線加速遙測。 此設定適用於開發，但僅限於大量磁碟區。詳細的活動記錄只是設計來協助開發自訂原則。 請勿在生產環境中使用開發模式。 記錄會收集往返識別提供者在開發期間所傳送的所有宣告。 如果在生產環境中使用，開發人員會負責他們自己的 App Insights 記錄中收集的 PII (私人識別資訊)。 將此值設定為 `true` 時，只會收集這些詳細的記錄。|
| ClientEnabled | 是 | 可能的值：`true` 或 `false`。 如果是 `true`，則傳送 ApplicationInsights 用戶端指令碼來追蹤頁面檢視和用戶端錯誤。 |
| ServerEnabled | 是 | 可能的值：`true` 或 `false`。 如果是 `true`，則將現有的 UserJourneyRecorder JSON 當作自訂事件傳送至 Application Insights。 |
| TelemetryVersion | 是 | 值必須是 `1.0.0`。 |

有關詳細資訊,請參閱[收集日誌](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

使用 Azure AD B2C 中的自訂原則，您可以在查詢字串中傳送參數。 將參數傳遞至您的 HTML 端點，即可動態變更網頁內容。 例如，視您從 Web 或行動裝置應用程式傳遞的參數而定，您可以變更 Azure AD B2C 註冊或登入背景影像。 Azure AD B2C 會將查詢字串參數傳遞到您的動態 HTML 檔案，例如 aspx 檔案。

下列範例會在查詢字串中傳遞名為 `campaignId` 且值為 `hawaii` 的參數：

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**ContentDefinitionParameters** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | 字串，其中包含附加至內容定義負載 URI 查詢字串的金鑰值組。 |

**ContentDefinitionParameter** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 名稱 | 是 | 金鑰值組的名稱。 |

如需詳細資訊，請參閱[使用自訂原則設定具有動態內容的 UI](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>TechnicalProfile

**TechnicalProfile** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 值必須是 `PolicyProfile`。 |

**TechnicalProfile** 包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | 包含技術設定檔名稱的字串。 |
| 描述 | 0:1 | 包含技術設定檔描述的字串。 |
| 通訊協定 | 1:1 | 用於同盟的通訊協定。 |
| 中繼資料 | 0:1 | 金鑰/值組的 *Item* 集合，通訊協定會在交易過程中利用它來與端點進行通訊，以設定信賴憑證者與其他社群參與者之間的互動。 |
| OutputClaims | 1:1 | 宣告類型清單，可取得來作為技術設定檔中的輸出。 這些元素中的每一個均會參考已經定義於 **ClaimsSchema** 區段中或此原則檔所繼承之原則中的 **ClaimType**。 |
| SubjectNamingInfo | 1:1 | 權杖中所使用的主體名稱。 |

**Protocol** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 名稱 | 是 | Azure AD B2C 所支援的有效通訊協定名稱，可用來作為技術設定檔的一部分。 可能的值：`OpenIdConnect` 或 `SAML2`。 `OpenIdConnect` 值代表以每個 OpenID 基礎規格為依據的 OpenID Connect 1.0 通訊協定標準。 `SAML2` 代表以每個 OASIS 規格為依據的 SAML 2.0 通訊協定標準。 |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | 信賴憑證者所訂閱之原則支援清單中預期的宣告類型名稱。 此宣告會用來做為技術設定檔的輸出。 |

**OutputClaim** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 是 | 對已經定義於原則檔 **ClaimsSchema** 區段中之 **ClaimType** 的參考。 |
| DefaultValue | 否 | 如果宣告值是空的時可以使用的預設值。 |
| PartnerClaimType | 否 | 以與 ClaimType 定義中設定的不同名稱來傳送宣告。 |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

使用 **SubjectNameingInfo** 元素，您可以控制權杖主體的值：
- **JWT 權杖**`sub`- 聲明。 這是權杖判斷資訊時所針對的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 它可用來執行安全的授權檢查，例如，將權杖用於存取資源時。 根據預設，主體宣告會填入目錄中使用者的物件識別碼。 如需詳細資訊，請參閱[權杖、工作階段及單一登入設定](session-behavior.md)。
- **SAML 權杖**：可識別主體元素的 `<Subject><NameID>` 元素。

**SubjectNamingInfo** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ClaimType | 是 | 對輸出宣告之 **PartnerClaimType** 的參考。 輸出宣告必須定義於信賴憑證者原則 **OutputClaims** 集合中。 |

下面的範例展示如何定義 OpenID 連接依賴方。 主體名稱資訊會設定為 `objectId`：

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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
JWT 權杖包含具使用者 objectId 的 `sub` 宣告：

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
