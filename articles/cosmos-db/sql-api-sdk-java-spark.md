---
title: 適用于 SQL API 版本資訊和資源的 Cosmos DB Apache Spark 連接器
description: 瞭解 SQL API 的 Azure Cosmos DB Apache Spark 連接器，包括發行日期、停用日期，以及 Azure Cosmos DB SQL Async JAVA SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4ee7e180094c1584995e5d734adbe589a112754b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477567"
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
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 連接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 資源提供者](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [大量執行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式 - Java](sql-api-sdk-bulk-executor-java.md)

您可以使用適用于 Core (SQL) 的 Azure Cosmos DB Apache Spark Connector，加速進行大型資料分析。 Spark 連接器可讓您對儲存在 Azure Cosmos DB 中的資料執行 [spark](https://spark.apache.org/) 作業。 支援批次和資料流程處理。

您可以使用連接器搭配 [Azure Databricks](https://azure.microsoft.com/services/databricks) 或 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)，以在 Azure 上提供受控 Spark 叢集。 下表顯示支援的版本：

| 元件 | 版本 |
|---------|-------|
| Apache Spark | 2.4.*x*、2.3。*x*、2.2。*x*和2.1。*x* |
| Scala | 2.11 |
| Azure Databricks (執行階段版本)  | 晚于3。4 |

> [!WARNING]
> 此連接器支援 Azure Cosmos DB 的 core (SQL) API。
> 針對適用于 MongoDB 的 Cosmos DB API，請使用 [適用于 Spark 的 MongoDB 連接器](https://docs.mongodb.com/spark-connector/master/)。
> 針對 Cosmos DB Cassandra API，請使用 [Cassandra Spark 連接器](https://github.com/datastax/spark-cassandra-connector)。
>

## <a name="resources"></a>資源

| 資源 | 連結 |
|---|---|
| **SDK 下載** | [下載最新的 .jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)、 [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**API 文件** | [Spark 連接器參考]() |
|**參與 SDK** | [GitHub 上 Apache Spark 的 Azure Cosmos DB 連接器](https://github.com/Azure/azure-cosmosdb-spark) | 
|**開始使用** | [使用 Apache Spark 至 Azure Cosmos DB 連接器來加速巨量資料分析](./spark-connector.md#bk_working_with_connector) <br> [搭配 Apache Kafka 和 Azure Cosmos DB 使用 Apache Spark 結構化串流](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>版本歷程記錄

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>新功能
- 加入新的設定選項， `changefeedstartfromdatetime` 可用來指定 changefeed 應該處理的開始時間。 如需詳細資訊，請參閱 [配置選項](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)。

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>重要 bug 修正
- 修正針對大型結果集的執行程式造成過多記憶體耗用量的回歸 (例如，有數百萬個數據列) ，最後會導致錯誤 `java.lang.OutOfMemoryError: GC overhead limit exceeded` 。

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正資料流程檢查點邊緣案例，其中包含已套用設定的 `ID` 管道字元 (|) `ChangeFeedMaxPagesPerBatch` 。

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>新功能
* 當使用嵌套分割區索引鍵時，新增大量更新的支援。
* 在寫入至 Azure Cosmos DB 時，新增 Decimal 和 Float 資料類型的支援。
* 當使用 Long (Unix epoch) 作為值時，新增時間戳記類型的支援。

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正使用設定時所發生的轉換例外狀況 `WriteThroughputBudget` 。

### <a name="307"></a>3.0.7 版
#### <a name="new-features"></a>新功能
* 將大量失敗的錯誤資訊新增至例外狀況和記錄檔。

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正串流檢查點問題。

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 若要減少雜訊，請修正錯誤的訊息記錄層級，並在發生層級錯誤的情況下保留。

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正資料分割分割期間結構化串流中的 bug。 Bug 可能會導致某些遺漏的變更摘要記錄，或檢查點寫入的 Null 例外狀況。

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正導致針對 readStream 提供的自訂架構被忽略的 bug。

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正回歸 (unshaded JAR 包含所有陰影相依性) 將組建時間增加50%。

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>重要 bug 修正
* 修正導致透過 TCP 直接傳輸失敗的相依性問題 RequestTimeoutException。

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>新功能
* 改善連接管理和連接共用，以減少中繼資料呼叫的數目。

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。

深入了解 [Apache Spark](https://spark.apache.org/) \(英文\)。
