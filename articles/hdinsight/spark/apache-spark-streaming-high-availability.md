---
title: YARN 中的高可用性 Spark 串流作業-Azure HDInsight
description: 如何在 Azure HDInsight 中設定高可用性案例的 Apache Spark 串流
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: ac51b77e1ffc2b476b0a73dac9b6917552a86ce4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74807148"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>使用 YARN 建立高可用性 Apache Spark 串流作業

[Apache Spark](https://spark.apache.org/) 串流可讓您實作可擴充、高輸送量、容錯應用程式，以便進行資料流處理。 您可以將 HDInsight Spark 叢集上的 Spark 串流應用程式連線到不同類型的資料來源，例如 Azure 事件中樞、Azure IoT 中樞、 [Apache Kafka](https://kafka.apache.org/)、 [apache Flume](https://flume.apache.org/)、Twitter、 [ZeroMQ](http://zeromq.org/)、原始 TCP 通訊端，或藉由監視[Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)檔案系統的變更。 Spark 串流可支援容錯，並保證任何指定的事件只會處理一次，即使節點失敗亦然。

Spark 串流會建立長時間執行的作業，在這段期間，您可以將轉換套用到資料，然後將結果推送至檔案系統、資料庫、儀表板和主控台。 Spark 串流會先收集定義的時間間隔內的事件批次，藉此處理資料微批次。 接著會傳送該批次以進行處理和輸出。 批次時間間隔通常是以幾分之一秒定義。

![Spark Streaming](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStreams

Spark 串流會使用「離散化串流」** (DStream) 來表示連續資料流。 此 DStream 可以從事件中樞或 Kafka 等輸入來源建立，或將轉換套用至另一個 DStream 來建立 DStream。 當事件抵達 Spark 串流應用程式時，事件會以可靠的方式儲存。 也就是會複寫事件資料，讓多個節點擁有其複本。 這可確保任何單一節點的失敗都不會導致事件遺失。

Spark 核心會使用「彈性分散式資料集」**(RDD)。 RDD 會將資料分散於叢集中的多個節點，其中的每個節點通常都會在記憶體內部完整維護其資料，以達到最佳效能。 每個 RDD 代表在一段批次間隔內收集的事件。 當批次間隔消逝時，Spark 串流會產生一個新 RDD，其中包含該間隔內的所有資料。 這一組連續的 RDD 會被收集到 DStream。 Spark 串流應用程式會處理每個批次的 RDD 中儲存的資料。

![Spark DStream](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark 結構化串流作業

Spark 2.0 引進了 Spark 結構化串流，作為用於串流結構化資料的分析引擎。 Spark 結構化串流會使用 SparkSQL 批次引擎 API。 就像 Spark 串流一樣，Spark 結構化串流會執行其計算，而不會持續抵達資料的微批次。 Spark 結構化串流代表會以內含無限多個資料列的輸入資料表來表示資料流。 也就是說，輸入資料表會隨新資料送達而持續成長。 此輸入資料表會由長時間執行的查詢持續進行處理，而結果會寫出至輸出資料表。

![Spark 結構化串流](./media/apache-spark-streaming-high-availability/structured-streaming.png)

在結構化串流中，資料會送達系統並立即內嵌到輸入資料表中。 您可撰寫查詢來對此輸入資料表執行作業。 查詢輸出會產生稱為「結果資料表」的其他資料表。 結果資料表包含您的查詢結果，您可以從中提取資料以傳送至外部資料存放區 (如關聯式資料庫)。 「觸發間隔」** 可設定從輸入資料表處理資料的時機。 根據預設，結構化串流會在資料送達時立即進行處理。 不過，您也可將觸發程序設定為以較長的間隔執行，從而以時間為基礎的批次方式處理串流資料。 每次有新資料時，都可能會重新整理結果資料表中的資料，使其包含自串流查詢開始後的所有輸出資料（*完整模式*），或只包含自上次處理查詢之後的新資料（*附加模式*）。

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>建立容錯的 Spark 串流作業

若要為您的 Spark 串流作業建立高度可用的環境，請先撰寫個別作業的程式碼，以便在發生失敗時進行復原。 此種自我復原作業具有容錯特性。

Rdd 有數個可協助高可用性和容錯 Spark 串流作業的屬性：

* 儲存在 RDD 作為 DStream 的輸入資料批次會自動在記憶體中複寫，以供容錯之用。
* 您可以從不同背景工作角色的複寫輸入資料重新計算資料遺失的原因，只要這些背景工作角色節點可供使用。
* 由於可透過記憶體中運算，從錯誤/落後的執行緒執行復原，因此一秒內即可執行錯誤快速復原。

### <a name="exactly-once-semantics-with-spark-streaming"></a>使用 Spark 串流正好一次的語義

若要建立可處理每個事件一次 (且僅只一次) 的應用程式，請考慮在發生問題後如何重新啟動所有系統故障點，以及如何避免遺失資料。 只有一次的語義需要在任何時間點都不會遺失任何資料，而且不論發生失敗的位置為何，都能重新開機訊息處理。 請參閱[使用剛好一次的事件處理來建立 Spark 串流作業](apache-spark-streaming-exactly-once.md)。

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark 串流和 Apache Hadoop YARN

在 HDInsight 中，叢集工作是由 *Yet Another Resource Negotiator* (YARN) 協調。 設計高可用性的 Spark 串流時包含 Spark 串流技術，以及 YARN 元件技術。  使用 YARN 的組態範例如下所示。

![YARN 架構](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

下列各節說明此組態的設計考量。

### <a name="plan-for-failures"></a>針對失敗進行規劃

若要建立高可用性的 YARN 組態，您應針對可能的執行程式或驅動程式失敗進行規劃。 有些 Spark 串流作業也包含需要額外設定和安裝的資料保證需求。 例如，串流應用程式可能有零資料遺失保證的業務需求，儘管主控串流系統或 HDInsight 叢集中發生任何錯誤時也是如此。

如果**執行**程式失敗，Spark 會自動重新開機其工作和接收者，因此不需要進行任何設定變更。

不過，如果**驅動程式**失敗，則其所有相關聯的執行程式都會失敗，而且會失去所有收到的區塊和運算結果。 若要從驅動程式失敗中復原，請使用*DStream 檢查點*，如[建立僅限一次事件處理的 Spark 串流作業](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers)中所述。 DStream 檢查點檢查會定期將 DStreams 的「有向非循環圖」** (DAG) 儲存至容錯儲存體，例如 Azure 儲存體。  檢查點檢查可讓 Spark 結構化串流從檢查點資訊重新啟動失敗的驅動程式。  重新啟動驅動程式即可啟動新的執行程式，也可重新啟動接收器。

若要使用 DStream 檢查點檢查來復原驅動程式：

* 使用 `yarn.resourcemanager.am.max-attempts` 組態設定，在 YARN 上設定驅動程式自動重新啟動。
* 使用 `streamingContext.checkpoint(hdfsDirectory)`，在 HDFS 相容的檔案系統中設定檢查點目錄。
* 重新建構原始程式碼以使用檢查點進行復原，例如：

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* 藉由使用 `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` 來啟用預寫記錄檔 (WAL)，設定遺失的資料復原，並使用 `StorageLevel.MEMORY_AND_DISK_SER` 來停用輸入 DStream 的記憶體中複寫。

總而言之，使用檢查點 + WAL + 可靠的接收者，您將能夠傳遞「至少一次」的資料復原：

* 剛好一次，只要收到的資料不會遺失，而且輸出為等冪或交易式。
* 使用新的 Kafka 直接方法一次，這會使用 Kafka 做為複寫的記錄，而不是使用接收者或 Wal。

### <a name="typical-concerns-for-high-availability"></a>高可用性的一般考量

* 比起批次工作，更難以監視串流作業。 Spark 串流作業通常會長時間執行，而且 YARN 會等到作業完成才彙總記錄。  Spark 檢查點會在應用程式或 Spark 升級期間遺失，您必須在升級期間清除檢查點目錄。

* 將您的 YARN 叢集模式設定為即使用戶端失敗也會執行驅動程式。 若要設定驅動程式自動重新啟動：

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark 和 Spark 串流 UI 都有可設定的計量系統。 您也可以使用其他程式庫（例如 Graphite/Grafana）來下載儀表板計量，例如「已處理的記錄數」、「驅動 & 程式上的記憶體/GC 使用量」、「總延遲」、「叢集的使用率」等等。 在結構化串流 2.1 版或更新版本中，您可以使用 `StreamingQueryListener` 來收集其他計量。

* 您應該分割長時間執行的作業。  將 Spark 串流應用程式提交至叢集時，必須定義執行作業的 YARN 佇列。 您可以使用 [YARN 容量排程器](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)，將長時間執行的作業提交至不同的佇列。

* 正常關閉串流應用程式。 如果已知您的位移，且所有應用程式狀態都儲存在外部，您可以透過程式設計方式在適當位置停止串流應用程式。 其中有個技巧是在 Spark 中使用「執行緒勾點」，做法是每隔 *n* 秒檢查一次外部旗標。 您也可以使用啟動應用程式時在 HDFS 上建立的「標記檔」**，然後在想要停止時移除。 若採用標記檔方法，請在 Spark 應用程序中使用個別的執行緒來呼叫類似下面的程式碼：

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>後續步驟

* [Apache Spark 串流概觀](apache-spark-streaming-overview.md)
* [建立只處理一次事件的 Apache Spark 串流作業](apache-spark-streaming-exactly-once.md)
* [YARN 上長時間執行的 Apache Spark 串流作業](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) (英文)
* [結構化串流：容錯語意](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [離散化串流：可擴充串流處理的容錯模型](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
