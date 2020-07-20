---
title: 快速入門：將搜尋要求傳送至以 Node.js 撰寫的 REST API - Bing 實體搜尋
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用 C# 來傳送要求給「Bing 實體搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: 194368acd6be65da6a800ad1394ac156a6654b50
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650234"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>快速入門：使用 Node.js 將搜尋要求傳送至 Bing 實體搜尋 REST API

使用本快速入門以第一次呼叫 Bing 實體搜尋 API，並檢視 JSON 回應。 這個簡單的 JavaScript 應用程式會將新聞搜尋查詢傳送給 API，並顯示回應。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js) 上有此範例的原始程式碼。

雖然此應用程式是以 JavaScript 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>Prerequisites

* 最新版的 [Node.js](https://nodejs.org/en/download/)。

* [JavaScript 要求程式庫](https://github.com/request/request)。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 JavaScript 檔案，並設定嚴謹度 和 HTTPS 需求。

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. 建立適用於 API 端點、您訂用帳戶金鑰及搜尋查詢的變數。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. 將您的市場和查詢參數附加至稱為 `query` 的字串。 請務必使用 `encodeURI()` 對您的查詢進行 url 編碼。
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>處理及剖析回應

1. 定義一個名為 `response_handler()` 的函式，此函式會接受 HTTP 呼叫 `response` 作為參數。 

2. 在此函式中，定義一個變數來包含 JSON 回應本文。  
    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. 在呼叫 `data` 旗標時，儲存回應本文。
    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. 出現 `end` 旗標的訊號時，剖析 JSON，然後將其列印出來。

    ```javascript
    response.on ('end', function () {
    let json = JSON.stringify(JSON.parse(body), null, '  ');
    console.log (json);
    });
        ```

## Send a request

1. Create a function called `Search()` to send a search request. In it, perform the following steps:

2. Within this function, create a JSON object containing your request parameters. Use `Get` for the method, and add your host and path information. Add your subscription key to the `Ocp-Apim-Subscription-Key` header. 

3. Use `https.request()` to send the request with the response handler created previously, and your search parameters.
    
   ```javascript
   let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + query,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    
    let req = https.request (request_params, response_handler);
    req.end ();
   }
      ```

2. 呼叫 `Search()` 函式。

## <a name="example-json-response"></a>範例 JSON 回應

如以下範例所示，成功的回應會以 JSON 格式來傳回： 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置單頁 Web 應用程式](../tutorial-bing-entities-search-single-page-app.md)

* [什麼是 Bing 實體搜尋 API？](../overview.md )
* [Bing 實體搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)。
