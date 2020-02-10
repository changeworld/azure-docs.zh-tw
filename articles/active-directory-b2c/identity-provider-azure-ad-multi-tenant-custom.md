---
title: 設定由自訂原則進行多租使用者 Azure AD 的登入
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 中的自訂原則新增多租使用者 Azure AD 識別提供者。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9d8d13ec955867eb574b5f0d782727d6ff8d063a
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111555"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定多租用戶 Azure Active Directory 的登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何在 Azure AD B2C 中透過使用[自訂原則](custom-policy-overview.md)，讓使用 Azure Active Directory (Azure AD) 多租用戶端點的使用者能夠登入。 這可讓來自多個 Azure AD 租使用者的使用者使用 Azure AD B2C 登入，而不需要為每個租使用者設定身分識別提供者。 不過，所有這些租用戶中的來賓成員並**不能**登入。 因此，您必須[個別設定每個租用戶](identity-provider-azure-ad-single-tenant-custom.md)。

## <a name="prerequisites"></a>必要條件

完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。

## <a name="register-an-application"></a>註冊應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 請確定您使用的是包含組織 Azure AD 租使用者的目錄（例如 contoso.com）。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
1. 選取 [新增註冊]。
1. 輸入應用程式的**名稱**。 例如： `Azure AD B2C App` 。
1. 在此應用程式的**任何組織目錄中**，選取 [帳戶]。
1. 針對 [重新**導向 URI**]，接受 [ **Web**] 的值，並以所有小寫字母輸入下列 URL，其中 `your-B2C-tenant-name` 會取代為您 Azure AD B2C 的租使用者名稱。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如： `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 。

1. 選取 [註冊]。 記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。
1. 選取 [**憑證 & 密碼**]，然後選取 [**新增用戶端密碼**]。
1. 輸入密碼的**描述**，選取到期日，然後選取 [**新增**]。 記錄密碼的**值**，以便在稍後的步驟中使用。

## <a name="configuring-optional-claims"></a>設定選擇性宣告

如果您想要從 Azure AD 取得 `family_name` 和 `given_name` 宣告，您可以在 Azure 入口網站 UI 或應用程式資訊清單中設定應用程式的選擇性宣告。 如需詳細資訊，請參閱[如何提供選擇性宣告給您的 Azure AD 應用程式](../active-directory/develop/active-directory-optional-claims.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [Azure Active Directory]。
1. 從 [**管理**] 區段中，選取 [**應用程式註冊**]。
1. 在清單中選取您想要為其設定選擇性宣告的應用程式。
1. 從 [**管理**] 區段中，選取 [**權杖設定（預覽）** ]。
1. 選取 [**新增選擇性**宣告]。
1. 選取您想要設定的權杖類型。
1. 選取要新增的選擇性宣告。
1. 按一下 [加入]。

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將所建立的應用程式金鑰儲存在 Azure AD B2C 租用戶中。

1. 請確定您使用的是包含您 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [**原則**] 底下，選取 [ **Identity Experience Framework**]。
1. 選取 [**原則金鑰**]，然後選取 [**新增**]。
1. 針對 [選項] 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]。 例如： `AADAppSecret` 。  前置詞 `B2C_1A_` 會在建立時自動新增至金鑰的名稱，因此在下一節的 XML 中，其參考是要*B2C_1A_AADAppSecret*。
1. 在 [**秘密**] 中，輸入您先前記錄的用戶端密碼。
1. 針對 [金鑰使用方法]，選取 `Signature`。
1. 選取 [建立]。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 Azure AD 進行登入，您必須將 Azure AD 定義成 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Azure AD 新增至原則擴充檔中的 **ClaimsProvider** 元素，將 Azure AD 定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml* 檔案。
1. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
1. 新增新的 **ClaimsProvider**，如下所示：

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. 在 **ClaimsProvider** 元素底下，將 **Domain** 的值更新成可用來與其他識別提供者做區別的唯一值。
1. 在**TechnicalProfile**元素底下，更新**DisplayName**的值，例如 `Contoso Employee`。 這個值會顯示在登入分頁的登入按鈕上。
1. 將**client_id**設定為您稍早註冊的 Azure AD 多租使用者應用程式的應用程式識別碼。
1. 在 [ **CryptographicKeys**] 底下，將 [ **StorageReferenceId** ] 的值更新為稍早建立之原則金鑰的名稱。 例如： `B2C_1A_AADAppSecret` 。

### <a name="restrict-access"></a>限制存取

> [!NOTE]
> 使用 `https://login.microsoftonline.com/` 作為 **ValidTokenIssuerPrefixes** 的值時，可允許所有 Azure AD 使用者登入您的應用程式。

