---
title: REST API 宣告交換作為驗證
titleSuffix: Azure AD B2C
description: 建立與 RESTful 服務互動之 Azure AD B2C 使用者旅程圖的逐步解說。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6381f678979437fdfc10d2ea63a79ed347183e92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388913"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>逐步解說：將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中，以驗證使用者輸入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

支撐 Azure Active Directory B2C (Azure AD B2C) 的 Identity Experience Framework (IEF) 讓身分識別開發人員能夠在使用者旅程圖中整合與 RESTful API 的互動。  在本逐步解說結束時，您將能夠建立與 [RESTful 服務](custom-policy-rest-api-intro.md) 互動的 Azure AD B2C 使用者旅程圖，以驗證使用者輸入。

在此案例中，我們將新增使用者在 Azure AD B2C 註冊頁面中輸入忠誠度號碼的能力。 我們會將此資料傳送給 REST API，以驗證電子郵件和忠誠度號碼的組合是否會對應到促銷代碼。 如果 REST API 找到此使用者的促銷代碼，則會傳回 Azure AD B2C。 最後，將會在權杖宣告中插入促銷代碼，讓應用程式使用。

您也可以將互動設計為協調流程步驟。 當 REST API 不會驗證螢幕上的資料，而且一律會傳回宣告時，就適合這種情況。 如需詳細資訊，請參閱[逐步解說︰將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為協調流程步驟](custom-policy-rest-api-claims-exchange.md)。

## <a name="prerequisites"></a>Prerequisites

- 完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該有一個使用本機帳戶來註冊和登入的有效自訂原則。
- 了解如何[在您的 Azure AD B2C 自訂原則中整合 REST API 宣告交換](custom-policy-rest-api-intro.md)。

## <a name="prepare-a-rest-api-endpoint"></a>準備 REST API 端點

在這個逐步解說中，您應該有一個 REST API，可驗證是否已在您的後端系統中，以忠誠度識別碼註冊電子郵件地址。 如果已註冊，REST API 應該會傳回註冊促銷代碼，讓客戶可以用來在您的應用程式中購買商品。 否則，REST API 應該會傳回 HTTP 409 錯誤訊息：「忠誠度識別碼 ' {忠誠度 ID} ' 與 ' {email} ' 電子郵件地址沒有關聯」。

下列 JSON 程式碼會說明 Azure AD B2C 將傳送至您 REST API 端點的資料。 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

一旦您的 REST API 驗證資料之後，其必須傳回 HTTP 200 (確定)，並提供下列 JSON 資料：

```json
{
    "promoCode": "24534"
}
```

如果驗證失敗，則 REST API 必須傳回具有 JSON 元素的 HTTP 409 (衝突) `userMessage` 。 IEF 預期 REST API 傳回的宣告 `userMessage` 。 如果驗證失敗，此宣告會以字串的形式呈現給使用者。

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

REST API 端點的設定不在本文討論範圍內。 我們已建立 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) \(部分機器翻譯\) 範例。 您可以在 [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function) \(英文\) 中存取完整的 Azure 函式程式碼。

## <a name="define-claims"></a>定義宣告

宣告會在 Azure AD B2C 原則執行期間，提供資料的暫存位置。 您可以在[宣告結構描述](claimsschema.md)區段中進行宣告。 

1. 開啟原則的擴充檔案。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>。
1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 尋找 [ClaimsSchema](claimsschema.md) (機器翻譯) 元素。 如果此元素不存在，請加以新增。
1. 將下列宣告新增至 **ClaimsSchema** 元素。  

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

## <a name="configure-the-restful-api-technical-profile"></a>設定 RESTful API 技術設定檔 

[Restful 技術設定檔](restful-technical-profile.md)可支援與您自己的 Restful 服務互動。 Azure AD B2C 會在 `InputClaims` 集合中將資料傳送至 RESTful 服務，並在 `OutputClaims` 集合中接收回資料。 尋找 **claimsprovider** 元素，並加入新的宣告提供者，如下所示：

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

在此範例中，會在 JSON 承載中，以 `lang` 形式將 `userLanguage` 傳送至 REST 服務。 `userLanguage` 宣告的值包含目前的使用者語言識別碼。 如需詳細資訊，請參閱[宣告解析程式](claim-resolver-overview.md)。

`AuthenticationType` 和 `AllowInsecureAuthInProduction` 上方的註解指定當您移至實際執行環境時應進行的變更。 若要了解如何保護您的 RESTful API 以用於實際執行環境，請參閱[保護 RESTful API](secure-rest-api.md)。

## <a name="validate-the-user-input"></a>驗證使用者輸入

若要在註冊期間取得使用者的忠誠度號碼，您必須允許使用者在畫面上輸入此資料。 將 **loyaltyId** 輸出宣告新增至現有的註冊技術設定檔區段的元素，以將其新增至註冊頁面 `OutputClaims` 。 指定輸出宣告的完整清單，以控制宣告在螢幕上的顯示順序。  

將驗證技術設定檔參考新增至註冊技術設定檔，其會呼叫 `REST-ValidateProfile` 。 新的驗證技術設定檔會新增至 `<ValidationTechnicalProfiles>` 基底原則中定義的集合頂端。 此行為表示，只有在成功驗證之後，Azure AD B2C 移至在目錄中建立帳戶。   

1. 尋找 **ClaimsProviders** 元素。 新增宣告提供者，如下所示：

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

## <a name="include-a-claim-in-the-token"></a>在權杖中包含宣告 

若要將促銷代碼索取傳回給信賴憑證者應用程式，請將輸出宣告新增至檔案 <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> 。 輸出宣告可在成功的使用者旅程圖之後，將宣告新增至權杖，並且將會傳送至應用程式。 修改 [信賴憑證者] 區段內的技術設定檔元素，以新增 `promoCode` 作為輸出宣告。
 
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

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
1. 選取 [識別體驗架構]。
1. 選取 **[上傳自訂原則**]，然後上傳您變更的原則檔案： *TrustFrameworkExtensions.xml*，然後 *SignUpOrSignin.xml*。 
1. 選取您上傳的註冊或登入原則，按一下 [立即執行] 按鈕。
1. 您應該可以使用電子郵件地址註冊。
1. 按一下 [ **立即註冊** ] 連結。
1. 在 **您的忠誠度識別碼**中輸入1234，然後按一下 [ **繼續**]。 此時，您應該會收到驗證錯誤訊息。
1. 變更為另一個值，然後按一下 [ **繼續**]。
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

若要了解如何保護您的 API，請參閱下列文章：

- [逐步解說：將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為協調流程步驟](custom-policy-rest-api-claims-exchange.md)
- [保護您的 RESTful API](secure-rest-api.md)
- [參考：RESTful 技術設定檔](restful-technical-profile.md)
