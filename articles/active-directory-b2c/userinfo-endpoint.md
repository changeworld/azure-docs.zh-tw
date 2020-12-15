---
title: 使用者資訊端點 |Microsoft Docs
description: 在 Azure Active Directory B2C 的自訂原則中定義使用者資訊端點。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 95f41283b78c39ed2cf3b4abb75275902d8cf7bd
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509575"
---
# <a name="userinfo-endpoint"></a>使用者資訊端點

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

使用者資訊端點是 [OpenID Connect standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) 規格的一部分，其設計目的是要傳回已驗證使用者的相關宣告。 使用者資訊端點是在使用 [端點](relyingparty.md#endpoints) 元素的信賴憑證者原則中定義。  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>使用者資訊端點總覽

使用者資訊 UserJourney 指定：

- **授權**：以持有人權杖保護的使用者資訊端點。 已發行的存取權杖會顯示在使用者資訊端點的授權標頭中。 原則會指定驗證傳入權杖的技術設定檔，並解壓縮宣告，例如使用者的 objectId。 使用者的 objectId 會用來取得在使用者資訊端點旅程圖的回應中傳回的宣告。 
- **協調流程步驟**： 
  - 協調流程步驟是用來收集使用者的相關資訊。 根據傳入存取權杖內的宣告，使用者旅程圖會叫用 [Azure Active Directory 技術設定檔](active-directory-technical-profile.md) ，以抓取使用者的相關資料，例如，由 objectId 讀取使用者。 
  - **選用協調流程步驟** -您可以新增更多協調流程步驟，例如 REST API 技術設定檔，以取得更多有關使用者的資訊。 
  - **使用者資訊簽發者** -指定使用者資訊端點傳回的宣告清單。

## <a name="create-a-userinfo-endpoint"></a>建立使用者資訊端點

### <a name="1-add-the-token-issuer-technical-profile"></a>1. 新增權杖簽發者技術設定檔

1. 開啟 *TrustFrameworkExtensions.xml* 檔案。
1. 如果尚未存在，請將 ClaimsProvider 元素和其子專案加入為 BuildingBlocks 專案下的第一個元素。
1. 新增下列宣告提供者：

    ```xml
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="UserInfoIssuer">
          <DisplayName>JSON Issuer</DisplayName>
          <Protocol Name="None" />
          <OutputTokenFormat>JSON</OutputTokenFormat>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
          </CryptographicKeys>
          <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId"/>
            <InputClaim ClaimTypeReferenceId="givenName"/>
            <InputClaim ClaimTypeReferenceId="surname"/>
            <InputClaim ClaimTypeReferenceId="displayName"/>
            <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
          </InputClaims>
          <OutputClaims />
        </TechnicalProfile>
        <TechnicalProfile Id="UserInfoAuthorization">
          <DisplayName>UserInfo authorization</DisplayName>
          <Protocol Name="None" />
          <InputTokenFormat>JWT</InputTokenFormat>
          <Metadata>
            <!-- Update the Issuer and Audience below -->
            <!-- Audience is optional, Issuer is required-->
            <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
            <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
            <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
            <!-- Optional claims to read from the access token  -->
            <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ``` 

1. UserInfoIssuer 技術設定檔中的 outputClaims 區段會指定您想要傳回的屬性。 UserInfoIssuer 技術設定檔會在使用者旅程圖結束時呼叫。 
1. UserInfoAuthorization 技術設定檔會驗證簽章、簽發者名稱和權杖物件，並從輸入權杖中解壓縮宣告。 變更下列中繼資料以反映您的環境：
    1. **簽發者** -此值必須與存取權杖宣告內的宣告相同 `iss` 。 Azure AD B2C 所簽發的權杖會使用格式的簽發者 `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` 。 深入瞭解 [權杖自訂](configure-tokens.md)。
    1. **IdTokenAudience** -必須與存取權杖宣告內的宣告相同 `aud` 。 在 Azure AD B2C 宣告 `aud` 是信賴憑證者應用程式的識別碼。 此值為集合，並使用逗號分隔符號支援多個值。

在下列存取權杖中，宣告 `iss` 值為 `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` 。 宣告 `aud` 值為 `22222222-2222-2222-2222-222222222222` 。

```json
{
  "exp": 1605549468,
  "nbf": 1605545868,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
  "sub": "44444444-4444-4444-4444-444444444444",
  "aud": "22222222-2222-2222-2222-222222222222",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1605545868,
  "auth_time": 1605545868,
  "name": "John Smith",
  "given_name": "John",
  "family_name": "Smith",
  "tid": "11111111-1111-1111-1111-111111111111"
}
```

### <a name="2-add-the-userjourney-element"></a>2. 新增 UserJourney 元素 

[UserJourney](userjourneys.md) 元素會定義使用者與應用程式進行互動時所採用的路徑。 新增 **UserJourney** 識別為 `UserInfoJourney` 的 **UserJourneys** 元素 (如果此元素不存在)：

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps >
    <OrchestrationStep Order="1" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>objectId</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges UserIdentity="false">
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
  </OrchestrationSteps>
</UserJourney>
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. 將端點加入至信賴憑證者原則

若要在信賴憑證者應用程式中包含使用者資訊端點，請將 [端點](relyingparty.md#endpoints) 元素新增至 *SocialAndLocalAccounts/SignUpOrSignIn.xml* 檔案。 

```xml
<Endpoints>
  <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
</Endpoints>
```

完成的信賴憑證者元素將如下所示：

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. 上傳檔案

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [識別體驗架構]。
1. 在 [自訂原則] 頁面上，選取 [上傳自訂原則]。
1. **如果自訂原則已存在**，請選取 [覆寫]，然後搜尋並選取 *TrustframeworkExtensions.xml* 的檔案。
1. 按一下 [上傳] 。
1. 對信賴憑證者檔案重複步驟 5 到 7，例如 *SignUpOrSignIn.xml*。

## <a name="calling-the-userinfo-endpoint"></a>呼叫使用者資訊端點

使用者名稱端點會使用標準 OAuth2 持有人權杖 API，並使用取得應用程式權杖時所收到的存取權杖來呼叫。 它會傳回 JSON 回應，其中包含使用者的相關宣告。 使用者資訊端點裝載于 Azure AD B2C：

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

[/.Well-known 設定端點 (OpenID Connect 探索檔]) 會列出該 `userinfo_endpoint` 欄位。 您可以使用/.well-known 設定端點，以程式設計方式探索使用者資訊端點： 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>測試原則

1. 在 [ **自訂原則**] 底下，選取您已整合使用者資訊端點的原則。 例如， *B2C_1A_SignUpOrSignIn*。
1. 選取 [立即執行]。 
1. 在 [ **選取應用程式**] 底下，選取您先前註冊的應用程式。 在 [ **選取回復 url**] 中，選擇 `https://jwt.ms` 。 如需詳細資訊，請參閱 [在 Azure Active Directory B2C 中註冊 web 應用程式](tutorial-register-applications.md)。
1. 使用電子郵件地址或社交帳戶註冊或登入。
1. 從網站複製其編碼格式的 id_token [https://jwt.ms](https://jwt.ms) 。 您可以使用此資訊將 GET 要求提交至使用者資訊端點，並取得使用者資訊。
1. 將 GET 要求提交至使用者資訊端點，並取得使用者資訊。

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your ID token>
```

成功的回應看起來會像這樣：

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>後續步驟

- 您可以在 [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint)上找到使用者資訊端點原則的範例。

::: zone-end