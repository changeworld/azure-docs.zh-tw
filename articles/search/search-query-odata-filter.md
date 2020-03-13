---
title: OData 篩選參考
titleSuffix: Azure Cognitive Search
description: 用來在 Azure 認知搜尋查詢中建立篩選條件運算式的 OData 語言參考和完整語法。
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282883"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的 OData $filter 語法

Azure 認知搜尋會使用[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)，將額外的準則套用至除了全文檢索搜尋詞彙以外的搜尋查詢。 本文詳細說明篩選準則的語法。 如需有關哪些篩選準則，以及如何使用它們來實現特定查詢案例的一般資訊，請參閱[Azure 認知搜尋中的篩選](search-filters.md)。

## <a name="syntax"></a>語法

OData 語言中的篩選是布林運算式，它可以是數種運算式類型的其中一種，如下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）所示：

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

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

布林運算式的類型包括：

- 使用 `any` 或 `all`的集合篩選運算式。 這些會將篩選準則套用至集合欄位。 如需詳細資訊，請參閱[Azure 認知搜尋中的 OData 集合運算子](search-query-odata-collection-operators.md)。
- 使用運算子 `and`、`or`和 `not`結合其他布林運算式的邏輯運算式。 如需詳細資訊，請參閱[Azure 認知搜尋中的 OData 邏輯運算子](search-query-odata-logical-operators.md)。
- 比較運算式，使用運算子 `eq`、`ne`、`gt`、`lt`、`ge`和 `le`，比較欄位或範圍變數與常數值。 如需詳細資訊，請參閱[Azure 認知搜尋中的 OData 比較運算子](search-query-odata-comparison-operators.md)。 比較運算式也用來比較地理空間座標之間使用 `geo.distance` 函數的距離。 如需詳細資訊，請參閱[Azure 認知搜尋中的 OData 地理空間函式](search-query-odata-geo-spatial-functions.md)。
- 布林常值 `true` 和 `false`。 這些常數有時在以程式設計方式產生篩選時很有用，但通常不會在實務中使用。
- 對布耳函數的呼叫，包括：
  - `geo.intersects`，它會測試指定的點是否在指定的多邊形內。 如需詳細資訊，請參閱[Azure 認知搜尋中的 OData 地理空間函式](search-query-odata-geo-spatial-functions.md)。
  - `search.in`，這會比較欄位或範圍變數與值清單中的每個值。 如需詳細資訊，請參閱[Azure 認知搜尋中的 OData `search.in`](search-query-odata-search-in-function.md)函式。
  - `search.ismatch` 和 `search.ismatchscoring`，這會在篩選內容中執行全文檢索搜尋作業。 如需詳細資訊，請參閱[Azure 認知搜尋中的 OData 全文檢索搜尋功能](search-query-odata-full-text-search-functions.md)。
- 欄位路徑或類型的範圍變數 `Edm.Boolean`。 例如，如果您的索引有一個名為 `IsEnabled` 的布林值欄位，而您想要傳回此欄位 `true`的所有檔，則您的篩選運算式可以是 `IsEnabled`的名稱。
- 括弧中的布林運算式。 使用括弧有助於明確判斷篩選準則中的作業順序。 如需 OData 運算子之預設優先順序的詳細資訊，請參閱下一節。

### <a name="operator-precedence-in-filters"></a>篩選中的運算子優先順序

如果您撰寫的篩選條件運算式的子運算式周圍沒有括弧，Azure 認知搜尋會根據一組運算子優先順序規則來進行評估。 這些規則是以用來結合子運算式的運算子為基礎。 下表列出運算子群組，順序從最高到最低優先順序：

| 群組 | 運算子 |
| --- | --- |
| 邏輯運算子 | `not` |
| 比較運算子 | `eq`、`ne`、`gt`、`lt`、`ge`、`le` |
| 邏輯運算子 | `and` |
| 邏輯運算子 | `or` |

上表中較高的運算子會將「系結更緊密」地「系結」到其運算元，而不是其他運算子。 例如，`and` 的優先順序高於 `or`，而比較運算子的優先順序高於其中之一，因此下列兩個運算式是相等的：

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

