---
title: 在自訂原則中定義 RESTful 技術設定檔
titleSuffix: Azure AD B2C
description: 定義 Azure Active Directory B2C 自訂原則中的 RESTful 技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80332616"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定義 Azure Active Directory B2C 自訂原則中的 RESTful 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）提供整合您自己的 RESTful 服務的支援。 Azure AD B2C 會在輸入宣告集合中將資料傳送至 RESTful 服務，並在輸出宣告集合中接收資料。 如需詳細資訊，請參閱[在您的 Azure AD B2C 自訂原則中整合 REST API 宣告交換](custom-policy-rest-api-intro.md)。  

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **handler** 屬性必須包含 Azure AD B2C 所使用之通訊協定處理常式組件的完整名稱：`Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`。

下列範例顯示的是 RESTful 技術設定檔：

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>輸入宣告

**InputClaims** 元素包含傳送至 REST API 的宣告清單。 您可以將宣告名稱對應至 REST API 中定義的名稱。 下列範例顯示了您的原則和 REST API 之間的對應。 會將 **givenName** 宣告傳送至 REST API 做為 **firstName**，而傳送 **surname** 做為 **lastName**。 **email** 宣告會依原樣設定。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** 元素可能含有 **InputClaimsTransformation** 的集合，用於修改輸入宣告或產生新的輸入宣告，然後再傳送至 REST API。

## <a name="send-a-json-payload"></a>傳送 JSON 承載

REST API 技術設定檔可讓您將複雜的 JSON 承載傳送至端點。

若要傳送複雜的 JSON 承載：

