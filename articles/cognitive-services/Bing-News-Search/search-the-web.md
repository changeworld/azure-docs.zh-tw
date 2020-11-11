---
title: 什麼是 Bing 新聞搜尋 API？
titleSuffix: Azure Cognitive Services
description: 了解如何使用「Bing 新聞搜尋 API」來搜尋 Web，以尋找類別 (包括頭條和趨勢主題) 之間目前的頭條。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 2f793c675b045f995d3f8b208e11b60bc2140ec9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367143"
---
# <a name="what-is-the-bing-news-search-api"></a>什麼是 Bing 新聞搜尋 API？

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

Bing 新聞搜尋 API 可讓您輕鬆地將 Bing 的認知新聞搜尋功能整合到應用程式中。 API 提供類似於 [Bing 新聞](https://www.bing.com/news)的體驗，讓您可傳送搜尋查詢，並接收相關的新聞文章。

請注意，Bing 新聞搜尋 API 僅提供新聞搜尋結果。 對於其他類型的 Web 內容，請使用 [Bing Web 搜尋 API](../bing-web-search/overview.md)、[影片搜尋 API](../bing-video-search/overview.md) 和[影像搜尋 API](../bing-image-search/overview.md)。

## <a name="bing-news-search-api-features"></a>Bing 新聞搜尋 API 功能

Bing 新聞搜尋 API 主要用於尋找及傳回相關的新聞文章，同時也提供多個在 Web 上智慧擷取重點新聞的功能。

|功能  |描述  |
|---------|---------|
|[建議和使用搜尋字詞](concepts/search-for-news.md#suggest-and-use-search-terms)     | 使用 [Bing 自動建議 API](../bing-autosuggest/get-suggested-search-terms.md) 隨著使用者的輸入顯示建議的搜尋字詞，以改善您的搜尋體驗。         |
|[取得一般新聞](concepts/search-for-news.md#get-general-news)     | 藉由將搜尋查詢傳送至 Bing 新聞搜尋 API 來尋找新聞，並取得相關新聞文章的清單。           |
|[今天的熱門新聞](concepts/search-for-news.md#get-todays-top-news)      | 取得當天所有類別中的熱門新聞事件。       |
|[各類別的新聞](concepts/search-for-news.md)     | 搜尋特定類別的新聞。        | 
|[頭條新聞](concepts/search-for-news.md)     | 搜尋所有類別中的熱門頭條新聞。         |

## <a name="workflow"></a>工作流程

Bing 新聞搜尋 API 是一種 RESTful Web 服務，因此可輕易地從任何可發出 HTTP 要求及剖析 JSON 的程式設計語言呼叫。 您可以透過 REST API 或 SDK 來使用此服務。

1. 建立具備 Bing 搜尋 API 存取權的[認知服務 API 帳戶](../cognitive-services-apis-create-account.md)。 如果您沒有 Azure 訂用帳戶，可以[建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。
2. 使用有效的搜尋查詢，將要求傳送至 API。
3. 剖析傳回的 JSON 訊息以處理 API 回應。

## <a name="next-steps"></a>後續步驟

首先，嘗試使用 Bing 新聞搜尋 API 的[互動式示範](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)。 此示範將說明如何快速地自訂搜尋查詢，並尋找 Web 上的新聞。

若要快速開始進行您的第一個 API 要求，請嘗試使用 [REST API](./csharp.md) 或其中一個 [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp) 的快速入門。

## <a name="see-also"></a>另請參閱

* [Bing 新聞搜尋 API v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) 參考章節包含您在要求以影像為基礎的搜尋結果時所能使用的端點、標頭、API 回應和查詢參數的定義和資訊。
* [Bing 使用和顯示需求](../bing-web-search/use-display-requirements.md)指定了透過 Bing 搜尋 API 取得的內容和資訊可行的用法。
* 請瀏覽 [Bing 搜尋 API 中樞頁面](../bing-web-search/overview.md)以探索其他可用的 API。