---
title: 更多類似此（預覽）查詢功能
titleSuffix: Azure Cognitive Search
description: 描述更類似于這個（預覽）功能，該功能在 Azure 認知搜索 REST API 的預覽版本中可用。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873806"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>更類似于 Azure 認知搜索中的此（預覽）

> [!IMPORTANT] 
> 此功能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 當前沒有門戶或 .NET SDK 支援。

`moreLikeThis=[key]`是[搜索文檔 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)中的查詢參數，用於查找與文檔鍵指定的文檔類似的文檔。 使用 `moreLikeThis` 提出搜尋要求時，系統會利用最能描述該文件的指定文件中擷取的搜尋詞彙，產生查詢。 然後將產生的查詢用於提出搜尋要求。 預設情況下，將考慮所有可搜索欄位的內容，減去使用`searchFields`參數指定的任何受限欄位。 `moreLikeThis` 參數無法搭配搜尋參數 `search=[string]` 使用。

預設情況下，將考慮所有頂級可搜索欄位的內容。 如果要改為指定特定欄位，可以使用 參數`searchFields`。 

不能對`MoreLikeThis`[複雜類型的](search-howto-complex-data-types.md)可搜索子欄位使用。

## <a name="examples"></a>範例

以下所有示例都使用["快速入門"中的酒店示例：在 Azure 門戶中創建搜索索引](search-get-started-portal.md)。

### <a name="simple-query"></a>簡單查詢

以下查詢查找描述欄位與`moreLikeThis`參數指定的來源文件欄位最相似的文檔：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

在此示例中，請求搜索類似于具有`HotelId`29 的酒店的酒店。
您可以使用 HTTP POST 調用`MoreLikeThis`，而不是使用 HTTP GET：

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>套用篩選條件

`MoreLikeThis`可以與其他常見查詢參數（如`$filter`）結合使用。 例如，查詢只能限制為類別為"預算"且評級高於 3.5 的酒店：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>選擇欄位並限制結果

選擇`$top`器可用於限制`MoreLikeThis`應在查詢中返回多少結果。 此外，可以使用 選擇欄位`$select`。 此處選擇前三家酒店及其 ID、名稱和評級： 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>後續步驟

您可以使用任何 Web 測試控管來試驗此功能。  我們建議在本練習中使用郵遞員。

> [!div class="nextstepaction"]
> [使用郵遞員流覽 Azure 認知搜索 REST API](search-get-started-postman.md)
