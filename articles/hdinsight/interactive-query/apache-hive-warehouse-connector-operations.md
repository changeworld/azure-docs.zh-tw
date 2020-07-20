---
title: Azure HDInsight 中 Hive Warehouse Connector 支援的 Apache Spark 作業
description: 了解 Azure HDInsight 上 Hive Warehouse Connector 的不同功能。
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: ed3dbe4cb5b9d8b50c028a68feeded170130bfb8
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085832"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Azure HDInsight 中 Hive Warehouse Connector 支援的 Apache Spark 作業

本文說明 Hive Warehouse Connector (HWC) 支援的 Spark 型作業。 以下顯示的所有範例都會透過 Apache Spark 殼層來執行。

## <a name="prerequisite"></a>必要條件

完成 [Hive Warehouse Connector 設定](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)步驟。

## <a name="getting-started"></a>開始使用

若要啟動 spark-shell 工作階段，請執行下列步驟：

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)來連線到 Apache Spark 叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 從 ssh 工作階段執行下列命令，以記下 `hive-warehouse-connector-assembly` 版本：

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. 使用上面所識別的 `hive-warehouse-connector-assembly` 版本來編輯下列程式碼。 然後執行命令以啟動 Spark 殼層：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. 啟動 Spark 殼層之後，您可以使用下列命令來啟動 Hive Warehouse Connector 執行個體：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>使用 Hive 查詢建立 Spark 資料框架

使用 HWC 程式庫的所有查詢結果都會以資料框架的形式傳回。 下列範例示範如何建立基本 Hive 查詢。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

查詢結果是 Spark 資料框架，可以與像是 MLIB 和 SparkSQL 的 Spark 程式庫搭配使用。

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>將 Spark 資料框架寫出至 Hive 資料表

Spark 原生不支援寫入 Hive 的受控 ACID 資料表。 不過，使用 HWC 時，您可以將任何資料框架寫出至 Hive 資料表。 您可以在下列範例中看到這項功能運作：

1. 建立名為 `sampletable_colorado` 的資料表，並使用下列命令來指定其資料行：

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. 篩選資料表 `hivesampletable`，其中資料行 `state` 等於 `Colorado`。 此 Hive 查詢會使用 `write` 函式，傳回儲存在 Hive 資料表 `sampletable_colorado` 的 Spark 資料框架分析。

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. 使用下列命令來檢視結果：

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![Hive Warehouse Connector 顯示 Hive 資料表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)


## <a name="structured-streaming-writes"></a>結構化串流寫入

使用 Hive Warehouse Connector，您也可以使用 Spark 串流將資料寫入至 Hive 資料表。

> [!IMPORTANT]
> 在已啟用 ESP 的 Spark 4.0 叢集中，不支援結構化串流寫入。

請遵循下列步驟，透過下列方式，將 localhost 連接埠 9999上的資料從 Spark 串流內嵌至 Hive 資料表： Hive Warehouse Connector。

1. 從您的開啟 Spark 殼層，使用下列命令開始 Spark 串流：

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 執行下列步驟，為您建立的 Spark 串流產生資料：
    1. 在相同的 Spark 叢集中開啟第二個 SSH 工作階段。
    1. 在命令提示字元中，輸入 `nc -lk 9999`。 此命令會使用 netcat 公用程式，從命令列將資料傳送至指定的連接埠。

1. 返回第一個 SSH 工作階段，並建立新的 Hive 資料表來保存串流資料。 在 spark-shell 中，輸入下列命令：

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 然後使用下列命令，將串流資料寫入至新建立的資料表：

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > 因為 Apache Spark 中有已知問題，所以 `metastoreUri` 和 `database` 選項目前必須手動設定。 如需有關此問題的詳細資訊，請參閱 [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)。

1. 返回第二個 SSH 工作階段，然後輸入下列值：

    ```bash
    foo
    HiveSpark
    bar
    ```

1. 返回第一個 SSH 工作階段，並記下簡短的活動。 請使用下列命令以檢視資料：

    ```scala
    hive.table("stream_table").show()
    ```

使用 **Ctrl + C**，在第二個 SSH 工作階段上停止 netcat。 使用 `:q`，在第一個 SSH 工作階段上結束 spark-shell。

## <a name="next-steps"></a>後續步驟

* [HWC 與 Apache Spark 和 Apache Hive 的整合](./apache-hive-warehouse-connector.md)
* [搭配 HDInsight 使用互動式查詢](./apache-interactive-query-get-started.md)
* [HWC 與 Apache Zeppelin 的整合](./apache-hive-warehouse-connector-zeppelin.md)
