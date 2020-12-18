---
title: 在自訂原則中定義識別碼權杖提示技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自訂原則中定義識別碼權杖提示技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d77e145cabcef2931d5fe6e76599da7931e576e8
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97669154"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自訂原則中定義識別碼權杖提示技術設定檔

Azure AD B2C 可讓信賴憑證者應用程式傳送輸入 JWT 作為 OAuth2 授權要求的一部分。 JWT 權杖可以由信賴憑證者應用程式或身分識別提供者發出，並且可以傳遞關於使用者或授權要求的提示。 Azure AD B2C 會驗證簽章、簽發者名稱和權杖物件，並從輸入權杖中解壓縮宣告。

## <a name="use-cases"></a>使用案例

您可以使用此解決方案，將資料傳送至封裝在單一 JWT 權杖中 Azure AD B2C。 [註冊電子郵件邀請解決方案](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md)，您的系統管理員可以將簽署的邀請傳送給使用者，以 id_token_hint 為基礎。 只有具有邀請電子郵件存取權限的使用者可以在目錄中建立帳戶。

## <a name="token-signing-approach"></a>權杖簽署方法

使用 id_token_hint，權杖簽發者 (信賴憑證者應用程式或身分識別提供者) 撰寫權杖，然後使用簽署金鑰來簽署權杖，以證明權杖來自受信任的來源。 簽署金鑰可以是對稱或非對稱的。 對稱式加密或私密金鑰密碼編譯會使用共用密碼來簽署和驗證簽章。 非對稱式密碼編譯或公開金鑰加密是一種密碼編譯系統，同時使用私密金鑰和公開金鑰。 只有權杖簽發者知道私密金鑰，並使用此金鑰來簽署權杖。 公開金鑰會與 Azure AD B2C 原則共用，以驗證權杖的簽章。

## <a name="token-format"></a>權杖格式

Id_token_hint 必須是有效的 JWT 權杖。 下表列出必要的宣告。 其他宣告是選擇性的。

| 名稱 | 宣告 | 範例值 | 描述 |
| ---- | ----- | ------------- | ----------- |
| 適用對象 | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | 識別權杖的預定接收者。 物件是權杖簽發者所定義的任一字元串。 Azure AD B2C 會驗證此值，並在不符合的情況下拒絕權杖。  |
| Issuer | `iss` |`https://localhost` | 識別 (權杖簽發者) 的安全性權杖服務。 簽發者是權杖簽發者所定義的任意 URI。 Azure AD B2C 會驗證此值，並在不符合的情況下拒絕權杖。  |
| 到期時間 | `exp` | `1600087315` | 權杖失效的時間 (以新紀元 (Epoch) 時間表示)。 Azure AD B2C 會驗證此值，如果權杖已過期，則會拒絕權杖。|
| 生效時間 | `nbf` | `1599482515` | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 此時間通常與權杖的簽發時間相同。 Azure AD B2C 會驗證此值，並在權杖存留期無效時拒絕權杖。 |

 下列權杖是有效識別碼權杖的範例：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `None`。 例如， **IdTokenHint_ExtractClaims** 技術設定檔的通訊協定是 `None` ：

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

技術設定檔是從類型為的協調流程步驟所呼叫 `GetClaims` 。

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>輸出宣告

**OutputClaims** 元素包含要從 JWT 權杖中解壓縮的宣告清單。 您可能需要將原則中定義的宣告名稱對應至 JWT 權杖中定義的名稱。 只要您設定屬性，您也可以包含 JWT 權杖未傳回的宣告 `DefaultValue` 。

## <a name="metadata"></a>中繼資料

以下是使用對稱金鑰時的相關中繼資料。 

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 簽發者 | 是 | 識別 (權杖簽發者) 的安全性權杖服務。 此值必須與 JWT 權杖宣告內的宣告相同 `iss` 。 | 
| IdTokenAudience | 是 | 識別權杖的預定接收者。 必須與 JWT 權杖宣告內的宣告相同 `aud` 。 | 

以下是使用非對稱金鑰時的相關中繼資料。 

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| METADATA| 是 | 指向權杖簽發者設定檔的 URL，也稱為 OpenID 知名設定端點。   |
| 簽發者 | 否 | 識別 (權杖簽發者) 的安全性權杖服務。 這個值可以用來覆寫中繼資料中設定的值，而且必須與 JWT 權杖宣告內的宣告相同 `iss` 。 |  
| IdTokenAudience | 否 | 識別權杖的預定接收者。 必須與 JWT 權杖宣告內的宣告相同 `aud` 。 |  

## <a name="cryptographic-keys"></a>密碼編譯金鑰

使用對稱金鑰時， **CryptographicKeys** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| client_secret | 是 | 用來驗證 JWT 權杖簽章的密碼編譯金鑰。|


## <a name="how-to-guide"></a>操作指南

### <a name="issue-a-token-with-symmetric-keys"></a>使用對稱金鑰發行權杖

