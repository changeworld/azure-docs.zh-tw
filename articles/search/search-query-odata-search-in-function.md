---
title: OData search.in 函數參考
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋查詢中使用 search.in 函數的語法和參考檔。
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
ms.openlocfilehash: 9ad6f89392846564631b70f0acfb5658a050be80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88922815"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>`search.in`Azure 認知搜尋中的 OData 函數

[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)中的常見案例是檢查每份檔中的單一欄位是否等於許多可能值的其中一個。 例如，這是某些應用程式如何執行 [安全性](search-security-trimming-for-azure-search.md) 調整的方式，就是針對代表發出查詢之使用者的主體識別碼清單，檢查包含一或多個主體識別碼的欄位。 撰寫查詢的其中一種方式就是使用 [`eq`](search-query-odata-comparison-operators.md) 和 [`or`](search-query-odata-logical-operators.md) 運算子：

```odata-filter-expr
    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')
```

不過，使用函式可讓您更輕鬆地撰寫 `search.in` ：

```odata-filter-expr
    group_ids/any(g: search.in(g, '123, 456, 789'))
```

> [!IMPORTANT]
> 除了較短且更容易閱讀，使用 `search.in` 也可提供 [效能優點](#bkmk_performance) ，並在篩選準則中包含上百個或甚至上千個值時，避免 [篩選準則的特定大小限制](search-query-odata-filter.md#bkmk_limits) 。 基於這個理由，我們強烈建議使用， `search.in` 而不是更複雜的等號比較運算式分離。

> [!NOTE]
> OData 標準的4.01 版最近引進了[ `in` 運算子](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)，其行為與 Azure 認知搜尋中的函式類似 `search.in` 。 但是，Azure 認知搜尋不支援這個運算子，所以您必須改用函式 `search.in` 。

## <a name="syntax"></a>語法

下列 EBNF ([擴充 Backus-Naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義函數的文法 `search.in` ：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

此 `search.in` 函數會測試指定的字串欄位或範圍變數是否等於其中一個指定的值清單。 變數與清單中每個值之間的相等是以區分大小寫的方式來決定，與運算子的方式相同 `eq` 。 因此，像 `search.in(myfield, 'a, b, c')` 這樣的運算式會等同於 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`，差別在於 `search.in` 所產生的效果會好得多。

函數有兩個多載 `search.in` ：

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

這些參數定義于下表中：

| 參數名稱 | 類型 | 說明 |
| --- | --- | --- |
| `variable` | `Edm.String` | 字串欄位參考 (或字串集合欄位的範圍變數，如果 `search.in` 用於 `any` 或運算式) 內，則為 `all` 。 |
| `valueList` | `Edm.String` | 字串，包含要與參數比對的值分隔清單 `variable` 。 如果 `delimiters` 未指定參數，則預設分隔符號為空格和逗號。 |
| `delimiters` | `Edm.String` | 剖析參數時，將每個字元視為分隔符號的字串 `valueList` 。 這個參數的預設值是，這 `' ,'` 表示任何具有空格及/或逗號的值都將被分隔。 如果您需要使用空格和逗點以外的分隔符號，因為您的值包含這些字元，您可以 `'|'` 在此參數中指定替代分隔符號（例如）。 |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>效能 `search.in`

如果您使用 `search.in`，當第二個參數所含的清單有數百或數千個值時，預期的回應時間應該不到一秒。 您可以傳遞給的專案數目沒有明確的限制 `search.in` ，但您仍受限於最大要求大小。 不過，當值的數目增加時，延遲也會隨之增長。

## <a name="examples"></a>範例

尋找名稱等於「海洋視圖 motel」或「預算旅館」的所有旅館。 片語包含空格，這是預設的分隔符號。 您可以使用單引號來指定替代分隔符號作為第三個字串參數：  

```odata-filter-expr
    search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

尋找名稱等於「海洋視圖 motel」或「預算旅館」的所有旅館，並以 ' | ' 分隔) ：

```odata-filter-expr
    search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

找出具有「wifi」或「浴盆」標記的房間的所有旅館：

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))
```

尋找集合內的片語相符，例如標籤中的「加熱的紙巾」或「hairdryer 包含」。

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

尋找沒有標記 ' motel ' 或 ' cabin' 的所有旅館：

```odata-filter-expr
    Tags/all(tag: not search.in(tag, 'motel, cabin'))
```

## <a name="next-steps"></a>接下來的步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)