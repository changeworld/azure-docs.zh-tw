---
title: 使用 SAML 通訊協定設定與 Salesforce SAML 提供者的登入
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中使用 SAML 通訊協定來設定使用 Salesforce SAML 提供者登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9dce61817bdd6b42223028a624cd6e237be28bfe
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953813"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用 SAML 通訊協定來設定以 Salesforce SAML 提供者登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何在 Azure Active Directory B2C (Azure AD B2C) 中藉由使用[自訂原則](custom-policy-overview.md)，讓使用者能夠從 Salesforce 組織帳戶登入。 將 [SAML 識別提供者技術設定檔](saml-identity-provider-technical-profile.md)新增至自訂原則，以啟用登入。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- 如果您還沒有這麼做，請註冊[免費的開發人員版本帳戶](https://developer.salesforce.com/signup)。 這篇文章會運用 [Salesforce Lightning 經驗](https://developer.salesforce.com/page/Lightning_Experience_FAQ)。
- 為您的 Salesforce 組織[設定網域](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)。

## <a name="set-up-salesforce-as-an-identity-provider"></a>將 Salesforce 設定為識別提供者

1. [登入 Salesforce](https://login.salesforce.com/)。
2. 在左側功能表的 [設定]下，展開 [身分識別]，然後選取 [識別提供者]。
3. 選取 [啟用識別提供者]。
4. 在 [選取憑證] 下，選取您想要讓 Salesforce 在與 Azure AD B2C 通訊時使用的憑證。 您可以使用預設憑證。
5. 按一下 [檔案] 。

### <a name="create-a-connected-app-in-salesforce"></a>在 Salesforce 中建立連線應用程式

1. 在 [身分識別提供者] 頁面上，選取 **服務提供者現已透過連線應用程式建立。** 按一下這裡。
2. 在 [基本資訊] 下，為連線應用程式輸入必要值。
3. 在 [Web 應用程式設定]下，核取 [啟用 SAML] 方塊。
4. 在 [實體識別碼] 欄位中，輸入下列 URL。 請確保使用 Azure AD B2C 租戶的名稱替換 `your-tenant` 的值。

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. 在 [ACS URL] 欄位中，輸入下列 URL。 請確保使用 Azure AD B2C 租戶的名稱替換 `your-tenant` 的值。

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. 捲動到清單底部，然後按一下 [儲存]。

### <a name="get-the-metadata-url"></a>取得中繼資料 URL

1. 在連線應用程式的概觀分頁上，按一下 [管理]。
2. 複製 **中繼資料探索端點** 的值，並將其儲存。 您將在本文稍後使用它。

### <a name="set-up-salesforce-users-to-federate"></a>設定 Salesforce 使用者以建立同盟

1. 在連線應用程式的 [管理] 分頁上，按一下 [管理設定檔]。
2. 選取您想要與 Azure AD B2C 建立同盟的設定檔 (或使用者群組)。 身為系統管理員，選取 [系統管理員] 核取方塊，以便使用您的 Salesforce 帳戶建立同盟。

## <a name="create-a-self-signed-certificate"></a>建立自我簽署憑證

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將所建立的憑證儲存在 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]。
5. 選取 [原則金鑰]，然後選取 [新增]。
6. 針對 [選項] 選擇 `Upload`。
7. 輸入原則的 [名稱]。 例如，SAMLSigningCert。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 瀏覽並選取您所建立的 B2CSigningCert.pfx 憑證。
9. 輸入憑證的 [密碼]。
3. 按一下頁面底部的 [新增] 。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 Salesforce 帳戶進行登入，您必須將該帳戶定義為 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Salesforce 帳戶新增至原則擴充檔中的 **ClaimsProviders** 元素，將其定義成宣告提供者。 如需詳細資訊，請參閱[定義 SAML 識別提供者技術設定檔](saml-identity-provider-technical-profile.md)。

1. 開啟 *TrustFrameworkExtensions.xml*。
1. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
1. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 使用您稍早複製的 Salesforce 中繼資料 URL 更新 **PartnerEntity** 的值。
1. 將 **StorageReferenceId** 兩個執行個體的值，更新至簽章憑證金鑰的名稱。 例如，B2C_1A_SAMLSigningCert。
1. 找出 [`<ClaimsProviders>`] 區段，並新增下列 XML 程式碼片段。 如果您的原則已包含 `SM-Saml-idp` 技術設定檔，請跳至下一個步驟。 如需詳細資訊，請參閱[單一登入工作階段管理](custom-policy-reference-sso.md)。

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. 儲存檔案。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end