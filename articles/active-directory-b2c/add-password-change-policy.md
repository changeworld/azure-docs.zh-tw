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
ms.date: 12/17/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a42cb97d123d0943dab02bf1f70fcf306d6bcd96
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629116"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定密碼變更

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory B2C (Azure AD B2C) ，您可以讓以本機帳戶登入的使用者變更其密碼，而不需要透過電子郵件驗證來證明其真實性。 密碼變更流程包含下列步驟：

1. 使用本機帳戶登入。 如果會話仍在使用中，Azure AD B2C 會授權使用者，並跳至下一個步驟。
1. 使用者必須確認 **舊密碼**、建立並確認 **新密碼**。

![密碼變更流程](./media/add-password-change-policy/password-change-flow.png)

## <a name="prerequisites"></a>必要條件

* 完成[在 Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。
* 如果您尚未這麼做，請 [在 Azure Active Directory B2C 中註冊 web 應用程式](tutorial-register-applications.md)。

## <a name="add-the-elements"></a>新增元素

1. 開啟 TrustframeworkExtensions.xml 檔案，並將下列識別碼為 `oldPassword` 的 **ClaimType** 元素新增到 [ClaimsSchema](claimsschema.md) 元素：

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
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
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
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

3. [UserJourney](userjourneys.md) 元素會定義使用者與應用程式進行互動時所採用的路徑。 新增 **UserJourney** 識別為 `PasswordChange` 的 **UserJourneys** 元素 (如果此元素不存在)：

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
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
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. 儲存 *TrustFrameworkExtensions.xml* 原則檔案。
5. 複製與入門套件一起下載的 *ProfileEdit.xml* 檔案，並將其命名為 *ProfileEditPasswordChange.xml*。
6. 開啟新檔案，然後將 **PolicyId** 屬性更新成唯一值。 此值是您原則的名稱。 例如 *B2C_1A_profile_edit_password_change*。
7. 將 `<DefaultUserJourney>` 中的 **ReferenceId** 屬性修改成符合您所建立新使用者旅程圖的識別碼。 例如 *PasswordChange*。
8. 儲存您的變更。

## <a name="upload-and-test-the-policy"></a>上傳並測試原則

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [識別體驗架構]。
5. 在 [自訂原則] 頁面上，按一下 [上傳原則]。
6. 選取 [覆寫現有的原則]，然後搜尋並選取 TrustframeworkExtensions.xml 檔案。
7. 按一下 [上傳] 。
8. 針對信賴憑證者檔案 (例如 *ProfileEditPasswordChange.xml*) 重複步驟 5 到 7。

### <a name="run-the-policy"></a>執行原則

1. 開啟您所變更的原則。 例如 *B2C_1A_profile_edit_password_change*。
2. 針對 **應用程式**，請選取您先前註冊的應用程式。 若要查看權杖，[回覆 URL] 應該顯示 `https://jwt.ms`。
3. 按一下 [立即執行] 。 使用您先前建立的帳戶登入。 您現在應該有機會變更密碼。

## <a name="next-steps"></a>下一步

- 在 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)上尋找範例原則。
- 瞭解如何 [在 Azure AD B2C 中設定密碼複雜度](password-complexity.md)。
- 設定 [密碼重設流程](add-password-reset-policy.md)。

::: zone-end
