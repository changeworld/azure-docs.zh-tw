---
title: 在自訂策略中定義 RESTful 技術設定檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332616"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定義 Azure Active Directory B2C 自訂原則中的 RESTful 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活動目錄 B2C（Azure AD B2C）支援集成您自己的 RESTful 服務。 Azure AD B2C 會在輸入宣告集合中將資料傳送至 RESTful 服務，並在輸出宣告集合中接收資料。 有關詳細資訊，請參閱在[Azure AD B2C 自訂策略中集成 REST API 聲明交換](custom-policy-rest-api-intro.md)。  

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

## <a name="send-a-json-payload"></a>發送 JSON 有效負載

REST API 技術設定檔允許您將複雜的 JSON 負載發送到終結點。

要發送複雜的 JSON 負載：

1. 使用[生成 Jon](json-transformations.md)聲明轉換構建 JSON 有效負載。
1. 在 REST API 技術設定檔中：
    1. 添加輸入聲明轉換，引用`GenerateJson`聲明轉換。
    1. 將`SendClaimsIn`中繼資料選項設置為`body`
    1. 將`ClaimUsedForRequestPayload`中繼資料選項設置為包含 JSON 負載的聲明的名稱。
    1. 在輸入聲明中，添加對包含 JSON 負載的輸入聲明的引用。

以下示例`TechnicalProfile`使用協力廠商電子郵件服務（本例中為 SendGrid）發送驗證電子郵件。

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
| AuthenticationType | 是 | RESTful 宣告提供者正在執行的驗證類型。 可能的值：`None`、`Basic`、`Bearer` 或 `ClientCertificate`。 `None` 值表示 REST API 並非匿名。 `Basic` 值表示 REST API 受到 HTTP 基本驗證保護。 只有經過驗證的使用者 (包括 Azure AD B2C) 才能存取您的 API。 `ClientCertificate` （建議）值表示 REST API 使用用戶端憑證身份驗證限制訪問。 只有具有適當證書的服務（例如 Azure AD B2C）才能訪問 API。 該`Bearer`值指示 REST API 使用用戶端 OAuth2 承載權杖限制訪問。 |
| 允許不安全生產| 否| `AuthenticationType`指示是否可以`none`在生產環境中設置為`DeploymentMode`（[信任框架策略](trustframeworkpolicy.md)設置為`Production`， 或未指定）。 可能的值：true 或 false（預設值）。 |
| SendClaimsIn | 否 | 指定輸入宣告如何傳送至 RESTful 宣告提供者。 可能的值：`Body` (預設)、`Form`、`Header` 或 `QueryString`。 `Body` 值是以 JSON 格式在要求本文中傳送的輸入宣告。 `Form` 值是以符號 '&' 分隔的金鑰值格式在要求本文中傳送的輸入宣告。 `Header` 值是在要求標題中傳送的輸入宣告。 `QueryString` 值是在要求查詢字串中傳送的輸入宣告。 每個引用的 HTTP 謂詞如下所示：<br /><ul><li>`Body`： 郵遞</li><li>`Form`： 郵遞</li><li>`Header`： 獲取</li><li>`QueryString`： 獲取</li></ul> |
| ClaimsFormat | 否 | 當前未使用，可以忽略。 |
| 請求有效負載的索賠| 否 | 包含要發送到 REST API 的負載的字串聲明的名稱。 |
| DebugMode | 否 | 在偵錯模式中執行技術設定檔。 可能的值：`true`或`false`（預設值）。 在偵錯模式中，REST API 可以傳回更多資訊。 請參閱[返回錯誤訊息](#returning-error-message)部分。 |
| 包括索賠解決索賠處理  | 否 | 對於輸入和輸出聲明，指定[索賠解析](claim-resolver-overview.md)是否包含在技術設定檔中。 可能的值：`true`或`false` （預設值）。 如果要在技術設定檔中使用聲明解析器，則將此解決方案設置為`true`。 |
| 解決JsonPathinJonTokens  | 否 | 指示技術設定檔是否解析 JSON 路徑。 可能的值：`true`或`false`（預設值）。 使用此中繼資料可以從嵌套的 JSON 元素讀取資料。 在[輸出聲明](technicalprofiles.md#outputclaims)中，`PartnerClaimType`將 設置為要輸出的 JSON 路徑元素。 例如：`firstName.localized`或`data.0.to.0.email`。|
| 使用聲明標記| 否| 包含無記名權杖的聲明的名稱。|

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
| 承載身份驗證權杖 | 否 | OAuth 2.0 承載權杖。 |

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

您的 REST API 可能需要傳回錯誤訊息，例如「CRM 系統中找不到使用者」。 如果發生錯誤，REST API 應返回 HTTP 4xx 錯誤訊息，例如 400（錯誤請求）或 409（衝突）回應狀態碼。 回應正文包含在 JSON 中格式化的錯誤訊息：

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
| version | 是 | REST API 版本。 例如： 1.0.1 |
| status | 是 | 必須為 409 |
| 代碼 | 否 | RESTful 端點提供者的錯誤代碼，在啟用 `DebugMode` 時顯示。 |
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

有關使用 RESTful 技術設定檔的示例，請參閱以下文章：

- [在 Azure AD B2C 自訂策略中集成 REST API 聲明交換](custom-policy-rest-api-intro.md)
- [演練：在 Azure AD B2C 使用者旅程中集成 REST API 聲明交換，作為使用者輸入的驗證](custom-policy-rest-api-claims-validation.md)
- [演練：將 REST API 聲明交換添加到 Azure 活動目錄 B2C 中的自訂策略](custom-policy-rest-api-claims-validation.md)
- [保護您的 REST API 服務](secure-rest-api.md)