`not` 運算子的優先順序最高，甚至比比較運算子高。 因此，如果您嘗試撰寫如下所示的篩選器：

    not Rating gt 5

您會收到下列錯誤訊息：

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

發生這個錯誤的原因是運算子只與 `Rating` 欄位相關聯，這是 `Edm.Int32`的類型，而不是與整個比較運算式有關。 修正方法是將 `not` 的運算元放在括弧中：

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>篩選大小限制

您可以傳送至「Azure 認知搜尋」的篩選條件運算式大小和複雜度有所限制。 這些限制大致上取決於篩選運算式中的子句數目。 一個不錯的指導方針是，如果您有數百個子句，就會有超過限制的風險。 我們建議您將應用程式設計成不會產生未受限制大小的篩選器。

> [!TIP]
> 使用[`search.in` 函數](search-query-odata-search-in-function.md)，而不是長 disjunctions 的相等比較，有助於避免篩選子句的限制，因為函式呼叫會計算為單一子句。

## <a name="examples"></a>範例

找出至少有一個房間小於 $200 且評分為或高於4的飯店：

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

尋找自2010以來已翻新的所有旅館（「海洋 View Motel」除外）：

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

尋找在2010或更新版本中翻新的所有旅館。 日期時間常值包含太平洋標準時間的時區資訊：  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

尋找所有具有停車的飯店，而且所有房間都不會吸煙：

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- 或 -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

尋找所有屬豪華等級，或附停車位且獲評為 5 星級的飯店：  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

在至少一個房間中尋找所有具有標記 "wifi" 的飯店（其中每個房間的標籤都儲存在 `Collection(Edm.String)` 欄位中）：  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

尋找任何會議室的所有旅館：  

    $filter=Rooms/any()

尋找沒有會議室的所有旅館：

    $filter=not Rooms/any()

尋找在指定參考點的10公里內的所有旅館（其中 `Location` 是 `Edm.GeographyPoint`類型的欄位）：

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

尋找指定的範圍內的所有旅館，並將其描述為多邊形（其中 `Location` 是 GeographyPoint 類型的欄位）。 多邊形必須關閉，表示第一個和最後一個點集合必須相同。 此外，[這些點必須以逆時針順序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

尋找 [描述] 欄位為 null 的所有旅館。 如果絕對不會設定此欄位，或如果已將它明確設定為 null，則會是 null：  

    $filter=Description eq null

尋找名稱等於「海洋 View motel」或「預算飯店」的所有旅館。 這些片語包含空格，而 space 是預設的分隔符號。 您可以使用單引號來指定替代分隔符號，做為第三個字串參數：  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

尋找名稱等於「海運視圖 motel」或「預算飯店」（以 ' | ' 分隔）的所有旅館：  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

尋找所有房間都具有標記 ' wifi ' 或 ' 浴盆 ' 的所有旅館：

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

尋找集合中片語的相符項，例如標記中的「加熱式的紙巾機架」或「hairdryer 包含」。

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

尋找含有「海濱」一詞的文件。 此篩選查詢等同於使用 [ 的](https://docs.microsoft.com/rest/api/searchservice/search-documents)搜尋要求`search=waterfront`。

    $filter=search.ismatchscoring('waterfront')

尋找含有「青年旅館」一詞、且評分為 4 或以上的文件，或含有「汽車旅館」一詞、且評分為 5 的文件。 無法在沒有 `search.ismatchscoring` 函數的情況下表示此要求，因為它會使用 `or`將全文檢索搜尋與篩選作業結合。

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

尋找不含「豪華」一詞的文件。

    $filter=not search.ismatch('luxury')

尋找含有「海景」一詞或評分為 5 的文件。 `search.ismatchscoring` 查詢只會對欄位 `HotelName` 和 `Description` 執行。 只會傳回比對的第二個子句相符的檔--`Rating` 等於5的飯店。 這些檔會以等於零的分數傳回，使其清楚指出不符合運算式的任何計分部分。

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

尋找「飯店」和「機場」詞彙在描述中不超過五個單字，而且所有房間都不吸煙的旅館。 此查詢會使用[完整 Lucene 查詢語言](query-lucene-syntax.md)。

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔&#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
