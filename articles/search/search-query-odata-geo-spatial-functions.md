---
title: OData 地理空間函數參考
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋查詢中使用 OData 地理空間函式、地理距離和地理交集的語法和參考檔。
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74113171"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure 認知搜尋中的 OData 地理空間函數- `geo.distance` 和`geo.intersects`

Azure 認知搜尋可透過和函式，在[OData 篩選運算式](query-odata-filter-orderby-syntax.md)中支援地理空間查詢 `geo.distance` `geo.intersects` 。 函式會 `geo.distance` 傳回兩個點之間的距離（以公里為單位），一個是欄位或範圍變數，另一個是做為篩選準則一部分傳遞的常數。 `geo.intersects`如果指定的 `true` 點在指定的多邊形內，此函式會傳回，其中點是欄位或範圍變數，而多邊形則會指定為做為篩選準則一部分傳遞的常數。

函式 `geo.distance` 也可以用在[ **$orderby**參數](search-query-odata-orderby.md)中，以從指定點的距離來排序搜尋結果。 `geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 `geo.distance`在 **$orderby**中使用時，它所套用的欄位必須是類型 `Edm.GeographyPoint` ，而且也必須是可**排序**的。

> [!NOTE]
> `geo.distance`在 **$orderby**參數中使用時，您傳遞給函式的欄位必須只包含單一的地理點。 換句話說，它的類型必須是 `Edm.GeographyPoint` ，而不是 `Collection(Edm.GeographyPoint)` 。 您不能在 Azure 認知搜尋中排序集合欄位。

## <a name="syntax"></a>Syntax

下列 EBNF （[延伸巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義和函式的 `geo.distance` 文法 `geo.intersects` ，以及其運作所在的地理空間值：

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

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

### <a name="geodistance"></a>地理距離

函 `geo.distance` 式接受兩個類型的參數 `Edm.GeographyPoint` ，並傳回 `Edm.Double` 值，也就是兩者之間的距離（以公里為間隔）。 這與支援 OData 地理空間作業的其他服務不同，這通常會傳回量表中的距離。

的其中一個參數 `geo.distance` 必須是地理位置點常數，另一個必須是欄位路徑（或是在篩選準則的情況下，會反復查看類型的欄位）的範圍變數 `Collection(Edm.GeographyPoint)` 。 這些參數的順序並不重要。

地理位置點常數的格式為 `geography'POINT(<longitude> <latitude>)'` ，其中經度和緯度是數值常數。

> [!NOTE]
> `geo.distance`在篩選準則中使用時，您必須使用 `lt` 、、或，比較函式所傳回的距離與常數 `le` `gt` `ge` 。 比較距離時不支援使用運算子 `eq` 和 `ne`。 例如，這是正確的使用方式 `geo.distance` ： `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5` 。

### <a name="geointersects"></a>地理交集

函式 `geo.intersects` 會採用型別和常數的變數， `Edm.GeographyPoint` `Edm.GeographyPolygon` `Edm.Boolean`  --  `true` 如果點位於多邊形的界限內， `false` 則會傳回，否則會傳回。

多邊形是儲存為一連串點的二維介面，可定義周框環形（請參閱下列[範例](#examples)）。 多邊形必須是封閉的，也就是第一個和最後一個點集合必須相同。 [多邊形內的點必須採用逆時針順序](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>跨180度經線經線的地理空間查詢和多邊形

許多地理空間查詢程式庫所組成的查詢，包含180度經線經線（接近 dateline 附近）是關閉限制或需要因應措施，例如將多邊形分割成兩個，一個在經線的兩邊。

在 Azure 認知搜尋中，如果查詢圖形是矩形，而且您的座標與經度和緯度的格線版面配置對齊（例如），則包含180度經度的地理空間查詢將會如預期般運作 `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'` 。 而如果是非矩形或未對齊的形狀，請考慮採用分割多邊形的方法。  

### <a name="geo-spatial-functions-and-null"></a>地理空間函數和`null`

就像 Azure 認知搜尋中的所有其他非集合欄位，類型的欄位 `Edm.GeographyPoint` 可以包含 `null` 值。 當 Azure 認知搜尋評估為的 `geo.intersects` 欄位時 `null` ，結果一律會是 `false` 。 `geo.distance`在此情況下，的行為取決於內容：

- 在 [篩選] 中， `geo.distance` 欄位的會 `null` 產生 `null` 。 這表示檔將不會相符，因為 `null` 相較于任何非 null 值，會評估為 `false` 。
- 使用 **$orderby**排序結果時， `geo.distance` 欄位會 `null` 產生最大可能距離。 具有這類欄位的檔在使用排序方向（預設值）時，會比其他專案排序更低 `asc` ，而當方向為時，則會高於其他所有檔 `desc` 。

## <a name="examples"></a>範例

### <a name="filter-examples"></a>篩選範例

尋找在指定參考點的10公里內的所有旅館（其中 location 是類型的欄位 `Edm.GeographyPoint` ）：

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

尋找指定的範圍內的所有旅館，以多邊形的形式描述（其中 location 是類型的欄位 `Edm.GeographyPoint` ）。 請注意，多邊形必須是封閉的 (第一個和最後一個點集合必須相同)，且[點必須以逆時針順序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order-by 範例

將飯店遞減排序 `rating` ，然後依指定座標的距離遞增：

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

以遞減的順序依和排序飯店 `search.score` `rating` ，然後依照指定座標的遞增順序，讓兩個飯店具有相同的評等，最先列出最接近的旅館：

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
