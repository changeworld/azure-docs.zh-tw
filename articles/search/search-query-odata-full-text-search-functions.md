---
title: O資料全文檢索搜尋功能參考
titleSuffix: Azure Cognitive Search
description: OData 全文檢索搜尋功能，搜索.ismatch 和搜索.ismatch 評分，在 Azure 認知搜索查詢中。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793358"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData 全文檢索搜尋功能在 Azure 認知搜索`search.ismatch`- 和`search.ismatchscoring`

Azure 認知搜索支援通過`search.ismatch`和`search.ismatchscoring`函數在[OData 篩選器運算式](query-odata-filter-orderby-syntax.md)的上下文中進行全文檢索搜尋。 這些函數允許您將全文檢索搜尋與嚴格的布林篩選相結合，其方式僅通過使用搜索`search`[API](https://docs.microsoft.com/rest/api/searchservice/search-documents)的頂級參數是不可能的。

> [!NOTE]
> 和`search.ismatch``search.ismatchscoring`函數僅在[搜索 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)中的篩選器中受支援。 [建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)或[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)API 中不支援它們。

## <a name="syntax"></a>語法

以下 EBNF （[擴展 Backus-Naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）`search.ismatch`定義了`search.ismatchscoring`和 函數的語法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

### <a name="searchismatch"></a>搜索.ismatch

該`search.ismatch`函數將全文檢索搜尋查詢計算為篩選器運算式的一部分。 符合搜尋查詢的文件將會在結果集中傳回。 此函式有下列多載可供使用：

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

參數在下表中定義：

| 參數名稱 | 類型 | 描述 |
| --- | --- | --- |
| `search` | `Edm.String` | 搜索查詢（[以簡單](query-simple-syntax.md)或[完整的](query-lucene-syntax.md)Lucene 查詢語法）。 |
| `searchFields` | `Edm.String` | 要搜索的可搜索欄位的逗號分隔清單;預設值為索引中的所有可搜索欄位。 在`search`參數中使用[欄位搜索](query-lucene-syntax.md#bkmk_fields)時，Lucene 查詢中的欄位指定值將覆蓋此參數中指定的任何欄位。 |
| `queryType` | `Edm.String` | `'simple'`或`'full'`;預設值為`'simple'`。 指定 `search` 參數中使用的查詢語言。 |
| `searchMode` | `Edm.String` | `'any'`或`'all'`，預設值為`'any'`。 指示參數中`search`的任何搜索詞或所有搜索詞是否必須匹配才能將文檔計數為匹配項。 在`search`參數中使用[Lucene 布林運算子](query-lucene-syntax.md#bkmk_boolean)時，它們將優先于此參數。 |

上述所有參數都等效于搜索 API[中的相應搜索請求參數](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

該`search.ismatch`函數返回類型`Edm.Boolean`的值 ，它允許您使用布林[邏輯運算子](search-query-odata-logical-operators.md)使用其他篩選器子運算式對其進行組合。

> [!NOTE]
> Azure 認知搜索不支援使用或`search.ismatch``search.ismatchscoring`內部使用 lambda 運算式。 這意味著無法將文本搜索匹配與同一物件上的嚴格篩選器匹配相關聯的物件集合上寫入篩選器。 有關此限制的更多詳細資訊以及示例，請參閱 Azure[認知搜索 中的故障排除集合篩選器](search-query-troubleshoot-collection-filters.md)。 有關存在此限制的原因的更深入資訊，請參閱[在 Azure 認知搜索 中瞭解集合篩選器](search-query-understand-collection-filters.md)。


### <a name="searchismatchscoring"></a>搜索.是匹配評分

函數`search.ismatchscoring`與 函數一`search.ismatch`樣，返回`true`與作為參數傳遞的全文檢索搜尋查詢匹配的文檔。 兩者的差別在於，符合 `search.ismatchscoring` 查詢的文件所得到的相關性分數將對整體的文件分數產生貢獻，而 `search.ismatch` 則不會變更文件分數。 此函式的下列多載可與等同於 `search.ismatch` 參數的參數搭配使用：

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch`和`search.ismatchscoring`函數都可以在同一篩選器運算式中使用。

## <a name="examples"></a>範例

尋找含有「海濱」一詞的文件。 此篩選查詢等同於使用 `search=waterfront` 的[搜尋要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

    search.ismatchscoring('waterfront')

尋找含有「青年旅館」一詞、且評分為 4 或以上的文件，或含有「汽車旅館」一詞、且評分為 5 的文件。 請注意，此要求必須使用 `search.ismatchscoring` 函式來表示。

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

尋找不含「豪華」一詞的文件。

    not search.ismatch('luxury')

尋找含有「海景」一詞或評分為 5 的文件。 `search.ismatchscoring` 查詢只會對欄位 `HotelName` 和 `Rooms/Description` 執行。

只與分界第二條款相符的檔也將退回 -`Rating`等於 5 的酒店。 為了明確這些檔與運算式的任何評分部分不匹配，它們將返回的分數等於零。

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

在酒店描述中，查找"酒店"和"機場"等術語在 5 字以內的檔，並且至少某些房間內禁止吸煙。 此查詢會使用[完整 Lucene 查詢語言](query-lucene-syntax.md)。

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜索中的篩選器](search-filters.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