1. 使用[GenerateJson](json-transformations.md)宣告轉換來建立您的 JSON 承載。
1. 在 [REST API 技術設定檔：
    1. 使用`GenerateJson`宣告轉換的參考來新增輸入宣告轉換。
    1. 將`SendClaimsIn`中繼資料選項設定為`body`
    1. 將`ClaimUsedForRequestPayload`中繼資料選項設定為包含 JSON 承載的宣告名稱。
    1. 在輸入宣告中，將參考新增至包含 JSON 承載的輸入宣告。

下列範例`TechnicalProfile`會使用協力廠商電子郵件服務（在此案例中為 SendGrid）來傳送驗證電子郵件。

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>輸出宣告

**OutputClaims** 元素包含 REST API 傳回的宣告清單。 您可能需要將原則中定義的宣告名稱對應至 REST API 中定義的名稱。 只要設定了 `DefaultValue` 屬性，也可以加入 REST API 識別提供者未傳回的宣告。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

下列範例顯示 REST API 傳回的宣告：

- 對應至 **loyaltyNumber** 宣告名稱的 **MembershipId** 宣告。

技術設定檔也會傳回識別提供者未傳回的宣告：

- 預設值已設為 `true` 的 **loyaltyNumberIsNew** 宣告。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ServiceUrl | 是 | REST API 端點的 URL。 |
| AuthenticationType | 是 | RESTful 宣告提供者正在執行的驗證類型。 可能的值：`None`、`Basic`、`Bearer` 或 `ClientCertificate`。 `None` 值表示 REST API 並非匿名。 `Basic` 值表示 REST API 受到 HTTP 基本驗證保護。 只有經過驗證的使用者 (包括 Azure AD B2C) 才能存取您的 API。 `ClientCertificate` （建議）值表示 REST API 使用用戶端憑證驗證來限制存取。 只有具有適當憑證（例如 Azure AD B2C）的服務才能存取您的 API。 `Bearer`值表示 REST API 使用用戶端 OAuth2 持有人權杖來限制存取。 |
| AllowInsecureAuthInProduction| 否| 指出是否`AuthenticationType`可在生產環境中`none`設定為（`DeploymentMode` [TrustFrameworkPolicy](trustframeworkpolicy.md)的設定為`Production`或未指定）。 可能的值： true 或 false （預設值）。 |
| SendClaimsIn | 否 | 指定輸入宣告如何傳送至 RESTful 宣告提供者。 可能的值：`Body` (預設)、`Form`、`Header` 或 `QueryString`。 `Body` 值是以 JSON 格式在要求本文中傳送的輸入宣告。 `Form` 值是以符號 '&' 分隔的金鑰值格式在要求本文中傳送的輸入宣告。 `Header` 值是在要求標題中傳送的輸入宣告。 `QueryString` 值是在要求查詢字串中傳送的輸入宣告。 每個所叫用的 HTTP 指令動詞如下：<br /><ul><li>`Body`： POST</li><li>`Form`： POST</li><li>`Header`： GET</li><li>`QueryString`： GET</li></ul> |
| ClaimsFormat | 否 | 目前未使用，可以忽略。 |
| ClaimUsedForRequestPayload| 否 | 字串宣告的名稱，其中包含要傳送至 REST API 的承載。 |
| DebugMode | 否 | 在偵錯模式中執行技術設定檔。 可能的值`true`：、 `false`或（預設）。 在偵錯模式中，REST API 可以傳回更多資訊。 請參閱傳回[錯誤訊息](#returning-error-message)一節。 |
| IncludeClaimResolvingInClaimsHandling  | 否 | 針對輸入和輸出宣告，指定技術設定檔中是否包含[宣告解析](claim-resolver-overview.md)。 可能的值`true`：、 `false`  或（預設）。 如果您想要在技術設定檔中使用宣告解析程式，請將`true`此設定為。 |
| ResolveJsonPathsInJsonTokens  | 否 | 指出技術設定檔是否會解析 JSON 路徑。 可能的值`true`：、 `false`或（預設）。 使用此中繼資料，從嵌套的 JSON 元素讀取資料。 在[OutputClaim](technicalprofiles.md#outputclaims)中，將設定`PartnerClaimType`為您想要輸出的 JSON 路徑元素。 例如： `firstName.localized`、或`data.0.to.0.email`。|
| UseClaimAsBearerToken| 否| 包含持有人權杖的宣告名稱。|

## <a name="cryptographic-keys"></a>密碼編譯金鑰

如果驗證類型設為 `None`，則不會使用 **CryptographicKeys** 元素。

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

如果驗證類型設為 `Basic`，則 **CryptographicKeys** 元素會包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | 是 | 用來驗證的使用者名稱。 |
| BasicAuthenticationPassword | 是 | 用來驗證的密碼。 |

下列範例是使用基本驗證的技術設定檔：

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

如果驗證類型設為 `ClientCertificate`，則 **CryptographicKeys** 元素會包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ClientCertificate | 是 | 用來驗證的 X509 憑證 (RSA 金鑰組)。 |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

如果驗證類型設為 `Bearer`，則 **CryptographicKeys** 元素會包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | 否 | OAuth 2.0 持有人權杖。 |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>傳回錯誤訊息

您的 REST API 可能需要傳回錯誤訊息，例如「CRM 系統中找不到使用者」。 如果發生錯誤，REST API 應該會傳回 HTTP 4xx 錯誤訊息，例如400（不正確的要求）或409（衝突）回應狀態碼。 回應主體包含以 JSON 格式格式化的錯誤訊息：

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| version | 是 | 您的 REST API 版本。 例如：1.0。1 |
| status | 是 | 必須是409 |
| code | 否 | RESTful 端點提供者的錯誤代碼，在啟用 `DebugMode` 時顯示。 |
| requestId | 否 | RESTful 端點提供者的要求識別碼，在啟用 `DebugMode` 時顯示。 |
| userMessage | 是 | 向使用者顯示的錯誤訊息。 |
| developerMessage | 否 | 問題的詳細描述及修復方式，在啟用 `DebugMode` 時顯示。 |
| moreInfo | 否 | 指向其他資訊的的 URI，在啟用 `DebugMode` 時顯示。 |


下列範例顯示的是傳回錯誤訊息的 C# 類別：

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>後續步驟

如需使用 RESTful 技術設定檔的範例，請參閱下列文章：

- [在您的 Azure AD B2C 自訂原則中整合 REST API 宣告交換](custom-policy-rest-api-intro.md)
- [逐步解說：在您的 Azure AD B2C 使用者旅程圖中整合 REST API 宣告交換，做為使用者輸入的驗證](custom-policy-rest-api-claims-validation.md)
- [逐步解說：在 Azure Active Directory B2C 中將 REST API 宣告交換新增至自訂原則](custom-policy-rest-api-claims-validation.md)
- [保護您的 REST API 服務](secure-rest-api.md)

