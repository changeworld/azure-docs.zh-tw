---
title: 搭配 Azure Cosmos DB Gremlin API 使用圖形大量執行工具 .NET 程式庫
description: 了解如何使用大量執行程式程式庫將圖形資料大量匯入 Azure Cosmos DB Gremlin API 容器中。
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: jasonh
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 53c770bb8cc9d7a80ae7d11b6b1c089fcc9355da
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565627"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>在 Azure Cosmos DB Gremlin API 中使用圖形大量執行程式 .NET 程式庫執行大量作業

本教學課程說明如何使用 Azure Cosmos DB 的大量執行程式 .NET 程式庫，將圖形物件匯入並更新至 Azure Cosmos DB Gremlin API 容器中。 此程序會使用[大量執行程式程式庫](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview)中的圖形類別，以程式設計方式建立頂點和邊緣物件，繼而為個別的網路要求插入其中多項。 此行為可透過大量執行程式程式庫進行設定，以充分運用資料庫和本機記憶體資源。

不同於將 Gremlin 查詢傳送至資料庫時，命令會逐一受到評估繼而執行，使用大量執行程式程式庫時，將必須在本機建立及驗證物件。 建立物件之後，此程式庫可讓您以循序方式將圖形物件傳送至資料庫服務。 使用此方法時，資料擷取速度最多可增加 100 倍，因此這種方法非常適用於初始資料移轉或定期資料移動作業。 若要深入了解，請瀏覽 [Azure Cosmos DB 圖形大量執行程式範例應用程式](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)的 GitHub 頁面。

## <a name="bulk-operations-with-graph-data"></a>圖形資料的大量作業

[大量執行程式程式庫](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet&preserve-view=true)包含 `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` 命名空間，可提供建立和匯入圖形物件的功能。 

下列程序概述如何將資料移轉用於 Gremlin API 容器：
1. 從資料來源擷取記錄。
2. 從取得的記錄建構 `GremlinVertex` 和 `GremlinEdge`物件，並將其新增至 `IEnumerable` 資料結構中。 在應用程式的這個部分中，如果資料來源不是圖形資料庫，則應實作偵測和新增關聯性的邏輯。
3. 使用 [Graph BulkImportAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet&preserve-view=true)，將圖形物件插入集合中。

相較於使用 Gremlin 用戶端，這項機制可改善資料移轉效率。 之所以有此改善，是因為使用 Gremlin 插入資料時，應用程式必須要逐一傳送查詢，且此查詢將必須受到驗證、評估繼而執行，以建立資料。 大量執行程式程式庫會在應用程式中處理驗證，並同時為每個網路要求傳送多個圖形物件。

### <a name="creating-vertices-and-edges"></a>建立頂點和邊緣

`GraphBulkExecutor` 所提供的 `BulkImportAsync` 方法需要 `GremlinVertex` 或 `GremlinEdge`物件的 `IEnumerable` 清單，兩者皆定義於 `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element` 命名空間中。 在此範例中，我們將邊緣和頂點分成兩個 BulkExecutor 匯入工作。 請參閱下面的範例：

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

如需與大量執行程式程式庫的參數有關的詳細資訊，請參閱[對 Azure Cosmos DB 進行 BulkImportData 的主題](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account)。

承載必須具現化為 `GremlinVertex` 和 `GremlinEdge` 物件。 這些物件的建立方式如下：

**頂點**：
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**邊緣**：
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> 在新增邊緣之前，大量執行程式公用程式不會自動檢查是否有現有的頂點。 在執行 BulkImport 工作之前，必須先在應用程式中進行此驗證。

## <a name="sample-application"></a>範例應用程式

