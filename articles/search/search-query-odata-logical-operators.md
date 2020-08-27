---
title: OData 邏輯運算子參考
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋查詢中使用 OData 邏輯運算子、and、or 和 not 的語法和參考檔。
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
ms.openlocfilehash: 27d5427d34de591f9cfeab2310d79a2fde217624
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88917868"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure 認知搜尋中的 OData 邏輯運算子- `and` 、 `or` 、 `not`

Azure 認知搜尋中的[OData 篩選運算式](query-odata-filter-orderby-syntax.md)是評估為或的布林運算式 `true` `false` 。 您可以撰寫一系列 [簡單的篩選](search-query-odata-comparison-operators.md) ，並使用 [布林代數](https://en.wikipedia.org/wiki/Boolean_algebra)中的邏輯運算子來撰寫複雜的篩選，以撰寫複雜的篩選準則：

- `and`：二元運算子， `true` 如果其左邊和右邊的子運算式都評估為，則會評估為 `true` 。
- `or`：二元運算子， `true` 如果其中一個左邊或右邊的子運算式評估為，則會評估為 `true` 。
- `not`：一元運算子， `true` 如果子運算式評估為，則會評估為 `false` ，反之亦然。

這些會與[集合運算子 `any` 和 `all` ](search-query-odata-collection-operators.md)一起使用，可讓您建立可表達非常複雜搜尋條件的篩選準則。

## <a name="syntax"></a>語法

下列 EBNF ([外延巴克斯格斯表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義使用邏輯運算子之 OData 運算式的文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

有兩種形式的邏輯運算式：二元 (`and` / `or`) ，其中有兩個子運算式和一元 (`not`) ，其中只有一個。 子運算式可以是任何種類的布林運算式：

- 類型的欄位或範圍變數 `Edm.Boolean`
- 傳回類型值的函式 `Edm.Boolean` ，例如 `geo.intersects` 或。 `search.ismatch`
- [比較運算式](search-query-odata-comparison-operators.md)，例如 `rating gt 4`
- [集合運算式](search-query-odata-collection-operators.md)，例如 `Rooms/any(room: room/Type eq 'Deluxe Room')`
- 布林常值 `true` 或 `false` 。
- 使用、和所建立的其他邏輯運算式 `and` `or` `not` 。

> [!IMPORTANT]
> 在某些情況下，並非所有類型的子運算式都可以搭配使用 `and` / `or` ，尤其是在 lambda 運算式中。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 OData 集合運算子](search-query-odata-collection-operators.md#limitations) 。

### <a name="logical-operators-and-null"></a>邏輯運算子和 `null`

大部分的布林運算式（如函數和比較）無法產生 `null` 值，而且邏輯運算子無法直接套用至 `null` 常值 (例如， `x and null`) 不允許。 不過，布林值欄位可以是 `null` ，因此您必須留意 `and` 、 `or` 和 `not` 運算子在存在 null 時的行為。 下表將摘要說明，其中是下列 `b` 類型的欄位 `Edm.Boolean` ：

| 運算是 | 當 `b` 為時的結果 `null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

當布林值欄位 `b` 本身出現在篩選運算式中時，其行為會如同已寫入 `b eq true` ，因此如果 `b` 為 `null` ，則運算式會評估為 `false` 。 同樣地， `not b` 行為類似 `not (b eq true)` ，因此它會評估為 `true` 。 如此一來， `null` 欄位的行為會與 `false` 相同。 這與使用和的其他運算式結合時的行為一致 `and` ，如上 `or` 表所示。 儘管如此， () 的直接比較 `false` `b eq false` 仍會評估為 `false` 。 換句話說，雖然 `null` `false` 它在布林運算式中的行為類似，但不等於。

## <a name="examples"></a>範例

符合 `rating` 欄位介於3到5之間的檔（包含：

```odata-filter-expr
    rating ge 3 and rating le 5
```

比對檔中欄位的所有元素 `ratings` 小於3或大於5：

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

比對欄位在 `location` 指定多邊形內的檔，而且檔不包含「公用」一詞。

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

在 deluxe 的房間內，在加拿大的旅館中比對的旅館，其基本費率低於160：

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)