---
title: 保護您 Azure AD B2C 中的 RESTful 服務
titleSuffix: Azure AD B2C
description: 保護您 Azure AD B2C 中的自訂 REST API 宣告交換。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f1897a4f58276bbac2a7de673544e592a562562
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826667"
---
# <a name="secure-your-restful-services"></a>保護您的 RESTful 服務 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure AD B2C 的使用者旅程圖中整合 REST API 時，您必須使用驗證來保護您的 REST API 端點。 這可確保只有具有適當認證的服務 (例如 Azure AD B2C)，才能對您的 REST API 端點發起呼叫。

在[驗證使用者輸入](custom-policy-rest-api-claims-validation.md)以及[將 REST API 宣告交換新增至自訂原則](custom-policy-rest-api-claims-exchange.md)文章內，了解如何在 Azure AD B2C 使用者旅程圖中整合 REST API。

本文將探討如何使用 HTTP 的基本用戶端憑證或 OAuth2 驗證，來保護您的 REST API。 

## <a name="prerequisites"></a>Prerequisites

完成下列「操作說明」指南中的其中一項步驟：

- [將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中，以驗證使用者輸入](custom-policy-rest-api-claims-validation.md)。
- [將 REST API 宣告交換新增至自訂原則](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP 基本驗證

[RFC 2617](https://tools.ietf.org/html/rfc2617) 中有 HTTP 基本驗證的定義。 基本驗證會如下所示運作︰Azure AD B2C 會使用授權標頭中的用戶端認證傳送 HTTP 要求。 該認證會格式化為 base64 編碼的字串 "name:password"。  

### <a name="add-rest-api-username-and-password-policy-keys"></a>新增 REST API 使用者名稱與密碼原則金鑰

若要使用 HTTP 基本驗證來設定 REST API 技術設定檔，請建立下列密碼編譯金鑰，以儲存使用者名稱與密碼：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇您的 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]。
1. 選取 [原則金鑰]，然後選取 [新增]。
1. 針對 [選項]，選取 [手動]。
1. 針對 [名稱]，鍵入 **RestApiUsername**。
    可能會自動新增前置詞 B2C_1A_。
1. 在 [祕密] 方塊中，輸入 REST API 使用者名稱。
1. 針對 [金鑰使用方法] 選取 [加密]。
1. 選取 [建立]。
1. 再次選取 [原則金鑰]。
1. 選取 [新增]。
1. 針對 [選項]，選取 [手動]。
1. 針對 [名稱]，鍵入 **RestApiPassword**。
    可能會自動新增前置詞 B2C_1A_。
1. 在 [祕密] 方塊中，輸入 REST API 密碼。
1. 針對 [金鑰使用方法] 選取 [加密]。
1. 選取 [建立]。

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>將您的 REST API 技術設定檔設定為使用 HTTP 基本驗證

在建立必要的金鑰之後，將您的 REST API 技術設定檔中繼資料設定為參考認證。

1. 在您的工作目錄中，開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
1. 搜尋 REST API 技術設定檔。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找出 `<Metadata>` 元素。
1. 將 *AuthenticationType* 變更為 `Basic`。
1. 將 *AllowInsecureAuthInProduction* 變更為 `false`。
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

用戶端憑證驗證是一種交互憑證型驗證，由用戶端 Azure AD B2C 向伺服器提供其用戶端憑證，以證明其身分識別。 這發生在 SSL 交握過程中。 只有具有適當憑證 (例如 Azure AD B2C) 的服務，才能存取您的 REST API 服務。 用戶端憑證為 X.509 數位憑證。 在生產環境中，必須由憑證授權單位簽署。

### <a name="prepare-a-self-signed-certificate-optional"></a>準備自我簽署憑證 (選擇性)

針對非生產環境，若您還沒有憑證，可以使用自我簽署憑證。 在 Windows 上，您可以使用 PowerShell 的 [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) (英文) Cmdlet 來產生憑證。

1. 執行此 PowerShell 命令來產生自我簽署憑證。 針對您的應用程式與 Azure AD B2C 租用戶名稱，適當地修改 `-Subject` 引數。 您也可以調整 `-NotAfter` 日期，為憑證指定不同的到期日。
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
1. 開啟 [管理使用者憑證] > [目前使用者] > [個人] > [憑證] > [yourappname.yourtenant.onmicrosoft.com]。
1. 選取憑證 > [動作] > [所有工作] > [匯出]。
1. 選取 [是] > [下一步] > [是，匯出私密金鑰] > [下一步]。
1. 接受 [匯出檔案格式] 的預設值。
1. 提供憑證的密碼。

### <a name="add-a-client-certificate-policy-key"></a>新增用戶端憑證原則金鑰

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇您的 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]。
1. 選取 [原則金鑰]，然後選取 [新增]。
1. 在 [選項] 方塊中，選取 [上傳]。
1. 在 [名稱] 方塊中，鍵入 **RestApiClientCertificate**。
    即會自動新增前置詞 *B2C_1A_* 。
