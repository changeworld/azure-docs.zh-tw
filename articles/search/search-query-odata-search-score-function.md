---
title: OData 搜尋。計分函數參考
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋查詢中使用 [搜尋] 計分函數的語法和參考檔集。
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
ms.openlocfilehash: 0a84d0310573a1210e21157102a445fff9244782
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923971"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>`search.score`Azure 認知搜尋中的 OData 函數

當您將查詢傳送至不含[ **$orderby**參數](search-query-odata-orderby.md)的 Azure 認知搜尋時，傳回的結果將依相關性分數的遞減順序排序。 即使當您使用 **$orderby**時，根據預設會將相關性分數用於中斷系結。 不過，有時候使用相關性分數做為初始排序準則，以及將其他準則做為系結器，會很有用。 `search.score`函數可讓您這樣做。

## <a name="syntax"></a>語法

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 `search.score` 函式不接受任何參數。 您可以搭配 `asc` 或 `desc` 排序次序規範使用它，就像 **$orderby** 參數中的任何其他子句一樣。 它可以出現在排序準則清單中的任何位置。

## <a name="example"></a>範例

以遞減的順序排序旅館 `search.score` ，然後以相對於 `rating` 指定座標的遞增順序排序，如此一來，在兩個具有相同評等的旅館之間，就會先列出最接近的旅館：

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋 &#40;Azure 認知搜尋 EST API&#41;的檔 ](/rest/api/searchservice/Search-Documents)