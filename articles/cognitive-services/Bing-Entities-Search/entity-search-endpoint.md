---
title: Bing 實體搜尋 API 端點
titleSuffix: Azure Cognitive Services
description: Bing 實體搜尋 API 有一個端點，該端點會根據查詢從 Web 傳回實體。 這些搜尋結果會以 JSON 傳回。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 5d6b0a6be0d99815c4d14bb04e56241d011812b5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084947"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing 實體搜尋 API 端點

> [!WARNING]
> Bing 搜尋 Api 會從認知服務移至 Bing 搜尋服務。 從 **2020 年10月 30** 日開始，任何新的 Bing 搜尋實例都必須依照 [此處](https://aka.ms/cogsvcs/bingmove)所述的程式進行布建。
> 接下來的三年或 Enterprise 合約結束之前，將支援使用認知服務布建的 Bing 搜尋 Api （以先發生者為准）。
> 如需遷移指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。


Bing 實體搜尋 API 有一個端點，該端點會根據查詢從 Web 傳回實體。 這些搜尋結果會以 JSON 傳回。

## <a name="get-entity-results-from-the-endpoint"></a>從端點取得實體結果

若要使用 **Bing API** 取得實體結果，請將 `GET` 要求傳送至下列端點。 使用 [headers](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) 和 [query 參數](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)來自訂您的搜尋要求。 搜尋要求可以使用 `?q=` 參數來傳送。

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [什麼是 Bing 實體搜尋 API？](overview.md)

## <a name="see-also"></a>另請參閱 

如需標頭、參數、市場代碼、回應物件、錯誤等項目的詳細資訊，請參閱 [Bing 實體搜尋 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 參考文章。
