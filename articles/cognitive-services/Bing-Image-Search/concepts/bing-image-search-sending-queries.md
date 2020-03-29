---
title: 自訂和建議圖像搜索查詢 - 必應圖像搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何自訂傳送至 Bing 影像搜尋 API 的搜尋查詢。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: d833b017004365e9dad7241e360f42ff41a55883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542747"
---
# <a name="customize-and-suggest-image-search-queries"></a>自訂和建議圖像搜索查詢

使用本文瞭解如何自訂查詢，並建議將搜索詞發送到必應圖像搜索 API。

## <a name="suggest-search-terms"></a>建議搜索詞

如果您的應用程式有用來輸入搜尋字詞的搜尋方塊，您可以使用[Bing 自動建議 API](../../bing-autosuggest/get-suggested-search-terms.md) 來改善體驗。 API 會即時顯示建議的搜尋字詞。 API 會根據部分搜尋字詞和認知服務，傳回建議的查詢字串。

## <a name="pivot-the-query"></a>樞紐分析查詢

如果 Bing 可以分割原始搜尋查詢，則傳回的 [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) 物件會包含 `pivotSuggestions` 欄位。 樞紐分析建議能以選用搜尋字詞的形式向使用者呈現。 例如，如果原始查詢為 Microsoft Surface**，則 Bing 可能會將查詢分割為 Microsoft** 和 Surface**，並提供每個字詞的建議樞紐。 這些建議能以選用查詢自詞的形式向使用者呈現。

下列範例示範 Microsoft Surface** 的樞紐建議：  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

`pivotSuggestions` 欄位包含原始查詢細分成的區段 (樞紐) 清單。 對於每個樞紐，回應會包含一份[查詢](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj)物件清單，其中包含建議的查詢。 `text` 欄位包含建議的查詢。 `displayText` 欄位包含的字詞可取代原始查詢中的樞紐分析。 例如 Surface 的發行日期。

如果樞紐查詢字串就是使用者所要尋找的內容，您可以使用 `text` 和 `thumbnail` 欄位對使用者顯示樞紐查詢字串。 使用 `webSearchUrl` URL 或 `searchLink` URL，讓縮圖和文字可以點選。 使用 `webSearchUrl` 將使用者傳送給 Bing 搜尋結果。 如果您提供的是自己的結果頁面，請使用 `searchLink`。

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>擴展查詢

如果 Bing 可以擴展查詢來縮小原始搜尋範圍，則 [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) 物件會包含 `queryExpansions` 欄位。 例如，如果查詢為 *Microsoft Surface*，則擴展後的查詢可能會是：
- Microsoft Surface **Pro 3**。
- Microsoft Surface **RT**。
- Microsoft Surface **Phone**。
- Microsoft Surface **Hub**。

下列範例示範 *Microsoft Surface* 的擴展查詢。

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

`queryExpansions` 欄位包含 [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) 物件的清單。 `text` 欄位包含擴展的查詢。 `displayText` 欄位包含擴展字詞。 如果擴展查詢字串就是使用者所要尋找的內容，您可以使用 `text` 和 `thumbnail` 欄位顯示擴展查詢字串。 使用 `webSearchUrl` URL 或 `searchLink` URL，讓縮圖和文字可以點選。 使用 `webSearchUrl` 將使用者傳送給 Bing 搜尋結果。 如果您提供的是自己的結果頁面，請使用 `searchLink`。

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>節流要求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>後續步驟

如果您從未試用過 Bing 影像搜尋 API，請透過[快速入門](../quickstarts/csharp.md)試用此功能。 如果您想要尋找更複雜的內容，請嘗試可建立[單頁 Web 應用程式](../tutorial-bing-image-search-single-page-app.md)的教學課程。
