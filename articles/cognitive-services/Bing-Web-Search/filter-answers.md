---
title: 如何篩選搜尋結果 - Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: 您可以使用"回應篩選器"查詢參數篩選必應在回應中包括的答案類型（例如圖像、視頻和新聞）。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221379"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>篩選搜尋回應所包含的答案  

當您查詢 Web 時，Bing 會傳回它針對該搜尋找到的所有相關內容。 例如，如果搜尋查詢為 "sailing + dinghies"，回應可能包含下列答案：

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>查詢參數

要篩選必應返回的答案，在調用 API 時請使用以下查詢參數。  

### <a name="responsefilter"></a>回應篩選器

您可以使用[回應Filter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter)查詢參數（即表示的"答案"清單）來篩選必應在回應中包含的答案類型（例如圖像、視頻和新聞）。 如果必應找到相關內容，回應中將包含答案。 

要從回應（如圖像）中排除特定答案，請先`-`為答案類型預置字元。 例如：

```
&responseFilter=-images,-videos
```

下列範例示範如何使用 `responseFilter` 來要求 sailing dinghies (小帆艇) 的影像、影片和新聞。 當您為查詢字串編碼時，逗號會變更為 %2C。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

以下顯示前一個查詢的回應。 由於 Bing 找不到相關的影片和新聞結果，因此，回應不會包含它們。

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

雖然 Bing 未在前一個回應中傳回影片和新聞結果，但不表示影片和新聞內容不存在。 這只是表示此頁面未包含它們。 不過，如果您[翻閱](./paging-webpages.md)更多結果，後續的頁面可能就會包含它們。 此外，如果您直接呼叫[影片搜尋 API](../bing-video-search/search-the-web.md) 和[新聞搜尋 API](../bing-news-search/search-the-web.md) 端點，回應很可能就會包含結果。

不建議您使用 `responseFilter` 來從單一 API 取得結果。 如果您想要來自單一 Bing API 的內容，請直接呼叫該 API。 例如，如果只要接收影像，將要求傳送到影像搜尋 API 端點 `https://api.cognitive.microsoft.com/bing/v7.0/images/search` 或其他[影像](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints)端點之一。 呼叫單一 API 非常重要，不只是出於效能考量，而且還因為內容特定的 API 會提供更豐富的結果。 例如，您可以使用不適用 Web 搜尋 API 的篩選來篩選結果。  

### <a name="site"></a>網站

要從特定域獲取搜尋結果，請在`site:`查詢字串中包括查詢參數。  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> 視查詢而定，如果您使用 `site:` 查詢運算子，不論 [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) 設定為何，回應都有可能包含成人內容。 只有在您了解網站上的內容，而且您的案例支援成人內容的可能性時，才得以使用 `site:`。

### <a name="freshness"></a>時效性

要將 Web 應答結果限制為必應在特定時間段內發現的網頁，請將[新鮮度](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness)查詢參數設置為以下不區分大小寫的值之一：

* `Day`• 返回必應在過去 24 小時內發現的網頁
* `Week`• 返回必應在過去 7 天內發現的網頁
* `Month`• 返回過去 30 天內發現的網頁

您還可以將此參數設置為表單 中的自訂日期範圍`YYYY-MM-DD..YYYY-MM-DD`。 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

要將結果限制為單個日期，將新鮮度參數設置為特定日期：

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

如果必應與您的篩選準則匹配的網頁數量少於您請求的網頁數（或必應返回的預設編號），則結果可能包括超出指定期限的網頁。

## <a name="limiting-the-number-of-answers-in-the-response"></a>限制回應中的答案數目

必應可以在 JSON 回應中返回多個答案類型。 例如，如果您查詢*sailing_dinghies，* 必應可能會返回`webpages` `images`、`videos`和`relatedSearches`。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

若要將 Bing 傳回的答案數目限制為前兩個答案 (webpages 和 images)，請將 [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) \(英文\) 查詢參數設為 2。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

回應只會包含 `webPages` 和 `images`。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

如果您將 `responseFilter` 查詢參數新增到前一個查詢，並將它設為 webpages 和 news，回應就只會包含 webpages，因為不會將 news 設為順位。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>提升未設為順位的答案

如果 Bing 針對查詢所傳回的前幾個順位的答案為 webpages、images、videos 及 relatedSearches，則回應會包含那些答案。 如果您將 [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) \(英文\) 設為二 (2)，Bing 就會傳回前兩個順位的答案：webpages 和 images。 如果您想要 Bing 在回應中包含 images 和 videos，請指定 [promote](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) \(英文\) 查詢參數，並將它設為 images 和 videos。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

以下為上述要求的回應。 Bing 會傳回前兩個答案 webpages 和 images，並將 videos 提升到答案中。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

如果您將 `promote` 設為 news，回應就不會包含 news 答案，因為它不是已設為順位的答案。您只能提升已設為順位的答案。

您想要提升的答案不會計入 `answerCount` 限制。 例如，如果已設為順位的答案為 news、images 及 videos，而且您將 `answerCount` 設為 1 並將 `promote` 設為 news，回應就會包含 news 和 images。 或者，如果已設為順位的答案為 videos、images 及 news，則回應會包含 videos 和 news。

只有當您指定 `answerCount` 查詢參數時，才能使用 `promote`。
