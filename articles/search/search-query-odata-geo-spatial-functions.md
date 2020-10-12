---
title: OData 地理空間函數參考
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋查詢中使用 OData 地理空間函式、地理位置和地理交集的語法和參考檔。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 376cece922ca424ec78011224852b1fa5499da16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934832"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure 認知搜尋中的 OData 異地空間函數 `geo.distance` 和 `geo.intersects`

Azure 認知搜尋透過和函式在 [OData 篩選條件運算式](query-odata-filter-orderby-syntax.md) 中支援異地空間查詢 `geo.distance` `geo.intersects` 。 `geo.distance`函數會傳回兩個點之間的距離（以公里為單位），一個是欄位或範圍變數，另一個則是做為篩選準則一部分傳遞的常數。 如果指定的點在指定的多邊形內，則此函式會傳回 `geo.intersects` `true` ，其中的點是欄位或範圍變數，且多邊形會指定為做為篩選準則一部分傳遞的常數。

`geo.distance`函數也可以在[ **$orderby**參數](search-query-odata-orderby.md)中使用，以根據指定點的距離來排序搜尋結果。 `geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 `geo.distance`在 **$orderby**中使用時，它所套用的欄位必須屬於類型 `Edm.GeographyPoint` ，而且也必須可**排序**。

> [!NOTE]
> `geo.distance`在 **$orderby**參數中使用時，您傳遞至函式的欄位必須只包含單一地理位置。 換句話說，它必須是型別， `Edm.GeographyPoint` 而不是 `Collection(Edm.GeographyPoint)` 。 您無法在 Azure 認知搜尋的集合欄位上進行排序。

## <a name="syntax"></a>語法

下列 EBNF ([擴充 Backus-Naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義和函式的 `geo.distance` 文法 `geo.intersects` ，以及它們在其上運作的地理空間值：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

### <a name="geodistance"></a>地理距離

此函 `geo.distance` 式會採用兩種類型的參數 `Edm.GeographyPoint` ，並傳回 `Edm.Double` 值，其為公里之間的距離。 這不同于支援 OData 地理空間作業的其他服務，這通常會傳回度量的距離。

其中一個參數 `geo.distance` 必須是地理位置點常數，另一個參數必須是欄位路徑 (或者，如果篩選準則反復查看) 類型的欄位，則必須是範圍變數 `Collection(Edm.GeographyPoint)` 。 這些參數的順序並不重要。

地理位置點常數的格式為 `geography'POINT(<longitude> <latitude>)'` ，其中經度和緯度是數值常數。

> [!NOTE]
> `geo.distance`在篩選中使用時，您必須使用 `lt` 、、或來比較函數所傳回的距離和 `le` 常數 `gt` `ge` 。 比較距離時不支援使用運算子 `eq` 和 `ne`。 例如，這是正確的使用方式 `geo.distance` ： `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5` 。

### <a name="geointersects"></a>地理交集

函 `geo.intersects` 式會採用類型的變數 `Edm.GeographyPoint` 和常數， `Edm.GeographyPolygon` `Edm.Boolean`  --  `true` 如果點位於多邊形的範圍內， `false` 則會傳回，否則會傳回。

多邊形是儲存為一連串點的二維介面，用來定義周框環形 (請參閱下面的 [範例](#examples)) 。 多邊形必須是封閉的，也就是第一個和最後一個點集合必須相同。 [多邊形內的點必須採用逆時針順序](/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>跨180度經線經線的異地空間查詢和多邊形

針對許多地理空間查詢程式庫，會將包含180度經線經線 (接近 dateline) 的查詢設定為關閉限制或需要因應措施，例如將多邊形分割成兩個（經線的任一端）。

在 Azure 認知搜尋中，如果查詢圖形是矩形，而您的座標與經度和緯度的方格配置一致，則包含180度經度的異地空間查詢將會如預期般運作 (例如 `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`) 。 而如果是非矩形或未對齊的形狀，請考慮採用分割多邊形的方法。  

### <a name="geo-spatial-functions-and-null"></a>異地空間函數和 `null`

如同 Azure 認知搜尋中的所有其他非集合欄位，類型的欄位 `Edm.GeographyPoint` 可以包含 `null` 值。 當 Azure 認知搜尋評估為的 `geo.intersects` 欄位時 `null` ，結果一律會是 `false` 。 `geo.distance`在此情況下的行為取決於內容：

- 在 [篩選] 中， `geo.distance` `null` 欄位會產生 `null` 。 這表示檔將不會相符，因為 `null` 相較于任何非 null 值，則會評估為 `false` 。
- 使用 **$orderby**排序結果時， `geo.distance` 欄位會 `null` 產生最大可能距離。 當使用排序方向 (預設) 時，具有這類欄位的檔將會比其他專案更低 `asc` ，而方向為時則為其他所有檔 `desc` 。

## <a name="examples"></a>範例

### <a name="filter-examples"></a>篩選範例

尋找指定參考點的10公里內的所有旅館 (其中的位置是) 類型的欄位 `Edm.GeographyPoint` ：

```odata-filter-expr
    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

尋找指定之區域內的所有旅館（以多邊形 (，其中位置是) 類型的欄位） `Edm.GeographyPoint` 。 請注意，多邊形必須是封閉的 (第一個和最後一個點集合必須相同)，且[點必須以逆時針順序列出](/rest/api/searchservice/supported-data-types#Anchor_1)。

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

### <a name="order-by-examples"></a>Order-by 範例

以遞減方式排序旅館 `rating` ，然後依指定座標的距離遞增：

```odata-filter-expr
    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

以遞減的順序排序旅館 `search.score` ，然後以相對於 `rating` 指定座標的遞增順序排序，如此一來，在兩個具有相同評等的旅館之間，就會先列出最接近的旅館：

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>接下來的步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)