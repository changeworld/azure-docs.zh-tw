---
title: 使用 Spark 讀取 Cassandra API 資料表資料
titleSufix: Azure Cosmos DB
description: 本文描述如何從 Azure Cosmos DB 中的 Cassandra API 資料表讀取資料。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: 4ecb7758ee5f58345fccc2c490cee4d23043a20c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85257409"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>使用 Spark 從 Azure Cosmos DB Cassandra API 資料表中讀取資料

 本文描述如何從 Spark 讀取 Azure Cosmos DB Cassandra API 中儲存的資料。

## <a name="cassandra-api-configuration"></a>Cassandra API 設定
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Dataframe API

### <a name="read-table-using-sessionreadformat-command"></a>使用 session.read.format 命令讀取資料表

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>使用 spark.read.cassandraFormat 讀取資料表 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>讀取資料表中的特定資料行

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>套用篩選

您可以將述詞向下推送至資料庫，以允許更佳的優化 Spark 查詢。 述詞是傳回 true 或 false 之查詢的條件，通常位於 WHERE 子句中。 述詞下推會篩選資料庫查詢中的資料，減少從資料庫中抓取的專案數，並改善查詢效能。 根據預設，Spark 資料集 API 會將有效的 WHERE 子句自動推送至資料庫。 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

實體計畫的 PushedFilters 區段包含 GreaterThan 向下推篩選。 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="資料分割":::

## <a name="rdd-api"></a>RDD API

### <a name="read-table"></a>讀取資料表
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>讀取資料表中的特定資料行

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL 檢視 

### <a name="create-a-temporary-view-from-a-dataframe"></a>從資料框架建立暫存的檢視

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>針對檢視執行查詢

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>後續步驟

從 Spark 使用 Azure Cosmos DB Cassandra API 的其他文章如下：
 
 * [作業](cassandra-spark-upsert-ops.md)
 * [刪除作業](cassandra-spark-delete-ops.md)
 * [彙總作業](cassandra-spark-aggregation-ops.md)
 * [資料表複製作業](cassandra-spark-table-copy-ops.md)

