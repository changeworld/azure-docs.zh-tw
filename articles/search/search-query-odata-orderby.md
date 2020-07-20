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
ms.openlocfilehash: 68e6ec0af0b24771b21dac35c944fc7fa098b404
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203103"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的 OData $orderby 語法

 您可以使用[OData **$orderby**參數](query-odata-filter-orderby-syntax.md)，在 Azure 認知搜尋中套用搜尋結果的自訂排序次序。 本文會詳細說明 **$orderby**的語法。 如需有關如何在呈現搜尋結果時使用 **$orderby**的一般資訊，請參閱[如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>語法

**$Orderby**參數接受以逗號分隔的清單，其中最多可有32個**order by 子句**。 下列 EBNF ([Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 描述 order by 子句的語法：

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

每個子句都有排序準則，並選擇性地後面加上 (`asc` 遞增或遞減) 的排序方向 `desc` 。 如果您未指定方向，預設值為 [遞增]。 排序準則可以是欄位的路徑，或是呼叫或函式的方法 `sortable` [`geo.distance`](search-query-odata-geo-spatial-functions.md) [`search.score`](search-query-odata-search-score-function.md) 。

如果多個檔具有相同的排序準則，但未使用此函式 `search.score` (例如，如果您依數值 `Rating` 欄位排序，而三個檔的評等為 4) ，則系結將會依檔分數遞減順序中斷。 當檔分數是相同的 (例如，未在要求) 中指定全文檢索搜尋查詢時，系結檔的相對順序就是不確定的。

您可以指定多個排序準則。 運算式的順序會決定最終的排序順序。 例如，若要依分數遞減排序，然後按下評等，語法會是 `$orderby=search.score() desc,Rating desc` 。

`geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 在 **$orderby** 中使用 `geo.distance` 時，加以套用的欄位必須屬於 `Edm.GeographyPoint` 類型，而且也必須是 `sortable`。

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 函數 `search.score` 不接受任何參數。

## <a name="examples"></a>範例

依基本費率將飯店遞增排序：

```odata-filter-expr
    $orderby=BaseRate asc
```

先依評等將飯店遞減排序，再依基本費率遞增排序 (請記住，遞增是預設值)：

```odata-filter-expr
    $orderby=Rating desc,BaseRate
```

依評等將飯店遞減排序，然後依指定座標的距離遞增：

```odata-filter-expr
    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

依照搜尋分數和評等的遞減順序來排序飯店，然後依照距指定座標的遞增順序。 在兩個具有相同相關性分數和評等的飯店之間，最接近的旅館會最先列出：

```odata-filter-expr
    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>後續步驟  

- [如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
