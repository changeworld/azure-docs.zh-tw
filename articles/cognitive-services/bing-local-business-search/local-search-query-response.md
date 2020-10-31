---
title: 傳送和使用 API 查詢和回應-Bing 當地企業搜尋
titleSuffix: Azure Cognitive Services
description: 使用本文了解如何使用 Bing 當地商家搜尋 API 來傳送和使用搜尋查詢。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 8e26d128fea7208206be8a7329522f8202051215
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095402"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>傳送並使用 Bing 當地商家搜尋 API 查詢和回應

> [!WARNING]
> Bing 搜尋 Api 會從認知服務移至 Bing 搜尋服務。 從 **2020 年10月 30** 日開始，任何新的 Bing 搜尋實例都必須依照 [此處](https://aka.ms/cogsvcs/bingmove)所述的程式進行布建。
> 接下來的三年或 Enterprise 合約結束之前，將支援使用認知服務布建的 Bing 搜尋 Api （以先發生者為准）。
> 如需遷移指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

您可以透過向 Bing 當地商家搜尋 API 的端點傳送搜尋查詢並包含 `Ocp-Apim-Subscription-Key` 標頭 (這是必需的) 來取得其當地結果。 除了可用的[標頭](local-search-reference.md#headers)和[參數](local-search-reference.md#query-parameters)之外，還可以藉由為要搜尋的區域以及傳回之位置的[類別](local-search-query-response.md)指定[地理界限](specify-geographic-search.md)來自訂搜尋。

## <a name="creating-a-request"></a>建立要求

若要將要求傳送至 Bing 當地商家搜尋 API，請在將 `q=` 參數新增至 API 端點之前將搜尋詞彙新增至該參數，並包含 `Ocp-Apim-Subscription-Key` 標頭。 例如：

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

完整要求 URL 的語法如下所示。 如需有關傳送要求的詳細資訊，請參閱 Bing 當地商家搜尋 API [快速入門](quickstarts/local-quickstart.md)，並參考[標頭](local-search-reference.md#headers)和[參數](local-search-reference.md#query-parameters)的內容。 

如需當地搜尋類別的資訊，請參閱 [Bing 當地商家搜尋 API 的搜尋類別](local-categories.md)。

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>使用回應

Bing 當地商家搜尋 API 的 JSON 回應包含 `SearchResponse` 物件。 API 將在 `places` 欄位中傳回相關的搜尋結果。 如果未找到結果，則 `places` 欄位將不會包含在回應中。

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>搜尋結果屬性

API 所傳回的 JSON 結果會包含下列屬性：

* _type
* address
* entityPresentationInfo
* 地區
* id
* NAME
* routeablePoint
* telephone
* url

如需關於標頭、參數、市場代碼、回應物件、錯誤的一般資訊，請參閱 [Bing 當地搜尋 API v7](local-search-reference.md) 參考。

> [!NOTE]
> 您或代表您的第三方可能無法使用、保留、儲存、快取、共用或散發當地搜尋 API 的任何資料，以進行測試、開發、訓練、散發或提供給任何非 Microsoft 服務或功能。 


## <a name="example-json-response"></a>範例 JSON 回應

下列 JSON 回應包含查詢 `?q=restaurant+in+Bellevue` 所指定的搜尋結果。

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>節流要求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>下一步
- [當地商家搜尋快速入門](quickstarts/local-quickstart.md)
- [當地商家搜尋 Java 快速入門](quickstarts/local-search-java-quickstart.md)
- [當地商家搜尋 Node 快速入門](quickstarts/local-search-node-quickstart.md)
- [當地商家搜尋 Python 快速入門](quickstarts/local-search-python-quickstart.md)
