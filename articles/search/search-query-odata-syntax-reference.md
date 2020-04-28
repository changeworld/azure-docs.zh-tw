---
title: OData 運算式語法參考
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋查詢中 OData 運算式的正式文法和語法規格。
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
ms.openlocfilehash: f3422fd10e062ae87bc165491e0d01ac2b4943d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72793241"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Azure 認知搜尋的 OData 運算式語法參考

Azure 認知搜尋會使用[OData 運算式](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html)做為整個 API 中的參數。 最常見的是，OData 運算式是用於`$orderby`和`$filter`參數。 這些運算式可能很複雜，其中包含多個子句、函數和運算子。 不過，即使是屬性路徑之類的簡單 OData 運算式，也會用於 Azure 認知搜尋 REST API 的許多部分。 例如，路徑運算式是用來參考 API 中的所有複雜欄位的子欄位，例如在[建議工具](index-add-suggesters.md)中列出子欄位、[計分函數](index-add-scoring-profiles.md)、 `$select`參數，或甚至是[在 Lucene 查詢中回復搜尋](query-lucene-syntax.md)。

本文說明所有使用正式文法的 OData 運算式形式。 另外還有一個[互動式圖表](#syntax-diagram)，可協助您以視覺化方式流覽文法。

## <a name="formal-grammar"></a>正式文法

我們可以使用 EBNF （[延伸巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）文法來描述 Azure 認知搜尋所支援的 OData 語言子集。 規則會以「由上而下」列出，從最複雜的運算式開始，再將其細分為更基本的運算式。 最上方是對應至 Azure 認知搜尋之特定參數的文法規則 REST API：

- [`$filter`](search-query-odata-filter.md)，由`filter_expression`規則定義。
- [`$orderby`](search-query-odata-orderby.md)，由`order_by_expression`規則定義。
- [`$select`](search-query-odata-select.md)，由`select_expression`規則定義。
- 欄位路徑，由`field_path`規則定義。 在整個 API 中都會使用欄位路徑。 它們可以參考索引的最上層欄位，或是具有一或多個[複雜欄位](search-howto-complex-data-types.md)祖系的子欄位。

在 EBNF 是可流覽的[語法圖表](https://en.wikipedia.org/wiki/Syntax_diagram)之後，可讓您以互動方式流覽文法和其規則之間的關聯性。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>語法圖表

若要以視覺化方式探索 Azure 認知搜尋所支援的 OData 語言文法，請嘗試互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>另請參閱  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 查詢語法](query-lucene-syntax.md)
- [Azure 認知搜尋中的簡單查詢語法](query-simple-syntax.md)
