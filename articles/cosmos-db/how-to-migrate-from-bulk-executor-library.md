---
title: 從大量執行程式程式庫遷移至 Azure Cosmos DB .NET V3 SDK 中的大量支援
description: 瞭解如何使用大量執行程式程式庫，將應用程式遷移至 Azure Cosmos DB SDK V3 中的大量支援
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: maquaran
ms.openlocfilehash: d63b34c118cd719f73abbd6711dcb3ef02a6fb28
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146297"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>從大量執行程式程式庫遷移至 Azure Cosmos DB .NET V3 SDK 中的大量支援

本文說明將使用[.net 大量執行](bulk-executor-dot-net.md)程式程式庫之現有應用程式的程式碼，遷移到最新版 .net SDK 中的[大量支援](tutorial-sql-api-dotnet-bulk-import.md)功能時所需的步驟。

## <a name="enable-bulk-support"></a>啟用大量支援

透過 AllowBulkExecution 設定，在`CosmosClient`實例上啟用[AllowBulkExecution](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)大量支援：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>建立每個作業的工作

.NET SDK 中的大量支援可利用同時發生的工作[平行程式庫](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl)和群組作業來運作。 

SDK 中沒有單一方法會將您的檔或作業清單當做輸入參數使用，但您必須針對想要大量執行的每項作業建立工作，然後只等待它們完成。

例如，如果您的初始輸入是專案清單，其中的每個專案都有下列架構：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

如果您想要執行大量匯入（類似于使用 BulkExecutor. BulkImportAsync），您必須具有對`CreateItemAsync`的並行呼叫。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

如果您想要進行大量*更新*（類似于使用[BulkExecutor. BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)），則在更新專案值之後， `ReplaceItemAsync`您必須具有方法的並行呼叫。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

而且，如果您想要執行大量*刪除*（類似于使用[BulkExecutor. BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)），您必須使用每個專案`DeleteItemAsync`的`id`和分割區索引鍵，對進行並行呼叫。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>捕捉工作結果狀態

在先前的程式碼範例中，我們建立了並行的工作清單，並在`CaptureOperationResponse`每個工作上呼叫方法。 這個方法是一個延伸模組，可讓我們將*類似的回應架構*維護為 BulkExecutor，方法是捕捉任何錯誤並追蹤[要求單位的使用量](request-units.md)。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

`OperationResponse`其中，宣告為：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>同時執行作業

為了追蹤整個工作清單的範圍，我們使用此 helper 類別：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

`ExecuteAsync`方法會等到所有作業都完成，而且您可以使用它，如下所示：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>捕捉統計資料

先前的程式碼會等到所有作業都完成，並計算所需的統計資料。 這些統計資料與大量執行程式程式庫的[BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)類似。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse`包含：

1. 透過大量支援來處理作業清單所花費的總時間。
1. 成功作業的數目。
1. 耗用的要求單位總數。
1. 如果發生失敗，它會顯示包含例外狀況的元組清單，以及用於記錄和識別用途的相關聯專案。

## <a name="retry-configuration"></a>重試設定

大量執行程式程式庫的[指引](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) `MaxRetryWaitTimeInSeconds`會將[RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) `0`的`MaxRetryAttemptsOnThrottledRequests`和設定為，以將控制項委派給程式庫。

針對 .NET SDK 中的大量支援，沒有隱藏的行為。 您可以直接透過[CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests)和[CosmosClientOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests)來設定重試選項。

> [!NOTE]
> 如果布建的要求單位比預期的資料量更低，您可能會想要考慮將它們設定為高值。 大量作業需要較長的時間，但因為重試次數過高，所以可能會有更高的成功。

## <a name="performance-improvements"></a>效能改善

如同使用 .NET SDK 的其他作業，使用串流 Api 會產生較佳的效能，並避免任何不必要的序列化。 

只有當您使用的資料本質與位元組資料流程（例如，檔案資料流程）相符時，才可以使用資料流程 Api。 在這種情況下， `CreateItemStreamAsync`使用`ReplaceItemStreamAsync`、或`DeleteItemStreamAsync`方法，並使用`ResponseMessage` （而不`ItemResponse`是）來增加可達成的輸送量。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 .NET SDK 版本，請參閱[AZURE COSMOS DB SDK](sql-api-sdk-dotnet.md)文章。
* 從 GitHub 取得完整的「[遷移」原始程式碼](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration)。
* [GitHub 上的其他大量範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
