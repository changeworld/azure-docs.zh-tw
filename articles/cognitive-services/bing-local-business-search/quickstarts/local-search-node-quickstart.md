---
title: 快速入門 - 傳送查詢至以 Node.js 撰寫的 API - Bing 當地企業搜尋
titleSuffix: Azure Cognitive Services
description: 使用此快速入門開始向 Bing 當地商家搜尋 API 傳送要求，該 API 是 Azure 認知服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 512ecd44d0d6fd6edc07fb7dc1508c062dd27f78
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095164"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>快速入門：使用 Node.js 向 Bing 當地商家搜尋 API 傳送查詢

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照[這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

使用此快速入門了解如何向 Bing 當地企業搜尋 API 傳送要求，該 API 是 Azure 認知服務。 雖然此簡單應用程式是以 Node.js 撰寫，但 API 是一種與任何程式語言相容的 RESTful Web 服務，可產生 HTTP 要求，並剖析 JSON。

此範例應用程式會從 API 取得搜尋查詢的當地回應資料。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Node.js](https://nodejs.org/en/download/)。
* [JavaScript 要求程式庫](https://github.com/request/request)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="建立 Bing 搜尋資源"  target="_blank">建立 Bing 搜尋資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。


## <a name="code-scenario"></a>程式碼案例

下列程式碼會定義並傳送要求，這會在下列步驟中實作：

1. 宣告變數以依主機及路徑指定端點。
2. 指定查詢，並新增查詢參數。
3. 建立回應的處理常式函式。
4. 定義建立要求的搜尋函式，並新增 `Ocp-Apim-Subscription-Key` 標題。
5. 執行搜尋函式。


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>後續步驟

* [當地企業搜尋 C# 快速入門](local-quickstart.md)
* [當地商家搜尋 Java 快速入門](local-search-java-quickstart.md)
* [當地商家搜尋 Python 快速入門](local-search-python-quickstart.md)
