---
title: OData 順序-依參考
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋查詢中使用 order by 的語法和語言參考檔。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74113156"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的 OData $orderby 語法

 您可以使用[OData **$orderby**參數](query-odata-filter-orderby-syntax.md)，在 Azure 認知搜尋中套用搜尋結果的自訂排序次序。 本文會詳細說明 **$orderby**的語法。 如需有關如何在呈現搜尋結果時使用 **$orderby**的一般資訊，請參閱[如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>Syntax

**$Orderby**參數接受以逗號分隔的清單，其中最多可有32個**order by 子句**。 Order by 子句的語法是由下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）所描述：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

每個子句都有排序準則，並選擇性地後面接著排序方向（ `asc` 針對遞增或 `desc` 遞減）。 如果您未指定方向，預設值為 [遞增]。 排序準則可以是欄位的路徑，或是呼叫或函式的方法 `sortable` [`geo.distance`](search-query-odata-geo-spatial-functions.md) [`search.score`](search-query-odata-search-score-function.md) 。

如果多個檔具有相同的排序準則，但 `search.score` 未使用函式（例如，如果您依數值欄位排序， `Rating` 而三個檔的評等為4個），則會依檔分數遞減順序來中斷系結。 當檔分數相同時（例如，在要求中未指定全文檢索搜尋查詢時），則系結檔的相對順序是不定的。

您可以指定多個排序準則。 運算式的順序會決定最終的排序順序。 例如，若要依分數遞減排序，然後按下評等，語法會是 `$orderby=search.score() desc,Rating desc` 。

`geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 在 **$orderby** 中使用 `geo.distance` 時，加以套用的欄位必須屬於 `Edm.GeographyPoint` 類型，而且也必須是 `sortable`。

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 函數 `search.score` 不接受任何參數。

## <a name="examples"></a>範例

依基本費率將飯店遞增排序：

    $orderby=BaseRate asc

先依評等將飯店遞減排序，再依基本費率遞增排序 (請記住，遞增是預設值)：

    $orderby=Rating desc,BaseRate

依評等將飯店遞減排序，然後依指定座標的距離遞增：

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

依照搜尋分數和評等的遞減順序來排序飯店，然後依照距指定座標的遞增順序。 在兩個具有相同相關性分數和評等的飯店之間，最接近的旅館會最先列出：

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
