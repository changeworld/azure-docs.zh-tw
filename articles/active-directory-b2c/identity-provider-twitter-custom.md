---
title: 使用自訂策略使用 Twitter 帳戶設置登錄
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中使用自訂原則來設定以 Twitter 帳戶進行登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5804ded875ef03d7ade4414eb8f08885634748dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051603"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 Twitter 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介紹如何使用 Azure 活動目錄 B2C （Azure AD B2C） 中的[自訂策略](custom-policy-overview.md)為 Twitter 帳戶的使用者啟用登錄。

## <a name="prerequisites"></a>Prerequisites

- 完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。
- 如果您還沒有 Twitter 帳戶，請在 [Twitter 註冊頁面](https://twitter.com/signup)建立一個帳戶。

## <a name="create-an-application"></a>建立應用程式

若要使用 Twitter 作為 Azure AD B2C 中的識別提供者，您需要建立 Twitter 應用程式。

1. 使用您的 Twitter 帳戶認證登入 [Twitter 開發人員](https://developer.twitter.com/en/apps) \(英文\) 網站。
2. 選擇 **"創建應用**"。
3. 輸入**應用程式名稱**和**應用程式說明**。
4. 在 [Website URL]**** \(網站 URL\) 中，輸入 `https://your-tenant.b2clogin.com`。 以您的租用戶名稱取代 `your-tenant`。 例如： `https://contosob2c.b2clogin.com` 。
5. 在 [回呼 URL]**** 中輸入 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`。 將 `your-tenant` 取代為您的租用戶名稱，並將 `your-policy-Id` 取代為您的原則識別碼。 例如： `b2c_1A_signup_signin_twitter` 。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，您還是需要在輸入租用戶名稱時，全部使用小寫字母。
6. 在頁面底部，閱讀並接受條款，然後選取 [Create]**** \(建立\)。
7. 在 [App details]**** \(應用程式詳細資料\) 頁面上，選取 [Edit] \(編輯\) > [Edit details] \(編輯詳細資料\)****、核取 [Enable Sign in with Twitter]**** \(使用 Twitter 啟用登入\) 的方塊，然後選取 [Save]**** \(儲存\)。
8. 選取 [Keys and tokens]**** \(金鑰和權杖\)，並記錄**取用者 API 金鑰**和**取用者 API 祕密金鑰**的值，以供稍後使用。

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將先前記錄的祕密金鑰儲存在 Azure AD B2C 租用戶中。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇包含租戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
5. 選取 [原則金鑰]****，然後選取 [新增]****。
6. 針對 [選項]**** 選擇 `Manual`。
7. 輸入原則金鑰的 [名稱]****。 例如： `TwitterSecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 在 [祕密]**** 中，輸入您先前記錄的用戶端密碼。
9. 針對 [金鑰使用方法]****，選取 `Encryption`。
10. 按一下 **[建立]**。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 Twitter 帳戶進行登入，您必須將該帳戶定義成 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 Twitter 帳戶新增至原則擴充檔中的 **ClaimsProviders** 元素，將其定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. 以您先前記錄的取用者金鑰取代 **client_id** 的值。
5. 儲存檔案。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

到目前為止，您已經配置了策略，以便 Azure AD B2C 知道如何與 Twitter 帳戶進行通信。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則]**** 頁面上，選取 [上傳原則]****。
2. 啟用 [覆寫現有的原則]****，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
3. 按一下 [上傳]****。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，識別提供者已設定妥當，但還未出現在任何註冊或登入畫面中。 若要讓它可供使用，您必須建立現有範本使用者旅程圖的複本，然後修改它，讓它也包含 Twitter 識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml** 檔案。
2. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInTwitter` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊或登入畫面上的識別提供者按鈕。 如果您為 Twitter 帳戶新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 在您建立的使用者旅程圖中，尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `TwitterExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是用來讓 Azure AD B2C 與 Twitter 帳戶進行通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 ID 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    將 **TechnicalProfileReferenceId** 的值更新成您稍早所建立技術設定檔的識別碼。 例如： `Twitter-OAUTH1` 。

3. 儲存 TrustFrameworkExtensions.xml** 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

通過您在 B2C 租戶中註冊的應用程式與 Azure AD B2C 的通信。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml** 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignInTwitter.xml*。
2. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInTwitter` 。
3. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_twitter`
4. 更新 **DefaultUserJourney** 中 **ReferenceId** 屬性的值，以符合您所建立新使用者旅程圖 (SignUpSignTwitter) 的識別碼。
5. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
6. 確定 [選取應用程式]**** 欄位中已選取您所建立的 Azure AD B2C 應用程式，然後按一下 [立即執行]**** 來進行測試。
