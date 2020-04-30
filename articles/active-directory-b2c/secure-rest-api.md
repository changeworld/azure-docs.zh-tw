---
title: 保護 Azure AD B2C 中的 Restful 服務
titleSuffix: Azure AD B2C
description: 在您的 Azure AD B2C 中保護您的自訂 REST API 宣告交換。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 34ed6d043f713aa55bfe464c48d4332364df805d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680369"
---
# <a name="secure-your-restful-services"></a>保護您的 RESTful 服務 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure AD B2C 的使用者旅程圖中整合 REST API 時，您必須使用驗證來保護您的 REST API 端點。 這可確保只有具有適當認證的服務（例如 Azure AD B2C）才能對您的 REST API 端點進行呼叫。

瞭解如何在「[驗證使用者輸入](custom-policy-rest-api-claims-validation.md)」和「[將 REST API 宣告交換加入至自訂原則](custom-policy-rest-api-claims-exchange.md)」文章中，整合 Azure AD B2C 使用者旅程圖中的 REST API。

本文將探討如何使用 HTTP 基本、用戶端憑證或 OAuth2 authentication 來保護您的 REST API。 

## <a name="prerequisites"></a>先決條件

完成下列其中一項「操作說明」指南中的步驟：

- [在您的 Azure AD B2C 使用者旅程圖中整合 REST API 宣告交換，以驗證使用者輸入](custom-policy-rest-api-claims-validation.md)。
- [將 REST API 宣告交換新增至自訂原則](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP 基本驗證

HTTP 基本驗證是在[RFC 2617](https://tools.ietf.org/html/rfc2617)中定義。 基本驗證的運作方式如下： Azure AD B2C 使用授權標頭中的用戶端認證來傳送 HTTP 要求。 認證會格式化為 base64 編碼的字串 "name： password"。  

### <a name="add-rest-api-username-and-password-policy-keys"></a>新增 REST API 使用者名稱和密碼原則金鑰

若要使用 HTTP 基本驗證來設定 REST API 技術設定檔，請建立下列密碼編譯金鑰來儲存使用者名稱和密碼：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請確定您使用的是包含您 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇您的 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
1. 選取 [**原則金鑰**]，然後選取 [**新增**]。
1. 針對 [選項]****，選取 [手動]****。
1. 針對 [**名稱**]，輸入**RestApiUsername**。
    可能會自動新增前置詞 B2C_1A_**。
1. 在 [**密碼**] 方塊中，輸入 REST API 的使用者名稱。
1. 針對 [金鑰使用方法]**** 選取 [加密]****。
1. 選取 [建立]  。
1. 再次選取 [**原則金鑰**]。
1. 選取 [新增]  。
1. 針對 [選項]****，選取 [手動]****。
1. 針對 [**名稱**]，輸入**RestApiPassword**。
    可能會自動新增前置詞 B2C_1A_**。
1. 在 [**秘密**] 方塊中，輸入 REST API 密碼。
1. 針對 [金鑰使用方法]**** 選取 [加密]****。
1. 選取 [建立]  。

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>將您的 REST API 技術設定檔設定為使用 HTTP 基本驗證

建立必要的金鑰之後，請將您的 REST API 技術設定檔中繼資料設定為參考認證。

1. 在您的工作目錄中，開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
1. 搜尋 REST API 技術設定檔。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找出 `<Metadata>` 元素。
1. 將*AuthenticationType*變更為`Basic`。
1. 將*AllowInsecureAuthInProduction*變更為`false`。
1. 在關閉 `</Metadata>` 元素之後，立即新增下列 XML 程式碼片段：
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

以下是使用 HTTP 基本驗證設定的 RESTful 技術設定檔範例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>HTTPS 用戶端憑證驗證

用戶端憑證驗證是相互憑證型驗證，其中用戶端 Azure AD B2C 會提供其用戶端憑證給伺服器，以證明其身分識別。 這會在 SSL 交握過程中發生。 只有具有適當憑證（例如 Azure AD B2C）的服務才能存取您的 REST API 服務。 用戶端憑證是 x.509 數位憑證。 在生產環境中，它必須由憑證授權單位單位進行簽署。

### <a name="prepare-a-self-signed-certificate-optional"></a>準備自我簽署憑證（選擇性）

若是非生產環境，如果您還沒有憑證，可以使用自我簽署憑證。 在 Windows 上，您可以使用 PowerShell 的[SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) Cmdlet 來產生憑證。

1. 執行此 PowerShell 命令來產生自我簽署憑證。 針對您`-Subject`的應用程式適當地修改引數，並 Azure AD B2C 租使用者名稱。 您也可以調整`-NotAfter`日期，為憑證指定不同的到期日。
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
1. 開啟 [**管理使用者憑證** > ] [**目前使用者** > **個人** > **憑證** > ]*yourappname.yourtenant.onmicrosoft.com*。
1. 選取憑證 >**動作** > [**所有** > 工作] [**匯出**]。
1. 選取 [**是** > **] [下一步** > **是，匯出私密金鑰** > **]**。
1. 接受**匯出檔案格式**的預設值。
1. 提供憑證的密碼。

### <a name="add-a-client-certificate-policy-key"></a>新增用戶端憑證原則金鑰

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請確定您使用的是包含您 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇您的 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
1. 選取 [**原則金鑰**]，然後選取 [**新增**]。
1. 在 [選項]**** 方塊中，選取 [上傳]****。
1. 在 [**名稱**] 方塊中，輸入**RestApiClientCertificate**。
    即會自動新增前置詞 *B2C_1A_*。
1. 在 [檔案上傳]**** 方塊中，選取包含私密金鑰的憑證 .pfx 檔案。
1. 在 [密碼]**** 方塊中，輸入憑證的密碼。
1. 選取 [建立]  。

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>將您的 REST API 技術設定檔設定為使用用戶端憑證驗證

建立必要的金鑰之後，請將您的 REST API 技術設定檔中繼資料設定為參考用戶端憑證。

1. 在您的工作目錄中，開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
1. 搜尋 REST API 技術設定檔。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找出 `<Metadata>` 元素。
1. 將*AuthenticationType*變更為`ClientCertificate`。
1. 將*AllowInsecureAuthInProduction*變更為`false`。
1. 在關閉 `</Metadata>` 元素之後，立即新增下列 XML 程式碼片段：
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

以下是使用 HTTP 用戶端憑證設定的 RESTful 技術設定檔範例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 持有人驗證 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

持有人權杖驗證定義于[oauth 2.0 授權架構中：持有人權杖用法（RFC 6750）](https://www.rfc-editor.org/rfc/rfc6750.txt)。 在持有人權杖驗證中，Azure AD B2C 會在授權標頭中傳送具有權杖的 HTTP 要求。

```http
Authorization: Bearer <token>
```

持有人權杖是不透明的字串。 它可以是 JWT 存取權杖，或 REST API 預期在授權標頭中傳送 Azure AD B2C 的任何字串。 Azure AD B2C 支援下列類型：

- **持有人權杖**。 若要能夠在 Restful 技術設定檔中傳送持有人權杖，您的原則必須先取得持有人權杖，然後在 RESTful 技術設定檔中使用它。  
- **靜態持有人權杖**。 當您的 REST API 發出長期存取權杖時，請使用此方法。 若要使用靜態持有人權杖，請建立原則金鑰，並從 RESTful 技術設定檔參考至您的原則金鑰。 


## <a name="using-oauth2-bearer"></a>使用 OAuth2 持有人  

下列步驟示範如何使用用戶端認證來取得持有人權杖，並將它傳遞至 REST API 呼叫的 Authorization 標頭。  

### <a name="define-a-claim-to-store-the-bearer-token"></a>定義用來儲存持有人權杖的宣告

宣告會在 Azure AD B2C 原則執行期間，提供資料的暫時儲存。 [宣告架構](claimsschema.md)是您宣告宣告的位置。 存取權杖必須儲存在宣告中，以供稍後使用。 

1. 開啟原則的擴充檔案。 例如， <em> `SocialAndLocalAccounts/` </em>。
1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 找出[ClaimsSchema](claimsschema.md)元素。 如果此元素不存在，請加以新增。
1. 將下列宣告新增至**ClaimsSchema**元素。  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>取得存取權杖 

您可以透過下列其中一種方式取得存取權杖：[從同盟識別提供者](idp-pass-through-custom.md)取得，方法是使用[ROPC 流程](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)來呼叫傳回存取權杖的 REST API，或使用[用戶端認證流程](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)。  

下列範例會使用 REST API 技術設定檔，向使用作為 HTTP 基本驗證傳遞之用戶端認證的 Azure AD 權杖端點提出要求。 若要在 Azure AD 中進行此設定，請參閱[Microsoft 身分識別平臺和 OAuth 2.0 用戶端認證流程](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)。 您可能需要將此修改為與您的身分識別提供者互動。 

針對 ServiceUrl，將您的租使用者名稱取代為 Azure AD 租使用者的名稱。 如需所有可用的選項，請參閱[RESTful 技術設定檔](restful-technical-profile.md)參考。

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>將 REST 技術設定檔變更為使用持有人權杖驗證

若要在您的自訂原則中支援持有人權杖驗證，請使用下列程式碼修改 REST API 技術設定檔：

1. 在您的工作目錄中，開啟 *TrustFrameworkExtensions.xml* 擴充原則檔案。
1. 搜尋包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 節點。
1. 找出 `<Metadata>` 元素。
1. 將*AuthenticationType*變更為*持有*人，如下所示：
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 將*UseClaimAsBearerToken*變更或新增至*bearerToken*，如下所示。 *BearerToken*是將從中抓取持有人權杖的宣告名稱（來自`SecureREST-AccessToken`的輸出宣告）。

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 請確定您已新增上述用來做為輸入宣告的宣告：

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

新增上述程式碼片段之後，您的技術設定檔看起來應該像下列 XML 程式碼：

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>使用靜態 OAuth2 持有人 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>新增 OAuth2 持有人權杖原則金鑰

建立原則金鑰來儲存持有人權杖值。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請確定您使用的是包含您 Azure AD B2C 租使用者的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選擇您的 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
1. 選取 [**原則金鑰**]，然後選取 [**新增**]。
1. 針對 [選項]**** 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]****。 例如： `RestApiBearerToken` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 [祕密]**** 中，輸入您先前記錄的用戶端密碼。
1. 針對 [金鑰使用方法]****，選取 `Encryption`。
1. 選取 [建立]  。

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>將您的 REST API 技術設定檔設定為使用持有人權杖原則金鑰

建立必要的金鑰之後，請將您的 REST API 技術設定檔中繼資料設定為參考持有人權杖。

1. 在您的工作目錄中，開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
1. 搜尋 REST API 技術設定檔。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找出 `<Metadata>` 元素。
1. 將*AuthenticationType*變更為`Bearer`。
1. 將*AllowInsecureAuthInProduction*變更為`false`。
1. 在關閉 `</Metadata>` 元素之後，立即新增下列 XML 程式碼片段：
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

以下是使用持有人權杖驗證設定的 RESTful 技術設定檔範例：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解 IEF 參考中的[Restful 技術設定檔](restful-technical-profile.md)元素。 
