---
title: 使用自訂原則來設定以 Microsoft 帳戶帳戶進行登入
titleSuffix: Azure AD B2C
description: 如何使用自訂原則，以使用 OpenID Connect （OIDC）通訊協定來啟用 Microsoft 帳戶（MSA）作為身分識別提供者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188112"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 Microsoft 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何使用 Azure Active Directory B2C （Azure AD B2C）中的[自訂原則](custom-policy-overview.md)，讓使用者能夠從 Microsoft 帳戶登入。

## <a name="prerequisites"></a>先決條件

- 完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。
- 如果您還沒有 Microsoft 帳戶，請在上[https://www.live.com/](https://www.live.com/)建立一個。

## <a name="register-an-application"></a>註冊應用程式

若要讓具有 Microsoft 帳戶的使用者能夠登入，您必須在 Azure AD 租使用者內註冊應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD 租使用者的目錄，以確定您使用的是包含 Azure AD 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [新增註冊]  。
1. 輸入應用程式的 [**名稱**]。 例如 *MSAapp1*。
1. 在 [**支援的帳戶類型**] 底下，選取 **[任何組織目錄中的帳戶] 和 [個人 Microsoft 帳戶] （例如 Skype、Xbox、Outlook.com）**。
1. 在 [重新**導向 URI （選用）**] 底下， `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`選取 [ **Web** ]，然後在文字方塊中輸入。 將`your-tenant-name`取代為您的 Azure AD B2C 租使用者名稱。
1. 選取 [**註冊**]
1. 記錄 [應用程式總覽] 頁面上顯示的**應用程式（用戶端）識別碼**。 當您在稍後的章節中設定宣告提供者時，就需要此項。
1. 選取**憑證 & 秘密**
1. 按一下 [**新增用戶端密碼**]
1. 輸入密碼的**描述**，例如*MSA 應用程式用戶端密碼*，然後按一下 [**新增**]。
1. 記錄 [**值**] 資料行中顯示的應用程式密碼。 您會在下一節中使用此值。

## <a name="configuring-optional-claims"></a>設定選擇性宣告

如果您想要從 Azure AD `family_name`取得`given_name`和宣告，您可以在 Azure 入口網站 UI 或應用程式資訊清單中設定應用程式的選擇性宣告。 如需詳細資訊，請參閱[如何提供選擇性宣告給您的 Azure AD 應用程式](../active-directory/develop/active-directory-optional-claims.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [Azure Active Directory]  。
1. 從 [**管理**] 區段中，選取 [**應用程式註冊**]。
1. 在清單中選取您想要為其設定選擇性宣告的應用程式。
1. 從 [**管理**] 區段中，選取 [**權杖設定（預覽）**]。
1. 選取 [**新增選擇性**宣告]。
1. 選取您想要設定的權杖類型。
1. 選取要新增的選擇性宣告。
1. 按一下 [加入]  。

## <a name="create-a-policy-key"></a>建立原則金鑰

既然您已在 Azure AD 租使用者中建立應用程式，您必須將該應用程式的用戶端密碼儲存在您的 Azure AD B2C 租使用者中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請確定您使用的是包含您 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
1. 選取 [原則金鑰]****，然後選取 [新增]****。
1. 針對 [選項]**** 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]****。 例如： `MSASecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 [**秘密**] 中，輸入您在上一節中記錄的用戶端密碼。
1. 針對 [金鑰使用方法]****，選取 `Signature`。
1. 按一下 [建立]  。

## <a name="add-a-claims-provider"></a>新增宣告提供者

若要讓使用者能夠使用 Microsoft 帳戶進行登入，您必須將此帳戶定義為 Azure AD B2C 可以透過端點與通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由在原則擴充檔中新增 **ClaimsProvider** 元素，將 Azure AD 定義成宣告提供者。

1. 開啟*TrustFrameworkExtensions*原則檔案。
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
            <Item Key="UsePolicyInRedirectUri">0</Item>
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

1. 將**client_id**的值取代為您稍早記錄的 Azure AD 應用程式的*應用程式（用戶端）識別碼*。
1. 儲存檔案。

您現在已設定原則，讓 Azure AD B2C 知道如何在 Azure AD 中與您的 Microsoft 帳戶應用程式通訊。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

在繼續之前，請先上傳修改過的原則，以確認它目前沒有任何問題。

1. 流覽至您在 Azure 入口網站中的 Azure AD B2C 租使用者，然後選取 [ **Identity Experience Framework**]。
1. 在 [**自訂**原則] 頁面上，選取 [**上傳自訂原則**]。
1. 啟用 [覆寫現有的原則]****，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
1. 按一下 [上傳]  。

如果入口網站中沒有顯示任何錯誤，請繼續下一節。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，您已設定身分識別提供者，但它尚未在任何註冊或登入畫面中提供。 若要讓它可供使用，請建立現有範本使用者旅程圖的複本，然後加以修改，讓它也具有 Microsoft 帳戶身分識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml** 檔案。
1. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
1. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
1. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
1. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInMSA` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊或登入畫面上的識別提供者按鈕。 如果您為 Microsoft 帳戶新增**ClaimsProviderSelection**元素，當使用者在頁面上時，就會顯示新的按鈕。

1. 在 TrustFrameworkExtensions.xml** 檔案中，於您所建立的使用者旅程圖中尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
1. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `MicrosoftAccountExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是用於 Azure AD B2C 與 Microsoft 帳戶通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
1. 新增下列 **ClaimsExchange** 元素，請確定用於 ID 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    更新**TechnicalProfileReferenceId**的值，以符合您稍早`Id`新增的宣告提供者之**TechnicalProfile**元素中的值。 例如： `MSA-OIDC` 。

1. 儲存 TrustFrameworkExtensions.xml** 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

與 Azure AD B2C 的通訊會透過您在 B2C 租使用者中註冊的應用程式進行。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml** 複本，並將它重新命名。 例如，將它重新命名為 SignUpSignInMSA.xml**。
1. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInMSA` 。
1. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_msa`
1. 更新**DefaultUserJourney**中**ReferenceId**屬性的值，以符合您稍早建立之使用者旅程圖的識別碼（SignUpSignInMSA）。
1. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
1. 請確定已在 [**選取應用程式**] 欄位中選取您在上一節中建立的 Azure AD B2C 應用程式（或完成必要條件，例如*webapp1*或*testapp1*），然後按一下 [**立即執行**] 進行測試。
1. 選取 [ **Microsoft 帳戶**] 按鈕並登入。

    如果登入作業成功，系統會將您重新導向至`jwt.ms` ，其中會顯示已解碼的權杖，如下所示：

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
