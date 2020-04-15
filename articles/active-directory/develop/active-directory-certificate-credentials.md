---
title: 微軟標識平台證書認證
titleSuffix: Microsoft identity platform
description: 本文討論了證書憑據對應用程式身份驗證的註冊和使用。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d834d37f686279facf0052e3c8f4da3c8d259e0b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309683"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>微軟識別平台應用程式身份驗證憑證認證

Microsoft 標識平台允許應用程式使用自己的認證進行身份驗證,例如,在[OAuth 2.0用戶端認證授權的授予流2.0](v2-oauth2-client-creds-grant-flow.md)和[代表流](v2-oauth2-on-behalf-of-flow.md)中。

應用程式可用於驗證的認證形式之一，是以應用程式擁有的憑證簽署的 JSON Web 權杖 (JWT) 判斷提示。

## <a name="assertion-format"></a>判斷提示格式
Microsoft 識別平台 要計算斷言,可以使用您選擇的語言中的許多[JSON Web 令牌](https://jwt.ms/)庫之一。 權杖所承載的資訊如下︰

### <a name="header"></a>頁首

| 參數 |  備註 |
| --- | --- |
| `alg` | 應該是 **RS256** |
| `typ` | 應該是 **JWT** |
| `x5t` | 應該是 X.509 憑證 SHA-1 憑證指紋 |

### <a name="claims-payload"></a>宣告 (承載)

| 參數 |  備註 |
| --- | --- |
| `aud` | 受眾:應**https://login.microsoftonline.com/*tenant_Id*tenant_Id/auth2/token** |
| `exp` | 到期日：權杖到期的日期。 時間會表示為從 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 到權杖有效時間到期的秒數。|
| `iss` | 簽發者︰應該是 client_id (用戶端服務的應用程式識別碼) |
| `jti` | GUID：JWT ID |
| `nbf` | 生效時間：無法在此日期之前使用權杖。 時間會表示為從 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 到權杖發出時間的秒數。 |
| `sub` | 主旨：對於 `iss`，應該是 client_id (用戶端服務的應用程式識別碼) |

### <a name="signature"></a>簽章

簽章是使用 [JSON Web 權杖 RFC7519 規格](https://tools.ietf.org/html/rfc7519) 中所述的憑證計算的

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

下列字串是已編碼判斷提示的範例。 如果您仔細看，會發現三個以點 (.) 分隔的區段：
* 第一個區段編碼標頭
* 第二個區段編碼承載
* 最後一個區段則是使用前兩個區段的內容中包含的憑證計算出來的簽章

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>透過 Microsoft 身份平台註冊憑證

可以使用以下任一方法將證書認證與 Microsoft 識別平臺中的用戶端應用程式關聯:

### <a name="uploading-the-certificate-file"></a>上傳憑證檔案

在用戶端應用程式的 Azure 應用程式註冊中：
1. 選取 [憑證和秘密]****。
2. 按下 **「上傳證書**」並選擇要上傳的憑證檔。
3. 按一下 **[新增]**。
  上載證書後,將顯示指紋、開始日期和過期值。

### <a name="updating-the-application-manifest"></a>更新應用程式資訊清單

擁有憑證之後，您必須計算︰

- `$base64Thumbprint`，這是憑證雜湊的 base64 編碼
- `$base64Value`，它是憑證未經處理資料的 base64 編碼

您也必須提供 GUID 來識別應用程式資訊清單中的金鑰 (`$keyId`)。

在用戶端應用程式的 Azure 應用程式註冊中：
1. 選擇 **「清單」** 以打開應用程式清單。
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
3. 將編輯內容保存到應用程式清單,然後將該清單上載到 Microsoft 標識平臺。

   `keyCredentials` 屬性是多重值，因此您可以上傳多個憑證以進行更豐富的金鑰管理。

## <a name="code-sample"></a>程式碼範例

> [!NOTE]
> 您必須使用證書的哈希將 X5T 標頭轉換為基 64 字串來計算它。 在 C# 中執行此操作的`System.Convert.ToBase64String(cert.GetCertHash());`代碼為 。

代碼示例[.NET Core 守護程式主控台應用程式使用 Microsoft 識別平台](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)顯示應用程式如何使用自己的認證進行身份驗證。 此外也說明如何使用 `New-SelfSignedCertificate` PowerShell 命令[建立自我簽署憑證](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script)。 您也可以利用[應用程式建立指令碼](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md)來建立憑證、計算指紋，和執行其他作業。
