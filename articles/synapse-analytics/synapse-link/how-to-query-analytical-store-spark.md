---
title: 使用 Azure Synapse 連結中的 Apache Spark 與 Azure Cosmos DB 互動 (預覽)
description: 如何使用 Azure Synapse 連結中的 Apache Spark 與 Azure Cosmos DB 互動
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 07342cb31f1c44273f98a97b018620538f86c17f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287724"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link-preview"></a>使用 Azure Synapse 連結中的 Apache Spark 與 Azure Cosmos DB 互動 (預覽)

在本文中，您將了解如何使用 Synapse Apache Spark 與 Azure Cosmos DB 互動。 Synapse Apache Spark 提供 Scala、Python、SparkSQL 和 C# 的完整支援，是[適用於 Azure Cosmos DB 的 Azure Synapse 連結](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)中分析、資料工程、資料科學和資料探索案例的核心。

與 Azure Cosmos DB 互動時，系統支援下列功能：
* Synapse Apache Spark 可讓您以近乎即時的方式，分析以 Azure Synapse 連結啟用之 Azure Cosmos DB 容器中的資料，而不會影響交易式工作負載的效能。 下列兩個選項可用來查詢 Spark 中的 Azure Cosmos DB [分析存放區](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)：
    + 載入至 Spark 資料框架
    + 建立 Spark 資料表
* Synapse Apache Spark 也可讓您將資料內嵌至 Azure Cosmos DB。 請務必注意，資料一律會透過交易式存放區內嵌到 Azure Cosmos DB 容器。 啟用 Synapse 連結時，任何新的插入、更新和刪除都會自動同步處理到分析存放區。
* Synapse Apache Spark 也支援使用 Azure Cosmos DB 作為來源和接收的 Spark 結構化串流。 

下列各節將逐步解說上述功能的語法。 Azure Synapse Analytics 工作區中手勢的設計，是為了讓您在開始使用時能有簡單的立即可用體驗。 在 Synapse 工作區的**資料**索引標籤中，以滑鼠右鍵按一下 Azure Cosmos DB 容器時，就會顯示手勢。 您可以透過手勢快速產生程式碼，並依據您的需求進行調整。 按一下以探索資料時，也非常適合使用手勢。

## <a name="query-azure-cosmos-db-analytical-store"></a>查詢 Azure Cosmos DB 分析存放區

在了解可用來查詢 Azure Cosmos DB 分析存放區、載入 Spark 資料框架和建立 Spark 資料表的兩個可能選項之前，請先探索經驗的差異，以便選擇適合您需求的選項。

體驗方面的差異在於，Azure Cosmos DB 容器中的基礎資料變更是否應該自動反映在 Spark 中執行的分析。 當 Spark 資料框架已註冊，或針對容器的分析存放區建立 Spark 資料表時，系統會將分析存放區中目前資料快照集的中繼資料提取至 Spark，以有效率地推送後續分析。 請務必注意，由於 Spark 會遵循延遲評估原則，除非在 Spark 資料框架上叫用動作，或針對 Spark 資料表執行 SparkSQL 查詢，否則系統不會從基礎容器的分析存放區提取實際的資料。

在**載入至 Spark 資料框架**的情況下，系統會透過 Spark 工作階段的存留期快取提取的中繼資料，因此在建立資料框架時，系統會針對分析存放區的快照集評估在資料框架上叫用的後續動作。

另一方面，在**建立 Spark 資料表**的情況下，系統不會在 Spark 中快取分析存放區狀態的中繼資料，而且會在每次對 Spark 資料表執行 SparkSQL 查詢時重新載入。

因此，您可以選擇要針對分析存放區的固定快照集，或分別針對分析存放區的最新快照集評估您的 Spark 分析來載入 Spark 資料框架並建立 Spark 資料表。

