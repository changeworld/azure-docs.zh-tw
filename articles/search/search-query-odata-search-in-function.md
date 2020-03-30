---
title: ODatasearch.in函數引用
titleSuffix: Azure Cognitive Search
description: 用於在 Azure 認知搜索查詢中使用search.in函數的語法和參考文檔。
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113115"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Azure`search.in`認知搜索中的 OData 函數

[OData 篩選器運算式](query-odata-filter-orderby-syntax.md)中的一個常見方案是檢查每個文檔中的單個欄位是否等於許多可能值之一。 例如，這是某些應用程式實現[安全修整](search-security-trimming-for-azure-search.md)的方式 -- 通過對照表示發出查詢的使用者的主體指示清單檢查包含一個或多個主體的代號的欄位。 編寫這樣的查詢的一種方法是使用[`eq`](search-query-odata-comparison-operators.md)和[`or`](search-query-odata-logical-operators.md)運算子：

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

但是，使用`search.in`函數編寫此功能的方法較短：

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> 除了更短且更易於閱讀外，使用`search.in`還提供了[性能優勢](#bkmk_performance)，並在篩選器中包含數百甚至數千個值時避免[某些大小的篩選器限制](search-query-odata-filter.md#bkmk_limits)。 因此，我們強烈建議使用`search.in`而不是更複雜的相等運算式的分離。

> [!NOTE]
> OData 標準的版本 4.01 最近引入了[`in`運算子](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)，該運算子具有與 Azure`search.in`認知搜索中的函數類似的行為。 但是，Azure 認知搜索不支援此運算子，因此必須改用函數`search.in`。

## <a name="syntax"></a>語法

以下 EBNF （[擴展 Backus-Naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)） 定義了`search.in`函數的語法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

函數`search.in`測試給定字串欄位或範圍變數是否等於給定的值清單之一。 變數和清單中每個值之間的相等性以區分大小寫的方式確定，與`eq`運算子相同。 因此，像 `search.in(myfield, 'a, b, c')` 這樣的運算式會等同於 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`，差別在於 `search.in` 所產生的效果會好得多。

`search.in`函數有兩個重載：

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

參數在下表中定義：

| 參數名稱 | 類型 | 描述 |
| --- | --- | --- |
| `variable` | `Edm.String` | 字串欄位引用（或字串集合欄位上的範圍變數，在`search.in`或`any``all`運算式中使用的情況下）。 |
| `valueList` | `Edm.String` | 包含要與參數匹配的值的分隔清單的`variable`字串。 如果未指定`delimiters`參數，則預設分隔符號為空格和逗號。 |
| `delimiters` | `Edm.String` | 在分析參數時，每個字元都被視為分隔符號的`valueList`字串。 此參數的預設值表示`' ,'`，它們之間具有空格和/或逗號的任何值都將分開。 如果需要使用空格和逗號以外的分隔符號，因為值包含這些字元，則可以指定備用分隔符號，如`'|'`在此參數中。 |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>性能`search.in`

如果您使用 `search.in`，當第二個參數所含的清單有數百或數千個值時，預期的回應時間應該不到一秒。 雖然您仍然受最大請求大小的限制，但可以傳遞給`search.in`的項數沒有明確限制。 不過，當值的數目增加時，延遲也會隨之增長。

## <a name="examples"></a>範例

查找名稱等於"海景汽車旅館"或"經濟型酒店"的所有酒店。 短語包含空格，這是預設分隔符號。 您可以在單個引號中指定替代分隔符號作為第三個字串參數：  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

查找名稱等於"海景汽車旅館"或"經濟型酒店"（由"*"分隔）的所有酒店：

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

查找所有房間都帶有"wifi"或"tub"標籤的酒店：

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

查找集合中的短語的匹配項，例如標籤中的"加熱毛巾架"或"包括吹風機"。

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

查找所有沒有標籤"汽車旅館"或"小屋"的酒店：

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜索中的篩選器](search-filters.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
