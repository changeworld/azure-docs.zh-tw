---
title: 按自訂策略設置多租戶 Azure AD 的登錄
titleSuffix: Azure AD B2C
description: 使用 Azure 活動目錄 B2C 中的自訂策略添加多租戶 Azure AD 標識提供程式。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9ad51e113a752e0692cb377a83d4819b4e284bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188426"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定多租用戶 Azure Active Directory 的登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何在 Azure AD B2C 中透過使用[自訂原則](custom-policy-overview.md)，讓使用 Azure Active Directory (Azure AD) 多租用戶端點的使用者能夠登入。 這允許來自多個 Azure AD 租戶的使用者使用 Azure AD B2C 登錄，而無需為每個租戶配置標識提供程式。 不過，所有這些租用戶中的來賓成員並**不能**登入。 因此，您必須[個別設定每個租用戶](identity-provider-azure-ad-single-tenant-custom.md)。

## <a name="prerequisites"></a>Prerequisites

完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。

## <a name="register-an-application"></a>註冊應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 確保使用的目錄包含組織 Azure AD 租戶（例如，contoso.com）。 在頂部功能表中選擇**目錄 + 訂閱篩選器**，然後選擇包含租戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [應用程式註冊]****。
1. 選取 [新增註冊]****。
1. 輸入應用程式**的名稱**。 例如： `Azure AD B2C App` 。
1. 選擇此應用程式**的任何組織目錄中的帳戶**。
1. 對於**重定向 URI**，接受**Web**的值，並在所有小寫字母中輸入以下 URL，其中`your-B2C-tenant-name`替換為 Azure AD B2C 租戶的名稱。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如： `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 。

1. 選取 [註冊]****。 記錄 [應用程式 (用戶端) 識別碼]****，以便在稍後的步驟中使用。
1. 選擇**證書&機密**，然後選擇 **"新用戶端機密**"。
1. 輸入機密**的描述**，選擇過期，然後選擇 **"添加**"。 記錄機密**的值**，以便在後面的步驟中使用。

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

您必須將所建立的應用程式金鑰儲存在 Azure AD B2C 租用戶中。

1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱篩選器**，然後選擇包含 Azure AD B2C 租戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 **"策略"** 下，選擇**身份體驗框架**。
1. 選擇**策略鍵**，然後選擇 **"添加**"。
1. 針對 [選項]**** 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]****。 例如： `AADAppSecret` 。  首碼`B2C_1A_`在創建時會自動添加到金鑰的名稱中，因此其在下一節中的 XML 中的引用是*B2C_1A_AADAppSecret*。
1. 在 **"秘密**"中，輸入您之前記錄的客戶機密。
1. 針對 [金鑰使用方法]****，選取 `Signature`。
1. 選取 [建立]****。

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
1. 在 **"技術設定檔"** 元素下，更新**DisplayName**的值，例如`Contoso Employee`。 這個值會顯示在登入分頁的登入按鈕上。
1. **將client_id**設置為之前註冊的 Azure AD 多租戶應用程式的應用程式 ID。
1. 在**加密金鑰**下，將**StorageReferenceId**的值更新為前面創建的策略金鑰的名稱。 例如： `B2C_1A_AADAppSecret` 。

### <a name="restrict-access"></a>限制存取

> [!NOTE]
> 使用 `https://login.microsoftonline.com/` 作為 **ValidTokenIssuerPrefixes** 的值時，可允許所有 Azure AD 使用者登入您的應用程式。

您必須更新有效權杖簽發者清單，並將存取權僅限於能夠登入的特定 Azure AD 租用戶使用者清單。

要獲取這些值，請查看希望讓使用者登錄的每個 Azure AD 租戶的 OpenID Connect 發現中繼資料。 中繼資料 URL 的格式類似于`https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`，Azure `your-tenant` AD 租戶名稱位於此名稱。 例如：

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

對應用於登錄的每個 Azure AD 租戶執行以下步驟：

1. 打開瀏覽器並轉到租戶的 OpenID 連接中繼資料 URL。 查找**頒發物件**並記錄其值。 它應該看起來類似于`https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`。
1. 複製該值並將其粘貼到**ValidToken 問題首碼鍵中**。 使用逗號分隔多個頒發者。 上`ClaimsProvider`一個 XML 示例中將顯示一個包含兩個頒發者的示例。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

到目前為止，您已經配置了策略，以便 Azure AD B2C 知道如何與 Azure AD 目錄進行通信。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則]**** 頁面上，選取 [上傳原則]****。
2. 啟用 [覆寫現有的原則]****，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
3. 選取 [上傳]****。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

目前，識別提供者已設定，但還未出現在任何註冊/登入畫面中。 若要讓它可供使用，您必須建立現有範本使用者旅程圖的複本，然後修改它，讓它也包含 Azure AD 識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml** 檔案。
2. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInContoso` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊/登入畫面上的識別提供者按鈕。 如果您為 Azure AD 新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 查找在*TrustFramework 擴展.xml*中創建的使用者旅程中包含`Order="1"`的**業務流程步驟**元素。
1. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `AzureADExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 Azure AD 通訊以接收權杖。 藉由連結 Azure AD 宣告提供者的技術設定檔，將按鈕連結至動作。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 **Id** 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    將**技術設定檔參考 Id**的值更新到您之前創建的技術設定檔的**ID。** 例如： `Common-AAD` 。

3. 儲存 TrustFrameworkExtensions.xml** 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

通過您在 B2C 租戶中註冊的應用程式與 Azure AD B2C 的通信。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新啟動您創建的使用者旅程的依賴方 （RP） 檔：

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml** 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignContoso.xml*。
1. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInContoso` 。
1. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如： `http://contoso.com/B2C_1A_signup_signin_contoso` 。
1. 更新**預設使用者旅程**中的**參考 Id**屬性的值，以匹配您之前創建的使用者旅程的 ID。 例如，*註冊登錄 Contoso*。
1. 儲存變更並上傳檔案。
1. 在 **"自訂策略"** 下，在清單中選擇新策略。
1. 在 **"選擇應用程式**下拉清單"中，選擇之前創建的 Azure AD B2C 應用程式。 例如 *，testapp1*。
1. 立即複製 **"立即運行"終結點**並在專用瀏覽器視窗中打開它，例如，Google Chrome 中的隱身模式或 Microsoft Edge 中的 InPrivate 視窗。 通過在專用瀏覽器視窗中打開，您可以通過不使用任何當前緩存的 Azure AD 憑據來測試完整的使用者旅程。
1. 選擇 Azure AD 登錄按鈕，例如*Contoso 員工*，然後在 Azure AD 組織租戶之一中輸入使用者的憑據。 系統將要求您授權應用程式，然後輸入您的個人資料資訊。

如果登錄過程成功，瀏覽器將重定向到`https://jwt.ms`，顯示 Azure AD B2C 返回的權杖的內容。

要測試多租戶登錄功能，請使用存在另一個 Azure AD 租戶的使用者的憑據執行最後兩個步驟。

## <a name="next-steps"></a>後續步驟

使用自訂策略時，有時在開發策略期間對策略進行故障排除時可能需要其他資訊。

為了説明診斷問題，可以暫時將策略置於"開發人員模式"，並使用 Azure 應用程式見解收集日誌。 瞭解如何在[Azure 活動目錄 B2C 中：收集日誌](troubleshoot-with-application-insights.md)。
