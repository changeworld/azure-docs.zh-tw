---
title: 排程索引子執行
titleSuffix: Azure Cognitive Search
description: 排程 Azure 認知搜尋索引子，以定期或在特定時間編制內容的索引。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 80c3f9aa02680097276f966ce6aea02acf1e40fb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358791"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>如何排程 Azure 認知搜尋中的索引子

索引子通常會在建立後立即執行一次。 您可以使用入口網站、REST API 或 .NET SDK，視需要重新執行它。 您也可以設定依排程定期執行的索引子。

索引子排程的某些情況很有用：

* 來源資料會隨著時間而變更，而您希望 Azure 認知搜尋索引子自動處理變更的資料。
* 索引將會從多個資料來源填入，而您想要確保索引子會在不同的時間執行以減少衝突。
* 來源資料很大，而您想要分散一段時間的索引子處理。 如需有關索引大量資料的詳細資訊，請參閱 [如何在 Azure 認知搜尋中為大型資料集編制索引](search-howto-large-index.md)。

排程器是 Azure 認知搜尋的內建功能。 您無法使用外部排程器來控制搜尋索引子。

## <a name="define-schedule-properties"></a>定義排程屬性

索引子排程有兩個屬性：
* **間隔** ，定義排程索引子執行之間的時間量。 允許的最小間隔為5分鐘，而最大間隔為24小時。
* **開始時間 (UTC)** ，指出第一次執行索引子的時間。

您可以在第一次建立索引子時指定排程，或稍後更新索引子的屬性來指定。 您可以使用 [入口網站](#portal)、 [REST API](#restApi)或 [.net SDK](#dotNetSdk)來設定索引子排程。

索引子一次僅能執行一個。 如果索引子在下一次執行時已在執行，則該執行會延後到下一個排程的時間。

讓我們參考一個範例，讓您更具體了解詳情。 假設我們設定的索引子排程的 **間隔** 為每小時，而 **開始時間** 為2019年6月1日上午8:00:00： UTC。 以下是當索引子執行花費的時間超過一小時時所發生的情況：

* 第一個索引子執行會在2019年6月1日上午8:00： UTC 開始。 假設此執行需要 20 分鐘 (或任何小於 1 小時的時間)。
* 第二次執行會在2019年6月1日（UTC）上午9:00 開始或左右。 假設此執行花費的時間是70分鐘-超過一小時，而且在上午10:10： UTC 之前都不會完成。
* 第三次執行已排程在上午 10:00 UTC 開始，但在這段時間，先前的執行仍在執行中。 接著會跳過此排定的執行。 下次執行索引子時，將不會啟動，直到上午 11:00 UTC 為止。

> [!NOTE]
> 如果索引子設定為特定的排程，但每次執行時重複出現在同一份檔中，則索引子會以較不頻繁的間隔開始執行 (最多每24小時) 最多一次，直到它成功地進行進度為止。  如果您認為您已修正導致索引子停滯在某個時間點的任何問題，您可以執行索引子的隨選執行，如果成功，則索引子會再次回到其設定排程間隔。

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>入口網站中的排程

入口網站中的 [匯入資料] 嚮導可讓您在建立時定義索引子的排程。 預設排程設定為 [ **每** 小時]，這表示索引子會在建立之後執行一次，然後在每小時之後再次執行。

如果您不想要自動執行索引子，或 **每天每天** 執行一次，則可以將排程設定變更為 **一次** 。 如果您想要指定不同的間隔或特定的未來開始時間，請將它設定為 [ **自訂** ]。

當您將排程設定為 [ **自訂** ] 時，欄位會顯示為可讓您指定 **間隔** 和 **開始時間 (UTC)** 。 允許的最短時間間隔為5分鐘，而最長為1440分鐘 (24 小時) 。

   ![在 [匯入資料] 嚮導中設定索引子排程](media/search-howto-schedule-indexers/schedule-import-data.png "在 [匯入資料] 嚮導中設定索引子排程")

建立索引子之後，您可以使用索引子的 [編輯] 面板來變更排程設定。 [排程] 欄位與 [匯入資料] 嚮導中的相同。

   ![在索引子編輯面板中設定排程](media/search-howto-schedule-indexers/schedule-edit.png "在索引子編輯面板中設定排程")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>使用 REST Api 排程

您可以使用 REST API 定義索引子的排程。 若要這樣做，請在建立或更新索引子時包含 **schedule** 屬性。 下列範例顯示更新現有索引子的 PUT 要求：

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

選擇性 **startTime** 會指出排程的執行應該開始的時間。 如果省略選用時間，則會使用您目前的 UTC 時間。 這次可以是過去的時間，在這種情況下，第一次執行的排程就如同在原始 **startTime** 之後，索引子一直都在執行。

您也可以使用執行索引子呼叫，隨時依需求執行索引子。 如需執行索引子和設定索引子排程的詳細資訊，請參閱 REST API 參考中的 [執行索引子](/rest/api/searchservice/run-indexer)、 [取得索引](/rest/api/searchservice/get-indexer)器和 [更新索引子](/rest/api/searchservice/update-indexer) 。

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>使用 .NET SDK 進行排程

您可以使用 Azure 認知搜尋 .NET SDK 來定義索引子的排程。 若要這樣做，請在建立或更新索引子時包含 **Schedule** 屬性。

下列 c # 範例會使用預先定義的資料來源和索引來建立 Azure SQL 資料庫索引子，並將其排程設定為每天開始執行一次：

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```


如果省略了 **Schedule** 屬性，索引子只會在建立後立即執行一次。

**StartTime** 參數可以設定為過去的時間。 在這種情況下，第一次執行的排程就像是在指定 **StartTime** 之後連續執行索引子一樣。

排程會使用 [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) 類別來定義。 **IndexingSchedule** 的函式需要使用 **TimeSpan** 物件指定的 **間隔** 參數。 允許的最小間隔值為5分鐘，而最大值為24小時。 第二個 **StartTime** 參數（指定為 **DateTimeOffset** 物件）是選擇性的。

.NET SDK 可讓您使用 [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)來控制索引子作業。 

您可以使用其中一個 [RunIndexer](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) 或 [RunIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync) 方法，隨時依需求執行索引子。

如需有關建立、更新和執行索引子的詳細資訊，請參閱 [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)。