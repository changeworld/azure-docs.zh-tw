---
title: OData 集合運算子參考
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋查詢中建立篩選運算式時，當篩選準則位於集合或複雜集合欄位時，請在 lambda 運算式中使用 "any" 和 "all" 運算子。
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
ms.openlocfilehash: 4c1243d5d9122539466e94b6bbfdd5ced588e69a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934900"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Azure 認知搜尋中的 OData 集合運算子 `any` 和 `all`

當撰寫要搭配 Azure 認知搜尋使用的 [OData 篩選運算式](query-odata-filter-orderby-syntax.md) 時，篩選集合欄位通常會很有用。 您可以使用和運算子來達成這個目的 `any` `all` 。

## <a name="syntax"></a>語法

下列 EBNF ([擴充 Backus-Naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義使用或之 OData 運算式的文法 `any` `all` 。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

有三種形式的運算式可篩選集合。

- 前兩個會逐一查看集合欄位，將 lambda 運算式形式的述詞套用至集合的每個元素。
  - `all` `true` 如果集合的每個元素的述詞為 true，則使用的運算式會傳回。
  - `any` `true` 如果至少有一個集合元素的述詞為 true，則使用的運算式會傳回。
- 第三種形式的集合篩選準則 `any` 會在沒有 lambda 運算式的情況下使用，以測試集合欄位是否為空白。 如果集合具有任何元素，則會傳回 `true` 。 如果集合是空的，則會傳回 `false` 。

集合篩選中的 **lambda 運算式** 就像程式設計語言中的迴圈主體。 它會定義一個變數，稱為「 **範圍變數**」，其會在反復專案期間保存集合的目前專案。 它也會定義另一個布林運算式，也就是要套用至集合中每個元素之範圍變數的篩選準則。

## <a name="examples"></a>範例

比 `tags` 對欄位只包含字串 "wifi" 的檔：

```text
tags/any(t: t eq 'wifi')
```

符合欄位的每個專案 `ratings` 介於3到5（含）之間的檔：

```text
ratings/all(r: r ge 3 and r le 5)
```

將欄位中任何地理位置座標相符的檔放在 `locations` 指定的多邊形內：

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

符合 `rooms` 欄位空白的檔：

```text
not rooms/any()
```

比對所有房間的檔， `rooms/amenities` 欄位包含 "tv" 且 `rooms/baseRate` 小於100：

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>限制

Lambda 運算式的主體中不提供篩選運算式的每項功能。 這些限制會根據您要篩選之集合欄位的資料類型而有所不同。 下表摘要說明這項限制。

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

如需這些限制及範例的詳細資訊，請參閱 [Azure 認知搜尋中的集合篩選疑難排解](search-query-troubleshoot-collection-filters.md)。 如需這些限制為何存在的詳細資訊，請參閱 [瞭解 Azure 認知搜尋中的集合篩選](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)