---
title: 適用于 SQL API 版本資訊和資源的 Azure Cosmos DB Apache Spark 連接器
description: 深入瞭解 SQL API 的 Azure Cosmos DB Apache Spark Connector，包括發行日期、停用日期，以及 Azure Cosmos DB SQL Async JAVA SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 46ddbd18051ffa44232468704ce189d4171b50e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590004"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>適用于 Core (SQL) API 的 Azure Cosmos DB Apache Spark 連接器：版本資訊和資源
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 變更摘要 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [非同步 Java SDK v2](sql-api-sdk-async-java.md)
> * [同步 Java SDK v2](sql-api-sdk-java.md)
> * [春季資料 v2](sql-api-sdk-java-spring-v2.md)
> * [春季資料 v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 連接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 資源提供者](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [大量執行程式-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式 - Java](sql-api-sdk-bulk-executor-java.md)

使用適用于 Core (SQL) 的 Azure Cosmos DB Apache Spark Connector 來加速 big data analytics。 Spark 連接器可讓您對儲存在 Azure Cosmos DB 中的資料執行 [spark ](https://spark.apache.org/) 作業。 支援批次和資料流程處理。

您可以使用連接器搭配 [Azure Databricks](https://azure.microsoft.com/services/databricks) 或 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)，以在 Azure 上提供受控 Spark 叢集。 下表顯示支援的 Spark 版本。

| 元件 | 版本 |
|---------|-------|
| Apache Spark | 2.4. x、2.3. x、2.2. x 和 2.1. x |
| Scala | 2.11 |
| Azure Databricks 執行階段版本 | > 3.4 |

> [!WARNING]
> 此連接器支援 Azure Cosmos DB 的 core (SQL) API。
> 針對 MongoDB API 的 Cosmos DB，請使用 [Mongodb Spark 連接器](https://docs.mongodb.com/spark-connector/master/)。
> 針對 Cosmos DB Cassandra API，請使用 [Cassandra Spark 連接器](https://github.com/datastax/spark-cassandra-connector)。
>

## <a name="helpful-content"></a>有用的內容

| 內容 | 連結 |
|---|---|
| **SDK 下載** | [從 Apache Spark 下載](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**API 文件** | [Spark 連接器參考]() |
|**參與 SDK** | [GitHub 上 Apache Spark 的 Azure Cosmos DB 連接器](https://github.com/Azure/azure-cosmosdb-spark) | 
|**開始使用** | [使用 Apache Spark 至 Azure Cosmos DB 連接器來加速巨量資料分析](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [搭配 Apache Kafka 和 Azure Cosmos DB 使用 Apache Spark 結構化串流](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>版本歷程記錄

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正「識別碼」中包含已套用 "ChangeFeedMaxPagesPerBatch" 設定之 "|" 字元的串流檢查點邊緣案例

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>新功能
* 新增使用嵌套分割區索引鍵時的大量更新支援
* 在寫入至 Cosmos DB 時，新增 Decimal 和 Float 資料類型的支援。
* 當使用 Long (unix Epoch) 作為值時，新增時間戳記類型的支援
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正使用 "WriteThroughputBudget" 設定時的類型轉換例外狀況。

### <a name="307"></a>3.0.7 版
#### <a name="new-features"></a>新功能
* 將大量失敗的錯誤資訊新增至例外狀況和記錄檔。
#### <a name="key-bug-fixes"></a>重要 bug 修正

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正串流檢查點問題。

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正不慎遺留的訊息記錄層級錯誤，以降低雜訊

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 在分割區分割期間修正結構化串流中的 bug-可能會導致遺失某些變更摘要記錄，或查看檢查點寫入的 Null 例外狀況

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正針對 readStream 提供的自訂架構被忽略的 bug

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正回歸 (unshaded JAR 包含所有陰影相依性) 將組建時間增加50%

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正相依性問題，導致透過 TCP 的直接傳輸失敗並產生 RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>新功能
* 改善連接管理和連接共用，以減少中繼資料呼叫的數目
#### <a name="key-bug-fixes"></a>重要 bug 修正

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟

若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

若要深入瞭解 Apache Spark，請參閱 [首頁](https://spark.apache.org/)。