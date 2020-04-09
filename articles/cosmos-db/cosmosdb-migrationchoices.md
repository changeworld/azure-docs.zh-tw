---
title: 宇宙資料庫移轉選項
description: 此文件介紹將本地或雲端資料移到 Azure Cosmos DB 的各種選項
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 34698a215477abdd7d68c3dfe050657ecf049690
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984890"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>將本地或雲端資料移到 Azure Cosmos DB 的選項

您可以將資料從各種資料源載入Azure Cosmos DB。 此外,由於 Azure Cosmos DB 支援多個 API,因此目標可以是任何現有的 API。 為了支援從各種源到不同 Azure Cosmos DB API 的遷移路徑,有多個解決方案為每個遷移路徑提供專門處理。 本文檔列出了可用的解決方案,並描述了它們的優點和局限性。

## <a name="factors-affecting-the-choice-of-migration-tool"></a>影響移轉工具選擇的因素

以下因素決定了移轉工具的選擇:
* **線上移移與離線遷移**:許多遷移工具提供僅執行一次性遷移的路徑。 這意味著訪問資料庫的應用程式可能會遇到一段時間的停機時間。 某些遷移解決方案提供了一種在源和目標之間設置了複製管道的情況下執行即時遷移的方法。

* **數據源**:現有數據可以位於各種資料來源中,如 Oracle DB2、Datatax Cassanda、Azure SQL Server、PostgreSQL 等。數據也可以位於現有的 Azure Cosmos DB 帳戶中,遷移的目的可能是更改數據模型或使用不同的分區鍵重新分區容器中的數據。

* **Azure Cosmos DB API**:對於 Azure Cosmos DB 中的 SQL API,Azure Cosmos DB 團隊開發了多種工具,這些工具有助於處理不同的遷移方案。 所有其他 API 都有其自己的專業工具集,由社區開發和維護。 由於 Azure Cosmos DB 在有線協定等級支援這些 API,因此這些工具在將數據遷移到 Azure Cosmos DB 時也應以現在的身份工作。 但是,它們可能需要自定義處理節流,因為此概念特定於 Azure Cosmos DB。

* **數據的大小**:大多數遷移工具對於較小的數據集非常有效。 當數據集超過幾百 GB 時,遷移工具的選擇是有限的。 