1. 在 [檔案上傳] 方塊中，選取包含私密金鑰的憑證 .pfx 檔案。
1. 在 [密碼] 方塊中，輸入憑證的密碼。
1. 選取 [建立]。

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>將您的 REST API 技術設定檔設定為使用用戶端憑證驗證

在建立必要的金鑰之後，將您的 REST API 技術設定檔中繼資料設定為參考用戶端憑證。

1. 在您的工作目錄中，開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
1. 搜尋 REST API 技術設定檔。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找出 `<Metadata>` 元素。
1. 將 *AuthenticationType* 變更為 `ClientCertificate`。
1. 將 *AllowInsecureAuthInProduction* 變更為 `false`。
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

持有人權杖驗證定義於 [OAuth2.0 授權架構：持有人權杖使用方式 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) \(英文\)。 在持有人權杖驗證中，Azure AD B2C 會在授權標頭內傳送具有權杖的 HTTP 要求。

```http
Authorization: Bearer <token>
```

持有人權杖為不透明字串。 其可以是 JWT 存取權杖，或 REST API 預期 Azure AD B2C 在授權標頭中傳送的任何字串。 Azure AD B2C 支援下列類型：

- **持有人權杖**。 為能夠在 Restful 技術設定檔中傳送持有人權杖，您的原則必須先取得持有人權杖，從而使用在 RESTful 技術設定檔中。  
- **靜態持有人權杖**。 當您的 REST API 發出長期存取權杖時，請使用此方法。 若要使用靜態持有人權杖，請建立原則金鑰，並建立 RESTful 技術設定檔對您原則金鑰的參考。 


## <a name="using-oauth2-bearer"></a>使用 OAuth2 持有人  

下列步驟示範如何使用用戶端認證來取得持有人權杖，並將其傳遞到 REST API 呼叫的授權標頭內。  

### <a name="define-a-claim-to-store-the-bearer-token"></a>定義用以儲存持有人權杖的宣告

宣告會在 Azure AD B2C 原則執行期間，提供資料的暫存位置。 [宣告結構描述](claimsschema.md)是您宣告自有宣告的位置。 存取權杖必須儲存在宣告中，以供後用。 

1. 開啟您原則的擴充檔。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>。
1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 尋找 [ClaimsSchema](claimsschema.md) (機器翻譯) 元素。 如果此元素不存在，請加以新增。
1. 將下列宣告新增至 **ClaimsSchema** 元素。  

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

您可以透過下列其中一種方式取得存取權杖：[從同盟識別提供者](idp-pass-through-custom.md)取得、透過呼叫傳回存取權杖的 REST API 取得、使用 [ROPC 流程](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc) 取得，或使用[用戶端認證流程](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)取得。  

下列範例會使用 REST API 技術設定檔，使用以 HTTP 基本驗證傳遞的用戶端認證，向 Azure AD 權杖端點提出要求。 若要在 Azure AD 中進行此設定，請參閱 [Microsoft 身分識別平台與 OAuth 2.0 用戶端認證流程](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) (機器翻譯)。 您可能必須修改此設定，才能聯繫您的識別提供者。 

針對 ServiceUrl，請以您的 Azure AD 租用戶名稱取代 your-tenant-name。 如需所有可用的選項，請參閱 [RESTful 技術設定檔](restful-technical-profile.md)參考。

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
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

若要在您的自訂原則中支援持有人權杖驗證，請使用下列方式修改 REST API 技術設定檔：

1. 在您的工作目錄中，開啟 *TrustFrameworkExtensions.xml* 擴充原則檔案。
1. 搜尋包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 節點。
1. 找出 `<Metadata>` 元素。
1. 將 *AuthenticationType* 變更為 *Bearer*，如下所示：
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 將 *UseClaimAsBearerToken* 變更或新增為 *bearerToken*，如下所示。 *bearerToken* 是宣告的名稱，持有人權杖由此擷取 (來自 `SecureREST-AccessToken` 的輸出宣告)。

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 請確定您已將上述使用的宣告新增為輸入宣告：

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

在新增上述程式碼片段之後，您的技術設定檔看起來應該類似下列 XML 程式碼：

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
1. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇您的 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]。
1. 選取 [原則金鑰]，然後選取 [新增]。
1. 針對 [選項] 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]。 例如： `RestApiBearerToken` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 [祕密] 中，輸入您先前記錄的用戶端密碼。
1. 針對 [金鑰使用方法]，選取 `Encryption`。
1. 選取 [建立]。

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>將您的 REST API 技術設定檔設定為使用持有人權杖原則金鑰

在建立必要的金鑰之後，將您的 REST API 技術設定檔中繼資料設定為參考持有人權杖。

1. 在您的工作目錄中，開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
1. 搜尋 REST API 技術設定檔。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找出 `<Metadata>` 元素。
1. 將 *AuthenticationType* 變更為 `Bearer`。
1. 將 *AllowInsecureAuthInProduction* 變更為 `false`。
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

- 深入了解 IEF 參考中的 [RESTful 技術設定檔](restful-technical-profile.md)元素。 
