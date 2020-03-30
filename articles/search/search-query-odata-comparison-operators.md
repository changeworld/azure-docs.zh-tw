---
title: O資料比較運算子參考
titleSuffix: Azure Cognitive Search
description: 用於在 Azure 認知搜索查詢中使用 OData 比較運算子（eq、ne、gt、lt、ge 和 le）的語法和參考文檔。
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113216"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Azure 認知搜索中的 OData`eq`比較`ne`運算子`gt` `lt`- `ge`、、、、、、、、、、、和`le`

Azure 認知搜索中的[OData 篩選器運算式](query-odata-filter-orderby-syntax.md)中最基本的操作是將欄位與給定值進行比較。 兩種類型的比較是可能的 - 相等比較和範圍比較。 可以使用以下運算子將欄位與常量值進行比較：

相等運算子：

- `eq`：測試欄位是否**等於**常量值
- `ne`：測試欄位**是否不等於**常量值

範圍運算子：

- `gt`：測試欄位是否**大於**常量值
- `lt`：測試欄位是否**小於**常量值
- `ge`： 測試欄位是否**大於或等於**常量值
- `le`： 測試欄位是否**小於或等於**常量值

可以將範圍運算子與[邏輯運算子](search-query-odata-logical-operators.md)結合使用，以測試欄位是否在特定值範圍內。 請參閱本文後面的[示例](#examples)。

> [!NOTE]
> 如果您願意，可以將常量值放在運算子的左側，將欄位名稱放在右側。 對於範圍運算子，比較的含義是相反的。 例如，如果常量值位於左側，`gt`則將測試常量值是否大於欄位。 您還可以使用比較運算子將函數的結果（如`geo.distance`）與值進行比較。 對於布耳函數（如`search.ismatch`），將結果與`true``false`或 進行比較是可選的。

## <a name="syntax"></a>語法

以下 EBNF （[擴展 Backus-Naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)） 定義了使用比較運算子的 OData 運算式的語法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

有兩種形式的比較運算式。 它們之間的唯一區別是常量是否出現在運算子的左側或右側。 運算子另一側的運算式必須是**變數**或函式呼叫。 變數可以是欄位名稱，也可以是[lambda 運算式](search-query-odata-collection-operators.md)中的範圍變數。

## <a name="data-types-for-comparisons"></a>用於比較的資料類型

比較運算子兩側的資料類型必須相容。 例如，如果左側是類型的`Edm.DateTimeOffset`欄位，則右側必須是日期-時間常量。 數位資料類型更靈活。 您可以將任何數數值型別的變數和函數與任何其他數數值型別的常量進行比較，並有幾個限制，如下表所述。

| 變數或函數類型 | 常量數值型別 | 限制 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 比較受[特殊規則的約束： `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | 常量轉換為`Edm.Double`，導致大量值的精度損失 |
| `Edm.Double` | `Edm.Int32` | n/a |
| `Edm.Int64` | `Edm.Double` | 不允許與`NaN``-INF`比較`INF`。 |
| `Edm.Int64` | `Edm.Int64` | n/a |
| `Edm.Int64` | `Edm.Int32` | 常量在比較`Edm.Int64`前轉換為 |
| `Edm.Int32` | `Edm.Double` | 不允許與`NaN``-INF`比較`INF`。 |
| `Edm.Int32` | `Edm.Int64` | n/a |
| `Edm.Int32` | `Edm.Int32` | n/a |

對於不允許的比較，例如將類型`Edm.Int64`欄位與`NaN`比較 Azure 認知搜索 REST API 將返回"HTTP 400：錯誤請求"錯誤。

> [!IMPORTANT]
> 即使數數值型別比較是靈活的，我們強烈建議在篩選器中編寫比較，以便常量值與要比較它的變數或函數的資料類型相同。 這在混合浮點和整數值時尤其重要，因為其中可能存在失去精度的隱式轉換。

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>和 的`null`特殊情況`NaN`

使用比較運算子時，請務必記住，Azure 認知搜索中的所有非集合欄位都`null`可能是 。 下表顯示了比較運算式的所有可能結果，其中任一方可以是`null`：

| 運算子 | 當只有欄位或變數時，結果`null` | 當只有常量時，結果`null` | 當欄位或變數和常量都`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400： 錯誤請求錯誤 | HTTP 400： 錯誤請求錯誤 |
| `lt` | `false` | HTTP 400： 錯誤請求錯誤 | HTTP 400： 錯誤請求錯誤 |
| `ge` | `false` | HTTP 400： 錯誤請求錯誤 | HTTP 400： 錯誤請求錯誤 |
| `le` | `false` | HTTP 400： 錯誤請求錯誤 | HTTP 400： 錯誤請求錯誤 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

總之，`null`僅等於自身，並且不小於或大於任何其他值。

如果您的索引具有類型`Edm.Double`欄位，並且將值上載`NaN`到這些欄位，則在編寫篩選器時需要考慮這一點。 Azure 認知搜索實現 IEEE 754`NaN`標準來處理值，與此類值的比較會產生非明顯結果，如下表所示。

| 運算子 | 當至少一個運算元是`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

總之，`NaN`不等於任何值，包括本身。

### <a name="comparing-geo-spatial-data"></a>比較地理空間資料

不能將類型的`Edm.GeographyPoint`欄位與常量值直接進行比較，但可以使用 函數`geo.distance`。 此函數返回類型`Edm.Double`值，因此您可以將其與數值常量進行比較，以便根據與常量地理空間座標的距離進行篩選。 請參閱下面的[示例](#examples)。

### <a name="comparing-string-data"></a>比較字串資料

可以使用 和`eq``ne`運算子在篩選器中比較字串，以便進行精確匹配。 這些比較區分大小寫。

## <a name="examples"></a>範例

匹配`Rating`欄位介於 3 和 5 之間的文檔，包括：

    Rating ge 3 and Rating le 5

與`Location`給定緯度和經度小於 2 公里的欄位匹配文檔：

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

匹配`LastRenovationDate`欄位大於或等於 2015 年 1 月 1 日午夜 UTC 的文檔：

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

匹配欄位不是`null`的文檔`Details/Sku`：

    Details/Sku ne null

匹配至少有一個房間鍵入"豪華房"的酒店的文檔，其中`Rooms/Type`欄位的字串與篩選器完全符合：

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜索中的篩選器](search-filters.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
