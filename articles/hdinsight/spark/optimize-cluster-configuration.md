---
title: 最佳化 Apache Spark 叢集設定 - Azure HDInsight
description: 了解如何設定您的 Apache Spark 叢集，以最大化 Azure HDInsight 的輸送量。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 464f0dcab3debf92605d2f13be9b25ece63f4bf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737677"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Apache Spark 的叢集設定優化

本文討論如何最佳化 Apache Spark 叢集的設定，讓 Azure HDInsight 發揮最佳效能。

## <a name="overview"></a>概觀

端視您的 Spark 叢集工作負載而定，您可能會決定非預設 Spark 設定達到更最佳化的 Spark 作業執行。  使用範例工作負載進行基準測試，以驗證任何非預設叢集設定。

以下是您可以調整的一些常見參數：

|參數 |描述 |
|---|---|
|--num-executors|設定適當數量的執行程式。|
|--executor-cores|設定每個執行程式的核心數目。 一般而言，您應該有中型執行程式，因為其他處理序會耗用一些可用的記憶體。|
|--executor-memory|設定每個執行程式的記憶體大小，控制 YARN 的堆積大小。 對於執行額外負荷保留一些記憶體。|

## <a name="select-the-correct-executor-size"></a>選取正確的執行程式大小

決定執行程式設定時，請考慮 Java 記憶體回收 (GC) 額外負荷。

* 減少執行程式大小的因素：
    1. 將堆積大小減少到 32 GB 以下，使 GC 額外負荷低於 10%。
    2. 減少核心數，使 GC 額外負荷低於 10%。

* 增加執行程式大小的因素：
    1. 減少執行程式之間的通訊額外負擔。
    2. 減少較大叢集 (大於 100 個執行程式) 上的執行程式 (N2) 之間的開啟連接數。
    3. 增加堆積大小以配合需要大量記憶體的工作。
    4. 選擇性：減少每個執行程式的記憶體額外負荷。
    5. 選擇性：透過超載使用 CPU 以增加使用率和並行處理。

選取執行程式大小時的一般規則是：

1. 首先每個執行程式 30 GB，並發佈可用的機器核心。
2. 增加較大叢集 (> 100 個執行程式) 的執行程式核心數。
3. 根據試用狀況和前述因素 (例如 GC 額外負荷) 修改大小。

執行並行查詢時，請考慮：

1. 首先每個執行程式和所有機器核心 30 GB。
2. 超載使用 CPU (大約延遲提升 30%) 來建立多個平行 Spark 應用程式。
3. 分散平行應用程式的查詢。
4. 根據試用狀況和前述因素 (例如 GC 額外負荷) 修改大小。

如需使用 Ambari 設定執行程式的詳細資訊，請參閱 [Apache Spark 設定 - Spark 執行程式](apache-spark-settings.md#configuring-spark-executors)。

藉由查看時間軸檢視等等，監視極端值的查詢效能或其他效能問題。 同時也查看 SQL graph、作業統計資料等等。 如需有關使用 YARN 和 Spark 記錄伺服器對 Spark 進行偵錯的詳細資訊，請參閱[對在 Azure HDInsight 上執行的 Apache Spark 作業進行偵錯](apache-spark-job-debugging.md)。 如需使用 YARN 時間軸伺服器的秘訣，請參閱[存取 Apache Hadoop YARN 應用程式記錄檔](../hdinsight-hadoop-access-yarn-app-logs-linux.md)。

有時一或數個執行程式比其他執行程式慢，而且工作花較長的執行時間。 這種緩慢的情況通常發生在較大的叢集 (> 30 個節點) 上。 在此情況下，將工作分割為較多的工作，讓排程程式彌補緩慢的工作。 例如，應用程式中的核心數目至少是工作數目的兩倍。 您也可以使用 `conf: spark.speculation = true` 啟用工作的推測性執行。

## <a name="next-steps"></a>後續步驟

* [最佳化 Apache Spark 的資料處理](optimize-cluster-configuration.md)
* [最佳化 Apache Spark 的資料儲存體](optimize-data-storage.md)
* [最佳化 Apache Spark 的記憶體使用量](optimize-memory-usage.md)
