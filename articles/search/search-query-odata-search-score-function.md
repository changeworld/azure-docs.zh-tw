---
title: OData 搜尋。計分函數參考
titleSuffix: Azure Cognitive Search
description: 使用 Azure 認知搜尋查詢中的搜尋. 計分函數的語法和參考檔。
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
ms.openlocfilehash: c31304228d9629b0df7f7511ecca2616b4891ee7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206950"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>`search.score`Azure 認知搜尋中的 OData 函式

當您將查詢傳送至不含[ **$orderby**參數](search-query-odata-orderby.md)的 Azure 認知搜尋時，傳回的結果會依相關性分數以遞減順序排序。 即使您使用 **$orderby**，預設也會使用相關性分數來中斷系結。 不過，使用相關性分數做為初始排序準則，以及一些其他準則做為系結工具有時會很有用。 函式可 `search.score` 讓您執行此動作。

## <a name="syntax"></a>語法

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 `search.score` 函式不接受任何參數。 它可以與 `asc` 或 `desc` 排序次序規範搭配使用，就像 **$orderby**參數中的任何其他子句一樣。 它可以出現在排序條件清單中的任何位置。

## <a name="example"></a>範例

以遞減的順序依和排序飯店 `search.score` `rating` ，然後依照指定座標的遞增順序，讓兩個飯店具有相同的評等，最先列出最接近的旅館：

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋服務預估 API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
