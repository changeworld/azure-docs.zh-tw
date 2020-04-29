---
title: Cosmos DB 遷移選項
description: 本檔說明將內部部署或雲端資料移轉至 Azure Cosmos DB 的各種選項
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 34698a215477abdd7d68c3dfe050657ecf049690
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984890"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>將內部部署或雲端資料移轉至 Azure Cosmos DB 的選項

您可以從各種資料來源將資料載入 Azure Cosmos DB。 此外，由於 Azure Cosmos DB 支援多個 Api，因此目標可以是任何現有的 Api。 為了支援從各種來源到不同 Azure Cosmos DB Api 的遷移路徑，有多個解決方案可為每個遷移路徑提供特殊處理。 本檔列出可用的解決方案，並說明其優點和限制。

## <a name="factors-affecting-the-choice-of-migration-tool"></a>影響遷移工具選擇的因素

下列因素決定了遷移工具的選擇：
* **線上與離線遷移**：許多遷移工具提供僅執行一次性遷移的途徑。 這表示存取資料庫的應用程式可能會遇到一段停機時間。 有些遷移解決方案提供了一種方式，可在來源與目標之間設定複寫管線的情況下進行即時移轉。

* **資料來源**：現有的資料可以位於各種資料來源，例如 Oracle DB2、Datastax Cassanda、Azure SQL Server、于 postgresql 等等。資料也可以在現有的 Azure Cosmos DB 帳戶中，而遷移的意圖可以是變更資料模型，或使用不同的分割區索引鍵來重新分割容器中的資料。

* **AZURE COSMOS DB API**：在 AZURE COSMOS DB 的 SQL api 中，有許多由 Azure Cosmos DB 小組開發的工具，可協助不同的遷移案例。 所有其他 Api 都有一組專門由社區開發和維護的專屬工具。 由於 Azure Cosmos DB 支援連線通訊協定層級的這些 Api，因此這些工具應該會依情況正常執行，同時將資料移轉至 Azure Cosmos DB。 不過，它們可能需要針對節流進行自訂處理，因為此概念是 Azure Cosmos DB 特有的。

* **資料大小**：大部分的遷移工具適用于較小的資料集。 當資料集超過一百 gb 時，遷移工具的選擇會受到限制。 

* **預期的遷移持續時間**：可將遷移設定為以速度較低的漸進步調進行，或耗用在目標 Azure Cosmos DB 容器上布建的整個輸送量，並在較短的時間內完成遷移。

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**遷移類型**|**解決方法**|**考量**|
|---------|---------|---------|
|離線|[資料移轉工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;易於設定和支援多個來源 <br/>&bull;不適合大型資料集|
|離線|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;易於設定和支援多個來源 <br/>&bull;利用 Azure Cosmos DB 大量執行程式程式庫 <br/>&bull;適用于大型資料集 <br/>&bull;缺少檢查點-這表示如果在遷移過程中發生問題，您需要重新開機整個遷移程式<br/>&bull;缺少寄不出的信件佇列-這表示有幾個錯誤的檔案可能會停止整個遷移程式。|
|離線|[Azure Cosmos DB Spark 連接器](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;利用 Azure Cosmos DB 大量執行程式程式庫 <br/>&bull;適用于大型資料集 <br/>&bull;需要自訂 Spark 安裝程式 <br/>&bull;Spark 對架構不一致很敏感，而這在遷移期間可能會造成問題 |
|離線|[具有 Cosmos DB 大量執行程式程式庫的自訂工具](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;提供檢查點、無效信件功能，可提高遷移復原能力 <br/>&bull;適用于非常大型的資料集（10 TB +）  <br/>&bull;需要以 App Service 的形式執行此工具的自訂設定 |
|線上|[Cosmos DB 函式 + Changefeed program.cs API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;易於設定 <br/>&bull;僅適用于來源為 Azure Cosmos DB 容器的情況 <br/>&bull;不適合大型資料集 <br/>&bull;不會從來源容器中捕捉刪除 |
|線上|[使用 Changefeed program.cs 的自訂遷移服務](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;提供進度追蹤 <br/>&bull;僅適用于來源為 Azure Cosmos DB 容器的情況 <br/>&bull;也適用于較大的資料集 <br/>&bull;需要使用者設定 App Service 來裝載變更摘要處理器 <br/>&bull;不會從來源容器中捕捉刪除|
|線上|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;適用于許多不同的來源，例如 Oracle、DB2、SQL Server <br/>&bull;輕鬆建立 ETL 管線並提供監視的儀表板 <br/>&bull;支援較大的資料集 <br/>&bull;因為這是協力廠商工具，所以必須從 marketplace 購買並安裝在使用者的環境中。|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**遷移類型**|**解決方法**|**考量**|
|---------|---------|---------|
|離線|[資料移轉工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;易於設定和支援多個來源 <br/>&bull;不適合大型資料集|
|離線|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;易於設定和支援多個來源 <br/>&bull;利用 Azure Cosmos DB 大量執行程式程式庫 <br/>&bull;適用于大型資料集 <br/>&bull;缺少檢查點表示在遷移過程中的任何問題，都需要重新開機整個遷移程式<br/>&bull;缺少寄不出的信件佇列，表示有幾個錯誤的檔案可能會停止整個遷移程式 <br/>&bull;需要自訂程式碼來增加特定資料來源的讀取輸送量|
|離線|[現有的 Mongo 工具（mongodump、mongorestore、Studio3T）](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;易於設定和整合 <br/>&bull;需要適用于節流的自訂處理|
|線上|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;利用 Azure Cosmos DB 大量執行程式程式庫 <br/>&bull;適用于大型資料集，並負責複寫即時變更 <br/>&bull;僅適用于其他 MongoDB 來源|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**遷移類型**|**解決方法**|**考量**|
|---------|---------|---------|
|離線|[cqlsh COPY 命令](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;易於設定 <br/>&bull;不適合大型資料集 <br/>&bull;只有在來源是 Cassandra 資料表時才適用|
|離線|[使用 Spark 複製資料表](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;可以利用 Spark 功能來平行處理轉換和內嵌 <br/>&bull;需要使用自訂重試原則進行設定以處理節流|
|線上|[Striim （從 Oracle DB/Apache Cassandra）](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;適用于許多不同的來源，例如 Oracle、DB2、SQL Server <br/>&bull;輕鬆建立 ETL 管線並提供監視的儀表板 <br/>&bull;支援較大的資料集 <br/>&bull;因為這是協力廠商工具，所以必須從 marketplace 購買並安裝在使用者的環境中。|
|線上|[Blitzz （從 Oracle DB/Apache Cassandra）](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;支援較大的資料集 <br/>&bull;因為這是協力廠商工具，所以必須從 marketplace 購買並安裝在使用者的環境中。|

## <a name="other-apis"></a>其他 API
對於 SQL API、Mongo API 和 Cassandra API 以外的 Api，每個 API 的現有生態系統都支援各種不同的工具。 

**資料表 API** 
* [資料移轉工具](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [圖形大量執行程式程式庫](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解，請嘗試使用[.net](bulk-executor-dot-net.md)和[JAVA](bulk-executor-java.md)中的大量執行程式程式庫。 
* 大量執行程式程式庫已整合到 Cosmos DB Spark 連接器中，若要深入瞭解，請參閱[Azure Cosmos DB Spark 連接器](spark-connector.md)一文。  
* 請洽詢「一般諮詢」問題類型和「大型（TB +）遷移」問題子類型底下的支援票證，以取得 Azure Cosmos DB 產品小組，以取得大規模遷移的其他協助。
