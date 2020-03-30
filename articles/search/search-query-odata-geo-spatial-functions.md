---
title: OData地理空間函數參考
titleSuffix: Azure Cognitive Search
description: 用於在 Azure 認知搜索查詢中使用 OData 地理空間函數（地理.距離和地理.相交）的語法和參考文檔。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113171"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure 認知搜索中的 OData 地理空間`geo.distance`函數 - 和`geo.intersects`

Azure 認知搜索通過`geo.distance`和`geo.intersects`函數支援[OData 篩選器運算式](query-odata-filter-orderby-syntax.md)中的地理空間查詢。 該`geo.distance`函數返回兩個點之間的距離（以公里形式，一個是欄位或範圍變數，另一個是作為篩選器的一部分傳遞的常量）。 如果`geo.intersects`給定點`true`位於給定多邊形內，則函數將返回，其中點是欄位或範圍變數，而面被指定為作為篩選器的一部分傳遞的常量。

該`geo.distance`函數還可用於[**$orderby**參數](search-query-odata-orderby.md)中按與給定點的距離對搜尋結果進行排序。 `geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 在$orderby`geo.distance`中使用 **$orderby**時，它所應用的欄位必須是類型的`Edm.GeographyPoint`欄位，並且還必須**是可排序的**。

> [!NOTE]
> 在$orderby`geo.distance`參數中使用 **$orderby**時，傳遞給函數的欄位必須僅包含一個地理點。 換句話說，它必須是類型`Edm.GeographyPoint`，而不是`Collection(Edm.GeographyPoint)`。 無法對 Azure 認知搜索中的集合欄位進行排序。

## <a name="syntax"></a>語法

以下 EBNF（[擴展 Backus-Naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義了`geo.distance`和`geo.intersects`函數的語法，以及它們操作的地理空間值：

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

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

### <a name="geodistance"></a>地理距離

該`geo.distance`函數採用兩個類型的`Edm.GeographyPoint`參數，並返回一`Edm.Double`個值，該值以公里表示它們之間的距離。 這與支援 OData 地理空間操作的其他服務不同，後者通常以米為單位返回距離。

要的`geo.distance`參數之一必須是地理點常量，另一個參數必須是欄位路徑（或篩選器在類型`Collection(Edm.GeographyPoint)`欄位上反覆運算時的範圍變數）。 這些參數的順序並不重要。

地理點常量是形式`geography'POINT(<longitude> <latitude>)'`，其中經度和緯度是數值常量。

> [!NOTE]
> `geo.distance`在篩選器`lt`中使用時，必須將函數返回的距離與使用 的`le`常量進行比較。 `gt` `ge` 比較距離時不支援使用運算子 `eq` 和 `ne`。 例如，這是`geo.distance`的正確用法。 `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`

### <a name="geointersects"></a>地理.相交

函數`geo.intersects``Edm.GeographyPoint`採用類型的變數和常量`Edm.GeographyPolygon`，如果點在多邊形`Edm.Boolean` -- `true`的邊界內，則返回 返回 ，`false`否則。

面是作為定義邊界環的點序列存儲的二維曲面（請參閱下面的[示例](#examples)）。 多邊形必須是封閉的，也就是第一個和最後一個點集合必須相同。 [多邊形內的點必須採用逆時針順序](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>跨越第 180 個子午線的地理空間查詢和多邊形

對於許多地理空間查詢庫，制定包含第 180 個子午線（靠近日期線）的查詢是禁區，或者需要解決方法，例如將多邊形拆分為兩個，一個位於子午線兩側。

在 Azure 認知搜索中，如果查詢形狀為矩形，並且座標沿經度和緯度（例如），`geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`與網格佈局對齊，則包含 180 度經度的地理空間查詢將按預期工作。 而如果是非矩形或未對齊的形狀，請考慮採用分割多邊形的方法。  

### <a name="geo-spatial-functions-and-null"></a>地理空間函數和`null`

與 Azure 認知搜索中的所有其他非集合欄位一樣，類型的`Edm.GeographyPoint`欄位可以`null`包含值。 當 Azure 認知搜索`geo.intersects`評估 的欄位時`null`，結果將始終為`false`。 在這種情況下，其`geo.distance`行為取決於上下文：

- 在篩選器中`geo.distance``null`，欄位的結果為`null`。 這意味著文檔將不匹配，因為`null`與任何非空值計算為`false`相比。
- 使用`geo.distance`**欄位的$orderby**對結果進行排序`null`時，將產生最大可能的距離。 使用排序方向（預設值）時，具有此類欄位的文檔排序將低於`asc`所有其他欄位，當該方向為`desc`時，其排序方向將高於所有其他文檔。

## <a name="examples"></a>範例

### <a name="filter-examples"></a>篩選範例

查找給定參考點 10 公里內的所有酒店（位置是類型`Edm.GeographyPoint`欄位）：

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

查找描述為多邊形的給定視口中的所有酒店（其中位置是類型`Edm.GeographyPoint`欄位）。 請注意，多邊形必須是封閉的 (第一個和最後一個點集合必須相同)，且[點必須以逆時針順序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order-by 範例

按 以下排序`rating`酒店，然後按與給定座標的距離向上排列：

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sort hotels in descending order by `search.score` and `rating`, and then in ascending order by distance from the given coordinates so that between two hotels with identical ratings, the closest one is listed first:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜索中的篩選器](search-filters.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
