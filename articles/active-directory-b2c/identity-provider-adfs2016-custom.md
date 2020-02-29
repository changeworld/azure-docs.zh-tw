---
title: 使用自訂原則新增 ADFS 作為 SAML 識別提供者
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中使用 SAML 通訊協定和自訂原則來設定 ADFS 2016
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f331a537c80628a386525e29743807a70a163f0d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914315"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則將 ADFS 新增為 SAML 識別提供者

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何使用 Azure Active Directory B2C （Azure AD B2C）中的[自訂原則](custom-policy-overview.md)，啟用 ADFS 使用者帳戶的登入。 將 [SAML 技術設定檔](saml-technical-profile.md)新增至自訂原則，以啟用登入。

## <a name="prerequisites"></a>Prerequisites

- 完成在 [Azure Active Directory B2C 中開始使用自訂原則](custom-policy-get-started.md)中的步驟。
- 確定您可以存取具有私密金鑰的憑證 .pfx 檔案。 您可以產生自己簽署的憑證，並將它上傳至 Azure AD B2C。 Azure AD B2C 會使用此憑證簽署傳送給您的 SAML 身分識別提供者的 SAML 要求。
- 為了讓 Azure 接受 .pfx 檔案密碼，密碼必須使用 Windows 憑證存放區匯出公用程式中的 TripleDES-SHA1 選項來加密，而不是 AES256-SHA256。

## <a name="create-a-policy-key"></a>建立原則金鑰

您需要將憑證儲存至 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 請確定您使用的是包含您 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您租使用者的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 在 [概觀] 頁面上，選取 [識別體驗架構]。
5. 選取 [原則金鑰]，然後選取 [新增]。
6. 針對 [選項] 選擇 `Upload`。
7. 輸入原則金鑰的 [名稱]。 例如： `SamlCert` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 瀏覽至包含私密金鑰的憑證 .pfx 檔案，並選取該檔案。
9. 按一下 **[建立]** 。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 ADFS 帳戶進行登入，您必須將該帳戶定義為 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由將 ADFS 帳戶新增至原則擴充檔中的 **ClaimsProviders** 元素，將其定義成宣告提供者。 如需詳細資訊，請參閱[定義 SAML 技術設定檔](saml-technical-profile.md)。

1. 開啟 *TrustFrameworkExtensions.xml*。
1. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
1. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 以 ADFS 網域的名稱取代 `your-ADFS-domain`，並以 DNS (指出網域的任意值) 取代 **identityProvider** 輸出宣告。

