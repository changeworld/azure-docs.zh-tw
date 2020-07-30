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
ms.openlocfilehash: f0673732bd657b237ed0f4b4db276b8bf9a3a2b5
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424370"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API 錯誤代碼
 
Azure Key Vault web 服務上的作業可能會傳回下列錯誤碼。
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401：未驗證的要求

401表示要求未經驗證而無法 Key Vault。 

在下列情況中，會驗證要求：

- 金鑰保存庫知道呼叫者的身分識別;和
- 呼叫者可以嘗試存取 Key Vault 資源。 

有幾個不同的原因，要求可能會傳回401。

### <a name="no-authentication-token-attached-to-the-request"></a>要求未附加任何驗證權杖。 

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

「授權」標頭是每次呼叫資料平面作業的 Key Vault 時所需的存取權杖。 如果標頭遺失，則回應必須是401。

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Token 缺少與其相關聯的正確資源。 

從 Azure OAUTH 端點要求存取權杖時，必須要有稱為「資源」的參數。 此值對權杖提供者而言很重要，因為它會將權杖的範圍限定于其預期用途。 **所有**用來存取 Key Vault 之權杖的資源都是*HTTPs： \/ /vault.keyvault.net* （不含尾端斜線）。

### <a name="the-token-is-expired"></a>權杖已過期

權杖是以 base64 編碼，而且這些值可以在網站（例如）上解碼 [http://jwt.calebb.net](http://jwt.calebb.net) 。 以下是已解碼的上述權杖：

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

我們可以在此權杖中看到許多重要的部分：

- aud （物件）： token 的資源。 請注意，這是 <https://vault.azure.net> 。 對於未明確符合此值的任何資源（例如圖形），此權杖將無法運作。
- iat （發行于）：發出權杖時，自 epoch 啟動後的滴答數。
- nbf （不早于）：此權杖生效時，自 epoch 啟動後的滴答數。
- exp （到期）：此權杖到期後，epoch 啟動後的滴答數。
- appid （應用程式識別碼）：提出此要求之應用程式識別碼的 GUID。
- tid （租使用者識別碼）：提出此要求之主體的租使用者識別碼 GUID

請務必在權杖中正確識別所有的值，才能讓要求正常運作。 如果所有專案都正確，則要求不會產生401。

### <a name="troubleshooting-401"></a>疑難排解401

在對金鑰保存庫提出要求之前，應該從權杖產生的點調查401s。 通常會使用程式碼來要求權杖。 一旦收到權杖，就會傳遞至 Key Vault 要求。 如果程式碼在本機執行，您可以使用 Fiddler 來捕捉對的要求/回應 `https://login.microsoftonline.com` 。 要求看起來像這樣：

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
- 資源值設定為 HTTPs %3 A %2 F %2 F 保存庫。 azure （URL 已編碼）
- 用戶端識別碼
- 用戶端密碼

請確定要求的其餘部分幾乎完全相同。

如果您只能取得回應存取權杖，您可以對其進行解碼（如上所示），以確保租使用者識別碼、用戶端識別碼（應用程式識別碼）和資源。

## <a name="http-403-insufficient-permissions"></a>HTTP 403：許可權不足

HTTP 403 表示已驗證要求（它知道要求的身分識別），但識別沒有存取所要求資源的許可權。 原因有兩個：

- 沒有身分識別的存取原則。
- 要求的資源的 IP 位址不在金鑰保存庫防火牆設定的白名單中。

當客戶的應用程式未使用客戶所認為的用戶端識別碼時，通常會發生 HTTP 403。 這通常表示未針對實際呼叫身分識別正確設定存取原則。

### <a name="troubleshooting-403"></a>疑難排解403

首先，開啟記錄功能。 如需有關如何執行此操作的指示，請參閱[Azure Key Vault 記錄](logging.md)。

一旦開啟記錄功能，您可以判斷403是因為存取原則或防火牆原則。

#### <a name="error-due-to-firewall-policy"></a>因防火牆原則而產生的錯誤

「用戶端位址（00.00.00.00）未獲授權，而且呼叫者不是受信任的服務」

有一份有限的「Azure 信任的服務」清單。 Azure 網站**不**是受信任的 azure 服務。 如需詳細資訊，請參閱[Azure App Services Key Vault 防火牆存取](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)的 blog 文章。

您必須將 Azure 網站的 IP 位址新增至 Key Vault，才能使其正常執行。

如果因為存取原則，請尋找要求的物件識別碼，並確定物件識別碼與使用者嘗試指派存取原則的物件相符。 AAD 中通常會有多個具有相同名稱的物件，因此選擇正確的一個非常重要。 藉由刪除並重新新增存取原則，可以查看是否有多個物件具有相同名稱。

此外，大部分的存取原則都不需要使用如入口網站中所示的「授權的應用程式」。 授權的應用程式用於「代理者」驗證案例，這種情況很罕見。 


## <a name="http-429-too-many-requests"></a>HTTP 429：要求太多

當要求數目超過時間範圍的指定最大值時，就會進行節流。 如果發生節流，Key Vault 的回應將會是 HTTP 429。 針對提出的要求類型，會有最大的指定。 例如：建立 HSM 2048 位金鑰是每10秒5個要求，但所有其他 HSM 交易都有1000要求/10 秒的限制。 因此，請務必瞭解在判斷節流的原因時，會進行哪些類型的呼叫。
一般來說，對 Key Vault 的要求限制為2000個要求/10 秒。 例外狀況是重要的作業，如[Key Vault 服務限制](service-limits.md)中所述

### <a name="troubleshooting-429"></a>疑難排解429
節流處理是使用下列技巧來解決：

- 判斷要求的資源是否有模式並嘗試在呼叫應用程式中快取，以減少對 Key Vault 提出的要求數目。 

- 發生 Key Vault 節流時，請調整要求的程式碼，以使用指數輪詢重試。 這裡會說明此演算法：[如何節流您的應用程式](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- 如果快取無法減少要求數目，而且計時輪詢無法運作，請考慮將金鑰分割成多個金鑰保存庫。 單一訂用帳戶的服務限制是個別 Key Vault 限制的5倍。 如果使用5個以上的金鑰保存庫，則應提供考慮使用多個訂用帳戶。 

如需更詳細的指引，包括增加限制的要求，請參閱這裡： [Key Vault 節流指導](overview-throttling.md)方針


