---
title: 將搜尋要求傳送至 Bing 實體搜尋 API
titleSuffix: Azure cognitive Services
description: Bing 實體搜尋 API 會將搜尋查詢傳送至 Bing，並取得包含實體和地點的結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: b290539bf6424895a9685c1e4cdb53d8fb8cfb41
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365902"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>將搜尋要求傳送至 Bing 實體搜尋 API

> [!WARNING]
> Bing 搜尋 Api 會從認知服務移至 Bing 搜尋服務。 從 **2020 年10月 30** 日開始，任何新的 Bing 搜尋實例都必須依照 [此處](https://aka.ms/cogsvcs/bingmove)所述的程式進行布建。
> 接下來的三年或 Enterprise 合約結束之前，將支援使用認知服務布建的 Bing 搜尋 Api （以先發生者為准）。
> 如需遷移指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

Bing 實體搜尋 API 會將搜尋查詢傳送至 Bing，並取得包含實體和地點的結果。 地點結果包含餐廳、旅館或其他本地商家。 針對地點，查詢可指定本地商家名稱或要求清單 (例如，我附近的餐廳)。 實體結果包含人員、地點或事項。 此內容中的地點是觀光景點、州、國家/地區等。

## <a name="the-endpoint"></a>端點

若要取得實體和地點搜尋結果，請將 GET 要求傳送至下列端點：  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

要求必須使用 HTTPS 通訊協定。

建議讓所有要求來自伺服器。 隨著用戶端應用程式散佈金鑰，會讓惡意的第三方有更多機會存取該應用程式。 此外，從伺服器進行呼叫，API 未來就能以同個升級點更新版本。

## <a name="specifying-query-parameters-and-headers"></a>指定查詢參數和標頭

要求必須指定 [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) 查詢參數，其中含有使用者的搜尋字詞。 要求也必須指定 [mkt](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt) 查詢參數，其可識別您希望結果來自哪個市場。 如需選擇性查詢參數的清單，請參閱 [查詢參數](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)。 對所有查詢參數進行 URL 編碼。  
  
要求必須指定 [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey) 標頭。 雖然是選擇性的，但我們仍建議使用以下標頭：  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-X-msedge-clientid-ClientID](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-X-msedge-clientid-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

用戶端 IP 和位置標頭對於傳回位置感知內容很重要。  

如需所有要求和回應標頭的清單，請參閱[標頭](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers)。

## <a name="the-request"></a>要求

以下顯示的實體要求包含所有建議的查詢參數和標頭。 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

如果這是您第一次呼叫任何的 Bing API，請勿包含用戶端識別碼標頭。 如果您先前已呼叫 Bing API 且 Bing 傳回了使用者和裝置組合的用戶端識別碼，則只要包含用戶端識別碼。

## <a name="the-response"></a>回應

以下顯示前一個要求的回應。 此範例也示範 Bing 特定回應標頭。 如需回應物件的相關資訊，請參閱 [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse)。

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>後續步驟

* [使用 Bing 實體 API 搜尋實體](search-for-entities.md)
* [Bing API 的使用和顯示需求](../../bing-web-search/use-display-requirements.md)