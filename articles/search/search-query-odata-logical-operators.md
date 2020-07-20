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
ms.openlocfilehash: 741bf9e2aba6f893f670e86fb8bf5cd6c8b9d803
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86201985"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure 認知搜尋中的 OData 邏輯運算子- `and` 、 `or` 、`not`

Azure 認知搜尋中的[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)是評估為或的布林運算式 `true` `false` 。 您可以撰寫一系列[較簡單](search-query-odata-comparison-operators.md)的篩選器，並使用來自[布林代數](https://en.wikipedia.org/wiki/Boolean_algebra)的邏輯運算子來撰寫複雜的篩選

- `and`：二元運算子， `true` 如果其左和右子運算式評估為，則會評估為 `true` 。
- `or`： `true` 如果其中一個左或右子運算式評估為，則會評估為的二元運算子 `true` 。
- `not`：評估為的一元運算子 `true` （如果其子運算式評估為 `false` ，反之亦然）。

這些與[集合運算子 `any` 和 `all` ](search-query-odata-collection-operators.md)結合，可讓您建立可以表達非常複雜之搜尋準則的篩選器。

## <a name="syntax"></a>語法

下列 EBNF ([Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義使用邏輯運算子之 OData 運算式的文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

邏輯運算式有兩種形式：二進位 (`and` / `or`) ，其中有兩個子運算式，而一元 (`not`) ，其中只有一個。 子運算式可以是任何種類的布林運算式：

- 類型的欄位或範圍變數`Edm.Boolean`
- 傳回類型值的函式 `Edm.Boolean` ，例如 `geo.intersects` 或`search.ismatch`
- [比較運算式](search-query-odata-comparison-operators.md)，例如`rating gt 4`
- [集合運算式](search-query-odata-collection-operators.md)，例如`Rooms/any(room: room/Type eq 'Deluxe Room')`
- 布林常值 `true` 或 `false` 。
- 使用、和所建造的其他邏輯運算式 `and` `or` `not` 。

> [!IMPORTANT]
> 在某些情況下，並非所有類型的子運算式都可以搭配使用 `and` / `or` ，特別是在 lambda 運算式中。 如需詳細資訊，請參閱[Azure 認知搜尋中的 OData 集合運算子](search-query-odata-collection-operators.md#limitations)。

### <a name="logical-operators-and-null"></a>邏輯運算子和`null`

大部分的布林運算式（例如函式和比較）都無法產生 `null` 值，而且邏輯運算子無法直接套用到 `null` 常值 (例如，不 `x and null` 允許) 。 不過，布林值欄位可以是 `null` ，因此您必須留意 `and` 、 `or` 和 `not` 運算子在存在 null 時的行為。 下表摘要說明這一點，其中 `b` 是類型的欄位 `Edm.Boolean` ：

| 運算式 | 當 `b` 為時的結果`null` |
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

當布林值欄位 `b` 本身在篩選運算式中出現時，其行為會如同已撰寫 `b eq true` ，因此如果 `b` 為 `null` ，則運算式會評估為 `false` 。 同樣地，的 `not b` 行為類似 `not (b eq true)` ，因此它會評估為 `true` 。 如此一來， `null` 欄位的行為會與 `false` 相同。 這與使用和的其他運算式結合時的行為一致 `and` ，如上 `or` 表所示。 儘管如此，與 () 的直接比較， `false` `b eq false` 仍然會評估為 `false` 。 換句話說，不 `null` 等於 `false` ，即使它在布林運算式中的行為類似也一樣。

## <a name="examples"></a>範例

符合 `rating` 欄位介於3和5之間的檔（包含：

```odata-filter-expr
    rating ge 3 and rating le 5
```

符合欄位的所有元素 `ratings` 小於3或大於5的檔：

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

`location`比對欄位位於給定多邊形中的檔，且檔不包含「公用」一詞。

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

將檔用於符合 deluxe 房間且低於160的飯店（加拿大）：

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
