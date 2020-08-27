---
title: OData 順序-依參考
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋查詢中使用 order by 的語法和語言參考檔。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/05/2020
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
ms.openlocfilehash: 83ab2c6b97435ace0d2bc508cbf522600391b60b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926825"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的 OData $orderby 語法

 您可以使用 [OData **$orderby** 參數](query-odata-filter-orderby-syntax.md) ，將自訂排序次序套用至 Azure 認知搜尋中的搜尋結果。 本文將詳細說明 **$orderby** 的語法。 如需有關如何在呈現搜尋結果時使用 **$orderby** 的一般資訊，請參閱 [如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>語法

**$Orderby**參數接受以逗號分隔的清單，最多可達32個**order by 子句**。 Order by 子句的語法如下所述 EBNF ([外延巴克斯-格斯表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) ：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

每個子句都有排序準則，並選擇性地接著排序方向 (`asc` 遞增或 `desc` 遞減) 。 如果您未指定方向，則預設值為遞增。 如果欄位中有 null 值，如果排序是，則會先出現 null 值，如果 `asc` 排序是，則為最後一個 `desc` 。

排序準則可以是欄位的路徑，也可以是 `sortable` [`geo.distance`](search-query-odata-geo-spatial-functions.md) 或函數的呼叫 [`search.score`](search-query-odata-search-score-function.md) 。

如果有多份檔具有相同的排序準則，但未使用此函式 `search.score` (例如，如果您依數值 `Rating` 欄位排序，且三份檔的評等為 4) ，則會以遞減順序將系結分成檔分數。 當檔分數相同時 (例如，當要求中沒有指定任何全文檢索搜尋查詢時) ，則系結檔的相對順序不確定。

您可以指定多個排序準則。 運算式的順序會決定最終的排序順序。 例如，若要依分數遞減排序，然後依評等，則語法為 `$orderby=search.score() desc,Rating desc` 。

`geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 在 **$orderby** 中使用 `geo.distance` 時，加以套用的欄位必須屬於 `Edm.GeographyPoint` 類型，而且也必須是 `sortable`。

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 函數 `search.score` 不採用任何參數。

## <a name="examples"></a>範例

依基本費率將飯店遞增排序：

```odata-filter-expr
    $orderby=BaseRate asc
```

先依評等將飯店遞減排序，再依基本費率遞增排序 (請記住，遞增是預設值)：

```odata-filter-expr
    $orderby=Rating desc,BaseRate
```

依評等遞減方式排序旅館，然後依指定座標的距離遞增：

```odata-filter-expr
    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

依搜尋的遞減順序排序旅館，然後以相對於給定座標的距離遞增順序排序。 在具有相同相關性分數和評等的兩個旅館之間，會先列出最接近的旅館：

```odata-filter-expr
    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>後續步驟  

- [如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)