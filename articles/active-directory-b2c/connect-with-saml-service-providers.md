---
title: 將 Azure AD B2C 設定為應用程式的 SAML IdP
title-suffix: Azure AD B2C
description: 如何設定 Azure AD B2C 以提供 SAML 通訊協定判斷提示給您的應用程式（服務提供者）。 Azure AD B2C 將作為 SAML 應用程式的單一身分識別提供者（IdP）。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 7ccc5fe314d49ea65aaa8750937170ab79a8c04f
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77581458"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中註冊 SAML 應用程式

在本文中，您將瞭解如何設定 Azure Active Directory B2C （Azure AD B2C），以作為應用程式的安全性聲明標記語言（SAML）識別提供者（IdP）。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>案例概觀

使用 Azure AD B2C 作為其客戶身分識別和存取管理解決方案的組織，可能需要與身分識別提供者或已設定為使用 SAML 通訊協定進行驗證的應用程式互動。

Azure AD B2C 利用下列兩種方式的其中一種來達到 SAML 互通性：

* 藉由作為身分*識別提供者*（IdP），並使用 SAML 型服務提供者（您的應用程式）來達到單一登入（SSO）
* 藉由作為*服務提供者*（SP），並與以 SAML 為基礎的身分識別提供者（例如 SALESFORCE 和 ADFS）互動

![在左側和 B2C 上以 B2C 作為身分識別提供者的圖表，做為右側的服務提供者](media/saml-identity-provider/saml-idp-diagram-01.jpg)

使用 SAML 摘要說明兩個非專屬的核心案例：

