---
title: Microsoft 身分識別平台的憑證認證
titleSuffix: Microsoft identity platform
description: 本文討論如何註冊及使用憑證認證來進行應用程式驗證。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 77e34e4a18012f15b9e907e3b9efc1965b98f824
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612115"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft 身分識別平台的應用程式驗證憑證認證

Microsoft 身分識別平臺可讓應用程式在可使用用戶端密碼的任何地方使用自己的認證來進行驗證，例如，在 OAuth 2.0  [用戶端認證授](v2-oauth2-client-creds-grant-flow.md) 與流程和代理 [者 (OBO](v2-oauth2-on-behalf-of-flow.md)) 流程中。

應用程式可用於驗證的一種認證格式是 [JSON Web 權杖](./security-tokens.md#json-web-tokens-jwts-and-claims) ， (JWT) 使用應用程式所擁有之憑證簽署的判斷提示。

## <a name="assertion-format"></a>判斷提示格式

若要計算判斷提示，您可以使用您選擇的語言中的許多 JWT 程式庫之一， [MSAL 使用 `.WithCertificate()` 來支援此](msal-net-client-assertions.md)功能。 這項資訊會由權杖在其[標頭](#header)、[宣告](#claims-payload)和簽章中[攜帶。](#signature)

### <a name="header"></a>頁首

| 參數 |  備註 |
| --- | --- |
| `alg` | 應該是 **RS256** |
| `typ` | 應該是 **JWT** |
| `x5t` | X.509 憑證雜湊的 (也稱為 cert 的 SHA-1 *指紋*) 十六進位標記法，以 Base64 字串值編碼。 例如，假設有 (Hex) 的 x.509 憑證雜湊 `84E05C1D98BCE3A5421D225B140B36E86A3D5534` ，則宣告會 `x5t` `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64) 。 |

### <a name="claims-payload"></a>宣告 (承載)

宣告類型 | 值 | 描述
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | 「Aud」 (物件) 宣告會識別 JWT 適用于此處 (的收件者 Azure AD) 請參閱 [RFC 7519，4.1.3 一節](https://tools.ietf.org/html/rfc7519#section-4.1.3)。  在此情況下，該收件者是 (login.microsoftonline.com) 的登入伺服器。
exp | 1601519414 | "exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不得接受 JWT 以進行處理。 請參閱 [RFC 7519，4.1.4 一節](https://tools.ietf.org/html/rfc7519#section-4.1.4)。  如此一來，就可以使用判斷提示，直到那次為止，因此最短為5-10 分鐘 `nbf` 。  Azure AD 不會限制 `exp` 目前的時間。 
iss | ClientID | 「Iss」 (簽發者) 宣告可識別發出 JWT 的主體，在此案例中為您的用戶端應用程式。  使用 GUID 應用程式識別碼。
jti |  (Guid)  | "Jti" (JWT ID) 宣告會提供 JWT 的唯一識別碼。 您必須指派識別碼值，以確保不會將相同的值不慎指派給不同的資料物件，因此會有一個明顯的可能性。如果應用程式使用多個簽發者，則在不同簽發者所產生的值之間也必須防止衝突。 "Jti" 值是區分大小寫的字串。 [RFC 7519，Section 4.1。7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | "nbf" (生效時間) 宣告會識別生效時間，在此時間之前不得接受 JWT 以進行處理。 [RFC 7519，區段 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5)。  使用目前的時間是適當的。 
sub | ClientID | 「子」 (主體) 宣告會識別 JWT 的主旨，在此案例中也是您的應用程式。 使用與相同的值 `iss` 。 

### <a name="signature"></a>簽章

簽章的計算方式是將憑證套用至 [JSON Web 權杖 RFC7519 規格](https://tools.ietf.org/html/rfc7519)中所述。

## <a name="example-of-a-decoded-jwt-assertion"></a>已解碼的 JWT 判斷提示範例

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>已編碼的 JWT 判斷提示範例

下列字串是已編碼判斷提示的範例。 如果您仔細看看，您會發現三個以點分隔的區段 (`.`) ：

* 第一個區段會編碼 *標頭*
* 第二個區段會將 *宣告* (承載進行編碼) 
* 最後一個區段是使用前兩個區段內容中的憑證 *來計算的* 簽章

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>向 Microsoft 身分識別平台註冊憑證

您可使用以下任一種方法，透過 Azure 入口網站在 Microsoft 身分識別平台中建立憑證認證與用戶端應用程式之間的關聯：

### <a name="uploading-the-certificate-file"></a>上傳憑證檔案

在用戶端應用程式的 Azure 應用程式註冊中：
1. 選取 [憑證和秘密]。
2. 按一下 [上傳憑證]，然後選取要上傳的憑證檔案。
3. 按一下 [新增] 。
  上傳憑證之後，即會顯示指紋、開始日期和到期值。

### <a name="updating-the-application-manifest"></a>更新應用程式資訊清單

擁有憑證之後，您必須計算︰

- `$base64Thumbprint` -Base64 編碼的憑證雜湊值
- `$base64Value` -以 Base64 編碼的憑證原始資料值

您也必須提供 GUID 來識別應用程式資訊清單中的金鑰 (`$keyId`)。

在用戶端應用程式的 Azure 應用程式註冊中：
1. 選取 [資訊清單]，以開啟應用程式資訊清單。
2. 使用下列結構描述，將 *keyCredentials* 屬性取代為您新的憑證資訊。

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. 儲存對應用程式資訊清單所做的編輯，然後將資訊清單上傳到 Microsoft 身分識別平台。

   `keyCredentials` 屬性是多重值，因此您可以上傳多個憑證以進行更豐富的金鑰管理。
   
## <a name="using-a-client-assertion"></a>使用用戶端判斷提示

用戶端判斷提示可以在使用用戶端密碼的任何地方使用。  例如，在 [授權碼流程](v2-oauth2-auth-code-flow.md)中，您可以傳入， `client_secret` 以證明要求是來自您的應用程式。 您可以使用和參數來取代此 `client_assertion` `client_assertion_type` 參數。 

| 參數 | 值 | 描述|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| 這是固定值，表示您使用的是憑證認證。 |
|`client_assertion`| JWT |這是上面建立的 JWT。 |

## <a name="next-steps"></a>後續步驟

MSAL.NET 程式庫會以一行程式碼 [處理此案例](msal-net-client-assertions.md) 。

在 GitHub 上 [使用 Microsoft 身分識別平臺程式碼範例的 .Net Core daemon 主控台應用程式](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) ，會顯示應用程式如何使用自己的認證進行驗證。 它也會說明如何使用 PowerShell Cmdlet 來 [建立自我簽署憑證](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) `New-SelfSignedCertificate` 。 您也可以使用範例存放庫中的 [應用程式建立腳本](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) 來建立憑證、計算指紋等等。
