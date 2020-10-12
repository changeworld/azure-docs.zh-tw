---
title: moreLikeThis (preview) 查詢功能
titleSuffix: Azure Cognitive Search
description: 描述 moreLikeThis (preview) 功能，Azure 認知搜尋 REST API 的預覽版本中提供此功能。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cd6b64f118460a115963ed0bf105641d80334348
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934985"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure 認知搜尋中的 moreLikeThis (preview) 

> [!IMPORTANT] 
> 此功能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 2020-06-30-Preview 版](search-api-preview.md)提供這項功能。 目前沒有入口網站或 .NET SDK 支援。

`moreLikeThis=[key]` 是 [搜尋檔 API](/rest/api/searchservice/search-documents) 中的查詢參數，可尋找類似檔索引鍵所指定檔的檔。 使用 `moreLikeThis` 提出搜尋要求時，系統會利用最能描述該文件的指定文件中擷取的搜尋詞彙，產生查詢。 然後將產生的查詢用於提出搜尋要求。 依預設，會考慮所有可搜尋欄位的內容，減去您使用參數指定的任何受限制欄位 `searchFields` 。 `moreLikeThis` 參數無法搭配搜尋參數 `search=[string]` 使用。

依預設，會考慮所有最上層可搜尋欄位的內容。 如果您想要改為指定特定欄位，可以使用 `searchFields` 參數。 

您無法在 `MoreLikeThis` [複雜類型](search-howto-complex-data-types.md)中的可搜尋子欄位上使用。

## <a name="examples"></a>範例

下列範例會使用快速入門中的旅館範例 [：在 Azure 入口網站中建立搜尋索引](search-get-started-portal.md)。

### <a name="simple-query"></a>簡單查詢

下列查詢會尋找其描述欄位最類似于參數所指定之來源文件欄位的檔 `moreLikeThis` ：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

在此範例中，要求會搜尋類似于29的旅館 `HotelId` 。
您也可以使用 HTTP POST 來叫用 HTTP GET，而不是使用 HTTP GET `MoreLikeThis` ：

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>套用篩選條件

`MoreLikeThis` 可以與其他常見的查詢參數結合，例如 `$filter` 。 例如，您可以將查詢限制為僅限類別為「預算」且分級高於3.5 的飯店：

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>選取欄位並限制結果

`$top`選取器可以用來限制查詢中應該傳回的結果數目 `MoreLikeThis` 。 此外，也可以使用選取欄位 `$select` 。 在這裡，會選取前三個飯店，以及其識別碼、名稱和評等： 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>接下來的步驟

您可以使用任何 web 測試控管來實驗這項功能。  建議您在此練習中使用 Postman。

> [!div class="nextstepaction"]
> [使用 Postman 探索 Azure 認知搜尋 REST Api](search-get-started-postman.md)