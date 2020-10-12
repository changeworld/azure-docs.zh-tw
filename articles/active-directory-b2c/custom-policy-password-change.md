---
title: 使用自訂原則來設定密碼變更
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中使用自訂原則以讓使用者變更其密碼。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6faf9fb3d15709d3897db9a77bf14ebf238e8fbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87116365"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定密碼變更

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory B2C (Azure AD B2C) ，您可以讓以本機帳戶登入的使用者變更其密碼，而不需要透過電子郵件驗證來證明其真實性。 如果在使用者進入密碼變更流程時工作階段到期，系統會提示他們重新登入。 本文說明如何在[自訂原則](custom-policy-overview.md)中設定密碼變更。 此外，您也可以為使用者流程設定[自助式密碼重設](user-flow-self-service-password-reset.md)。

## <a name="prerequisites"></a>Prerequisites

完成[在 Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。

## <a name="add-the-elements"></a>新增元素

1. 開啟 TrustframeworkExtensions.xml** 檔案，並將下列識別碼為 `oldPassword` 的 **ClaimType** 元素新增到 [ClaimsSchema](claimsschema.md) 元素：

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. [ClaimsProvider](claimsproviders.md) 元素包含會驗證使用者的技術設定檔。 請將下列宣告提供者新增至 **ClaimsProvider** 元素：

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="grant_type">password</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    以您在先決條件教學課程中所建立 IdentityExperienceFramework 應用程式的應用程式識別碼取代 `IdentityExperienceFrameworkAppId`。 以您同樣先前所建立 ProxyIdentityExperienceFramework 應用程式的應用程式識別碼取代 `ProxyIdentityExperienceFrameworkAppId`。

3. [UserJourney](userjourneys.md) 元素會定義使用者與應用程式進行互動時所採用的路徑。 新增 **UserJourney** 識別為 `PasswordChange` 的 **UserJourneys** 元素 (如果此元素不存在)：

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. 儲存 *TrustFrameworkExtensions.xml* 原則檔案。
5. 複製與入門套件一起下載的 *ProfileEdit.xml* 檔案，並將其命名為 *ProfileEditPasswordChange.xml*。
6. 開啟新檔案，然後將 **PolicyId** 屬性更新成唯一值。 此值是您原則的名稱。 例如 *B2C_1A_profile_edit_password_change*。
7. 將 `<DefaultUserJourney>` 中的 **ReferenceId**屬性修改成符合您所建立新使用者旅程圖的識別碼。 例如 *PasswordChange*。
8. 儲存您的變更。

您可以在[這裡](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)找到範例原則。

## <a name="test-your-policy"></a>測試您的原則

在 Azure AD B2C 中測試應用程式時，將 Azure AD B2C 權杖傳回到 `https://jwt.ms` 以檢閱其中的宣告很有用。

### <a name="upload-the-files"></a>上傳檔案

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [識別體驗架構]。
5. 在 [自訂原則] 頁面上，按一下 [上傳原則]****。
6. 選取 [覆寫現有的原則]****，然後搜尋並選取 TrustframeworkExtensions.xml** 檔案。
7. 按一下 [上傳] 。
8. 針對信賴憑證者檔案 (例如 *ProfileEditPasswordChange.xml*) 重複步驟 5 到 7。

### <a name="run-the-policy"></a>執行原則

1. 開啟您所變更的原則。 例如 *B2C_1A_profile_edit_password_change*。
2. 針對**應用程式**，請選取您先前註冊的應用程式。 若要查看權杖，[回覆 URL]**** 應該顯示 `https://jwt.ms`。
3. 按一下 [立即執行] ****。 使用您先前建立的帳戶進行登入。 您現在應該有機會變更密碼。

## <a name="next-steps"></a>接下來的步驟

- 了解如何[在 Azure Active Directory B2C 中使用自訂原則來設定密碼複雜度](custom-policy-password-complexity.md)。
