---
title: 將 Azure AD B2C 配置為對應用程式的 SAML IdP
title-suffix: Azure AD B2C
description: 如何配置 Azure AD B2C 以向應用程式（服務提供者）提供 SAML 協定斷言。 Azure AD B2C 將作為 SAML 應用程式的單個標識提供程式 （IdP）。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051611"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>在 Azure AD B2C 中註冊 SAML 應用程式

在本文中，您將瞭解如何配置 Azure 活動目錄 B2C（Azure AD B2C）以充當應用程式的安全斷言標記語言 （SAML） 標識提供程式 （IdP）。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>案例概觀

使用 Azure AD B2C 作為客戶標識和訪問管理解決方案的組織可能需要與配置為使用 SAML 協定進行身份驗證的標識提供程式或應用程式進行交互。

Azure AD B2C 可通過兩種方式之一實現 SAML 互通性：

* 通過充當*標識提供程式*（IdP）並與基於 SAML 的服務提供者 （您的應用程式） 實現單一登入 （SSO）
* 通過充當*服務提供者*（SP） 並與基於 SAML 的標識供應商（如 Salesforce 和 ADFS）進行交互

![左側為標識提供程式，右側為 B2C 作為服務提供者](media/saml-identity-provider/saml-idp-diagram-01.jpg)

使用 SAML 總結兩個非獨佔核心方案：

