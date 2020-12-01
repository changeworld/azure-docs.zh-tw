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
ms.openlocfilehash: 7fc65b27c3c02d6102210ae277690795d763d91a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338252"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing 實體搜尋 API 端點

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。


Bing 實體搜尋 API 有一個端點，該端點會根據查詢從 Web 傳回實體。 這些搜尋結果會以 JSON 傳回。

## <a name="get-entity-results-from-the-endpoint"></a>從端點取得實體結果

若要使用 **Bing API** 取得實體結果，請將 `GET` 要求傳送至下列端點。 使用 [headers](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) 和 [query 參數](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)來自訂您的搜尋要求。 搜尋要求可以使用 `?q=` 參數來傳送。

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [什麼是 Bing 實體搜尋 API？](overview.md)

## <a name="see-also"></a>另請參閱 

如需標頭、參數、市場代碼、回應物件、錯誤等項目的詳細資訊，請參閱 [Bing 實體搜尋 API v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 參考文章。