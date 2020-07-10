---
title: OData 比較運算子參考
titleSuffix: Azure Cognitive Search
description: 使用 OData 比較運算子的語法和參考檔， (Azure 認知搜尋查詢中的 eq、ne、gt、lt、ge 和 le) 。
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
ms.openlocfilehash: 572b653a49833ae06ee57b1718000e8555239de7
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146021"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Azure 認知搜尋中的 OData 比較運算子- `eq` 、、、、 `ne` `gt` `lt` `ge` 和`le`

Azure 認知搜尋的[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)中最基本的作業，就是將欄位與指定的值進行比較。 有兩種可能的比較類型--相等比較和範圍比較。 您可以使用下列運算子來比較欄位與常數值：

等號比較運算子：

- `eq`：測試欄位是否**等於**常數值
- `ne`：測試欄位是否**不等於**常數值

範圍運算子：

- `gt`：測試欄位是否**大於**常數值
- `lt`：測試欄位是否**小於**常數值
- `ge`：測試欄位是否**大於或等於**常數值
- `le`：測試欄位是否**小於或等於**常數值

您可以搭配使用範圍運算子與[邏輯運算子](search-query-odata-logical-operators.md)，測試欄位是否在特定的值範圍內。 請參閱本文稍後的[範例](#examples)。

> [!NOTE]
> 如果您想要的話，可以將常數值放在運算子的左邊，並在右邊加上功能變數名稱。 若為範圍運算子，比較的意義會相反。 例如，如果常數值位於左邊， `gt` 會測試常數值是否大於欄位。 您也可以使用比較運算子來比較函式的結果（例如 `geo.distance` ）與值。 針對布耳函數（例如 `search.ismatch` ），比較結果與 `true` 或 `false` 是選擇性的。

## <a name="syntax"></a>語法

下列 EBNF ([Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義使用比較運算子之 OData 運算式的文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

比較運算式有兩種形式。 兩者之間唯一的差異在於常數是否出現在運算子的左邊或右邊。 運算子另一端的運算式必須是**變數**或函式呼叫。 變數可以是功能變數名稱，或[lambda 運算式](search-query-odata-collection-operators.md)案例中的範圍變數。

## <a name="data-types-for-comparisons"></a>用於比較的資料類型

比較運算子兩邊的資料類型必須相容。 例如，如果左側是類型的欄位 `Edm.DateTimeOffset` ，則右側必須是日期時間常數。 數值資料類型更具彈性。 您可以使用任何其他數數值型別的常數來比較任何數數值型別的變數和函式，但有一些限制，如下表所述。

| 變數或函數類型 | 常數數值型別 | 限制 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 比較會受到[的 `NaN` 特殊規則所規範](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | 常數會轉換成 `Edm.Double` ，因而導致大量值的精確度遺失 |
| `Edm.Double` | `Edm.Int32` | n/a |
| `Edm.Int64` | `Edm.Double` | `NaN` `-INF` 不允許與、或 `INF` 的比較 |
| `Edm.Int64` | `Edm.Int64` | n/a |
| `Edm.Int64` | `Edm.Int32` | 常數會 `Edm.Int64` 在比較之前轉換成 |
| `Edm.Int32` | `Edm.Double` | `NaN` `-INF` 不允許與、或 `INF` 的比較 |
| `Edm.Int32` | `Edm.Int64` | n/a |
| `Edm.Int32` | `Edm.Int32` | n/a |

若為不允許的比較（例如，比較類型的欄位 `Edm.Int64` 與 `NaN` ），Azure 認知搜尋 REST API 會傳回「HTTP 400：不正確的要求」錯誤。

> [!IMPORTANT]
> 雖然數數值型別比較有彈性，但我們強烈建議您在篩選中撰寫比較，讓常數值與比較的變數或函數具有相同的資料類型。 混合浮點和整數值時，這一點特別重要，因為可能會失去精確度的隱含轉換。

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>和的特殊案例 `null``NaN`

使用比較運算子時，請務必記住，Azure 認知搜尋中的所有非集合欄位都可能是 `null` 。 下表顯示比較運算式的所有可能結果，其中任一端可以是 `null` ：

| 運算子 | 只有欄位或變數為時才會產生`null` | 只有當常數為時才會產生`null` | 當欄位或變數和常數都是時的結果`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400：不正確的要求錯誤 | HTTP 400：不正確的要求錯誤 |
| `lt` | `false` | HTTP 400：不正確的要求錯誤 | HTTP 400：不正確的要求錯誤 |
| `ge` | `false` | HTTP 400：不正確的要求錯誤 | HTTP 400：不正確的要求錯誤 |
| `le` | `false` | HTTP 400：不正確的要求錯誤 | HTTP 400：不正確的要求錯誤 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

總而言之， `null` 只等於其本身，而且不小於或大於任何其他值。

如果您的索引具有類型為的欄位 `Edm.Double` ，而且您 `NaN` 將值上傳至這些欄位，則在撰寫篩選器時，您必須考慮此情況。 Azure 認知搜尋會實作為處理值的 IEEE 754 標準 `NaN` ，而這類值的比較會產生不明顯的結果，如下表所示。

| 運算子 | 當至少有一個運算元為時的結果`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

總而言之，不 `NaN` 等於任何值，包括本身。

### <a name="comparing-geo-spatial-data"></a>比較地理空間資料

您無法直接比較類型的欄位 `Edm.GeographyPoint` 與常數值，但您可以使用 `geo.distance` 函數。 此函式會傳回類型的值 `Edm.Double` ，因此您可以將它與數值常數做比較，以根據來自常數地理空間座標的距離進行篩選。 請參閱下列[範例](#examples)。

### <a name="comparing-string-data"></a>比較字串資料

您可以使用和運算子，在篩選中比較字串中的完全相符專案 `eq` `ne` 。 這些比較會區分大小寫。

## <a name="examples"></a>範例

符合 `Rating` 欄位介於3和5之間的檔（包含：

```text
Rating ge 3 and Rating le 5
```

符合 `Location` 指定緯度和經度的欄位小於2公里的檔：

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

符合 `LastRenovationDate` 欄位大於或等於2015年1月1日午夜 UTC 的檔：

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

符合 `Details/Sku` 欄位不是的檔 `null` ：

```text
Details/Sku ne null
```

比對檔，其中至少有一個房間的類型為 "Deluxe 室"，其中欄位的字串 `Rooms/Type` 完全符合篩選準則：

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
