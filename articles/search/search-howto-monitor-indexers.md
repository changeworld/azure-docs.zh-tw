---
title: 監視索引子狀態和結果
titleSuffix: Azure Cognitive Search
description: 使用 REST API 或 .NET SDK，在 Azure 入口網站中監視 Azure 認知搜尋索引子的狀態、進度和結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 2e62296e95a7b412a24c9d0c151c2bc9175ab4b7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529738"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>如何監視 Azure 認知搜尋索引子的狀態和結果

Azure 認知搜尋提供有關每個索引子的目前和歷程記錄執行狀態和監視資訊。

當您想要執行下列動作時，索引子監視非常有用：

* 追蹤索引子在進行中執行時的進度。
* 檢查進行中或前一個索引子執行的結果。
* 找出最上層的索引子錯誤，以及有關要編制索引之個別檔的錯誤或警告。

## <a name="get-status-and-history"></a>取得狀態和歷程記錄

您可以透過各種方式來存取索引子監視資訊，包括：

* 在 [ [Azure 入口網站](#portal)
* 使用[REST API](#restapi)
* 使用[.NET SDK](#dotnetsdk)

可用的索引子監視資訊包括下列所有內容（雖然資料格式會根據所使用的存取方法而有所不同）：

* 索引子本身的狀態資訊
* 最新執行之索引子的相關資訊，包括其狀態、開始和結束時間，以及詳細的錯誤和警告。
* 歷程記錄索引子的清單會執行，以及其狀態、結果、錯誤和警告。

處理大量資料的索引子可能需要很長的時間來執行。 例如，處理數百萬個來源文件的索引子可以執行24小時，然後幾乎立即重新開機。 高容量索引子的狀態在入口網站中可能一律會顯示為 [**進行**中]。 即使索引子正在執行，詳細資料仍適用于進行中的進度和先前的執行。

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>使用入口網站進行監視

您可以在 [搜尋服務] [總覽] 頁面的 [**索引子**] 清單中，查看所有索引子的目前狀態。

   ![索引子清單](media/search-monitor-indexers/indexers-list.png "索引子清單")

執行索引子時，清單中的狀態會顯示為 [**進行中**]，而 [檔**成功**] 值會顯示到目前為止處理的檔數目。 可能需要幾分鐘的時間，入口網站才會更新索引子狀態值和檔計數。

最近一次執行成功的索引子顯示**成功**。 即使錯誤數目小於索引子的 [**失敗的最大專案**] 設定，索引子執行還是可以成功。

如果最近執行結束時出現錯誤，則狀態會顯示為 [**失敗**]。 [**重設**] 狀態表示索引子的變更追蹤狀態為 [已重設]。

按一下清單中的索引子，以查看有關索引子目前和最近執行的更多詳細資料。

   ![索引子摘要和執行歷程記錄](media/search-monitor-indexers/indexer-summary.png "索引子摘要和執行歷程記錄")

**索引子摘要**圖表會顯示其最近執行中處理的檔數目圖表。

[**執行詳細資料**] 清單會顯示最多50的最近執行結果。

按一下清單中的執行結果，以查看有關該執行的詳細資訊。 這包括其開始和結束時間，以及任何發生的錯誤和警告。

   ![索引子執行詳細資料](media/search-monitor-indexers/indexer-execution.png "索引子執行詳細資料")

如果在執行期間發生檔特定的問題，它們會列在 [錯誤] 和 [警告] 欄位中。

   ![有錯誤的索引子詳細資料](media/search-monitor-indexers/indexer-execution-error.png "有錯誤的索引子詳細資料")

警告在某些類型的索引子中是常見的，而且不一定表示有問題。 例如，使用認知服務的索引子可以在影像或 PDF 檔案不包含任何要處理的文字時報告警告。

如需調查索引子錯誤和警告的詳細資訊，請參閱針對[Azure 認知搜尋中的常見索引子問題進行疑難排解](search-indexer-troubleshooting.md)。

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>使用 REST Api 進行監視

您可以使用[取得索引子狀態命令](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)來抓取索引子的狀態和執行歷程記錄：

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

回應包含整體索引子的狀態、最後 (或進行中) 的索引子叫用，以及最新的索引子叫用歷程記錄。

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

執行歷程記錄包含最多50個最近執行，並以反向時間順序排序（最新的第一個）。

請注意，有兩個不同的狀態值。 最上層狀態適用于索引子本身。 索引子狀態為 [執行中 **] 表示索引**器已正確設定且可供執行，但不是目前正在執行。

每次執行索引子時，也會有自己的狀態，指出該特定執行是否正在進行**中（執行**中），或已完成且具有**成功**、 **transientFailure**或**persistentFailure**狀態。 

重設索引子以重新整理其變更追蹤狀態時，會加入具有**重設**狀態的個別執行歷程記錄專案。

如需有關狀態碼和索引子監視資料的詳細資訊，請參閱[GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>使用 .NET SDK 進行監視

您可以使用 Azure 認知搜尋 .NET SDK 來定義索引子的排程。 若要這麼做，請在建立或更新索引子時加入**排程**屬性。

下列 c # 範例會將索引子狀態的相關資訊，以及其最近（或進行中）執行的結果寫入主控台。

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

主控台中的輸出看起來會像這樣：

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

請注意，有兩個不同的狀態值。 最上層狀態是索引子本身的狀態。 [執行中的索引子]**狀態表示索引**器已正確設定且可供執行，但不是目前正在執行。

每次執行索引子時，都有它自己的狀態，以指出該特定執行是否正在進行**中（執行**中），或已完成且具有**成功**或**TransientError**狀態。 

重設索引子以重新整理其變更追蹤狀態時，會加入具有**重設**狀態的個別歷程記錄專案。

如需有關狀態碼和索引子監視資訊的詳細資訊，請參閱 REST API 中的[GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) 。

您可以藉由列舉清單和來抓取檔特定錯誤或警告的詳細資料 `IndexerExecutionResult.Errors` `IndexerExecutionResult.Warnings` 。

如需用來監視索引子之 .NET SDK 類別的詳細資訊，請參閱[IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet)和[IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)。
