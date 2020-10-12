---
title: OData 全文檢索搜尋函數參考
titleSuffix: Azure Cognitive Search
description: OData 全文檢索搜尋函數，ismatch 和 search.ismatchscoring，在 Azure 認知搜尋查詢中。
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
ms.openlocfilehash: 78f9e4d8fa80fdf74bdb5cd79f4489d12696fcc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935784"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Azure 認知搜尋中的 OData 全文檢索搜尋函數 `search.ismatch` 和 `search.ismatchscoring`

Azure 認知搜尋透過和函式，支援 [OData 篩選運算式](query-odata-filter-orderby-syntax.md) 內容中的全文檢索搜尋 `search.ismatch` `search.ismatchscoring` 。 這些函數可讓您使用 `search` [搜尋 API](/rest/api/searchservice/search-documents)的最上層參數，將全文檢索搜尋與嚴格的布林值篩選結合在一起。

> [!NOTE]
> `search.ismatch` `search.ismatchscoring` 只有[搜尋 API](/rest/api/searchservice/search-documents)中的篩選準則支援和函式。 [建議](/rest/api/searchservice/suggestions)或[自動完成](/rest/api/searchservice/autocomplete)Api 不支援這些功能。

## <a name="syntax"></a>語法

下列 EBNF ([擴充 Backus-Naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義和函數的文法 `search.ismatch` `search.ismatchscoring` ：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

### <a name="searchismatch"></a>搜尋. ismatch

函數會將 `search.ismatch` 全文檢索搜尋查詢評估為篩選運算式的一部分。 符合搜尋查詢的文件將會在結果集中傳回。 此函式有下列多載可供使用：

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

這些參數定義于下表中：

| 參數名稱 | 類型 | 說明 |
| --- | --- | --- |
| `search` | `Edm.String` | 搜尋查詢 ([簡單](query-simple-syntax.md) 或 [完整](query-lucene-syntax.md) 的 Lucene 查詢語法) 。 |
| `searchFields` | `Edm.String` | 要在其中搜尋的可搜尋欄位清單（以逗號分隔）;預設為索引中所有可搜尋的欄位。 在參數中使用 [回復搜尋](query-lucene-syntax.md#bkmk_fields) 時 `search` ，Lucene 查詢中的欄位規範會覆寫此參數中指定的任何欄位。 |
| `queryType` | `Edm.String` | `'simple'` 或 `'full'` ; 預設為 `'simple'` 。 指定 `search` 參數中使用的查詢語言。 |
| `searchMode` | `Edm.String` | `'any'` 或者 `'all'` ，預設為 `'any'` 。 指出參數中是否有任何或所有搜尋詞彙 `search` 必須符合，才能將檔計算為相符。 在參數中使用 [Lucene 布林運算子](query-lucene-syntax.md#bkmk_boolean) 時 `search` ，它們的優先順序高於此參數。 |

上述所有參數都相當於 [搜尋 API 中的對應搜尋要求參數](/rest/api/searchservice/search-documents)。

函數會傳回 `search.ismatch` 型別的值 `Edm.Boolean` ，這個值可讓您使用布林值 [邏輯運算子](search-query-odata-logical-operators.md)，將它與其他篩選子運算式一起撰寫。

> [!NOTE]
> Azure 認知搜尋不支援 `search.ismatch` `search.ismatchscoring` 在 lambda 運算式中使用或。 這表示，您無法將篩選寫入物件的集合，這些物件可以將全文檢索搜尋相符專案與相同物件上的嚴格篩選相符專案相互關聯。 如需這項限制的詳細資訊和範例，請參閱 [Azure 認知搜尋中的集合篩選疑難排解](search-query-troubleshoot-collection-filters.md)。 如需這項限制的原因的詳細資訊，請參閱 [瞭解 Azure 認知搜尋中的集合篩選](search-query-understand-collection-filters.md)。


### <a name="searchismatchscoring"></a>搜尋. search.ismatchscoring

函數（例如函式 `search.ismatchscoring` `search.ismatch` ） `true` 會針對符合作為參數傳遞之全文檢索搜尋查詢的檔傳回。 兩者的差別在於，符合 `search.ismatchscoring` 查詢的文件所得到的相關性分數將對整體的文件分數產生貢獻，而 `search.ismatch` 則不會變更文件分數。 此函式的下列多載可與等同於 `search.ismatch` 參數的參數搭配使用：

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch`和函數都 `search.ismatchscoring` 可以在相同的篩選運算式中使用。

## <a name="examples"></a>範例

尋找含有「海濱」一詞的文件。 此篩選查詢等同於使用 `search=waterfront` 的[搜尋要求](/rest/api/searchservice/search-documents)。

```odata-filter-expr
    search.ismatchscoring('waterfront')
```

尋找含有「青年旅館」一詞、且評分為 4 或以上的文件，或含有「汽車旅館」一詞、且評分為 5 的文件。 請注意，此要求必須使用 `search.ismatchscoring` 函式來表示。

```odata-filter-expr
    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5
```

尋找不含「豪華」一詞的文件。

```odata-filter-expr
    not search.ismatch('luxury')
```

尋找含有「海景」一詞或評分為 5 的文件。 `search.ismatchscoring` 查詢只會對欄位 `HotelName` 和 `Rooms/Description` 執行。

僅符合分離第二個子句的檔會傳回太--等於5的旅館 `Rating` 。 為了清楚指出這些檔不符合運算式的任何計分部分，會傳回分數等於零的部分。

```odata-filter-expr
    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5
```

在旅館的描述中尋找條款為「飯店」和「機場」的檔，其中的「飯店」和「機場」各有5個單字，且至少某些房間內不允許吸煙。 此查詢會使用[完整 Lucene 查詢語言](query-lucene-syntax.md)。

```odata-filter-expr
    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)
```

## <a name="next-steps"></a>接下來的步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)