| 狀況 | Azure AD B2C 角色 | 操作方式 |
| -------- | ----------------- | ------- |
| 我的應用程式預期 SAML 判斷提示會完成驗證。 | **Azure AD B2C 作為身分識別提供者（IdP）**<br />Azure AD B2C 會作為應用程式的 SAML IdP。 | 本文。 |
| 我的使用者必須使用與 SAML 相容的身分識別提供者（例如 ADFS、Salesforce 或 Shibboleth）進行單一登入。  | **Azure AD B2C 會作為服務提供者（SP）**<br />當連接到 SAML 識別提供者時，Azure AD B2C 會作為服務提供者。 這是您的應用程式與 SAML 識別提供者之間的同盟 proxy。  | <ul><li>[使用自訂原則，設定以 ADFS 作為 SAML IdP 的登入](identity-provider-adfs2016-custom.md)</li><li>[使用自訂原則來設定使用 Salesforce SAML 提供者進行登入](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Prerequisites

* 完成在 Azure AD B2C 中[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您需要從文章中討論的自訂原則入門套件中的*SocialAndLocalAccounts*自訂原則。
* 安全性聲明標記語言（SAML）通訊協定的基本瞭解。
* 設定為 SAML 服務提供者（SP）的 web 應用程式。 在本教學課程中，您可以使用我們提供的[SAML 測試應用程式][samltest]。

## <a name="components-of-the-solution"></a>解決方案的元件

此案例需要三個主要元件：

* SAML**服務提供者**能夠傳送 saml 要求，以及接收、解碼和回應 AZURE AD B2C 的 saml 判斷提示。 這也稱為信賴憑證者。
* 服務提供者的公開可用 SAML**中繼資料端點**。
* [Azure AD B2C 租用戶](tutorial-create-tenant.md)

如果您還沒有 SAML 服務提供者和相關聯的中繼資料端點，您可以使用我們提供的範例 SAML 應用程式來進行測試：

[SAML 測試應用程式][samltest]

## <a name="1-set-up-certificates"></a>1. 設定憑證

若要在您的服務提供者與 Azure AD B2C 之間建立信任關係，您需要提供 X509 憑證和其私密金鑰。

* **服務提供者憑證**
  * 具有儲存在 Web 應用程式中之私密金鑰的憑證。 您的服務提供者會使用此憑證來簽署傳送給 Azure AD B2C 的 SAML 要求。 Azure AD B2C 從服務提供者中繼資料讀取公開金鑰，以驗證簽章。
  * 選擇性具有儲存在 Web 應用程式中之私密金鑰的憑證。 Azure AD B2C 從服務提供者中繼資料讀取公開金鑰，以加密 SAML 判斷提示。 服務提供者接著會使用私密金鑰來解密判斷提示。
* **Azure AD B2C 憑證**
  * Azure AD B2C 中具有私密金鑰的憑證。 Azure AD B2C 使用此憑證來簽署傳送給您的服務提供者的 SAML 回應。 您的服務提供者會讀取 Azure AD B2C 中繼資料公開金鑰，以驗證 SAML 回應的簽章。

您可以使用公開憑證授權單位單位所發行的憑證，或本教學課程中的自我簽署憑證。

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 準備自我簽署憑證

如果您還沒有憑證，可以在本教學課程中使用自我簽署憑證。 在 Windows 上，您可以使用 PowerShell 的[SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) Cmdlet 來產生憑證。

1. 執行此 PowerShell 命令來產生自我簽署憑證。 針對您的應用程式適當地修改 `-Subject` 引數，並 Azure AD B2C 租使用者名稱。 您也可以調整 `-NotAfter` 日期，為憑證指定不同的到期日。

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. 開啟 **管理使用者憑證** > **目前使用者** > **個人** > **憑證** > *yourappname.yourtenant.onmicrosoft.com*
1. 選取 > **所有**工作 > **匯出**的憑證 >**動作**
1. 選取 [**是**] > **下一步** > **是，匯出私密金鑰** > **下一步**
1. 接受**匯出檔案格式**的預設值
1. 提供憑證的密碼

### <a name="12-upload-the-certificate"></a>1.2 上傳憑證

接下來，將 SAML 判斷提示和回應簽署憑證上傳至 Azure AD B2C。

1. 登入[Azure 入口網站](https://portal.azure.com)，然後流覽至您的 Azure AD B2C 租使用者。
1. 在 [**原則**] 底下，選取 [ **Identity Experience Framework** ] 和 [**原則金鑰**]。
1. 選取 **新增**，然後選取 **選項** > **上傳**。
1. 輸入**名稱**，例如*SamlIdpCert*。 金鑰名稱前面會自動新增前置詞 B2C_1A_。
1. 使用上傳檔案控制項上傳您的憑證。
1. 輸入憑證的密碼。
1. 選取 [建立]。
1. 確認金鑰如預期般出現。 例如， *B2C_1A_SamlIdpCert*。

## <a name="2-prepare-your-policy"></a>2. 準備您的原則

### <a name="21-create-the-saml-token-issuer"></a>2.1 建立 SAML 權杖簽發者

現在，為您的租使用者新增功能以發行 SAML 權杖。

開啟自訂原則入門套件中的 `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** 。

找出 [`<ClaimsProviders>`] 區段，並新增下列 XML 程式碼片段。

您可以變更 `IssuerUri` 中繼資料的值。 這是 Azure AD B2C 的 SAML 回應中所傳回的簽發者 URI。 您的信賴憑證者應用程式應該設定為在 SAML 判斷提示驗證期間接受簽發者 URI。

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. 新增 SAML 信賴憑證者原則

現在，您的租使用者可以發出 SAML 判斷提示，您必須建立 SAML 信賴憑證者原則，並修改使用者旅程圖，讓它發出 SAML 判斷提示，而不是 JWT。

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 建立註冊或登入原則

1. 在您的入門套件工作目錄中建立*signuporsignin.xml*的複本，並以新的名稱儲存。 例如， *SignUpOrSigninSAML*。 這是您的信賴憑證者原則檔案。

1. 在您慣用的編輯器中開啟*SignUpOrSigninSAML。*

1. 將原則的 `PolicyId` 和 `PublicPolicyUri` 變更為_B2C_1A_signup_signin_saml_和 `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml`，如下所示。

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. 在 `<RelyingParty>` 元素之前加入下列 XML 程式碼片段。 此 XML 會覆寫_signuporsignin.xml_使用者旅程圖的協調流程步驟號碼7。 如果您從入門套件中的不同資料夾啟動，或藉由新增或移除協調流程步驟來自訂您的使用者旅程圖，請確定數位（在 `order` 元素中）與權杖簽發者步驟的使用者旅程圖中所指定的數目一致（例如，在其他入門套件資料夾中，`LocalAccounts`為6，針對 `SocialAccounts`，9則用於 `SocialAndLocalAccountsWithMfa`）。

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. 使用下列技術設定檔 XML 來取代 `<RelyingParty>` 元素中的整個 `<TechnicalProfile>` 元素。

    ```XML
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. 以您的 Azure AD B2C 租使用者名稱更新 `tenant-name`。

您的最終信賴憑證者原則檔案看起來應該如下所示：

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 上傳並測試您的原則中繼資料

儲存您的變更，並上傳新的原則檔案。 在您上傳這兩個原則（延伸模組和信賴憑證者檔案）之後，請開啟網頁瀏覽器並流覽至原則中繼資料。

Azure AD B2C 原則 IDP 中繼資料是 SAML 通訊協定中用來公開 SAML 識別提供者設定的資訊。 中繼資料會定義服務的位置，例如，登入和登出、憑證、登入方法，以及其他項目。 Azure AD B2C 的原則中繼資料可在下列 URL 取得。 以您 Azure AD B2C 的租使用者名稱取代 `tenant-name`，並以原則的名稱（ID）取代 `policy-name`：

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

您的自訂原則和 Azure AD B2C 租使用者現在已準備就緒。 接下來，在 Azure AD B2C 中建立應用程式註冊。

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. 在 Azure AD B2C 目錄中設定應用程式

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 在 Azure Active Directory 中註冊您的應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊 (預覽)]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如， *SAMLApp1*。
1. 在 [**支援的帳戶類型**] 底下，選取 [**僅此組織目錄中的帳戶**]
1. 在 [重新**導向 URI**] 底下，選取 [ **Web**]，然後輸入 `https://localhost`。 您稍後會在應用程式註冊的資訊清單中修改此值。
1. 選取 **[授與系統管理員同意 openid 和 offline_access 許可權**]。
1. 選取 [註冊]。

### <a name="42-update-the-app-manifest"></a>4.2 更新應用程式資訊清單

針對 SAML 應用程式，您需要在應用程式註冊的資訊清單中設定數個屬性。

1. 在  [Azure 入口網站](https://portal.azure.com)中，流覽至您在上一節中建立的應用程式註冊。
1. 在 [**管理**] 底下，選取 [**資訊清單**] 以開啟資訊清單編輯器。 您會在下列各節中修改數個屬性。

#### <a name="identifieruris"></a>identifierUris

`identifierUris` 是包含使用者定義 URI 的字串集合，可在其 Azure AD B2C 租使用者內唯一識別 Web 應用程式。 您的服務提供者必須在 SAML 要求的 `Issuer` 元素中設定此值。

#### <a name="samlmetadataurl"></a>samlMetadataUrl

這個屬性代表服務提供者的公開可用中繼資料 URL。 中繼資料 URL 可以指向上傳至任何可匿名存取的端點（例如 blob 儲存體）的中繼資料檔案。

中繼資料是 SAML 通訊協定中用來公開 SAML 合作物件（例如服務提供者）設定的資訊。 中繼資料會定義服務的位置，例如登入和登出、憑證、登入方法等等。 Azure AD B2C 會讀取服務提供者中繼資料，並據以採取動作。 中繼資料不是必要的。 您也可以直接在應用程式資訊清單中指定一些屬性，例如 [回復 URI] 和 [登出 URI]。

如果 SAML 中繼資料 URL 和應用程式註冊的指令*清單中都*指定了屬性，則會**合併**它們。 中繼資料 URL 中指定的屬性會先進行處理，並優先使用。

針對使用 SAML 測試應用程式的本教學課程，請針對 `samlMetadataUrl`使用下列值：

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType （選擇性）

如果您未提供中繼資料 URI，可以明確指定回復 URL。 這個選擇性屬性代表服務提供者中繼資料中的 `AssertionConsumerServiceUrl` （`SingleSignOnService` URL），而 `BindingType` 則假設為 `HTTP POST`。

如果您選擇在應用程式資訊清單中設定 [回復 URL] 和 [登出 URL]，而不使用服務提供者中繼資料，Azure AD B2C 將不會驗證 SAML 要求籤章，也不會加密 SAML 回應。

在此教學課程中，您會使用 SAML 測試應用程式，將 `replyUrlsWithType` 的 `url` 屬性設定為下列 JSON 程式碼片段中所示的值。

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl （選擇性）

此選擇性屬性代表信賴憑證者中繼資料中的 `Logout` URL （`SingleLogoutService` URL），而此的 `BindingType` 假設為 `Http-Redirect`。

在使用 SAML 測試應用程式的本教學課程中，請將 `logoutUrl` 設定為 `https://samltestapp2.azurewebsites.net/logout`：

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. 更新您的應用程式代碼

最後一個步驟是將 Azure AD B2C 啟用為 SAML 信賴憑證者應用程式中的 SAML IdP。 每個應用程式都不同，而執行這項操作的步驟則有所不同。 如需詳細資訊，請參閱應用程式的檔。

通常需要下列部分或全部：

* **中繼資料**： `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **簽發者**： `https://tenant-name.onmicrosoft.com/policy-name`
* **登入 url/saml 端點/Saml Url**：檢查中繼資料檔案中的值
* **憑證**：這是*B2C_1A_SamlIdpCert*，但不含私密金鑰。 若要取得憑證的公開金鑰：

    1. 移至上面指定的中繼資料 URL。
    1. 複製 `<X509Certificate>` 元素中的值。
    1. 將它貼到文字檔中。
    1. 將文字檔儲存為 *.cer*檔案。

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 使用 SAML 測試應用程式進行測試（選擇性）

若要使用我們的[SAML 測試應用程式][samltest]來完成本教學課程：

* 更新租使用者名稱
* 更新原則名稱，例如*B2C_1A_signup_signin_saml*
* 指定此簽發者 URI： `https://contoso.onmicrosoft.com/app-name`

選取 **[登**入]，您應該會看到終端使用者的登入畫面。 登入時，會將 SAML 判斷提示回傳給範例應用程式。

## <a name="sample-policy"></a>範例原則

我們會提供完整的範例原則，供您用來搭配 SAML 測試應用程式進行測試。

1. 下載[SAML SP 起始的登入範例原則](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. 更新 `TenantId` 以符合您的租使用者名稱，例如*contoso.b2clogin.com*
1. 保留原則名稱*B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>支援和不支援的 SAML 形式

下列 SAML 信賴憑證者（RP）案例可透過您自己的中繼資料端點來支援：

* 應用程式/服務主體物件中登出 URL 的多個登出 Url 或 POST 系結。
* 指定簽署金鑰以驗證應用程式/服務主體物件中的 RP 要求。
* 在應用程式/服務主體物件中指定權杖加密金鑰。
* 預覽版本目前不支援識別提供者起始的登入。

## <a name="next-steps"></a>後續步驟

您可以[在 OASIS 網站上找到有關 SAML 通訊協定](https://www.oasis-open.org/)的詳細資訊。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
