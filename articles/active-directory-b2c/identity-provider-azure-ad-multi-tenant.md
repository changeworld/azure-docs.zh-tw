---
title: 設定多租使用者 Azure AD 的登入（依自訂原則）
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中使用自訂原則新增多租使用者 Azure AD 識別提供者。
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
ms.openlocfilehash: 8c2b97d1848450ecda2e83d5ba12469d7c61d8f9
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98952733"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定多租用戶 Azure Active Directory 的登入

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

本文說明如何使用 Azure Active Directory (Azure AD) 的多租使用者端點，讓使用者能夠登入。 允許來自多個 Azure AD 租使用者的使用者使用 Azure AD B2C 登入，而不需要為每個租使用者設定身分識別提供者。 不過，所有這些租用戶中的來賓成員並 **不能** 登入。 因此，您必須[個別設定每個租用戶](identity-provider-azure-ad-single-tenant.md)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>註冊應用程式

若要在 Azure Active Directory B2C (Azure AD B2C) 中讓具有 Azure AD 帳戶的使用者登入，您需要在 [Azure 入口網站](https://portal.azure.com)中建立應用程式。 如需詳細資訊，請參閱 [使用 Microsoft 身分識別平臺註冊應用程式](../active-directory/develop/quickstart-register-app.md)。


1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 請確定您使用的目錄包含您組織 Azure AD 租使用者 (例如 contoso.com) 。 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
1. 選取 [新增註冊]。
1. 輸入應用程式的 **名稱**。 例如： `Azure AD B2C App` 。
1. 選取 **任何組織目錄中的帳戶 (任何 Azure AD 目錄–** 此應用程式的多租使用者) 。
1. 針對 [重新 **導向 URI**]，接受 [ **Web**] 的值，並以所有小寫字母輸入下列 URL，其中以 `your-B2C-tenant-name` 您 Azure AD B2C 租使用者的名稱取代。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如： `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp` 。

1. 選取 [註冊]。 記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。
1. 選取 [ **憑證] & 秘密**，然後選取 [ **新增用戶端密碼**]。
1. 輸入秘密的 **描述** 、選取到期日，然後選取 [ **新增**]。 記錄秘密的 **值** ，以便在稍後的步驟中使用。

## <a name="configuring-optional-claims"></a>設定選擇性宣告

如果您想要 `family_name` 從 Azure AD 取得和 `given_name` 宣告，您可以在 Azure 入口網站 UI 或應用程式資訊清單中設定應用程式的選擇性宣告。 如需詳細資訊，請參閱[如何為 Azure AD 應用程式提供選擇性宣告](../active-directory/develop/active-directory-optional-claims.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 搜尋並選取 [Azure Active Directory]。
1. 從 [管理] 區段中，選取 [應用程式註冊]。
1. 從清單中，選取您要設定選擇性宣告的應用程式。
1. 從 [管理] 區段中，選取 [權杖設定]。
1. 選取 [新增選擇性宣告]。
1. 針對 **Token 類型**，選取 [ **識別碼**]。
1. 選取選擇性的宣告以新增、 `family_name` 和 `given_name` 。
1. 按一下 [新增] 。

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將所建立的應用程式金鑰儲存在 Azure AD B2C 租用戶中。

1. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 之下，選取 [Identity Experience Framework]。
1. 選取 [ **原則金鑰** ]，然後選取 [ **新增**]。
1. 針對 [選項] 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]。 例如： `AADAppSecret` 。  前置詞 `B2C_1A_` 會在建立時自動新增至您的金鑰名稱，因此在下一節的 XML 中，其參考是 *B2C_1A_AADAppSecret*。
1. 在 [ **密碼**] 中，輸入您稍早記錄的用戶端密碼。
1. 針對 [金鑰使用方法]，選取 `Signature`。
1. 選取 [建立]。

## <a name="configure-azure-ad-as-an-identity-provider"></a>將 Azure AD 設定成識別提供者

若要讓使用者使用 Azure AD 帳戶登入，您必須將 Azure AD 定義為 Azure AD B2C 可透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Azure AD 新增至原則擴充檔中的 **ClaimsProvider** 元素，將 Azure AD 定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml* 檔案。
1. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
1. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
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
1. 在 **TechnicalProfile** 元素底下，更新 **DisplayName** 的值，例如 `Contoso Employee` 。 這個值會顯示在登入分頁的登入按鈕上。
1. 將 **client_id** 設定為您稍早註冊 Azure AD 多租使用者應用程式的應用程式識別碼。
1. 在 [ **CryptographicKeys**] 底下，將 [ **StorageReferenceId** ] 的值更新為稍早建立之原則金鑰的名稱。 例如： `B2C_1A_AADAppSecret` 。

### <a name="restrict-access"></a>限制存取

使用 `https://login.microsoftonline.com/` 作為 **ValidTokenIssuerPrefixes** 的值時，可允許所有 Azure AD 使用者登入您的應用程式。 更新有效權杖簽發者的清單，並限制只能存取可登入 Azure AD 租使用者使用者的特定清單。

若要取得值，請查看您想要讓使用者從中登入的每個 Azure AD 租使用者的 OpenID Connect 探索中繼資料。 中繼資料 URL 的格式類似于 `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` ，其中 `your-tenant` 是您的 Azure AD 租使用者名稱。 例如：

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

針對每個應該用來登入的 Azure AD 租使用者，執行下列步驟：

1. 開啟您的瀏覽器，並移至租使用者的 OpenID Connect 中繼資料 URL。 尋找 **簽發者** 物件，並記錄其值。 它看起來應該像這樣 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` 。
1. 將此值複製並貼到 **>validtokenissuerprefixes** 索引鍵中。 以逗號分隔多個簽發者。 先前的 XML 範例中會顯示具有兩個簽發者的範例 `ClaimsProvider` 。

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

若要測試多租使用者登入功能，請使用存在另一個 Azure AD 租使用者的使用者認證，執行最後兩個步驟。 複製 [ **立即執行] 端點** ，然後在私用瀏覽器視窗中開啟它，例如 Google Chrome 中的 Incognito 模式或 Microsoft Edge 中的 InPrivate 視窗。 在私用瀏覽器視窗中開啟，可讓您藉由不使用目前快取的 Azure AD 認證來測試完整的使用者旅程圖。

## <a name="next-steps"></a>下一步

使用自訂原則時，您有時可能需要在其開發期間針對原則進行疑難排解時需要額外的資訊。

若要協助診斷問題，您可以暫時將原則設為「開發人員模式」，並使用 Azure 應用程式見解收集記錄。 瞭解如何 [Azure Active Directory B2C：收集記錄](troubleshoot-with-application-insights.md)檔。

::: zone-end
