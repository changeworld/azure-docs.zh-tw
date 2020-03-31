---
title: 監視索引子狀態和結果
titleSuffix: Azure Cognitive Search
description: 使用 REST API 或 .NET SDK 監視 Azure 門戶中的 Azure 認知搜索索引子的狀態、進度和結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112987"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>如何監視 Azure 認知搜索索引子狀態和結果

Azure 認知搜索提供有關每個索引子的當前和歷史運行的狀態和監視資訊。

當您想要：

* 跟蹤索引子在持續運行期間的進度。
* 查看正在進行的或以前的索引子運行的結果。
* 識別頂級索引子錯誤，以及有關要編制索引的單個文檔的錯誤或警告。

## <a name="get-status-and-history"></a>獲取狀態和歷史記錄

您可以通過多種方式訪問索引子監視資訊，包括：

* 在[Azure 門戶](#portal)中
* 使用[REST API](#restapi)
* 使用[.NET SDK](#dotnetsdk)

可用的索引子監視資訊包括以下所有內容（儘管資料格式因所使用的存取方法而異）：

* 有關索引子本身的狀態資訊
* 有關索引子最近運行的資訊，包括其狀態、開始和結束時間以及詳細的錯誤和警告。
* 歷史索引子的清單運行，其狀態、結果、錯誤和警告。

處理大量資料的索引子可能需要很長時間才能運行。 例如，處理數百萬來源文件的索引子可以運行 24 小時，然後幾乎立即重新開機。 大容量索引子的狀態可能始終表示門戶中的 **"正在進行**"。 即使索引子正在運行，有關正在進行的進度和以前的運行的詳細資訊也可用。

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>使用門戶進行監視

您可以在搜索服務概述頁上的**索引子**清單中查看所有索引子的目前狀態。

   ![索引子清單](media/search-monitor-indexers/indexers-list.png "索引子清單")

當索引子執行時，清單中的狀態將顯示 **"正在進行**"，並且 **"文檔繼承"** 值顯示到目前為止處理的文檔數。 門戶可能需要幾分鐘時間更新索引子狀態值和文檔計數。

最近運行成功的索引子顯示**成功**。 如果錯誤數小於索引子的**Max 失敗項**設置，即使單個文檔有錯誤，索引子運行也可以成功。

如果最近的運行以錯誤結束，則狀態將顯示 **"失敗**"。 **重置**狀態表示索引子的更改跟蹤狀態已重置。

按一下清單中的索引子以查看有關索引子當前和最近運行的更多詳細資訊。

   ![索引子摘要和執行歷史記錄](media/search-monitor-indexers/indexer-summary.png "索引子摘要和執行歷史記錄")

**索引子匯總**圖顯示最近運行中處理的文檔數的圖表。

**"執行詳細資訊**"清單最多顯示 50 個最新的執行結果。

按一下清單中的執行結果以查看有關該運行的詳細資訊。 這包括其開始和結束時間，以及發生的任何錯誤和警告。

   ![索引子執行詳細資訊](media/search-monitor-indexers/indexer-execution.png "索引子執行詳細資訊")

如果在運行過程中存在特定于文檔的問題，則這些問題將在"錯誤和警告"欄位中列出。

   ![索引子詳細資訊與錯誤](media/search-monitor-indexers/indexer-execution-error.png "索引子詳細資訊與錯誤")

警告在某些類型的索引子中很常見，並且並不總是指示問題。 例如，當圖像或 PDF 檔不包含要處理的任何文本時，使用認知服務的索引子可以報告警告。

有關調查索引子錯誤和警告的詳細資訊，請參閱在[Azure 認知搜索 中排除常見索引子問題的疑難排解](search-indexer-troubleshooting.md)。

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>使用 REST API 進行監控

您可以使用[獲取索引子狀態命令](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)檢索索引子的狀態和執行歷史記錄：

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

回應包含整體索引子的狀態、最後 (或進行中) 的索引子叫用，以及最新的索引子叫用歷程記錄。

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

執行歷史記錄包含最多 50 個最近的運行，按相反的時間順序排序（最近第一次）。

請注意，有兩個不同的狀態值。 頂級狀態是索引子本身。 索引子**運行**狀態意味著索引子設置正確且可用於運行，但不是索引子當前正在運行。

索引子的每個運行也有自己的狀態，指示該特定執行是正在進行的（**正在運行**），還是已完成**成功**、**暫態失敗**或**持久失敗**狀態。 

當重置索引子以刷新其更改跟蹤狀態時，將添加帶有**重置**狀態的單獨執行歷史記錄條目。

有關狀態碼和索引子監視資料的詳細資訊，請參閱[獲取索引子狀態](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>使用 .NET SDK 進行監視

您可以使用 Azure 認知搜索 .NET SDK 為索引子定義計畫。 為此，請在創建或更新索引子時包括**計畫**屬性。

以下 C# 示例將有關索引子的狀態及其最近（或正在進行）運行到主控台的結果的資訊。

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

主控台中的輸出將如下所示：

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

請注意，有兩個不同的狀態值。 頂級狀態是索引子本身的狀態。 索引子"**正在運行"** 狀態意味著索引子設置正確且可供執行，但不是索引子當前正在執行。

索引子的每個運行也具有其自己的狀態，即該特定執行是正在進行的（**正在運行**），還是已完成**成功或****暫時錯誤**狀態。 

當重置索引子以刷新其更改跟蹤狀態時，將添加帶有**重置**狀態的單獨歷史記錄條目。

有關狀態碼和索引子監視資訊的更多詳細資訊，請參閱 REST API 中的[GetIndexer 狀態](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

可以通過枚舉清單`IndexerExecutionResult.Errors`和`IndexerExecutionResult.Warnings`檢索有關特定于文檔的錯誤或警告的詳細資訊。

有關用於監視索引子的 .NET SDK 類的詳細資訊，請參閱[索引子執行資訊](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet)和[索引子執行結果](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)。
