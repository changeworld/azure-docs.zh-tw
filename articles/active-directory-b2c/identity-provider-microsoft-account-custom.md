---
title: 使用自訂策略使用 Microsoft 帳戶帳戶設置登錄
titleSuffix: Azure AD B2C
description: 如何使用自訂策略啟用 Microsoft 帳戶 （MSA） 作為使用 OpenID 連接 （OIDC） 協定的身份供應商。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188112"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 Microsoft 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介紹如何使用 Azure 活動目錄 B2C （Azure AD B2C） 中的[自訂策略](custom-policy-overview.md)為 Microsoft 帳戶的使用者啟用登錄。

## <a name="prerequisites"></a>Prerequisites

- 完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。
- 如果您還沒有 Microsoft 帳戶，請在 創建[https://www.live.com/](https://www.live.com/)一個帳戶。

## <a name="register-an-application"></a>註冊應用程式

要為使用 Microsoft 帳戶的使用者啟用登錄，需要在 Azure AD 租戶中註冊應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 通過在頂部功能表中選擇**目錄 + 訂閱**篩選器並選擇包含 Azure AD 租戶的目錄，請確保使用的目錄包含 Azure AD 租戶。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [新增註冊]****。
1. 輸入應用程式**的名稱**。 例如 *MSAapp1*。
1. 在 **"支援帳戶類型**"下，選擇**任何組織目錄中的帳戶和個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com）。**
1. 在**重定向 URI（可選）** 下，選擇 **"Web"** 並在文字方塊中輸入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 替換為`your-tenant-name`Azure AD B2C 租戶名稱。
1. 選擇 **"註冊"**
1. 記錄應用程式概述頁上顯示**的應用程式（用戶端）ID。** 在後續部分中配置聲明提供程式時，需要這樣做。
1. 選擇**證書&機密**
1. 按一下 **"新用戶端"機密**
1. 輸入機密**的說明**，例如*MSA 應用程式用戶端金鑰*，然後按一下"**添加**"。
1. 記錄 **"值"** 列中顯示的應用程式密碼。 在下一節中使用此值。

## <a name="configuring-optional-claims"></a>設定選擇性宣告

如果要從 Azure AD `family_name` `given_name`獲取 和 聲明，可以在 Azure 門戶 UI 或應用程式清單中為應用程式佈建可選聲明。 有關詳細資訊，請參閱[如何向 Azure AD 應用提供可選聲明](../active-directory/develop/active-directory-optional-claims.md)。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 搜尋並選取 [Azure Active Directory]****。
1. 在 **"管理"** 部分中，選擇**應用註冊**。
1. 選擇要在清單中為其配置可選聲明的應用程式。
1. 在 **"管理**"部分中，選擇**權杖配置（預覽）。**
1. 選擇 **"添加可選聲明**"。
1. 選擇要配置的權杖類型。
1. 選擇要添加的可選聲明。
1. 按一下 **[新增]**。

## <a name="create-a-policy-key"></a>建立原則金鑰

現在，您已經在 Azure AD 租戶中創建了應用程式，您需要在 Azure AD B2C 租戶中存儲該應用程式的用戶端機密。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇包含租戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
1. 選取 [原則金鑰]****，然後選取 [新增]****。
1. 針對 [選項]**** 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]****。 例如： `MSASecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 **"機密**"中，輸入您在上一節中記錄的用戶端機密。
1. 針對 [金鑰使用方法]****，選取 `Signature`。
1. 按一下 **[建立]**。

## <a name="add-a-claims-provider"></a>新增宣告提供者

要使使用者能夠使用 Microsoft 帳戶登錄，您需要將帳戶定義為 Azure AD B2C 可通過終結點與其通信的聲明提供程式。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由在原則擴充檔中新增 **ClaimsProvider** 元素，將 Azure AD 定義成宣告提供者。

1. 打開*信任框架擴展.xml*策略檔。
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

1. 將**client_id**的值替換為前面記錄的 Azure AD*應用程式（用戶端）ID。*
1. 儲存檔案。

現在，您已經配置了策略，以便 Azure AD B2C 知道如何與 Azure AD 中的 Microsoft 帳戶應用程式進行通信。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

在繼續之前，請上載修改後的策略，以確認到目前為止沒有任何問題。

1. 導航到 Azure 門戶中的 Azure AD B2C 租戶，然後選擇**標識體驗框架**。
1. 在**自訂策略**頁上，選擇 **"上載自訂策略**"。
1. 啟用 [覆寫現有的原則]****，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
1. 按一下 [上傳]****。

如果門戶中未顯示任何錯誤，請繼續執行下一節。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，您已設置標識提供程式，但它尚未在任何註冊或登錄螢幕中可用。 要使其可用，請創建現有範本使用者旅程的重複項，然後對其進行修改，以便它還必須具有 Microsoft 帳戶標識提供程式。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml** 檔案。
1. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
1. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
1. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
1. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInMSA` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊或登入畫面上的識別提供者按鈕。 如果為 Microsoft 帳戶添加**聲明提供程式選擇**元素，則當使用者落在頁面上時將顯示一個新按鈕。

1. 在 TrustFrameworkExtensions.xml** 檔案中，於您所建立的使用者旅程圖中尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
1. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `MicrosoftAccountExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在這種情況下，操作是 Azure AD B2C 與 Microsoft 帳戶通信以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
1. 新增下列 **ClaimsExchange** 元素，請確定用於 ID 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    更新**技術設定檔基準Id**的值，以匹配前面添加的聲明`Id`提供程式**的技術設定檔**元素中的值的值。 例如： `MSA-OIDC` 。

1. 儲存 TrustFrameworkExtensions.xml** 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

通過您在 B2C 租戶中註冊的應用程式與 Azure AD B2C 的通信。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml** 複本，並將它重新命名。 例如，將它重新命名為 SignUpSignInMSA.xml**。
1. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInMSA` 。
1. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_msa`
1. 更新**預設使用者旅程**中的**參考 Id**屬性的值，以匹配您之前創建的使用者旅程的 ID（SignUpSignInMSA）。
1. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
1. 請確保在上一部分中創建的 Azure AD B2C 應用程式（或通過完成先決條件（例如*Webapp1*或*testapp1）* 在 **"選擇應用程式**"欄位中被選中，然後通過按一下"**立即運行"** 來測試它。
1. 選擇 **"微軟帳戶"** 按鈕並登錄。

    如果登錄操作成功，您將被重定向到`jwt.ms`顯示解碼權杖的標記，類似于：

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