> [!NOTE]
> 若要查詢 Mongo DB 帳戶的 Azure Cosmos DB API，請深入了解分析存放區中的[完整精確度結構描述標記法](../../cosmos-db/analytical-store-introduction.md#analytical-schema)，以及要使用的擴充屬性名稱。

### <a name="load-to-spark-dataframe"></a>載入至 Spark 資料框架

在此範例中，您將建立指向 Azure Cosmos DB 分析存放區的 Spark 資料框架。 接著，您可以對資料框架叫用 Spark 動作，以執行額外的分析。 此作業不會影響交易存放區。

**Python** 中的語法如下：
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

**Scala** 中對等的語法如下：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>建立 Spark 資料表

在此範例中，您將建立指向 Azure Cosmos DB 分析存放區的 Spark 資料表。 接著，您可以對資料表叫用 SparkSQL 查詢，以執行額外的分析。 此作業並不會影響交易式存放區，也不會移動任何資料。 如果您決定刪除此 Spakr 資料表，基礎 Azure Cosmos DB 容器和對應的分析存放區將不受影響。 

此案例便於透過第三方工具重複使用 Spark 資料表，並提供對執行階段基礎資料的存取性。

建立 Spark 資料表的語法如下：
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> 如果您有基礎 Azure Cosmos DB 容器的結構描述在一段時間內變更的案例，而且希望更新的結構描述可自動反映在 Spark 資料表的查詢，可以將 `spark.cosmos.autoSchemaMerge` 選項設定為 Spark 資料表選項中的 `true` 來達成此目的。


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>將 Spark 資料框架寫入 Azure Cosmos DB 容器

在此範例中，您會將 Spark 資料框架寫入 Azure Cosmos DB 容器。 這項作業會影響交易式工作負載的效能，並取用在 Azure Cosmos DB 容器或共用資料庫上佈建的要求單位。

**Python** 中的語法如下：
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

**Scala** 中對等的語法如下：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>從容器載入串流 DataFrame
在此手勢中，您將使用 Spark 串流功能將容器中的資料載入至 DataFrame。 資料將會儲存在已連線至工作區的主要 Data Lake 帳戶 (和檔案系統) 中。 
> [!NOTE]
> 如果要參考 Synapse Apache Spark 中的外部程式庫，請[在此深入了解](#external-library-management)。 比方說，如果要將 Spark 資料框架內嵌至適用於 Mongo DB 的 Cosmos DB API 容器，可以在[這裡](https://docs.mongodb.com/spark-connector/master/)利用適用於 Spark 的 Mongo DB 連接器。

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>從 Azure Cosmos DB 容器載入串流 DataFrame
在此範例中，您將使用 Spark 的結構化串流功能，從 Azure Cosmos DB 容器將資料載入至使用 Azure Cosmos DB 中變更摘要功能的 Spark 串流資料框架。 Spark 使用的檢查點資料將會儲存在已連線至工作區的主要 Data Lake 帳戶 (和檔案系統) 中。

如果未建立 /localReadCheckpointFolder 資料夾 (在下列範例中)，系統會自動建立。 這項作業會影響交易式工作負載的效能，並取用在 Azure Cosmos DB 容器或共用資料庫上佈建的要求單位。

**Python** 中的語法如下：
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

**Scala** 中對等的語法如下：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>將串流資料框架寫入 Azure Cosmos DB 容器
在此範例中，您會將串流資料框架寫入 Azure Cosmos DB 容器。 這項作業會影響交易式工作負載的效能，並取用在 Azure Cosmos DB 容器或共用資料庫上佈建的要求單位。 如果未建立 /localWriteCheckpointFolder 資料夾 (在下列範例中)，系統會自動建立。 

**Python** 中的語法如下：
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

**Scala** 中對等的語法如下：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>外部程式庫管理

在此範例中，您將了解在 Synpase Apache Spark 工作區中使用 Spark 筆記本時，如何參考 JAR 檔案中的外部程式庫。 您可以將 JAR 檔案放在您連線至工作區之主要資料湖帳戶的容器中，然後在您的 Spark 筆記本中新增下列 `%configure` 陳述式：

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
如果要將遠端 Spark 作業定義提交至 Synapse Spark 集區，可以遵循此[教學課程](../spark/apache-spark-job-definitions.md)了解如何參考外部程式庫。

## <a name="next-steps"></a>後續步驟

* [GitHub 上的開始使用 Azure Synapse 連結範例](https://aka.ms/cosmosdb-synapselink-samples)
* [了解適用於 Azure Cosmos DB 的 Azure Synapse Link 支援的功能](./concept-synapse-link-cosmos-db-support.md)
* [連線至適用於 Azure Cosmos DB 的 Synapse Link](../quickstart-connect-synapse-link-cosmos-db.md)