#### <a name="step-1-create-a-shared-key"></a>步驟 1： 建立共用金鑰 

建立可用於簽署權杖的金鑰。 例如，使用下列 PowerShell 程式碼來產生金鑰。

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

此程式碼會建立類似的秘密字串 `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` 。

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>步驟 2： 將簽署金鑰新增至 Azure AD B2C

權杖簽發者所使用的相同金鑰必須建立在您的 Azure AD B2C 原則金鑰中。  

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [概觀] 頁面的 [原則] 下，選取 [識別體驗架構]。
1. 選取 **原則金鑰** 
1. 選取 [ **手動**]。
1. 針對 [名稱] 使用 `IdTokenHintKey`。  
   可能會自動加入前置詞 `B2C_1A_`。
1. 在 [ **密碼** ] 方塊中，輸入您稍早產生的登入金鑰。
1. 針對 [金鑰使用方法] 使用 [加密]。
1. 選取 [建立]。
1. 確認您已建立金鑰 `B2C_1A_IdTokenHintKey`。


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>步驟 3： 新增識別碼權杖提示技術設定檔

下列技術設定檔會驗證權杖，並將宣告解壓縮。 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>步驟 4： 準備您的原則

完成 [設定您的原則](#configure-your-policy) 步驟。

#### <a name="step-5-prepare-the-code"></a>步驟 5。 準備程式碼  

[GitHub 範例](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key)是 ASP.NET web 應用程式和主控台應用程式，它會產生使用對稱金鑰簽署的識別碼權杖。 


### <a name="issue-a-token-with-asymmetric-keys"></a>使用非對稱金鑰發出權杖

使用非對稱金鑰時，會使用 RSA 憑證簽署權杖。 此應用程式會將 Open ID Connect 中繼資料端點和 JSON Web 金鑰 (Azure AD B2C 用來驗證識別碼權杖簽章的) 端點。

權杖簽發者必須提供下列端點：

* `/.well-known/openid-configuration` -知名的設定端點，其中包含權杖的相關資訊，例如權杖簽發者名稱和 JWK 端點的連結。 
* `/.well-known/keys` -JSON Web 金鑰 (JWK) 端點，其中包含用來用來簽署金鑰的公開金鑰 (憑證) 的私密金鑰部分。

請參閱 [TokenMetadataController.cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .net MVC 控制器範例。

#### <a name="step-1-prepare-a-self-signed-certificate"></a>步驟 1： 準備自我簽署憑證

如果您還沒有憑證，您可以使用自我簽署的憑證來進行本操作指南。 在 Windows 上，您可以使用 PowerShell 的 [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) (英文) Cmdlet 來產生憑證。

執行此 PowerShell 命令以產生自我簽署憑證。 針對您的應用程式與 Azure AD B2C 租用戶名稱，適當地修改 `-Subject` 引數。 您也可以調整 `-NotAfter` 日期，為憑證指定不同的到期日。

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


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>步驟 2： 新增識別碼權杖提示技術設定檔 

下列技術設定檔會驗證權杖，並將宣告解壓縮。 將中繼資料 URI 變更為您的權杖簽發者知名設定端點。  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>步驟 3： 準備您的原則

完成 [設定您的原則](#configure-your-policy) 步驟。

#### <a name="step-4-prepare-the-code"></a>步驟 4： 準備程式碼 

此 [GitHub 範例](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET web 應用程式會產生識別碼權杖，並裝載在 Azure AD B2C 中使用 "id_token_hint" 參數所需的中繼資料端點。


### <a name="configure-your-policy"></a>設定您的原則

針對對稱和非對稱方法， `id_token_hint` 會從類型為的協調流程步驟呼叫技術設定檔， `GetClaims` 而且必須指定信賴憑證者原則的輸入宣告。

1. 將 IdTokenHint_ExtractClaims 技術設定檔新增到您的延伸模組原則。
1. 將下列協調流程步驟新增至使用者旅程圖，作為第一個專案。  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. 在您的信賴憑證者原則中，重複您在 IdTokenHint_ExtractClaims 技術設定檔中設定的相同輸入宣告。 例如：
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

視您的商務需求而定，您可能需要新增權杖驗證，例如檢查電子郵件地址的格式。 若要這樣做，請新增可叫用 [宣告轉換技術設定檔](claims-transformation-technical-profile.md)的協調流程步驟。 此外，也請新增自行判斷提示的 [技術設定檔](self-asserted-technical-profile.md) ，以顯示錯誤訊息。 

### <a name="create-and-sign-a-token"></a>建立和簽署權杖

GitHub 範例說明如何建立這類權杖，以在稍後以查詢字串參數形式傳送的 JWT 發出 JWT `id_token_hint` 。 以下是使用 id_token_hint 參數的授權要求範例
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>後續步驟

- 在 Azure AD B2C 社區 GitHub 存放庫上，檢查 [ [使用邀請電子郵件註冊](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) ] 解決方案。
