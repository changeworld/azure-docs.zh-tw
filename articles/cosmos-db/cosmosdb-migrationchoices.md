---
title: Cosmos DB 遷移選項
description: 本檔說明將內部部署或雲端資料移轉至 Azure Cosmos DB 的各種選項。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 4de6d4ba019af75b0f6179b2794ddb6c1e35e0c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030067"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>將您的內部部署或雲端資料移轉至 Azure Cosmos DB 的選項

您可以從各種資料來源將資料載入至 Azure Cosmos DB。 由於 Azure Cosmos DB 支援多個 Api，因此目標可以是任何現有的 Api。 以下是您將資料移轉至 Azure Cosmos DB 的一些案例：

* 將資料從一個 Azure Cosmos 容器移至相同資料庫中的另一個容器或不同的資料庫。
* 將專用容器之間的資料移至共用資料庫容器。
* 將位於 >region1 的 Azure Cosmos 帳戶中的資料移至相同或不同區域中的另一個 Azure Cosmos 帳戶。
* 將資料從 Azure blob 儲存體、JSON 檔案、Oracle 資料庫、Couchbase、DynamoDB 等來源移至 Azure Cosmos DB。

為了支援從各種來源到不同 Azure Cosmos DB Api 的遷移路徑，有多種解決方案可為每個遷移路徑提供特殊處理。 本檔列出可用的解決方案，並說明其優點與限制。

## <a name="factors-affecting-the-choice-of-migration-tool"></a>影響遷移工具選擇的因素

下列因素決定了遷移工具的選擇：

* **線上與離線遷移**：許多遷移工具都提供僅執行單次遷移的路徑。 這表示存取資料庫的應用程式可能會遇到一段時間的停機時間。 某些遷移解決方案提供了一種方法來進行即時移轉，在來源與目標之間會設定複寫管線。

* **資料來源**：現有的資料可以位於不同的資料來源，例如 Oracle DB2、Datastax Cassanda、Azure SQL Database、于 postgresql 等。資料也可以在現有的 Azure Cosmos DB 帳戶中，而遷移意圖可以是變更資料模型，或重新分割具有不同分割區索引鍵之容器中的資料。

* **AZURE COSMOS DB api**：針對 Azure Cosmos DB 中的 SQL api，Azure Cosmos DB 小組會開發各種不同的工具，以協助進行不同的遷移案例。 所有其他 Api 都有專屬的專門工具組，由該社區開發和維護。 由於 Azure Cosmos DB 在有線通訊協定層級支援這些 Api，因此這些工具在將資料移轉至 Azure Cosmos DB 時，應該會依原樣運作。 不過，它們可能需要針對節流進行自訂處理，因為這是 Azure Cosmos DB 特有的概念。

* **資料大小**：大部分的遷移工具適用于較小的資料集。 當資料集超過幾百 gb 時，就會限制遷移工具的選擇。 

* **預期的遷移持續時間**：您可以將遷移設定為以較低的遞增步調進行，以取用較低的輸送量，或取用在目標 Azure Cosmos DB 容器上布建的整個輸送量，並在較短的時間內完成遷移。

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API

