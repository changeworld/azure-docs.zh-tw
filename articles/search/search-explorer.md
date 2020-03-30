---
title: Azure 門戶中的搜索資源管理器查詢工具
titleSuffix: Azure Cognitive Search
description: 在此 Azure 門戶快速入門中，使用搜索資源管理器學習查詢語法、測試查詢運算式或檢查搜索文檔。 搜索資源管理器查詢 Azure 認知搜索中的索引。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369686"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>快速入門：使用搜索資源管理器在門戶中執行查詢

**搜索資源管理器**是一種內置查詢工具，用於在 Azure 認知搜索中針對搜索索引執行查詢。 此工具通過驗證存在較新的內容，可以輕鬆地學習查詢語法、測試查詢或篩選器運算式或確認索引刷新的結果。

此快速入門使用**房地產-我們樣本索引**來演示搜索資源管理器。 請求使用[搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)進行配製，回應返回為 JSON 文檔。

## <a name="prerequisites"></a>Prerequisites

+ [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。

+ **房地產-我們樣本指數**用於此快速啟動。 逐步執行[**導入資料**](search-import-data-portal.md)嚮導，從內置示例資料來源生成索引。

## <a name="start-search-explorer"></a>開啟 [搜尋總管]

1. 在[Azure 門戶](https://portal.azure.com)中，從儀表板打開搜索服務頁面或[查找服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. 從命令列打開搜索資源管理器：

   ![門戶中的搜索資源管理器命令](./media/search-explorer/search-explorer-cmd2.png "門戶中的搜索資源管理器命令")

    或者使用打開索引上的嵌入**搜索資源管理器**選項卡：

   ![搜索資源管理器選項卡](./media/search-explorer/search-explorer-tab.png "搜索資源管理器選項卡")

## <a name="unspecified-query"></a>未指定的查詢

對於第一次查看內容，請按一下"**搜索**"，但未提供任何字詞，即可執行空搜索。 空的搜尋作為第一個查詢很有用，因為它會傳回整個文件，以便您可以檢閱文件組合。 在空的搜尋上，沒有任何搜尋排名，而且會以任意順序傳回文件 (`"@search.score": 1` 為所有文件)。 根據預設，會傳回搜尋要求中的 50 份文件。

空的搜尋的對等語法是 `*` 或 `search=*`。
   
   ```http
   search=*
   ```

   **結果**
   
   ![空查詢示例](./media/search-explorer/search-explorer-example-empty.png "不合格或空查詢示例")

## <a name="free-text-search"></a>免費文本搜索

自由格式查詢（帶或不帶運算子）可用於類比從自訂應用發送到 Azure 認知搜索的使用者定義的查詢。 僅掃描索引定義中歸結為**可搜索**的欄位。 

請注意，當您提供搜尋條件（如查詢詞或運算式）時，搜索排名就會發揮作用。 以下透過範例說明任意文字搜尋。

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **結果**

   您可以使用 Ctrl-F 來搜尋結果中感興趣的特定字詞。

   ![自由文字查詢示例](./media/search-explorer/search-explorer-example-freetext.png "自由文字查詢示例")

## <a name="count-of-matching-documents"></a>匹配文檔計數 

添加 **$count_true**以獲取索引中找到的匹配項數。 在空的搜尋中，計數會是索引中的文件總數。 在限定搜尋中，它是符合查詢輸入的文件數目。

   ```http
   $count=true
   ```

   **結果**

   ![文檔計數示例](./media/search-explorer/search-explorer-example-count.png "索引中的匹配文檔計數")

## <a name="limit-fields-in-search-results"></a>限制搜尋結果中的欄位

添加[**$select**](search-query-odata-select.md)以將結果限制為顯式命名的欄位，以便**搜索資源管理器**中的更多可讀輸出。 若要保留搜尋字串和 **$count=true**，請在引數前加上 **&** 首碼。 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **結果**

   ![限制欄位示例](./media/search-explorer/search-explorer-example-selectfield.png "限制搜尋結果中的欄位")

## <a name="return-next-batch-of-results"></a>返回下一批結果

Azure 認知搜索根據搜索排名返回前 50 個匹配項。 若要取得下一組相符文件，請附加 **$top=100,&$skip=50** 以將結果集增加到 100 份文件 (預設值為 50，最大值為 1000)，略過前 50 個文件。 回想一下，您必須提供搜尋準則，例如查詢字詞或運算式，以取得高排序結果。 請注意，搜尋分數會降低您在搜尋結果中的深度。

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **結果**

   ![批次搜尋結果](./media/search-explorer/search-explorer-example-topskip.png "返回下一批搜尋結果")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>篩選條件運算式 (大於、小於、等於)

如果要指定精確的條件而不是自由文本搜索，請使用[**$filter**](search-query-odata-filter.md)參數。 該欄位必須歸為索引中的**可篩選**欄位。 此範例會搜尋大於 3 的臥室：

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **結果**

   ![篩選器運算式](./media/search-explorer/search-explorer-example-filter.png "按條件篩選")

## <a name="order-by-expressions"></a>Order-by 運算式

添加[**$orderby**](search-query-odata-orderby.md)以除搜索分數之外的另一個欄位對結果進行排序。 該欄位必須歸為索引中的**可排序**欄位。 可用於測試此項的示例運算式是：

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **結果**

   ![按順序運算式](./media/search-explorer/search-explorer-example-ordery.png "變更排序次序")

**$filter** 和 **$orderby** 運算式都是 OData 建構。 如需詳細資訊，請參閱[篩選 OData 語法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)。

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>重要心得

在此快速入門中，您可以使用**搜索資源管理器**使用 REST API 查詢索引。

+ 結果會以詳細的 JSON 文件傳回，以便您可以完整地查看文檔構造和內容。 您可以使用範例中顯示的查詢運算式來限制要傳回哪些欄位。

+ 文檔由索引中標記為 **"可檢索"** 的所有欄位組成。 若要在入口網站中檢視索引屬性，請在 [搜尋概觀] 頁面上按一下 [索引]**** 清單中的 realestate-us-sample**。

+ 自由格式查詢類似於您在商業 Web 瀏覽器中輸入的內容，適合用於測試使用者體驗。 比方說，假設內建的房地產 範例索引，您可以輸入 "Seattle apartments lake washington"，然後您可以使用 Ctrl-F 在搜尋結果中尋找字詞。 

+ 查詢和篩選器運算式在 Azure 認知搜索支援的語法中表示。 預設值是[簡單語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)，但您可以選擇使用[完整 Lucene 功能](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)進行更強大的查詢。 [篩選條件運算式](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)是 OData 語法。

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源]**** 或 [資源群組]**** 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

要瞭解有關查詢結構和語法的更多資訊，請使用 Postman 或等效工具創建利用 API 更多部分的查詢運算式。 [搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)特別有助於學習和探索。

> [!div class="nextstepaction"]
> [在郵遞員中創建基本查詢](search-query-simple-examples.md)