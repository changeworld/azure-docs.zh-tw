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
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0d8b90e18865afeb5cb0c171d21c89d7c6e932f0
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654280"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Salesforce 帳戶登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>建立 Salesforce 應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中使用 Salesforce 帳戶，您需要在您的 Salesforce **App Manager** 中建立應用程式。 如需詳細資訊，請參閱 [設定基本連線應用程式設定](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)，以及 [啟用 API 整合的 OAuth 設定](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. 從 **安裝程式** 的 [**快速尋找**] 方塊中，輸入 **應用程式**，然後選取 [**應用程式管理員**]。
1. 選取 [ **新增已連線的應用程式**]。
1. 在 [ **基本資訊**] 下，輸入：
    1. **已連線的應用程式名稱** -已連線的應用程式名稱會顯示在應用程式管理員和其應用程式啟動器磚上。 名稱在您的組織內必須是唯一的。 
    1. **API 名稱** 
    1. **連絡人電子郵件** -Salesforce 的連絡人電子郵件
1. 在 [ **API (啟用 Oauth 設定])** 上，選取 [**啟用 oauth 設定**]。
1. 在 [ **回呼 URL**] 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` 。 以您的租用戶名稱取代 `your-tenant-name`。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，您還是需要在輸入租用戶名稱時，全部使用小寫字母。
1. 在 **選取的 OAuth 範圍** 中，選取 [ **存取您的基本資訊] (識別碼、設定檔、電子郵件、位址、電話)**，並 **允許 (openid) 存取您的唯一識別碼**。
1. 選取 [ **Web 服務器流程需要秘密**]。
1. 選取 [ **設定識別碼權杖**]，然後選取 [ **包含標準宣告**]。
1. 按一下 [檔案]  。
1. 複製 [取用 **者金鑰** ] 和 [取用 **者密碼**] 的值。 您將需要這兩個設定，以將 Salesforce 設定為租使用者中的身分識別提供者。 **用戶端密碼** 是重要的安全性認證。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-salesforce-account-as-an-identity-provider"></a>將 Salesforce 帳戶設定為身分識別提供者

1. 確定您使用的目錄包含 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂閱] 篩選，並選擇包含您 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [識別提供者]，然後選取 [新增 OpenID Connect 提供者]。
1. 輸入 [名稱]。 例如，輸入 *Salesforce*。
1. 在 [ **中繼資料 url**] 中，輸入下列 `{org}` 以您的 Salesforce 組織取代的 url：

    ```
    https://{org}.my.salesforce.com/.well-known/openid-configuration
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
    - **電子郵件**： *preferred_username*

1. 選取 [儲存]  。
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
10. 按一下 [建立]。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果您想要讓使用者使用 Salesforce 帳戶登入，您必須將該帳戶定義為宣告提供者，Azure AD B2C 可透過端點進行通訊。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Salesforce 帳戶新增至原則擴充檔中的 **ClaimsProviders** 元素，將其定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OIDC">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the {org} below to your Salesforce organization -->
            <Item Key="METADATA">https://{org}.my.salesforce.com/.well-known/openid-configuration</Item>
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

4. 設定 `{org}` 您的 Salesforce 組織的中繼資料 URI。
5. 將 **client_id** 設定為來自應用程式註冊的應用程式識別碼。
6. 儲存檔案。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

到目前為止，您已設定原則，讓 Azure AD B2C 知道如何與您的 Salesforce 帳戶進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
2. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
3. 按一下 [上傳] 。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

目前，識別提供者已設定，但還未出現在任何註冊/登入畫面中。 若要讓其可供使用，您需建立現有範本使用者旅程圖的複本，然後加以修改，讓它也包含 Salesforce 識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml 檔案。
2. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInSalesforce` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊/登入畫面上的識別提供者按鈕。 如果您新增 Salesforce 帳戶的 **ClaimsProviderSelection** 專案，當使用者在頁面上時，就會顯示新的按鈕。

1. 在您建立的使用者旅程圖中，尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `SalesforceExchange`：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 Salesforce 帳戶通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 ID 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```xml
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OIDC" />
    ```

    將 **TechnicalProfileReferenceId** 的值更新成您稍早所建立技術設定檔的 ID。 例如： `Salesforce-OIDC` 。

3. 儲存 TrustFrameworkExtensions.xml 檔案，並再次上傳它以供驗證。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>將 Salesforce 身分識別提供者新增至使用者流程 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 按一下您想要加入 Salesforce 身分識別提供者的使用者流程。
1. 在 **社交識別提供者** 底下，選取 [ **Salesforce**]。
1. 選取 [儲存]  。
1. 若要測試您的原則，請選取 [ **執行使用者流程**]。
1. 針對 [ **應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [**執行使用者流程**]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignInSalesforce.xml*。
1. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInSalesforce` 。
1. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_Salesforce`
1. 更新 **>referenceid** 中 **ReferenceId** 屬性的值，以符合您 (SignUpSignSalesforce) 建立之新使用者旅程圖的識別碼。
1. 儲存您的變更，然後上傳檔案。
1. 在 [自訂原則] 下，選取 [B2C_1A_signup_signin]。
1. 針對 [ **選取應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 選取 [ **立即執行** ]，然後選取 [salesforce] 以使用 salesforce 登入並測試自訂原則。

::: zone-end

## <a name="next-steps"></a>後續步驟

瞭解如何將 [Salesforce 權杖傳遞至您的應用程式](idp-pass-through-user-flow.md)。