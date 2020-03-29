---
title: 如何流覽搜尋結果 - 必應搜索 API
titleSuffix: Azure Cognitive Services
description: 瞭解如何從必應搜索 API 中流覽搜尋結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481736"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>如何翻閱必應搜索 API 的結果

當您向必應 Web、自訂、圖像、新聞或視頻搜索 API 發送呼叫時，必應返回可能與查詢相關的結果總數的子集。 要獲取估計的可用結果總數，請訪問答案物件的`totalEstimatedMatches`欄位。 

例如： 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>通過搜尋結果進行分頁

要翻閱可用結果，請在發送請求時`count`使用`offset`和 查詢參數。  

> [!NOTE]
>
> * 使用必應視頻、圖像和新聞 API 進行尋呼僅適用于常規視頻 （）、`/video/search`新聞`/news/search`（ ）`/image/search`和圖像 （ ） 搜索。 不支援通過趨勢主題和類別進行分頁。  
> * 該`TotalEstimatedMatches`欄位是當前查詢的搜尋結果總數的估計。 設置 和`count``offset`參數時，此估計值可能會更改。

| 參數 | 描述                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 指定要在回應中傳回的結果數目。 請注意，`count`的預設值和可能請求的最大結果數因 API 而異。 您可以在["後續步驟](#next-steps)"下的參考文檔中找到這些值。 |
| `offset`  | 指定要略過的結果數目。 `offset` 是以零為起始，而且應該小於 (`totalEstimatedMatches` - `count`)。                                           |

例如，如果要每頁顯示 15 個結果，則`count`設置為 15 和`offset`0 以獲取結果的第一頁。 對於每個後續 API 呼叫，您將`offset`增加 15。 以下範例要求 15 個於位移 45 開始的網頁。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果使用預設值`count`，則只需在 API 呼叫中指定`offset`查詢參數。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

使用必應圖像和視頻 API 時，可以使用 該`nextOffset`值來避免重複搜尋結果。 從`Images`或`Videos`回應物件獲取該值，並在請求中使用 參數。 `offset`  

> [!NOTE]
> 必應 Web 搜索 API 返回搜尋結果，這些結果可以包括網頁、圖像、視頻和新聞。 當您翻閱 Bing Web 搜索 API 的搜尋結果時，您只分頁[網頁](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)，而不是其他答案類型，如圖像或新聞。 物件中的`WebPage`搜尋結果可能還包括出現在其他答案類型中的結果。
>
> 如果使用`responseFilter`查詢參數而不指定任何篩選器值，請不要使用 和`count``offset`參數。 

## <a name="next-steps"></a>後續步驟

* [什麼是必應 Web 搜索 API？](bing-api-comparison.md)
* [Bing Web 搜尋 API v7參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [必應自訂搜索 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [必應新聞搜索 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [必應視頻搜索 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [必應圖像搜索 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
