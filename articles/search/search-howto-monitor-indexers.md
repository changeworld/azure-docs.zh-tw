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
ms.custom: devx-track-csharp
ms.openlocfilehash: 06a31e61583ac28218b34195dd6e5b7f92776dce
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541232"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>如何監視 Azure 認知搜尋索引子狀態和結果

Azure 認知搜尋提供有關每個索引子目前和歷程記錄執行的狀態和監視資訊。

當您想要執行下列動作時，索引子監視會很有用：

* 在進行中的執行期間追蹤索引子的進度。
* 查看進行中或上一個索引子執行的結果。
* 找出最上層的索引子錯誤，以及有關要編制索引之個別檔的錯誤或警告。

## <a name="get-status-and-history"></a>取得狀態和歷程記錄

您可以透過各種方式來存取索引子監視資訊，包括：

* 在 [Azure 入口網站](#portal)
* 使用 [REST API](#restapi)
* 使用 [.NET SDK](#dotnetsdk)

可用的索引子監視資訊包括下列所有 (，但資料格式會根據使用) 的存取方法而有所不同：

* 索引子本身的狀態資訊
* 最新的索引子執行的相關資訊，包括其狀態、開始和結束時間，以及詳細的錯誤和警告。
* 歷程記錄索引子的執行清單，以及其狀態、結果、錯誤和警告。

處理大量資料的索引子可能需要很長的時間才能執行。 例如，處理數百萬個來源文件的索引子可以執行24小時，然後幾乎立即重新開機。 高容量索引子的狀態在入口網站中可能一律會顯示為「 **進行** 中」。 即使是在執行中的索引子，也可以取得進行中進度和先前執行的詳細資料。

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>使用入口網站監視

您可以在 [搜尋服務] 頁面上的 [ **索引子** ] 清單中看到所有索引子的目前狀態。

   ![索引子清單](media/search-monitor-indexers/indexers-list.png "索引子清單")

當索引子執行時，清單中的狀態會顯示為 [ **進行中**]，而 [檔 **成功** ] 值會顯示到目前為止處理的檔數目。 入口網站可能需要幾分鐘的時間來更新索引子狀態值和檔計數。

最近執行成功的索引子會顯示 **成功**。 如果個別檔的錯誤數目小於索引子的 **最大失敗專案** 設定，則索引子執行可能會成功。

如果最近一次的執行結束但發生錯誤，則狀態會顯示為 [ **失敗**]。 **重設**狀態表示已重設索引子的變更追蹤狀態。

按一下清單中的索引子，以查看有關索引子目前和最近執行的更多詳細資料。

   ![索引子摘要和執行歷程記錄](media/search-monitor-indexers/indexer-summary.png "索引子摘要和執行歷程記錄")

**索引子摘要**圖表會顯示其最新執行中所處理檔數目的圖表。

[ **執行詳細資料** ] 清單最多會顯示50的最新執行結果。

按一下清單中的執行結果，以查看該執行的詳細資訊。 這包括其開始和結束時間，以及任何發生的錯誤和警告。

   ![索引子執行詳細資料](media/search-monitor-indexers/indexer-execution.png "索引子執行詳細資料")

如果在執行期間發生檔特定的問題，這些問題會列在 [錯誤和警告] 欄位中。

   ![具有錯誤的索引子詳細資料](media/search-monitor-indexers/indexer-execution-error.png "具有錯誤的索引子詳細資料")

警告常見於某些類型的索引子，而且不一定表示有問題。 例如，使用認知服務的索引子可以在影像或 PDF 檔案未包含任何要處理的文字時報告警告。

如需調查索引子錯誤和警告的詳細資訊，請參閱 [Azure 認知搜尋中的常見索引子問題疑難排解](search-indexer-troubleshooting.md)。

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>使用 REST Api 進行監視

您可以使用 [ [取得索引子狀態] 命令](/rest/api/searchservice/get-indexer-status)來取得索引子的狀態和執行歷程記錄：

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

執行歷程記錄最多包含50個最近的執行，這些回合會以反向時間順序排序 (最近的第一個) 。

請注意，有兩個不同的狀態值。 最上層狀態是索引子本身的狀態。 索引子狀態為 [執行中 **] 表示索引** 器已正確設定且可供執行，但不是目前正在執行。

每次執行的索引子也都有自己的狀態，指出該特定執行 **是否正在進行中， (執行**) ，還是已完成，但有 **成功**、 **transientFailure**或 **persistentFailure** 狀態。 

重設索引子以重新整理其變更追蹤狀態時，會加入具有 **重設** 狀態的個別執行歷程記錄專案。

如需狀態碼和索引子監視資料的詳細資訊，請參閱 [GetIndexerStatus](/rest/api/searchservice/get-indexer-status)。

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>使用 .NET SDK 進行監視

您可以使用 Azure 認知搜尋 .NET SDK 來定義索引子的排程。 若要這樣做，請在建立或更新索引子時包含 **schedule** 屬性。

下列 c # 範例會寫入索引子狀態的相關資訊，以及其最近 (或正在進行的) 執行至主控台的結果。

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

請注意，有兩個不同的狀態值。 最上層狀態是索引子本身的狀態。 索引子狀態為 [執行中 **] 表示索引** 器已正確設定且可供執行，但不是目前正在執行。

每次執行的索引子也都有自己的狀態，以指出該特定執行 **是否正在進行中， (執行**) ，或是已完成且 **成功** 或 **TransientError** 狀態。 

重設索引子以重新整理其變更追蹤狀態時，會加入具有 **重設** 狀態的個別歷程記錄專案。

如需狀態碼和索引子監視資訊的詳細資訊，請參閱 REST API 中的 [GetIndexerStatus](/rest/api/searchservice/get-indexer-status) 。

您可以藉由列舉清單和來抓取檔特定錯誤或警告的詳細資料 `IndexerExecutionResult.Errors` `IndexerExecutionResult.Warnings` 。

如需用來監視索引子之 .NET SDK 類別的詳細資訊，請參閱 [IndexerExecutionInfo](/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo) 和 [IndexerExecutionResult](/dotnet/api/microsoft.azure.search.models.indexerexecutionresult)。