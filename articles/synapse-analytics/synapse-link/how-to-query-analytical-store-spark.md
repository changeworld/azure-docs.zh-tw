---
title: 使用適用於 Azure Synapse Analytics 的 Apache Spark 查詢 Azure Cosmos DB 分析存放區 (預覽)
description: 如何使用適用於 Azure Synapse Analytics 的 Apache Spark 查詢 Azure Cosmos DB 分析
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 5c19a3f808b85ba9e34d3304251fe8acb21204ce
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700197"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>使用適用於 Azure Synapse Analytics 的 Apache Spark 查詢 Azure Cosmos DB 分析存放區 (預覽)

本文會提供一些範例，說明如何透過 Synapse 手勢與分析存放區互動。 當您以滑鼠右鍵按一下容器時，就可以看到手勢。 您可以透過手勢快速產生程式碼，並依據您的需求進行調整。 按一下以探索資料時，也非常適合使用手勢。

## <a name="load-to-dataframe"></a>載入至 DataFrame

在此步驟中，您將從 Spark DataFrame 中的 Azure Cosmos DB 分析存放區讀取資料。 這會從名為 ***df*** 的 DataFrame 中顯示 10 個資料列。 當您的資料進入 DataFrame 後，您即可執行額外的分析。

此作業不會影響交易存放區。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

**Scala** 中對等的程式碼手勢會是下列程式碼：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>建立 Spark 資料表

在此手勢中，您將建立指向所選容器的 Spark 資料表。 該作業不會導致任何資料移動。 如果您決定刪除該資料表，基礎容器 (和對應的分析存放區) 將不受影響。 

此案例便於透過第三方工具重複使用資料表，並提供對執行階段資料的存取性。

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>將 DataFrame 寫入容器

在此手勢中，您會將 DataFrame 寫入容器中。 此作業會影響交易效能，並使用要求單位。 就寫入交易而言，使用 Azure Cosmos DB 交易效能是非常適合的。 請務必將 **YOURDATAFRAME** 取代為您要寫回的目標 DataFrame。

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

**Scala** 中對等的程式碼手勢會是下列程式碼：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>從容器載入串流 DataFrame
在此手勢中，您將使用 Spark 串流功能將容器中的資料載入至 DataFrame。 資料將會儲存在已連線至工作區的主要 Data Lake 帳戶 (和檔案系統) 中。 

如果未建立 */localReadCheckpointFolder* 資料夾，則會加以自動建立。 這項作業會影響 Azure Cosmos DB 的交易效能。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

**Scala** 中對等的程式碼手勢會是下列程式碼：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>將串流 DataFrame 寫入容器
在此手勢中，您會將串流 DataFrame 寫入您選取的 Azure Cosmos DB 容器中。 如果未建立 */localReadCheckpointFolder* 資料夾，則會加以自動建立。 這項作業會影響 Azure Cosmos DB 的交易效能。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

**Scala** 中對等的程式碼手勢會是下列程式碼：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>後續步驟

* [了解 Synapse 與 Azure Cosmos DB 之間支援的項目](./concept-synapse-link-cosmos-db-support.md)
* [連線至適用於 Azure Cosmos DB 的 Synapse Link](../quickstart-connect-synapse-link-cosmos-db.md)
