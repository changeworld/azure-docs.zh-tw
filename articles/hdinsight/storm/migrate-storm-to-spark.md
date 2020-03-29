---
title: 將 Azure HDInsight 3.6 阿帕奇風暴遷移到 HDInsight 4.0 阿帕奇火花
description: 用於將 Apache Storm 工作負載遷移到 Spark 流式處理或 Spark 結構化流的差異和遷移流。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157788"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>將 Azure HDInsight 3.6 阿帕奇風暴遷移到 HDInsight 4.0 阿帕奇火花

本文檔介紹如何在 HDInsight 3.6 上將 Apache 風暴工作負載遷移到 HDInsight 4.0。 HDInsight 4.0 不支援 Apache 風暴群集類型，您需要遷移到另一個流資料平臺。 兩個合適的選項是 Apache Spark 流式處理和 Spark 結構化流式處理。 本文檔介紹這些平臺之間的差異，並推薦用於遷移 Apache Storm 工作負載的工作流。

## <a name="storm-migration-paths-in-hdinsight"></a>HDInsight 中的風暴遷移路徑

如果要在 HDInsight 3.6 上從 Apache 風暴遷移，您有多種選項：

* HDInsight 4.0 上的火花流
* HDInsight 4.0 上的火花結構化流式處理
* Azure 串流分析

本文檔提供了從 Apache 風暴遷移到 Spark 流和火花結構化流的指南。

