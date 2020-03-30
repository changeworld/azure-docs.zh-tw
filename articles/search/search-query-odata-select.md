---
title: O資料選擇參考
titleSuffix: Azure Cognitive Search
description: 語法和語言引用，用於明確選取要在 Azure 認知搜索查詢查詢的搜尋結果中返回的欄位。
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113093"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Azure 認知搜索中的 OData $select語法

 可以使用[OData **$select**參數](query-odata-filter-orderby-syntax.md)從 Azure 認知搜索中選擇要包含在搜尋結果中的欄位。 本文詳細介紹了 **$select**的語法。 有關在顯示搜尋結果時如何使用 **$select**的更多常規資訊，請參閱[在 Azure 認知搜索中如何使用搜尋結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>語法

**$select**參數確定在查詢結果集中返回每個文檔的欄位。 以下 EBNF （[擴展 Backus-Naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)） 定義了 **$select**參數的語法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

還提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜索的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 有關完整的 EBNF，請參閱[Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)。

**$select**參數有兩種形式：

1. 單星星`*`（ ），指示應返回所有可檢索欄位，或
1. 欄位路徑的逗號分隔清單，標識應返回哪些欄位。

使用第二個表單時，只能指定清單中的可檢索欄位。

如果列出複雜欄位而不顯式指定其子欄位，則查詢結果集中將包含所有可檢索的子欄位。 例如，假設您的索引具有 一`Address`個欄位`Street`，`City`該`Country`欄位具有 全部可檢索的欄位。 如果在`Address`**$select**中指定，則查詢結果將包括所有三個子欄位。

## <a name="examples"></a>範例

在結果`HotelId`中`HotelName`包括`Rating`、 和 頂級欄位，以及`City`： `Address`

    $select=HotelId, HotelName, Rating, Address/City

示例結果可能如下所示：

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

`HotelName`在結果`Address`中包括頂級欄位，以及 集合中`Type``BaseRate``Rooms`每個物件的 的所有子欄位以及 和 子欄位：

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

示例結果可能如下所示：

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟  

- [如何在 Azure 認知搜索中處理搜尋結果](search-pagination-page-layout.md)
- [Azure 認知搜索的 OData 運算式語言概述](query-odata-filter-orderby-syntax.md)
- [用於 Azure 認知搜索的 OData 運算式語法引用](search-query-odata-syntax-reference.md)
- [搜索文檔&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
