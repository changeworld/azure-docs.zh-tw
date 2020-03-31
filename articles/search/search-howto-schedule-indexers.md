---
title: 計畫索引子執行
titleSuffix: Azure Cognitive Search
description: 計畫 Azure 認知搜索索引子定期或在特定時間索引內容。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112956"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中安排索引子

索引子通常在創建索引子後立即運行一次。 您可以使用門戶、REST API 或 .NET SDK 按需再次運行它。 還可以將索引子配置為按計劃定期運行。

索引子計畫有用的一些情況：

* 來源資料將隨時間而變化，並且希望 Azure 認知搜索索引子自動處理更改的資料。
* 索引將從多個資料來源填充，並且您希望確保索引子在不同的時間運行以減少衝突。
* 來源資料非常大，您希望隨著時間的推移分散索引子處理。 有關索引大量資料的詳細資訊，請參閱[如何在 Azure 認知搜索中索引大型資料集](search-howto-large-index.md)。

計畫程式是 Azure 認知搜索的內置功能。 不能使用外部計畫程式來控制搜索索引子。

## <a name="define-schedule-properties"></a>定義計畫屬性

索引子計畫具有兩個屬性：
* **間隔**，它定義計畫索引子執行之間的時間量。 允許的最小間隔為 5 分鐘，最大間隔為 24 小時。
* **開始時間 （UTC），** 指示應首次運行索引子。

您可以在首次創建索引子時指定計劃，或者以後更新索引子的屬性。 可以使用[門戶](#portal)[、REST API](#restApi)或[.NET SDK](#dotNetSdk)設置索引子計畫。

索引子一次僅能執行一個。 如果索引子在計畫下一次執行時已在運行，則該執行將推遲到下一個計畫時間。

讓我們參考一個範例，讓您更具體了解詳情。 假設我們配置一個索引子計畫，其**間隔**為每小時，**開始時間為**2019 年 6 月 1 日上午 8：00 UTC。 以下是索引子執行時間超過一小時時發生的情況：

* 第一個索引子執行開始于 2019 年 6 月 1 日上午 8：00 UTC。 假設此執行需要 20 分鐘 (或任何小於 1 小時的時間)。
* 第二次執行開始于 2019 年 6 月 1 日上午 9：00 UTC。 假設此執行需要 70 分鐘 - 超過一小時 - 並且直到 UTC 上午 10：10 才能完成。
* 第三次執行定于 UTC 上午 10：00 開始，但此時以前的執行仍在運行。 然後跳過此計畫執行。 索引子的下一次執行要到 UTC 上午 11：00 才能啟動。

> [!NOTE]
> 如果索引子設置為特定計劃，但每次運行時都會重複失敗，則索引子將開始以不太頻繁的間隔（最多每 24 小時運行一次），直到它成功取得進展再次。  如果您認為已修復導致索引子卡在某個點的任何問題，則可以執行索引子的按需運行，如果成功取得進展，索引子將再次返回到其設置的計畫間隔。

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>門戶中的計畫

門戶中的"導入資料"嚮導允許您在創建時定義索引子的計畫。 預設計畫設置為 **"每小時**"，這意味著索引子在創建後運行一次，之後每隔一小時再次運行。

如果不希望索引子再次自動運行，則可以將"計畫"設置更改為 **"一次**"，也可以將 **"每日"** 設置為每天運行一次。 如果要指定不同的間隔或特定的將來開始時間，請將其設置為 **"自訂**"。

將計畫設置為 **"自訂 "** 欄位時，將顯示欄位，以便指定**間隔**和**開始時間 （UTC）。** 允許的最短時間間隔為 5 分鐘，最長時間為 1440 分鐘（24 小時）。

   ![在導入資料嚮導中設置索引子計畫](media/search-howto-schedule-indexers/schedule-import-data.png "在導入資料嚮導中設置索引子計畫")

創建索引子後，可以使用索引子的"編輯"面板更改計畫設置。 "計畫"欄位與"導入資料"嚮導中相同。

   ![在索引子編輯面板中設置計畫](media/search-howto-schedule-indexers/schedule-edit.png "在索引子編輯面板中設置計畫")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>使用 REST API 計畫

您可以使用 REST API 為索引子定義計畫。 為此，請在創建或更新索引子時包括**計畫**屬性。 下面的示例顯示了用於更新現有索引子的 PUT 請求：

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**間隔** 參數是必需的。 間隔指兩個連續索引子開始執行的時間。 允許的最小間隔為 5 分鐘；最長間隔為一天。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 間隔的模式為： `P(nD)(T(nH)(nM))`。 範例：`PT15M` 代表每隔 15 分鐘，`PT2H` 代表每隔 2 個小時。

可選**的開始時間**指示計畫執行何時開始。 如果省略選用時間，則會使用您目前的 UTC 時間。 這一次可以過去，在這種情況下，第一次執行是計畫，就像索引子自原始**啟動時間**以來一直連續運行。

您還可以使用運行索引子調用隨時按需運行索引子。 有關運行索引子和設置索引子計畫的詳細資訊，請參閱 REST API 參考中的[運行索引子](https://docs.microsoft.com/rest/api/searchservice/run-indexer)、[獲取索引子](https://docs.microsoft.com/rest/api/searchservice/get-indexer)和[更新索引子](https://docs.microsoft.com/rest/api/searchservice/update-indexer)。

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>使用 .NET SDK 進行計畫

您可以使用 Azure 認知搜索 .NET SDK 為索引子定義計畫。 為此，請在創建或更新索引子時包括**計畫**屬性。

以下 C# 示例使用預定義的資料來源和索引創建索引子，並將其計畫設置為從 30 分鐘後開始每天運行一次：

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
如果省略**計畫**參數，索引子在創建索引子後將立即運行一次。

**startTime**參數可以設置為過去的時間。 在這種情況下，第一次執行被安排，就像索引子自給定**的開始時間**以來一直連續運行一樣。

計畫使用[索引計畫](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet)類定義。 **索引計畫**建構函式需要使用**TimeSpan**物件指定的**間隔**參數。 允許的最小間隔值為 5 分鐘，最大值為 24 小時。 第二個**startTime**參數（指定為**DateTimeOffset 物件**）是可選的。

.NET SDK 允許您使用[SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)類及其[索引子](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers)屬性控制索引子操作，該屬性從**IIndexers 操作**介面實現方法。 

您可以隨時使用[運行](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run)、[運行 Async](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)或[RunWithMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)方法之一按需運行索引子。

有關創建、更新和運行索引子的詳細資訊，請參閱[IIindexers 操作](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)。
