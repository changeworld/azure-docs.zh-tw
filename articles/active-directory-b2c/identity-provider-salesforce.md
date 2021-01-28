---
title: 使用 Salesforce 帳戶設定註冊和登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，在您的應用程式中為具有 Salesforce 帳戶的客戶提供註冊和登入。
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
ms.openlocfilehash: 0981687b03344daf7a447cc4d9e50f0923341340
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98952286"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Salesforce 帳戶登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>建立 Salesforce 應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中讓具有 Salesforce 帳戶的使用者登入，您需要在 Salesforce [App Manager](https://login.salesforce.com/)中建立應用程式。 如需詳細資訊，請參閱 [設定基本連線應用程式設定](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)，以及 [啟用 API 整合的 OAuth 設定](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [登入 Salesforce](https://login.salesforce.com/)。
1. 從功能表選取 [ **設定**]。
1.  展開 [ **應用程式**]，然後選取 [ **應用程式管理員**]。
1. 選取 [ **新增已連線的應用程式**]。
1. 在 [ **基本資訊**] 下，輸入：
    1. **已連線的應用程式名稱** -已連線的應用程式名稱會顯示在應用程式管理員和其應用程式啟動器磚上。 名稱在您的組織內必須是唯一的。 
    1. **API 名稱** 
    1. **連絡人電子郵件** -Salesforce 的連絡人電子郵件
1. 在 [ **API (啟用 Oauth 設定])** 上，選取 [**啟用 oauth 設定**]。
    1. 在 [ **回呼 URL**] 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 。 以您的租用戶名稱取代 `your-tenant-name`。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，您還是需要在輸入租用戶名稱時，全部使用小寫字母。
    1. 在 **選取的 OAuth 範圍** 中，選取 [ **存取您的基本資訊] (識別碼、設定檔、電子郵件、位址、電話)**，並 **允許 (openid) 存取您的唯一識別碼**。
    1. 選取 [ **Web 服務器流程需要秘密**]。
1. 選取 [**設定識別碼權杖**] 
    1. 設定5分鐘 **有效的權杖** 。
    1. 選取 [ **包含標準宣告**]。
1. 按一下 [儲存]。
1. 複製 [取用 **者金鑰** ] 和 [取用 **者密碼**] 的值。 您將需要這兩個設定，以將 Salesforce 設定為租使用者中的身分識別提供者。 **用戶端密碼** 是重要的安全性認證。

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>將 Salesforce 設定為身分識別提供者

1. 確定您使用的目錄包含 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂閱] 篩選，並選擇包含您 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [識別提供者]，然後選取 [新增 OpenID Connect 提供者]。
1. 輸入 [名稱]。 例如，輸入 *Salesforce*。
1. 在 [ **中繼資料 url**] 中，輸入 [Salesforce OpenID Connect 設定檔](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)的 url。 針對沙箱，login.salesforce.com 會取代為 test.salesforce.com。 針對社區，login.salesforce.com 會取代為 [社區 URL]，例如 username.force.com/.well-known/openid-configuration。 URL 必須是 HTTPS。

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. 在 [用戶端識別碼]中，輸入您先前記錄的應用程式識別碼。
1. 在 [用戶端密碼] 中，輸入您先前記錄的用戶端密碼。
1. 在 [範圍] 中，輸入 `openid id profile email`。
1. 保留 **回應類型** 和 **回應模式** 的預設值。
1. (選擇性) 在 [網域提示] 中，輸入 `contoso.com`。 如需詳細資訊，請參閱[使用 Azure Active Directory B2C 設定直接登入](direct-signin.md#redirect-sign-in-to-a-social-provider)。
1. 在 [識別提供者宣告對應] 底下，選取下列宣告：

    - **使用者識別碼**： *sub*
    - **顯示名稱**：name
    - **指定的名稱**：given_name
    - **姓氏**：family_name
    - **電子** 郵件： *電子郵件*

1. 選取 [儲存]。

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>將 Salesforce 身分識別提供者新增至使用者流程 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 按一下您想要新增 Salesforce 身分識別提供者的使用者流程。
1. 在 **社交識別提供者** 底下，選取 [ **Salesforce**]。
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
7. 輸入原則金鑰的 [名稱]。 例如： `SalesforceSecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 在 [祕密] 中，輸入您先前記錄的用戶端密碼。
9. 針對 [金鑰使用方法]，選取 `Signature`。
10. 按一下頁面底部的 [新增] 。

## <a name="configure-salesforce-as-an-identity-provider"></a>將 Salesforce 設定為身分識別提供者

若要讓使用者能夠使用 Salesforce 帳戶登入，您必須將該帳戶定義為宣告提供者，Azure AD B2C 可透過端點進行通訊。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Salesforce 帳戶新增至原則擴充檔中的 **ClaimsProviders** 元素，將其定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **中繼資料** 會設定為 [Salesforce OpenID Connect 設定檔](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm)的 URL。 針對沙箱，login.salesforce.com 會取代為 test.salesforce.com。 針對社區，login.salesforce.com 會取代為 [社區 URL]，例如 username.force.com/.well-known/openid-configuration。 URL 必須是 HTTPS。
5. 將 **client_id** 設定為來自應用程式註冊的應用程式識別碼。
6. 儲存檔案。

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
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end

## <a name="next-steps"></a>下一步

瞭解如何將 [Salesforce 權杖傳遞至您的應用程式](idp-pass-through-user-flow.md)。
