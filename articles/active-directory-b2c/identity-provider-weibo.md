---
title: 設定註冊，並以 Weibo 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有微博帳戶的客戶得以註冊和登入您的應用程式。
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
ms.openlocfilehash: 26c4e154deec02b0642e6c131ced50acb02f9899
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98951512"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以微博帳戶登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-weibo-application"></a>建立 Weibo 應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中讓具有 Weibo 帳戶的使用者登入，您必須在 [Weibo 開發人員入口網站](https://open.weibo.com/)中建立應用程式。 如果您還沒有 Weibo 帳戶，您可以在中註冊 [https://weibo.com](https://weibo.com/signup/signup.php?lang=en-us) 。

1. 使用您的微博帳戶認證登入[微博開發人員入口網站](https://open.weibo.com/)。
1. 登入之後，在右上角選取您的顯示名稱。
1. 在下拉式清單中，選取 [编辑开发者信息] \(編輯開發人員資訊)。
1. 輸入必要資訊，並選取 [提交]。
1. 完成電子郵件驗證程序。
1. 移至[識別驗證頁面](https://open.weibo.com/developers/identity/edit)。
1. 輸入必要資訊，並選取 [提交]。

### <a name="register-a-weibo-application"></a>註冊 Weibo 應用程式

1. 移至[新的 Weibo 應用程式註冊頁面](https://open.weibo.com/apps/new)。
1. 輸入必要的應用程式資訊。
1. 選取 [创建]\(建立\)。
1. 複製 **應用程式金鑰** 和 **應用程式密碼** 的值。 您需要這兩個值，才能將識別提供者新增至您的租用戶。
1. 上傳所需的相片，並輸入所需的資訊。
1. 選取 [保存以上信息]\(儲存\)。
1. 選取 [高级信息]\(進階資訊\)。
1. 選取 OAuth2.0 [授权设置] (\重新導向 URL\) 旁的 [编辑] \(編輯\)。
1. 針對 OAuth2.0 [授权设置] \(重新導向 URL) 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 例如，如果您的租用戶名稱是 contoso，請將 URL 設定為 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 選取 [提交]。

::: zone pivot="b2c-user-flow"

## <a name="configure-weibo-as-an-identity-provider"></a>將 Weibo 設定為身分識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [ **識別提供者**]，然後選取 [ **Weibo (預覽])**。
1. 輸入 [名稱]。 例如， *Weibo*。
1. 針對 [ **用戶端識別碼**]，輸入您稍早建立之 Weibo 應用程式的應用程式金鑰。
1. 在 [用戶端密碼] 中，輸入您記下的應用程式祕密。
1. 選取 [儲存]。

## <a name="add-weibo-identity-provider-to-a-user-flow"></a>將 Weibo 身分識別提供者新增至使用者流程 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 按一下您想要新增 Weibo 身分識別提供者的使用者流程。
1. 在 **社交識別提供者** 底下，選取 [ **Weibo**]。
1. 選取 [儲存]。
1. 若要測試您的原則，請選取 [ **執行使用者流程**]。
1. 針對 [ **應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [**執行使用者流程**]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將先前記錄的用戶端密碼儲存在 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]。
5. 選取 [原則金鑰]，然後選取 [新增]。
6. 針對 [選項] 選擇 `Manual`。
7. 輸入原則金鑰的 [名稱]。 例如： `WeiboSecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 在 [祕密] 中，輸入您先前記錄的用戶端密碼。
9. 針對 [金鑰使用方法]，選取 `Signature`。
10. 按一下頁面底部的 [新增] 。

## <a name="configure-weibo-as-an-identity-provider"></a>將 Weibo 設定為身分識別提供者

若要讓使用者能夠使用 Weibo 帳戶登入，您必須將該帳戶定義為宣告提供者，Azure AD B2C 可透過端點進行通訊。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以將 Weibo 帳戶定義為宣告提供者，方法是將它新增至原則擴充檔中的 **claimsprovider** 元素。

1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>weibo.com</Domain>
      <DisplayName>Weibo (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Weibo-OAuth2">
          <DisplayName>Weibo</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">weibo</Item>
            <Item Key="authorization_endpoint">https://api.weibo.com/oauth2/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.weibo.com/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weibo.com/2/account/get_uid.json</Item>
            <Item Key="scope">email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">uid</Item>
            <Item Key="client_id">Your Weibo application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeiboSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="uid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="weibo.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="Weibo User" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="UserId" />
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
    <ClaimsProvider>
    ```

4. 將 **client_id** 設定為來自應用程式註冊的應用程式識別碼。
5. 儲存檔案。

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
    <ClaimsProviderSelection TargetClaimsExchangeId="WeiboExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="WeiboExchange" TechnicalProfileReferenceId="Weibo-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
