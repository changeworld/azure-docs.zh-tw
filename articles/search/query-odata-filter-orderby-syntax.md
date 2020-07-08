---
title: OData 語言總覽
titleSuffix: Azure Cognitive Search
description: 適用于 Azure 認知搜尋查詢的篩選、選取和排序依據的 OData 語言總覽。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77153871"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>`$filter` `$orderby` `$select` Azure 認知搜尋中、和的 OData 語言總覽

Azure 認知搜尋支援 **$filter**、 **$orderby**和 **$select**運算式的 OData 運算式語法子集。 在進行查詢剖析、將搜尋限定於特定欄位，或在索引掃描期間新增比對準則時，會評估篩選運算式。 Order by 運算式會套用為結果集上的後置處理步驟，以排序傳回的檔。 選取 [運算式] 可決定哪些檔欄位包含在結果集中。 這些運算式的語法與**搜尋**參數中使用的[簡單](query-simple-syntax.md)或[完整](query-lucene-syntax.md)查詢語法不同，但參考欄位的語法有一些重迭。

本文概述用於篩選、排序依據和選取運算式的 OData 運算式語言。 語言會以「自下而上」顯示，從最基本的元素開始，並以其為基礎來建立。 每個參數的最上層語法會在個別的文章中加以說明：

- [$filter 語法](search-query-odata-filter.md)
- [$orderby 語法](search-query-odata-orderby.md)
- [$select 語法](search-query-odata-select.md)

OData 運算式的範圍從簡單到非常複雜，但全都共用通用元素。 Azure 認知搜尋中 OData 運算式的最基本部分為：

- **欄位路徑**，指的是索引的特定欄位。
- **常數**，這是特定資料類型的常值。

