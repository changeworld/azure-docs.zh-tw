---
title: O資料按訂單引用
titleSuffix: Azure Cognitive Search
description: 用於在 Azure 認知搜索查詢中使用訂單的語法和語言參考文檔。
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113156"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData$orderby Azure 認知搜索中的語法

 可以使用[OData **$orderby**參數](query-odata-filter-orderby-syntax.md)對 Azure 認知搜索中的搜尋結果應用自訂排序次序。 本文詳細介紹了 **$orderby**的語法。 有關在顯示搜尋結果時如何使用 **$orderby**的更多常規資訊，請參閱[在 Azure 認知搜索中如何使用搜尋結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>語法

**$orderby**參數接受最多 32 個**訂單子句**的逗號分隔清單。 訂單單子句的語法由以下 EBNF（[擴展 Backus-Naur 表單）](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)描述：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

每個子句都有排序條件，可選後跟排序方向（`asc`對於昇冪或`desc`降冪）。 如果不指定方向，則預設值為昇冪。 排序條件可以是`sortable`欄位的路徑，也可以是對 或[`geo.distance`](search-query-odata-geo-spatial-functions.md)函數的[`search.score`](search-query-odata-search-score-function.md)調用。

如果多個文檔具有相同的排序條件，並且`search.score`函數未使用（例如，如果按數位`Rating`欄位排序，並且三個文檔的評級都為 4），則按降冪按文檔分數斷開關系。 當文檔分數相同時（例如，當請求中未指定全文檢索搜尋查詢時），則綁定文檔的相對順序不確定。

您可以指定多個排序準則。 運算式的順序會決定最終的排序順序。 例如，要按分數排序，後跟評級排序，語法將為`$orderby=search.score() desc,Rating desc`。

`geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 在 **$orderby** 中使用 `geo.distance` 時，加以套用的欄位必須屬於 `Edm.GeographyPoint` 類型，而且也必須是 `sortable`。

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 函數`search.score`不採用任何參數。

## <a name="examples"></a>範例

依基本費率將飯店遞增排序：

    $orderby=BaseRate asc

先依評等將飯店遞減排序，再依基本費率遞增排序 (請記住，遞增是預設值)：

    $orderby=Rating desc,BaseRate

按評級對酒店進行排序，然後按與給定座標的距離向上排列：

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

按搜索.評分和評級按降冪對酒店進行排序，然後按與給定座標的距離按昇冪排序。 在兩個關聯分數和評級相同的酒店之間，最靠近的酒店首先列出：

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [如何在 Azure 認知搜索中處理搜尋結果](search-pagination-page-layout.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
