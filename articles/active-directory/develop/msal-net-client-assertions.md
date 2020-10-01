---
title: 用戶端判斷提示 (MSAL.NET) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解適用于 .NET 的 Microsoft 驗證程式庫中的機密用戶端應用程式的已簽署用戶端判斷提示支援 (MSAL.NET) 。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/30/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: bb1ce0a8ba568dc651accdc5f8c84e9c2c980e73
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612807"
---
# <a name="confidential-client-assertions"></a>機密用戶端判斷提示

為了證明其身分識別，機密用戶端應用程式會與 Azure AD 交換秘密。 密碼可以是：
-  (應用程式密碼) 的用戶端密碼。
- 憑證，用來建立包含標準宣告的帶正負號的宣告。

這個秘密也可以直接是帶正負號的判斷提示。

MSAL.NET 有四種方法可提供認證或判斷提示至機密用戶端應用程式：
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> 雖然您可以使用 `WithClientAssertion()` API 來取得機密用戶端的權杖，但我們不建議您預設使用它，因為它較為先進，而且是設計來處理不常見的非常特定案例。 使用 `.WithCertificate()` API 可讓 MSAL.NET 為您處理這種情況。 此 api 可讓您視需要自訂驗證要求，但所建立的預設判斷提示 `.WithCertificate()` 將足以滿足大部分的驗證案例。 在某些情況下，此 API 也可用來做為因應措施，MSAL.NET 無法在內部執行簽署作業。

### <a name="signed-assertions"></a>簽署的判斷提示

帶正負號的用戶端判斷提示會採用已簽署的 JWT 格式，其中包含由 Azure AD （Base64 編碼）所規定的必要驗證宣告。 使用方式：

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

[Azure AD 所預期的宣告](active-directory-certificate-credentials.md)如下：

宣告類型 | 值 | 說明
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | 「Aud」 (物件) 宣告會識別 JWT 適用于此處 (的收件者 Azure AD) 請參閱 [RFC 7519，4.1.3 一節](https://tools.ietf.org/html/rfc7519#section-4.1.3)。  在此情況下，該收件者是 (login.microsoftonline.com) 的登入伺服器。
exp | 1601519414 | "exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不得接受 JWT 以進行處理。 請參閱 [RFC 7519，4.1.4 一節](https://tools.ietf.org/html/rfc7519#section-4.1.4)。  如此一來，就可以使用判斷提示，直到那次為止，因此最短為5-10 分鐘 `nbf` 。  Azure AD 不會限制 `exp` 目前的時間。 
iss | ClientID | 「Iss」 (簽發者) 宣告可識別發出 JWT 的主體，在此案例中為您的用戶端應用程式。  使用 GUID 應用程式識別碼。
jti |  (Guid)  | "Jti" (JWT ID) 宣告會提供 JWT 的唯一識別碼。 您必須指派識別碼值，以確保不會將相同的值不慎指派給不同的資料物件，因此會有一個明顯的可能性。如果應用程式使用多個簽發者，則在不同簽發者所產生的值之間也必須防止衝突。 "Jti" 值是區分大小寫的字串。 [RFC 7519，Section 4.1。7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | "nbf" (生效時間) 宣告會識別生效時間，在此時間之前不得接受 JWT 以進行處理。 [RFC 7519，區段 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5)。  使用目前的時間是適當的。 
sub | ClientID | 「子」 (主體) 宣告會識別 JWT 的主旨，在此案例中也是您的應用程式。 使用與相同的值 `iss` 。 

以下是如何製作這些宣告的範例：

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

以下說明如何製作簽署的用戶端判斷提示：

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>替代方法

您也可以選擇使用 [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) 來為您建立判斷提示。 程式碼會比較簡潔，如下列範例所示：

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

當您有已簽署的用戶端判斷提示之後，您可以將它與 MSAL api 搭配使用，如下所示。

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` 根據預設，會產生包含 Azure AD 所預期宣告的帶正負號的宣告，再加上您想要傳送的額外用戶端宣告。 以下是如何進行這項操作的程式碼片段。

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

如果您傳入的字典中的其中一個宣告與其中一個強制宣告相同，則會將額外的宣告值納入考慮。 它會覆寫 MSAL.NET 所計算的宣告。

如果您想要提供自己的宣告（包括 Azure AD 所預期的強制宣告），請傳入 `false` `mergeWithDefaultClaims` 參數。
