---
title: O資料收集運算子參考
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜索查詢中創建篩選器運算式時，當篩選器位於集合或複雜集合欄位上時，請使用 lambda 運算式中的"任何"和"全部"運算子。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113245"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Azure 認知搜索中的 O 資料`any`收集運算子 - 和`all`

編寫[OData 篩選器運算式](query-odata-filter-orderby-syntax.md)以與 Azure 認知搜索一起使用時，在集合欄位上進行篩選通常很有用。 您可以使用 和`any``all`運算子來實現此目的。

## <a name="syntax"></a>語法

以下 EBNF（[擴展 Backus-Naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義了使用`any`或`all`的 OData 運算式的語法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

篩選集合的運算式有三種形式。

- 前兩個反覆運算集合欄位，將以 lambda 運算式形式給出的謂詞應用於集合的每個元素。
  - 如果謂詞`all`對於`true`集合的每個元素都為 true，則使用 返回的運算式。
  - 如果謂詞`any`對於`true`集合的至少一個元素為 true，則使用 返回的運算式。
- 第三種形式的集合篩選器不使用`any`lambda 運算式來測試集合欄位是否為空。 如果集合具有任何元素，它將返回`true`。 如果集合為空，它將返回`false`。

集合篩選器中的**lambda 運算式**類似于程式設計語言中的迴圈正文。 它定義一個變數，稱為**範圍變數**，在反覆運算期間保存集合的當前元素。 它還定義了另一個布林運算式，該運算式是應用於集合每個元素的範圍變數的篩選準則。

## <a name="examples"></a>範例

欄位`tags`完全包含字串"wifi"的匹配文檔：

    tags/any(t: t eq 'wifi')

匹配`ratings`欄位的每個元素介於 3 和 5 之間的文檔，包括：

    ratings/all(r: r ge 3 and r le 5)

匹配`locations`欄位中任何地理座標位於給定多邊形內的文檔：

    locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))

匹配欄位為空`rooms`的文檔：

    not rooms/any()

匹配文檔，所有房間的`rooms/amenities`欄位包含"tv"，`rooms/baseRate`小於 100：

    rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)

## <a name="limitations"></a>限制

並非所有篩選器運算式的功能都可用於 lambda 運算式的正文中。 限制因要篩選的集合欄位的資料類型而異。 下表總結了限制。

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

有關這些限制的更多詳細資訊以及示例，請參閱[Azure 認知搜索 中的故障排除集合篩選器](search-query-troubleshoot-collection-filters.md)。 有關存在這些限制的原因的更深入資訊，請參閱在 Azure[認知搜索 中瞭解集合篩選器](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜索中的篩選器](search-filters.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
