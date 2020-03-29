---
title: 快取驗證權杖
titleSuffix: Azure Cognitive Services
description: 本文將介紹如何緩存身份驗證權杖。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946292"
---
# <a name="how-to-cache-the-authentication-token"></a>如何緩存身份驗證權杖

本文演示如何緩存身份驗證權杖以提高應用程式的性能。

## <a name="using-aspnet"></a>使用ASP.NET

導入用於獲取權杖的**Microsoft.身份模型.用戶端.ActiveDirectory** NuGet 包。 接下來，使用以下代碼獲取 的`AuthenticationResult`，使用[創建沉浸式讀取器資源](./how-to-create-immersive-reader.md)時獲得的身份驗證值。

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

該`AuthenticationResult`物件具有一`AccessToken`個屬性，該屬性是使用 SDK 啟動沉浸式讀取器時將使用的實際權杖。 它還具有一個`ExpiresOn`屬性，表示權杖何時過期。 在啟動沉浸式讀取器之前，您可以檢查權杖是否已過期，並且僅在權杖過期時才獲取新權杖。

## <a name="using-nodejs"></a>使用節點.JS

將[**請求**](https://www.npmjs.com/package/request)npm 包添加到您的專案。 使用以下代碼獲取權杖，使用[創建沉浸式讀取器資源](./how-to-create-immersive-reader.md)時獲得的身份驗證值。

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

該`expires_on`屬性是權杖過期的日期和時間，表示為自 1970 年 1 月 1 日 UTC 以來的秒數。 在嘗試獲取新權杖之前，使用此值確定權杖是否已過期。

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK 參考](./reference.md)