* **預期的遷移持續時間**:可以將遷移配置為以緩慢、增量的速度進行,消耗更少的輸送量,或者可以使用目標 Azure Cosmos DB 容器上預配的整個輸送量,並在更短的時間內完成遷移。

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**遷移類型**|**解決方法**|**考量**|
|---------|---------|---------|
|離線|[資料移轉工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;易於設定及支援多個來源 <br/>&bull;不適合大型資料集|
|離線|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;易於設定及支援多個來源 <br/>&bull;使用 Azure 宇宙資料庫大容量執行器庫 <br/>&bull;調整到一個資料集 <br/>&bull;缺乏檢查點 ─ 這意味著,如果在遷移過程中出現問題,則需要重新啟動整個遷移過程<br/>&bull;缺少死信佇列 ─ 這意味著一些錯誤的檔可能會停止整個遷移過程。|
|離線|[Azure 宇宙 DB 火花連接器](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;使用 Azure 宇宙資料庫大容量執行器庫 <br/>&bull;調整到一個資料集 <br/>&bull;需要自訂 Spark 設定 <br/>&bull;Spark 對架構不一致很敏感,這可能是遷移期間的問題 |
|離線|[具有 Cosmos DB 批次執行器庫的自訂工具](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;提供檢查點、死字母功能,可提高遷移恢復能力 <br/>&bull;適用於非常大的資料集(10 TB+)  <br/>&bull;需要自訂此工具作為應用服務執行 |
|線上|[宇宙資料庫功能和變更來源 API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;易於設定 <br/>&bull;只有原始來源是 Azure Cosmos DB 容器時才有效 <br/>&bull;不適合大型資料集 <br/>&bull;不捕捉從來源容器中移除的 |
|線上|[使用變更來源的自訂移轉服務](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;提供進度追蹤 <br/>&bull;只有原始來源是 Azure Cosmos DB 容器時才有效 <br/>&bull;也適用於較大的資料集 <br/>&bull;要求使用者設定應用服務以承載變更來源處理器 <br/>&bull;不捕捉從來源容器中移除的|
|線上|[斯特裡伊姆](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;適用於各種來源,如 Oracle、DB2、SQL Server <br/>&bull;易於建構 ETL 管道,並提供用於監視的儀表板 <br/>&bull;支援更大的資料集 <br/>&bull;由於這是第三方工具,因此需要從市場購買並安裝在使用者環境中|

## <a name="azure-cosmos-db-mongo-api"></a>Azure 宇宙 DB 蒙戈 API
|**遷移類型**|**解決方法**|**考量**|
|---------|---------|---------|
|離線|[資料移轉工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;易於設定及支援多個來源 <br/>&bull;不適合大型資料集|
|離線|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;易於設定及支援多個來源 <br/>&bull;使用 Azure 宇宙資料庫大容量執行器庫 <br/>&bull;調整到一個資料集 <br/>&bull;缺乏檢查點意味著移轉過程中的任何問題都需要重新啟動整個遷移過程<br/>&bull;缺少死信佇列意味著一些錯誤的檔案可能會停止整個遷移過程 <br/>&bull;需要自訂代碼來增加某些資料來源的讀取量|
|離線|[現有蒙戈工具(蒙戈轉儲、mongorestore、Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;易於設定和整合 <br/>&bull;需要自訂油門處理|
|線上|[Azure 資料庫移轉服務](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;使用 Azure 宇宙資料庫大容量執行器庫 <br/>&bull;適合大型資料集,並負責複製即時變更 <br/>&bull;只適用於其他蒙高DB源|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**遷移類型**|**解決方法**|**考量**|
|---------|---------|---------|
|離線|[cqlsh COPY 命令](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;易於設定 <br/>&bull;不適合大型資料集 <br/>&bull;只當源是 Cassandra 時才起作用|
|離線|[使用 Spark 複製表](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;可以利用 Spark 功能並行化轉換和引入 <br/>&bull;需要設定自訂重試原則來處理限制|
|線上|[斯特裡姆(來自甲骨文資料庫/阿帕奇卡桑德拉)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;適用於各種來源,如 Oracle、DB2、SQL Server <br/>&bull;易於建構 ETL 管道,並提供用於監視的儀表板 <br/>&bull;支援更大的資料集 <br/>&bull;由於這是第三方工具,因此需要從市場購買並安裝在使用者環境中|
|線上|[布利茨(來自甲骨文資料庫/阿帕奇卡桑德拉)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;支援更大的資料集 <br/>&bull;由於這是第三方工具,因此需要從市場購買並安裝在使用者環境中|

## <a name="other-apis"></a>其他 API
對於 SQL API、Mongo API 和 Cassandra API 以外的 API,每個 API 的現有生態系統都支援各種工具。 

**資料表 API** 
* [資料移轉工具](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [阿茲比貝](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [圖像批次批次執行器庫](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [格雷姆林火花](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>後續步驟

* 通過嘗試在[.NET](bulk-executor-dot-net.md)和[Java](bulk-executor-java.md)中使用批量執行者庫的範例應用程式,瞭解更多資訊。 
* 批量執行器庫整合到Cosmos DB Spark連接器中,有關詳細資訊,請參閱[Azure Cosmos DB Spark連接器](spark-connector.md)一文。  
* 通過在「一般諮詢」問題類型和「大型(TB+) 遷移「問題子類型下打開支援票證,與 Azure Cosmos DB 產品團隊聯繫,以獲取有關大規模遷移的其他説明。
