---
title: 在 Azure AD B2C 中保護寧靜的服務
titleSuffix: Azure AD B2C
description: 在 Azure AD B2C 中保護自訂 REST API 聲明交換。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5a80c6e3bd8cf647590ed757c042ef3301e27b4a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743522"
---
# <a name="secure-your-restful-services"></a>保護您的 RESTt 服務 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure AD B2C 使用者旅程中整合 REST API 時,必須透過身份驗證保護 REST API 終結點。 這可確保只有具有適當認證的服務(如 Azure AD B2C)才能對 REST API 終結點進行調用。

瞭解如何在 Azure AD B2C 使用者旅程中整合 REST API 以[驗證使用者輸入](custom-policy-rest-api-claims-validation.md)並將[REST API 聲明交換添加到自訂策略](custom-policy-rest-api-claims-exchange.md)文章。

本文將探討如何使用 HTTP 基本、用戶端證書或 OAuth2 身份驗證來保護 REST API。 

## <a name="prerequisites"></a>Prerequisites

完成以下「操作」指南之一中的步驟:

- [在 Azure AD B2C 使用者旅程中整合 REST API 宣告交換,以驗證使用者輸入](custom-policy-rest-api-claims-validation.md)。
- [將 REST API 宣告交換到自訂政策](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP 認證

HTTP 基本身份驗證在[RFC 2617](https://tools.ietf.org/html/rfc2617)中定義。 基本身份驗證的工作原理如下:Azure AD B2C 發送具有授權標頭中的用戶端憑據的 HTTP 請求。 認證格式化為基64編碼字串。name:password"  

### <a name="add-rest-api-username-and-password-policy-keys"></a>新增 REST API 使用者名稱與密碼原則金鑰

要使用 HTTP 基本身份驗證設定 REST API 技術設定檔,請建立以下加密金鑰以儲存使用者名稱和密碼:

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器,然後選擇 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
1. 選擇**策略鍵**,然後選擇 **"添加**"。
1. 針對 [選項]****，選取 [手動]****。
1. 此**名稱**,鍵入**RestApi 使用者名稱**。
    可能會自動新增前置詞 B2C_1A_**。
1. 在 **「機密」** 框中,輸入 REST API 使用者名稱。
1. 針對 [金鑰使用方法]**** 選取 [加密]****。
1. 選取 [建立]  。
1. 再次選擇**策略鍵**。
1. 選取 [新增]  。
1. 針對 [選項]****，選取 [手動]****。
1. 對**名稱**,鍵入**RestApi 密碼**。
    可能會自動新增前置詞 B2C_1A_**。
1. 在 **「機密」** 框中,輸入 REST API 密碼。
1. 針對 [金鑰使用方法]**** 選取 [加密]****。
1. 選取 [建立]  。

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>將 REST API 技術設定檔設定為使用 HTTP 基本身份驗證

創建必要的密鑰後,配置 REST API 技術設定檔中資料以參考認證。

1. 在您的工作目錄中，開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技術配置檔。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找出 `<Metadata>` 元素。
1. 將*認證型態*變更為`Basic`。
1. 將 *「允許不安全 Auth 生產」* 變更為`false`。
1. 在關閉 `</Metadata>` 元素之後，立即新增下列 XML 程式碼片段：
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

下面是設定 HTTP 基本身份驗證的 RESTful 技術設定檔的範例:

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

## <a name="https-client-certificate-authentication"></a>HTTPS 用戶端憑證認證

用戶端證書身份驗證是一種基於相互證書的身份驗證,其中用戶端 Azure AD B2C 向伺服器提供其用戶端證書以證明其身份。 這作為 SSL 握手的一部分發生。 只有具有適當證書的服務(如 Azure AD B2C)才能訪問 REST API 服務。 用戶端憑證是 X.509 數位證書。 在生產環境中,必須由證書頒發機構簽名。

### <a name="prepare-a-self-signed-certificate-optional"></a>準備自簽署憑證(選擇性的)

對於非生產環境,如果您還沒有證書,則可以使用自簽名證書。 在 Windows 上,您可以使用 PowerShell 的[「新自簽名證書](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate)」cmdlet 產生證書。

1. 執行此 PowerShell 命令以生成自簽名證書。 根據需要修改`-Subject`參數,以適合您的應用程式和 Azure AD B2C 租戶名稱。 您還可以調整日期以`-NotAfter`指定證書的不同過期。
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
1. 開啟**管理使用者憑證** > **目前使用者** > **個人** > **憑證** > *yourappname.yourtenant.onmicrosoft.com*。
1. 選擇憑證>**操作** > **所有工作** > **匯出**。
1. 選擇 **「是** > **下一個是** > **」,匯出私鑰** > **」下一個**。
1. 接受**匯出檔案格式**的預設值。
1. 提供證書的密碼。

### <a name="add-a-client-certificate-policy-key"></a>新增客戶端憑證原則金鑰

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器,然後選擇 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
1. 選擇**策略鍵**,然後選擇 **"添加**"。
1. 在 [選項]**** 方塊中，選取 [上傳]****。
1. 在 **「名稱」** 框中, 鍵入**RestApiClient 憑證**。
    即會自動新增前置詞 *B2C_1A_*。
1. 在 [檔案上傳]**** 方塊中，選取包含私密金鑰的憑證 .pfx 檔案。
1. 在 [密碼]**** 方塊中，輸入憑證的密碼。
1. 選取 [建立]  。

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>設定 REST API 技術設定檔以使用客戶端憑證認證

建立必要的金鑰後,配置 REST API 技術設定檔中資料以參考用戶端證書。

1. 在您的工作目錄中，開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技術配置檔。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找出 `<Metadata>` 元素。
1. 將*認證型態*變更為`ClientCertificate`。
1. 將 *「允許不安全 Auth 生產」* 變更為`false`。
1. 在關閉 `</Metadata>` 元素之後，立即新增下列 XML 程式碼片段：
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

下面是設定 HTTP 客戶端憑證的 RESTful 技術設定檔的範例:

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

## <a name="oauth2-bearer-authentication"></a>OAuth2 承載身分驗證 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

承載權杖身份驗證在[OAuth2.0 授權框架中定義:承載權杖使用 (RFC 6750)。](https://www.rfc-editor.org/rfc/rfc6750.txt) 在無記名權杖身份驗證中,Azure AD B2C 發送具有授權標頭中權杖的 HTTP 請求。

```http
Authorization: Bearer <token>
```

承載令牌是不透明字串。 它可以是 JWT 訪問權杖或 REST API 期望 Azure AD B2C 在授權標頭中發送的任何字串。 Azure AD B2C 支援以下類型:

- **承載權杖**. 為了能夠在寧靜的技術配置檔中發送無記名令牌,您的策略需要首先獲取無記名令牌,然後在 RESTful 技術配置檔中使用它。  
- **靜態承載權杖**。 當 REST API 發出長期存取權杖時,請使用此方法。 要使用靜態無記名權杖,請建立策略金鑰,並從 RESTful 技術設定檔向策略密鑰進行引用。 


## <a name="using-oauth2-bearer"></a>使用 OAuth2 承載器  

以下步驟演示如何使用客戶端認證取得無記名權杖並將其傳遞到 REST API 呼叫的授權標頭中。  

### <a name="define-a-claim-to-store-the-bearer-token"></a>定義用於儲存無記名權杖的聲明

聲明在 Azure AD B2C 策略執行期間提供數據的臨時存儲。 [聲明架構](claimsschema.md)是聲明聲明的位置。 訪問權杖必須儲存在聲明中,以便以後使用。 

1. 打開策略的擴展檔。 例如, <em> `SocialAndLocalAccounts/` </em>.
1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 找到[聲明架構](claimsschema.md)元素。 如果此元素不存在，請加以新增。
1. 將城市承載令牌添加到**聲明架構**元素。  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>取得存取權杖 

您可以透過以下幾種方式的一抓取權限碼:[透過從聯合識別提供者](idp-pass-through-custom.md)取得存取權杖、透過呼叫傳送權碼的 REST API、使用[ROPC 串流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)或使用[客戶端認證串流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)。  

下面的範例使用 REST API 技術設定檔使用作為 HTTP 基本身份驗證傳遞的用戶端認證向 Azure AD 令牌終結點發出請求。 在 Azure AD 中設定此功能,請參閱[Microsoft 識別平台和 OAuth 2.0 客戶端認證串流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)。 您可能需要修改此內容以與您的身份提供者介面。 

對於 ServiceUrl,請將租戶名稱替換為 Azure AD 租戶的名稱。 有關所有可用的選項,請參閱[RESTful 技術配置檔](restful-technical-profile.md)參考。

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

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>將 REST 技術設定檔變更為使用無記名權杖身份驗證

要在自訂策略中支援無記名權杖身份驗證,請修改 REST API 技術設定檔,並參用以下內容:

1. 在您的工作目錄中，開啟 *TrustFrameworkExtensions.xml* 擴充原則檔案。
1. 搜尋包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 節點。
1. 找出 `<Metadata>` 元素。
1. 將*認證類型*更改為*承載類型*,如下所示:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 更改或添加*使用聲明標記*到*承載令牌*,如下所示。 *承載權杖*是從(`SecureREST-AccessToken`來自的輸出聲明)檢索的持號標記的聲明的名稱。

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 請確保將上述使用的聲明加入為輸入聲明:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

新增此程式碼段後,您的技術設定檔應類似於以下 XML 代碼:

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

## <a name="using-a-static-oauth2-bearer"></a>使用靜態 OAuth2 承載器 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>新增 OAuth2 承載權杖原則金鑰

創建策略鍵以存儲無記名權杖值。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器,然後選擇 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
1. 選擇**策略鍵**,然後選擇 **"添加**"。
1. 針對 [選項]**** 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]****。 例如： `RestApiBearerToken` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 [祕密]**** 中，輸入您先前記錄的用戶端密碼。
1. 針對 [金鑰使用方法]****，選取 `Encryption`。
1. 選取 [建立]  。

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>設定 REST API 技術設定檔以使用無記名權杖原則金鑰

建立必要的密鑰後,配置 REST API 技術設定檔中資料以參考無記名權杖。

1. 在您的工作目錄中，開啟擴充原則檔案 (TrustFrameworkExtensions.xml)。
1. 搜索 REST API 技術配置檔。 例如 `REST-ValidateProfile` 或 `REST-GetProfile`。
1. 找出 `<Metadata>` 元素。
1. 將*認證型態*變更為`Bearer`。
1. 將 *「允許不安全 Auth 生產」* 變更為`false`。
1. 在關閉 `</Metadata>` 元素之後，立即新增下列 XML 程式碼片段：
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

下面是設定承載權杖身份驗證的 RESTful 技術設定檔的範例:

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

- 詳細瞭解 IEF 參考中的[寧靜技術配置檔](restful-technical-profile.md)元素。 
