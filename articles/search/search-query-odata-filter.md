---
title: O資料篩選器引用
titleSuffix: Azure Cognitive Search
description: OData 語言引用和用於在 Azure 認知搜索查詢中創建篩選器運算式的完整語法。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282883"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Azure 認知搜索中的 OData $filter語法

Azure 認知搜索使用[OData 篩選器運算式](query-odata-filter-orderby-syntax.md)將其他條件應用於搜索查詢，以及全文檢索搜尋詞。 本文詳細介紹了篩選器的語法。 有關篩選器是什麼以及如何使用它們來實現特定查詢方案的更多常規資訊，請參閱[Azure 認知搜索 中的篩選器](search-filters.md)。

## <a name="syntax"></a>語法

OData 語言中的篩選器是布林運算式，它反過來可以是幾種運算式類型之一，如以下 EBNF（[擴展 Backus-Naur 表單）所示](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)。

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

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

布林運算式的類型包括：

- 使用`any`或`all`的集合篩選器運算式。 這些將篩選準則應用於集合欄位。 有關詳細資訊，請參閱[Azure 認知搜索 中的 OData 集合運算子](search-query-odata-collection-operators.md)。
- 使用 運算子`and`組合其他布林運算式的邏輯運算式 ，`or`和`not`。 有關詳細資訊，請參閱[Azure 認知搜索 中的 OData 邏輯運算子](search-query-odata-logical-operators.md)。
- `eq`比較運算式，使用運算子 、、、`ne``gt``lt``ge`和 將`le`欄位或範圍變數與常量值進行比較。 有關詳細資訊，請參閱[Azure 認知搜索 中的 OData 比較運算子](search-query-odata-comparison-operators.md)。 比較運算式還用於使用`geo.distance`函數比較地理空間座標之間的距離。 有關詳細資訊，請參閱[Azure 認知搜索 中的 OData 地理空間函數](search-query-odata-geo-spatial-functions.md)。
- 布林文本`true`和`false`。 有時，在以程式設計方式生成篩選器時，這些常量可能很有用，但在實踐中則不傾向于使用。
- 對布耳函數的調用，包括：
  - `geo.intersects`，用於測試給定點是否位於給定多邊形內。 有關詳細資訊，請參閱[Azure 認知搜索 中的 OData 地理空間函數](search-query-odata-geo-spatial-functions.md)。
  - `search.in`將欄位或範圍變數與值清單中的每個值進行比較。 有關詳細資訊，請參閱[Azure 認知`search.in`搜索 中的 OData 函數](search-query-odata-search-in-function.md)。
  - `search.ismatch`和`search.ismatchscoring`，在篩選器上下文中執行全文檢索搜尋操作。 有關詳細資訊，請參閱[Azure 認知搜索 中的 OData 全文檢索搜尋功能](search-query-odata-full-text-search-functions.md)。
- 類型的欄位路徑或範圍變數`Edm.Boolean`。 例如，如果索引具有稱為`IsEnabled`布林欄位，並且要返回此欄位所在的`true`所有文檔，則篩選器運算式只能是名稱`IsEnabled`。
- 括弧中的布林運算式。 使用括弧有助於顯式確定篩選器中的操作順序。 有關 OData 運算子的預設優先順序的詳細資訊，請參閱下一節。

### <a name="operator-precedence-in-filters"></a>篩選器中的運算子優先順序

如果編寫篩選器運算式在其子運算式周圍沒有括弧，則 Azure 認知搜索將根據一組運算子優先順序規則對其進行計算。 這些規則基於用於組合子運算式的運算子。 下表按從最高優先順序到最低優先順序的順序列出了運算子組：

| 群組 | 操作員 |
| --- | --- |
| 邏輯運算子 | `not` |
| 比較運算子 | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| 邏輯運算子 | `and` |
| 邏輯運算子 | `or` |

與其它運算子相比，上表中較高的運算子將"更緊密地綁定"到其運算元。 例如，`and`優先順序高於`or`，比較運算子的優先順序高於其中任一，因此以下兩個運算式等效：

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

