---
title: 使用自訂原則來設定以 Amazon 帳戶進行登入
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中使用自訂原則來設定以 Amazon 帳戶進行登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2de891ee109677f92ff603759701f7732f5951ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188506"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 Amazon 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何使用 Azure Active Directory B2C （Azure AD B2C）中的[自訂原則](custom-policy-overview.md)，讓 Amazon 帳戶的使用者登入。

## <a name="prerequisites"></a>Prerequisites

- 完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。
- 如果您還沒有 Amazon 帳戶，請在[https://www.amazon.com/](https://www.amazon.com/)建立一個。

## <a name="register-the-application"></a>註冊應用程式

若要讓使用者能夠從 Amazon 帳戶登入，您必須建立 Amazon 應用程式。

1. 以您的 Amazon 帳戶認證登入 [Amazon Developer Center (Amazon 開發人員中心)](https://login.amazon.com/)。
2. 若您尚未執行此動作，請按一下 [註冊] ****、遵循開發人員註冊步驟，並接受原則。
3. 選取 [註冊新應用程式]****。
4. 請輸入 [名稱]****、[描述]****，以及 [隱私權注意事項 URL]****，然後按一下 [儲存]****。 隱私權注意事項是您要提供給使用者之隱私權資訊的管理頁面。
5. 在 [Web 設定]**** 區段中，複製 [用戶端識別碼]**** 的值。 選取 [顯示密碼]**** 以取得用戶端密碼，然後複製該密碼。 您必須使用這兩個值，將 Amazon 帳戶設為租用戶中的識別提供者。 **用戶端密碼**是重要的安全性認證。
6. 在 [Web 設定]**** 區段中，選取 [編輯]****，然後在 [允許的 JavaScript 原始來源]**** 中，輸入 `https://your-tenant-name.b2clogin.com`，並在 [允許的傳回 URL]**** 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，仍請在輸入租用戶名稱時，全部使用小寫字母。
7. 按一下 [檔案]  。

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將先前記錄的用戶端密碼儲存在 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取頂端功能表中的 [目錄 + 訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
5. 選取 [原則金鑰]****，然後選取 [新增]****。
6. 針對 [選項]**** 選擇 `Manual`。
7. 輸入原則金鑰的 [名稱]****。 例如： `AmazonSecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 在 [祕密]**** 中，輸入您先前記錄的用戶端密碼。
9. 針對 [金鑰使用方法]****，選取 `Signature`。
10. 按一下頁面底部的 [新增]  。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 Amazon 帳戶進行登入，您必須將該帳戶定義成 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Amazon 帳戶新增至原則擴充檔中的 **ClaimsProviders** 元素，將其定義成宣告提供者。


1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. 將 **client_id** 設定為來自應用程式註冊的應用程式識別碼。
5. 儲存檔案。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

現在，您應該已設定原則，所以 Azure AD B2C 知道如何與 Azure AD 目錄進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則]**** 頁面上，選取 [上傳原則]****。
2. 啟用 [覆寫現有的原則]****，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
3. 按一下 [上傳]  。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

目前，識別提供者已設定，但還未出現在任何註冊/登入畫面中。 若要讓它可供使用，您需建立現有範本使用者旅程圖的複本，然後加以修改，讓它也包含 Amazon 識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml** 檔案。
2. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInAmazon` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊/登入畫面上的識別提供者按鈕。 如果您為 Amazon 帳戶新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 在您建立的使用者旅程圖中，尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `AmazonExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 Amazon 帳戶通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 ID 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```

    將 **TechnicalProfileReferenceId** 的值更新成您稍早所建立技術設定檔的識別碼。 例如： `Amazon-OAuth` 。

3. 儲存 TrustFrameworkExtensions.xml** 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

與 Azure AD B2C 的通訊會透過您在 B2C 租使用者中註冊的應用程式進行。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml** 複本，並將它重新命名。 例如，將它重新命名為 SignUpSignInAmazon.xml**。
2. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInAmazon` 。
3. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_amazon`
4. 更新 **DefaultUserJourney** 中 **ReferenceId** 屬性的值，以符合您所建立新使用者旅程圖 (SignUpSignAmazon) 的識別碼。
5. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
6. 確定 [選取應用程式]**** 欄位中已選取您所建立的 Azure AD B2C 應用程式，然後按一下 [立即執行]**** 來進行測試。
