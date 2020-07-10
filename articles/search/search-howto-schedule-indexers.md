---
title: 排程索引子執行
titleSuffix: Azure Cognitive Search
description: 排程 Azure 認知搜尋索引子，以定期或在特定時間為內容編制索引。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f85645d8c77d2317807bb02a19a308070acb6007
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143541"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中排程索引子

索引子通常會在建立之後立即執行一次。 您可以使用入口網站、REST API 或 .NET SDK，視需要再次執行它。 您也可以設定依排程定期執行的索引子。

索引子排程非常有用的一些情況：

* 來源資料會隨著時間變更，而您想要讓 Azure 認知搜尋索引子自動處理已變更的資料。
* 索引會從多個資料來源填入，而您想要確保索引子在不同的時間執行，以減少衝突。
* 來源資料很大，而且您想要在一段時間內散佈索引子處理。 如需索引大量資料的詳細資訊，請參閱[如何在 Azure 認知搜尋中為大型資料集編制索引](search-howto-large-index.md)。

排程器是 Azure 認知搜尋的內建功能。 您無法使用外部排程器來控制搜尋索引子。

## <a name="define-schedule-properties"></a>定義排程屬性

索引子排程有兩個屬性：
* **間隔**，定義排程的索引子執行之間的時間量。 允許的最小間隔為5分鐘，最大值為24小時。
* **開始時間 (UTC) **，這表示第一次應執行索引子。

您可以在第一次建立索引子時指定排程，或稍後更新索引子的屬性。 您可以使用[入口網站](#portal)、 [REST API](#restApi)或[.net SDK](#dotNetSdk)來設定索引子排程。

索引子一次僅能執行一個。 如果索引子在下一次執行排程時已經在執行，則該執行會延後到下一個排程的時間為止。

讓我們參考一個範例，讓您更具體了解詳情。 假設我們設定一個索引子排程，其**間隔**為每小時，而**開始時間**為2019年6月1日，UTC 為上午8:00:00。 以下是當索引子執行時間超過一小時時可能會發生的情況：

* 第一個索引子執行從2019年6月1日上午 8:00 AM UTC 開始。 假設此執行需要 20 分鐘 (或任何小於 1 小時的時間)。
* 第二個執行是從2019年6月1日上午 9:00 AM UTC 開始。 假設此執行需要70分鐘-超過一小時，而且在 10:10 AM UTC 之前不會完成。
* 第三次執行排定在 UTC 上午10:00 開始，但當時仍在執行上一次。 然後會略過這個已排程的執行。 下次執行索引子時，將不會啟動到 11:00 AM UTC。

> [!NOTE]
> 如果索引子設定為特定的排程，但每次執行時重複地在同一份檔上失敗，則索引子將會以較不頻繁的間隔開始執行 (最多每 24) 小時一次，直到成功地再次執行進度為止。  如果您相信您已修正導致索引子停滯在某個時間點的任何問題，您可以依需求執行索引子，如果該作業成功，則索引子會再次回到其設定的排程間隔。

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>在入口網站中排程

入口網站中的 [匯入資料] 會讓您在建立時定義索引子的排程。 預設排程設定為 [**每小時**]，表示索引子會在建立之後執行一次，並于之後每小時重新執行。

如果您不想要讓索引子自動再次執行，**或每天執行**一次，您可以將排程設定變更為 [**一次**]。 如果您想要指定不同的間隔或特定的未來開始時間，請將它設定為 [**自訂**]。

當您將排程設定為 [**自訂**] 時，會顯示欄位，讓您指定**間隔**和**開始時間 (UTC) **。 允許的最短時間間隔為5分鐘，而最長為1440分鐘 (24 小時) 。

   ![在匯入資料嚮導中設定索引子排程](media/search-howto-schedule-indexers/schedule-import-data.png "在匯入資料嚮導中設定索引子排程")

建立索引子之後，您可以使用索引子的 [編輯面板] 來變更排程設定。 [排程] 欄位與 [匯入資料] 嚮導中的相同。

   ![在索引子編輯面板中設定排程](media/search-howto-schedule-indexers/schedule-edit.png "在索引子編輯面板中設定排程")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>使用 REST Api 進行排程

您可以使用 REST API 來定義索引子的排程。 若要這麼做，請在建立或更新索引子時加入**排程**屬性。 下列範例顯示更新現有索引子的 PUT 要求：

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

**間隔** 參數是必需的。 間隔指兩個連續索引子開始執行的時間。 允許的最小間隔為 5 分鐘；最長間隔為一天。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 間隔的模式為： `P(nD)(T(nH)(nM))`。 範例：`PT15M` 代表每隔 15 分鐘，`PT2H` 代表每隔 2 個小時。

選擇性的**startTime**表示何時應開始排程的執行。 如果省略選用時間，則會使用您目前的 UTC 時間。 這一次可以是過去的時間，在這種情況下，首次執行的排程就如同在原始**startTime**之後，索引子一直是連續執行一樣。

您也可以使用執行索引子呼叫，隨時依需求執行索引子。 如需有關執行索引子和設定索引子排程的詳細資訊，請參閱在 REST API 參考中[執行索引子](https://docs.microsoft.com/rest/api/searchservice/run-indexer)、[取得索引子](https://docs.microsoft.com/rest/api/searchservice/get-indexer)和[更新索引子](https://docs.microsoft.com/rest/api/searchservice/update-indexer)。

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>使用 .NET SDK 進行排程

您可以使用 Azure 認知搜尋 .NET SDK 來定義索引子的排程。 若要這麼做，請在建立或更新索引子時加入**排程**屬性。

下列 c # 範例會使用預先定義的資料來源和索引來建立索引子，並將其排程設定為從現在起算的30分鐘每天執行一次：

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
如果省略**schedule**參數，索引子只會在建立之後立即執行一次。

**StartTime**參數可以設定為過去的時間。 在這種情況下，第一次執行的排程就如同在指定的**startTime**之後，索引子已經連續執行。

排程是使用[IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet)類別來定義。 **IndexingSchedule**的函式需要使用**TimeSpan**物件指定的**interval**參數。 允許的最小間隔值為5分鐘，而最大值為24小時。 第二個**startTime**參數（指定為**DateTimeOffset**物件）是選擇性的。

.NET SDK 可讓您使用[SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)類別和其 [[索引子](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers)] 屬性來控制索引子作業，其會從**IIndexersOperations**介面執行方法。 

您可以隨時使用其中一個[run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run)、 [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)或[RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)方法，依需求執行索引子。

如需建立、更新和執行索引子的詳細資訊，請參閱[IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)。
