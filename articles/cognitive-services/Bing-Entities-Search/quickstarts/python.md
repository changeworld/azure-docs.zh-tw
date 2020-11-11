---
title: 快速入門：將搜尋要求傳送至以 Python 撰寫的 REST API - Bing 實體搜尋
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用 Python 來傳送要求給「Bing 實體搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 1b7c19c06e663fc959389a26710c5c748a9654e8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369132"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-python"></a>快速入門：使用 Python 將搜尋要求傳送至 Bing 實體搜尋 REST API

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

使用本快速入門以第一次呼叫 Bing 實體搜尋 API，並檢視 JSON 回應。 這個簡單的 Python 應用程式會將新聞搜尋查詢傳送給 API，並顯示回應。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) 上有此範例的原始程式碼。

雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>Prerequisites

* [Python](https://www.python.org/downloads/) 2.x 或 3.x

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列匯入項目。 針對您的訂用帳戶金鑰、端點、市場和搜尋查詢建立變數。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```python
    import http.client, urllib.parse
    import json
    
    subscriptionKey = 'ENTER YOUR KEY HERE'
    host = 'api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

2. 藉由將市場變數附加至 `?mkt=` 參數來建立要求 url。 對查詢進行 Url 編碼，並將其附加至 `&q=` 參數。 
    
    ```python
    params = '?mkt=' + mkt + '&q=' + urllib.parse.quote (query)
    ```

## <a name="send-a-request-and-get-a-response"></a>傳送要求並取得回應

1. 建立稱為 `get_suggestions()` 的函式。 

2. 在此函式中，使用 `Ocp-Apim-Subscription-Key` 作為金鑰，將訂用帳戶金鑰新增至字典中。

3. 使用 `http.client.HTTPSConnection()` 建立 HTTPS 用戶端物件。 使用 `request()` 與路徑和參數以及標頭資訊來傳送 `GET` 要求。

4. 使用 `getresponse()` 儲存回應，並傳回 `response.read()`。

   ```python
   def get_suggestions ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read()
   ```

5. 呼叫 `get_suggestions()`，並列印 JOSN 回應。

    ```python
    result = get_suggestions ()
    print (json.dumps(json.loads(result), indent=4))
    ```

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

* [什麼是 Bing 實體搜尋 API？](../overview.md)
* [Bing 實體搜尋 API 參考](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)。