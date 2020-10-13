---
title: REST API 錯誤碼 - Azure Key Vault
description: Azure Key Vault web 服務上的作業可能會傳回這些錯誤碼。
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631217"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API 錯誤碼
 
Azure Key Vault web 服務上的作業可能會傳回下列錯誤碼。
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401：未驗證的要求

401表示要求未經驗證 Key Vault。 

如果有下列情況，則會驗證要求：

- Key vault 知道呼叫者的身分識別;和
- 允許呼叫端嘗試存取 Key Vault 資源。 

有幾個不同的原因可能會導致要求傳回401。

### <a name="no-authentication-token-attached-to-the-request"></a>未將任何驗證權杖附加至要求。 

以下是範例 PUT 要求，設定秘密的值：

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

「授權」標頭是每次呼叫資料平面作業的 Key Vault 時，所需的存取權杖。 如果標頭遺失，則回應必須為401。

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>權杖缺少與其相關聯的正確資源。 

從 Azure OAUTH 端點要求存取權杖時，必須要有一個稱為「資源」的參數。 值對權杖提供者而言很重要，因為它會將權杖的範圍限定在其預定用途。 存取 Key Vault 的 **所有** 權杖的資源是 *HTTPs： \/ /vault.keyvault.net* (，不含尾端斜線) 。

### <a name="the-token-is-expired"></a>權杖已過期

權杖是以 base64 編碼，而且值可以在網站上解碼，例如 [http://jwt.calebb.net](http://jwt.calebb.net) 。 以下是上述權杖解碼：

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

我們可以在這個權杖中看到許多重要的部分：

- aud (物件) ：權杖的資源。 請注意，這是 `https://vault.azure.net` 。 此權杖不適用於任何未明確符合此值（例如圖形）的資源。
- 在) 發出的 iat (：發出權杖時從 epoch 開始起的刻度數目。
- nbf (不在) 之前：當這個權杖生效時，從 epoch 開始起的刻度數目。
- exp (到期) ：此權杖到期之後，從 epoch 開始算起的刻度數目。
- appid (應用程式識別碼) ：提出此要求之應用程式識別碼的 GUID。
- tid (租使用者識別碼) ：提出此要求之主體的租使用者識別碼的 GUID

在權杖中正確識別所有的值，以便讓要求正常運作，是很重要的。 如果一切都正確無誤，則要求不會導致401。

### <a name="troubleshooting-401"></a>疑難排解401

在對金鑰保存庫提出要求之前，應該先從權杖產生的點調查401s。 通常會使用程式碼來要求權杖。 一旦收到權杖，就會將它傳遞至 Key Vault 要求。 如果程式碼是在本機執行，您可以使用 Fiddler 來捕捉的要求/回應 `https://login.microsoftonline.com` 。 要求如下所示：

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

下列使用者提供的資訊必須正確：

- 金鑰保存庫租使用者識別碼
- 資源值設定為 HTTPs %3 A %2 F %2 F 保存庫。 azure (URL 編碼) 
- 用戶端識別碼
- 用戶端密碼

確定要求的其餘部分幾乎相同。

如果您只能取得回應存取權杖，您可以將它解碼 (如上所示) 以確定租使用者識別碼、用戶端識別碼 (應用程式識別碼) 和資源。

## <a name="http-403-insufficient-permissions"></a>HTTP 403：許可權不足

HTTP 403 表示要求已經過驗證 (它知道要求的身分識別) 但身分識別沒有存取所要求資源的許可權。 原因有兩種：

- 沒有身分識別的存取原則。
- 在金鑰保存庫的防火牆設定中，未核准要求資源的 IP 位址。

當客戶的應用程式未使用客戶所認為的用戶端識別碼時，通常會發生 HTTP 403。 這通常表示未正確設定實際呼叫身分識別的存取原則。

### <a name="troubleshooting-403"></a>疑難排解403

首先，開啟記錄。 如需如何進行此作業的指示，請參閱 [Azure Key Vault 記錄](logging.md)。

開啟記錄之後，您可以判斷403是否因為存取原則或防火牆原則而造成。

#### <a name="error-due-to-firewall-policy"></a>因為防火牆原則而發生錯誤

「用戶端位址 (00.00.00.00) 未獲得授權，而且呼叫端不是受信任的服務」

有有限的「Azure 信任服務」清單。 Azure 網站 **並非** 受信任的 azure 服務。 如需詳細資訊，請參閱 [Azure App Services Key Vault 防火牆存取](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)的 blog 文章。

您必須將 Azure 網站的 IP 位址新增至 Key Vault，才能讓它運作。

如果是因為存取原則，請尋找要求的物件識別碼，並確定物件識別碼符合使用者嘗試指派存取原則的物件。 AAD 中通常會有多個具有相同名稱的物件，因此選擇正確的物件是很重要的。 藉由刪除並重新加入存取原則，您就可以查看是否有多個物件具有相同的名稱。

此外，大部分的存取原則都不需要使用「授權的應用程式」，如入口網站中所示。 授權的應用程式可用於「代理者」驗證案例，這種情況很罕見。 


## <a name="http-429-too-many-requests"></a>HTTP 429：要求太多

當要求數目超過指定時間範圍的最大值時，就會進行節流。 如果發生節流，Key Vault 的回應將會是 HTTP 429。 提出的要求類型有指定的最大數目。 例如：建立 HSM 2048 位金鑰是每10秒5個要求，但所有其他 HSM 交易都有1000個要求/10 秒的限制。 因此，請務必瞭解在判斷節流的原因時，會進行何種類型的呼叫。
一般而言，對 Key Vault 的要求限制為每10秒2000個要求。 例外狀況是關鍵作業，如[Key Vault 服務限制](service-limits.md)中所述

### <a name="troubleshooting-429"></a>疑難排解429
節流是使用這些技術來解決：

- 藉由判斷要求的資源是否有模式，並嘗試在呼叫的應用程式中加以快取，減少對 Key Vault 提出的要求數目。 

- 發生 Key Vault 節流時，請調整要求的程式碼，以使用指數輪詢重試。 此演算法會在此說明： [如何對您的應用程式進行節流](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- 如果快取無法減少要求數目，且計時輪詢無法運作，請考慮將金鑰分割成多個金鑰保存庫。 單一訂用帳戶的服務限制是個別 Key Vault 限制的5倍。 如果使用5個以上的金鑰保存庫，請考慮使用多個訂用帳戶。 

您可以在這裡找到詳細的指引，包括增加限制的要求： [Key Vault 節流指導](overview-throttling.md)方針
