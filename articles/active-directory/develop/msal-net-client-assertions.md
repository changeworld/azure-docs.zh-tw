---
title: 用戶端斷言 （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解在 .NET （MSAL.NET） 的 Microsoft 身份驗證庫中對機密用戶端應用程式的簽名用戶端斷言支援。
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
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050298"
---
# <a name="confidential-client-assertions"></a>機密用戶端斷言

為了證明其身份，機密用戶端應用程式與 Azure AD 交換器密。 秘密可以是：
- 用戶端金鑰（應用程式密碼）。
- 證書，用於生成包含標準聲明的簽名斷言。

此機密也可以直接是一個簽名的斷言。

MSAL.NET有四種方法可以向機密用戶端應用提供憑據或斷言：
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> 雖然可以使用`WithClientAssertion()`API 為機密用戶端獲取權杖，但我們不建議預設情況下使用它，因為它更先進，並且旨在處理非常具體的情況，這些場景並不常見。 使用`.WithCertificate()`API 將允許MSAL.NET為您處理此事務。 此 api 使您能夠在需要時自訂身份驗證請求，但由 它`.WithCertificate()`創建的預設斷言對於大多數身份驗證方案就足夠了。 在某些情況下，MSAL.NET無法在內部執行簽名操作，此 API 也可以用作解決方法。

### <a name="signed-assertions"></a>已簽名的斷言

已簽名的用戶端斷言採用簽名 JWT 的形式，其有效負載包含 Azure AD（Base64）編碼所需的身份驗證聲明。 使用方式：

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Azure AD 預期的聲明包括：

宣告類型 | 值 | 描述
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | "aud"（訪問者）聲明標識 JWT 用於（此處 Azure AD） 的收件者，請參閱 [RFC 7519，第 4.1.3 節]
exp | 2019年 06月 27日 星期四 15：04：17 GMT+0200 （浪漫夏令時） | "exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不得接受 JWT 以進行處理。 請參閱 [RFC 7519，第 4.1.4 節]
iss | [客戶 ID] | "iss"（發行人）聲明標識了簽發 JWT 的主體。 此聲明的處理特定于應用程式。 "iss"值是包含 StringOrURI 值的區分大小寫的字串。 [RFC 7519，第4.1.1節]
jti | （a 吉德） | "jti"（JWT ID）聲明為 JWT 提供了唯一識別碼。 識別碼值的分配方式必須確保同一值意外分配給其他資料物件的概率可以忽略不計;如果應用程式使用多個頒發者，則必須防止不同頒發者生成的值之間的衝突。 "jti"索賠可用於防止重播 JWT。 "jti"值是區分大小寫的字串。 [RFC 7519，第4.1.7節]
nbf | 2019年 06月 27日 星期四 14：54：17 GMT+0200 （浪漫夏令時） | "nbf" (生效時間) 宣告會識別生效時間，在此時間之前不得接受 JWT 以進行處理。 [RFC 7519，第4.1.5節]
sub | [客戶 ID] | "子"（主題）索賠確定了JWT的主題。 JWT 中的索賠通常是有關該主題的陳述。 主題值必須限定為在頒發者上下文中本地唯一或全域唯一。 參見 [RFC 7519， 第 4.1.2 節]

下面是如何編寫這些聲明的示例：

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

以下是如何製作已簽名的用戶端斷言：

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

您還可以選擇使用[Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/)為您創建斷言。 代碼將更加優雅，如下例所示：

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

獲得已簽名的用戶端斷言後，即可將其與 MSAL apis 一起使用，如下所示。

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>與Clientclaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`預設情況下，將生成一個簽名的斷言，其中包含 Azure AD 預期的聲明以及要發送的其他用戶端聲明。 下面是有關如何執行此操作的程式碼片段。

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

如果字典中您傳遞的一項聲明與強制聲明之一相同，則將考慮附加聲明的價值。 它將覆蓋MSAL.NET計算的聲明。

如果要提供自己的聲明（包括 Azure AD 預期的強制聲明），請轉接`false`該`mergeWithDefaultClaims`參數。
