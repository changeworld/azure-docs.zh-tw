---
title: 自訂原則中的條件式存取技術設定檔
titleSuffix: Azure AD B2C
description: Azure AD B2C 中條件式存取技術設定檔的自訂原則參考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ef7599441cbfa11c555453adea0ca135569524b5
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91459824"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自訂原則中定義條件式存取技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) 條件式存取是 Azure AD B2C 用來將信號結合在一起、做出決策並強制執行組織原則的工具。 使用原則條件進行自動化的風險評估，表示可以立即識別並補救或封鎖有風險的登入。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Azure AD B2C 所使用之通訊協定處理常式元件的完整名稱：

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

下列範例顯示條件式存取技術設定檔：

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>條件式存取評估

針對每個登入，Azure AD B2C 會評估所有原則，並在授與使用者存取權之前，確保符合所有需求。 「封鎖存取」會覆寫所有其他的設定。 條件式存取技術設定檔的 **評估** 模式會評估使用本機帳戶登入期間 Azure AD B2C 收集的信號。 條件式存取技術設定檔的結果是條件式存取評估所產生的一組宣告。 Azure AD B2C 原則會在下一個協調流程步驟中使用這些宣告來採取行動，例如封鎖使用者或使用多重要素驗證來挑戰使用者。 您可以針對此模式設定下列選項。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| OperationType | 是 | 必須是 **評估**。  |

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至條件式存取的宣告清單。 您也可以將您的宣告名稱對應至條件式存取技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 資料類型 | 描述 |
| --------- | -------- | ----------- |----------- |
| UserId | 是 | 字串 | 登入之使用者的識別碼。 |
| AuthenticationMethodsUsed | 是 |stringCollection | 使用者用來登入的方法清單。 可能的值： `Password` 、和 `OneTimePasscode` 。 |
| IsFederated | 是 |boolean | 指出使用者是否以同盟帳戶登入。 值必須是 `false`。 |
| IsMfaRegistered | 是 |boolean | 指出使用者是否已註冊用於多重要素驗證的電話號碼。 |


**InputClaimsTransformations**元素可以包含**InputClaimsTransformation**專案的集合，這些專案可用來修改輸入宣告或產生新的宣告，然後再將其傳送至條件式存取服務。

### <a name="output-claims"></a>輸出宣告

**OutputClaims**元素包含 ConditionalAccessProtocolProvider 所產生的宣告清單。 您也可以將您的宣告名稱對應至以下定義的名稱。

| ClaimReferenceId | 必要 | 資料類型 | 描述 |
| --------- | -------- | ----------- |----------- |
| 挑戰 | 是 |stringCollection | 補救已識別威脅的動作清單。 可能的值： `block` |
| MultiConditionalAccessStatus | 是 | stringCollection |  |

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="example-evaluation"></a>範例：評估

下列範例顯示用來評估登入威脅的條件式存取技術設定檔。

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>修復

條件式存取技術設定檔的 **補救** 模式會通知 Azure AD B2C 已補救登入識別的威脅。 您可以針對補救模式設定下列選項。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| OperationType | 是 | 必須是 **補救**。  |

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至條件式存取的宣告清單。 您也可以將您的宣告名稱對應至條件式存取技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 資料類型 | 描述 |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | 是 | stringCollection| 修正已識別的威脅，以從評估模式退回的挑戰、挑戰索賠的清單。|


**InputClaimsTransformations**元素可以包含**InputClaimsTransformation**專案的集合，這些元素可用來修改輸入宣告或產生新的宣告，然後再呼叫條件式存取服務。

### <a name="output-claims"></a>輸出宣告

條件式存取通訊協定提供者不會傳回任何 **OutputClaims**，因此不需要指定輸出宣告。 不過，您可以包含條件式存取通訊協定提供者未傳回的宣告，只要您設定屬性即可 `DefaultValue` 。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。


### <a name="example-remediation"></a>範例：補救

下列範例顯示用來修復已識別威脅的條件式存取技術設定檔：

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

若要讓範例能夠運作，請將下列範例中所示的宣告新增至延伸模組檔案中的 ClaimsSchema 區段：

```XML
<!-- Conditional Access claims  -->
   <ClaimType Id="conditionalAccessClaimCollection">
      <DisplayName>Conditional Access claims</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The list of claims which are the result of CA check</AdminHelpText>
   </ClaimType>
   <ClaimType Id="ConditionalAccessStatus">
      <DisplayName>The status of CA evaluation</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The status of CA evaluation</AdminHelpText>
    </ClaimType>
    <ClaimType Id="AuthenticationMethodsUsed">
      <DisplayName>Authentication methods used</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The list of authentication methods used</AdminHelpText>
    </ClaimType>
    <ClaimType Id="AuthenticationMethodUsed">
      <DisplayName>Authentication method used</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>The authentication method used</AdminHelpText>
    </ClaimType>
    <ClaimType Id="IsFederated">
      <DisplayName>IsFederated</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Is user authenticated via an external identity provider</AdminHelpText>
    </ClaimType>
    <ClaimType Id="IsMfaRegistered">
      <DisplayName>IsMfaRegistered</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Is user registered for MFA</AdminHelpText>
    </ClaimType> 
    <ClaimType Id="CAChallengeIsMfa">
      <DisplayName>CAChallengeIsMfa</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="CAChallengeIsChgPwd">
      <DisplayName>CAChallengeIsChgPwd</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="CAChallengeIsBlock">
      <DisplayName>CAChallengeIsBlock</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="responseMsg">
      <DisplayName>responseMsg</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
      <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
      <UserInputType>Paragraph</UserInputType>
    </ClaimType>
<!-- End of Conditional Access claims -->
```

