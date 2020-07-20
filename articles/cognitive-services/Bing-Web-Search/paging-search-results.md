---
title: 如何逐頁流覽搜尋結果-Bing 搜尋 Api
titleSuffix: Azure Cognitive Services
description: 瞭解如何從 Bing 搜尋 Api 逐頁流覽搜尋結果。
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73481736"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>如何逐頁流覽 Bing 搜尋 Api 的結果

當您傳送對 Bing Web、自訂、影像、新聞或影片搜尋 Api 的呼叫時，Bing 會傳回可能與查詢相關之結果總數的子集。 若要取得可用結果的預估總數，請存取解答物件的`totalEstimatedMatches`欄位。 

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

## <a name="paging-through-search-results"></a>搜尋結果的分頁

若要逐頁流覽可用的結果，請`count`在`offset`傳送要求時使用和查詢參數。  

> [!NOTE]
>
> * 使用 Bing 影片、影像和新聞 Api 進行分頁僅適用于一般影片（`/video/search`）、新聞（`/news/search`）和影像（`/image/search`）搜尋。 不支援透過趨勢主題和類別進行分頁。  
> * `TotalEstimatedMatches`欄位是目前查詢的搜尋結果總數預估值。 當您設定`count`和`offset`參數時，此估計值可能會變更。

| 參數 | 描述                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 指定要在回應中傳回的結果數目。 請注意`count`，的預設值，以及您可能要求的最大結果數目會依 API 而有所不同。 您可以在 [[後續步驟]](#next-steps)下的參考檔中找到這些值。 |
| `offset`  | 指定要略過的結果數目。 `offset` 是以零為起始，而且應該小於 (`totalEstimatedMatches` - `count`)。                                           |

例如，如果您想要每頁顯示15個結果，您可以將設`count`為15， `offset`並將設定為0，以取得結果的第一頁。 針對每個後續的 API 呼叫，您`offset`會遞增15。 以下範例要求 15 個於位移 45 開始的網頁。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果您使用預設`count`值，您只需要在 API 呼叫中`offset`指定查詢參數。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

使用 Bing 影像和影片 Api 時，您可以使用`nextOffset`值來避免重複的搜尋結果。 取得`Images`或`Videos`回應物件中的值，並使用`offset`參數將其用於您的要求中。  

> [!NOTE]
> Bing Web 搜尋 API 會傳回可包含網頁、影像、影片和新聞的搜尋結果。 當您從 Bing Web 搜尋 API 逐頁流覽搜尋結果時，您只會分頁，而不是其他回應[類型，例如](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)影像或新聞。 物件中`WebPage`的搜尋結果可能會包含出現在其他答案類型中的結果。
>
> 如果您使用`responseFilter`查詢參數，但未指定任何篩選值，請不要`count`使用`offset`和參數。 

## <a name="next-steps"></a>後續步驟

* [Bing Web 搜尋 Api 有哪些？](bing-api-comparison.md)
* [Bing Web 搜尋 API v7參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing 自訂搜尋 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing 新聞搜尋 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing 影片搜尋 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing 影像搜尋 API v7 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
