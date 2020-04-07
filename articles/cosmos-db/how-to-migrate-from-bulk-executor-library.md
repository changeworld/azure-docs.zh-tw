---
title: 從批次執行器庫移到 Azure Cosmos DB .NET V3 SDK 中的批次支援
description: 瞭解如何將應用程式從使用批次執行器庫移至 Azure Cosmos DB SDK V3 中的批次支援
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: maquaran
ms.openlocfilehash: 820a5398d84122659b1676b7d5722bce08b1837d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755968"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>從批次執行器庫移到 Azure Cosmos DB .NET V3 SDK 中的批次支援

本文介紹了將使用[.NET 批量執行器庫](bulk-executor-dot-net.md)的現有應用程式代碼遷移到最新版本 .NET SDK 中的[批量支援](tutorial-sql-api-dotnet-bulk-import.md)功能所需的步驟。

## <a name="enable-bulk-support"></a>開啟批次支援

使用`CosmosClient`[「允許批次執行」](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)設定對實體啟用批次支援:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>建立工作建立工作

.NET SDK 中的批量支援的工作原理是利用[任務並行庫](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl)並同時發生的操作分組。 

沒有一種方法將文檔或操作清單作為輸入參數,而是需要為要批量執行的每個操作創建一個任務。

例如,如果初始輸入是每個專案具有以下架構的項目清單:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

如果要執行批次匯入(類似於使用 BulkExecutor.BulkImportAsync),則需要對每個項目值進行併發呼叫`CreateItemAsync`。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

如果要執行批量*更新*(類似於使用[BulkExecutor.BulkUpdateAsync),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)則需要在更新`ReplaceItemAsync`項值後對 方法進行併發調用。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

如果要執行批次*刪除*(類似於使用[BulkExecutor.BulkDeleteAsync),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)則需要使用`DeleteItemAsync``id`每個項目的與分割區鍵對 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>擷取工作結果狀態

在前面的代碼示例中,您已經創建了任務的併發清單,並在每個任務上調用`CaptureOperationResponse`了方法。 此方法是一個擴充,讓我們透過捕捉任何錯誤和追蹤[要求單位使用方式](request-units.md)來維護與 BulkExecutor*的回應架構*。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

其中聲明`OperationResponse`為:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>同時執行作業

定義任務清單後,請等待,直到它們全部完成。 您可以通過定義批次操作的範圍來追蹤工作的完成情況,如以下代碼段所示:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>擷取統計資訊

前面的代碼等待所有操作完成並計算所需的統計資訊。 這些統計資料與批次執行器庫的[批次匯入回應相似](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

包含`BulkOperationResponse`:

1. 通過批量支援處理操作清單所佔用的總時間。
1. 成功操作的數量。
1. 已使用的請求單位總數。
1. 如果存在失敗,它將顯示包含異常的元數清單以及用於日誌記錄和標識目的的關聯項。

## <a name="retry-configuration"></a>重試設定

批次執行器庫具有重新[試選項](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions)`0``MaxRetryWaitTimeInSeconds`設定為`MaxRetryAttemptsOnThrottledRequests`將控制項的權子派給函式庫[的指導](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account)。

對於 .NET SDK 中的批量支援,沒有隱藏行為。 您可以透過[宇宙用戶端選項](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests)直接配置重試[CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests)選項。

> [!NOTE]
> 如果預配的請求單位根據數據量遠低於預期值,則可能需要考慮將這些單位設置為高值。 批量操作需要更長的時間,但由於重試度較高,因此完全成功的可能性更大。

## <a name="performance-improvements"></a>效能改善

與 .NET SDK 的其他操作一樣,使用流 API 可提高性能並避免任何不必要的序列化。 

僅當使用的數據的性質與位元組流(例如檔流)的性質匹配時,才可能使用流 API。 在這種情況下,使用`CreateItemStreamAsync`、`ReplaceItemStreamAsync`或`DeleteItemStreamAsync`方法以及使用`ResponseMessage`(`ItemResponse`而不是 ) 可提高可實現的輸送量。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關 .NET SDK 版本的更多,請參閱[Azure 宇宙 DB SDK](sql-api-sdk-dotnet.md)一文。
* 從 GitHub 取得完整的[移植原始碼](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration)。
* [GitHub 上的其他批次範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
