---
title: 概述如何在適用於 Azure Synapse Analytics 的 Apache Spark 中使用 Linux Foundation Delta Lake
description: 了解如何在適用於 Azure Synapse Analytics 的 Apache Spark 中使用 Delta Lake，以建立及使用具有 ACID 屬性的資料表。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 655daeb0149228d78d5288b0e5d0d705a5743d28
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89008636"
---
# <a name="linux-foundation-delta-lake-overview"></a>Linux Foundation Delta Lake 概觀

為了讓內容更為明確，本文已進行過調整，而與其位於[此處](https://docs.delta.io/latest/quick-start.html)的原始對應文章不同。 本文可協助您快速探索 [Delta Lake](https://delta.io) 的主要功能。 本文提供的程式碼片段會示範如何透過互動式查詢、批次查詢和串流查詢在 Delta Lake 資料表讀取和寫入資料。 這些程式碼片段也會以一組筆記本 [PySpark 在此](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb)、[Scala 在此](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb)和 [C# 在此](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb)的形式來提供

我們將討論以下內容：

* 建立資料表
* 讀取資料
* 更新資料表資料
* 覆寫資料表資料
* 不覆寫的條件式更新
* 使用時間移動來讀取較舊版本的資料
* 將資料串流寫入到資料表
* 從資料表讀取變更串流
* SQL 支援

## <a name="configuration"></a>組態

請務必針對您的環境適當地修改下列內容。

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

產生結果：

'/delta/delta-table-335323'

## <a name="create-a-table"></a>建立資料表

若要建立 Delta Lake 資料表，請以差異格式撰寫資料框架來剔除資料框架。 您可以將格式從 Parquet、CSV、JSON 等變更為差異格式。

隨後的程式碼示範如何使用從資料框架推斷而來的結構描述，來建立新的 Delta Lake 資料表。

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

產生結果：

| 識別碼|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>讀取資料

您可以藉由指定檔案的路徑和差異格式，來讀取 Delta Lake 資料表中的資料。

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

產生結果：

| ID|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

結果的順序與上方不同，因為在輸出結果之前並未明確指定順序。

## <a name="update-table-data"></a>更新資料表資料

Delta Lake 支援數個使用標準資料框架 API 來修改資料表的作業，這是差異格式所新增的其中一項重要增強功能。 下列範例會執行批次作業，以覆寫資料表中的資料。

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

產生結果：

| 識別碼|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

在這裡，您會看到五筆記錄全都已經更新來保留新的值。

## <a name="save-as-catalog-tables"></a>另存為目錄資料表

Delta Lake 可以寫入到受控或外部目錄資料表。

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

產生結果：

|[資料庫]|         tableName|isTemporary|
|--------|------------------|-----------|
| default|externaldeltatable|      false|
| default| manageddeltatable|      false|

透過此程式碼，您已從現有資料框架 (稱為受控資料表) 在目錄中建立新的資料表。 然後，您已在目錄中定義了使用現有位置的新外部資料表 (稱為外部資料表)。 您會在輸出中看到這兩個資料表 (不論其建立方式為何) 都列在目錄中。

現在您可以查看這兩個資料表的擴充屬性

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

產生結果：

|col_name                    |data_type                                                                                                    |comment|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |BIGINT                                                                                                       |null   |
|                            |                                                                                                             |       |
|詳細的資料表資訊  |                                                                                                             |       |
|資料庫                    |default                                                                                                      |       |
|資料表                       |manageddeltatable                                                                                            |       |
|擁有者                       |trusted-service-user                                                                                         |       |
|建立時間                |Sat Apr 25 00:35:34 UTC 2020                                                                                 |       |
|上次存取                 |Thu Jan 01 00:00:00 UTC 1970                                                                                 |       |
|建立者                  |Spark 2.4.4.2.6.99.201-11401300                                                                              |       |
|類型                        |MANAGED                                                                                                      |       |
|提供者                    |delta                                                                                                        |       |
|資料表屬性            |[transient_lastDdlTime=1587774934]                                                                           |       |
|統計資料                  |2407 個位元組                                                                                                   |       |
|Location                    |abfss://data@<data lake>.dfs.core.windows.net/synapse/workspaces/<workspace name>/warehouse/manageddeltatable|       |
|Serde 程式庫               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                                                           |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                                                             |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat                                                    |       |
|儲存體屬性          |[serialization.format=1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

產生結果：

|col_name                    |data_type                                                             |comment|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |BIGINT                                                                |null   |
|                            |                                                                      |       |
|詳細的資料表資訊  |                                                                      |       |
|資料庫                    |default                                                               |       |
|資料表                       |externaldeltatable                                                    |       |
|擁有者                       |trusted-service-user                                                  |       |
|建立時間                |Sat Apr 25 00:35:38 UTC 2020                                          |       |
|上次存取                 |Thu Jan 01 00:00:00 UTC 1970                                          |       |
|建立者                  |Spark 2.4.4.2.6.99.201-11401300                                       |       |
|類型                        |EXTERNAL                                                              |       |
|提供者                    |DELTA                                                                 |       |
|資料表屬性            |[transient_lastDdlTime=1587774938]                                    |       |
|Location                    |abfss://data@<data lake>.dfs.core.windows.net/delta/delta-table-587152|       |
|Serde 程式庫               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                    |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                      |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat             |       |
|儲存體屬性          |[serialization.format=1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>不覆寫的條件式更新

Delta Lake 會提供程式設計 API，以進行條件式更新、刪除和合併 (這通常稱為 upsert) 資料到資料表中。

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

產生結果：

| 識別碼|
|---|
|106|
|108|
|  5|
|  7|
|  9|

在這裡，您剛剛新增了 100 到每個偶數識別碼。

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

產生結果：

| 識別碼|
|---|
|  5|
|  7|
|  9|

請注意，每個偶數資料列都已遭到刪除。

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

產生結果：

| 識別碼|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

這裡有現有資料的組合。 更新 (WhenMatched) 程式碼路徑中的現有資料已獲派值 -1。 也新增了建立於程式碼片段之上，並透過插入程式碼路徑 (WhenNotMatched) 所新增的新資料。

### <a name="history"></a>記錄

Delta Lake 能夠允許查看資料表的記錄。 也就是對基礎 Delta 資料表所做的變更。 下面的資料格會顯示檢查記錄有多麼簡單。

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

產生結果：

|version|          timestamp|userId|userName|作業|                                                operationParameters| 作業|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  null|    null|    MERGE|                       [predicate -> (oldData.`ID` = newData.`ID`)]|null|    null|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|   刪除|[predicate -> ["((`ID` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|null|    null|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|   UPDATE| [predicate -> ((ID#744L % cast(2 as bigint)) = cast(0 as bigint))]|null|    null|     null|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|    WRITE|                             [mode -> Overwrite, partitionBy -> []]|null|    null|     null|          0|          null|        false|
|      0|2020-04-25 00:34:34|  null|    null|    WRITE|                         [mode -> ErrorIfExists, partitionBy -> []]|null|    null|     null|       null|          null|         true|

您可以在這裡看到對上述程式碼片段所做的所有修改。

## <a name="read-older-versions-of-data-using-time-travel"></a>使用時間移動來讀取較舊版本的資料

您可以使用稱為「時間移動」的功能來查詢 Delta Lake 資料表先前的快照集。 如果您想要存取之前覆寫的資料，則可以使用 versionAsOf 選項來查詢您覆寫第一組資料之前的資料表快照集。

當您執行下面的資料格後，應該就會看到進行覆蓋之前的第一組資料。 「時間移動」是一項非常強大的功能，可利用 Delta Lake 交易記錄的威力來存取不再位於資料表中的資料。 移除第 0 版選項 (或指定第 1 版) 便可再次看到較新的資料。 如需詳細資訊，請參閱[查詢資料表的舊版快照集](https://docs.delta.io/latest/delta-batch.html#deltatimetravel)。

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

產生結果：

| 識別碼|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

在這裡，您會看到您已回到最早的資料版本。

## <a name="write-a-stream-of-data-to-a-table"></a>將資料串流寫入到資料表

您也可以使用 Spark 的結構化串流來寫入到 Delta Lake 資料表。 Delta Lake 交易記錄可保證只處理一次，即使有其他串流或批次查詢同時對資料表執行也是如此。 根據預設，串流會以附加模式執行，這會將新記錄新增至資料表。

如需有關 Delta Lake 與結構化串流整合的詳細資訊，請參閱[資料表串流的讀取和寫入](https://docs.delta.io/latest/delta-streaming.html)。

在下面的資料格中，我們會進行以下操作：

* 資料格 30 顯示新附加的資料
* 資料格 31 檢查記錄
* 資料格 32 停止結構化串流作業
* 資料格 33 檢查記錄 <--您會注意到附加已停止

首先，您要設定簡單的 Spark 串流作業來產生順序，並將作業寫入至您的 Delta 資料表。

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>從資料表讀取變更串流

當串流寫入到 Delta Lake 資料表時，您也可以將該資料表作為串流來源而從中讀取資料。 例如，您可以啟動另一個串流查詢，以列印對 Delta Lake 資料表所做的所有變更。

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

產生結果：

| 識別碼|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

產生結果：

|version|          timestamp|       作業|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          刪除|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

在這裡，您會捨棄一些較不感興趣的資料行，以簡化記錄檢視的檢視體驗。

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

產生結果：

|version|          timestamp|       作業|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          刪除|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>將 Parquet 轉換成 Delta

您可以就地從 Parquet 格式轉換成 Delta。

在這裡，您要測試現有資料表是否為差異格式。
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

產生結果：

否

現在您要將資料轉換成差異格式，並確認其可正常運作。

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

產生結果：

是

## <a name="sql-support"></a>SQL 支援

Delta 可透過 SQL 來支援資料表公用程式命令。 您可以使用 SQL 來執行下列動作：

* 取得 DeltaTable 的記錄
* 清理 DeltaTable
* 將 Parquet 檔案轉換成 Delta

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

產生結果：

|version|          timestamp|userId|userName|       作業| operationParameters| 作業|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  null|    null|STREAMING UPDATE|[outputMode -> Ap...|null|    null|     null|          4|          null|         true|
|      4|2020-04-25 00:36:27|  null|    null|           MERGE|[predicate -> (ol...|null|    null|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|          刪除|[predicate -> ["(...|null|    null|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|          UPDATE|[predicate -> ((i...|null|    null|     null|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|           WRITE|[mode -> Overwrit...|null|    null|     null|          0|          null|        false|
|      0|2020-04-25 00:34:34|  null|    null|           WRITE|[mode -> ErrorIfE...|null|    null|     null|       null|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

產生結果：

|                path|
|--------------------|
|abfss://data@arca...|

現在您要確認資料表不是差異格式資料表，然後使用 Spark SQL 將其轉換成差異格式，並確認其已正確轉換。

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

產生結果：

是

如需完整文件，請參閱 [Delta Lake 文件頁面](https://docs.delta.io/latest/delta-intro.html)

如需詳細資訊，請參閱 [Delta Lake 專案](https://github.com/delta-io/delta)。

## <a name="next-steps"></a>後續步驟

* [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
