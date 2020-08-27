---
title: OData 篩選參考
titleSuffix: Azure Cognitive Search
description: OData 語言參考和完整語法，用於在 Azure 認知搜尋查詢中建立篩選條件運算式。
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
ms.openlocfilehash: 0f33b5a28d7c83be7e546c3f61bc517047c51312
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934849"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的 OData $filter 語法

Azure 認知搜尋使用 [OData 篩選運算式](query-odata-filter-orderby-syntax.md) ，在全文檢索搜尋詞彙以外的搜尋查詢中套用額外的條件。 本文將詳細說明篩選準則的語法。 如需有關哪些篩選準則以及如何使用它們來實現特定查詢案例的一般資訊，請參閱 [Azure 認知搜尋中的篩選](search-filters.md)。

## <a name="syntax"></a>語法

OData 語言中的篩選準則是布林運算式，它可以是數種運算式類型的其中一種，如下列 EBNF 所示 ([外延巴克斯格斯表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) ：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

布林運算式的類型包括：

- 使用或的集合篩選運算式 `any` `all` 。 這些會將篩選準則套用至集合欄位。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 OData 集合運算子](search-query-odata-collection-operators.md)。
- 使用運算子、和來合併其他布林運算式的邏輯運算式 `and` `or` `not` 。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 OData 邏輯運算子](search-query-odata-logical-operators.md)。
- 比較運算式，會使用運算子 `eq` 、 `ne` 、 `gt` 、、 `lt` `ge` 和 `le` ，將欄位或範圍變數與常數值進行比較。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 OData 比較運算子](search-query-odata-comparison-operators.md)。 比較運算式也用來比較使用函式之地理空間座標之間的距離 `geo.distance` 。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 OData 異地空間函數](search-query-odata-geo-spatial-functions.md)。
- 布林常值 `true` 和 `false` 。 當以程式設計方式產生篩選時，這些常數可能很有用，但在實務上則不常使用。
- 對布耳函數的呼叫，包括：
  - `geo.intersects`，用來測試指定的點是否在指定的多邊形內。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 OData 異地空間函數](search-query-odata-geo-spatial-functions.md)。
  - `search.in`，這會將欄位或範圍變數與值清單中的每個值進行比較。 如需詳細資訊，請參閱 [ `search.in` Azure 認知搜尋中的 OData 函數](search-query-odata-search-in-function.md)。
  - `search.ismatch` 和 `search.ismatchscoring` ，這會在篩選內容中執行全文檢索搜尋作業。 如需詳細資訊，請參閱 [Azure 認知搜尋中的 OData 全文檢索搜尋函數](search-query-odata-full-text-search-functions.md)。
- 類型的欄位路徑或範圍變數 `Edm.Boolean` 。 例如，如果您的索引有一個稱為布林值的欄位， `IsEnabled` 而您想要傳回此欄位為的所有檔 `true` ，則您的篩選運算式可以是名稱 `IsEnabled` 。
- 括弧中的布林運算式。 使用括弧有助於明確判斷篩選準則中的作業順序。 如需 OData 運算子之預設優先順序的詳細資訊，請參閱下一節。

### <a name="operator-precedence-in-filters"></a>篩選中的運算子優先順序

如果您撰寫的篩選運算式的子運算式周圍沒有括弧，Azure 認知搜尋將根據一組運算子優先順序規則來進行評估。 這些規則是根據用來合併子運算式的運算子。 下表依序列出運算子群組，順序是從最高到最低優先順序：

| 分組 | 運算子 (s)  |
| --- | --- |
| 邏輯運算子 | `not` |
| 比較運算子 | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| 邏輯運算子 | `and` |
| 邏輯運算子 | `or` |

在上表中較高的運算子，會「系結更緊密」至其運算元，而非其他運算子。 例如， `and` 的優先順序高於 `or` ，而且比較運算子的優先順序高於其中一種，因此下列兩個運算式是相等的：

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

`not`運算子的優先順序最高，甚至比比較運算子更高。 這就是為什麼您嘗試撰寫如下的篩選器：

```odata-filter-expr
    not Rating gt 5
```

