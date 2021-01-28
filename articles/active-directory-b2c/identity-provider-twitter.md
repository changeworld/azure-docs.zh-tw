---
title: 設定註冊，並以 Twitter 帳戶登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 Twitter 帳戶的客戶得以註冊和登入您的應用程式。
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
ms.openlocfilehash: a998491729a1d3bd472ecc3de9722c142f8dc182
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953779"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Twitter 帳戶登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>建立應用程式

若要在 Azure AD B2C 中讓具有 Twitter 帳戶的使用者能夠登入，您必須建立 Twitter 應用程式。 如果您還沒有 Twitter 帳戶，您可以在中註冊 [https://twitter.com/signup](https://twitter.com/signup) 。 您也必須 [申請開發人員帳戶](https://developer.twitter.com/en/apply/user.html)。 如需詳細資訊，請參閱 [申請存取](https://developer.twitter.com/en/apply-for-access)。

1. 使用您的 Twitter 帳號憑證登入 [Twitter 開發人員入口網站](https://developer.twitter.com/portal/projects-and-apps) 。
1. 在 [ **獨立應用程式**] 下，選取 [ **+ 建立應用程式**]。
1. 輸入 **應用程式名稱**，然後選取 [ **完成**]。
1. 複製 **應用程式金鑰** 的值和 **API 金鑰秘密**。  您可以使用這兩個專案，將 Twitter 設定為租使用者中的身分識別提供者。 
1. 在 [ **設定您的應用程式**] 下，選取 [ **應用程式設定**]。
1. 在 [**驗證設定**] 下，選取 [**編輯**]。
    1. 選取 [ **啟用 3-腳 OAuth** ] 核取方塊。
    1. 選取 [ **使用者要求電子郵件地址** ] 核取方塊。
    1. 在 [ **回呼 url**] 中，輸入 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` 。 將 `your-tenant` 取代為您的租用戶名稱，並將 `your-user-flow-Id` 取代為您的使用者流程識別碼。 例如： `b2c_1A_signup_signin_twitter` 。 輸入您的租使用者名稱和使用者流程識別碼時，請使用所有小寫字母，即使它們是使用 Azure AD B2C 中的大寫字母來定義也一樣。
    1. 在 [ **網站 URL**] 中，輸入 `https://your-tenant.b2clogin.com` 。 以您的租用戶名稱取代 `your-tenant`。 例如： `https://contosob2c.b2clogin.com` 。
    1. 輸入 **服務條款** 的 URL，例如 `http://www.contoso.com/tos` 。 原則 URL 是您維護的頁面，用來提供應用程式的條款及條件。
    1. 例如，輸入 **隱私權原則** 的 URL `http://www.contoso.com/privacy` 。 原則 URL 是您需維護以提供應用程式隱私權資訊的網頁。
    1. 選取 [儲存]。

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>將 Twitter 設定為身分識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [ **識別提供者**]，然後選取 [ **Twitter**]。
1. 輸入 [名稱]。 例如， *Twitter*。
1. 針對 [ **用戶端識別碼**]，輸入您稍早建立之 Twitter 應用程式的 *API 金鑰* 。
1. 針對 **用戶端密碼**，請輸入您記錄的 *API 金鑰密碼* 。
1. 選取 [儲存]。

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>將 Twitter 身分識別提供者新增至使用者流程 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 選取您要新增 Twitter 身分識別提供者的使用者流程。
1. 在 **社交識別提供者** 底下，選取 [ **Twitter**]。
1. 選取 [儲存]。
1. 若要測試您的原則，請選取 [ **執行使用者流程**]。
1. 針對 [ **應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [**執行使用者流程**]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將先前記錄的祕密金鑰儲存在 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]。
5. 選取 [原則金鑰]，然後選取 [新增]。
6. 針對 [選項] 選擇 `Manual`。
7. 輸入原則金鑰的 [名稱]。 例如： `TwitterSecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 在 [祕密] 中，輸入您先前記錄的用戶端密碼。
9. 針對 [金鑰使用方法]，選取 `Encryption`。
10. 按一下頁面底部的 [新增] 。

## <a name="configure-twitter-as-an-identity-provider"></a>將 Twitter 設定為身分識別提供者

若要讓使用者能夠使用 Twitter 帳戶登入，您需要將帳戶定義為宣告提供者，Azure AD B2C 可透過端點進行通訊。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Twitter 帳戶新增至原則擴充檔中的 **ClaimsProviders** 元素，將其定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. 以您先前記錄的 *API 金鑰密碼* 取代 **client_id** 的值。
5. 儲存檔案。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]


::: zone-end