|遷移類型|解決方法|支援的來源|支援的目標|考量|
|---------|---------|---------|---------|---------|
|離線|[資料移轉工具](import-data.md)| &bull;JSON/CSV 檔案<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;資料表儲存體<br/>&bull;AWS DynamoDB<br/>&bull;Azure Blob 儲存體|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB 資料表 API<br/>&bull;JSON 檔案 |&bull; 易於設定及支援多個來源。 <br/>&bull; 不適用於大型資料集。|
|離線|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV 檔案<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;適用於 MongoDB 的 Azure Cosmos DB API<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;資料表儲存體<br/>&bull;Azure Blob 儲存體 <br/> <br/>如需其他支援的來源，請參閱 [Azure Data Factory](../data-factory/connector-overview.md) 文章。|&bull;Azure Cosmos DB SQL API<br/>&bull;適用於 MongoDB 的 Azure Cosmos DB API<br/>&bull;JSON 檔案 <br/><br/> 如需其他支援的目標，請參閱 [Azure Data Factory](../data-factory/connector-overview.md) 文章。 |&bull; 易於設定及支援多個來源。<br/>&bull; 利用 Azure Cosmos DB 大量執行程式程式庫。 <br/>&bull; 適用于大型資料集。 <br/>&bull; 缺乏檢查點處理-這表示如果在遷移過程中發生問題，您必須重新開機整個遷移程式。<br/>&bull; 缺少寄不出的信件佇列-這表示有幾個錯誤的檔案可能會停止整個遷移程式。|
|離線|[Azure Cosmos DB Spark 連接器](spark-connector.md)|Azure Cosmos DB SQL API。 <br/><br/>您可以使用其他來源與 Spark 生態系統中的其他連接器。| Azure Cosmos DB SQL API。 <br/><br/>您可以使用其他目標搭配 Spark 生態系統中的其他連接器。| &bull; 利用 Azure Cosmos DB 大量執行程式程式庫。 <br/>&bull; 適用于大型資料集。 <br/>&bull; 需要自訂 Spark 安裝程式。 <br/>&bull; Spark 是對架構不一致的影響，而這在遷移期間可能會造成問題。 |
|離線|[使用 Cosmos DB 大量執行程式程式庫的自訂工具](migrate-cosmosdb-data.md)| 來源取決於您的自訂程式碼 | Azure Cosmos DB SQL API| &bull; 提供檢查點、無效信件功能，以提高遷移復原能力。 <br/>&bull; 適用于非常大型的資料集 (10 TB 以上的) 。  <br/>&bull; 需要以 App Service 的形式執行此工具的自訂設定。 |
|線上|[Cosmos DB 函數 + ChangeFeed API](change-feed-functions.md)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; 容易設定。 <br/>&bull; 只有在來源是 Azure Cosmos DB 的容器時，才會運作。 <br/>&bull; 不適用於大型資料集。 <br/>&bull; 不會從來源容器中捕捉刪除。 |
|線上|[使用 ChangeFeed 的自訂遷移服務](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; 提供進度追蹤。 <br/>&bull; 只有在來源是 Azure Cosmos DB 的容器時，才會運作。 <br/>&bull; 也適用于較大型的資料集。<br/>&bull; 需要使用者設定 App Service，以裝載變更摘要處理器。 <br/>&bull; 不會從來源容器中捕捉刪除。|
|線上|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> 如需其他支援的來源，請參閱 [Striim 網站](https://www.striim.com/sources-and-targets/) 。 |&bull;Azure Cosmos DB SQL API <br/>&bull; Azure Cosmos DB Cassandra API<br/><br/> 如需其他支援的目標，請參閱 [Striim 網站](https://www.striim.com/sources-and-targets/) 。 | &bull; 適用于各種不同的來源，例如 Oracle、DB2、SQL Server。<br/>&bull; 輕鬆建立 ETL 管線，並提供監視的儀表板。 <br/>&bull; 支援較大型的資料集。 <br/>&bull; 由於這是協力廠商工具，因此必須從 marketplace 購買並安裝在使用者的環境中。|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API

|遷移類型|解決方法|支援的來源|支援的目標|考量|
|---------|---------|---------|---------|---------|
|線上|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|適用於 MongoDB 的 Azure Cosmos DB API |&bull; 利用 Azure Cosmos DB 大量執行程式程式庫。 <br/>&bull; 適用于大型資料集，並負責複寫即時變更。 <br/>&bull; 僅適用于其他 MongoDB 來源。|
|離線|[Azure 資料庫移轉服務](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| 適用於 MongoDB 的 Azure Cosmos DB API| &bull; 利用 Azure Cosmos DB 大量執行程式程式庫。 <br/>&bull; 適用于大型資料集，並負責複寫即時變更。 <br/>&bull; 僅適用于其他 MongoDB 來源。|
|離線|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV 檔案<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;適用於 MongoDB 的 Azure Cosmos DB API <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;資料表儲存體<br/>&bull;Azure Blob 儲存體 <br/><br/> 如需其他支援的來源，請參閱 [Azure Data Factory](../data-factory/connector-overview.md) 文章。 | &bull;Azure Cosmos DB SQL API<br/>&bull;適用於 MongoDB 的 Azure Cosmos DB API <br/>&bull; JSON 檔案 <br/><br/> 如需其他支援的目標，請參閱 [Azure Data Factory](../data-factory/connector-overview.md) 文章。| &bull; 易於設定及支援多個來源。 <br/>&bull; 利用 Azure Cosmos DB 大量執行程式程式庫。 <br/>&bull; 適用于大型資料集。 <br/>&bull; 缺少檢查點，表示在遷移過程中的任何問題都需要重新開機整個遷移程式。<br/>&bull; 缺少寄不出的信件佇列，表示有幾個錯誤的檔案可能會停止整個遷移程式。 <br/>&bull; 需要自訂程式碼，以增加某些資料來源的讀取輸送量。|
|離線|[現有的 Mongo 工具 (mongodump、mongorestore、Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | 適用於 MongoDB 的 Azure Cosmos DB API| &bull; 易於設定和整合。 <br/>&bull; 需要針對節流進行自訂處理。|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API

|遷移類型|解決方法|支援的來源|支援的目標|考量|
|---------|---------|---------|---------|---------|
|離線|[cqlsh 複製命令](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|CSV 檔案 | Azure Cosmos DB Cassandra API| &bull; 容易設定。 <br/>&bull; 不適用於大型資料集。 <br/>&bull; 只有在來源是 Cassandra 資料表時，才會運作。|
|離線|[使用 Spark 複製資料表](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;Azure Cosmos DB Cassandra API| Azure Cosmos DB Cassandra API | &bull; 可以利用 Spark 功能來平行處理轉換和內嵌。 <br/>&bull; 需要具有自訂重試原則的設定，以處理節流。|
|線上|[從 Oracle DB/Apache Cassandra) Striim (](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> 如需其他支援的來源，請參閱 [Striim 網站](https://www.striim.com/sources-and-targets/) 。|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Cassandra API <br/><br/> 如需其他支援的目標，請參閱 [Striim 網站](https://www.striim.com/sources-and-targets/) 。| &bull; 適用于各種不同的來源，例如 Oracle、DB2、SQL Server。 <br/>&bull; 輕鬆建立 ETL 管線，並提供監視的儀表板。 <br/>&bull; 支援較大型的資料集。 <br/>&bull; 由於這是協力廠商工具，因此必須從 marketplace 購買並安裝在使用者的環境中。|
|線上|[從 Oracle DB/Apache Cassandra) blitzz 將 (](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>如需其他支援的來源，請參閱 [blitzz 將網站](https://www.blitzz.io/) 。 |Azure Cosmos DB Cassandra API。 <br/><br/>如需其他支援的目標，請參閱 [blitzz 將網站](https://www.blitzz.io/) 。 | &bull; 支援較大型的資料集。 <br/>&bull; 由於這是協力廠商工具，因此必須從 marketplace 購買並安裝在使用者的環境中。|

## <a name="other-apis"></a>其他 API

針對 SQL API、Mongo API 和 Cassandra API 以外的 Api，每個 API 的現有生態系統都支援各種不同的工具。 

**資料表 API** 

* [資料移轉工具](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Gremlin API**

* [圖形大量執行程式程式庫](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>後續步驟

* 試用使用 [.net](bulk-executor-dot-net.md) 和 [JAVA](bulk-executor-java.md)大量執行程式程式庫的範例應用程式，以深入瞭解。 
* 大量執行程式程式庫已整合到 Cosmos DB Spark 連接器中，若要深入瞭解，請參閱 [Azure Cosmos DB Spark 連接器](spark-connector.md) 文章。  
* 若要取得大規模遷移的額外說明，請在「一般諮詢」問題類型和「大型 (TB +) 遷移」問題子類別中開啟支援票證，以與 Azure Cosmos DB 產品小組聯繫。
