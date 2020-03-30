---
title: OData 邏輯運算子引用
titleSuffix: Azure Cognitive Search
description: 用於使用 OData 邏輯運算子以及（或者，而不是）在 Azure 認知搜索查詢中的語法和參考文檔。
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113179"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure 認知搜索中的 OData`and`邏輯`or`運算子 - ， ，`not`

Azure 認知搜索中的[OData 篩選器運算式](query-odata-filter-orderby-syntax.md)是計算 到`true`或`false`的布林運算式。 您可以通過編寫一系列[更簡單的篩選器](search-query-odata-comparison-operators.md)並使用[布林代數](https://en.wikipedia.org/wiki/Boolean_algebra)的邏輯運算子來編寫複雜的篩選器：

- `and`： 二進位運算子，用於計算`true`其左右子運算式是否都計算到`true`。
- `or`： 二進位運算子，用於計算`true`其左子運算式之一或右子運算式是否計算到`true`。
- `not`： 計算到`true`其子運算式是否計算為`false`的一個未編輯運算子，反之亦然。

這些，連同[集合運算子`any`和`all`](search-query-odata-collection-operators.md)，允許您構造可以表達非常複雜的搜尋條件的篩選器。

## <a name="syntax"></a>語法

以下 EBNF （[擴展 Backus-Naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)） 定義了使用邏輯運算子的 OData 運算式的語法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

邏輯運算式有兩種形式：二進位`and`/`or`（ ），其中有兩個子運算式，另一種是單一`not`運算式，只有一種。 子運算式可以是任何類型的布林運算式：

- 類型的欄位或範圍變數`Edm.Boolean`
- 返回類型值`Edm.Boolean`的函數，如`geo.intersects``search.ismatch`
- [比較運算式](search-query-odata-comparison-operators.md)，如`rating gt 4`
- [集合運算式](search-query-odata-collection-operators.md)，如`Rooms/any(room: room/Type eq 'Deluxe Room')`
- 布林文本`true`或`false`。
- 使用`and`和`or`構造的其他邏輯運算式。 `not`

> [!IMPORTANT]
> 在某些情況下，並非所有類型的子運算式都可以用於`and`/`or`，尤其是在 lambda 運算式內部。 有關詳細資訊[，請參閱 Azure 認知搜索中的 OData 集合運算子](search-query-odata-collection-operators.md#limitations)。

### <a name="logical-operators-and-null"></a>邏輯運算子和`null`

大多數布林運算式（如函數和比較）不能生成`null`值，並且邏輯運算子不能直接應用於`null`文本（例如，`x and null`不允許）。 `null`但是，布林欄位可以是 ，因此您需要瞭解`and`和`or``not`運算子在 null 存在時如何操作。 這在下表中總結，其中`b`是類型`Edm.Boolean`欄位：

| 運算是 | 結果時`b`是`null` |
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

當布林欄位`b`本身出現在篩選器運算式中時，它的表現就好像已寫入`b eq true`，因此，如果是`b``null`，運算式將計算到`false`。 類似地`not b`，的行為類似于`not (b eq true)`，因此它評估到`true`。 這樣，`null`欄位的表示方式與`false`相同。 這與它們與其他使用 和`and``or`的運算式結合時的表現一致，如上表所示。 儘管如此，與`false`（`b eq false`） 的直接比較仍將評估為`false`。 換句話說，`null`不等於`false`，即使它在布林運算式中表現類似。

## <a name="examples"></a>範例

匹配`rating`欄位介於 3 和 5 之間的文檔，包括：

    rating ge 3 and rating le 5

匹配`ratings`欄位所有元素小於 3 或大於 5 的文檔：

    ratings/all(r: r lt 3 or r gt 5)

匹配`location`欄位位於給定多邊形內的文檔，並且文檔不包含術語"公共"。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

加拿大溫哥華酒店有一間基本房價低於 160 的酒店匹配檔：

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜索中的篩選器](search-filters.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