1. 找出 [`<ClaimsProviders>`] 區段，並新增下列 XML 程式碼片段。 如果您的原則已包含 `SM-Saml-idp` 技術設定檔，請跳至下一個步驟。 如需詳細資訊，請參閱[單一登入會話管理](custom-policy-reference-sso.md)。

    ```XML
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 儲存檔案。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

到目前為止，您已設定原則，讓 Azure AD B2C 知道如何與 ADFS 帳戶進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
2. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
3. 按一下 [上傳]。

> [!NOTE]
> Visual Studio code B2C 延伸模組會使用 "socialIdpUserId"。 ADFS 也需要社交原則。
>

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，識別提供者已設定妥當，但還未出現在任何註冊或登入畫面中。 若要讓它可供使用，您需建立現有範本使用者旅程圖的複本，然後加以修改，讓它也包含 ADFS 識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml 檔案。
2. 尋找並複製包含 **之**UserJourney`Id="SignUpOrSignIn"` 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInADFS` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊或登入畫面上的識別提供者按鈕。 如果您為 ADFS 帳戶新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 在您建立的使用者旅程圖中，尋找包含 **的**OrchestrationStep`Order="1"` 元素。
2. 在 **ClaimsProviderSelections** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `ContosoExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是讓 Azure AD B2C 與 ADFS 帳戶通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 **的**OrchestrationStep`Order="2"`。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 ID 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    將 **TechnicalProfileReferenceId** 的值更新成您稍早所建立技術設定檔的識別碼。 例如： `Contoso-SAML2` 。

3. 儲存 TrustFrameworkExtensions.xml 檔案，並再次上傳它以供驗證。


## <a name="configure-an-adfs-relying-party-trust"></a>設定 ADFS 信賴憑證者信任

若要在 Azure AD B2C 中使用 ADFS 作為識別提供者，您必須使用 Azure AD B2C SAML 中繼資料建立 ADFS 信賴憑證者信任。 下列範例顯示 Azure AD B2C 技術設定檔 SAML 中繼資料的 URL 位址：

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

取代下列值：

- 將 **your-tenant** 取代為您的租用戶名稱，例如 your-tenant.onmicrosoft.com。
- 將 **your-policy** 取代為您的原則名稱。 例如，B2C_1A_signup_signin_adfs。
- **您的技術設定檔**，其中包含您的 SAML 身分識別提供者技術設定檔的名稱。 例如，Contoso-SAML2。

開啟瀏覽器並瀏覽至此 URL。 請確定您輸入正確的 URL，而且您可以存取 XML 中繼資料檔案。 若要使用 ADFS 管理嵌入式管理單元來新增新的信賴憑證者信任並手動進行設定，請在同盟伺服器上執行下列程序。 必須至少具有本機電腦上的**管理員**或同等成員資格，才能完成此程序。

1. 在 [伺服器管理員] 中選取 [工具]，然後選取 [ADFS 管理]。
2. 選取 [新增信賴憑證者信任]。
3. 在 [歡迎] 頁面上選擇 [宣告感知]，然後按一下 [開始]。
4. 在 [選取資料來源] 頁面上，選取 [匯入線上或區域網路上發行的信賴憑證者相關資料]，提供您的 Azure AD B2C 中繼資料 URL，然後按 [下一步]。
5. 在 [指定顯示名稱] 頁面上輸入**顯示名稱**、在 [備忘稿] 下輸入此信賴憑證者信任的描述，然後按 [下一步]。
6. 在 [選擇存取控制原則] 頁面上選擇原則，然後按 [下一步]。
7. 在 [準備新增信任] 頁面上檢閱設定，然後按 [下一步] 以儲存您的信賴憑證者信任資訊。
8. 在 [完成] 頁面上按一下 [關閉]，此動作就會自動顯示 [編輯宣告規則] 對話方塊。
9. 選取 [新增規則]。
10. 在 [宣告規則範本] 中，選取 [傳送 LDAP 屬性作為宣告]。
11. 提供**宣告規則名稱**。 針對 [屬性存放區]，選取 [選取 Active Directory] 來新增下列宣告，然後按一下 [完成] 和 [確定]。

    | LDAP 屬性 | 傳出宣告類型 |
    | -------------- | ------------------- |
    | 使用者主體名稱 | userPrincipalName |
    | Surname | family_name |
    | 指定的名稱 | given_name |
    | 電子郵件地址 | 電子郵件 |
    | 顯示名稱 | NAME |

    請注意，這些名稱不會顯示在 [傳出宣告類型] 下拉式清單中。 您必須在中手動輸入它們。 （下拉式清單實際上是可編輯的）。

12.  根據不同憑證類型，您可能需要設定雜湊演算法。 在信賴憑證者信任 (B2C 示範) 屬性視窗上，選取 [進階] 索引標籤、將 [安全雜湊演算法] 變更為 `SHA-256`，然後按一下 [確定]。
13. 在 [伺服器管理員] 中選取 [工具]，然後選取 [ADFS 管理]。
14. 選取您建立的信賴憑證者信任，並選取 [從同盟中繼資料中更新]，然後按一下 [更新]。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

與 Azure AD B2C 的通訊會透過您在 B2C 租使用者中註冊的應用程式進行。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml 複本，並將它重新命名。 例如，將它重新命名為 SignUpSignInADFS.xml。
2. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInADFS` 。
3. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_adfs`
4. 更新 **DefaultUserJourney** 中 **ReferenceId** 屬性的值，以符合您所建立新使用者旅程圖 (SignUpSignInADFS) 的識別碼。
5. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
6. 確定 [選取應用程式] 欄位中已選取您所建立的 Azure AD B2C 應用程式，然後按一下 [立即執行] 來進行測試。

