---
title: 快取驗證權杖
titleSuffix: Azure Cognitive Services
description: 本文將說明如何快取驗證權杖。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946292"
---
# <a name="how-to-cache-the-authentication-token"></a>如何快取驗證權杖

本文示範如何快取驗證權杖，以改善應用程式的效能。

## <a name="using-aspnet"></a>使用 ASP.NET

匯入**microsoft.identitymodel** ，這是用來取得權杖的 NuGet 套件。 接下來，使用下列程式`AuthenticationResult`代碼來取得，使用您在[建立沉浸式讀取器資源](./how-to-create-immersive-reader.md)時所得到的驗證值。

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

`AuthenticationResult`物件具有`AccessToken`屬性，這是您使用 SDK 啟動沉浸式讀取器時將使用的實際 token。 它也具有代表`ExpiresOn`權杖何時到期的屬性。 啟動沉浸式讀取器之前，您可以檢查權杖是否已過期，並只在過期時取得新的權杖。

## <a name="using-nodejs"></a>使用 NODE.JS

將[**要求**](https://www.npmjs.com/package/request)npm 套件新增至您的專案。 使用下列程式碼來取得權杖，使用您在[建立沉浸式讀取器資源](./how-to-create-immersive-reader.md)時所得到的驗證值。

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

`expires_on`屬性是權杖到期的日期和時間，以1970年1月1日之後的秒數表示。 請使用此值來判斷您的權杖是否已過期，再嘗試取得新的權杖。

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