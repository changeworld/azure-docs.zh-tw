---
title: OData 集合運算子參考
titleSuffix: Azure Cognitive Search
description: 在「Azure 認知搜尋」查詢中建立篩選條件運算式時，如果篩選是在集合或複雜集合欄位上，請使用 lambda 運算式中的「任何」和「所有」運算子。
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
ms.openlocfilehash: 54ddc8222816831b5b436297bbb1b40d03230f0c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74113245"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Azure 認知搜尋中的 OData 集合運算子- `any` 和`all`

撰寫[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)以搭配使用 Azure 認知搜尋時，篩選集合欄位通常會很有用。 您可以使用和運算子來達到這個目的 `any` `all` 。

## <a name="syntax"></a>Syntax

下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）會定義使用或之 OData 運算式的文法 `any` `all` 。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

有三種形式的運算式會篩選集合。

- 前兩個反覆運算會逐一查看集合欄位，將 lambda 運算式形式的述詞套用至集合的每個元素。
  - `all` `true` 如果集合中的每個元素的述詞為 true，則使用的運算式會傳回。
  - `any` `true` 如果至少有一個集合元素的述詞為 true，則使用的運算式會傳回。
- 第三種形式的集合篩選器 `any` 不使用 lambda 運算式來測試集合欄位是否為空白。 如果集合具有任何元素，則會傳回 `true` 。 如果集合是空的，則會傳回 `false` 。

集合篩選中的**lambda 運算式**類似于程式設計語言中的迴圈主體。 它會定義一個變數，稱為**範圍變數**，在反復專案期間保存集合的目前元素。 它也會定義另一個布林運算式，這是要套用至集合中每個元素之範圍變數的篩選準則。

## <a name="examples"></a>範例

比 `tags` 對欄位包含完全符合字串 "wifi" 的檔：

    tags/any(t: t eq 'wifi')

將欄位的每個元素都 `ratings` 落在3和5（含）之間的相符檔：

    ratings/all(r: r ge 3 and r le 5)

比對欄位中任何地理座標的檔在 `locations` 指定的多邊形內：

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

符合 `rooms` 欄位空白的檔：

    not rooms/any()

符合所有房間的檔， `rooms/amenities` 欄位包含 "tv" 且 `rooms/baseRate` 小於100：

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>限制

在 lambda 運算式的主體內，不會提供篩選條件運算式的每個功能。 限制會根據您要篩選之集合欄位的資料類型而有所不同。 下表摘要說明這些限制。

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

如需這些限制和範例的詳細資訊，請參閱針對[Azure 認知搜尋中的集合篩選進行疑難排解](search-query-troubleshoot-collection-filters.md)。 如需這些限制為何存在的深入資訊，請參閱[瞭解 Azure 認知搜尋中的集合篩選](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
