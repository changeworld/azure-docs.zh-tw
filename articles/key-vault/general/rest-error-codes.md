---
title: REST API 錯誤代碼 ─ Azure 金鑰保管庫
description: 這些錯誤代碼可以通過 Azure 密鑰保管庫 Web 服務上的操作返回。
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: bbb30c0ad41babca4158391c9e4e5c5d4d25cbf9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432056"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure 金鑰保存庫 REST API 錯誤代碼
 
Azure 密鑰保管庫 Web 服務上的操作可以返回以下錯誤代碼。
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401:未經身份驗證的請求

401 表示對密鑰保管庫的請求未進行身份驗證。 

如果:

- 密鑰保管庫知道調用方的身份;和
- 允許調用方嘗試訪問密鑰保管庫資源。 

請求返回 401 的原因有幾個不同。

### <a name="no-authentication-token-attached-to-the-request"></a>未附加到請求的身份驗證權杖。 

下面是 PUT 請求的範例,該請求設定機密的值:

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

"授權"標頭是每次呼叫金鑰保管庫進行資料平面操作時都需要的存取權杖。 如果標頭丟失,則回應必須為 401。

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>令牌缺少與其關聯的正確資源。 

從 Azure OAUTH 終結點請求訪問權杖時,必須使用名為"資源"的參數。 該值對於權杖提供程式非常重要,因為它將權杖範圍限定為其預期用途。 訪問金鑰保管庫**的所有**權杖的資源為*Ht:/vault.keyvault.net(\/* 沒有尾隨斜杠)。

### <a name="the-token-is-expired"></a>權杖已過期

令牌是基64編碼的,可以在網站(如[http://jwt.calebb.net](http://jwt.calebb.net)) 解碼值。 下面是上述解碼的權杖:

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

我們可以看到此權杖中的許多重要部分:

- aud(訪問者):權杖的資源。 請注意,這是<https://vault.azure.net>。 此令牌不適用於與此值不顯式匹配的任何資源,如圖形。
- iat(在)):自頒發權杖時開始的時代開始以來的刻度數。
- nbf(以前):自此令牌生效時,自紀元開始以來的刻度數。
- exp(過期):自此令牌過期時,自紀元開始以來的刻度數。
- 應用程式 ID(應用程式 ID):發出此請求的應用程式 ID 的 GUID。
- tid(租戶 ID):發出此請求的委託人租戶 ID 的 GUID

請務必在令牌中正確標識所有值,以便請求正常工作。 如果一切正確,則請求不會導致 401。

### <a name="troubleshooting-401"></a>損毀排除 401

在向密鑰保管庫發出請求之前,應從令牌生成點對 401s 進行調查。 通常,代碼用於請求權杖。 收到權杖後,它將傳遞到密鑰保管庫請求中。 如果代碼在本地運行,則可以使用 Fiddler 捕`https://login.microsoftonline.com`獲對 的請求/回應。 要求如下所示:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

以下使用者提供的資訊模糊是正確的:

- 金鑰保存庫租戶 ID
- 資源值設定為 hs%3A%2F%2Fvault.azure.net(URL 編碼)
- 用戶端識別碼
- 用戶端密碼

確保請求的其餘部分幾乎相同。

如果只能獲取回應訪問權杖,則可以對其進行解碼(如上所述),以確保租戶 ID、用戶端 ID(應用 ID)和資源。

## <a name="http-403-insufficient-permissions"></a>HTTP 403: 權限不足

HTTP 403 表示請求已過身份驗證(它知道請求標識),但標識沒有訪問請求資源的許可權。 有兩個原因:

- 標識沒有訪問策略。
- 請求資源的 IP 位址未在密鑰保管庫的防火牆設置中白名單。

當客戶的應用程式不使用客戶認為的用戶端 ID 時,通常會發生 HTTP 403。 這通常意味著訪問策略未正確設置為實際調用標識。

### <a name="troubleshooting-403"></a>損毀排除 403

首先,打開日誌記錄。 有關如何執行此操作的說明,請參閱[Azure 金鑰保管庫紀錄記錄](logging.md)。

打開記錄後,您可以確定 403 是否是由於存取策略或防火牆策略。

#### <a name="error-due-to-firewall-policy"></a>防火牆原則導致的錯誤

用戶端位址 (00.00.00.00) 未獲得授權,呼叫者不是受信任的服務

"Azure 受信任服務"清單有限。 Azure 網站**不是**受信任的 Azure 服務。 有關詳細資訊,請參閱[由 Azure 應用服務訪問密鑰保管庫防火牆的](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)部落格文章。

您必須將 Azure 網站的 IP 位址添加到金鑰保管庫,以便它正常工作。

如果由於存取策略:查找請求的物件 ID,並確保物件 ID 與用戶嘗試為其分配存取策略的物件匹配。 AAD 中通常有多個具有相同名稱的物件,因此選擇正確的物件非常重要。 通過刪除和重新添加訪問策略,可以查看是否存在同名多個物件。

此外,大多數訪問策略不需要使用門戶中顯示的"授權應用程式」。 授權應用程式用於"代表"身份驗證方案,這種情況很少見。 


## <a name="http-429-too-many-requests"></a>HTTP 429: 要求太多

當請求數超過規定的時間範圍的最大值時,將發生限制。 如果發生限制,密鑰保管庫的回應將為 HTTP 429。 對於發出的請求類型,有聲明的最大值。 例如:創建 HSM 2048 位元金鑰是每 10 秒 5 個請求,但所有其他 HSM 事務都有 1000 個請求/10 秒的限制。 因此,在確定限制的原因時,瞭解正在進行的呼叫類型非常重要。
通常,對金鑰保管庫的請求限制為 2000 個請求/10 秒。 例外是金鑰操作,如[金鑰保管庫服務限制中](service-limits.md)所述

### <a name="troubleshooting-429"></a>損毀排除 429
使用以下技術進行限制:

- 通過確定請求的資源是否存在模式並嘗試在調用應用程式中緩存這些模式,減少向密鑰保管庫發出的請求數。 

- 當發生密鑰保管庫限制時,調整請求代碼以使用指數迴轉重試。 此處解釋了該演演演算法:[如何限制應用](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- 如果無法通過緩存減少請求數,並且定時回退不起作用,則請考慮將密鑰拆分為多個密鑰保管庫。 單個訂閱的服務限制是單個密鑰保管庫限制的 5 倍。 如果使用超過 5 個密鑰保管庫,應考慮使用多個訂閱。 

詳細指南,包括請求增加限制,可在此處找到:[密鑰保管庫限制指南](overview-throttling.md)