運算子`not`的優先順序最高 ， 甚至高於比較運算子。 這就是為什麼如果您嘗試編寫這樣的篩選器：

    not Rating gt 5

您將看到以下錯誤訊息：

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

發生此錯誤的原因是運算子僅與`Rating`欄位（類型為`Edm.Int32`）相關聯，而不是與整個比較運算式關聯。 解決方法是將 的`not`運算元放在括弧中：

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>篩選大小限制

篩選器運算式的大小和複雜性是有限制的，可以發送到 Azure 認知搜索。 這些限制大致上取決於篩選運算式中的子句數目。 一個好的準則是，如果您有數百個子句，您有超出限制的風險。 我們建議以不生成大小不綁定的篩選器的方式設計應用程式。

> [!TIP]
> 使用[`search.in`函數](search-query-odata-search-in-function.md)而不是相等比較的長分離有助於避免篩選器子句限制，因為函式呼叫計為單個子句。

## <a name="examples"></a>範例

查找最低一間客房的至少一間客房低於 200 美元，評級為 4 或以上的所有酒店：

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

查找自 2010 年以來經過翻修的"海景汽車旅館"以外的所有酒店：

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

查找 2010 年或以後翻修的所有酒店。 日期時間文本包括太平洋標準時間的時區資訊：  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

查找所有包含停車位的酒店以及所有客房均禁止吸煙的位置：

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- 或 -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

尋找所有屬豪華等級，或附停車位且獲評為 5 星級的飯店：  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

在至少一個房間找到帶有"wifi"標籤的所有酒店（每個房間都存儲了欄位的標籤`Collection(Edm.String)`）：  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

查找所有擁有任何客房的酒店：  

    $filter=Rooms/any()

查找所有沒有客房的酒店：

    $filter=not Rooms/any()

查找給定參考點 10 公里內的所有酒店（其中`Location`是類型`Edm.GeographyPoint`欄位）：

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

查找描述為多邊形的給定視口中的所有酒店（其中`Location`是 Edm.地理點類型的欄位）。 必須關閉面，這意味著第一個點和最後一個點集必須相同。 此外[，點必須按逆時針順序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

查找"描述"欄位為空的所有酒店。 如果該欄位從未設置，或者顯式設置為 null，則該欄位將為 null：  

    $filter=Description eq null

查找名稱等於"海景汽車旅館"或"經濟型酒店"的所有酒店）。 這些短語包含空格，空格是預設分隔符號。 您可以在單個引號中指定替代分隔符號作為第三個字串參數：  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

查找名稱等於"海景汽車旅館"或"經濟型酒店"（由"*"分隔）的所有酒店：  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

查找所有客房都標為"wifi"或"tub"的所有酒店：

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

查找集合中的短語的匹配項，例如標籤中的"加熱毛巾架"或"包括吹風機"。

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

尋找含有「海濱」一詞的文件。 此篩選查詢等同於使用 `search=waterfront` 的[搜尋要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

    $filter=search.ismatchscoring('waterfront')

尋找含有「青年旅館」一詞、且評分為 4 或以上的文件，或含有「汽車旅館」一詞、且評分為 5 的文件。 如果沒有函數，`search.ismatchscoring`無法表示此請求，因為它將全文檢索搜尋與使用 的`or`篩選器操作相結合。

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

尋找不含「豪華」一詞的文件。

    $filter=not search.ismatch('luxury')

尋找含有「海景」一詞或評分為 5 的文件。 `search.ismatchscoring` 查詢只會對欄位 `HotelName` 和 `Description` 執行。 只與分界第二條款相符的檔也將退回 -`Rating`等於 5 的酒店。 這些檔將返回的分數等於零，以表明它們與運算式的任何評分部分不匹配。

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

查找描述中"酒店"和"機場"兩個單詞的字數不超過五個字的酒店，以及所有客房均禁止吸煙的酒店。 此查詢會使用[完整 Lucene 查詢語言](query-lucene-syntax.md)。

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜索中的篩選器](search-filters.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
