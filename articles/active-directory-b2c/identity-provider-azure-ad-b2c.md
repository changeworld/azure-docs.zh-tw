---
title: 使用來自另一個 Azure AD B2C 租使用者的 Azure AD B2C 帳戶設定註冊和登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 在您的應用程式中，為具有 Azure AD B2C 帳戶的客戶提供註冊和登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ea4def3cfaa19e27dc05e955bf97b41976ec2190
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953915"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>使用來自另一個 Azure AD B2C 租使用者的 Azure AD B2C 帳戶設定註冊和登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>概觀

本文說明如何設定與另一個 Azure AD B2C 租使用者的同盟。 當您的應用程式受到您的 Azure AD B2C 保護時，這可讓其他 Azure AD B2c 的使用者以現有的帳戶登入。 在下圖中，使用者可以使用由 *Fabrikam* 的 Azure AD B2C 租使用者管理的帳戶，登入受 *Contoso* Azure AD B2C 保護的應用程式 

![Azure AD B2C 與另一個 Azure AD B2C 租使用者的同盟](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

若要讓具有來自另一個 Azure AD B2C (租使用者之帳戶的使用者登入（例如，Fabrikam) ），請在 Azure AD B2C (例如，Contoso) ：

1. 建立 [使用者流程](tutorial-create-user-flows.md)或 [自訂原則](custom-policy-get-started.md)。
1. 然後，在 Azure AD B2C 中建立應用程式，如本節中的描述。 

建立應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 請確定您使用的目錄包含其他 Azure AD B2C 租使用者 (例如 Fabrikam.com) 。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如， *ContosoApp*。
1. 在 [支援的帳戶類型] 中，選取 [任何識別提供者或組織目錄中的帳戶 (適用於以使用者流程驗證使用者)]。
1. 在 [重新 **導向 URI**] 下，選取 [ **Web**]，然後以小寫字母輸入下列 URL，其中以 `your-B2C-tenant-name` 您 Azure AD B2C (租使用者的名稱取代，例如 Contoso) 。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如： `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 。

1. 在 [許可權] 底下，選取 [授與系統 **管理員同意 openid 和 offline_access 許可權** ] 核取方塊。
1. 選取 [註冊]。
1. 在 [ **Azure AD B2C 應用程式註冊** ] 頁面中，選取您所建立的應用程式，例如 *ContosoApp*。
1. 記錄應用程式 [概觀] 頁面上所顯示的 **應用程式 (用戶端) 識別碼**。 當您在下一節中設定識別提供者時，將需要這項資訊。
1. 在左側功能表的 [管理] 下，選取 [驗證和密碼]。
1. 選取 [新增用戶端密碼]。
1. 在 [描述] 方塊中，輸入用戶端密碼的描述。 例如，*clientsecret1*。
1. 在 [到期] 下，選取密碼有效的持續時間，然後選取 [新增]。
1. 記錄密碼的 **值**。 當您在下一節中設定識別提供者時，將需要這項資訊。


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>將 Azure AD B2C 設定為身分識別提供者

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 請確定您使用的目錄包含您要設定同盟的 Azure AD B2C 租使用者 (例如 Contoso) 。 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄 (例如 Contoso) 。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [識別提供者]，然後選取 [新增 OpenID Connect 提供者]。
1. 輸入 [名稱]。 例如，輸入 *Fabrikam*。
1. 在 [ **中繼資料 url**] 中，輸入下列 url， `{tenant}` 並以您 Azure AD B2C 租使用者的功能變數名稱取代 (例如 Fabrikam) 。 將取代為 `{policy}` 您在另一個租使用者中設定的原則名稱：

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    例如： `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration` 。

1. 在 [用戶端識別碼]中，輸入您先前記錄的應用程式識別碼。
1. 在 [用戶端密碼] 中，輸入您先前記錄的用戶端密碼。
1. 在 [範圍] 中，輸入 `openid`。
1. 保留 **回應類型** 和 **回應模式** 的預設值。
1.  (**網域提示** 的選擇性) ，請輸入您想要用於 [直接登入](direct-signin.md#redirect-sign-in-to-a-social-provider)的功能變數名稱。 例如， *fabrikam.com*。
1. 在 [識別提供者宣告對應] 底下，選取下列宣告：

    - **使用者識別碼**： *sub*
    - **顯示名稱**：name
    - **指定的名稱**：given_name
    - **姓氏**：family_name
    - **電子** 郵件： *電子郵件*

1. 選取 [儲存]。

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>將 Azure AD B2C 身分識別提供者新增至使用者流程 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 按一下您要新增 Azure AD B2C 識別提供者的使用者流程。
1. 在 **社交識別提供者** 底下，選取 [ **Fabrikam**]。
1. 選取 [儲存]。
1. 若要測試您的原則，請選取 [ **執行使用者流程**]。
1. 針對 [ **應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [**執行使用者流程**]
1. 在 [註冊或登入] 頁面中，選取 [ *Fabrikam* ] 以其他 Azure AD B2C 租使用者登入。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將稍早建立的應用程式金鑰儲存在 Azure AD B2C 租使用者中。

1. 請確定您使用的目錄包含您要設定同盟的 Azure AD B2C 租使用者 (例如 Contoso) 。 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄 (例如 Contoso) 。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 之下，選取 [Identity Experience Framework]。
1. 選取 [ **原則金鑰** ]，然後選取 [ **新增**]。
1. 針對 [選項] 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]。 例如： `FabrikamAppSecret` 。  前置詞 `B2C_1A_` 會在建立時自動新增至您的金鑰名稱，因此在下一節的 XML 中，其參考是 *B2C_1A_FabrikamAppSecret*。
1. 在 [ **密碼**] 中，輸入您稍早記錄的用戶端密碼。
1. 針對 [金鑰使用方法]，選取 `Signature`。
1. 選取 [建立]。

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>將 Azure AD B2C 設定為身分識別提供者

若要讓使用者能夠使用來自另一個 Azure AD B2C 租使用者 (Fabrikam) 的帳戶進行登入，您必須將另一個 Azure AD B2C 定義為 Azure AD B2C 可透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Azure AD B2C 新增至原則擴充檔中的 **ClaimsProvider** 專案，將 Azure AD B2C 定義為宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml* 檔案。
1. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
1. 新增新的 **ClaimsProvider**，如下所示：
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 使用相關的值來更新下列 XML 元素：

    |XML 元素  |值  |
    |---------|---------|
    |ClaimsProvider\Domain  | [直接登入](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider)所使用的功能變數名稱。 輸入您想要在直接登入中使用的功能變數名稱。 例如， *fabrikam.com*。 |
    |TechnicalProfile\DisplayName|這個值會顯示在登入畫面的登入按鈕上。 例如， *Fabrikam*。 |
    |中繼資料 \ client_id|識別提供者的應用程式識別碼。 使用您稍早在其他 Azure AD B2C 租使用者中建立的應用程式識別碼來更新用戶端識別碼。|
    |Metadata\METADATA|指向 OpenID Connect 身分識別提供者設定檔的 URL，也稱為 OpenID 知名的設定端點。 輸入下列 URL， `{tenant}` 並以其他 Azure AD B2C 租使用者 (Fabrikam) 的功能變數名稱取代。 將取代為 `{tenant}` 您在另一個租使用者中設定的原則名稱，並 `{policy]` 使用原則名稱： `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` 。 例如： `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration` 。|
    |CryptographicKeys| 將 **StorageReferenceId** 的值更新為您稍早建立之原則金鑰的名稱。 例如 `B2C_1A_FabrikamAppSecret`。| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end

## <a name="next-steps"></a>後續步驟

瞭解如何將 [其他 Azure AD B2C token 傳遞至您的應用程式](idp-pass-through-user-flow.md)。
