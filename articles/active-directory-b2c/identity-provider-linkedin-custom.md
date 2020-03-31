---
title: 使用自訂策略使用LinkedIn帳戶設置登錄
titleSuffix: Azure AD B2C
description: 使用自訂策略在 Azure 活動目錄 B2C 中使用LinkedIn帳戶設置登錄。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188166"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 LinkedIn 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介紹如何使用 Azure 活動目錄 B2C （Azure AD B2C） 中的[自訂策略](custom-policy-overview.md)，從LinkedIn帳戶為使用者啟用登錄。

## <a name="prerequisites"></a>Prerequisites

- 完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。
- LinkedIn帳戶 - 如果您還沒有帳戶，[請創建一個帳戶](https://www.linkedin.com/start/join)。
- LinkedIn頁面 - 您需要[一個LinkedIn頁面](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page)才能與您在下一節中創建LinkedIn應用程式相關聯。

## <a name="create-an-application"></a>建立應用程式

若要使用 LinkedIn 作為 Azure AD B2C 中的識別提供者，您必須建立 LinkedIn 應用程式。

### <a name="create-app"></a>建立應用程式

1. 使用您的 LinkedIn 帳戶認證來登入 [LinkedIn 應用程式管理](https://www.linkedin.com/secure/developer?newapp=)網站。
1. 選取 [建立應用程式]****。
1. 輸入**應用名稱**。
1. 輸入與LinkedIn頁名稱對應**的公司**名稱。 如果尚未創建主頁，請創建LinkedIn主頁。
1. （可選）輸入**隱私政策 URL**。 它必須是有效的 URL，但不需要是可訪問的終結點。
1. 輸入**商務電子郵件**。
1. 上傳**應用徽標**圖像。 徽標圖像必須為正方形，其尺寸必須至少為 100x100 圖元。
1. 在"**產品"** 部分保留預設設置。
1. 審查以**法律術語**形式提供的資訊。 如果您同意這些條款，請選中此框。
1. 選取 [建立應用程式]****。

### <a name="configure-auth"></a>設定驗證

1. 選擇 **"Auth"** 選項卡。
1. 記錄**用戶端 ID**。
1. 揭示和記錄**客戶秘密**。
1. 在**OAuth 2.0 設置**下，添加以下**重定向 URL**。 以您的租用戶名稱取代 `your-tenant`。 使用租戶名稱**的所有小寫字母**，即使該名稱在 Azure AD B2C 中使用大寫字母定義也是如此。

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將先前記錄的用戶端密碼儲存在 Azure AD B2C 租用戶中。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇包含租戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
5. 選擇**策略鍵**，然後選擇 **"添加**"。
6. 針對 [選項]**** 選擇 `Manual`。
7. 輸入原則金鑰的 [名稱]****。 例如： `LinkedInSecret` 。 首碼*B2C_1A_* 將自動添加到金鑰的名稱中。
8. 在 **"機密**"中，輸入您以前記錄的用戶端金鑰。
9. 針對 [金鑰使用方法]****，選取 `Signature`。
10. 按一下 **[建立]**。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 LinkedIn 帳戶進行登入，您必須將該帳戶定義成 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

通過將LinkedIn帳戶添加到策略擴展檔中**的聲明提供程式**元素，將其定義為聲明提供程式。

1. 在編輯器中打開*社交和本地帳戶/[信任框架擴展.xml]* 檔。 此檔位於作為先決條件之一的一部分下載的[自訂策略初學者包][starter-pack]中。
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

1. 將**client_id**的值替換為您以前記錄的應用程式的LinkedIn的用戶端 ID。
1. 儲存檔案。

### <a name="add-the-claims-transformations"></a>添加聲明轉換

技術設定檔LinkedIn需要**將"從連結回應中提取"** 和 **"從連結回應中提取"** 聲明轉換添加到索賠轉換清單中。 如果檔中未定義**聲明轉換**元素，請添加父 XML 元素，如下所示。 聲明轉換還需要定義名為**nullStringClaim**的新聲明類型。

在*TrustFramework 擴展.xml*檔頂部附近添加**構建塊**元素。 有關示例，請參閱*TrustFrameworkBase.xml。*

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

現在配置了策略，以便 Azure AD B2C 知道如何與LinkedIn帳戶進行通信。 請嘗試上傳策略的擴展檔，以確認到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則]**** 頁面上，選取 [上傳原則]****。
2. 啟用 [覆寫現有的原則]****，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
3. 按一下 [上傳]****。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，標識提供程式已設置，但它在任何註冊或登錄螢幕中不可用。 若要讓它可供使用，您必須建立現有範本使用者旅程圖的複本，然後修改它，讓它也包含 LinkedIn 識別提供者。

1. 在初學者包中打開*TrustFrameworkBase.xml*檔。
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

通過您在 B2C 租戶中註冊的應用程式與 Azure AD B2C 的通信。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml** 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignInLinkedIn.xml*。
2. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInLinkedIn` 。
3. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. 更新 **DefaultUserJourney** 中 **ReferenceId** 屬性的值，以符合您所建立新使用者旅程圖 (SignUpSignLinkedIn) 的識別碼。
5. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
6. 確定 [選取應用程式]**** 欄位中已選取您所建立的 Azure AD B2C 應用程式，然後按一下 [立即執行]**** 來進行測試。

## <a name="migration-from-v10-to-v20"></a>從 v1.0 遷移到 v2.0

LinkedIn最近[將其 API 從 v1.0 更新為 v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)。 要將現有配置遷移到新配置，請使用以下部分中的資訊更新技術設定檔中的元素。

### <a name="replace-items-in-the-metadata"></a>替換中繼資料中的項

在**技術設定檔**的現有**中繼資料**元素中，從以下**項**元素更新：

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

變更為：

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>將專案添加到中繼資料

在**技術設定檔**的**中繼資料**中，添加以下**項**元素：

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>更新輸出要求

在**技術設定檔**的現有**輸出聲明**中，從以下內容更新以下**輸出聲明**元素：

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

變更為：

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>添加新的輸出聲明轉換元素

在**技術設定檔**的 **"輸出要求轉換"中**，添加以下**輸出要求轉換**元素：

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>定義新的聲明轉換和聲明類型

在最後一步中，您添加了需要定義的新聲明轉換。 要定義聲明轉換，請將它們添加到**聲明轉換**清單中。 如果檔中未定義**聲明轉換**元素，請添加父 XML 元素，如下所示。 聲明轉換還需要定義名為**nullStringClaim**的新聲明類型。

構建**塊**元素應在檔頂部附近添加。 例如，請參閱*信任框架基礎.xml。*

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

### <a name="obtain-an-email-address"></a>獲取電子郵件地址

作為從 v1.0 遷移到 v2.0 LinkedIn遷移的一部分，需要對另一個 API 進行額外調用才能獲取電子郵件地址。 如果您在註冊期間需要獲取電子郵件地址，請執行以下操作：

1. 完成上述步驟，允許 Azure AD B2C 與LinkedIn聯合，以便使用者登錄。 作為聯合的一部分，Azure AD B2C 接收LinkedIn的訪問權杖。
2. 將LinkedIn訪問權杖保存到聲明中。 [在此處查看說明](idp-pass-through-custom.md)。
3. 添加以下聲明提供程式，將請求添加到LinkedIn的`/emailAddress`API。 為了授權此請求，您需要LinkedIn訪問權杖。

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

4. 將以下業務流程步驟添加到使用者旅程中，以便在使用者使用LinkedIn登錄時觸發 API 聲明提供程式。 請確保適當地更新`Order`號碼。 在觸發LinkedIn技術設定檔的業務流程步驟之後立即添加此步驟。

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

在註冊期間從LinkedIn獲取電子郵件地址是可選的。 如果您選擇不從LinkedIn獲取電子郵件，但在註冊期間需要一封電子郵件，則使用者需要手動輸入電子郵件地址並驗證它。

有關使用LinkedIn標識提供程式的策略的完整示例，請參閱[自訂策略初學者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)。

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
