---
title: 將 Azure AD B2C 設定為應用程式的 SAML IdP
title-suffix: Azure AD B2C
description: 如何設定 Azure AD B2C 以提供 SAML 通訊協定判斷提示給您的應用程式 (服務提供者)。 Azure AD B2C 將作為 SAML 應用程式的單一識別提供者 (IdP)。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 7c6ba79a82fe3d291008f3317ddce7df4adcda0a
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309642"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中註冊 SAML 應用程式

在本文中，您將了解如何將 Azure Active Directory B2C (Azure AD B2C) 設定為應用程式的安全性聲明標記語言 (SAML) 識別提供者 (IdP)。

## <a name="scenario-overview"></a>案例概觀

使用 Azure AD B2C 作為其客戶身分識別和存取管理解決方案的組織，可能需要與身分識別提供者或已設定為使用 SAML 通訊協定進行驗證的應用程式互動。

Azure AD B2C 利用下列兩種方式的其中一種來達到 SAML 互通性：

* 作為「識別提供者」(IdP)，並使用 SAML 型服務提供者 (您的應用程式) 來達到單一登入 (SSO)
* 作為「服務提供者」(SP)，並與 SAML 型識別提供者 (例如 Salesforce 和 ADFS) 互動

![左側是 B2C 作為識別提供者，右側是 B2C 作為服務提供者的圖表](media/saml-identity-provider/saml-idp-diagram-01.jpg)

摘要說明使用 SAML 的兩個非專屬核心案例：

