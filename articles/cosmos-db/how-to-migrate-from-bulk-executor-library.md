---
title: 從大量執行程式程式庫遷移至 Azure Cosmos DB .NET V3 SDK 的大量支援
description: 瞭解如何使用大量執行程式程式庫，將您的應用程式遷移至 Azure Cosmos DB SDK V3 中的大量支援
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 24d6b475964e4bf7745495e9c41d0e89bb76f7e9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341275"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>從大量執行程式程式庫遷移至 Azure Cosmos DB .NET V3 SDK 的大量支援
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文描述將使用 [.net 大量執行](bulk-executor-dot-net.md) 程式程式庫的現有應用程式程式碼遷移至最新版 .net SDK 中的 [大量支援](tutorial-sql-api-dotnet-bulk-import.md) 功能所需的步驟。

## <a name="enable-bulk-support"></a>啟用大量支援

透過 AllowBulkExecution 設定在實例上啟用大量支援 `CosmosClient` ： [AllowBulkExecution](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>為每個作業建立工作

.NET SDK 的大量支援的運作方式是利用工作 [平行程式庫](/dotnet/standard/parallel-programming/task-parallel-library-tpl) 和同時進行的群組作業。 

SDK 中沒有任何單一方法可將您的檔或作業清單做為輸入參數，但您必須為您想要大量執行的每個作業建立工作，然後等候它們完成。

例如，如果您的初始輸入是專案的清單，其中每個專案都有下列架構：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

如果您想要進行大量匯入 (類似于使用 BulkExecutor. BulkImportAsync) ，您必須同時呼叫 `CreateItemAsync` 。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

如果您想要進行大量 *更新* (類似于使用 [BulkExecutor. BulkUpdateAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)) ，則在 `ReplaceItemAsync` 更新專案值之後，您必須有對方法的並行呼叫。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

而且，如果您想要進行大量 *刪除* (類似于使用 [BulkExecutor. BulkDeleteAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)) ，您必須具有的並行呼叫 `DeleteItemAsync` ，以及 `id` 每個專案的和分割區索引鍵。 例如：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>捕捉工作結果狀態

在先前的程式碼範例中，我們建立了一份並行的工作清單，並 `CaptureOperationResponse` 在每個工作上呼叫方法。 此方法是一個延伸模組，可讓我們藉由捕捉任何錯誤並追蹤 [要求單位使用量](request-units.md)，來維持與 BulkExecutor *類似的回應架構* 。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

其中會將宣告 `OperationResponse` 為：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>同時執行作業

為了追蹤整個工作清單的範圍，我們使用此 helper 類別：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

`ExecuteAsync`方法會等候所有作業完成，而且您可以使用它，如下所示：

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>捕獲統計資料

先前的程式碼會等候所有作業完成，並計算所需的統計資料。 這些統計資料類似于大量執行程式程式庫的 [BulkImportResponse](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)。

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse`包含：

1. 透過大量支援來處理作業清單所花費的總時間。
1. 成功的作業數目。
1. 耗用的要求單位總計。
1. 如果發生失敗，則會顯示包含例外狀況的元組清單，以及用於記錄和識別用途的相關聯專案。

## <a name="retry-configuration"></a>重試設定

大量執行程式程式庫的 [指引](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) ，是為了 `MaxRetryWaitTimeInSeconds` 將 `MaxRetryAttemptsOnThrottledRequests` 控制項委派給程式庫，而將 [RetryOptions](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) 設定為 `0` 。

若要在 .NET SDK 中進行大量支援，則不會有隱藏的行為。 您可以直接透過 [CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) 和 [CosmosClientOptions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests)設定重試選項。

> [!NOTE]
> 如果布建的要求單位遠低於預期的資料量，您可能會想要考慮將這些單位設定為高值。 大量作業需要較長的時間，但由於重試次數較高，所以會有較高的機率。

## <a name="performance-improvements"></a>效能改進

如同使用 .NET SDK 的其他作業，使用串流 Api 會產生較佳的效能，並避免任何不必要的序列化。 

只有當您使用的資料本質與位元組資料流程的本質相符時，才可能使用 stream Api (例如，) 的檔案資料流程。 在這種情況下，使用 `CreateItemStreamAsync` 、 `ReplaceItemStreamAsync` 或 `DeleteItemStreamAsync` 方法，並使用 `ResponseMessage` (而不是 `ItemResponse`) 會提高可達到的輸送量。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 .NET SDK 版本，請參閱 [AZURE COSMOS DB sdk](sql-api-sdk-dotnet.md) 文章。
* 從 GitHub 取得完整的 [遷移原始碼](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) 。
* [GitHub 上的其他大量範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)