| 狀況 | Azure AD B2C 角色 | 操作方式 |
| -------- | ----------------- | ------- |
| 我的應用程式希望 SAML 斷言完成身份驗證。 | **Azure AD B2C 充當標識提供程式 （IdP）**<br />Azure AD B2C 充當應用程式的 SAML IdP。 | 本文。 |
| 我的使用者需要使用符合 SAML 的標識提供程式（如 ADFS、Salesforce 或 Shibboleth）單一登入。  | **Azure AD B2C 充當服務提供者 （SP）**<br />連接到 SAML 標識提供程式時，Azure AD B2C 充當服務提供者。 它是應用程式和 SAML 標識提供程式之間的聯合代理。  | <ul><li>[使用自訂策略將 ADFS 作為 SAML IdP 進行註冊](identity-provider-adfs2016-custom.md)</li><li>[使用自訂策略使用 Salesforce SAML 提供程式設置登錄](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Prerequisites

* 在 Azure [AD B2C 中完成"開始使用自訂策略"中](custom-policy-get-started.md)的步驟。 您需要本文中討論的自訂策略初學者包中的*社交和本地帳戶*自訂策略。
* 基本瞭解安全斷言標記語言 （SAML） 協定。
* 配置為 SAML 服務提供者 （SP） 的 Web 應用程式。 在本教程中，您可以使用我們提供的[SAML 測試應用程式][samltest]。

## <a name="components-of-the-solution"></a>解決方案的元件

此方案需要三個主要元件：

* SAML**服務提供者**能夠發送 SAML 請求，並從 Azure AD B2C 接收、解碼和回應 SAML 斷言。 這也被稱為依賴方。
* 服務提供者的公開可用的 SAML**中繼資料終結點**。
* [Azure AD B2C 租用戶](tutorial-create-tenant.md)

如果您還沒有 SAML 服務提供者和關聯的中繼資料終結點，則可以使用我們提供的可用於測試的此示例 SAML 應用程式：

[SAML 測試應用程式][samltest]

## <a name="1-set-up-certificates"></a>1. 設置證書

要在服務提供者和 Azure AD B2C 之間建立信任關係，您需要提供 Web 應用 X509 證書。

* **服務提供者證書**
  * 在 Web 應用中存儲私密金鑰的證書。 此證書由服務提供者用於對發送到 Azure AD B2C 的 SAML 請求進行簽名。 Azure AD B2C 從服務提供者中繼資料讀取公開金鑰以驗證簽名。
  * （可選）在 Web 應用中存儲私密金鑰的證書。 Azure AD B2C 從服務提供者中繼資料讀取公開金鑰以加密 SAML 斷言。 然後，服務提供者使用私密金鑰解密斷言。
* **Azure AD B2C 證書**
  * 具有 Azure AD B2C 中私密金鑰的證書。 此證書由 Azure AD B2C 用於對發送到服務提供者的 SAML 回應進行簽名。 服務提供者讀取 Azure AD B2C 中繼資料公開金鑰以驗證 SAML 回應的簽名。

您可以使用由公共憑證授權單位頒發的證書，或者對於本教程而言，使用自簽章憑證。

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 準備自簽章憑證

如果您還沒有證書，則可以使用自簽章憑證進行本教程。 在 Windows 上，您可以使用 PowerShell 的["新自簽章憑證](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate)"Cmdlet 生成證書。

1. 執行此 PowerShell 命令以生成自簽章憑證。 根據需要修改`-Subject`參數，以適合您的應用程式和 Azure AD B2C 租戶名稱。 您還可以調整日期以`-NotAfter`指定證書的不同過期。

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

1. 打開**管理 使用者證書** > **當前使用者** > **個人** > **證書** > *yourappname.yourtenant.onmicrosoft.com*
1. 選擇證書>**操作** > **所有任務** > **匯出**
1. 選擇 **"是** > **下一個"，** > **匯出私密金鑰** > **下一個**
1. 接受**匯出檔案格式**的預設值
1. 提供證書的密碼

### <a name="12-upload-the-certificate"></a>1.2 上傳證書

接下來，將 SAML 斷言和回應簽章憑證上載到 Azure AD B2C。

1. 登錄到 Azure[門戶](https://portal.azure.com)並流覽到 Azure AD B2C 租戶。
1. 在 **"策略**"下，選擇 **"身份體驗框架****"，然後選擇策略鍵**。
1. 選擇 **"添加**"，然後選擇 **"** > **上載**選項"。
1. 輸入**名稱**，例如*SamlidpCert*。 金鑰名稱前面會自動新增前置詞 B2C_1A_**。
1. 使用上載檔控制項上載證書。
1. 輸入證書的密碼。
1. 選取 [建立]****。
1. 驗證金鑰是否按預期顯示。 例如 *，B2C_1A_SamlIdpCert*。

## <a name="2-prepare-your-policy"></a>2. 準備您的保單

### <a name="21-create-the-saml-token-issuer"></a>2.1 創建 SAML 權杖頒發者

現在，使用[SAML 權杖頒發者和 SAML](saml-issuer-technical-profile.md) [會話提供程式](custom-policy-reference-sso.md#samlssosessionprovider)技術設定檔添加租戶頒發 SAML 權杖的功能。

在`SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** 自訂策略初學者包中打開。

找到該`<ClaimsProviders>`部分並添加以下 XML 程式碼片段。

您可以更改`IssuerUri`中繼資料的值。 這是從 Azure AD B2C 返回的 SAML 回應中的頒發者 URI。 應將依賴方應用程式佈建為在 SAML 斷言驗證期間接受頒發者 URI。

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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. 添加 SAML 依賴方政策

現在，租戶可以發出 SAML 斷言，您需要創建 SAML 依賴方策略，並修改使用者旅程，以便發佈 SAML 斷言而不是 JWT。

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 創建註冊或登錄策略

1. 在入門包工作目錄中創建*SignUpOrSignin.xml*檔的副本，並將其保存為新名稱。 例如，*註冊或SigninSAML.xml*。 這是您的依賴方策略檔。

1. 在首選編輯器中打開 *"註冊或簽名"檔。"xml"*

1. 將`PolicyId`策略`PublicPolicyUri`和將更改為_B2C_1A_signup_signin_saml，_`http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml`如下所示。

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

1. 在`<RelyingParty>`元素之前添加以下 XML 程式碼片段。 此 XML 覆蓋_SignUpOrSign 使用者_旅程的業務流程步驟 7。 如果從初學者包中的其他資料夾開始，或者通過添加或刪除業務流程步驟來自訂使用者旅程，請確保該編號`order`（元素中）與權杖頒發者步驟的使用者旅程中指定的號碼一致（例如，在其他初學者包資料夾中，其步驟號為`LocalAccounts`4 for`SocialAccounts`和 9。 `SocialAndLocalAccountsWithMfa`

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. 將元素中的`<TechnicalProfile>``<RelyingParty>`整個元素替換為以下技術設定檔 XML。

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

1. 使用`tenant-name`Azure AD B2C 租戶的名稱進行更新。

您的最終依賴方策略檔應如下所示：

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

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 上傳並測試策略中繼資料

保存更改並上載新的策略檔。 上載兩個策略（副檔名和依賴方檔）後，打開 Web 瀏覽器並導航到策略中繼資料。

Azure AD B2C 策略 IDP 中繼資料是 SAML 協定中用於公開 SAML 標識提供程式配置的資訊。 中繼資料會定義服務的位置，例如，登入和登出、憑證、登入方法，以及其他項目。 Azure AD B2C 策略中繼資料可在以下 URL 上使用。 替換為`tenant-name`Azure AD B2C 租戶的名稱，以及`policy-name`策略的名稱 （ID）：

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

自訂策略和 Azure AD B2C 租戶現已準備就緒。 接下來，在 Azure AD B2C 中創建應用程式註冊。

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Azure AD B2C 目錄中的安裝程式

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 在 Azure 活動目錄中註冊應用程式

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]****。 或者，選取 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 選取 [應用程式註冊 (預覽)]****，然後選取 [新增註冊]****。
1. 輸入應用程式的 [名稱]****。 例如 *，SAMLApp1*。
1. 在 **"支援帳戶類型**"下，**僅選擇此組織目錄中的帳戶**
1. 在**重定向 URI**下，選擇**Web** `https://localhost`，然後輸入 。 稍後在應用程式註冊的清單中修改此值。
1. 選取 [註冊]****。

### <a name="42-update-the-app-manifest"></a>4.2 更新應用清單

對於 SAML 應用，需要在應用程式註冊的清單中配置多個屬性。

1. 在[Azure 門戶](https://portal.azure.com)中，導航到您在上一節中創建的應用程式註冊。
1. 在 **"管理**"下，選擇 **"清單"** 以打開清單編輯器。 在以下部分中修改多個屬性。

#### <a name="identifieruris"></a>identifierUris

是`identifierUris`包含使用者定義的 URI 的字串集合，用於唯一標識其 Azure AD B2C 租戶中的 Web 應用。 您的服務提供者必須在 SAML 請求的元素`Issuer`中設置此值。

#### <a name="samlmetadataurl"></a>samlMetadataUrl

此屬性工作表示服務提供者的公開可用中繼資料 URL。 中繼資料 URL 可以指向上載到任何匿名可訪問的終結點（例如 Blob 存儲）的元資料檔案。

中繼資料是 SAML 協定中用於公開 SAML 參與方（如服務提供者）配置的資訊。 元資料定義服務的位置，如登錄和登出、證書、登錄方法等。 Azure AD B2C 讀取服務提供者中繼資料並相應地執行操作。 不需要中繼資料。 您還可以直接在應用清單中指定一些屬性，如回復 URI 和登出 URI。

如果 SAML 中繼資料*both*URL 和應用程式註冊的清單中都指定了屬性 **，則合併**這些屬性。 首先處理中繼資料 URL 中指定的屬性並優先處理。

對於使用 SAML 測試應用程式的本教程，請使用 以下值進行`samlMetadataUrl`：

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>回復 Urls 與類型（可選）

如果不提供中繼資料 URI，則可以顯式指定答覆 URL。 此可選屬性工作表示`AssertionConsumerServiceUrl`（`SingleSignOnService`服務提供者中繼資料中的 URL），`BindingType`假定 為`HTTP POST`。

如果選擇在不使用服務提供者中繼資料的情況下在應用程式清單中配置回復 URL 和登出 URL，則 Azure AD B2C 將不會驗證 SAML 請求籤名，也不會加密 SAML 回應。

在本教程中，使用 SAML 測試應用程式，將`url`的屬性`replyUrlsWithType`設置為以下 JSON 程式碼片段中顯示的值。

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>登出 Url（可選）

此可選屬性工作表示`Logout`URL（`SingleLogoutService`依賴方中繼資料中的 URL），並且假定`BindingType``Http-Redirect`為 。

對於使用 SAML 測試應用程式的本教程，將`logoutUrl`設置為 ： `https://samltestapp2.azurewebsites.net/logout`

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. 更新應用程式代碼

最後一步是在 SAML 依賴方應用程式中啟用 Azure AD B2C 作為 SAML IdP。 每個應用程式都不同，這樣做的步驟也各不相同。 有關詳細資訊，請參閱應用的文檔。

通常需要以下部分或全部：

* **中繼資料**：`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **頒發者**：使用元資料檔案中的實體 ID
* **登錄 Url/SAML 終結點/SAML Url**：檢查元資料檔案中的值
* **證書**：這是*B2C_1A_SamlIdpCert，* 但沒有私密金鑰。 要獲取證書的公開金鑰，可以：

    1. 轉到上面指定的中繼資料 URL。
    1. 複製元素中`<X509Certificate>`的值。
    1. 將其粘貼到文字檔中。
    1. 將文字檔另存為 *.cer*檔。

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 使用 SAML 測試應用程式進行測試（可選）

要使用我們的[SAML 測試應用程式][samltest]完成本教程：

* 更新租戶名稱
* 更新策略名稱，例如*B2C_1A_signup_signin_saml*
* 指定此頒發者 URI：`https://contoso.onmicrosoft.com/app-name`

選擇 **"登錄"，** 您應該顯示使用者登錄螢幕。 登錄後，SAML 斷言將發出回應用程式範例。

## <a name="sample-policy"></a>範例原則

我們提供完整的示例策略，您可以使用該策略與 SAML 測試應用進行測試。

1. 下載[SAML-SP 啟動的登錄示例策略](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. 更新`TenantId`以匹配租戶名稱，例如*contoso.b2clogin.com*
1. 保留*B2C_1A_SAML2_signup_signin*的策略名稱

## <a name="supported-and-unsupported-saml-modalities"></a>支援和不支援的 SAML 模式

以下 SAML 依賴方 （RP） 方案通過您自己的中繼資料終結點提供支援：

* 多個登出 URL 或 POST 綁定，用於應用程式/服務主體物件中的登出 URL。
* 指定簽名金鑰以驗證應用程式/服務主體物件中的 RP 請求。
* 在應用程式/服務主體物件中指定權杖加密金鑰。
* 預覽版本中當前不支援標識提供程式啟動的登錄。

## <a name="next-steps"></a>後續步驟

- 您可以在 OASIS 網站上找到有關[SAML 協定](https://www.oasis-open.org/)的更多資訊。
- 從[Azure AD B2C GitHub 社區存儲庫](https://github.com/azure-ad-b2c/saml-sp-tester)獲取 SAML 測試 Web 應用。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
