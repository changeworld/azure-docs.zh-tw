---
title: 最佳化 Apache Spark 的資料儲存體 - Azure HDInsight
description: 了解如何將資料儲存體最佳化，以與 Azure HDInsight 上的 Apache Spark 搭配使用。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: ad09cdc2c0054c9d9a58e6bfa00252862f1e8c0f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028218"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Apache Spark 的資料儲存體優化

本文討論將資料儲存體最佳化的策略，以便在 Azure HDInsight 上有效率地執行 Apache Spark 作業。

## <a name="overview"></a>概觀

Spark 支援許多格式，例如 csv、json、xml、parquet、orc 和 avro。 Spark 可以擴充來支援外部資料來源的多種格式，如需詳細資訊，請參閱 [Apache Spark 封裝](https://spark-packages.org)。

效能的最佳格式是 *snappy 壓縮* 的 parquet，這是 Spark 2.x 的預設格式。 Parquet 以單欄式格式儲存資料，而且在 Spark 中高度最佳化。

## <a name="choose-data-abstraction"></a>選擇資料抽象

舊版 Spark 使用 RDD 來提取資料，Spark 1.3 和 1.6 分別導入了 DataFrame 和 DataSet。 請考慮下列的相對優勢：

* **DataFrames**
    * 大部分情況下的最佳選擇。
    * 透過 Catalyst 提供查詢最佳化。
    * 整體階段程式碼產生。
    * 直接記憶體存取。
    * 低記憶體回收 (GC) 額外負荷。
    * 不像 DataSets 適合開發人員使用，因為沒有任何編譯時間檢查或網域物件程式設計。
* **DataSets**
    * 適合可接受效能影響的複雜 ETL 管線。
    * 不適合效能影響可能相當大的彙總。
    * 透過 Catalyst 提供查詢最佳化。
    * 提供網域物件程式設計和編譯時間檢查，因此適合開發人員使用。
    * 新增序列化/還原序列化額外負荷。
    * 高 GC 額外負荷。
    * 中斷整體階段程式碼產生。
* **RDDs**
    * 除非您需要建立新的自訂 RDD，否則不需要使用 RDD。
    * 沒有透過 Catalyst 的任何查詢最佳化。
    * 沒有整體階段程式碼產生。
    * 高 GC 額外負荷。
    * 必須使用 Spark 1.x 舊版 API。

## <a name="select-default-storage"></a>選取預設儲存體

您建立新的 Spark 叢集時，可以選取 Azure Blob 儲存體或 Azure Data Lake Storage 作為叢集的預設儲存體。 這兩個選項都可讓您取得暫時性叢集的長期儲存體優勢。 如此一來，當您刪除叢集時，您的資料就不會自動刪除。 您可以重新建立暫時性叢集，而且仍然可以存取您的資料。

| 存放區類型 | 檔案系統 | 速度 | 暫時性 | 使用案例 |
| --- | --- | --- | --- | --- |
| Azure Blob 儲存體 | **wasb:** //url/ | **Standard** | 是 | 暫時性叢集 |
| Azure Blob 儲存體 (安全) | **wasbs:** //url/ | **Standard** | 是 | 暫時性叢集 |
| Azure Data Lake Storage Gen 2| **abfs:** //url/ | **更快** | 是 | 暫時性叢集 |
| Azure Data Lake Storage Gen 1| **adl:** //url/ | **更快** | 是 | 暫時性叢集 |
| 本機 HDFS | **hdfs:** //url/ | **最快** | 否 | 互動式全天候叢集 |

如需儲存體選項的完整說明，請參閱[比較與 Azure HDInsight 叢集搭配使用的儲存體選項](../hdinsight-hadoop-compare-storage-options.md)。

## <a name="use-the-cache"></a>使用快取

Spark 提供本身的原生快取機制，可透過 `.persist()`、`.cache()` 和 `CACHE TABLE` 等不同的方式使用。 對於較小型資料集，以及需要快取中繼結果的 ETL 管線，此原生快取相當有效。 不過，Spark 原生快取目前不適用於資料分割，因為快取的資料表不會保留分割資料。 較通用且可靠的快取技術是「儲存層快取」。

* 原生 Spark 快取 (不建議使用)
    * 適用於小型資料集。
    * 不使用分割區，這種情況可能在未來的 Spark 版本中改變。

* 儲存層級快取 (建議使用)
    * 可以在 HDInsight 上使用 [IO 快取](apache-spark-improve-performance-iocache.md)功能來實作。
    * 使用記憶體內和 SSD 快取。

* 本機 HDFS (建議)
    * `hdfs://mycluster` 路徑。
    * 使用 SSD 快取。
    * 您刪除叢集時，快取的資料將遺失，因此需要重建快取。

## <a name="optimize-data-serialization"></a>最佳化資料序列化

Spark 作業相當分散，因此適當資料序列化對於達到最佳效能相當重要。  Spark 序列化有兩個選項：

* 預設為 Java 序列化。
* `Kryo` 序列化是較新的格式，可進行比 Java 更快且更精簡的序列化。  `Kryo` 會要求您註冊程式中的類別，而且不支援所有可序列化的類別。

## <a name="use-bucketing"></a>使用貯體

貯體類似於資料分割。 但是每個貯體都可以保存一組資料行值，而非只是其中一個值。 此方法適用於大量 (數百萬個以上) 值的資料分割，例如產品識別碼。 貯體是由資料列貯體索引鍵的雜湊所決定。 貯體資料表提供唯一的最佳化，因為這些資料表會儲存有關如何將資料表儲存和排序的中繼資料。

一些進階貯體功能如下：

* 以中繼資訊的貯體為基礎的查詢最佳化。
* 最佳化彙總。
* 最佳化聯結。

您可以同時使用資料分割和貯體。

## <a name="next-steps"></a>後續步驟

* [最佳化 Apache Spark 的資料處理](optimize-cluster-configuration.md)
* [最佳化 Apache Spark 的記憶體使用量](optimize-memory-usage.md)
* [最佳化 Apache Spark 的叢集設定](optimize-cluster-configuration.md)
