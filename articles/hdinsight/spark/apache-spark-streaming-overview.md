---
title: Azure HDInsight 中的 Spark 串流
description: 如何在 HDInsight Spark 叢集上使用 Apache Spark 串流應用程式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: a88d4893daa12ff2c35ee7cf8f4e5b7569f854f6
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086189"
---
# <a name="overview-of-apache-spark-streaming"></a>Apache Spark 串流概觀

[Apache Spark](https://spark.apache.org/)串流會在 HDInsight Spark 叢集上提供資料流程處理。 保證任何輸入事件只會處理一次，即使發生節點失敗也是一樣。 Spark 串流是長時間執行的作業，會接收來自各種來源的輸入資料，包括 Azure 事件中樞。 同時： Azure IoT 中樞、Apache Kafka、Apache Flume、Twitter、 `ZeroMQ` 原始 TCP 通訊端，或來自監視 Apache HADOOP YARN 檔案系統。 不同于僅事件驅動的程式，Spark 串流會將輸入資料批次到時間視窗。 例如，2秒的配量，然後使用對應、減少、聯結和解壓縮作業來轉換每個批次的資料。 Spark 串流會接著將已轉換的資料寫出至檔案系統、資料庫、儀表板及主控台。

![使用 HDInsight 和 Spark 串流處理資料流](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark 串流應用程式必須等候一小部分來收集每個 `micro-batch` 事件，然後再將該批次傳送至進行處理。 相較之下，事件驅動的應用程式會立即處理每個事件。 而 Spark 串流通常會延遲幾秒鐘。 微批次方法的優點是可更有效率的處理資料和更簡易的彙總運算。

## <a name="introducing-the-dstream"></a>DStream 簡介

Spark 串流會使用名為 DStream 的*離散化資料流*表示傳入資料的連續資料流。 DStream 可以從輸入來源建立，例如事件中樞或 Kafka。 或藉由在另一個 DStream 上套用轉換。

DStream 會在原始事件資料的頂端提供抽象層。

先以單一事件為例，例如從連接的控溫器讀取溫度。 當此事件抵達 Spark 串流應用程式時，事件會以可靠的方式儲存，並在其中複寫到多個節點。 此容錯功能可確保任何單一節點的失敗都不會導致事件遺失。 Spark core 會使用資料結構，將資料分散到叢集中的多個節點。 其中，每個節點通常會在記憶體中維護自己的資料，以獲得最佳效能。 此資料結構稱為*彈性分散式資料集* (resilient distributed dataset, RDD)。

每個 RDD 皆代表在使用者定義的時間範圍 (稱為「批次間隔」**) 內收集的事件。 當每個批次間隔過去後，新的 RDD 就會產生，並包含該間隔中的所有資料。 一組連續的 Rdd 會收集到 DStream 中。 例如，如果批次間隔長度為一秒，您的 DStream 就會每秒發出包含一個 RDD 的批次，該 RDD 會包含在這一秒期間內嵌的所有資料。 處理 DStream 時，溫度事件就會出現在這些批次的其中一個。 Spark 串流應用程式會處理包含事件的批次，並在最後處理儲存在每個 RDD 中的資料。

![DStream 與溫度事件的範例](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Spark 串流應用程式的結構

Spark 串流應用程式是長時間執行的應用程式，會從內嵌來源接收資料。 套用轉換來處理資料，然後將資料推送至一或多個目的地。 Spark 串流應用程式的結構包含一個靜態部分和一個動態部分。 靜態部分會定義資料的來源，以及要對資料進行哪些處理。 結果應該會在何處。 動態部分會無限期執行應用程式，等候停止訊號。

例如，以下的簡單應用程式會透過 TCP 通訊端接收一行文字，並計算每個字彙出現的次數。

### <a name="define-the-application"></a>定義應用程式

應用程式邏輯定義包含四個步驟：

1. 建立 StreamingContext。
2. 從 StreamingContext 建立 DStream。
3. 將轉換套用至 DStream。
4. 輸出結果。

此定義是靜態的，而且在您執行應用程式之前不會處理任何資料。

#### <a name="create-a-streamingcontext"></a>建立 StreamingContext

從指向您叢集的 SparkContext 建立 StreamingContext。 建立 StreamingContext 時，您可以指定批次大小 (以秒為單位)，例如：  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>建立 DStream

請使用 StreamingContext 執行個體來為您的輸入來源建立輸入 DStream。 在此情況下，應用程式會在預設連接的儲存體中監看新檔案的外觀。

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>套用轉換

在 DStream 上套用轉換後，您就可以實作處理作業。 此應用程式會從檔案一次接收一行文字，並將每一行分割成單字。 然後使用地圖-縮小模式來計算每個單字出現的次數。

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>輸出結果

藉由套用輸出作業，將轉換結果推送至目的地系統。 在此案例中，會將透過計算產生的每個執行結果顯示在主控台輸出中。

```
wordCounts.print()
```

### <a name="run-the-application"></a>執行應用程式

啟動串流應用程式並執行，直到收到終止訊號。

```
ssc.start()
ssc.awaitTermination()
```

如需 Spark 串流 API 的詳細資訊，請參閱[Apache Spark 串流程式設計指南](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)。

下列的範例應用程式皆各自獨立，因此您可以在 [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md) 中加以執行。 此範例會在 DummySource 類別中建立模擬資料來源，此類別會輸出計數器的值，以及每五秒輸出一次目前的時間 (以毫秒為單位)。 新 StreamingContext 物件的批次間隔為 30 秒。 每次建立批次時，串流應用程式都會檢查所產生的 RDD。 然後將 RDD 轉換成 Spark 資料框架，並透過資料框架建立臨時表。

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

啟動上述應用程式之後，請等候約30秒。  然後，您可以定期查詢資料框架，以查看目前在批次中的一組值，例如使用此 SQL 查詢：

```sql
%%sql
SELECT * FROM demo_numbers
```

產生的輸出看起來會像下列輸出：

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

一共有六個值，因為 DummySource 每隔 5 秒就會建立一個值，而應用程式是每隔 30 秒便發出一個批次。

## <a name="sliding-windows"></a>滑動時間範圍

若要在一段時間內執行 DStream 上的匯總計算，例如，若要取得過去兩秒的平均溫度，請使用 `sliding window` Spark 串流所包含的作業。 滑動時間範圍具有持續時間 (時間範圍長度) 及間隔，系統會在該期間內對該時間範圍的內容進行評估 (滑動間隔)。

滑動時間範圍可以重疊，例如，您可以定義一個長度為 2 秒且每秒滑動一次的時間範圍。 此動作表示每次進行匯總計算時，此視窗會包含上一個視窗最後一秒的資料。 以及接下來一秒的任何新資料。

![初始時間範圍和溫度事件範例](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![滑動後的時間範圍和溫度事件範例](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

下列範例會更新使用 DummySource 的程式碼，以將批次收集至持續時間為一分鐘且滑動為一分鐘的時間範圍內。

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

在第一分鐘之後，會有 12 個項目 - 在該時間範圍內收集的兩個批次中，每一個批次各有 6 個項目。

| value | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

可在 Spark 串流 API 中使用的滑動時間範圍函式包括 window、countByWindow、reduceByWindow 和 countByValueAndWindow。 如需有關這些函式的詳細資料，請參閱 [DStream 上的轉換](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams) \(英文\)。

## <a name="checkpointing"></a>檢查點

為了提供備援和容錯，Spark 串流會藉由檢查點來確保串流處理即使在發生節點錯誤時，也能夠持續而不中斷。 Spark 會建立持久儲存體（Azure 儲存體或 Data Lake Storage）的檢查點。 這些檢查點會儲存串流應用程式中繼資料，例如設定，以及應用程式所定義的作業。 此外，任何已排入佇列但尚未處理的批次。 有時候，檢查點也會包含將資料儲存在 Rdd 中，以更快速地從 Spark 所管理的 Rdd 中重建資料的狀態。

## <a name="deploying-spark-streaming-applications"></a>部署 Spark 串流處理應用程式

您通常會在本機將 Spark 串流應用程式建立在 JAR 檔案中。 然後將 JAR 檔案複製到預設連結的存放裝置，將其部署至 HDInsight 上的 Spark。 您可以使用 POST 作業，藉由叢集提供的 LIVY REST API 來啟動您的應用程式。 POST 的主體包含 JSON 檔，可提供 JAR 的路徑。 和類別的名稱，其主要方法會定義和執行串流應用程式，並選擇性地選擇作業的資源需求（例如執行程式、記憶體和核心的數目）。 此外，您的應用程式程式碼所需的任何配置設定。

![部署 Spark 串流應用程式](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

您也可以藉由 LIVY 端點，使用 GET 要求來檢查所有應用程式的狀態。 最後，您可以對 LIVY 端點發出 DELETE 要求，以結束執行中的應用程式。 如需 LIVY API 的詳細資料，請參閱[使用 Apache LIVY 執行遠端作業](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>後續步驟

* [在 HDInsight 中建立 Apache Spark 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html) (Apache Spark 串流程式設計指南)
* [Apache Spark 結構化串流的概觀](apache-spark-structured-streaming-overview.md)
