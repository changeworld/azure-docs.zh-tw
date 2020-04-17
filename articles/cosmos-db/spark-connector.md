---
title: 將 Apache Spark 連線至 Azure Cosmos DB
description: 了解可讓您將 Apache Spark 連線到 Azure Cosmos DB 的 Azure Cosmos DB Spark 連接器。
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: edfaf50b701f64b12f9cf5fcc9ab8d2c6d241d0a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482184"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>使用 Apache Spark 至 Azure Cosmos DB 連接器來加速巨量資料分析

您可以使用Cosmos DB Spark連接器使用儲存在Azure Cosmos DB中的數據執行[Spark](https://spark.apache.org/)作業。 Cosmos 可用於批處理和流處理,並作為低延遲訪問的服務層。

您可以將連接器與 Azure[資料塊](https://azure.microsoft.com/services/databricks)或[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)一起使用 ,後者在 Azure 上提供託管 Spark 群集。 下表顯示了支援的Spark版本。

| 元件 | 版本 |
|---------|-------|
| Apache Spark | 2.4.x、2.3.x、2.2.x 和 2.1.x |
| Scala | 2.11 |
| Azure Databricks 執行階段版本 | > 3.4 |

> [!WARNING]
> 此連接器支援 Azure Cosmos DB 的核心 (SQL) API。
> 對於蒙戈DB API 的宇宙 DB,請使用[MongoDB Spark 連接器](https://docs.mongodb.com/spark-connector/master/)。
> 對於宇宙 DB 卡桑德拉 API,請使用[卡桑德拉火花連接器](https://github.com/datastax/spark-cassandra-connector)。
>

## <a name="quickstart"></a>快速入門

* 按照 Java [SDK 入門](sql-api-async-java-get-started.md)的步驟設置 Cosmos DB 帳戶,並填充一些數據。
* 按照 Azure[資料磚塊入門](/azure/azure-databricks/quickstart-create-databricks-workspace-portal)的步驟來設置 Azure 數據塊工作區和群集。
* 您現在可以創建新的筆記本,並導入Cosmos DB連接器庫。 跳到[使用 Cosmos DB 連接器](#bk_working_with_connector),詳細瞭解如何設定工作區。
* 以下部分包含有關如何使用連接器讀取和寫入的代碼段。

### <a name="batch-reads-from-cosmos-db"></a>從宇宙 DB 進行批次處理讀取

以下代碼段演示如何創建 Spark 數據框架,以便從 PySpark 中的 Cosmos DB 讀取。

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

Scala 的同代碼:

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

### <a name="batch-writes-to-cosmos-db"></a>批次寫入 Cosmos DB

以下代碼段演示如何在 PySpark 中將數據框寫入 Cosmos DB。

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
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

Scala 的同代碼:

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

### <a name="streaming-reads-from-cosmos-db"></a>從宇宙 DB 串流式傳輸讀取

以下代碼段演示如何連接到 Azure Cosmos DB 更改源並將其讀取。

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
Scala 的同代碼:

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

### <a name="streaming-writes-to-cosmos-db"></a>串流式傳輸寫入 Cosmos DB

以下代碼段演示如何在 PySpark 中將數據框寫入 Cosmos DB。

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

Scala 的同代碼:

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
更多片段和端點向端範例,請參閱[Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)。

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>使用連接器

您可以在 GitHub 中的來源中建構連接器,或在下面的連結中從 Maven 下載優步罐。

| Spark | Scala | 最新版本 |
|---|---|---|
| 2.4.0 | 2.11 | [azure-宇宙-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-宇宙-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-宇宙db-spark_2.2.0*2.11_1.1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-宇宙db-spark_2.1.0_2.11_1.2.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>使用資料磚筆記本

依 Azure 資料磚塊指南中的指南>[使用 Azure Cosmos DB Spark 連接器](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)「中的指南」建立使用 Databricks 工作區的庫

> [!NOTE]
> 請注意,使用**Azure 宇宙 DB 火花連接器「** 頁目前不是最新的」。 而不是將六個單獨的罐子下載到六個不同的庫中,你可以從 mavenhttps://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)下載 優步罐,並安裝這個 jar/庫。
> 

### <a name="using-spark-cli"></a>使用火花-cli

要使用火花-cli(即`spark-shell``pyspark``spark-submit`,、、) 使用連接器,`--packages`可以將 參數與連接器的[maven 座標](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)一起使用。

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>使用朱派筆記本

如果您在 HDInsight 中使用 Jupyter 筆記本,則`%%configure`可以使用火花魔法 單元格來指定連接器的 maven 座標。

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> 請注意,包含`spark.jars.excludes`是用於消除連接器、Apache Spark 和 Livy 之間潛在衝突的特定內容。

### <a name="build-the-connector"></a>建置連接器

目前,此連接器專案使用`maven`這種產生來生成,無需依賴項,您可以執行:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>使用我們的樣品

[Cosmos DB Spark GitHub 儲存庫](https://github.com/Azure/azure-cosmosdb-spark)具有以下範例筆記本和腳本,您可以嘗試這些筆記本和腳本。

* **即時飛行性能與火花和宇宙DB(西雅圖**[)ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html:](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)使用HDInsight Jupyter筆記本服務連接Sparks DB,以展示Spark SQL,圖形幀,並使用ML管道預測航班延誤。
* **推特來源與阿帕契火花和Azure宇宙DB更改飼料**[:ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **使用 Apache Spark 查詢宇宙 DB 圖形**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* 使用`azure-cosmosdb-spark`**[Azure 資料塊連線到 Azure 宇宙資料庫](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**。  此處連結的還有即時[飛行性能筆記本](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)的 Azure 數據磚塊版本。
* **[使用 Azure Cosmos DB 和 HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** 的 Lambda 體系結構:您可以使用 Cosmos DB 和 Spark 減少維護大數據管道的操作開銷。

## <a name="more-information"></a>相關資訊

我們在`azure-cosmosdb-spark`[wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki)中擁有更多資訊,包括:

* [Azure 宇宙 DB 火花連接器使用者指南](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [彙總範例](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>組態和設定

* [火花連接器設定](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [火花到宇宙 DB 連接器設定](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)(正在進行中)
* [透過 Apache Spark (HDI) 將 Power BI 直接查詢設定為 Azure 宇宙資料庫](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>疑難排解

* [使用宇宙資料庫集合模式](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [已知問題](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>效能

* [效能提示](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [查詢測試執行](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [寫入測試執行](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>變更摘要

* [使用 Azure 宇宙 DB 變更來源與 Apache Spark 進行流處理變更](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [變更來源](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [結構化流演示](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>監視

* [使用應用程式見解監視 Spark 工作](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>後續步驟

如果您還沒這麼做，請從 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 存放庫下載 Spark 至 Azure Cosmos DB 連接器。 探索存放庫中的下列額外資源：

* [彙總範例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [範例文稿與筆記本](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

您也可以檢閱 [Apache Spark SQL、DataFrame 和 Dataset 指南](https://spark.apache.org/docs/latest/sql-programming-guide.html) (英文) 和 [Azure HDInsight 上的 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 文章。
