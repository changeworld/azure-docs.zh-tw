---
title: 教學課程：Apache Spark 串流和 Apache Kafka - Azure HDInsight
description: 了解如何使用 Apache Spark 串流將資料傳入或傳出 Apache Kafka。 在此教學課程中，您會使用 Jupyter Notebook 從 HDInsight 上的 Spark 串流處理資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,seodec18
ms.date: 03/11/2020
ms.openlocfilehash: 66bfa0d3ee4cb03f1b48e2db24be7a90d97f60d6
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117226"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>教學課程：將 Apache Spark 結構化串流用於 HDInsight 上的 Apache Kafka

本教學課程說明如何使用 [Apache Spark 結構化串流](https://spark.apache.org/docs/latest/structured-streaming-programming-guide)，對 Azure HDInsight 上的 [Apache Kafka](./kafka/apache-kafka-introduction.md) 讀取和寫入資料。

Spark 結構化串流是建置在 Spark SQL 上的串流處理引擎。 它允許您進行與靜態資料批次計算相同的串流計算。  

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 Azure Resource Manager 範本來建立叢集
> * 搭配 Kafka 使用 Spark 結構化串流

當您完成本文件中的步驟時，請記得刪除叢集，以避免產生過多的費用。

## <a name="prerequisites"></a>Prerequisites

* jq，這是命令列 JSON 處理器。  請參閱 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。

* 熟悉如何搭配使用 [Jupyter Notebook](https://jupyter.org/) 和 HDInsight 上的 Spark。 如需詳細資訊，請參閱[使用 HDInsight 上的 Apache Spark 載入資料及執行查詢](spark/apache-spark-load-data-run-query.md)文件。

* 熟悉 [Scala](https://www.scala-lang.org/) 程式設計語言。 教學課程中使用的程式碼是以 Scala 撰寫的。

* 熟悉如何建立 Kafka 主題。 如需詳細資訊，請參閱 [HDInsight 上的 Apache Kafka 快速入門](kafka/apache-kafka-get-started.md)文件。

> [!IMPORTANT]  
> 在本文件的步驟中，Azure 資源群組必須包含 HDInsight 上的 Spark 和 HDInsight 叢集上的 Kafka。 這兩個叢集都位於 Azure 虛擬網路中，可讓 Spark 叢集直接與 Kafka 叢集通訊。
>
> 為了方便您使用，本文件會連結至可建立所有必要 Azure 資源的範本。
>
> 如需在虛擬網路中使用 HDInsight 的詳細資訊，請參閱[規劃 HDInsight 的虛擬網路](hdinsight-plan-virtual-network-deployment.md)文件。

## <a name="structured-streaming-with-apache-kafka"></a>搭配 Apache Kafka 使用結構化串流

Spark 結構化串流是建置在 Spark SQL 引擎上的串流處理引擎。 使用結構化串流時，您可以比照撰寫批次查詢的方式來撰寫串流查詢。

下列程式碼片段說明如何讀取 Kafka 和儲存至檔案。 第一個程式碼片段是批次作業，第二個則是串流作業：

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

在這兩個程式碼片段中，都是從 Kafka 讀取資料並寫入至檔案。 這兩個範例的差異如下：

| Batch | 串流 |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

串流作業也會使用 `awaitTermination(30000)`，而會在 30,000 毫秒之後停止串流。

若要搭配使用結構化串流和 Kafka，您的專案必須具有對 `org.apache.spark : spark-sql-kafka-0-10_2.11` 套件的相依性。 此套件的版本應與 HDInsight 上的 Spark 版本相符。 對於 Spark 2.2.0 (適用於 HDInsight 3.6)，您可以在 [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar) 上找到不同專案類型的相依性資訊。

對於本教學課程中使用的 Jupyter Notebook，下列資料格會載入此套件相依性：

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>建立叢集

Apache Kafka on HDInsight 不提供透過公用網際網路存取 Kafka 訊息代理程式。 任何使用 Kafka 的項目都必須位於相同的 Azure 虛擬網路中。 在本教學課程中，Kafka 和 Spark 叢集位於相同的 Azure 虛擬網路中。

下圖顯示 Spark 與 Kafka 之間的通訊流程︰

![Azure 虛擬網路中的 Spark 和 Kafka 叢集圖表](./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Kafka 服務僅限於虛擬網路內的通訊。 叢集上的其他服務 (例如 SSH 和 Ambari) 可以透過網際網路存取。 如需有關適用於 HDInsight 的公用連接埠詳細資訊，請參閱 [HDInsight 所使用的連接埠和 URI](hdinsight-hadoop-port-settings-for-services.md)。

若要建立 Azure 虛擬網路，然後在其中建立 Kafka 和 Spark 叢集，請使用下列步驟：

1. 使用以下按鈕，在 Azure 入口網站中登入 Azure 並開啟範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager 範本位於 **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** 。

    此範本會建立下列資源：

   * HDInsight 3.6 叢集上的 Kafka。
   * HDInsight 3.6 叢集上的 Spark 2.2.0。
   * Azure 虛擬網路，其中包含 HDInsight 叢集。

     > [!IMPORTANT]  
     > 本教學課程中使用的結構化串流 Notebook 需要 HDInsight 3.6 上的 Spark 2.2.0。 如果您在 HDInsight 上使用較早版本的 Spark，當使用 Notebook 時會收到錯誤。

2. 使用下列資訊，填入 [自訂範本]  區段上的項目︰

    | 設定 | 值 |
    | --- | --- |
    | 訂用帳戶 | 您的 Azure 訂用帳戶 |
    | 資源群組 | 包含資源的資源群組。 |
    | Location | 資源建立所在的 Azure 區域。 |
    | Spark 叢集名稱 | Spark 叢集的名稱。 前六個字元必須與 Kafka 叢集名稱不同。 |
    | Kafka 叢集名稱 | Kafka 叢集的名稱。 前六個字元必須與 Spark 叢集名稱不同。 |
    | 叢集登入使用者名稱 | 叢集的管理員使用者名稱。 |
    | 叢集登入密碼 | 叢集的管理員使用者密碼。 |
    | SSH 使用者名稱 | 要為叢集建立的 SSH 使用者。 |
    | SSH 密碼 | SSH 使用者的密碼。 |

    ![自訂範本的螢幕擷取畫面](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. 讀取**條款及條件**，然後選取 [我同意上方所述的條款及條件]  。

4. 選取 [購買]  。

> [!NOTE]  
> 建立叢集可能需要 20 分鐘的時間。

## <a name="use-spark-structured-streaming"></a>使用 Spark 結構化串流

此範例將示範如何搭配使用 Spark 結構化串流與 HDInsight 上的 Kafka。 該範例將使用紐約市所提供的計程車車程資料。  此筆記本使用的資料集來自 [2016 年綠色計程車車程資料 (2016 Green Taxi Trip Data)](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb)。

1. 收集主機資訊。 使用 curl 和 [jq](https://stedolan.github.io/jq/) 命令來取得 Kafka ZooKeeper 及訊息代理程式主機的資訊。 這些命令專為 Windows 命令提示字元所設計，若使用其他環境，則需要一點變化。 以 Kafka 叢集的名稱取代 `KafkaCluster`，並以叢集登入密碼取代 `KafkaPassword`。 此外，將 `C:\HDI\jq-win64.exe` 取代為安裝 jq 的實際路徑。 在 Windows 命令提示字元中輸入命令並儲存輸出，以在稍後步驟中使用。

    ```cmd
    REM Enter cluster name in lowercase

    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/jupyter`，其中 `CLUSTERNAME` 是叢集的名稱。 出現提示時，輸入您在建立叢集時所使用的叢集登入 (admin) 和密碼。

1. 選取 [新增] > [Spark]  來建立 Notebook。

1. Spark 串流具有微批次處理，這表示資料會以批次方式提供，而執行程式會在資料批次上執行。 如果執行程式的閒置逾時小於處理批次所需的時間，則會不斷加入及移除執行程式。 如果執行程式的閒置逾時大於批次持續時間，則永遠不會移除執行程式。 因此，**我們建議您在執行串流應用程式時，將 spark.dynamicAllocation.enabled 設定為 false，以停用動態配置。**

    在 Notebook 資料格中輸入下列資訊，即可載入 Notebook 使用的套件。 使用 **CTRL + ENTER** 執行命令。

    ```configuration
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11",
            "spark.dynamicAllocation.enabled": false
        }
    }
    ```

1. 建立 Kafka 主題。 編輯下列命令，將 `YOUR_ZOOKEEPER_HOSTS` 取代為在第一個步驟中擷取的 Zookeeper 主機資訊。 將編輯後的命令輸入 Jupyter Notebook 中，以建立 `tripdata` 主題。

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

1. 擷取計程車車程資料。 在下一個資料格中輸入命令，以載入紐約市的計程車車程資料。 資料會載入 dataframe，然後 dataframe 會顯示為資料格的輸出。

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString

    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)

    // Display the dataframe containing trip data
    taxiDF.show()
    ```

1. 設定 Kafka 訊息代理程式主機資訊。 將 `YOUR_KAFKA_BROKER_HOSTS` 取代為您在步驟 1 擷取的訊息代理程式主機資訊。  在下一個 Jupyter Notebook 資料格中，輸入已編輯的命令。

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

1. 將資料傳送至 Kafka。 在下列命令中，`vendorid` 欄位會用來作為 Kafka 訊息的索引鍵值。 Kafka 會在分割資料時使用該索引鍵。 所有的欄位都會以 JSON 字串值的形式儲存在 Kafka 訊息中。 在 Jupyter 中輸入下列命令，以使用批次查詢將資料儲存到 Kafka。

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

1. 宣告結構描述。 下列命令會示範從 kafka 讀取 JSON 資料時如何使用結構描述。 在下一個 Jupyter 資料格中輸入命令。

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)

    println("Schema declared")
    ```

1. 選取資料，並啟動串流。 下列命令會示範如何使用批次查詢從 kafka 擷取資料，然後將結果寫出到 Spark 叢集上的 HDFS。 在此範例中，`select` 會從 Kafka 擷取訊息 (值欄位)，並對其套用結構描述。 接著，資料會以 Parquet 格式寫入到 HDFS (WASB 或 ADL)。 在下一個 Jupyter 資料格中輸入命令。

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()

    println("Wrote data to file")
    ```

1. 您可以在下一個 Jupyter 資料格中輸入命令，確認檔案是否已建立。 這會在 `/example/batchtripdata` 目錄中列出檔案。

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

1. 雖然上述範例使用批次查詢，但下列命令會示範如何使用串流查詢來執行相同動作。 在下一個 Jupyter 資料格中輸入命令。

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

1. 執行下列資料格來確認串流查詢是否已寫入這些檔案。

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>清除資源

若要清除本教學課程所建立的資源，您可以刪除資源群組。 刪除資源群組也會刪除相關聯的 HDInsight 叢集，以及與資源群組相關聯的任何其他資源。

若要使用 Azure 入口網站移除資源群組：

1. 在 [Azure 入口網站](https://portal.azure.com/)中展開左側功能表，以開啟服務的功能表，然後選擇 [資源群組]  以顯示資源群組的清單。
2. 找出要刪除的資源群組，然後以滑鼠右鍵按一下清單右側的 [更多]  按鈕 (...)。
3. 選取 [刪除資源群組]  ，並加以確認。

> [!WARNING]  
> HDInsight 叢集的計費起自叢集建立時，終至叢集刪除時。 計費是以每分鐘按比例計算，因此不再使用時，請一律刪除您的叢集。
>
> 刪除 HDInsight 叢集上的 Kafka，也會刪除 Kafka 中儲存的任何資料。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 [Apache Spark 結構化串流](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)對 HDInsight 上的 [Apache Kafka](./kafka/apache-kafka-introduction.md) 寫入及讀取資料。 請使用下列連結了解如何搭配使用 [Apache Storm](./storm/apache-storm-overview.md) 與 Kafka。

> [!div class="nextstepaction"]
> [使用 Apache Storm 搭配 Apache Kafka](hdinsight-apache-storm-with-kafka.md)
