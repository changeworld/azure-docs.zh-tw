---
title: Azure 入口網站中的搜尋總管查詢工具
titleSuffix: Azure Cognitive Search
description: 在此 Azure 入口網站的快速入門中，使用「搜尋總管」來了解查詢語法、測試查詢運算式或檢查搜尋文件。 搜尋總管會查詢 Azure 認知搜尋中的索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/25/2020
ms.openlocfilehash: adcd07ad370ad9f1301caa41c494a33958743dd8
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398403"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>快速入門：使用搜尋總管在入口網站中執行查詢

**搜尋總管**是內建的查詢工具，可用來對 Azure 認知搜尋中的搜尋索引執行查詢。 這項工具可讓您輕鬆學習查詢語法、測試查詢或篩選運算式，或檢查索引中是否有新內容，以確認資料重新整理。

本快速入門會使用現有的索引來示範搜尋總管。 要求會使用[搜尋 REST API](/rest/api/searchservice/) 來編寫，並以 JSON 文件的形式傳回回應。

## <a name="prerequisites"></a>必要條件

開始之前，您必須具備下列條件：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/)。

+ Azure 認知搜尋服務。 在目前的訂閱下，[建立服務](search-create-service-portal.md) 或 [尋找現有的服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。 

+ 本快速入門會使用 *realestate-us-sample-index*。 使用 [匯入資料][](search-import-data-portal.md) 精靈來建立索引。 在第一個步驟中，當系統要求您輸入資料來源時，請選擇 [樣本] 然後選取 [realestate-us-sample] 資料來源。 接受所有的精靈預設值以建立索引。

## <a name="start-search-explorer"></a>開啟 [搜尋總管]

1. 在 [Azure 入口網站](https://portal.azure.com)中，從儀表板開啟搜尋服務分頁，或是[尋找服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. 從命令列開啟搜尋總管：

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="入口網站中的搜尋總管命令" border="false":::

    或者，在開啟的索引上使用內嵌的 [搜尋總管] 索引標籤：

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="入口網站中的搜尋總管命令" border="false":::

## <a name="unspecified-query"></a>未指定的查詢

若要率先一睹內容，請執行空的搜尋，方法是按一下 [搜尋] 且不提供任何字詞。 空的搜尋作為第一個查詢很有用，因為它會傳回整個文件，以便您可以檢閱文件組合。 在空的搜尋上，沒有任何搜尋排名，而且會以任意順序傳回文件 (`"@search.score": 1` 為所有文件)。 根據預設，會傳回搜尋要求中的 50 份文件。

空的搜尋的對等語法是 `*` 或 `search=*`。
   
   ```http
   search=*
   ```

   **結果**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="入口網站中的搜尋總管命令" border="false":::

## <a name="free-text-search"></a>任意文字搜尋

自由格式查詢 (包含或不包含運算子) 可用於模擬從自訂應用程式傳送至 Azure 認知搜尋服務的使用者定義查詢。 只有其屬性在索引定義中為**可搜尋**的欄位，才能進行掃描以尋找相符項目。 

請注意，當您提供搜尋準則 (例如查詢字詞或運算式) 時，搜尋排名可派上用場。 以下透過範例說明任意文字搜尋。

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **結果**

   您可以使用 Ctrl-F 來搜尋結果中感興趣的特定字詞。

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="入口網站中的搜尋總管命令" border="false":::

## <a name="count-of-matching-documents"></a>相符文件的計數 

新增 **$count=true** 以取得索引中找到的相符項目數。 在空的搜尋中，計數會是索引中的文件總數。 在限定搜尋中，它是符合查詢輸入的文件數目。

   ```http
   $count=true
   ```

   **結果**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="入口網站中的搜尋總管命令" border="false":::

## <a name="limit-fields-in-search-results"></a>限制搜尋結果中的欄位

新增 [ **$select**](search-query-odata-select.md) 以將結果限制為明確具名欄位，以便 **[搜尋總管]** 中的輸出更容易讀取。 若要保留搜尋字串和 **$count=true**，請在引數前加上 **&** 首碼。 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **結果**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="入口網站中的搜尋總管命令" border="false":::

## <a name="return-next-batch-of-results"></a>傳回下一個批次的結果

Azure 認知搜尋服務會根據搜尋排名傳回前 50 個相符項目。 若要取得下一組相符文件，請附加 **$top=100,&$skip=50** 以將結果集增加到 100 份文件 (預設值為 50，最大值為 1000)，略過前 50 個文件。 回想一下，您必須提供搜尋準則，例如查詢字詞或運算式，以取得高排序結果。 請注意，搜尋分數會降低您在搜尋結果中的深度。

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **結果**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="入口網站中的搜尋總管命令" border="false":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>篩選條件運算式 (大於、小於、等於)

如果要指定精確的準則而不是任意文字搜尋，請使用 [ **$filter**](search-query-odata-filter.md) 參數。 欄位在索引中的屬性必須為**可篩選**。 此範例會搜尋大於 3 的臥室數目：

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **結果**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="入口網站中的搜尋總管命令" border="false":::

## <a name="order-by-expressions"></a>Order-by 運算式

新增 [ **$orderby**](search-query-odata-orderby.md) 以依照搜尋分數以外的其他欄位對結果進行排序。 欄位在索引中的屬性必須為**可排序**。 可用來測試此輸出的範例運算式是：

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **結果**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="入口網站中的搜尋總管命令" border="false":::

**$filter** 和 **$orderby** 運算式都是 OData 建構。 如需詳細資訊，請參閱[篩選 OData 語法](/rest/api/searchservice/odata-expression-syntax-for-azure-search)。

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>重要心得

在本快速入門中，您已透過**搜尋總管**來使用 REST API 查詢索引。

+ 結果會以詳細的 JSON 文件傳回，以便您可以完整地查看文檔構造和內容。 您可以使用範例中顯示的查詢運算式來限制要傳回哪些欄位。

+ 文件是由索引中標記為**可擷取**的所有欄位組成。 若要在入口網站中檢視索引屬性，請在 [搜尋概觀] 頁面上按一下 [索引] 清單中的 realestate-us-sample。

+ 自由格式查詢類似於您在商業 Web 瀏覽器中輸入的內容，適合用於測試使用者體驗。 比方說，假設內建的房地產 範例索引，您可以輸入 "Seattle apartments lake washington"，然後您可以使用 Ctrl-F 在搜尋結果中尋找字詞。 

+ 查詢和篩選條件運算式會以 Azure 認知搜尋所支援的語法表達。 預設值是[簡單語法](/rest/api/searchservice/simple-query-syntax-in-azure-search)，但您可以選擇使用[完整 Lucene 功能](/rest/api/searchservice/lucene-query-syntax-in-azure-search)進行更強大的查詢。 [篩選條件運算式](/rest/api/searchservice/odata-expression-syntax-for-azure-search)是 OData 語法。

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

若要深入了解查詢結構和語法，請使用 Postman 或對等工具來建立利用 API 更多部分的查詢運算式。 [搜尋 REST API](/rest/api/searchservice/) 特別有助於學習和探索。

> [!div class="nextstepaction"]
> [在 Postman 中建立基本查詢](search-query-simple-examples.md)