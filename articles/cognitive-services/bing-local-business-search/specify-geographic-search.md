---
title: 使用地理界限來篩選 Bing 當地商家搜尋 API 的結果
titleSuffix: Azure Cognitive Services
description: 使用本文了解如何從 Bing 當地商家搜尋 API 來篩選搜尋結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 2e72abd777ac4834385fd916b730d438b8540203
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490256"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>使用地理界限來篩選 Bing 當地商家搜尋 API 的結果

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

Bing 當地商家搜尋 API 可讓您透過使用 `localCircularView` 或 `localMapView` 查詢參數，在要搜尋的特定地理區域上設定界限。 請務必僅在查詢中使用一個參數。 

如果搜尋字詞包含明確的地理位置，Bing 當地商家搜尋 API 將自動使用它來設定搜尋結果的界限。 例如，如果搜尋字詞為 `sailing in San Diego`，則 `San Diego` 將作為位置使用，並且將忽略查詢參數或使用者標頭中的任何其他指定位置。 

如果未在搜尋詞彙中偵測到地理位置，並且未使用查詢參數指定地理位置，則 Bing 當地商家搜尋 API 將嘗試從要求的 `X-Search-ClientIP` 或 `X-Search-Location` 標頭中判斷位置。 如果未指定標頭，則 API 將根據要求的用戶端 IP 或行動裝置的 GPS 座標判斷位置。

## <a name="localcircularview"></a>localCircularView

`localCircularView` 參數會圍繞由半徑定義的一組緯度/經度座標建立圓形地理區域。 使用此參數時，Bing 當地商家搜尋 API 的回應將僅包含此圓形地理區域內的位置，這與 `localMapView` 參數不同，後者可能包含稍微位於搜尋區域之外的位置。

若要指定圓形地理搜尋區域，請選擇緯度和經度作為圓的中心，選擇半徑 (以公尺為單位)。 然後可以將此參數附加至查詢字串，例如：`q=Restaurants&localCircularView=47.6421,-122.13715,5000`。

完整查詢：

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

`localMapView` 參數指定要搜尋的矩形地理區域，使用兩組座標指定其東南部及西北部的邊角。 使用此參數時，Bing 當地商家搜尋 API 的回應可能包括指定區域內和區域外的位置，這與 `localCircularView` 參數不同，後者僅包括搜尋區域內的位置。

若要指定矩形搜尋區域，請選擇兩組緯度/經度座標作為界限的東南角和西北角。 請務必先定義東南角座標，如下列範例所示：`localMapView=47.619987,-122.181671,47.6421,-122.13715`。

完整查詢：

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>後續步驟
- [當地商家搜尋 JAVA 快速入門](quickstarts/local-search-java-quickstart.md)
- [當地商家搜尋 c # 快速入門](quickstarts/local-quickstart.md)
- [當地商家搜尋節點快速入門](quickstarts/local-search-node-quickstart.md)
- [當地商家搜尋 Python 快速入門](quickstarts/local-search-python-quickstart.md)