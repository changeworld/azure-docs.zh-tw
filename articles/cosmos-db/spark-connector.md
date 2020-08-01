---
title: 將 Apache Spark 連線至 Azure Cosmos DB
description: 了解可讓您將 Apache Spark 連線到 Azure Cosmos DB 的 Azure Cosmos DB Spark 連接器。
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: ce017d1ac92e3aabe7ad0e36b2e8b87dc04b34f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445935"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>使用 Apache Spark 至 Azure Cosmos DB 連接器來加速巨量資料分析

您可以使用 Cosmos DB Spark 連接器，以 Azure Cosmos DB 中儲存的資料執行[Spark](https://spark.apache.org/)作業。 Cosmos 可用於批次和串流處理，以及做為低延遲存取的服務層。

您可以使用連接器搭配[Azure Databricks](https://azure.microsoft.com/services/databricks)或[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)，這會在 Azure 上提供受控 Spark 叢集。 下表顯示支援的 Spark 版本。

| 元件 | 版本 |
|---------|-------|
| Apache Spark | 2.4. x、2.3. x、2.2 和 2.1. x |
| Scala | 2.11 |
| Azure Databricks 執行階段版本 | > 3.4 |

> [!WARNING]
> 此連接器支援 Azure Cosmos DB 的核心（SQL） API。
> 針對 MongoDB API 的 Cosmos DB，請使用[Mongodb Spark 連接器](https://docs.mongodb.com/spark-connector/master/)。
> 針對 Cosmos DB Cassandra API，請使用[Cassandra Spark 連接器](https://github.com/datastax/spark-cassandra-connector)。
>

## <a name="quickstart"></a>快速入門

* 請遵循[開始使用 JAVA SDK](sql-api-async-java-get-started.md)中的步驟來設定 Cosmos DB 帳戶，並填入一些資料。
* 依照[Azure Databricks 開始](/azure/azure-databricks/quickstart-create-databricks-workspace-portal)設定 Azure Databricks 工作區和叢集中的步驟進行。
* 您現在可以建立新的筆記本，並匯入 Cosmos DB 連接器程式庫。 如需如何設定工作區的詳細資料，請跳至使用[Cosmos DB 連接器](#bk_working_with_connector)。
* 下一節有關于如何使用連接器進行讀取和寫入的程式碼片段。

### <a name="batch-reads-from-cosmos-db"></a>批次讀取 Cosmos DB

下列程式碼片段顯示如何建立 Spark 資料框架，以從 PySpark 中的 Cosmos DB 讀取。

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

和 Scala 中的相同程式碼片段：

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>批次寫入至 Cosmos DB

下列程式碼片段顯示如何在 PySpark 中撰寫資料框架，以 Cosmos DB。

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

和 Scala 中的相同程式碼片段：

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>從 Cosmos DB 進行串流讀取

下列程式碼片段示範如何連接到 Azure Cosmos DB 變更摘要並從中讀取。

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
和 Scala 中的相同程式碼片段：

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>串流寫入至 Cosmos DB

下列程式碼片段顯示如何在 PySpark 中撰寫資料框架，以 Cosmos DB。

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

和 Scala 中的相同程式碼片段：

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
更多程式碼片段和端對端範例，請參閱[Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)。

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>使用連接器

您可以從 GitHub 中的來源建立連接器，或從下列連結中的 Maven 下載 uber jar。

| Spark | Scala | 最新版本 |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2. 3.0 _ 2.11 _ 1.3。3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2 2.0 _ 2.11 _ 1.1。1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2. 1.0 _ 2.11 _ 1.2。2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>使用 Databricks 筆記本

遵循 Azure Databricks 指南 >[使用 Azure Cosmos DB Spark 連接器](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)中的指導方針，使用您的 Databricks 工作區建立程式庫

> [!NOTE]
> [**使用 Azure Cosmos DB Spark 連接器**] 頁面目前不是最新狀態。 您可以從[azure-cosmosdb spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)的 maven 下載 uber jar，而不是將六個個別的 jar 下載成六個不同的程式庫，並安裝此 jar/程式庫。
> 

### <a name="using-spark-cli"></a>使用 spark-cli

若要使用 spark cli （也就是、、）來處理 `spark-shell` 連接器 `pyspark` `spark-submit` ，您可以使用 `--packages` 參數搭配連接器的[maven 座標](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)。

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>使用 Jupyter 筆記本

如果您是在 HDInsight 中使用 Jupyter 筆記本，您可以使用 spark-魔術資料 `%%configure` 格來指定連接器的 maven 座標。

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> 請注意，包含 `spark.jars.excludes` 是為了移除連接器、Apache Spark 和 Livy 之間的潛在衝突。

### <a name="build-the-connector"></a>建立連接器

目前，此連接器專案會使用它 `maven` 來建立沒有相依性的，您可以執行：

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>使用我們的範例

[Cosmos DB Spark GitHub 存放庫](https://github.com/Azure/azure-cosmosdb-spark)包含下列可供您嘗試的範例筆記本和腳本。

* **Spark 和 Cosmos DB （西雅圖） ipynb html 的即時航班效能** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)  |  [ ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)：使用 HDInsight Jupyter 筆記本服務將 spark 連線到 Cosmos DB，以展示 spark SQL、GraphFrames，以及使用 ML 管線預測航班延遲。
* **具有 Apache Spark 和 Azure Cosmos DB 變更摘要的 Twitter 來源**： [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **使用 Apache Spark 來查詢 Cosmos DB 圖形**： [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* 使用**[將 Azure Databricks 連接到 Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** `azure-cosmosdb-spark` 。  這裡連結的也是 Azure Databricks 版本的[即時航班效能筆記本](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)。
* **[具有 Azure Cosmos DB 和 HDInsight 的 Lambda 架構（Apache Spark）](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**：您可以使用 Cosmos DB 和 Spark，降低維護 big data 管線的操作額外負荷。

## <a name="more-information"></a>相關資訊

Wiki 中有更多資訊， `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki)包括：

* [Azure Cosmos DB Spark 連接器使用者指南](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [匯總範例](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>組態和設定

* [Spark 連接器設定](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark 至 Cosmos DB 連接器設定](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)（進行中）
* [透過 Apache Spark 設定 Power BI 直接查詢 Azure Cosmos DB （HDI）](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>疑難排解

* [使用 Cosmos DB 匯總](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [已知問題](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>效能

* [效能祕訣](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [查詢測試回合](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [撰寫測試回合](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>變更摘要

* [使用 Azure Cosmos DB 變更摘要和 Apache Spark 來串流處理變更](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [變更摘要示範](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [結構化串流示範](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>監視

* [使用 application insights 監視 Spark 作業](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>後續步驟

如果您還沒這麼做，請從 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 存放庫下載 Spark 至 Azure Cosmos DB 連接器。 探索存放庫中的下列額外資源：

* [彙總範例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [範例腳本和筆記本](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

您也可以檢閱 [Apache Spark SQL、DataFrame 和 Dataset 指南](https://spark.apache.org/docs/latest/sql-programming-guide.html) (英文) 和 [Azure HDInsight 上的 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 文章。
