---
title: Bing 新聞搜尋端點
titleSuffix: Azure Cognitive Services
description: 本文提供新聞搜尋 API 端點的摘要;新聞、熱門新聞和趨勢新聞。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 8c724925a12535c561b035296e374691f3fb2689
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098343"
---
# <a name="bing-news-search-api-endpoints"></a>Bing 新聞搜尋 API 端點

> [!WARNING]
> Bing 搜尋 Api 會從認知服務移至 Bing 搜尋服務。 從 **2020 年10月 30** 日開始，任何新的 Bing 搜尋實例都必須依照 [此處](https://aka.ms/cogsvcs/bingmove)所述的程式進行布建。
> 接下來的三年或 Enterprise 合約結束之前，將支援使用認知服務布建的 Bing 搜尋 Api （以先發生者為准）。
> 如需遷移指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

**新聞搜尋 API** 會傳回新聞文章、網頁、影像、影片和 [實體](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)。 實體包含人員、地點或主題的摘要資訊。

## <a name="endpoints"></a>端點

若要使用「Bing 新聞搜尋 API」取得新聞搜尋結果，請將 `GET` 要求傳送至下列其中一個端點。 標頭和 URL 參數可定義進一步的規格。

### <a name="news-items-by-search-query"></a>依搜尋查詢的新聞項目

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

根據搜尋查詢傳回新聞項目。 如果搜尋查詢是空的，則 API 將傳回不同類別的熱門新聞文章。 傳送查詢，做法是將搜尋字詞進行 url 編碼，然後將 `q=""` 參數加上該字詞。 如需可用性，請參閱 [支援的國家/地區與市場](language-support.md#supported-markets-for-news-search-endpoint)。

### <a name="top-news-items-by-category"></a>依類別的熱門新聞項目

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

依照類別傳回熱門新聞項目。 您可以使用 `category=business``category=sports` 或 `category=entertainment`，特別要求熱門商業、運動或娛樂文章。  `category` 參數只能搭配 `/news` URL 使用。 指定類別時有一些正式需求；請參閱[查詢參數](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters)文件中的 `category`。 傳送查詢，做法是將搜尋字詞進行 url 編碼，然後將 `q=""` 參數加上該字詞。 如需可用性，請參閱 [支援的國家/地區與市場](language-support.md#supported-markets-for-news-endpoint)。

### <a name="trending-news-topics"></a>趨勢新聞主題 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

傳回社交網路上目前的趨勢新聞主題。 若包含 `/trendingtopics` 選項，Bing 搜尋會忽略其他幾個參數，例如 `freshness` 和 `?q=""`。 如需可用性，請參閱 [支援的國家/地區與市場](language-support.md#supported-markets-for-news-trending-endpoint)。

## <a name="next-steps"></a>下一步

如需關於標頭、參數、市場代碼、回應物件、錯誤的詳細資料，請參閱 [Bing 新聞搜尋 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) 參考。

如需每個端點支援的參數完整相關資訊，請參閱每種類型的參考頁面。
如需透過範例了解使用新聞搜尋 API 的基本要求，請參閱 [Bing 新聞搜尋快速入門](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)。
