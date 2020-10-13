---
title: OData 選取參考
titleSuffix: Azure Cognitive Search
description: 明確選取要在 Azure 認知搜尋查詢搜尋結果中傳回之欄位的語法和語言參考。
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
ms.openlocfilehash: 54b6ae227fc4b3b951717799660543c02874dda0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919653"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的 OData $select 語法

 您可以使用 [OData **$select** 參數](query-odata-filter-orderby-syntax.md) ，從 Azure 認知搜尋選擇要包含在搜尋結果中的欄位。 本文將詳細說明 **$select** 的語法。 如需有關如何在呈現搜尋結果時使用 **$select** 的一般資訊，請參閱 [如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>語法

**$Select**參數會決定要在查詢結果集中傳回每份檔的哪些欄位。 下列 EBNF ([擴充 Backus-Naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義 **$select** 參數的文法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

您也可以使用互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 如需完整 EBNF 的詳細 Azure 認知搜尋，請參閱 [OData 運算式語法參考](search-query-odata-syntax-reference.md) 。

**$Select**參數有兩種形式：

1. 單一星星 (`*`) ，表示應該傳回所有可取出的欄位，或
1. 以逗號分隔的欄位路徑清單，識別應該傳回的欄位。

使用第二個表單時，您只能在清單中指定可檢索的欄位。

如果您在不明確指定其子欄位的情況下列出複雜欄位，則所有可取出的子欄位都會包含在查詢結果集中。 例如，假設您的索引具有 `Address` 具有 `Street` 、 `City` 和 `Country` 子欄位的欄位，這些欄位全都可供取得。 如果您 `Address` 在 **$select**中指定，查詢結果會包含這三個子欄位。

## <a name="examples"></a>範例

將 `HotelId` 、 `HotelName` 和 `Rating` 最上層欄位包含在結果中，以及的 `City` 子欄位 `Address` ：

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

範例結果可能如下所示：

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

在結果中包含 `HotelName` 最上層欄位，以及在 `Address` `Type` `BaseRate` 集合中包含每個物件的和子欄位，以及中的所有子欄位 `Rooms` ：

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

範例結果可能如下所示：

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

## <a name="next-steps"></a>接下來的步驟  

- [如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/Search-Documents)