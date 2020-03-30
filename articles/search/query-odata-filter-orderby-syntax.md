---
title: OData 語言概述
titleSuffix: Azure Cognitive Search
description: 用於 Azure 認知搜索查詢的篩選器、選擇和訂單的 OData 語言概述。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153871"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>OData 語言概述`$filter`，`$orderby`和`$select`在 Azure 認知搜索中

Azure 認知搜索支援 **$filter、$orderby**和 **$select**運算式的 OData 運算式語法的子集。 **$orderby** 在進行查詢剖析、將搜尋限定於特定欄位，或在索引掃描期間新增比對準則時，會評估篩選運算式。 按訂單運算式作為後處理步驟應用於結果集，以對返回的文檔進行排序。 選擇運算式確定結果集中包含哪些文檔欄位。 這些運算式的語法不同于**搜索**參數中使用的[簡單](query-simple-syntax.md)或[完整](query-lucene-syntax.md)查詢語法，儘管引用欄位的語法中有一些重疊。

本文概述了篩選器、排序和選擇運算式中使用的 OData 運算式語言。 語言呈現"自下而上"，從最基本的元素開始，並在此基礎上。 單獨一篇文章中描述了每個參數的頂級語法：

- [$filter語法](search-query-odata-filter.md)
- [$orderby語法](search-query-odata-orderby.md)
- [$select語法](search-query-odata-select.md)

OData 運算式的範圍從簡單到高度複雜，但它們都共用公共元素。 Azure 認知搜索中 OData 運算式的最基本部分是：

- **欄位路徑**，它引用索引的特定欄位。
- **常量**，是特定資料類型的文本值。

