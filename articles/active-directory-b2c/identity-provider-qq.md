---
title: 使用 Azure Active Directory B2C 設定註冊，並以 QQ 帳戶登入
description: 使用 Azure Active Directory B2C，讓具有 QQ 帳戶的客戶得以註冊和登入您的應用程式。
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
ms.openlocfilehash: 8bc2cddf4d0380e5dc22e8250b6ee26f4d005b8a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98952422"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 QQ 帳戶登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-qq-application"></a>建立 QQ 應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中讓具有 QQ 帳戶的使用者登入，您需要在 [QQ 開發人員入口網站](http://open.qq.com)中建立應用程式。 如果您還沒有 QQ 帳戶，可以在中註冊 [https://ssl.zc.qq.com](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) 。

### <a name="register-for-the-qq-developer-program"></a>註冊 QQ 開發人員計劃

1. 使用您的 QQ 帳戶認證登入 [QQ 開發人員入口網站](http://open.qq.com)。
1. 登入之後，請移至， [https://open.qq.com/reg](https://open.qq.com/reg) 以開發人員身分註冊您自己。
1. 選取 [个人]\(個人開發人員\)。
1. 輸入必要資訊，並選取 [下一步]\(下一步\)。
1. 完成電子郵件驗證程序。 在註冊為開發人員之後，您必須等待數天以進行核准。

### <a name="register-a-qq-application"></a>註冊 QQ 應用程式

1. 移至 [https://connect.qq.com/index.html](https://connect.qq.com/index.html)。
1. 選取 [应用管理]\(應用程式管理\)。
1. 選取 [创建应用]\(建立應用程式\)，然後輸入所需的資訊。
1. 在 [授权回调域]\(回呼 URL\) 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`。 例如，如果您的 `tenant_name` 是 contoso，請將 URL 設為 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 選取 [创建应用]\(建立應用程式\)。
1. 在 [確認] 頁面上，選取 [应用管理]\(應用程式管理\) 以返回應用程式管理頁面。
1. 在您建立的應用程式旁邊，選取 [查看]\(檢視\)。
1. 選取 [修改]\(編輯\)。
1. 複製 **應用程式識別碼** 和 **應用程式金鑰**。 您需要這兩個值，才能將識別提供者新增至您的租用戶。

::: zone pivot="b2c-user-flow"

## <a name="configure-qq-as-an-identity-provider"></a>將 QQ 設為識別提供者

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 選取 [ **識別提供者**]，然後選取 [ **QQ (預覽])**。
1. 輸入 [名稱]。 例如， *QQ*。
1. 針對 [ **用戶端識別碼**]，輸入您稍早建立的 QQ 應用程式的應用程式識別碼。
1. 針對 **用戶端密碼**，請輸入您記錄的應用程式金鑰。
1. 選取 [儲存]。

## <a name="add-qq-identity-provider-to-a-user-flow"></a>將 QQ 身分識別提供者新增至使用者流程 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 按一下您想要新增 QQ 身分識別提供者的使用者流程。
1. 在 **社交識別提供者** 底下，選取 [ **QQ**]。
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
7. 輸入原則金鑰的 [名稱]。 例如： `QQSecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 在 [祕密] 中，輸入您先前記錄的用戶端密碼。
9. 針對 [金鑰使用方法]，選取 `Signature`。
10. 按一下頁面底部的 [新增] 。

## <a name="configure-qq-as-an-identity-provider"></a>將 QQ 設為識別提供者

若要讓使用者能夠使用 QQ 帳戶登入，您需要將帳戶定義為宣告提供者，Azure AD B2C 可透過端點進行通訊。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以將 QQ 帳戶定義為宣告提供者，方法是將它新增至原則擴充檔中的 **claimsprovider** 元素。

1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAuth2">
          <DisplayName>QQ</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">qq</Item>
            <Item Key="authorization_endpoint">https://graph.qq.com/oauth2.0/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://graph.qq.com/oauth2.0/token</Item>
            <Item Key="ClaimsEndpoint">https://graph.qq.com/oauth2.0/me</Item>
            <Item Key="scope">get_user_info</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="ClaimsResponseFormat">JsonP</Item>
            <Item Key="ResponseErrorCodeParamName">error</Item>
            <Item Key="external_user_identity_claim_id">openid</Item>
            <Item Key="client_id">Your QQ application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_QQSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="openid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="qq.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. 將 **client_id** 設定為來自應用程式註冊的應用程式識別碼。
5. 儲存檔案。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
