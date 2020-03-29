---
title: Azure HDInsight 中的大規模串流
description: 如何在 Azure HDInsight 中使用可擴展的 Apache 群集的資料流程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 006310f1a0efa69881bbe6d6ea4403b9c50402e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435384"
---
# <a name="streaming-at-scale-in-hdinsight"></a>HDInsight 中的大規模串流

即時大資料解決方案可對正在啟動的資料執行操作。 通常此資料是在抵達時最具價值。 如果傳入資料流變大到無法在該時間處理的程度，您可能就需要為資源進行節流。 或者，HDInsight 叢集也可以視需要新增節點來擴大規模，以配合您的串流解決方案。

在串流應用程式中，一或多個資料來源會產生事件 (有時每秒會有數百萬個事件)，系統必須在不捨棄任何有用資訊的情況下快速消化這些事件。 處理連入事件時，會由服務 (例如 [Apache Kafka](kafka/apache-kafka-introduction.md) 或[事件中樞](https://azure.microsoft.com/services/event-hubs/)) 使用「串流緩衝處理」** (也稱為「事件佇列」**) 來處理。 在您收集事件之後，可以接著使用「串流處理」** 層 內的即時分析系統 (例如 [Apache Storm](storm/apache-storm-overview.md) 或 [Apache Spark 串流](spark/apache-spark-streaming-overview.md)) 來分析資料。 處理過的資料可以儲存在長期的儲存體系統中 (例如 [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/))，並即時顯示在商業智慧儀表板上 (例如 [Power BI](https://powerbi.microsoft.com)、Tableau 或自訂網頁)。

![Azure HDInsight 流模式](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka 提供一個高輸送量、低延遲的訊息佇列服務，且現在已包含在 Apache 的開放原始碼軟體 (OSS) 套件中。 Kafka 使用一種發佈和訂閱傳訊模型，並且會將已分割資料的串流安全地儲存在分散式、複寫的叢集中。 Kafka 會隨著輸送量的增加以線性方式調整規模。

如需詳細資訊，請參閱 [HDInsight 上的 Apache Kafka 簡介](kafka/apache-kafka-introduction.md)。

## <a name="apache-storm"></a>Apache Storm

Apache Storm 是一個容錯的分散式開放原始碼計算系統，此系統已經過最佳化，可搭配 Hadoop 來即時處理資料流。 連入事件的資料核心單位是 Tuple，這是一組不可變的索引鍵/值組。 這些 Tuple 的無限序列會形成來自 Spout 的串流。 Spout 會包裝串流資料來源 (例如 Kafka)，然後發出 Tuple。 Storm 拓撲是這些串流上的一連串轉換。

如需詳細資訊，請參閱[什麼是 Apache Storm on Azure HDInsight？](storm/apache-storm-overview.md)。

## <a name="spark-streaming"></a>Spark Streaming

Spark Streamin 是 Spark 的延伸，可讓您重複使用用於執行批次處理的相同程式碼。 您可以將批次和互動式查詢結合在相同的應用程式中。 與 Storm 不同，Spark 流提供一次處理語義的有狀態。 當與[Kafka 直接 API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html)結合使用時，確保 Spark Streaming 一次性接收所有 Kafka 資料，因此可以精確實現一次保證的端到端資料。 Spark Streaming 的其中一個優點就是容錯功能，可在叢集內使用多個節點的情況下，快速復原發生錯誤的節點。

如需詳細資訊，請參閱[什麼是 Apache Spark 串流？](hdinsight-spark-streaming-overview.md)。

## <a name="scaling-a-cluster"></a>調整叢集規模

雖然您可以在建立叢集時指定叢集中的節點數，但您可以擴大或縮小叢集來配合工作負載。 所有 HDInsight 叢集都允許您[變更叢集中的節點數目](hdinsight-administer-use-portal-linux.md#scale-clusters)。 因為所有資料都儲存在 Azure 儲存體或 Data Lake Storage 中，所以您可以在不遺失資料的情況下卸除 Spark 叢集。

脫鉤技術有一些優點。 例如，Kafka 是一種事件緩衝技術，因此它非常耗費 IO，不需要太多的處理能力。 相較之下，串流處理器 (例如 Spark Streaming) 則需要大量計算，因此需要較強大的 VM。 藉由讓這些技術脫鉤，分別置於不同的叢集中，您既可以個別調整它們，同時也可以將 VM 做最佳運用。

### <a name="scale-the-stream-buffering-layer"></a>調整串流緩衝處理層規模

「事件中樞」和 Kafka 這兩種串流緩衝處理技術都使用分割區，而取用者則是會從這些分割區讀取資料。 調整輸入輸送量需要上調分割區的數目，而新增分割區將可提升平行處理程度。 在事件中心中，部署後無法更改分區計數，因此從目標比例開始非常重要。 使用卡夫卡，即使卡夫卡正在處理資料，也可以[添加分區](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)。 Kafka 提供一個可重新指派分割區的工具 `kafka-reassign-partitions.sh`。 HDInsight 提供了一個[分區副本重新平衡工具](https://github.com/hdinsight/hdinsight-kafka-tools) `rebalance_rackaware.py`。 這個重新平衡工具會呼叫 `kafka-reassign-partitions.sh` 工具，讓每個複本都在個別的容錯網域和更新網域中，使得 Kafka 產生機架感知並提升容錯能力。

### <a name="scale-the-stream-processing-layer"></a>調整串流處理層規模

Apache Storm 和 Spark Streaming 都支援在其叢集中新增背景工作節點，甚至是在處理資料時也支援。

若要利用透過調整 Storm 規模而新增的新節點，您必須重新平衡在叢集大小增加前所啟動的所有 Storm 拓撲。 您可以使用 Storm Web UI 或其 CLI 來執行這項重新平衡作業。 如需詳細資訊，請參閱 [Apache Storm 文件](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) \(英文\)。

Apache Spark 可根據應用程式需求，使用下列三個主要參數來設定其環境：`spark.executor.instances`、`spark.executor.cores` 及 `spark.executor.memory`。 *executor* 是針對 Spark 應用程式啟動的程序。 executor 會在背景工作節點上執行，並負責執行應用程式的工作。 executor 的預設數目和每個叢集的 executor 大小，是根據背景工作節點數目和背景工作節點大小計算而得。 這些數目會儲存在每個叢集前端節點上的 `spark-defaults.conf` 檔案中。

您可以在叢集層級針對在叢集上執行的所有應用程式設定這三個組態參數，也可以針對每個個別應用程式指定這些參數。 如需詳細資訊，請參閱[管理 Apache Spark 的資源](spark/apache-spark-resource-manager.md)。

## <a name="next-steps"></a>後續步驟

* [建立和監視 Azure HDInsight 中的 Apache Storm 拓撲](storm/apache-storm-quickstart.md)
* [Apache Storm on HDInsight 的範例拓撲](storm/apache-storm-example-topology.md)
* [HDInsight 上的 Apache Spark 簡介](spark/apache-spark-overview.md)
* [開始使用 Apache Kafka on HDInsight](kafka/apache-kafka-get-started.md)
