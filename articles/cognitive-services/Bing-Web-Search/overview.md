---
title: 什麼是 Bing Web 搜尋 API？
titleSuffix: Azure Cognitive Services
description: Bing Web 搜尋 API 是一項可即時解答網路搜尋查詢的 RESTful 服務。 設定結果以包含網頁、影像、影片、新聞和其他資訊。 結果會以 JSON 的形式提供，並以搜尋相關性和您的 Bing Web 搜尋訂用帳戶為基礎。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/31/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: e312265705161dd1cfa23856c9f6dfb71997ec82
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381094"
---
# <a name="what-is-the-bing-web-search-api"></a>什麼是 Bing Web 搜尋 API？

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

Bing Web 搜尋 API 是一項可即時解答使用者查詢的 RESTful 服務。 搜尋結果可經由簡單的設定，將網頁、影像、影片、新聞、翻譯等項目包含在內。 Bing Web 搜尋會根據搜尋相關性和您的 Bing Web 搜尋訂用帳戶，以 JSON 的形式提供結果。

如果應用程式需要存取所有與使用者的搜尋查詢有關的內容，就非常適合使用此 API。 如果您要建置的應用程式只需要特定類型的結果，請考慮使用 [Bing 影像搜尋 API](../Bing-Image-Search/overview.md)、[Bing 影片搜尋 API](../bing-video-search/overview.md) 或 [Bing 新聞搜尋 API](../Bing-News-Search/search-the-web.md)。 請參閱[認知服務 API](../index.yml)，以取得 Bing 搜尋 API 的完整清單。

想了解其運作方式嗎？ 請觀看我們的 [Bing Web 搜尋 API 示範](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)。

## <a name="features"></a>特性  

Bing Web 搜尋不只可讓您存取即時解答。 還提供可讓您為使用者自訂搜尋結果的其他特性和功能。

| 功能 | 描述 |
|---------|-------------|
| [即時建議搜尋字詞](../bing-autosuggest/get-suggested-search-terms.md) | 使用 Bing 自動建議 API 隨著使用者的輸入顯示建議的搜尋字詞，以改善您的應用程式體驗。 |
| [依內容類型篩選及限制結果](filter-answers.md) | 使用篩選條件和查詢參數，自訂及精簡網頁、影像、影片、安全搜尋的搜尋結果。 |
| [Unicode 字元的搜尋結果醒目提示](hit-highlighting.md) | 透過搜尋結果醒目提示，在對使用者顯示搜尋結果之前先從中找出並移除不必要的 Unicode 字元。 |
| [依國家/地區、區域和/或市場將搜尋結果當地語系化](./language-support.md) | Bing Web 搜尋可支援三十多個國家/地區或區域。 使用這項功能可精簡特定國家/區域或市場的搜尋結果。 |
| [使用 Bing 統計資料分析搜尋計量](bing-web-stats.md) | Bing 統計資料是付費訂閱，可提供關於呼叫量、熱門查詢字串、地理分佈等項目的分析。 |

## <a name="workflow"></a>工作流程

Bing Web 搜尋 API 可輕易地從任何可發出 HTTP 要求及剖析 JSON 回應的程式設計語言呼叫。 此服務可使用 [REST API](quickstarts/python.md) 或 [Bing Web 搜尋用戶端程式庫](./quickstarts/client-libraries.md)來存取。

1. 為 Bing 搜尋 API [建立 Azure 資源](../cognitive-services-apis-create-account.md)。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。  
2. [將要求傳送至 Bing Web 搜尋 API](quickstarts/python.md)。
3. 剖析 JSON 回應。

## <a name="next-steps"></a>後續步驟

* 使用我們的 [Python 快速入門](./quickstarts/client-libraries.md?pivots=programming-language-python)，發出您對 Bing Web 搜尋 API 的第一個呼叫。  
* [建置單頁 Web 應用程式](tutorial-bing-web-search-single-page-app.md)。
* 檢閱 [Web 搜尋 API v7 參考](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)文件。  
* 深入了解 Bing Web 搜尋的[使用和顯示需求](./use-display-requirements.md)。