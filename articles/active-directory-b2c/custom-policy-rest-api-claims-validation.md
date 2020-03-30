---
title: REST API 聲明交換為驗證
titleSuffix: Azure AD B2C
description: 用於創建與 RESTful 服務交互的 Azure AD B2C 使用者旅程的演練。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330827"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>演練：在 Azure AD B2C 使用者旅程中集成 REST API 聲明交換，以驗證使用者輸入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

支援 Azure 活動目錄 B2C（Azure AD B2C）的標識體驗框架 （IEF） 使標識開發人員能夠在使用者旅程中集成與 RESTful API 的交互。  在本演練結束時，您將能夠創建 Azure AD B2C 使用者旅程，該旅程與[RESTful 服務](custom-policy-rest-api-intro.md)進行交互，以驗證使用者輸入。

在這種情況下，我們將添加使用者在 Azure AD B2C 註冊頁中輸入會員號碼的能力。 我們將通過將此資料發送到 REST API 來驗證此電子郵件和會員號碼的組合是否已映射到促銷代碼。 如果 REST API 找到此使用者的促銷代碼，它將返回到 Azure AD B2C。 最後，促銷代碼將插入到應用程式使用權杖聲明中。

您也可以將互動設計為協調流程步驟。 當 REST API 不會在螢幕上驗證資料，並且始終返回聲明時，這一點是合適的。 如需詳細資訊，請參閱[逐步解說︰將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為協調流程步驟](custom-policy-rest-api-claims-exchange.md)。

## <a name="prerequisites"></a>Prerequisites

- 完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該有一個使用本機帳戶來註冊和登入的有效自訂原則。
- 瞭解如何在[Azure AD B2C 自訂策略中集成 REST API 聲明交換](custom-policy-rest-api-intro.md)。

## <a name="prepare-a-rest-api-endpoint"></a>準備 REST API 終結點

在本演練中，您應該有一個 REST API，用於驗證電子郵件地址是否在後端系統中註冊了一個會員 ID。 如果註冊，REST API 應返回註冊促銷代碼，客戶可以使用該代碼在您的應用程式中購買商品。 否則，REST API 應返回 HTTP 409 錯誤訊息："忠誠 ID '[會員 ID] 未與 "[電子郵件]"電子郵件地址關聯。

以下 JSON 代碼說明了 Azure AD B2C 將發送到 REST API 終結點的資料。 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

REST API 驗證資料後，必須返回 HTTP 200 （Ok），並包含以下 JSON 資料：

```json
{
    "promoCode": "24534"
}
```

如果驗證失敗，REST API 必須返回帶有 JSON 元素的`userMessage`HTTP 409（衝突）。 IEF 期望`userMessage`REST API 返回的聲明。 如果驗證失敗，此聲明將作為字串呈現給使用者。

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>配置 RESTful API 技術設定檔 

[寧靜的技術設定檔](restful-technical-profile.md)支援與您自己的 RESTful 服務進行介面。 Azure AD B2C 將資料發送到`InputClaims`集合中的 RESTful 服務，並在`OutputClaims`集合中接收資料。 查找**聲明提供程式**元素並添加新的聲明提供程式，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

在此示例中，`userLanguage`將像在 JSON 負載中一樣`lang`發送到 REST 服務。 `userLanguage`聲明的值包含當前使用者語言識別碼。 有關詳細資訊，請參閱[聲明解析器](claim-resolver-overview.md)。

上面`AuthenticationType`的注釋並`AllowInsecureAuthInProduction`指定在移動到生產環境時應所做的更改。 要瞭解如何保護用於生產的 RESTful API，請參閱安全[RESTful API](secure-rest-api.md)。

## <a name="validate-the-user-input"></a>驗證使用者輸入

要在註冊期間獲取使用者的會員號碼，您必須允許使用者在螢幕上輸入此資料。 通過將**pageaid**輸出聲明添加到現有註冊技術設定檔部分`OutputClaims`的元素，將 pageaid 輸出聲明添加到註冊頁。 指定整個輸出聲明清單，以控制聲明在螢幕上顯示的順序。  

將驗證技術設定檔引用添加到註冊技術設定檔，該設定檔調用`REST-ValidateProfile`。 新的驗證技術設定檔將添加到基本策略中定義的`<ValidationTechnicalProfiles>`集合的頂部。 此行為意味著只有在成功驗證後，Azure AD B2C 才會繼續在目錄中創建帳戶。   

1. 尋找 **ClaimsProviders** 元素。 添加新的聲明提供程式，如下所示：

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>在權杖中包含聲明 

要將促銷代碼聲明返回到依賴方應用程式，請向<em>`SocialAndLocalAccounts/`</em>檔添加輸出聲明。 輸出聲明將允許在使用者成功訪問後將聲明添加到權杖中，並將發送到應用程式。 修改依賴方節中的技術設定檔元素以添加`promoCode`為輸出聲明。
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 通過在頂部功能表中選擇**目錄 + 訂閱**篩選器並選擇包含 Azure AD 租戶的目錄，請確保使用的目錄包含 Azure AD 租戶。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [識別體驗架構]****。
1. 選擇 **"上傳自訂策略**"，然後上載您更改的策略檔：*信任框架擴展.xml*和*SignUpOrSignin.xml*。 
1. 選取您上傳的註冊或登入原則，按一下 [立即執行]**** 按鈕。
1. 您應該可以使用電子郵件地址註冊。
1. 按一下"**立即註冊"** 連結。
1. 在 **"您的會員 ID"** 中，鍵入 1234，然後按一下"**繼續**"。 此時，您應該收到一條驗證錯誤訊息。
1. 更改為其他值，然後按一下"**繼續**"。
1. 傳送回您的應用程式的權杖包含 `promoCode` 宣告。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>後續步驟

要瞭解如何保護 API，請參閱以下文章：

- [逐步解說︰將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為協調流程步驟](custom-policy-rest-api-claims-exchange.md)
- [保護您的 RESTful API](secure-rest-api.md)
- [參考： RESTful 技術設定檔](restful-technical-profile.md)
