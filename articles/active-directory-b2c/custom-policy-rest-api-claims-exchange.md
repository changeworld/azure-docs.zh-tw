---
title: REST API 宣告交換-Azure Active Directory B2C
description: 將 REST API 宣告交換新增至 Active Directory B2C 中的自訂原則。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80330729"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>逐步解說：在 Azure Active Directory B2C 中將 REST API 宣告交換新增至自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）讓身分識別開發人員能夠在使用者旅程圖中整合與 RESTful API 的互動。 在本逐步解說結束時，您將能夠建立與[RESTful 服務](custom-policy-rest-api-intro.md)互動的 Azure AD B2C 使用者旅程圖。

在此案例中，我們藉由與公司企業營運工作流程整合，來擴充使用者的權杖資料。 在使用本機或同盟帳戶進行註冊或登入時，Azure AD B2C 會叫用 REST API，以從遠端資料源取得使用者的擴充設定檔資料。 在此範例中，Azure AD B2C 會傳送使用者的唯一識別碼（objectId）。 然後 REST API 會傳回使用者的帳戶餘額（亂數字）。 使用此範例作為起點，以與您自己的 CRM 系統、行銷資料庫或任何企業營運工作流程進行整合。

您也可以將互動設計為驗證技術設定檔。 當 REST API 將會在螢幕上驗證資料並傳回宣告時，適合這種情況。 如需詳細資訊，請參閱[逐步解說：在您的 Azure AD B2C 使用者旅程圖中整合 REST API 宣告交換以驗證使用者輸入](custom-policy-rest-api-claims-validation.md)。

## <a name="prerequisites"></a>Prerequisites

- 完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該有一個使用本機帳戶來註冊和登入的有效自訂原則。
- 瞭解如何[在您的 Azure AD B2C 自訂原則中整合 REST API 宣告交換](custom-policy-rest-api-intro.md)。

## <a name="prepare-a-rest-api-endpoint"></a>準備 REST API 端點

在此逐步解說中，您應該會有一個 REST API，可驗證使用者的 Azure AD B2C objectId 是否已在後端系統中註冊。 如果已註冊，REST API 會傳回使用者帳戶餘額。 否則，REST API 會在目錄中註冊新的帳戶，並傳回起始餘額`50.00`。

下列 JSON 程式碼說明 Azure AD B2C 將會傳送至您 REST API 端點的資料。 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

一旦您的 REST API 驗證資料，它必須傳回 HTTP 200 （確定），其中包含下列 JSON 資料：

```json
{
    "balance": "760.50"
}
```

REST API 端點的設定超出本文的範圍。 我們已建立[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference)範例。 您可以在[GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)存取完整的 Azure 函式程式碼。

## <a name="define-claims"></a>定義宣告

宣告會在 Azure AD B2C 原則執行期間，提供資料的暫時儲存。 您可以在[宣告架構](claimsschema.md)區段中宣告宣告。 

1. 開啟原則的擴充檔案。 例如， <em> `SocialAndLocalAccounts/` </em>。
1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 找出[ClaimsSchema](claimsschema.md)元素。 如果此元素不存在，請加以新增。
1. 將下列宣告新增至**ClaimsSchema**元素。  

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

## <a name="configure-the-restful-api-technical-profile"></a>設定 RESTful API 技術設定檔 

[Restful 技術設定檔](restful-technical-profile.md)提供與您自己的 Restful 服務互動的支援。 Azure AD B2C 會將資料傳送至`InputClaims`集合中的 RESTful 服務，並在`OutputClaims`集合中接收回資料。 在您**ClaimsProviders** <em>**`TrustFrameworkExtensions.xml`**</em>的檔案中尋找 ClaimsProviders 元素，並加入新的宣告提供者，如下所示：

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

在此範例中， `userLanguage`將會傳送至 REST 服務，就`lang`像 JSON 承載中的一樣。 宣告的值`userLanguage`包含目前的使用者語言識別項。 如需詳細資訊，請參閱宣告[解析程式](claim-resolver-overview.md)。

上述`AuthenticationType`批註，並`AllowInsecureAuthInProduction`指定當您移至生產環境時應該進行的變更。 若要瞭解如何保護您的 RESTful Api 以用於生產環境，請參閱[保護 RESTFUL api](secure-rest-api.md)。

## <a name="add-an-orchestration-step"></a>新增協調流程步驟

[使用者旅程](userjourneys.md)指定明確的路徑，原則允許信賴憑證者應用程式取得使用者所需的宣告。 使用者旅程圖會表示為必須遵循才能獲得成功交易的協調流程序列。 您可以新增或減去協調流程步驟。 在此情況下，您會新增一個新的協調流程步驟，在使用者透過 REST API 呼叫註冊或登入之後，用來增強提供給應用程式的資訊。

1. 開啟原則的基底檔案。 例如， <em> `SocialAndLocalAccounts/` </em>。
1. 搜尋 `<UserJourneys>` 元素。 複製整個元素，然後將它刪除。
1. 開啟原則的擴充檔案。 例如， <em> `SocialAndLocalAccounts/` </em>。
1. 將貼`<UserJourneys>`到擴充檔案中，在`<ClaimsProviders>`元素的結尾之後。
1. 找出`<UserJourney Id="SignUpOrSignIn">`，並在最後一步加入下列協調流程步驟。

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. 藉由將變更`Order`為， `8`重構上一個協調流程步驟。 最後兩個協調流程步驟看起來應該如下所示：

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. 針對**profileedit.xml**和**passwordreset.xml**使用者旅程重複最後兩個步驟。


## <a name="include-a-claim-in-the-token"></a>在權杖中包含宣告 

`balance`若要將宣告傳回給信賴憑證者應用程式，請將輸出宣告新增<em> `SocialAndLocalAccounts/` </em>至檔案。 新增輸出宣告會在使用者成功旅程後，將宣告發行至權杖，並將其傳送至應用程式。 修改 [信賴憑證者] 區段內的 [技術配置`balance`檔] 元素，以新增為輸出宣告。
 
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

針對**profileedit.xml**和**passwordreset.xml**重複執行此步驟。

儲存您變更的檔案： *trustframeworkbase.xml*，以及*TrustFrameworkExtensions*、 *signuporsignin.xml*、 *profileedit.xml*和*passwordreset.xml*.xml。 

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD 租使用者的目錄，以確定您使用的是包含 Azure AD 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [識別體驗架構]****。
1. 選取 **[上傳自訂原則**]，然後上傳您已變更的原則檔案： *trustframeworkbase.xml*和*TrustFrameworkExtensions*、 *signuporsignin.xml*、 *profileedit.xml*和*passwordreset.xml*.xml。 
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

若要瞭解如何保護您的 Api，請參閱下列文章：

- [逐步解說︰將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為協調流程步驟](custom-policy-rest-api-claims-exchange.md)
- [保護您的 RESTful API](secure-rest-api.md)
- [參考： RESTful 技術設定檔](restful-technical-profile.md)
