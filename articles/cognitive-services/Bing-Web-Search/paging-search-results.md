---
title: 如何逐頁查看搜尋結果-Bing 搜尋 Api
titleSuffix: Azure Cognitive Services
description: 瞭解如何從 Bing 搜尋 Api 逐頁查看搜尋結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: 670460759a9495de735da35ae9f3d8388e59e0e5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350614"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>如何逐頁流覽 Bing 搜尋 Api 的結果

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

當您將呼叫傳送至 Bing Web、自訂、影像、新聞或影片搜尋 Api 時，Bing 會傳回可能與查詢相關之結果總數的子集。 若要取得估計的可用結果總數，請存取回應物件的 `totalEstimatedMatches` 欄位。 

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

## <a name="paging-through-search-results"></a>逐頁查看搜尋結果

若要逐頁流覽可用的結果，請 `count` `offset` 在傳送要求時使用和查詢參數。  

> [!NOTE]
>
> * 使用 Bing 影片、影像和新聞 Api 進行分頁只適用于一般影片 (`/video/search`) 、新聞 (`/news/search`) 和影像 (`/image/search`) 搜尋。 不支援透過趨勢主題和類別進行分頁。  
> * 此 `TotalEstimatedMatches` 欄位是目前查詢之搜尋結果總數的估計值。 當您設定 `count` 和 `offset` 參數時，此預估值可能會變更。

| 參數 | 說明                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 指定要在回應中傳回的結果數目。 請注意，的預設值 `count` 和您可能要求的結果數目上限會依 API 而有所不同。 您可以在 [後續步驟](#next-steps)下的參考檔中找到這些值。 |
| `offset`  | 指定要略過的結果數目。 `offset` 是以零為起始，而且應該小於 (`totalEstimatedMatches` - `count`)。                                           |

例如，如果您想要每頁顯示15個結果，您可以將設 `count` 為15，並設定 `offset` 為0以取得結果的第一頁。 針對每個後續的 API 呼叫，您會遞增 `offset` 15。 以下範例要求 15 個於位移 45 開始的網頁。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果您使用預設 `count` 值，則只需要 `offset` 在您的 API 呼叫中指定查詢參數。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

使用 Bing 影像和影片 Api 時，您可以使用此 `nextOffset` 值來避免重複搜尋結果。 取得 `Images` 或回應物件中的值 `Videos` ，並使用參數在您的要求中使用它 `offset` 。  

> [!NOTE]
> Bing Web 搜尋 API 會傳回可包含網頁、影像、影片和新聞的搜尋結果。 當您從 Bing Web 搜尋 API 逐頁查看搜尋結果時，您只會逐頁查看 [網頁](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)，而不是其他答案類型（例如影像或新聞）。 物件中的搜尋結果 `WebPage` 可能也包含出現在其他答案類型中的結果。
>
> 如果您在 `responseFilter` 未指定任何篩選值的情況下使用 query 參數，請不要使用 `count` 和 `offset` 參數。 

## <a name="next-steps"></a>後續步驟

* [Bing Web 搜尋 Api 有哪些？](bing-api-comparison.md)
* [Bing Web 搜尋 API v7參考](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing 自訂搜尋 API v7 參考](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing 新聞搜尋 API v7 參考](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing 影片搜尋 API v7 參考](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing 影像搜尋 API v7 參考](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)