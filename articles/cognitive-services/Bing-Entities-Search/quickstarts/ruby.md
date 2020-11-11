---
title: 快速入門：將搜尋要求傳送至以 Ruby 撰寫的 REST API - Bing 實體搜尋
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用 Ruby 來傳送要求給「Bing 實體搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: ee050759181b5d28577e3a47620cce8b1cf9a847
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365681"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-ruby"></a>快速入門：使用 Ruby 將搜尋要求傳送至 Bing 實體搜尋 REST API

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

使用本快速入門以第一次呼叫 Bing 實體搜尋 API，並檢視 JSON 回應。 這個簡單的 Ruby 應用程式會將新聞搜尋查詢傳送給 API，並顯示回應。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingEntitySearchv7.rb) 上找到此應用程式的原始程式碼。

雖然此應用程式是以 Ruby 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>Prerequisites

* [Ruby 2.4 或更新版本](https://www.ruby-lang.org/en/downloads/) (英文)。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的 IDE 或程式碼編輯器中，建立新聞 Ruby 檔案並匯入下列套件：

    ```ruby
    require 'net/https'
    require 'cgi'
    require 'json'
    ```

2. 建立適用於 API 端點、新聞搜尋 URL、訂用帳戶金鑰及搜尋查詢的變數。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。
    
    ```ruby
    host = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/entities'
    
    mkt = 'en-US'
    query = 'italian restaurants near me'
    ```

## <a name="format-and-make-an-api-request"></a>製作 API 要求格式並提出 API 要求

1. 藉由將市場變數附加至 `?mkt=` 參數來為要求建立參數字串。 對查詢進行編碼，並將其附加至 `&q=` 參數。 結合 API 主機、路徑和要求參數，並將其轉換為 URI 物件。

    ```ruby
    params = '?mkt=' + mkt + '&q=' + CGI.escape(query)
    uri = URI (host + path + params)
    ```

2. 使用上一個步驟中的變數來建立要求。 將訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。

    ```ruby
    request = Net::HTTP::Get.new(uri)
    request['Ocp-Apim-Subscription-Key'] = subscriptionKey
    ```

3. 傳送要求並列印回應。

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request (request)
    end

    puts JSON::pretty_generate (JSON (response.body))
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