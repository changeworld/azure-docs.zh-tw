---
title: 用戶端判斷提示（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）中的機密用戶端應用程式的已簽署用戶端判斷提示支援。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3d73e803a31867bedbd0ff069b8c9321257b78cb
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695563"
---
# <a name="confidential-client-assertions"></a>機密用戶端判斷提示

為了證明其身分識別，機密用戶端應用程式會以 Azure AD 交換秘密。 秘密可以是：
- 用戶端秘密（應用程式密碼）。
- 憑證，用來建立包含標準宣告的帶正負號的判斷提示。

這個秘密也可以是直接簽署的判斷提示。

MSAL.NET 有四種方法可將認證或判斷提示提供給機密用戶端應用程式：
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> 雖然您可以使用 `WithClientAssertion()` API 來取得機密用戶端的權杖，但我們不建議預設使用它，因為它更先進，而且是設計用來處理不常見的非常特定案例。 使用 `.WithCertificate()` API 可讓 MSAL.NET 為您處理這種情況。 此 api 可讓您視需要自訂驗證要求，但 `.WithCertificate()` 所建立的預設判斷提示將足以滿足大部分的驗證案例。 在某些情況下，如果 MSAL.NET 無法在內部執行簽署作業，此 API 也可作為因應措施。

### <a name="signed-assertions"></a>帶正負號的判斷提示

已簽署的用戶端判斷提示會採用帶正負號的 JWT 格式，其承載包含 Azure AD，Base64 編碼的必要驗證宣告。 使用方式：

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Azure AD 所需的宣告為：

宣告類型 | 值 | 說明
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | "Aud" （物件）宣告可識別 JWT 適用的收件者（此處 Azure AD），請參閱 [RFC 7519，區段 4.1.3]
exp | 星期四6月 27 2019 15:04:17 GMT + 0200 （羅馬日光節約時間） | "exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不得接受 JWT 以進行處理。 請參閱 [RFC 7519，第4.1.4 節]
iss | ClientID | "Iss" （簽發者）宣告會識別發出 JWT 的主體。 此宣告的處理是應用程式特有的。 「Iss」值是區分大小寫的字串，其中包含 StringOrURI 值。 [RFC 7519，區段 4.1.1]
jti | （Guid） | "Jti" （JWT ID）宣告會提供 JWT 的唯一識別碼。 識別碼值必須以確保不會意外地將相同值指派給不同資料物件的機率來指派：如果應用程式使用多個簽發者，則必須在不同簽發者所產生的值之間避免衝突。 "Jti" 宣告可以用來防止 JWT 重新執行。 "Jti" 值是區分大小寫的字串。 [RFC 7519，區段 4.1.7]
nbf | 星期四6月 27 2019 14:54:17 GMT + 0200 （羅馬日光節約時間） | "nbf" (生效時間) 宣告會識別生效時間，在此時間之前不得接受 JWT 以進行處理。 [RFC 7519，區段 4.1.5]
sub | ClientID | "Sub" （subject）宣告會識別 JWT 的主旨。 JWT 中的宣告通常是關於主體的陳述。 主旨值的範圍必須是在簽發者內容中為本地唯一的，或必須是全域唯一的。 請參閱 [RFC 7519，第4.1.2 節]

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

以下說明如何製作已簽署的用戶端判斷提示：

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

您也可以選擇使用[Microsoft.identitymodel jsonwebtoken](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/)來為您建立判斷提示。 這段程式碼會更簡潔，如下列範例所示：

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

一旦您擁有已簽署的用戶端判斷提示之後，您就可以將它與 MSAL api 搭配使用，如下所示。

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` 預設會產生帶正負號的判斷提示，其中包含 Azure AD 加上您想要傳送的其他用戶端宣告所預期的宣告。 以下是如何執行此動作的程式碼片段。

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

如果您傳入字典中的其中一個宣告與其中一個強制宣告相同，則會將額外的宣告值納入考慮。 它會覆寫 MSAL.NET 所計算的宣告。

如果您想要提供自己的宣告，包括 Azure AD 所預期的必要宣告，請傳入 `false` 以進行 `mergeWithDefaultClaims` 參數。