| 狀況 | Azure AD B2C 角色 | 操作方式 |
| -------- | ----------------- | ------- |
| 我的應用程式預期使用 SAML 判斷提示來完成驗證。 | **Azure AD B2C 作為識別提供者 (IdP)**<br />Azure AD B2C 會作為應用程式的 SAML IdP。 | 本文。 |
| 我的使用者必須使用與 SAML 相容的識別提供者 (例如 ADFS、Salesforce 或 Shibboleth) 進行單一登入。  | **Azure AD B2C 作為服務提供者 (SP)**<br />當連線到 SAML 識別提供者時，Azure AD B2C 會作為服務提供者。 這是您應用程式與 SAML 識別提供者之間的同盟 Proxy。  | <ul><li>[使用自訂原則來設定以 ADFS 作為 SAML IdP 進行登入](identity-provider-adfs2016-custom.md)</li><li>[使用自訂原則來設定以 Salesforce SAML 提供者進行登入](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Prerequisites

* 完成[開始在 Azure AD B2C 中使用自訂原則](custom-policy-get-started.md)中的步驟。 您需要 SocialAndLocalAccounts 自訂原則，您可以從文章中討論的自訂原則入門套件中取得。
* 安全性聲明標記語言 (SAML) 通訊協定的基本了解。
* 設定為 SAML 服務提供者 (SP) 的 Web 應用程式。 在本教學課程中，您可以使用我們提供的 [SAML 測試應用程式][samltest]。

## <a name="components-of-the-solution"></a>解決方案的元件

此案例需要三個主要元件：

* SAML **服務提供者**，其能夠傳送 SAML 要求，以及從 Azure AD B2C 接收、解碼和回應 SAML 判斷提示。 服務提供者也稱為信賴憑證者應用程式。
* 公開可用的 SAML **中繼資料端點**，以提供給您的服務提供者使用。
* [Azure AD B2C 租用戶](tutorial-create-tenant.md)

如果您還沒有 SAML 服務提供者和相關聯的中繼資料端點，您可以使用我們基於測試用途提供的 SAML 應用程式範例：

[SAML 測試應用程式][samltest]

## <a name="1-set-up-certificates"></a>1.設定憑證

若要在您的服務提供者與 Azure AD B2C 之間建立信任關係，您必須提供 Web 應用程式 X509 憑證。

* **服務提供者憑證**
  * 將私密金鑰儲存在 Web 應用程式中的憑證。 您的服務提供者會使用此憑證來簽署傳送給 Azure AD B2C 的 SAML 要求。 Azure AD B2C 會從服務提供者中繼資料讀取公開金鑰，以驗證簽章。
  * (選擇性) 將私密金鑰儲存在 Web 應用程式中的憑證。 Azure AD B2C 會從服務提供者中繼資料讀取公開金鑰，以加密 SAML 判斷提示。 服務提供者接著會使用私密金鑰來解密判斷提示。
* **Azure AD B2C 憑證**
  * 私密金鑰在 Azure AD B2C 中的憑證。 Azure AD B2C 會使用此憑證來簽署傳送給您服務提供者的 SAML 回應。 您的服務提供者會讀取 Azure AD B2C 中繼資料公開金鑰，以驗證 SAML 回應的簽章。

您可以使用公開憑證授權單位單位所發行的憑證，或如本教學課程使用自我簽署憑證。

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 準備自我簽署憑證

如果您還沒有憑證，可以在本教學課程中使用自我簽署憑證。 在 Windows 上，您可以使用 PowerShell 的 [SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) Cmdlet 來產生憑證。

1. 執行此 PowerShell 命令來產生自我簽署憑證。 針對您的應用程式和 Azure AD B2C 租用戶名稱，適當地修改 `-Subject` 引數。 您也可以調整 `-NotAfter` 日期，為憑證指定不同的到期日。

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

1. 開啟 [管理使用者憑證] > [目前使用者] > [個人] > [憑證] > [yourappname.yourtenant.onmicrosoft.com]
1. 選取憑證 > [動作] > [所有工作] > [匯出]
1. 選取 [是] > [下一步] > [是，匯出私密金鑰] > [下一步]
1. 接受 [匯出檔案格式] 的預設值
1. 提供憑證的密碼

### <a name="12-upload-the-certificate"></a>1.2 上傳憑證

接下來，將 SAML 判斷提示和回應簽署憑證上傳至 Azure AD B2C。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至您的 Azure AD B2C 租用戶。
1. 在 [原則] 中，選取 [Identity Experience Framework]，然後選取 [原則金鑰]。
1. 選取 [新增]，然後選取 [選項] > [上傳]。
1. 輸入 **名稱** ，例如 SamlIdpCert。 金鑰名稱前面會自動新增前置詞 B2C_1A_。
1. 使用上傳檔案控制項來上傳您的憑證。
1. 輸入憑證的密碼。
1. 選取 [建立]。
1. 確認金鑰如預期般出現。 例如 B2C_1A_SamlIdpCert。

## <a name="2-prepare-your-policy"></a>2.準備您的原則

### <a name="21-create-the-saml-token-issuer"></a>2.1 建立 SAML 權杖簽發者

現在，使用 [SAML 權杖簽發者](saml-issuer-technical-profile.md)和 [SAML 工作階段提供者](custom-policy-reference-sso.md#samlssosessionprovider)技術設定檔，為您的租用戶新增用來發行 SAML 權杖的功能。

在自訂原則入門套件中開啟 `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** 。

找出 [`<ClaimsProviders>`] 區段，並新增下列 XML 程式碼片段。

您可以變更 `IssuerUri` 中繼資料的值。 這是簽發者 URI，會在 Azure AD B2C 的 SAML 回應中傳回。 您的信賴憑證者應用程式應該設定為在 SAML 判斷提示驗證期間接受簽發者 URI。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3.新增 SAML 信賴憑證者原則

現在，您的租用戶已可以發出 SAML 判斷提示，接著您必須建立 SAML 信賴憑證者原則，並修改使用者旅程圖，讓其發出 SAML 判斷提示，而不是 JWT。

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 建立註冊或登入原則

1. 在您的入門套件工作目錄中建立 SignUpOrSignin.xml 檔案的複本，並以新的名稱儲存。 例如 SignUpOrSigninSAML.xml。 這是您的信賴憑證者原則檔案。

1. 在您慣用的編輯器中開啟 SignUpOrSigninSAML.xml 檔案。

1. 將原則的 `PolicyId` 和 `PublicPolicyUri` 變更為 _B2C_1A_signup_signin_saml_ 和 `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml`，如下所示。

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. 在 `<RelyingParty>` 元素之前加入下列 XML 程式碼片段。 此 XML 會覆寫 _SignUpOrSignIn_ 使用者旅程圖的協調流程步驟 7。 如果您從入門套件中的不同資料夾啟動，或藉由新增或移除協調流程步驟來自訂您的使用者旅程圖，請確定該數字 (在 `order` 元素中) 與使用者旅程圖中為權杖簽發者步驟指定的數字一致 (例如，在其他入門套件資料夾中，`LocalAccounts` 是數字 4、`SocialAccounts` 是數字 6，而 `SocialAndLocalAccountsWithMfa` 是數字 9)。

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. 使用下列技術設定檔 XML 來取代 `<RelyingParty>` 元素中的整個 `<TechnicalProfile>` 元素。

    ```xml
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

1. 將 `tenant-name` 更新為您的 Azure AD B2C 租用戶名稱。

您最終的信賴憑證者原則檔案看起來應該類似下列 XML 程式碼：

```xml
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

> [!NOTE]
> 當您在執行其他類型的使用者流程 (例如登入、密碼重設或設定檔編輯) 時，此程式基本上與本節所述的相同。 在上述步驟4中，您會將使用者旅程的最後一個步驟從變更 `JWTIssuer` 為 `Saml2AssertionIssuer` 。 在上述的步驟6中，您會在 [信賴憑證者] 區段中，將 **通訊協定** 從變更 `OpenIdConnect` 為 `SAML2` 。

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 上傳並測試您的原則中繼資料

儲存您的變更，並上傳新的原則檔案。 在您上傳這兩個原則 (擴充和信賴憑證者檔案) 之後，請開啟網頁瀏覽器並瀏覽至原則中繼資料。

Azure AD B2C 原則 IDP 中繼資料是 SAML 通訊協定中用來公開 SAML 識別提供者設定的資訊。 中繼資料會定義服務的位置，例如，登入和登出、憑證、登入方法，以及其他項目。 Azure AD B2C 原則中繼資料可在下列 URL 取得。 `tenant-name`請以您 Azure AD B2C 租使用者的名稱取代，並 `policy-name` 以原則的名稱 (識別碼) 例如 .../B2C_1A_signup_signin_saml/samlp/metadata：

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

您的自訂原則和 Azure AD B2C 租用戶現在已準備就緒。 接下來，請在 Azure AD B2C 中建立應用程式註冊。

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4.在 Azure AD B2C 目錄中設定應用程式

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4.1 在 Azure AD B2C 中註冊您的應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如 SAMLApp1。
1. 在 [支援的帳戶類型] 區段中，選取 [僅限此組織目錄中的帳戶]
1. 在 [重新導向 URI] 底下，選取 [Web]，然後輸入 `https://localhost`。 您稍後會在應用程式註冊的資訊清單中修改此值。
1. 選取 [註冊]。

### <a name="42-update-the-app-manifest"></a>4.2 更新應用程式資訊清單

針對 SAML 應用程式，您需要在應用程式註冊的資訊清單中設定數個屬性。

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您在上一節中建立的應用程式註冊。
1. 在 [管理] 底下選取 [資訊清單]，以開啟資訊清單編輯器。 您會在下列各節中修改數個屬性。

#### <a name="identifieruris"></a>identifierUris

`identifierUris` 是包含使用者定義 URI 的字串集合，這些 URI 可在其 Azure AD B2C 租用戶中識別唯一的 Web 應用程式。 URI 必須符合 SAML 要求的 `Issuer` 名稱。 使用者定義的 URI 通常與服務提供者中繼資料的值相同 `entityID` 。

#### <a name="samlmetadataurl"></a>samlMetadataUrl

此屬性代表服務提供者的公用中繼資料 URL。 中繼資料 URL 可以指向已上傳至任何可匿名存取端點 (例如 Blob 儲存體) 的中繼資料檔案。

中繼資料是在 SAML 通訊協定中用來公開 SAML 合作對象設定 (例如服務提供者) 的資訊。 中繼資料會定義服務的位置，例如，登入和登出、憑證、登入方法，以及其他服務。 Azure AD B2C 會讀取服務提供者中繼資料，並據以採取動作。 中繼資料不是必要的。 您也可以直接在應用程式資訊清單中指定一些屬性，例如回覆 URI 和登出 URI。

如果 SAML 中繼資料 URL 和應用程式註冊的資訊清單中「都」指定了某些屬性，則這些 **屬性** 會合併。 中繼資料 URL 中指定的屬性會先進行處理，並優先使用。

在使用 SAML 測試應用程式的本教學課程中，請針對 `samlMetadataUrl` 使用下列值：

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (選擇性)

如果您未提供中繼資料 URI，您可以明確指定回覆 URL。 這個選擇性屬性代表 `AssertionConsumerServiceUrl` (服務提供者中繼資料中的 `SingleSignOnService` URL)，而 `BindingType` 則假設為 `HTTP POST`。

如果您選擇在應用程式資訊清單中設定回覆 URL 和登出 URL，而不使用服務提供者中繼資料，Azure AD B2C 將不會驗證 SAML 要求籤章，也不會加密 SAML 回應。

在此教學課程中，您會使用 SAML 測試應用程式，將 `replyUrlsWithType` 的 `url` 屬性設定為下列 JSON 程式碼片段中所示的值。

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (選擇性)

此選擇性屬性代表 `Logout` URL (信賴憑證者中繼資料中的 `SingleLogoutService` URL)，而這裡的 `BindingType` 會假設為 `Http-Redirect`。

在使用 SAML 測試應用程式的本教學課程中，請維持將 `logoutUrl` 設定為 `https://samltestapp2.azurewebsites.net/logout`：

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5.更新應用程式的程式碼

最後一個步驟是將 Azure AD B2C 啟用為 SAML 信賴憑證者應用程式中的 SAML IdP。 每個應用程式都不同，而執行這項操作的步驟也會有所不同。 如需詳細資訊，請參閱您的應用程式文件。

中繼資料可以設定為服務提供者中的「靜態中繼資料」或「動態中繼資料」。 在靜態模式中，您可以從 Azure AD B2C 的原則中繼資料複製所有或部分中繼資料。 在動態模式中，您會將 URL 設定為中繼資料，並讓應用程式動態讀取中繼資料。

通常會需要下列部分或全部的項目：

* **中繼資料**：`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **簽發者**： SAML 要求 `issuer` 值必須符合 `identifierUris` 應用程式註冊資訊清單的元素中所設定的其中一個 uri。 如果專案 `issuer` 中沒有 SAML 要求名稱 `identifierUris` ，請 [將它新增至應用程式註冊資訊清單](#identifieruris)。 例如： `https://contoso.onmicrosoft.com/app-name` 。 
* **登入 Url/saml 端點/Saml url**：檢查 XML 元素 Azure AD B2C SAML 原則中繼資料檔案中的值 `<SingleSignOnService>`
* **憑證**：這是 B2C_1A_SamlIdpCert，但沒有私密金鑰。 若要取得憑證的公開金鑰：

    1. 移至上面指定的中繼資料 URL。
    1. 複製 `<X509Certificate>` 元素中的值。
    1. 將其貼到文字檔中。
    1. 將文字檔儲存為 *.cer* 檔案。

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 使用 SAML 測試應用程式進行測試 (選擇性)

若要使用我們的 [SAML 測試應用程式][samltest]來完成本教學課程：

* 更新租用戶名稱
* 更新原則名稱，例如 B2C_1A_signup_signin_saml
* 指定此簽發者 URI。 `identifierUris`例如，使用在應用程式註冊資訊清單的元素中找到的其中一個 uri `https://contoso.onmicrosoft.com/app-name` 。

選取 [登入]，您應該會看到使用者登入畫面。 登入時，SAML 判斷提示會回傳給範例應用程式。

## <a name="enable-encrypted-assertions-optional"></a>啟用加密的判斷提示 (選擇性) 

若要加密傳回給服務提供者的 SAML 判斷提示，Azure AD B2C 將會使用服務提供者公開金鑰憑證。 公開金鑰必須存在於上述 ["samlMetadataUrl"](#samlmetadataurl) 中所述的 SAML 中繼資料中，做為使用 ' Encryption ' 的 >keydescriptor。

下列 XML 程式碼是使用 set to Encryption >keydescriptor 的 SAML 中繼資料的範例：

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

若要讓 Azure AD B2C 能夠傳送加密的判斷提示，請將 **WantsEncryptedAssertion** 中繼資料專案設定為信賴憑證者 `true` [技術設定檔](relyingparty.md#technicalprofile)中的。 您也可以設定用來加密 SAML 判斷提示的演算法。 如需詳細資訊，請參閱信賴憑證者 [技術設定檔中繼資料](relyingparty.md#metadata)。 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="enable-identity-provider-initiated-flow-optional"></a>啟用識別提供者起始的流程 (選擇性) 

在識別提供者起始的流程中，登入程式是由身分識別提供者起始 (Azure AD B2C) ，它會將未經要求的 SAML 回應傳送給服務提供者， (您的信賴憑證者應用程式) 。 我們目前不支援初始身分識別提供者為外部識別提供者（例如 [AD FS](identity-provider-adfs2016-custom.md)或 [Salesforce](identity-provider-salesforce-custom.md)）的案例。

若要啟用識別提供者 (Azure AD B2C) 起始的流程，請將 **>iDPInitiatedprofileenabled** 中繼資料專案設定為信賴憑證者 `true` [技術設定檔](relyingparty.md#technicalprofile)中的。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

若要透過識別提供者起始的流程登入或註冊使用者，請使用下列 URL：

```
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/generic/login?EntityId=app-identifier-uri 
```

取代下列值：

* **租使用者-名稱** 與您的租使用者名稱
* **原則-** 使用您的 SAML 信賴憑證者原則名稱
* 使用中繼資料檔案中的 **應用程式識別碼 uri** `identifierUris` ，例如`https://contoso.onmicrosoft.com/app-name`
## <a name="sample-policy"></a>範例原則

我們會提供完整的範例原則，供您用來搭配 SAML 測試應用程式進行測試。

1. 下載 [SAML SP 起始的登入範例原則](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. 更新 `TenantId`，以符合您的租用戶名稱，例如 contoso.b2clogin.com
1. 將原則名稱保留 *B2C_1A_signup_signin_saml*

## <a name="supported-and-unsupported-saml-modalities"></a>支援和不支援的 SAML 形式

下列 SAML 信賴憑證者 (RP) 案例可透過您自己的中繼資料端點來支援：

* 應用程式/服務主體物件中的多個登出 URL 或用於登出 URL 的 POST 繫結。
* 指定簽署金鑰以驗證應用程式/服務主體物件中的 RP 要求。
* 在應用程式/服務主體物件中指定權杖加密金鑰。
* 身分識別提供者起始登入，其中 Azure AD B2C 身分識別提供者。

## <a name="saml-token"></a>SAML 權杖

SAML 權杖是在成功登入之後 Azure AD B2C 所簽發的安全性權杖。 它包含使用者的相關資訊、權杖所適用的服務提供者、簽章和有效時間。 下表列出 Azure AD B2C 所簽發的 SAML 權杖中，您可以預期的宣告和屬性。

|元素  |屬性  |備註  |
|---------|---------|---------|
|`<Response>`| `ID` | 自動產生之回應的唯一識別碼。 | 
|`<Response>`| `InResponseTo` | 此訊息所回應的 SAML 要求識別碼。 | 
|`<Response>` | `IssueInstant` | 回應問題的時間。 時間值會以 UTC 編碼。  若要變更權杖存留期的設定，請設定 `TokenNotBeforeSkewInSeconds` SAML 權杖簽發者技術設定檔的 [中繼資料](saml-issuer-technical-profile.md#metadata) 。 | 
|`<Response>` | `Destination`| URI 參考，指出已傳送此回應的位址。 此值與 SAML 要求相同 `AssertionConsumerServiceURL` 。 | 
|`<Response>` `<Issuer>` | |識別權杖簽發者。 這是 SAML 權杖問題 `IssuerUri` [中繼資料](saml-issuer-technical-profile.md#metadata)所定義的任意 URI     |
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     |         |權杖判斷提示資訊的相關主體，例如使用者物件識別碼。 這個值不可變，而且無法重新指派或重複使用。 它可用來安全地執行授權檢查，例如當權杖用於存取資源時。 根據預設，主體宣告會填入目錄中使用者的物件識別碼。|
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     | `Format` | 表示以字串為基礎之識別碼資訊分類的 URI 參考。 預設會省略此屬性。 您可以設定信賴憑證者 [SubjectNamingInfo](relyingparty.md#subjectnaminginfo) 來指定 `NameID` 格式，例如 `urn:oasis:names:tc:SAML:2.0:nameid-format:transient` 。 |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` |`NotBefore` |權杖變成有效的時間。 時間值會以 UTC 編碼。 您的應用程式應使用此宣告來驗證權杖存留期的有效性。 若要變更權杖存留期的設定，請設定 `TokenNotBeforeSkewInSeconds` SAML 權杖問題技術設定檔的 [中繼資料](saml-issuer-technical-profile.md#metadata) 。 |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` | `NotOnOrAfter` | 權杖變成不正確時間。 您的應用程式應使用此宣告來驗證權杖存留期的有效性。 預設值為5分鐘之後 `NotBefore` ，可以藉由新增 `TokenLifeTimeInSeconds` SAML 權杖問題技術設定檔的 [中繼資料](saml-issuer-technical-profile.md#metadata) 來更新。|
|`<Response>` `<Assertion>` `<Conditions>` `<AudienceRestriction>` `<Audience>` | |識別目標物件的 URI 參考。 它會識別權杖的預定收件者。 此值與 SAML 要求相同 `AssertionConsumerServiceURL` 。|
|`<Response>``<Assertion>` `<AttributeStatement>` 的集合`<Attribute>` | | 判斷提示集合 (宣告) ，如信賴憑證者 [技術設定檔](relyingparty.md#technicalprofile) 輸出宣告中所設定。 您可以藉由設定輸出宣告的來設定判斷提示的名稱 `PartnerClaimType` 。 |

## <a name="next-steps"></a>後續步驟

- 您可以在 [OASIS 網站上找到有關 SAML 通訊協定的詳細資訊](https://www.oasis-open.org/)。
- [從 Azure AD B2C GitHub 社群存放庫](https://github.com/azure-ad-b2c/saml-sp-tester)取得 SAML 測試 Web 應用程式。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
