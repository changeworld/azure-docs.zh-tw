---
title: OData 語言總覽
titleSuffix: Azure Cognitive Search
description: 適用于 Azure 認知搜尋查詢的篩選、選取和排序的 OData 語言總覽。
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
ms.openlocfilehash: d04311fce81d147a0830918aee1d4a2a9c0808d4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923393"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>適用于 `$filter` 、和的 OData 語言總覽 `$orderby` `$select` Azure 認知搜尋

Azure 認知搜尋支援 **$filter**、 **$orderby**和 **$select** 運算式的 OData 運算式語法子集。 在進行查詢剖析、將搜尋限定於特定欄位，或在索引掃描期間新增比對準則時，會評估篩選運算式。 排序依據運算式會在結果集上套用為後置處理步驟，以排序傳回的檔。 選取 [運算式] 可決定結果集中包含的檔欄位。 這些運算式的語法與**搜尋**參數中使用的[簡單](query-simple-syntax.md)或[完整](query-lucene-syntax.md)查詢語法不同，雖然參考欄位的語法有一些重迭。

本文概述用於篩選、排序依據和選取運算式的 OData 運算式語言。 語言會顯示為「自下而上」，從最基本的元素開始，並加以建立。 個別的文章會說明每個參數的最上層語法：

- [$filter 語法](search-query-odata-filter.md)
- [$orderby 語法](search-query-odata-orderby.md)
- [$select 語法](search-query-odata-select.md)

OData 運算式的範圍從簡單到高度複雜，但全都共用一般元素。 Azure 認知搜尋的 OData 運算式中最基本的部分為：

- **欄位路徑**，指的是索引的特定欄位。
- **常數**，也就是特定資料類型的常值。

