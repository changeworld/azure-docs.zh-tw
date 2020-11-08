---
title: 使用排名顯示回應 - Bing 實體搜尋
titleSuffix: Azure Cognitive Services
description: 了解如何使用排名來顯示 Bing 實體搜尋 API 傳回的回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365630"
---
# <a name="using-ranking-to-display-entity-search-results"></a>使用排名來顯示實體搜尋結果  

> [!WARNING]
> Bing 搜尋 Api 會從認知服務移至 Bing 搜尋服務。 從 **2020 年10月 30** 日開始，任何新的 Bing 搜尋實例都必須依照 [此處](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程式進行布建。
> 接下來的三年或 Enterprise 合約結束之前，將支援使用認知服務布建的 Bing 搜尋 Api （以先發生者為准）。
> 如需遷移指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

每個實體搜尋回應都包含 [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) 回應，可指定您必須以何種方式顯示 Bing 實體搜尋 API 所傳回的搜尋結果。 排名回應會將結果分組為 pole、mainline 和 sidebar 內容。 pole 結果是最重要或顯著的結果，應該優先顯示。 如果您未以傳統 mainline 和 sidebar 格式顯示其餘結果，您必須提供比 sidebar 內容更高的 mainline 內容可見度。 
  
在每個群組內，[項目](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items)陣列會識別內容必須出現的順序。 每個項目會提供兩種方式來識別回應內的結果。  
 

|欄位 | 說明  |
|---------|---------|
|`answerType` 和 `resultIndex` | `answerType` 會識別回應 (實體或位置)，而 `resultIndex` 會識別回應內的結果 (例如一個實體)。 索引從 0 開始。|
|`value`    | `value` 包含的識別碼會符合回應或回應內結果的識別碼。 回應或結果其中一個 (而非兩者) 會包含識別碼。 |
  
使用 `answerType` 和 `resultIndex` 是一個兩步驟程序。 先使用 `answerType` 來識別哪個回應包含要顯示的結果。 然後使用 `resultIndex` 為回應的結果編製索引，以顯示結果。  (`answerType` 值是 [SearchResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) 物件中的功能變數名稱。 ) 如果您應該將所有答案的結果一起顯示，則排名回應專案不會包含該 `resultIndex` 欄位。

使用識別碼需要您比對排名識別碼與回應或其中一個結果的識別碼。 如果回應物件包含 `id` 欄位，則會同時顯示回應的所有結果。 例如，如果 `Entities` 物件包含 `id` 欄位，則會同時顯示所有實體發行項。 如果 `Entities` 物件不包含 `id` 欄位，則每個實體會包含 `id` 欄位，且排名回應會混合實體與位置結果。  
  
## <a name="ranking-response-example"></a>排名回應範例

以下顯示範例 [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)。
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

根據此排名回應，sidebar 會顯示與 Jimi Hendrix 相關的兩個實體結果。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](tutorial-bing-entities-search-single-page-app.md)