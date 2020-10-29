---
title: Azure Cosmos DB SQL API：Java SDK v4 範例
description: 在 GitHub 上尋找適合使用 Azure Cosmos DB SQL API 執行之一般工作 (包括 CRUD 作業) 的 Java 範例。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: afc9633c32174cbe1d94535d68e4c8f85f3d137b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478061"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API：Java SDK v4 範例

> [!div class="op_single_selector"]
> * [.NET V2 SDK 範例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 範例](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK 範例](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK 範例](sql-api-spring-data-sdk-samples.md)
> * [Node.js 範例](sql-api-nodejs-samples.md)
> * [Python 範例](sql-api-python-samples.md)
> * [Azure 程式碼範例庫](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> 若要深入了解 Java SDK v4，請檢視 Azure Cosmos DB Java SDK v4 [版本資訊](sql-api-sdk-java-v4.md)、[Maven 存放庫](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、Azure Cosmos DB Java SDK v4 [效能秘訣](performance-tips-java-sdk-v4-sql.md)和 Azure Cosmos DB Java SDK v4 [疑難排解指南](troubleshoot-java-sdk-v4-sql.md)。 如果您目前使用的版本比 v4 舊，請參閱[遷移至 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，以取得升級至 v4 的協助。
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- 您可以[啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：您的 Visual Studio 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

[azure-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) GitHub 存放庫中包含可對 Azure Cosmos DB 資源執行 CRUD 作業和其他常見作業的最新範例應用程式。 本文提供：

* 每個範例 Java 專案檔中各項工作的連結。 
* 相關 API 參考內容的連結。

**先決條件**

執行此範例應用程式需要下列項目：

* Java Development Kit 8
* Azure Cosmos DB Java SDK v4

您可選擇使用 Maven 來取得最新的 Azure Cosmos DB Java SDK v4 二進位檔，以使用於您的專案中。 Maven 會自動加入任何必要的相依性。 否則，您可以直接下載 pom.xml 檔案中列出的相依性，並將它們加入至您的組建路徑。

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**執行範例應用程式**

複製範例存放庫：
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

您可以使用 IDE (Eclipse、IntelliJ 或 VSCODE) 或從命令列使用 Maven 來執行範例。

必須設定這些環境變數

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
```

才能將您帳戶的讀取/寫入存取權授與範例。

若要執行範例，請指定其主要類別 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

其中，sample.synchronicity.MainClass 可以是
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* storedprocedure.sync.SampleStoredProcedure
* storedprocedure.async.SampleStoredProcedureAsync
* changefeed.SampleChangeFeedProcessor (Changefeed只有一個非同步範例，沒有同步範例。)...等等

> [!NOTE]
> 每個範例都各自獨立，會自己設定，並於完成後自行清理。 這些範例會發出多個呼叫來建立 `CosmosContainer`。 每當執行此動作時，即會根據所建立集合的效能層，對您的訂用帳戶計入 1 小時的使用費。 
> 
> 

## <a name="database-examples"></a>資料庫範例
[資料庫 CRUD 範例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java)檔案會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 資料庫，請參閱[使用資料庫、容器和項目](account-databases-containers-items.md)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| [建立資料庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient.createDatabaseIfNotExists |
| [依識別碼讀取資料庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient.getDatabase |
| [讀取所有資料庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [刪除資料庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase.delete |

## <a name="collection-examples"></a>集合範例
[集合 CRUD 範例](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java)檔案會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 集合，請參閱[使用資料庫、容器和項目](account-databases-containers-items.md)概念性文章。

| Task | API 參考資料 |
| --- | --- |
| [建立集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase.createContainerIfNotExists |
| [變更已設定的集合效能](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [依識別碼取得集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase.getContainer |
| [讀取資料庫中的所有集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [刪除集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>自動調整集合範例

若要在執行這些範例之前深入了解自動調整功能，請參閱這些指示，以在您的[帳戶](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/)和[資料庫和容器](./provision-throughput-autoscale.md)中啟用自動調整。

[自動調整資料庫 CRUD 範例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java)檔案會說明如何執行下列工作。

| Task | API 參考資料 |
| --- | --- |
| [以指定的自動調整最大輸送量建立資料庫](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient.createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

[自動調整集合 CRUD 範例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java)檔案會說明如何執行下列工作。 

| Task | API 參考資料 |
| --- | --- |
| [以指定的自動調整最大輸送量建立集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase.createContainerIfNotExists |
| [變更集合上已設定的自動調整最大輸送量](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [讀取集合的自動調整輸送量設定](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>分析儲存體集合範例

[分析儲存體集合 CRUD 範例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java)檔案會說明如何執行下列工作。 若要在執行下列範例之前了解 Azure Cosmos 集合，請閱讀 Azure Cosmos DB Synapse 和分析存放區。

| Task | API 參考資料 |
| --- | --- |
| [建立集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>文件範例
[文件 CRUD 範例](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java)檔案會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos 文件，請參閱[使用資料庫、容器和項目](account-databases-containers-items.md)概念性文章。

| Task | API 參考資料 |
| --- | --- |
| [建立文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer.createItem |
| [依識別碼讀取文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [查詢文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [取代文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [更新插入文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [刪除文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [以條件式 ETag 檢查取代文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition.setType<br>AccessCondition.setCondition |
| [僅在文件變更時才讀取文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>索引範例
[集合 CRUD 範例](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java)檔案會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的索引功能，請參閱[索引原則](index-policy.md)、[索引類型](index-overview.md#index-kinds)及[索引路徑](index-policy.md#include-exclude-paths)概念性文章。 

| Task | API 參考資料 |
| --- | --- |
| 從索引中排除某個文件 | ExcludedIndex<br>IndexingPolicy |
| 使用延遲索引 | IndexingPolicy.IndexingMode |
| [在索引中包含指定的文件路徑](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [從索引中排除指定的文件路徑](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [建立複合式索引](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| 對雜湊索引路徑強制執行範圍掃描作業 | FeedOptions.EnableScanInQuery |
| 對字串使用範圍索引 | IndexingPolicy.IncludedPaths<br>RangeIndex |
| 執行索引轉換 | - |
| [建立地理空間索引](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

如需索引編製的詳細資訊，請參閱 [Azure Cosmos DB 編製索引原則](index-policy.md)。

## <a name="query-examples"></a>查詢範例
[查詢範例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java)檔案會說明如何使用 SQL 查詢文法來執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的 SQL 查詢參考，請參閱[適用於 Azure Cosmos DB 的 SQL 查詢範例](./sql-query-getting-started.md)。 

| Task | API 參考資料 |
| --- | --- |
| [查詢所有文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [使用 == 查詢等號比較](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [使用 != 和 NOT 查詢不等比較](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [使用 >、<、>=、<= 等範圍運算子進行查詢](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [使用範圍運算子對字串進行查詢](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [使用 ORDER BY 進行查詢](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [使用 DISTINCT 進行查詢](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [使用彙總函式進行查詢](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [使用子文件](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [使用文件內聯結進行查詢](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [使用字串、數學和陣列運算子進行查詢](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [透過使用 SqlQuerySpec 的參數化 SQL 進行查詢](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [使用明確分頁進行查詢](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [以平行方式查詢分割的集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| 使用 RDER BY 查詢分割集合 | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>變更摘要範例 
[變更摘要處理器範例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java)檔案會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的變更摘要，請參閱[讀取 Azure Cosmos DB 變更摘要](read-change-feed.md)和[變更摘要處理器](change-feed-processor.md)。

| Task | API 參考資料 |
| --- | --- |
| [基本變更摘要功能](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| 從特定時間開始讀取變更摘要 | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [從頭開始讀取變更摘要](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>伺服器端程式設計範例

[預存程序範例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java)檔案會說明如何執行下列工作。 若要在執行下列範例之前先了解 Azure Cosmos DB 中的伺服器端程式設計，請參閱[預存程序、觸發程序和使用者定義函式](stored-procedures-triggers-udfs.md)。

| Task | API 參考資料 |
| --- | --- |
| [建立預存程序](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [執行預存程序](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.execute |
| [刪除預存程序](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>使用者管理範例
使用者管理範例檔案會說明如何執行下列工作：

| Task | API 參考資料 |
| --- | --- |
| 建立使用者 | - |
| 設定集合或文件的權限 | - |
| 取得使用者權限清單 |- |
