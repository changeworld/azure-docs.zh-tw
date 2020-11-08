---
title: 使用 Bing 自動建議 API 建議搜尋字詞
titleSuffix: Azure Cognitive Services
description: 本文討論使用 Bing 自動建議 API 建議查詢詞彙的概念，以及查詢長度對相關性的影響。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: cdbbd6afeedc1c8808e02aefa268be4fe0de5f9f
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363369"
---
# <a name="suggesting-query-terms"></a>建議查詢字詞

> [!WARNING]
> Bing 搜尋 Api 會從認知服務移至 Bing 搜尋服務。 從 **2020 年10月 30** 日開始，任何新的 Bing 搜尋實例都必須依照 [此處](https://aka.ms/cogsvcs/bingmove)所述的程式進行布建。
> 接下來的三年或 Enterprise 合約結束之前，將支援使用認知服務布建的 Bing 搜尋 Api （以先發生者為准）。
> 如需遷移指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

一般來說，您會在每次使用者於應用程式的搜尋方塊中鍵入新字元時，呼叫 Bing 自動建議 API。 查詢字串的完整性會影響 API 傳回之建議查詢字詞的相關性。 查詢字串越完整，建議之查詢字詞的相關性就越大。 例如，API 為 `s` 可能傳回的建議也許比查詢為 `sailing dinghies` 傳回的建議相關性較小。

## <a name="example-request"></a>範例要求

下列範例顯示針對 *sail* 傳回建議查詢字串的要求。 請記得在設定 [q](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) 查詢參數時，對使用者的部分查詢字詞進行 URL 編碼。 例如，如果使用者輸入「sailing les」，請將 `q` 設定為 `sailing+les` 或 `sailing%20les`。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

下列回應會包含一份 [SearchAction](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) 物件清單，其中包含建議的查詢字詞。

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>使用建議的查詢字詞

每個建議都包含 `displayText`、`query` 及 `url` 欄位。 `displayText` 欄位包含建議的查詢，您可以用來填入搜尋方塊的下拉式清單。 您必須依提供的順序顯示回應包含的所有建議。

下列範例顯示下拉式搜尋方塊與來自 Bing 自動建議 API 的建議查詢字詞。

![自動建議下拉式搜尋方塊清單](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

如果使用者從下拉式清單選取建議的查詢，您可以使用 `query` 欄位中的查詢字詞呼叫 [Bing Web 搜尋 API](../../bing-web-search/overview.md)，並自行顯示結果。 或者，您也可以改為使用 `url` 欄位中的 URL，將使用者傳送至 Bing 搜尋結果頁面。

## <a name="next-steps"></a>後續步驟

* [什麼是 Bing 自動建議 API？](../get-suggested-search-terms.md)