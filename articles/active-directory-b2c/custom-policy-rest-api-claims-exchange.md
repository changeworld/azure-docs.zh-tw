---
title: REST API 聲明交換 - Azure 活動目錄 B2C
description: 將 REST API 聲明交換添加到活動目錄 B2C 中的自訂策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330729"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>演練：將 REST API 聲明交換添加到 Azure 活動目錄 B2C 中的自訂策略

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活動目錄 B2C（Azure AD B2C）使身份開發人員能夠在使用者旅程中集成與 RESTful API 的交互。 在本演練結束時，您將能夠創建與[RESTful 服務](custom-policy-rest-api-intro.md)交互的 Azure AD B2C 使用者旅程。

在此方案中，我們通過與企業業務線工作流集成來豐富使用者的權杖資料。 在註冊或使用本地或聯合帳戶登錄期間，Azure AD B2C 調用 REST API 從遠端資料源獲取使用者的擴展設定檔資料。 在此示例中，Azure AD B2C 會發送使用者的唯一識別碼物件 Id。 然後，REST API 返回使用者的帳戶餘額（亂數）。 使用此示例作為起點，與您自己的 CRM 系統、行銷資料庫或任何業務線工作流集成。

您還可以將交互設計為驗證技術設定檔。 當 REST API 將驗證螢幕上的資料並返回聲明時，這一點是合適的。 有關詳細資訊，請參閱[演練：在 Azure AD B2C 使用者旅程中集成 REST API 聲明交換以驗證使用者輸入](custom-policy-rest-api-claims-validation.md)。

## <a name="prerequisites"></a>Prerequisites

- 完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該有一個使用本機帳戶來註冊和登入的有效自訂原則。
- 瞭解如何在[Azure AD B2C 自訂策略中集成 REST API 聲明交換](custom-policy-rest-api-intro.md)。

## <a name="prepare-a-rest-api-endpoint"></a>準備 REST API 終結點

在本演練中，應該有一個 REST API，用於驗證使用者的 Azure AD B2C 物件 Id 是否已在後端系統中註冊。 如果註冊，REST API 將返回使用者帳戶餘額。 否則，REST API 將新帳戶註冊到目錄中並返回起始餘額`50.00`。

以下 JSON 代碼說明了 Azure AD B2C 將發送到 REST API 終結點的資料。 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

REST API 驗證資料後，必須返回 HTTP 200 （Ok），並包含以下 JSON 資料：

```json
{
    "balance": "760.50"
}
```

REST API 終結點的設置不在本文的討論範圍之內。 我們創建了[Azure 函數](https://docs.microsoft.com/azure/azure-functions/functions-reference)示例。 您可以在[GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)訪問完整的 Azure 函數代碼。

## <a name="define-claims"></a>定義聲明

聲明在 Azure AD B2C 策略執行期間提供資料的臨時存儲。 您可以在[聲明架構](claimsschema.md)部分中聲明聲明聲明。 

1. 打開策略的擴展檔。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 找到[聲明架構](claimsschema.md)元素。 如果此元素不存在，請加以新增。
1. 將以下聲明添加到**聲明架構**元素。  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>配置 RESTful API 技術設定檔 

[寧靜的技術設定檔](restful-technical-profile.md)支援與您自己的 RESTful 服務進行介面。 Azure AD B2C 將資料發送到`InputClaims`集合中的 RESTful 服務，並在`OutputClaims`集合中接收資料。 在檔中<em>**`TrustFrameworkExtensions.xml`**</em>查找**聲明提供程式**元素，並添加新的聲明提供程式，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

在此示例中，`userLanguage`將像在 JSON 負載中一樣`lang`發送到 REST 服務。 `userLanguage`聲明的值包含當前使用者語言識別碼。 有關詳細資訊，請參閱[聲明解析器](claim-resolver-overview.md)。

上面`AuthenticationType`的注釋並`AllowInsecureAuthInProduction`指定在移動到生產環境時應所做的更改。 要瞭解如何保護用於生產的 RESTful API，請參閱安全[RESTful API](secure-rest-api.md)。

## <a name="add-an-orchestration-step"></a>添加業務流程步驟

[使用者旅程](userjourneys.md)指定策略允許依賴方應用程式獲取使用者所需的聲明的顯式路徑。 使用者旅程圖會表示為必須遵循才能獲得成功交易的協調流程序列。 您可以添加或減去業務流程步驟。 在這種情況下，您將添加一個新的業務流程步驟，用於在使用者通過 REST API 呼叫註冊或登錄後增強提供給應用程式的資訊。

1. 開啟原則的基底檔案。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 搜尋 `<UserJourneys>` 元素。 複製整個元素，然後將其刪除。
1. 打開策略的擴展檔。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 在`<ClaimsProviders>`元素`<UserJourneys>`關閉後，將 粘貼到擴展檔中。
1. 找到`<UserJourney Id="SignUpOrSignIn">`， 並在最後一個業務流程步驟之前添加以下業務流程步驟。

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. 通過將 更改為 重`Order`構最後一個業務流程步驟`8`。 最後兩個業務流程步驟應如下所示：

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. 重複**設定檔編輯**和**密碼重設**使用者旅程的最後兩個步驟。


## <a name="include-a-claim-in-the-token"></a>在權杖中包含聲明 

要將`balance`聲明返回到依賴方應用程式，請向<em>`SocialAndLocalAccounts/`</em>檔添加輸出聲明。 添加輸出聲明將在使用者成功旅程後將聲明發送到權杖中，並將發送到應用程式。 修改依賴方部分中的技術設定檔元素以添加`balance`為輸出聲明。
 
```xml
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

對**設定檔編輯.xml**和**PasswordReset.xml**使用者旅程重複此步驟。

保存您更改的檔：*信任框架Base.xml*， 和*信任框架擴展.xml*， *SignUpOrSignin.xml*，*設定檔編輯.xml*， 和*密碼Reset.xml*. . 

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 通過在頂部功能表中選擇**目錄 + 訂閱**篩選器並選擇包含 Azure AD 租戶的目錄，請確保使用的目錄包含 Azure AD 租戶。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [識別體驗架構]****。
1. 選擇 **"上傳自訂策略**"，然後上傳您更改的策略檔 *：TrustFrameworkBase.xml*和*TrustFramework 擴展.xml、SignUpOrSignin.xml、ProfileEdit.xml*和*PasswordReset.xml*。 *SignUpOrSignin.xml* *ProfileEdit.xml* 
1. 選取您上傳的註冊或登入原則，按一下 [立即執行]**** 按鈕。
1. 您應該能夠使用電子郵件地址或 Facebook 帳戶進行註冊。
1. 傳送回您的應用程式的權杖包含 `balance` 宣告。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>後續步驟


## <a name="next-steps"></a>後續步驟

要瞭解如何保護 API，請參閱以下文章：

- [逐步解說︰將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為協調流程步驟](custom-policy-rest-api-claims-exchange.md)
- [保護您的 RESTful API](secure-rest-api.md)
- [參考： RESTful 技術設定檔](restful-technical-profile.md)
