---
title: 什麼是 Bing 實體搜尋 API？
titleSuffix: Azure Cognitive Services
description: 了解 Bing 實體搜尋 API 的詳細資料，以及如何透過搜尋查詢擷取和搜尋實體和地點。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2a3d971ce9a4f89555eb3ffa489f8b19172a4b83
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351464"
---
# <a name="what-is-bing-entity-search-api"></a>什麼是 Bing 實體搜尋 API？

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

Bing 實體搜尋 API 會將搜尋查詢傳送至 Bing，並取得包含實體和地點的結果。 地點結果包含餐廳、旅館或其他本地商家。 若查詢指定本地商家名稱或要求商務類型 (例如我附近的餐廳)，Bing 會傳回地點。 若查詢指定已知人物、地點 (旅遊景點、縣/市、國家/地區等) 或事項，Bing 會傳回實體。

|功能  |描述  |
|---------|---------|
|[即時搜尋建議](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | 提供使用者輸入時可顯示為下拉式清單的搜尋建議。       | 
| [實體去除混淆](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | 針對具有多個可能意義的查詢取得多個實體。 |
| [尋找地點](concepts/search-for-entities.md#find-places) | 搜尋並傳回當地商家和實體的相關資訊  |

## <a name="workflow"></a>工作流程

Bing 實體搜尋 API 是一種 RESTful Web 服務，可讓您輕鬆地從任何可發出 HTTP 要求及剖析 JSON 的程式設計語言進行呼叫。 您可以透過 REST API 或 SDK 來使用此服務。

1. 建立具備 Bing 搜尋 API 存取權的[認知服務 API 帳戶](../cognitive-services-apis-create-account.md)。 如果您沒有 Azure 訂用帳戶，可以[建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。
2. 使用有效的搜尋查詢，將要求傳送至 API。
3. 剖析傳回的 JSON 訊息以處理 API 回應。

## <a name="next-steps"></a>後續步驟

* 嘗試使用 Bing 實體搜尋 API 的[互動式示範](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/)。 
* 若要快速開始使用您的第一個要求，請嘗試使用[快速入門](quickstarts/csharp.md)。
* [Bing 實體搜尋 API v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 參考小節。
* [Bing 使用和顯示需求](../bing-web-search/use-display-requirements.md)指定了透過 Bing 搜尋 API 取得的內容和資訊可行的用法。
* 請瀏覽 [Bing 搜尋 API 中樞頁面](../bing-web-search/overview.md)以探索其他可用的 API。