> [!NOTE]
> Azure 認知搜索中的術語在幾個方面與[OData 標準](https://www.odata.org/documentation/)不同。 我們在 Azure 認知搜索中稱為**欄位**，在 OData 中稱為**屬性**，對於**欄位路徑**與**屬性路徑**類似。 包含 Azure 認知搜索中**文檔的****索引**在 OData 中通常稱為包含實體的**實體****集**。 Azure 認知搜索術語將在整個引用中使用。

## <a name="field-paths"></a>欄位路徑

以下 EBNF （[擴展 Backus-Naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)） 定義了欄位路徑的語法.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

欄位路徑由一個或多個識別符組成，這些**識別碼**由斜杠分隔。 每個識別碼都是一個字元序列，必須以 ASCII 字母或底線開頭，並且僅包含 ASCII 字母、數位或底線。 字母可以是大寫或小寫。

識別碼可以引用欄位的名稱，也可以引用篩選器中[集合運算式](search-query-odata-collection-operators.md)（`any`或`all`） 上下文中**的範圍變數**。 範圍變數類似于表示集合的當前元素的迴圈變數。 對於複雜集合，該變數表示物件，這就是為什麼可以使用欄位路徑引用變數的子欄位。 這類似于許多程式設計語言中的點符號。

下表顯示了欄位路徑的示例：

| 欄位路徑 | 描述 |
| --- | --- |
| `HotelName` | 引用索引的頂級欄位 |
| `Address/City` | 指索引中`City`複雜欄位的子欄位;`Address`是本示例中`Edm.ComplexType`的類型 |
| `Rooms/Type` | 指索引中`Type`複雜集合欄位的子欄位;`Rooms`是本示例中`Collection(Edm.ComplexType)`的類型 |
| `Stores/Address/Country` | 指索引中`Country`複雜集合欄位`Address`的子欄位的子欄位的子欄位;`Stores`是類型`Collection(Edm.ComplexType)`，`Address`在本示例中為類型`Edm.ComplexType` |
| `room/Type` | 引用範圍變數`Type`的`room`子欄位，例如在篩選器運算式中`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | 引用`store`範圍變數`Country`子`Address`欄位的子欄位，例如在篩選器運算式中`Stores/any(store: store/Address/Country eq 'Canada')` |

欄位路徑的含義因上下文而異。 在篩選器中，欄位路徑引用當前文檔中欄位*的單個實例*的值。 在其他上下文中（如 **$orderby、$select**或 **$select**[完整 Lucene 語法中的欄位搜索中](query-lucene-syntax.md#bkmk_fields)，欄位路徑引用欄位本身。 這種差異對如何在篩選器中使用欄位路徑會產生一些影響。

考慮欄位路徑`Address/City`。 在篩選器中，這是指當前文檔的單個城市，如"三藩市"。 相反，`Rooms/Type`指許多房間的`Type`子場（如第一個房間的"標準"，第二個房間的"豪華"，等等）。 由於`Rooms/Type`不引用子欄位`Type`的*單個實例*，因此不能直接在篩選器中使用。 相反，要篩選房間類型，請使用具有範圍變數的[lambda 運算式](search-query-odata-collection-operators.md)，如下所示：

    Rooms/any(room: room/Type eq 'deluxe')

在此示例中，範圍變數`room`將顯示在欄位路徑中。 `room/Type` 這樣，`room/Type`是指當前文檔中當前會議室的類型。 這是子欄位的`Type`單個實例，因此可以直接在篩選器中使用。

### <a name="using-field-paths"></a>使用欄位路徑

欄位路徑用於[Azure 認知搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)的許多參數。 下表列出了可以使用它們的所有位置，以及對其使用的任何限制：

| API | 參數名稱 | 限制 |
| --- | --- | --- |
| [創建](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `suggesters/sourceFields` | None |
| [創建](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/text/weights` | 只能引用**可搜索**欄位 |
| [創建](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/functions/fieldName` | 只能引用**可篩選**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`何時`queryType`是`full` | 只能引用**可搜索**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | 只能引用**可面欄位** |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | 只能引用**可搜索**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | 只能引用**可搜索**欄位 |
| [建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | 只能引用建議器的一部分的[欄位](index-add-suggesters.md) |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents)、[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | 只能引用**可篩選**欄位 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents)和[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | 只能引用**可排序**欄位 |
| [搜索](https://docs.microsoft.com/rest/api/searchservice/search-documents)、[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[查找](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | 只能引用**可檢索**欄位 |

## <a name="constants"></a>常數

OData 中的常量是給定[實體資料模型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)（EDM） 類型的字面值。 有關 Azure 認知搜索中受支援類型的清單，請參閱[支援資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)。 不支援集合類型的常量。

下表顯示了 Azure 認知搜索支援的每個資料類型的常量示例：

| 資料類型 | 常量示例 |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>在字串常量中轉義特殊字元

OData 中的字串常量由單個引號分隔。 如果需要構造具有可能本身包含單個引號的字串常量的查詢，則可以通過加倍引用來逃避嵌入的報價。

例如，具有未格式化撇號（如"Alice 的車"）的短語將在 OData 中表示為字串常量`'Alice''s car'`。

> [!IMPORTANT]
> 以程式設計方式構造篩選器時，請務必記住從使用者輸入中逸出字元串常量。 這是為了減輕[注入攻擊](https://wikipedia.org/wiki/SQL_injection)的可能性，尤其是在使用篩檢程式實現[安全修整](search-security-trimming-for-azure-search.md)時。

### <a name="constants-syntax"></a>常量語法

以下 EBNF（[擴展 Backus-Naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義了上表中顯示的大多數常量的語法。 地理空間類型的語法可以在[Azure 認知搜索 中的 OData 地理空間函數中找到](search-query-odata-geo-spatial-functions.md)。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

## <a name="building-expressions-from-field-paths-and-constants"></a>從欄位路徑和常量生成運算式

欄位路徑和常量是 OData 運算式最基本的部分，但它們本身已經是完整的運算式。 事實上，Azure 認知搜索中的 **$select**參數只不過是欄位路徑的逗號分隔清單 **，$orderby**並不比 **$select**複雜得多。 如果索引中碰巧有一個類型`Edm.Boolean`欄位，甚至可以編寫一個篩選器，該篩選器只是該欄位的路徑。 常量`true`和`false`同樣有效的篩選器。

但是，大多數情況下，您需要更複雜的運算式來引用多個欄位和常量。 這些運算式根據參數的不同生成方式。

以下 EBNF（[擴展 Backus-Naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義了 **$filter、$orderby**和 **$orderby****$select**參數的語法。 這些由引用欄位路徑和常量的更簡單的運算式構建：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

**$orderby**和 **$select**參數都是比較簡單的運算式的逗號分隔清單。 **$filter**參數是由更簡單的子運算式組成的布林運算式。 這些子運算式使用邏輯運算子（如[`and`、`or`和`not`](search-query-odata-logical-operators.md)、比較運算子（如[`eq`、、、、`gt``lt`等](search-query-odata-comparison-operators.md)）和集合運算子（如[`any`和`all`](search-query-odata-collection-operators.md)）進行組合。

**$filter、$orderby**和 **$select**參數在以下文章中進行了更詳細的探討： **$orderby**

- [Azure 認知搜索中的 OData $filter語法](search-query-odata-filter.md)
- [OData$orderby Azure 認知搜索中的語法](search-query-odata-orderby.md)
- [Azure 認知搜索中的 OData $select語法](search-query-odata-select.md)

## <a name="see-also"></a>另請參閱  

- [Azure 認知搜索中的分面導航](search-faceted-navigation.md)
- [Azure 認知搜索中的篩選器](search-filters.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 查詢語法](query-lucene-syntax.md)
- [Azure 認知搜索中的簡單查詢語法](query-simple-syntax.md)
