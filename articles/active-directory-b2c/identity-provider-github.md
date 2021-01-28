---
title: 設定註冊，並以 GitHub 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 GitHub 帳戶的客戶得以註冊和登入您的應用程式。
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
ms.openlocfilehash: 22548703b456eb28a30c2d210d21f810d7b3ae6e
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98952693"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 GitHub 帳戶登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-github-oauth-application"></a>建立 GitHub OAuth 應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中啟用使用 GitHub 帳戶登入，您必須在 [GitHub 開發人員](https://github.com/settings/developers) 入口網站中建立應用程式。 如需詳細資訊，請參閱 [建立 OAuth 應用程式](https://docs.github.com/en/free-pro-team@latest/developers/apps/creating-an-oauth-app)。 如果您還沒有 GitHub 帳戶，可以在中註冊 [https://www.github.com/](https://www.github.com/) 。

1. 使用您的 GitHub 認證登入 [Github 開發人員](https://github.com/settings/developers) 。
1. 選取 [OAuth 應用程式]，然後選取 [新增 OAuth 應用程式]。
1. 輸入 [Application name] \(應用程式名稱\) 和您的 [Homepage URL] \(首頁 URL\)。
1. 在 [授權回呼 URL] 中輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，仍請在輸入租用戶名稱時，全部使用小寫字母。
1. 按一下 **[Register application]** (註冊應用程式)。
1. 複製 **用戶端識別碼** 和 **用戶端密碼** 的值。 您必須同時將識別提供者新增至您的租用戶。

::: zone pivot="b2c-user-flow"

## <a name="configure-github-as-an-identity-provider"></a>將 GitHub 設定為身分識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [ **識別提供者**]，然後選取 [ **GitHub (預覽])**。
1. 輸入 [名稱]。 例如， *GitHub*。
1. 針對 [ **用戶端識別碼**]，輸入您稍早建立之 GitHub 應用程式的用戶端識別碼。
1. 針對 **用戶端密碼**，請輸入您所記錄的用戶端密碼。
1. 選取 [儲存]。

## <a name="add-github-identity-provider-to-a-user-flow"></a>將 GitHub 身分識別提供者新增至使用者流程 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 按一下您想要新增 GitHub 身分識別提供者的使用者流程。
1. 在 **社交識別提供者** 底下，選取 [ **GitHub**]。
1. 選取 [儲存]。
1. 若要測試您的原則，請選取 [ **執行使用者流程**]。
1. 針對 [ **應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [**執行使用者流程**]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將先前記錄的用戶端密碼儲存在 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]。
1. 選取 [原則金鑰]，然後選取 [新增]。
1. 針對 [選項] 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]。 例如： `GitHubSecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 [祕密] 中，輸入您先前記錄的用戶端密碼。
1. 針對 [金鑰使用方法]，選取 `Signature`。
1. 按一下頁面底部的 [新增] 。

## <a name="configure-github-as-an-identity-provider"></a>將 GitHub 設定為身分識別提供者

若要讓使用者能夠使用 GitHub 帳戶登入，您需要將帳戶定義為宣告提供者，Azure AD B2C 可透過端點與之通訊。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以將 GitHub 帳戶定義為宣告提供者，方法是將它新增至原則擴充檔中的 **claimsprovider** 元素。

1. 開啟 *TrustFrameworkExtensions.xml*。
1. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
1. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>github.com</Domain>
      <DisplayName>GitHub</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="GitHub-OAuth2">
          <DisplayName>GitHub</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">github.com</Item>
            <Item Key="authorization_endpoint">https://github.com/login/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://github.com/login/oauth/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.github.com/user</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="scope">read:user user:email</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="UserAgentForClaimsExchange">CPIM-Basic/{tenant}/{policy}</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your GitHub application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GitHubSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="github.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 將 **client_id** 設定為來自應用程式註冊的應用程式識別碼。
1. 儲存檔案。

### <a name="add-the-claims-transformations"></a>新增宣告轉換

GitHub 技術設定檔需要將 **CreateIssuerUserId** 宣告轉換新增至 ClaimsTransformations 清單。 如果您的檔案中未定義 **ClaimsTransformations** 專案，請新增父 XML 元素，如下所示。 宣告轉換也需要一個名為 **numericUserId** 的新宣告型別。

1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 尋找 [ClaimsSchema](claimsschema.md) (機器翻譯) 元素。 如果此元素不存在，請加以新增。
1. 將 numericUserId 宣告加入至 **ClaimsSchema** 元素。
1. 找出 [ClaimsTransformations](claimstransformations.md) 元素。 如果此元素不存在，請加以新增。
1. 將 CreateIssuerUserId 宣告轉換加入至 **ClaimsTransformations** 元素。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="GitHubExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="GitHubExchange" TechnicalProfileReferenceId="GitHub-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