> [!div class="mx-imgBorder"]
> ![HDInsight 風暴遷移路徑](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>阿帕奇風暴和火花流的比較，火花結構化流

Apache Storm 可以提供不同程度的訊息處理保證。 例如，基本的 Storm 應用程式可以保證至少處理一次，而 [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) 可以保證只處理一次。 Spark 流式處理和 Spark 結構化流可確保任何輸入事件完全處理一次，即使發生節點故障也是如此。 Storm 具有處理每個單個事件的模型，您還可以將微型批次處理模型與三叉星一起使用。 Spark 流式處理和火花結構化流提供微批次處理模型。

|  |Storm |Spark 串流 | 火花結構化流|
|---|---|---|---|
|**事件處理保證**|至少一次 <br> 正好一次（三叉） |[正好一次](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[正好一次](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**處理模型**|即時 <br> 微型批次（三叉） |微型批次 |微型批次 |
|**事件時間支援**|[是](https://storm.apache.org/releases/2.0.0/Windowing.html)|否|[是](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**語言**|JAVA 等|斯卡拉， 爪哇， Python|Python， R， Scala， JAVA， SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>火花流與 Spark 結構化流

火花結構化流正在取代火花流 （DStreams）。 結構化流將繼續接收增強和維護，而 DStreams 將僅處於維護模式。 **注意：需要連結來強調這一點**。 結構化流棧的功能不如它支援的源和接收器的 DStreams 多，因此請評估您的要求，以選擇適當的 Spark 流處理選項。

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>流式處理（單事件）處理與微批次處理處理

Storm 提供了處理每個單個事件的模型。 這意味著所有傳入的記錄一到達即會立即處理。 Spark 串流應用程式必須等待幾毫秒，才可收集每個微批次事件，然後才能傳送該批次以進行處理。 相較之下，事件驅動的應用程式會立即處理每個事件。 而 Spark 串流通常會延遲幾秒鐘。 微批次方法的優點是可更有效率的處理資料和更簡易的彙總運算。

> [!div class="mx-imgBorder"]
> ![流式處理和微批次處理](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>風暴架構和元件

Storm 拓撲是由有向非循環圖 (DAG) 中排列的多個元件所組成。 圖形中元件之間的資料流程。 每個元件會取用一或多個資料流，並可選擇性地發出一或多個資料流。

|元件 |描述 |
|---|---|
|壺 嘴|將資料引入拓撲。 它們會將一或多個串流發出至拓撲。|
|螺栓|消耗從噴口或其他螺栓發出的流。 Bolt 可以選擇性地將串流發出至拓撲。 Bolt 也負責將資料寫入外部服務或儲存體，例如 HDFS、Kafka 或 HBase。|

> [!div class="mx-imgBorder"]
> ![風暴成分的相互作用](./media/migrate-storm-to-spark/apache-storm-components.png)

風暴由以下三個守護進程組成，它們使風暴群集保持正常運行。

|精靈 |描述 |
|---|---|
|Nimbus|與 Hadoop JobTracker 類似，它負責在群集周圍分發代碼，並將任務分配給電腦並監視故障。|
|Zookeeper|用於群集協調。|
|監督員|偵聽分配給其機器的工作，並根據 Nimbus 的指令啟動和停止工作流程。 每個輔助角色進程執行拓撲的子集。 使用者的應用程式邏輯（Souts 和螺栓）在此處運行。|

> [!div class="mx-imgBorder"]
> ![尼姆布斯，動物園管理員和主管守護神](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark 流式處理架構和元件

以下步驟總結了元件在 Spark 流 （DStreams） 和 Spark 結構化流中如何協同工作：

* 啟動 Spark 流式處理時，驅動程式將啟動執行器中的任務。
* 執行器從流資料來源接收流。
* 當執行器接收資料流程時，它會將流拆分為塊，並將它們保存在記憶體中。
* 資料塊將複製到其他執行器。
* 然後，處理後的資料存儲在目標資料存儲中。

> [!div class="mx-imgBorder"]
> ![火花流路徑輸出](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>火花流 （DStream） 工作流

當每個批次間隔過去後，新的 RDD 就會產生，並包含該間隔中的所有資料。 連續的 RDD 集被收集到 DStream 中。 例如，如果批次間隔長度為一秒，您的 DStream 就會每秒發出包含一個 RDD 的批次，該 RDD 會包含在這一秒期間內嵌的所有資料。 處理 DStream 時，溫度事件就會出現在這些批次的其中一個。 Spark 串流應用程式會處理包含事件的批次，並在最後處理儲存在每個 RDD 中的資料。

> [!div class="mx-imgBorder"]
> ![火花流處理批次](./media/migrate-storm-to-spark/spark-streaming-batches.png)

有關 Spark 流式處理可用的不同轉換的詳細資訊，請參閱[DStreams 上的轉換](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)。

## <a name="spark-structured-streaming"></a>Spark 結構化串流

Spark 結構化流表示資料流程作為未綁定深度的表。 隨著新資料的到來，該表將繼續增長。 此輸入表由長時間運行的查詢持續處理，並將結果發送到輸出表。

在結構化串流中，資料會送達系統並立即內嵌到輸入資料表中。 您可撰寫查詢 (使用 DataFrame 和資料集 API) 來對此輸入資料表執行作業。

查詢輸出生成*結果表*，其中包含查詢的結果。 可以從外部資料存儲（此類關係資料庫）的結果表中繪製資料。

「觸發間隔」會控制從輸入資料表處理資料的時機。 根據預設，觸發間隔為零，所以結構化串流會在資料到達時立即嘗試處理資料。 實際上，這表示只要是結構化串流處理完前一個查詢的執行後，就會針對任何新收到的資料啟動另一個處理執行。 您可以將觸發程序設定為依據間隔執行，從而以時間為基礎的批次方式處理串流資料。

> [!div class="mx-imgBorder"]
> ![結構化流中資料處理](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![結構化流程式設計模型](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>一般遷移流

建議的從 Storm 到 Spark 的遷移流假定以下初始體系結構：

* 卡夫卡用作流資料來源
* 卡夫卡和風暴部署在同一虛擬網路上
* Storm 處理的資料將寫入資料接收器，例如 Azure 存儲或 Azure 資料湖存儲 Gen2。

    > [!div class="mx-imgBorder"]
    > ![假定當前環境圖](./media/migrate-storm-to-spark/presumed-current-environment.png)

要將應用程式從 Storm 遷移到 Spark 流 API 之一，請執行以下操作：

1. **部署新群集。** 在同一虛擬網路中部署新的 HDInsight 4.0 Spark 群集，並在其中部署 Spark 流式處理或 Spark 結構化流式處理應用程式並對其進行徹底測試。

    > [!div class="mx-imgBorder"]
    > ![HDInsight 中的新火花部署](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **停止在舊的風暴群集上使用。** 在現有 Storm 中，停止使用來自流資料來源的資料，然後等待資料完成寫入目標接收器。

    > [!div class="mx-imgBorder"]
    > ![停止在當前群集上使用](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **開始在新的 Spark 群集上使用。** 從新部署的 HDInsight 4.0 Spark 群集開始資料流資料。 此時，該過程通過從最新的 Kafka 偏移量中消耗來接管。

    > [!div class="mx-imgBorder"]
    > ![開始在新群集上使用](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **根據需要刪除舊群集。** 交換器完成並正常工作後，根據需要刪除舊的 HDInsight 3.6 風暴群集。

    > [!div class="mx-imgBorder"]
    > ![根據需要刪除舊的 HDInsight 群集](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>後續步驟

有關風暴、火花流和 Spark 結構化流的詳細資訊，請參閱以下文檔：

* [Apache Spark 串流概觀](../spark/apache-spark-streaming-overview.md)
* [Apache Spark 結構化串流的概觀](../spark/apache-spark-structured-streaming-overview.md)