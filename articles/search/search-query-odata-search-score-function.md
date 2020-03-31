---
title: OData 搜索.分數函數引用
titleSuffix: Azure Cognitive Search
description: 用於在 Azure 認知搜索查詢中使用 search.score 函數的語法和參考文檔。
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113131"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Azure`search.score`認知搜索中的 OData 函數

當您在沒有[**$orderby**參數](search-query-odata-orderby.md)的情況下向 Azure 認知搜索發送查詢時，返回的結果將按相關性分數按降冪排序。 即使您確實使用 **$orderby，** 相關性分數也會預設用於斷開關系。 但是，有時將相關性分數用作初始排序條件，將其他一些條件用作綁定中斷條件非常有用。 該`search.score`函數允許您執行此操作。

## <a name="syntax"></a>語法

`search.score` 在 **$orderby** 中的語法為 `search.score()`。 `search.score` 函式不接受任何參數。 它可以與`asc`或`desc`排序次序指定器一起使用，就像 **$orderby**參數中的任何其他子句一樣。 它可以出現在排序條件清單中的任意位置。

## <a name="example"></a>範例

Sort hotels in descending order by `search.score` and `rating`, and then in ascending order by distance from the given coordinates so that between two hotels with identical ratings, the closest one is listed first:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
