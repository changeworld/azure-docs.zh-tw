---
title: 使用自訂原則來設定以 LinkedIn 帳戶進行登入
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中使用自訂原則來設定以 LinkedIn 帳戶登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188166"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 LinkedIn 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何使用 Azure Active Directory B2C （Azure AD B2C）中的[自訂原則](custom-policy-overview.md)，讓 LinkedIn 帳戶的使用者登入。

## <a name="prerequisites"></a>先決條件

- 完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。
- LinkedIn 帳戶-如果您還沒有，請[建立一個帳戶](https://www.linkedin.com/start/join)。
- LinkedIn 頁面-您必須要有[Linkedin 頁面](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page)，才能與您在下一節中建立的 linkedin 應用程式產生關聯。

## <a name="create-an-application"></a>建立應用程式

若要使用 LinkedIn 作為 Azure AD B2C 中的識別提供者，您必須建立 LinkedIn 應用程式。

### <a name="create-app"></a>建立應用程式

1. 使用您的 LinkedIn 帳戶認證來登入 [LinkedIn 應用程式管理](https://www.linkedin.com/secure/developer?newapp=)網站。
1. 選取 [建立應用程式]****。
1. 輸入**應用程式名稱**。
1. 輸入與 LinkedIn 頁面名稱對應的 [**公司**名稱]。 如果您還沒有 LinkedIn 頁面，請建立一個。
1. 選擇性輸入 [**隱私權原則 URL**]。 它必須是有效的 URL，但不需要是可連線的端點。
1. 輸入**商務電子郵件**。
1. 上傳**應用程式標誌**影像。 標誌影像必須是正方形，且其尺寸必須至少為100x100 圖元。
1. 保留 [**產品**] 區段中的預設設定。
1. 請參閱**法律條款**中提供的資訊。 如果您同意這些條款，請核取此方塊。
1. 選取 [建立應用程式]****。

### <a name="configure-auth"></a>設定驗證

1. 選取 [**驗證**] 索引標籤。
1. 記錄**用戶端識別碼**。
1. 顯示並記錄**用戶端密碼**。
1. 在 [ **OAuth 2.0 設定**] 底下，新增下列重新**導向 URL**。 以您的租用戶名稱取代 `your-tenant`。 針對租使用者名稱使用**全部小寫字母**，即使在 Azure AD B2C 中以大寫字母定義也一樣。

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將先前記錄的用戶端密碼儲存在 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 請確定您使用的是包含您 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您租使用者的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
5. 選取 [**原則金鑰**]，然後選取 [**新增**]。
6. 針對 [選項]**** 選擇 `Manual`。
7. 輸入原則金鑰的 [名稱]****。 例如： `LinkedInSecret` 。 前置詞*B2C_1A_* 會自動新增至金鑰的名稱。
8. 在 [**秘密**] 中，輸入您先前記錄的用戶端密碼。
9. 針對 [金鑰使用方法]****，選取 `Signature`。
10. 按一下 [建立]  。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 LinkedIn 帳戶進行登入，您必須將該帳戶定義成 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

將 LinkedIn 帳戶定義為宣告提供者，方法是將它新增至原則擴充檔中的**ClaimsProviders**元素。

1. 在您的編輯器中開啟*SocialAndLocalAccounts/* * TrustFrameworkExtensions*檔案。 此檔案位於您在其中一個必要條件中下載的[自訂原則入門套件][starter-pack]。
1. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
1. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 將**client_id**的值取代為您先前記錄之 LinkedIn 應用程式的用戶端識別碼。
1. 儲存檔案。

### <a name="add-the-claims-transformations"></a>新增宣告轉換

LinkedIn 技術設定檔需要將**ExtractGivenNameFromLinkedInResponse**和**ExtractSurNameFromLinkedInResponse**宣告轉換新增至 ClaimsTransformations 清單。 如果您的檔案中未定義**ClaimsTransformations**元素，請新增父 XML 元素，如下所示。 宣告轉換也需要一個定義為**nullStringClaim**的新宣告類型。

在*TrustFrameworkExtensions*的頂端附近新增**BuildingBlocks**元素。 如需範例，請參閱*trustframeworkbase.xml* 。

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

您現在已設定原則，Azure AD B2C 知道如何與您的 LinkedIn 帳戶進行通訊。 請嘗試上傳原則的擴充檔案，以確認目前沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則]**** 頁面上，選取 [上傳原則]****。
2. 啟用 [覆寫現有的原則]****，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
3. 按一下 [上傳]  。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，識別提供者已設定完成，但在任何註冊或登入畫面中都無法使用。 若要讓它可供使用，您必須建立現有範本使用者旅程圖的複本，然後修改它，讓它也包含 LinkedIn 識別提供者。

1. 開啟入門套件中的*trustframeworkbase.xml* 。
2. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInLinkedIn` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊或登入畫面上的識別提供者按鈕。 如果您為 LinkedIn 帳戶新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 在您建立的使用者旅程圖中，尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelections** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `LinkedInExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是用來讓 Azure AD B2C 與 LinkedIn 帳戶進行通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 ID 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    將 **TechnicalProfileReferenceId** 的值更新成您稍早所建立技術設定檔的識別碼。 例如： `LinkedIn-OAUTH` 。

3. 儲存 TrustFrameworkExtensions.xml** 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

與 Azure AD B2C 的通訊會透過您在 B2C 租使用者中註冊的應用程式進行。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml** 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignInLinkedIn.xml*。
2. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInLinkedIn` 。
3. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. 更新 **DefaultUserJourney** 中 **ReferenceId** 屬性的值，以符合您所建立新使用者旅程圖 (SignUpSignLinkedIn) 的識別碼。
5. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
6. 確定 [選取應用程式]**** 欄位中已選取您所建立的 Azure AD B2C 應用程式，然後按一下 [立即執行]**** 來進行測試。

## <a name="migration-from-v10-to-v20"></a>從 v1.0 遷移至 v2。0

LinkedIn 最近[將其 api 從 v1.0 更新至](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)v2.0。 若要將現有的設定遷移至新的設定，請使用下列各節中的資訊來更新技術設定檔中的元素。

### <a name="replace-items-in-the-metadata"></a>取代中繼資料中的專案

在**TechnicalProfile**的現有**中繼資料**元素中，更新下列**專案**元素：

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

變更為：

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>將專案新增至中繼資料

在**TechnicalProfile**的**中繼資料**中，新增下列**專案**元素：

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>更新 OutputClaims

在**TechnicalProfile**的現有**OutputClaims**中，更新下列**OutputClaim**元素：

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

變更為：

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>加入新的 OutputClaimsTransformation 元素

在**TechnicalProfile**的**OutputClaimsTransformations**中，新增下列**OutputClaimsTransformation**元素：

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>定義新的宣告轉換和宣告類型

在最後一個步驟中，您已加入需要定義的新宣告轉換。 若要定義宣告轉換，請將它們新增至**ClaimsTransformations**清單。 如果您的檔案中未定義**ClaimsTransformations**元素，請新增父 XML 元素，如下所示。 宣告轉換也需要一個定義為**nullStringClaim**的新宣告類型。

**BuildingBlocks**元素應該新增到靠近檔案頂端的地方。 如需範例，請參閱*trustframeworkbase.xml* 。

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>取得電子郵件地址

從 v1.0 到 v2.0 的 LinkedIn 遷移過程中，需要額外呼叫另一個 API 才能取得電子郵件地址。 如果您需要在註冊期間取得電子郵件地址，請執行下列動作：

1. 完成上述步驟，以允許 Azure AD B2C 與 LinkedIn 建立同盟，讓使用者能夠登入。 做為同盟的一部分，Azure AD B2C 會接收 LinkedIn 的存取權杖。
2. 將 LinkedIn 存取權杖儲存至宣告。 [請參閱這裡的指示](idp-pass-through-custom.md)。
3. 新增下列向 LinkedIn `/emailAddress` API 提出要求的宣告提供者。 若要授權此要求，您需要 LinkedIn 存取權杖。

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 將下列協調流程步驟新增至您的使用者旅程圖，以便在使用者使用 LinkedIn 登入時觸發 API 宣告提供者。 請務必適當地更新`Order`編號。 在觸發 LinkedIn 技術設定檔的協調流程步驟之後，立即新增此步驟。

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

註冊時從 LinkedIn 取得電子郵件地址是選擇性的。 如果您選擇不從 LinkedIn 取得電子郵件，但在註冊期間需要一個，則使用者必須手動輸入電子郵件地址並加以驗證。

如需使用 LinkedIn 身分識別提供者之原則的完整範例，請參閱[自訂原則入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)。

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