新增下列宣告轉換：

```xml
<!--Conditional Access Transformations-->
  <ClaimsTransformation Id="AddToAuthenticationMethodsUsed" TransformationMethod="AddItemToStringCollection">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="item" />
      <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="CreatePasswordAuthenticationMethodClaim" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="Password" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="CreateOneTimePasscodeAuthenticationMethodClaim" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="OneTimePasscode" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsMfaRegisteredCT" TransformationMethod="DoesClaimExist">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="IsMfaRegistered" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="SetCAChallengeIsMfa" TransformationMethod="StringCollectionContains">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="item" DataType="string" Value="mfa" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" TransformationClaimType="outputClaim" />
    </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="SetCAChallengeIsChgPwd" TransformationMethod="StringCollectionContains">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="item" DataType="string" Value="chg_pwd" />
        <InputParameter Id="ignoreCase" DataType="string" Value="true" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsChgPwd" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="SetCAChallengeIsBlock" TransformationMethod="StringCollectionContains">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="item" DataType="string" Value="block" />
        <InputParameter Id="ignoreCase" DataType="string" Value="true" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
```

新增下列技術設定檔：

```xml
<TechnicalProfile Id="GenerateCAClaimFlags">
  <DisplayName>GenerateCAClaimFlags</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" />
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsChgPwd" />
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsMfa" />
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsChgPwd" />
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsBlock" />
  </OutputClaimsTransformations>
</TechnicalProfile>

<!--This is a self-asserted technical profile that we will use to show a block screen-->
<TechnicalProfile Id="ShowBlockPage">
  <DisplayName>Show Block message</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
    <Item Key="TokenLifeTimeInSeconds">3600</Item>
    <Item Key="AllowGenerationOfClaimsWithNullValues">true</Item>
    <Item Key="setting.showContinueButton">false</Item>
    <Item Key="setting.showCancelButton">false</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseMsg" DefaultValue="The user is blocked due to conditional access check." />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
  <EnabledForUserJourneys>Always</EnabledForUserJourneys>
</TechnicalProfile>

```

在 TrustFrameworkPolicy 元素中，新增這些 SubJourneys，如下列範例所示：

```xml
  <SubJourneys>
    <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>conditionalAccessClaimCollection</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
          </ClaimsExchanges>
        </OrchestrationStep>
      </OrchestrationSteps>
    </SubJourney>

    <SubJourney Id="ConditionalAccess_Remediation" Type="Call">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>conditionalAccessClaimCollection</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="ConditionalAccessRemediation" TechnicalProfileReferenceId="ConditionalAccessRemediation" />
          </ClaimsExchanges>
        </OrchestrationStep>
      </OrchestrationSteps>
    </SubJourney>
  </SubJourneys>

```

新增使用新宣告的使用者旅程圖，如下列範例所示：

```xml
  <UserJourneys>
    <UserJourney Id="SignUpOrSignInWithCA">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />

          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Check if the user has selected to sign in using one of the social providers -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!-- For social IDP authentication, attempt to find the user account in the directory. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>authenticationSource</Value>
              <Value>socialIdpAuthentication</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <OrchestrationStep Order="4" Type="InvokeSubJourney">
          <JourneyList>
            <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
          </JourneyList>
        </OrchestrationStep>

        <!--MFA based on Conditional Access-->
        <OrchestrationStep Order="5" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>CAChallengeIsMfa</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>CAChallengeIsMfa</Value>
              <Value>false</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!--Save MFA phone number: The precondition verifies whether the user provided a new number in the previous step. If so, the phone number is stored in the directory for future authentication requests.-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newPhoneNumberEntered</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserWriteWithObjectId" TechnicalProfileReferenceId="AAD-UserWritePhoneNumberUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <OrchestrationStep Order="7" Type="ClaimsExchange" >
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>CAChallengeIsBlock</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
              <Value>CAChallengeIsBlock</Value>
              <Value>true</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="ShowBlockPage" TechnicalProfileReferenceId="ShowBlockPage" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!--If a user has reached this point, this means a remediation was applied-->
        <!--  You can add a precondition here to call remediation only if a Conditional Access challenge was issued-->
        <OrchestrationStep Order="8" Type="InvokeSubJourney">
          <JourneyList>
            <Candidate SubJourneyReferenceId="ConditionalAccess_Remediation" />
          </JourneyList>
        </OrchestrationStep>
        <OrchestrationStep Order="9" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>

```

以下是參考此 UserJourney 的信賴憑證者檔案範例：

```xml
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
                      xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
                      xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
                      PolicySchemaVersion="0.3.0.0" TenantId="contoso.onmicrosoft.com" 
                      PolicyId="ha-sam-signup_signin-CA" 
                      PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_CA"
                      DeploymentMode="Development"
                      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignInWithCA" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>604800</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="<add your app insights instrumentation key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="signInName" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" />
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" />
        <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