### <a name="prerequisites"></a>必要條件
* 啟用 Azure 開發工作負載的 Visual Studio 2019。 您可以使用免費的 [Visual Studio 2019 Community 版本](https://visualstudio.microsoft.com/downloads/)展開工作。
* Azure 訂用帳戶。 您可以[在這裡建立免費的 Azure 帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db)。 或者，您可以透過[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 建立 Cosmos 資料庫帳戶，而不需要 Azure 訂用帳戶。
* 透過**無限制集合**建立 Azure Cosmos DB Gremlin API 資料庫。 本指南將說明如何開始使用 [.NET 中的 Azure Cosmos DB Gremlin API](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet)。
* Git。 如需詳細資訊，請參閱 [Git 下載頁面](https://git-scm.com/downloads)。

### <a name="clone-the-sample-application"></a>複製範例應用程式
在本教學課程中，我們將使用裝載於 GitHub 上的 [Azure Cosmos DB 圖形大量執行程式範例](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)，逐步完成開始使用的步驟。 此應用程式包含一個 .NET 解決方案，會隨機產生頂點和邊緣物件，然後對指定的圖形資料庫帳戶執行大量插入。 若要取得應用程式，請執行下列 `git clone` 命令：

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

此存放庫包含使用下列檔案的 GraphBulkExecutor 範例：

檔案|描述
---|---
`App.config`|應用程式和資料庫特有的參數指定於此處。 此檔案應先修改為連線至目的地資料庫和集合。
`Program.cs`| 此檔案包含建立 `DocumentClient` 集合、處理清除項目和傳送大量執行程式要求時所依據的邏輯。
`Util.cs`| 此檔案包含一個協助程式類別，其中包含產生測試資料以及檢查資料庫和集合是否存在時所依據的邏輯。

在 `App.config` 檔案中，可提供的組態值如下：

設定|描述
---|---
`EndPointUrl`|這是 **您的 .NET SDK 端點** (位於您 Azure Cosmos DB Gremlin API 資料庫帳戶中的 [概觀] 刀鋒視窗中)。 其格式為 `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|這是您的 Azure Cosmos DB 帳戶下所列的主要或次要金鑰。 請深入了解[保護 Azure Cosmos DB 資料的存取](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#primary-keys)
`DatabaseName`, `CollectionName`|這些是**目標資料庫和集合名稱**。 當 `ShouldCleanupOnStart` 設定為 `true` 時，這些值將連同 `CollectionThroughput` 用來卸除資料庫和集合，並建立新的資料庫和集合。 同樣地，如果 `ShouldCleanupOnFinish` 設定為 `true`，則會在擷取結束時用這些值來刪除資料庫。 請注意，目標集合必須是**無限制集合**。
`CollectionThroughput`|如果 `ShouldCleanupOnStart` 選項設定為 `true`，則會使用此值建立新的集合。
`ShouldCleanupOnStart`|這將會在程式執行前卸除資料庫帳戶和集合，然後使用 `DatabaseName`、`CollectionName` 和 `CollectionThroughput` 值建立新的帳戶和集合。
`ShouldCleanupOnFinish`|這將會在程式執行後，卸除具有指定 `DatabaseName` 和 `CollectionName` 的資料庫帳戶和集合。
`NumberOfDocumentsToImport`|這將會決定要在範例中產生的測試頂點和邊緣數目。 此數目會同時套用至頂點和邊緣。
`NumberOfBatches`|這將會決定要在範例中產生的測試頂點和邊緣數目。 此數目會同時套用至頂點和邊緣。
`CollectionPartitionKey`|這將會用來建立測試頂點和邊緣，而這個屬性將會自動指派。 如果 `ShouldCleanupOnStart` 選項設定為 `true`，則在重新建立資料庫和集合時也會使用此值。

### <a name="run-the-sample-application"></a>執行範例應用程式

1. 在 `App.config` 中新增您的特定資料庫組態參數。 這將會用來建立 DocumentClient 執行個體。 如果資料庫和容器尚未建立，將會自動建立。
2. 執行應用程式。 這會呼叫 `BulkImportAsync` 兩次，一個用來匯入頂點，一個用來匯入邊緣。 如有任何物件在插入時產生錯誤，這些物件將會新增至 `.\BadVertices.txt` 或 `.\BadEdges.txt`。
3. 藉由查詢圖形資料庫來評估結果。 如果 `ShouldCleanupOnFinish` 選項設定為 True，則會自動刪除資料庫。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 NuGet 套件詳細資料和大量執行程式 .NET 程式庫的版本資訊，請參閱 [大量執行程式 SDK 詳細資料](sql-api-sdk-bulk-executor-dot-net.md)。 
* 參考[效能祕訣](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips)以進一步最佳化大量執行程式的使用方式。
* 檢閱 [BulkExecutor.Graph 參考文章](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet&preserve-view=true)，以取得與此命名空間中定義的類別和方法有關的詳細資訊。