> [!NOTE]
> Azure 認知搜尋中的術語與[OData 標準](https://www.odata.org/documentation/)有幾種不同。 我們在「Azure 認知搜尋」中呼叫**欄位**的是 OData 中的**屬性**，類似于**欄位路徑**與**屬性路徑**。 在 OData 中，包含 Azure 認知搜尋中的**檔**的**索引**會更普遍地視為包含**實體**的**實體集**。 此參考中會使用 Azure 認知搜尋術語。

## <a name="field-paths"></a>欄位路徑

下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義欄位路徑的文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

欄位路徑是由一個或多個以斜線分隔的**識別碼**所組成。 每一個識別碼都是一串字元，必須以 ASCII 字母或底線開頭，而且只包含 ASCII 字母、數位或底線。 字母可以是大寫或小寫。

識別碼可以參考欄位的名稱，或是篩選中[集合運算式](search-query-odata-collection-operators.md)（或）內容中的**範圍變數** `any` `all` 。 範圍變數就像迴圈變數，代表集合的目前元素。 針對複雜的集合，該變數代表物件，這就是為什麼您可以使用欄位路徑來參考變數的子欄位。 這類似于許多程式設計語言中的點標記法。

下表顯示欄位路徑的範例：

| 欄位路徑 | Description |
| --- | --- |
| `HotelName` | 參考索引的最上層欄位 |
| `Address/City` | 參考 `City` 索引中複雜欄位的子欄位， `Address` `Edm.ComplexType` 在此範例中為類型 |
| `Rooms/Type` | 參考 `Type` 索引中複雜集合欄位的子欄位; `Rooms` `Collection(Edm.ComplexType)` 在此範例中的類型為 |
| `Stores/Address/Country` | 參考 `Country` `Address` 索引中複雜集合欄位之子欄位的子欄位， `Stores` 屬於類型 `Collection(Edm.ComplexType)` ，而且 `Address` `Edm.ComplexType` 在此範例中屬於類型 |
| `room/Type` | 參考 `Type` 範圍變數的子欄位 `room` ，例如在篩選運算式中`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | 參考 `Country` `Address` 範圍變數之子欄位的子欄位 `store` ，例如在篩選運算式中`Stores/any(store: store/Address/Country eq 'Canada')` |

欄位路徑的意義會因內容而有所不同。 在 [篩選] 中，[欄位路徑] 是指目前檔中欄位的*單一實例*值。 在其他內容中（例如 **$orderby**、 **$select**，或在[回復中搜尋完整 Lucene 語法](query-lucene-syntax.md#bkmk_fields)），欄位路徑會參考欄位本身。 這項差異對於您在篩選中使用欄位路徑的方式有一些影響。

請考慮欄位路徑 `Address/City` 。 在篩選中，這指的是目前檔的單一城市，例如「三藩市」。 相反地， `Rooms/Type` 指的是 `Type` 許多房間的子欄位（例如「標準」代表第一個聊天室，"deluxe" 代表第二個房間，依此類推）。 由於 `Rooms/Type` 不會參考子欄位的*單一實例* `Type` ，因此無法直接在篩選中使用。 相反地，若要篩選會議室類型，您可以使用[lambda 運算式](search-query-odata-collection-operators.md)搭配範圍變數，如下所示：

    Rooms/any(room: room/Type eq 'deluxe')

在此範例中，範圍變數 `room` 會出現在 `room/Type` 欄位路徑中。 如此一來，就 `room/Type` 表示目前檔中的目前聊天室類型。 這是子欄位的單一實例 `Type` ，因此可以直接在篩選中使用。

### <a name="using-field-paths"></a>使用欄位路徑

欄位路徑用於[Azure 認知搜尋 REST api](https://docs.microsoft.com/rest/api/searchservice/)的許多參數。 下表列出所有可用的位置，以及其使用方式的任何限制：

| API | 參數名稱 | 限制 |
| --- | --- | --- |
| [建立](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `suggesters/sourceFields` | None |
| [建立](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/text/weights` | 只能**參考可搜尋的欄位** |
| [建立](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/functions/fieldName` | 只能參考可**篩選**的欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`當 `queryType` 為時`full` | 只能**參考可搜尋的欄位** |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | 只能參考**facetable**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | 只能**參考可搜尋的欄位** |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | 只能**參考可搜尋的欄位** |
| [建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)與[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | 只能參考屬於[建議工具](index-add-suggesters.md)的欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents)、[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | 只能參考可**篩選**的欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents)和[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | 只能參考可**排序**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents)、[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[查閱](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | 只能參考可**檢索**的欄位 |

## <a name="constants"></a>常數

OData 中的常數是指定[實體資料模型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)（EDM）類型的常值。 如需 Azure 認知搜尋中支援的類型清單，請參閱[支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)。 不支援集合類型的常數。

下表顯示 Azure 認知搜尋所支援的每個資料類型的常數範例：

| 資料類型 | 範例常數 |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>在字串常數中，將特殊字元轉義

OData 中的字串常數是以單引號分隔。 如果您需要使用可能本身包含單引號的字串常數來建立查詢，您可以將內嵌引號加倍來加以轉義。

例如，具有未格式化的撇號（例如 "Alice ' car"）的片語會在 OData 中表示為字串常數 `'Alice''s car'` 。

> [!IMPORTANT]
> 以程式設計方式建立篩選器時，請務必記得要將來自使用者輸入的字串常數加以轉義。 這是為了降低[插入式攻擊](https://wikipedia.org/wiki/SQL_injection)的可能性，特別是在使用篩選器來執行[安全性](search-security-trimming-for-azure-search.md)調整時。

### <a name="constants-syntax"></a>常數語法

下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義上表所示大部分常數的文法。 地理空間類型的文法可以在[Azure 認知搜尋的 OData 地理空間函式](search-query-odata-geo-spatial-functions.md)中找到。

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

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

## <a name="building-expressions-from-field-paths-and-constants"></a>從欄位路徑和常數建立運算式

欄位路徑和常數是 OData 運算式中最基本的部分，但它們已經是完整的運算式本身。 事實上，Azure 認知搜尋中的 **$select**參數並不只是以逗號分隔的欄位路徑清單，而且 **$orderby**不會比 **$select**複雜許多。 如果您的索引中有類型為的欄位 `Edm.Boolean` ，您甚至可以撰寫不是任何內容的篩選，而是該欄位的路徑。 常數 `true` 和 `false` 同樣是有效的篩選準則。

不過，大部分的情況下，您需要更複雜的運算式來參考一個以上的欄位和常數。 這些運算式是以不同的方式建立，視參數而定。

下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）會定義 **$filter**、 **$orderby**和 **$select**參數的文法。 這些是從參考欄位路徑和常數的較簡單運算式所建立的：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

**$Orderby**和 **$select**參數都是簡單運算式的逗號分隔清單。 **$Filter**參數是由較簡單的子運算式所組成的布林運算式。 這些子運算式會使用邏輯運算子（例如、 [ `and` `or` 和 `not` ](search-query-odata-logical-operators.md)）結合，比較運算子（例如[ `eq` 、 `lt` 、 `gt` 等等](search-query-odata-comparison-operators.md)）和集合運算子（例如[ `any` 和 `all` ](search-query-odata-collection-operators.md)）。

在下列文章中會更詳細地探索 **$filter**、 **$orderby**和 **$select**參數：

- [Azure 認知搜尋中的 OData $filter 語法](search-query-odata-filter.md)
- [Azure 認知搜尋中的 OData $orderby 語法](search-query-odata-orderby.md)
- [Azure 認知搜尋中的 OData $select 語法](search-query-odata-select.md)

## <a name="see-also"></a>另請參閱  

- [Azure 認知搜尋中的多面向導覽](search-faceted-navigation.md)
- [Azure 認知搜尋中的篩選](search-filters.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 查詢語法](query-lucene-syntax.md)
- [Azure 認知搜尋中的簡單查詢語法](query-simple-syntax.md)
