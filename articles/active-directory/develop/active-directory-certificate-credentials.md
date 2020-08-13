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
ms.date: 08/12/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 06f15257148342879a164005a8f4fb302c539e67
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163657"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft 身分識別平台的應用程式驗證憑證認證

Microsoft 身分識別平臺可讓應用程式使用自己的認證進行驗證，例如，在 OAuth 2.0[用戶端認證授](v2-oauth2-client-creds-grant-flow.md)與流程中，以及代理[者 (OBO](v2-oauth2-on-behalf-of-flow.md)) 流程。

應用程式可用於驗證的一種認證形式是[JSON Web 權杖](./security-tokens.md#json-web-tokens-jwts-and-claims)， (JWT) 判斷提示，並以應用程式擁有的憑證簽署。

## <a name="assertion-format"></a>判斷提示格式

若要計算判斷提示，您可以使用所選語言中的許多 JWT 程式庫其中之一。 此資訊會由權杖在[標頭](#header)、[宣告](#claims-payload)和簽章中[攜帶。](#signature)

### <a name="header"></a>頁首

| 參數 |  備註 |
| --- | --- |
| `alg` | 應該是 **RS256** |
| `typ` | 應該是 **JWT** |
| `x5t` | X.509 憑證雜湊 (也稱為 cert 的 SHA-1*指紋*，) 編碼為 Base64 字串值。 例如，如果指定了的 x.509 憑證雜湊 `84E05C1D98BCE3A5421D225B140B36E86A3D5534` ，則宣告 `x5t` 會是 `hOBcHZi846VCHSJbFAs26Go9VTQ` 。 |

### <a name="claims-payload"></a>宣告 (承載)

| 參數 |  備註 |
| --- | --- |
| `aud` | 物件：應該是`https://login.microsoftonline.com/<your-tenant-id>/oauth2/token` |
| `exp` | 到期日：權杖到期的日期。 時間會表示為從 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 到權杖有效時間到期的秒數。 我們建議使用較短的到期時間-10 分鐘到1小時。|
| `iss` | 簽發者：應該是用戶端服務的 client_id (*應用程式 (用戶端) 識別碼*)  |
| `jti` | GUID： JWT 識別碼 |
| `nbf` | 不早于：無法使用權杖的日期。 時間以1970年1月1日 (1970-01-01T0：0： 0Z) UTC 的秒數表示，直到建立判斷提示的時間為止。 |
| `sub` | 主旨：對於 `iss` ，應該是用戶端服務的 client_id (*應用程式 (用戶端) 識別碼*)  |

### <a name="signature"></a>簽名

簽章的計算方式是套用憑證，如[JSON Web 權杖 RFC7519 規格](https://tools.ietf.org/html/rfc7519)中所述。

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

下列字串是已編碼判斷提示的範例。 如果您仔細看一下，您會注意到三個以點分隔的區段， (`.`) ：

* 第一個區段編碼*標頭*
* 第二個區段會將*宣告*編碼 (承載) 
* 最後一節是使用前兩個區段內容中的憑證*所計算的*簽章

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

- `$base64Thumbprint`-以 Base64 編碼的憑證雜湊值
- `$base64Value`-憑證原始資料的 Base64 編碼值

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

## <a name="next-steps"></a>後續步驟

在 GitHub 上[使用 Microsoft 身分識別平臺程式碼範例的 .Net Core daemon 主控台應用程式](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)，會顯示應用程式如何使用自己的認證進行驗證。 它也會說明如何使用 PowerShell Cmdlet[建立自我簽署憑證](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) `New-SelfSignedCertificate` 。 您也可以使用範例存放庫中的[應用程式建立腳本](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md)來建立憑證、計算指紋等等。
