---
title: 使用 Microsoft 帳戶設定註冊和登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C，讓具有 Microsoft 帳戶的客戶得以註冊和登入您的應用程式。
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
ms.openlocfilehash: 123b36ba854bec8b363d59bbed5e70f18da1e578
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653702"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定註冊，並以 Microsoft 帳戶登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>建立 Microsoft 帳戶應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中使用 Microsoft 帳戶作為[識別提供者](openid-connect.md)，您必須在 Azure AD 租用戶中建立應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。 如果您還沒有 Microsoft 帳戶，可以在 [https://www.live.com/](https://www.live.com/) 取得。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
1. 選取 [新增註冊]。
1. 輸入應用程式的 **名稱**。 例如 *MSAapp1*。
1. 在 [支援的帳戶類型] 底下，選取 [任何組織目錄中的帳戶 (任何 Azure AD 目錄 - 多租用戶) 和個人 Microsoft 帳戶 (例如 Skype、Xbox)]。

   如需不同帳戶類型選項的詳細資訊，請參閱[快速入門：向 Microsoft 身分識別平台註冊應用程式](../active-directory/develop/quickstart-register-app.md)。
1. 在 [重新導向 URI (選擇性)] 底下，選取 [Web]，然後在文字方塊中輸入 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp`。 以您的 Azure AD B2C 租用戶名稱取代 `<tenant-name>`。
1. 選取 [註冊]
1. 記錄應用程式 [概觀] 頁面上所顯示的 **應用程式 (用戶端) 識別碼**。 當您在下一節中設定識別提供者時，將需要這項資訊。
1. 選取 [憑證及祕密]
1. 按一下 [新增用戶端密碼]
1. 輸入該密碼的 [描述] (例如 *應用程式密碼 1*)，然後按一下 [新增]。
1. 記錄 [值] 欄中顯示的應用程式密碼。 當您在下一節中設定識別提供者時，將需要這項資訊。

::: zone pivot="b2c-user-flow"

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>將 Microsoft 帳戶設為識別提供者

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。
1. 選取 [識別提供者]，然後選取 [Microsoft 帳戶]。
1. 輸入 [名稱]。 例如 *MSA*。
1. 在 [用戶端識別碼]，輸入您先前建立之 Azure AD 應用程式的應用程式 (用戶端) 識別碼。
1. 在 [用戶端密碼] 中，輸入您記下的用戶端密碼。
1. 選取 [儲存]。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>設定選擇性宣告

如果您想要從 Azure AD 取得 `family_name` 和 `given_name` 宣告，您可以在 Azure 入口網站 UI 或應用程式資訊清單中設定應用程式的選擇性宣告。 如需詳細資訊，請參閱[如何為 Azure AD 應用程式提供選擇性宣告](../active-directory/develop/active-directory-optional-claims.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [Azure Active Directory]。
1. 從 [管理] 區段中，選取 [應用程式註冊]。
1. 從清單中，選取您要為其設定選擇性宣告的應用程式。
1. 從 [管理] 區段中，選取 [權杖設定 (預覽)]。
1. 選取 [新增選擇性宣告]。
1. 選取您要設定的權杖類型。
1. 選取要新增的選擇性宣告。
1. 按一下 [新增] 。

## <a name="create-a-policy-key"></a>建立原則金鑰

既然您已在 Azure AD 租用戶中建立應用程式，您就必須將該應用程式的用戶端密碼儲存於您的 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]。
1. 選取 [原則金鑰]，然後選取 [新增]。
1. 針對 [選項] 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]。 例如： `MSASecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 [祕密] 中，輸入您在上一節記下的用戶端密碼。
1. 針對 [金鑰使用方法]，選取 `Signature`。
1. 按一下 [建立]。

## <a name="add-a-claims-provider"></a>新增宣告提供者

若要讓使用者使用 Microsoft 帳戶進行登入，您必須將該帳戶定義為 Azure AD B2C 能夠透過端點進行通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由在原則擴充檔中新增 **ClaimsProvider** 元素，將 Azure AD 定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml* 原則檔案。
1. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
1. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. 將 **client_id** 的值取代為您稍早記錄的 Azure AD 應用程式的「應用程式 (用戶端) 識別碼」。
1. 儲存檔案。

您現在已設定原則，讓 Azure AD B2C 知道如何在 Azure AD 中與您的 Microsoft 帳戶應用程式進行通訊。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

繼續之前，先上傳修改過的原則，以確認其目前沒有任何問題。

1. 在 Azure 入口網站中瀏覽至您的 Azure AD B2C 租用戶，然後選取 [Identity Experience Framework]。
1. 在 [自訂原則] 頁面上，選取 [上傳自訂原則]。
1. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
1. 按一下 [上傳] 。

如果入口網站中未顯示任何錯誤，請繼續下一節。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，您已設定識別提供者，但其還未出現在任何註冊或登入畫面中。 若要使其可供使用，請建立現有範本使用者旅程圖的複本，然後加以修改，讓其也能具有該 Microsoft 帳戶識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml 檔案。
1. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
1. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
1. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
1. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInMSA` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊或登入畫面上的識別提供者按鈕。 如果您為 Microsoft 帳戶新增 **ClaimsProviderSelection** 元素，則會在使用者登陸頁面時顯示新按鈕。

1. 在 TrustFrameworkExtensions.xml 檔案中，於您所建立的使用者旅程圖中尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
1. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `MicrosoftAccountExchange`：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 Microsoft 帳戶通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
1. 新增下列 **ClaimsExchange** 元素，請確定用於 ID 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    更新 **TechnicalProfileReferenceId** 的值，以符合您稍早新增的宣告提供者之 **TechnicalProfile** 元素中的 `Id` 值。 例如： `MSA-OIDC` 。

1. 儲存 TrustFrameworkExtensions.xml 檔案，並再次上傳它以供驗證。

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>將 Microsoft 身分識別提供者新增至使用者流程 

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 按一下您想要使用 Microsoft 身分識別提供者的使用者流程。
1. 在 **社交識別提供者** 底下，選取 [ **Microsoft 帳戶**]。
1. 選取 [儲存]  。
1. 若要測試您的原則，請選取 [ **執行使用者流程**]。
1. 針對 [ **應用程式**]，選取您先前註冊的 web 應用程式（名為 *testapp1-pre-production* ）。 **Reply URL** 應顯示 `https://jwt.ms`。
1. 按一下 [**執行使用者流程**]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml 複本，並將它重新命名。 例如，將它重新命名為 SignUpSignInMSA.xml。
1. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInMSA` 。
1. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_msa`
1. 更新 **DefaultUserJourney** 中 **ReferenceId** 屬性的值，以符合您先前建立之使用者旅程圖的識別碼 (SignUpSignInMSA)。
1. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
1. 確定已在 [選取應用程式] 欄位中選取您在上一節建立的 Azure AD B2C 應用程式 (或完成先決條件，例如 *webapp1* 或 *testapp1*)，然後按一下 [立即執行] 進行測試。
1. 選取 [Microsoft 帳戶] 按鈕並登入。

::: zone-end