您必須更新有效權杖簽發者清單，並將存取權僅限於能夠登入的特定 Azure AD 租用戶使用者清單。

若要取得這些值，請查看您想要讓使用者從中登入的每個 Azure AD 租使用者的 OpenID Connect 探索中繼資料。 中繼資料 URL 的格式類似于 `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`，其中 `your-tenant` 是您 Azure AD 的租使用者名稱。 例如，

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

針對應該用來登入的每個 Azure AD 租使用者，執行下列步驟：

1. 開啟瀏覽器，並移至租使用者的 OpenID Connect 中繼資料 URL。 尋找**簽發者**物件並記錄其值。 看起來應該類似 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`。
1. 複製值並貼到**ValidTokenIssuerPrefixes**機碼中。 以逗號分隔多個簽發者。 先前的 `ClaimsProvider` XML 範例中會顯示具有兩個簽發者的範例。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

現在，您已設定原則，讓 Azure AD B2C 知道如何與您的 Azure AD 目錄通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
2. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
3. 選取 [上傳]。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

目前，識別提供者已設定，但還未出現在任何註冊/登入畫面中。 若要讓它可供使用，您必須建立現有範本使用者旅程圖的複本，然後修改它，讓它也包含 Azure AD 識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml 檔案。
2. 尋找並複製包含 **之**UserJourney`Id="SignUpOrSignIn"` 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInContoso` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊/登入畫面上的識別提供者按鈕。 如果您為 Azure AD 新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 在您于*TrustFrameworkExtensions*中建立的使用者旅程圖中，尋找包含 `Order="1"` 的**OrchestrationStep**元素。
1. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `AzureADExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 Azure AD 通訊以接收權杖。 藉由連結 Azure AD 宣告提供者的技術設定檔，將按鈕連結至動作。

1. 在使用者旅程圖中，尋找包含 **的**OrchestrationStep`Order="2"`。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 **Id** 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    將 **TechnicalProfileReferenceId** 的值更新成您稍早所建立技術設定檔的 **Id**。 例如： `Common-AAD` 。

3. 儲存 TrustFrameworkExtensions.xml 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

與 Azure AD B2C 的通訊會透過您在 B2C 租使用者中註冊的應用程式進行。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者（RP）檔案，此檔案會起始您建立的使用者旅程圖：

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignContoso.xml*。
1. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInContoso` 。
1. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如： `http://contoso.com/B2C_1A_signup_signin_contoso` 。
1. 更新**DefaultUserJourney**中**ReferenceId**屬性的值，以符合您稍早建立的使用者旅程圖的識別碼。 例如， *SignUpSignInContoso*。
1. 儲存變更並上傳檔案。
1. 在 [**自訂原則**] 底下，選取清單中的新原則。
1. 在 [**選取應用程式**] 下拉式選單中，選取您稍早建立的 Azure AD B2C 應用程式。 例如， *testapp1*。
1. 複製 [**立即執行] 端點**，並在私用瀏覽器視窗中開啟它，例如 Google Chrome 中的 Incognito 模式或 Microsoft Edge 中的 InPrivate 視窗。 在私用瀏覽器視窗中開啟，可讓您藉由不使用任何目前快取的 Azure AD 認證來測試完整的使用者旅程圖。
1. 選取 [Azure AD 登入] 按鈕（例如*Contoso Employee*），然後在其中一個 Azure AD 組織租使用者中輸入使用者的認證。 系統會要求您授權應用程式，然後輸入您的設定檔資訊。

如果登入程式成功，您的瀏覽器會重新導向至 `https://jwt.ms`，以顯示 Azure AD B2C 所傳回之權杖的內容。

若要測試多租使用者登入功能，請執行最後兩個步驟，針對存在另一個 Azure AD 租使用者的使用者使用認證。

## <a name="next-steps"></a>後續步驟

使用自訂原則時，您有時可能需要在其開發期間針對原則進行疑難排解時的其他資訊。

若要協助診斷問題，您可以暫時讓原則進入「開發人員模式」，並收集具有 Azure 應用程式深入解析的記錄。 瞭解[Azure Active Directory B2C：收集記錄](troubleshoot-with-application-insights.md)檔。