> [!NOTE]
> Azure 認知搜尋中的術語與 [OData 標準](https://www.odata.org/documentation/) 有幾種不同之處。 我們在 Azure 認知搜尋中呼叫 **欄位** 的方式稱為 OData 中的 **屬性** ，類似于 **欄位路徑** 與 **屬性路徑**。 包含 Azure 認知搜尋中**檔**的**索引**，在 OData 中通常稱為包含**實體**的**實體集**。 本參考中使用 Azure 認知搜尋術語。

## <a name="field-paths"></a>欄位路徑

下列 EBNF ([外延巴克斯格斯表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義欄位路徑的文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

欄位路徑是由一或多個以斜線分隔的 **識別碼** 所組成。 每個識別碼都是一串字元，必須以 ASCII 字母或底線開頭，而且只包含 ASCII 字母、數位或底線。 字母可以是大寫或小寫。

識別碼可以參考欄位的名稱，或參考[集合運算式](search-query-odata-collection-operators.md)內容中的**範圍變數** (`any` 或 `all` 在篩選中) 。 範圍變數就像是迴圈變數，代表集合的目前元素。 針對複雜的集合，該變數表示物件，這也是為什麼您可以使用欄位路徑來參考變數的子欄位。 這類似于許多程式設計語言中的點標記法。

下表顯示欄位路徑的範例：

| 欄位路徑 | 描述 |
| --- | --- |
| `HotelName` | 參考索引的最上層欄位 |
| `Address/City` | 參考 `City` 索引中複雜欄位的子欄位， `Address` `Edm.ComplexType` 在此範例中為類型 |
| `Rooms/Type` | 參考 `Type` 索引中複雜集合欄位的子欄位; `Rooms` `Collection(Edm.ComplexType)` 此範例中的類型為 |
| `Stores/Address/Country` | 參考 `Country` `Address` 索引中複雜集合欄位之子欄位的子欄位; `Stores` 的類型為 `Collection(Edm.ComplexType)` ， `Address` `Edm.ComplexType` 在此範例中為類型。 |
| `room/Type` | 參考 `Type` 範圍變數的子欄位 `room` ，例如在篩選運算式中 `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | 參考 `Country` `Address` 範圍變數之子欄位的子欄位 `store` ，例如在篩選運算式中 `Stores/any(store: store/Address/Country eq 'Canada')` |

欄位路徑的意義會因內容而有所不同。 在篩選中，欄位路徑會參考目前檔中欄位的 *單一實例* 值。 在其他內容中，例如 **$orderby**、 **$select**或 [回復搜尋中的完整 Lucene 語法](query-lucene-syntax.md#bkmk_fields)，欄位路徑會參考欄位本身。 這項差異對於您如何在篩選中使用欄位路徑有一些影響。

請考慮欄位路徑 `Address/City` 。 在篩選中，這是指目前檔的單一城市，例如「三藩市」。 相反地，會 `Rooms/Type` 參考 `Type` 許多房間的子欄位 (例如，第一個房間的 "standard"、第二個房間的 "deluxe" 等) 。 由於 `Rooms/Type` 不會參考子欄位的 *單一實例* `Type` ，因此不能直接用於篩選中。 相反地，若要篩選房間型別，您可以使用具有範圍變數的 [lambda 運算式](search-query-odata-collection-operators.md) ，如下所示：

```odata
Rooms/any(room: room/Type eq 'deluxe')
```

在此範例中，範圍變數 `room` 會出現在 `room/Type` 欄位路徑中。 如此一來，就會 `room/Type` 參考目前檔中目前房間的型別。 這是子欄位的單一實例 `Type` ，因此可以直接用於篩選中。

### <a name="using-field-paths"></a>使用欄位路徑

欄位路徑用於 [AZURE 認知搜尋 REST api](/rest/api/searchservice/)的許多參數中。 下表列出可用的所有位置，以及其使用方式的任何限制：

| API | 參數名稱 | 限制 |
| --- | --- | --- |
| [建立](/rest/api/searchservice/create-index) 或 [更新](/rest/api/searchservice/update-index) 索引 | `suggesters/sourceFields` | 無 |
| [建立](/rest/api/searchservice/create-index) 或 [更新](/rest/api/searchservice/update-index) 索引 | `scoringProfiles/text/weights` | 只能**參考可搜尋的欄位** |
| [建立](/rest/api/searchservice/create-index) 或 [更新](/rest/api/searchservice/update-index) 索引 | `scoringProfiles/functions/fieldName` | 只能參考可 **篩選** 的欄位 |
| [搜尋](/rest/api/searchservice/search-documents) | `search` 當 `queryType` 為 `full` | 只能**參考可搜尋的欄位** |
| [搜尋](/rest/api/searchservice/search-documents) | `facet` | 只能參考 **可 facet** 欄位 |
| [搜尋](/rest/api/searchservice/search-documents) | `highlight` | 只能**參考可搜尋的欄位** |
| [搜尋](/rest/api/searchservice/search-documents) | `searchFields` | 只能**參考可搜尋的欄位** |
| [建議](/rest/api/searchservice/suggestions) 和 [自動完成](/rest/api/searchservice/autocomplete) | `searchFields` | 只能參考屬於[建議工具](index-add-suggesters.md)一部分的欄位 |
| [搜尋](/rest/api/searchservice/search-documents)、 [建議](/rest/api/searchservice/suggestions)和 [自動完成](/rest/api/searchservice/autocomplete) | `$filter` | 只能參考可 **篩選** 的欄位 |
| [搜尋](/rest/api/searchservice/search-documents) 和 [建議](/rest/api/searchservice/suggestions) | `$orderby` | 只能參考可 **排序** 的欄位 |
| [搜尋](/rest/api/searchservice/search-documents)、 [建議](/rest/api/searchservice/suggestions)和 [查閱](/rest/api/searchservice/lookup-document) | `$select` | 只能**參考可抓取的欄位** |

## <a name="constants"></a>常數

OData 中的常數是給定 [實體資料模型](/dotnet/framework/data/adonet/entity-data-model) (EDM) 類型的常值。 請參閱 [支援的資料類型](/rest/api/searchservice/supported-data-types) ，以取得 Azure 認知搜尋中支援的類型清單。 不支援集合類型的常數。

下錶針對 Azure 認知搜尋所支援的每個資料類型，顯示常數的範例：

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

### <a name="escaping-special-characters-in-string-constants"></a>將字串常數中的特殊字元轉義

OData 中的字串常數以單引號分隔。 如果您需要使用可能包含單引號的字串常數來建立查詢，您可以將內嵌的引號加倍，以將其換成雙引號。

例如，具有未格式化之撇號的片語（例如 "Alice's car"）在 OData 中會以字串常數表示 `'Alice''s car'` 。

> [!IMPORTANT]
> 以程式設計方式建立篩選器時，請務必記得要將來自使用者輸入的字串常數進行換用。 這是為了降低 [插入式攻擊](https://wikipedia.org/wiki/SQL_injection)的可能性，特別是在使用篩選器來執行 [安全性](search-security-trimming-for-azure-search.md)調整時。

### <a name="constants-syntax"></a>常數語法

下列 EBNF ([外延巴克斯格斯表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義上表所示大部分常數的文法。 您可以在 [Azure 認知搜尋的 OData 地理空間函式](search-query-odata-geo-spatial-functions.md)中找到地理空間類型的文法。

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

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

## <a name="building-expressions-from-field-paths-and-constants"></a>從欄位路徑和常數建立運算式

欄位路徑和常數是 OData 運算式中最基本的部分，但它們本身已經是完整的運算式。 事實上，Azure 認知搜尋中的 **$select** 參數不是，而是以逗號分隔的欄位路徑清單，而且 **$orderby** 的內容不如 **$select**複雜。 如果您的索引中有類型的欄位 `Edm.Boolean` ，您甚至可以撰寫不是任何專案的篩選，而是該欄位的路徑。 常數 `true` 和 `false` 同樣是有效的篩選準則。

不過，大部分的情況下，您需要更複雜的運算式來參考一個以上的欄位和常數。 這些運算式會根據參數以不同的方式來建立。

下列 EBNF ([外延巴克斯格斯表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義 **$filter**、 **$orderby**和 **$select** 參數的文法。 這些是由簡單的運算式所建立，這些運算式會參考欄位路徑和常數：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

**$Orderby**和 **$select**參數都是簡單運算式的逗號分隔清單。 **$Filter**參數是由較簡單子運算式所組成的布林運算式。 這些子運算式會使用邏輯運算子[ `and` `or` `not` 結合，例如、和、比較](search-query-odata-logical-operators.md)運算子（如[ `eq` 、 `lt` `gt` ](search-query-odata-comparison-operators.md)、等）和集合運算子（例如[ `any` 和 `all` ](search-query-odata-collection-operators.md)）。

下列文章會更詳細地探索 **$filter**、 **$orderby**和 **$select** 參數：

- [Azure 認知搜尋中的 OData $filter 語法](search-query-odata-filter.md)
- [Azure 認知搜尋中的 OData $orderby 語法](search-query-odata-orderby.md)
- [Azure 認知搜尋中的 OData $select 語法](search-query-odata-select.md)

## <a name="see-also"></a>請參閱  

- [Azure 認知搜尋中的多面向導覽](search-faceted-navigation.md)
- [Azure 認知搜尋中的篩選](search-filters.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene 查詢語法](query-lucene-syntax.md)
- [Azure 認知搜尋中的簡單查詢語法](query-simple-syntax.md)