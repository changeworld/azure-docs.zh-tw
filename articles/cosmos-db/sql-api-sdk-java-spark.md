---
title: 適用于 SQL API 版本資訊和資源的 Azure Cosmos DB Apache Spark 連接器
description: 深入瞭解適用于 SQL API 的 Azure Cosmos DB Apache Spark 連接器，包括發行日期、停用日期，以及 Azure Cosmos DB SQL 非同步 JAVA SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 72b3b190492be5cec9986729875c5b09e2559ae5
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854213"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>適用于 Core (SQL) API 的 Azure Cosmos DB Apache Spark 連接器：版本資訊與資源
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 變更摘要 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [非同步 Java SDK v2](sql-api-sdk-async-java.md)
> * [同步 Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Spark 連接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 資源提供者](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [大量執行程式-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式 - Java](sql-api-sdk-bulk-executor-java.md)

使用適用于核心 (SQL) 的 Azure Cosmos DB Apache Spark 連接器，加速 big data 分析。 Spark 連接器可讓您對儲存在 Azure Cosmos DB 中的資料執行[spark](https://spark.apache.org/)作業。 支援批次和串流處理。

您可以使用連接器搭配[Azure Databricks](https://azure.microsoft.com/services/databricks)或[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)，這會在 Azure 上提供受控 Spark 叢集。 下表顯示支援的 Spark 版本。

| 元件 | 版本 |
|---------|-------|
| Apache Spark | 2.4. x、2.3. x、2.2 和 2.1. x |
| Scala | 2.11 |
| Azure Databricks 執行階段版本 | > 3.4 |

> [!WARNING]
> 此連接器支援 Azure Cosmos DB 的核心 (SQL) API。
> 針對 MongoDB API 的 Cosmos DB，請使用[Mongodb Spark 連接器](https://docs.mongodb.com/spark-connector/master/)。
> 針對 Cosmos DB Cassandra API，請使用[Cassandra Spark 連接器](https://github.com/datastax/spark-cassandra-connector)。
>

## <a name="helpful-content"></a>有用的內容

| 內容 | 連結 |
|---|---|
| **SDK 下載** | [從 Apache Spark 下載](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**API 文件** | [Spark 連接器參考]() |
|**參與 SDK** | [GitHub 上適用于 Apache Spark 的 Azure Cosmos DB 連接器](https://github.com/Azure/azure-cosmosdb-spark) | 
|**開始使用** | [使用 Apache Spark 至 Azure Cosmos DB 連接器來加速巨量資料分析](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [搭配 Apache Kafka 和 Azure Cosmos DB 使用 Apache Spark 結構化串流](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>版本歷程記錄

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 修正「識別碼」中的資料流程檢查點邊緣案例，其中包含已套用 "ChangeFeedMaxPagesPerBatch" 設定的 "|" 字元

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>新功能
* 新增使用嵌套分割區索引鍵時的大量更新支援
* 在寫入 Cosmos DB 期間，新增 Decimal 和 Float 資料類型的支援。
* 當時間戳類型使用 Long (unix Epoch) 做為值時，新增其支援
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 修正使用 "WriteThroughputBudget" 設定時的類型轉換例外狀況。

### <a name="307"></a>3.0.7 版
#### <a name="new-features"></a>新功能
* 將大量失敗的錯誤資訊新增至例外狀況和記錄。
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 修正串流檢查點問題。

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 修正錯誤訊息的記錄層級，並以不小心的等級錯誤來減少雜訊

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 修正分割區分割期間的結構化串流中的 bug-可能導致遺失某些變更摘要記錄，或發現檢查點寫入的 Null 例外狀況

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 修正為 readStream 提供的自訂架構已忽略的 bug

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 修正回歸 (unshaded JAR 包含所有陰影相依性) 這會增加50% 的組建時間

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>新功能
#### <a name="key-bug-fixes"></a>金鑰錯誤修正
* 修正相依性問題，導致直接傳輸透過 TCP 失敗並出現 RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>新功能
* 改善連接管理和連接共用，以減少中繼資料呼叫的次數
#### <a name="key-bug-fixes"></a>金鑰錯誤修正

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟

若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

若要深入瞭解 Apache Spark，請參閱[首頁](https://spark.apache.org/)。