您將會收到此錯誤訊息：

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

之所以發生此錯誤，是因為運算子只與欄位相關聯，而此 `Rating` 欄位的類型為 `Edm.Int32` ，而不是整個比較運算式。 修正方法是將運算元放 `not` 在括弧中：

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>篩選大小限制

您可以傳送給 Azure 認知搜尋之篩選條件運算式的大小和複雜度有所限制。 這些限制大致上取決於篩選運算式中的子句數目。 很好的指導方針是，如果您有數百個子句，則有超過限制的風險。 我們建議您以無法產生未系結大小篩選的方式來設計您的應用程式。

> [!TIP]
> 使用函 [式而不 `search.in` ](search-query-odata-search-in-function.md) 是 long disjunctions 的相等比較，有助於避免篩選子句的限制，因為函式呼叫會計算為單一子句。

## <a name="examples"></a>範例

找出至少有一個房間低於或大於4的基本費率低於 $200 的所有旅館：

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

尋找自2010起整修的「海洋 View Motel」以外的所有旅館：

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

尋找2010或更新版本中整修的所有旅館。 日期時間常值包含太平洋標準時間的時區資訊：  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

尋找所有具有停車且所有房間都不吸煙的旅館：

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- 或 -  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

尋找所有屬豪華等級，或附停車位且獲評為 5 星級的飯店：  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

在至少一個房間內找出標記為 "wifi" 的所有旅館 (其中每個房間都有儲存在 `Collection(Edm.String)` 欄位) 的標記：  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

尋找任何房間的旅館：  

```odata-filter-expr
    $filter=Rooms/any()
```

尋找沒有房間的所有旅館：

```odata-filter-expr
    $filter=not Rooms/any()
```

尋找指定參考點的10公里內的所有旅館 (其中 `Location` 是) 類型的欄位 `Edm.GeographyPoint` ：

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

找出指定之區域內以多邊形形式描述的所有旅館 (其中 `Location` 是 GeographyPoint) 的欄位。 多邊形必須是封閉的，這表示第一個和最後一個點集合必須相同。 此外， [點必須以逆時針順序列出](/rest/api/searchservice/supported-data-types#Anchor_1)。

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

找出 [描述] 欄位為 null 的所有旅館。 如果從未設定過此欄位，或是明確設定為 null，則此欄位會是 null：  

```odata-filter-expr
    $filter=Description eq null
```

尋找名稱等於「海洋視圖 motel」或「預算旅館」 ) 的所有旅館。 這些片語包含空格，而空格是預設的分隔符號。 您可以使用單引號來指定替代分隔符號作為第三個字串參數：  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

尋找名稱等於「海洋視圖 motel」或「預算旅館」的所有旅館，並以 ' | ' 分隔) ：  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

尋找所有會議室都具有標記 ' wifi ' 或 ' 浴盆 ' 的所有旅館：

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

尋找集合內的片語相符，例如標籤中的「加熱的紙巾」或「hairdryer 包含」。

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

尋找含有「海濱」一詞的文件。 此篩選查詢等同於使用 `search=waterfront` 的[搜尋要求](/rest/api/searchservice/search-documents)。

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

尋找含有「青年旅館」一詞、且評分為 4 或以上的文件，或含有「汽車旅館」一詞、且評分為 5 的文件。 無法在沒有函式的情況下表示此要求， `search.ismatchscoring` 因為它會使用來合併全文檢索搜尋與篩選作業 `or` 。

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

尋找不含「豪華」一詞的文件。

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

尋找含有「海景」一詞或評分為 5 的文件。 `search.ismatchscoring` 查詢只會對欄位 `HotelName` 和 `Description` 執行。 僅符合分離第二個子句的檔會傳回太--等於5的旅館 `Rating` 。 這些檔將會以等於零的分數傳回，使其清楚不符合運算式的任何計分部分。

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

找出條款「飯店」和「機場」在描述中不超過五個單字的旅館，以及所有房間都不吸煙。 此查詢會使用[完整 Lucene 查詢語言](query-lucene-syntax.md)。

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)