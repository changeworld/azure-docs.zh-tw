---
title: 使用自訂政策使用 Azure AD 帳號設定登入
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中使用自訂原則來設定以 Azure Active Directory 帳戶進行登入。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: be3a7a3ce4ce3a06398436058ea5d4d935ef5a5c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678098"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 Azure Active Directory 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文演示如何使用 Azure 活動目錄 B2C (Azure AD B2C) 中的[自訂策略](custom-policy-overview.md)為 Azure 活動目錄 (Azure AD) 組織的使用者啟用登錄。

## <a name="prerequisites"></a>Prerequisites

完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將所建立的應用程式金鑰儲存在 Azure AD B2C 租用戶中。

1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱篩選器**,然後選擇包含 Azure AD B2C 租戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 **「政策」** 下,選擇**身份體驗框架**。
1. 選擇**策略鍵**,然後選擇 **"添加**"。
1. 針對 [選項]**** 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]****。 例如： `ContosoAppSecret` 。  前置文字`B2C_1A_`在建立時會自動加入金鑰的名稱中,因此在下一個 XML 中的引言B2C_1A_ContosoAppSecret *。*
1. 在 **「秘密**」中,輸入您之前記錄的客戶機密。
1. 針對 [金鑰使用方法]****，選取 `Signature`。
1. 選取 [建立]  。

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

1. 更新 **TechnicalProfile** 元素的識別碼。 此 ID 用於引用策略其他部分`OIDC-Contoso`(例如 )。
1. 更新 **DisplayName** 的值。 這個值會顯示在登入畫面的登入按鈕上。
1. 更新 [描述]**** 的值。
1. Azure AD 使用 OpenID Connect 通訊協定，因此請確定 [通訊協定]**** 的值為 `OpenIdConnect`。
1. 將 **METADATA** 的值設定為 `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`，其中 `tenant-name` 是您的 Azure AD 租用戶名稱。 例如， `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. 將 **client_id** 設定為來自應用程式註冊的應用程式識別碼。
1. 在 **「加密金鑰**」下,將**StorageReferenceId**的值更新為之前創建的策略金鑰的名稱。 例如： `B2C_1A_ContosoAppSecret` 。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

現在，您應該已設定原則，所以 Azure AD B2C 知道如何與 Azure AD 目錄進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則]**** 頁面上，選取 [上傳原則]****。
1. 啟用 [覆寫現有的原則]****，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
1. 按一下 [上傳]  。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時,標識提供程式已設置,但尚未在任何註冊/登錄頁中可用。 要使其可用,請建立現有樣本使用者旅程的重複項,然後對其進行修改,以便它還必須具有 Azure AD 識別提供者:

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml** 檔案。
1. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
1. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
1. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
1. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInContoso` 。

### <a name="display-the-button"></a>顯示按鈕

**聲明提供程式選擇**元素類似於註冊/登錄頁面上的標識提供程式按鈕。 如果您為 Azure AD 新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 尋找在*TrustFramework 擴展.xml*中建立的使用者旅程`Order="1"`中包含的**業務流程步驟**元素。
1. 在 **ClaimsProviderSelections** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `ContosoExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 Azure AD 通訊以接收權杖。 藉由連結 Azure AD 宣告提供者的技術設定檔，將按鈕連結至動作：

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
1. 新增下列 **ClaimsExchange** 元素，請確定用於 **Id** 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    將**技術設定檔參考 Id**的值更新到您之前創建的技術設定檔的**ID。** 例如： `OIDC-Contoso` 。

1. 儲存 TrustFrameworkExtensions.xml** 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

通過您在 B2C 租戶中註冊的應用程式與 Azure AD B2C 的通訊。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml** 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignInContoso.xml*。
1. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInContoso` 。
1. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如： `http://contoso.com/B2C_1A_signup_signin_contoso` 。
1. 更新**預設使用者旅程**中的**參考 Id**屬性的值,以匹配您之前創建的使用者旅程的 ID。 例如,*註冊登入 Contoso*。
1. 儲存變更並上傳檔案。
1. 在 **「自訂策略」** 下,在清單中選擇新策略。
1. 在 **「選擇應用程式**下拉清單」中,選擇之前創建的 Azure AD B2C 應用程式。 例如 *,testapp1*。
1. 立即複製 **「立即運行」終結點**並在專用瀏覽器視窗中打開它,例如,Google Chrome 中的隱身模式或 Microsoft Edge 中的 InPrivate 視窗。 通過在專用瀏覽器視窗中打開,您可以通過不使用任何當前緩存的 Azure AD 認證來測試完整的使用者旅程。
1. 選擇 Azure AD 登入按鈕,例如*Contoso 員工*,然後在 Azure AD 組織租戶中輸入使用者的認證。 系統將要求您授權應用程式,然後輸入您的個人資料資訊。

如果登入過程成功,瀏覽器將重定向`https://jwt.ms`到 ,顯示 Azure AD B2C 返回的權杖的內容。

## <a name="next-steps"></a>後續步驟

使用自定義策略時,有時在開發策略期間對策略進行故障排除時可能需要其他資訊。

為了幫助診斷問題,可以暫時將策略置於"開發人員模式",並使用 Azure 應用程式見解收集日誌。 瞭解如何在[Azure 活動目錄 B2C 中:收集紀錄](troubleshoot-with-application-insights.md)。
