---
title: 使用 Databricks (Spark) 將資料從 Apache Cassandra 遷移至 Azure Cosmos DB Cassandra API
description: 瞭解如何使用 Azure Databricks 和 Spark，將資料從 Apache Cassandra 資料庫移轉至 Azure Cosmos DB Cassandra API。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 74088d749279ab72851e714a50b558dc2adbc0d7
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516547"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>使用 Azure Databricks 將資料從 Cassandra 遷移至 Azure Cosmos DB Cassandra API 帳戶
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 已成為在 Apache Cassandra 上執行之企業工作負載的絕佳選擇，原因如下： 

* **管理和監視沒有額外負荷：** 它消除了管理和監視各種作業系統、JVM 和 yaml 檔案與其互動的設定時，會產生的額外負荷。

* **大幅節省成本：** 您可以使用 Azure Cosmos DB 來節省成本，包括 VM 的成本、頻寬和任何適用的授權。 此外，您不需要管理資料中心、伺服器、SSD 儲存體、網路和電力成本。 

* **使用現有程式碼和工具的能力：** Azure Cosmos DB 提供與現有 Cassandra Sdk 和工具的有線通訊協定層級相容性。 此相容性可確保您可以透過 Azure Cosmos DB Cassandra API 使用現有程式碼基底執行瑣碎的變更。

有多種方式可將資料庫工作負載從某個平臺遷移至另一個平臺。 [Azure Databricks](https://azure.microsoft.com/services/databricks/) 是一種平臺即 [Apache Spark](https://spark.apache.org/) 服務供應專案，可提供一種方式來大規模執行離線遷移。 本文說明使用 Azure Databricks 將資料從原生 Apache Cassandra keyspace/資料表遷移至 Azure Cosmos DB Cassandra API 所需的步驟。

## <a name="prerequisites"></a>必要條件

* [佈建 Azure Cosmos DB Cassandra API 帳戶](create-cassandra-dotnet.md#create-a-database-account)

* [檢閱連線至 Azure Cosmos DB Cassandra API 的基本概念](cassandra-spark-generic.md)

* 請參閱 [Azure Cosmos DB Cassandra API 中支援的功能](cassandra-support.md) ，以確保相容性。

* 確定您已在目標 Azure Cosmos DB Cassandra API 帳戶中建立空的 keyspace 和資料表

* [使用 cqlsh 或 hosted shell 進行驗證](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>佈建 Azure Databricks 叢集

您可以依照指示來布建 [Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)叢集。 不過，請注意，Apache Cassandra 連接器目前不支援 Apache Spark 3.x。 您將需要使用支援的2.x 版 Apache Spark 來布建 Databricks 執行時間。 建議您選取支援最新版本 Spark 2.x 的 Databricks 執行時間版本，而不是在 Scala 2.11 版之前：

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks 執行時間":::


## <a name="add-dependencies"></a>新增相依性

您必須將 Apache Spark Cassandra 連接器程式庫新增至您的叢集，才能連線至原生和 Cosmos DB Cassandra 端點。 在您的叢集中選取程式庫-> 安裝新 > 的 maven > 搜尋套件：

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks 搜尋套件":::

`Cassandra`在 [搜尋] 方塊中輸入，並選取可用的最新 `spark-cassandra-connector` maven 存放庫，然後選取 [安裝：

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks 選取套件":::

> [!NOTE]
> 請確定您已安裝 Cassandra 連接器程式庫之後，重新開機 Databricks 叢集。

## <a name="create-scala-notebook-for-migration"></a>建立 Scala 筆記本以進行遷移

使用下列程式碼，在 Databricks 中建立 Scala 筆記本。 以對應的認證、來源/目標 keyspace 和資料表取代您的來源和目標 cassandra 設定，然後執行：

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> `spark.cassandra.output.concurrent.writes`和設定 `connections_per_executor_max` 對於避免[速率限制](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)很重要，這會在 Cosmos DB 的要求超過布建的輸送量 ([要求單位](./request-units.md)) 時才會發生。 您可能需要根據 Spark 叢集中的執行程式數目來調整這些設定，而且可能的大小 (，因此會將每個記錄寫入目標資料表的 RU 成本) 。

## <a name="next-steps"></a>後續步驟

* [在容器和資料庫中佈建輸送量](set-throughput.md) 
* [分割區索引鍵的最佳作法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB 容量規劃文章來預估 RU/秒](estimate-ru-with-capacity-planner.md)
* [Azure Cosmos DB Cassandra API 的彈性調整](manage-scale-cassandra.md)