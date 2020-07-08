---
title: 使用自訂原則來設定以 Azure AD 帳戶進行登入
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中使用自訂原則來設定以 Azure Active Directory 帳戶進行登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06ba565de8ca24c8c0baa576b74e70035384be09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388420"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 Azure Active Directory 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何使用 Azure Active Directory B2C （Azure AD B2C）中的[自訂原則](custom-policy-overview.md)，讓來自 Azure Active Directory （Azure AD）組織的使用者登入。

## <a name="prerequisites"></a>必要條件

完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將所建立的應用程式金鑰儲存在 Azure AD B2C 租用戶中。

1. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 之下，選取 [Identity Experience Framework]。
1. 選取 [**原則金鑰**]，然後選取 [**新增**]。
1. 針對 [選項] 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]。 例如： `ContosoAppSecret` 。  前置詞 `B2C_1A_` 會在建立時自動新增至金鑰的名稱，因此在下一節的 XML 中，其參考是*B2C_1A_ContosoAppSecret*。
1. 在 [**秘密**] 中，輸入您先前記錄的用戶端密碼。
1. 針對 [金鑰使用方法]，選取 `Signature`。
1. 選取 [建立]。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 Azure AD 進行登入，您必須將 Azure AD 定義成 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Azure AD 新增至原則擴充檔中的 **ClaimsProvider** 元素，將 Azure AD 定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml* 檔案。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
3. 新增新的 **ClaimsProvider**，如下所示：
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 在 **ClaimsProvider** 元素底下，將 **Domain** 的值更新成可用來與其他識別提供者做區別的唯一值。 例如 `Contoso` 。 您不應在此網域設定的結尾放置 `.com`。
5. 在 **ClaimsProvider** 元素下，將 **DisplayName** 的值更新成可用來與其他識別提供者區別的唯一值。 目前不使用此值。

### <a name="update-the-technical-profile"></a>更新技術設定檔

若要從 Azure AD 端點取得權杖，您需要定義 Azure AD B2C 應該用來與 Azure AD 進行通訊的通訊協定。 此作業可在 **ClaimsProvider** 的 **TechnicalProfile** 元素內完成。

1. 更新 **TechnicalProfile** 元素的識別碼。 此識別碼是用來從原則的其他部分參考此技術設定檔，例如 `OIDC-Contoso` 。
1. 更新 **DisplayName** 的值。 這個值會顯示在登入畫面的登入按鈕上。
1. 更新 [描述]**** 的值。
1. Azure AD 使用 OpenID Connect 通訊協定，因此請確定 [通訊協定]**** 的值為 `OpenIdConnect`。
1. 將 **METADATA** 的值設定為 `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`，其中 `tenant-name` 是您的 Azure AD 租用戶名稱。 例如， `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. 將 **client_id** 設定為來自應用程式註冊的應用程式識別碼。
1. 在 [ **CryptographicKeys**] 底下，將 [ **StorageReferenceId** ] 的值更新為您稍早建立之原則金鑰的名稱。 例如： `B2C_1A_ContosoAppSecret` 。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

現在，您應該已設定原則，所以 Azure AD B2C 知道如何與 Azure AD 目錄進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
1. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
1. 按一下 [上傳] 。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，識別提供者已設定完成，但尚未在任何註冊/登入頁面中提供。 若要讓它可供使用，請建立現有範本使用者旅程圖的複本，然後修改它，讓它也具有 Azure AD 身分識別提供者：

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml 檔案。
1. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
1. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
1. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
1. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInContoso` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection**元素類似于註冊/登入頁面上的識別提供者按鈕。 如果您為 Azure AD 新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 在**OrchestrationStep** `Order="1"` 您于*TrustFrameworkExtensions.xml*中建立的使用者旅程圖中，尋找包含的 OrchestrationStep 元素。
1. 在 **ClaimsProviderSelections** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `ContosoExchange`：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 Azure AD 通訊以接收權杖。 藉由連結 Azure AD 宣告提供者的技術設定檔，將按鈕連結至動作：

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
1. 新增下列**ClaimsExchange**元素，確保您針對用於**TargetClaimsExchangeId**的**識別碼**使用相同的值：

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    將**TechnicalProfileReferenceId**的值更新為您稍早建立之技術設定檔的**識別碼**。 例如： `OIDC-Contoso` 。

1. 儲存 TrustFrameworkExtensions.xml 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

與 Azure AD B2C 通訊，會透過您在 B2C 租用戶中註冊的應用程式來進行。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignInContoso.xml*。
1. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInContoso` 。
1. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如： `http://contoso.com/B2C_1A_signup_signin_contoso` 。
1. 更新**DefaultUserJourney**中**ReferenceId**屬性的值，以符合您稍早建立的使用者旅程圖的識別碼。 例如， *SignUpSignInContoso*。
1. 儲存變更並上傳檔案。
1. 在 [**自訂原則**] 底下，選取清單中的新原則。
1. 在 [**選取應用程式**] 下拉式選單中，選取您稍早建立的 Azure AD B2C 應用程式。 例如 *testapp1*。
1. 複製 [**立即執行] 端點**，並在私用瀏覽器視窗中開啟它，例如 Google Chrome 中的 Incognito 模式或 Microsoft Edge 中的 InPrivate 視窗。 在私用瀏覽器視窗中開啟，可讓您藉由不使用任何目前快取的 Azure AD 認證來測試完整的使用者旅程圖。
1. 選取 [Azure AD 登入] 按鈕（例如*Contoso Employee*），然後在您的 Azure AD 組織租使用者中輸入使用者的認證。 系統會要求您授權應用程式，然後輸入您的設定檔資訊。

如果登入程式成功，您的瀏覽器會重新導向至 `https://jwt.ms` ，其中會顯示 Azure AD B2C 所傳回之權杖的內容。

## <a name="next-steps"></a>後續步驟

使用自訂原則時，您有時可能需要在其開發期間針對原則進行疑難排解時的其他資訊。

若要協助診斷問題，您可以暫時讓原則進入「開發人員模式」，並收集具有 Azure 應用程式深入解析的記錄。 瞭解[Azure Active Directory B2C：收集記錄](troubleshoot-with-application-insights.md)檔。
