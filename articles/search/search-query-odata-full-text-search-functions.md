---
title: OData 全文檢索搜尋函數參考
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋查詢中的 OData 全文檢索搜尋功能 ismatch 和 ismatchscoring。
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72793358"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Azure 認知搜尋中的 OData 全文檢索搜尋功能- `search.ismatch`和`search.ismatchscoring`

Azure 認知搜尋支援透過`search.ismatch`和`search.ismatchscoring`函式，在[OData 篩選運算式](query-odata-filter-orderby-syntax.md)的內容中進行全文檢索搜尋。 這些函數可讓您使用`search` [搜尋 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)的最上層參數，以不可能的方式，將全文檢索搜尋與嚴格的布林值篩選結合。

> [!NOTE]
> 只有`search.ismatch`在`search.ismatchscoring` [搜尋 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)的篩選中才支援和函數。 [建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)或[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)api 不支援它們。

## <a name="syntax"></a>語法

下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義`search.ismatch`和`search.ismatchscoring`函式的文法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

### <a name="searchismatch"></a>搜尋. ismatch

`search.ismatch`函數會將全文檢索搜尋查詢評估為篩選運算式的一部分。 符合搜尋查詢的文件將會在結果集中傳回。 此函式有下列多載可供使用：

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

參數定義于下表中：

| 參數名稱 | 類型 | 描述 |
| --- | --- | --- |
| `search` | `Edm.String` | 搜尋查詢（[簡單](query-simple-syntax.md)或[完整](query-lucene-syntax.md)的 Lucene 查詢語法）。 |
| `searchFields` | `Edm.String` | 以逗號分隔的清單，其中列出要搜尋的可搜尋欄位;預設為索引中所有可搜尋的欄位。 在`search`參數中使用[回復搜尋](query-lucene-syntax.md#bkmk_fields)時，Lucene 查詢中的欄位規範會覆寫此參數中指定的任何欄位。 |
| `queryType` | `Edm.String` | `'simple'`或`'full'`;預設為`'simple'`。 指定 `search` 參數中使用的查詢語言。 |
| `searchMode` | `Edm.String` | `'any'`或者`'all'`，預設為`'any'`。 指出是否必須符合`search`參數中的任何或所有搜尋詞彙，才能將檔計為相符的。 在`search`參數中使用[Lucene 布林運算子](query-lucene-syntax.md#bkmk_boolean)時，它們的優先順序高於此參數。 |

上述所有參數都相當於[搜尋 API 中對應的搜尋要求參數](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

`search.ismatch`函式會傳回類型`Edm.Boolean`的值，這可讓您使用布林[邏輯運算子](search-query-odata-logical-operators.md)，以其他篩選子運算式來撰寫它。

> [!NOTE]
> Azure 認知搜尋不支援在 lambda `search.ismatch`運算式`search.ismatchscoring`中使用或。 這表示您無法對物件集合寫入篩選器，以便將全文檢索搜尋相符專案與相同物件上的嚴格篩選準則相符相互關聯。 如需這項限制和範例的詳細資訊，請參閱針對[Azure 認知搜尋中的集合篩選進行疑難排解](search-query-troubleshoot-collection-filters.md)。 如需這項限制的原因更深入的資訊，請參閱[瞭解 Azure 認知搜尋中的集合篩選](search-query-understand-collection-filters.md)。


### <a name="searchismatchscoring"></a>搜尋. ismatchscoring

`search.ismatchscoring`函式（例如`search.ismatch`函式）會`true`針對符合當做參數傳遞之全文檢索搜尋查詢的檔傳回。 兩者的差別在於，符合 `search.ismatchscoring` 查詢的文件所得到的相關性分數將對整體的文件分數產生貢獻，而 `search.ismatch` 則不會變更文件分數。 此函式的下列多載可與等同於 `search.ismatch` 參數的參數搭配使用：

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch`和`search.ismatchscoring`函數都可以在相同的篩選條件運算式中使用。

## <a name="examples"></a>範例

尋找含有「海濱」一詞的文件。 此篩選查詢等同於使用 `search=waterfront` 的[搜尋要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

    search.ismatchscoring('waterfront')

尋找含有「青年旅館」一詞、且評分為 4 或以上的文件，或含有「汽車旅館」一詞、且評分為 5 的文件。 請注意，此要求必須使用 `search.ismatchscoring` 函式來表示。

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

尋找不含「豪華」一詞的文件。

    not search.ismatch('luxury')

尋找含有「海景」一詞或評分為 5 的文件。 `search.ismatchscoring` 查詢只會對欄位 `HotelName` 和 `Rooms/Description` 執行。

只會傳回比對之第二個子句相符的檔，也會傳回`Rating`等於5的飯店。 為了清楚說明這些檔不符合運算式的任何計分部分，它們會傳回，分數等於零。

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

尋找「飯店」和「機場」這兩個詞彙在飯店的描述中的5個單字內，而且至少有部分房間中不允許吸煙的檔。 此查詢會使用[完整 Lucene 查詢語言](query-lucene-syntax.md)。

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
