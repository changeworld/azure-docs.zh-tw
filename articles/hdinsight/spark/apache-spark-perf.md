---
title: 最佳化 Spark 作業的效能 - Azure HDInsight
description: 在 Azure HDInsight 中顯示通用策略，以獲得最佳性能的 Apache Spark 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 3d8f4a28961be7e0ece517e00026d9711d8f67e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198866"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>在 HDInsight 中優化阿帕奇火花作業

了解如何特定工作負載最佳化 [Apache Spark](https://spark.apache.org/) 叢集設定。  最常見的挑戰是記憶體壓力，因為配置不當（尤其是大小錯誤的執行器）、長時間運行的操作以及導致笛卡爾操作的任務。 您可以使用適當的快取，並允許[資料扭曲](#optimize-joins-and-shuffles)，以便作業加速執行。 為了達到最佳效能，必須監視和檢閱長時間執行而且耗用資源的 Spark 作業執行。 有關在 HDInsight 上開始使用 Apache Spark 的資訊，請參閱[使用 Azure 門戶創建 Apache Spark 群集](apache-spark-jupyter-spark-sql-use-portal.md)。

下列各節描述常見的 Spark 作業最佳化和建議。

## <a name="choose-the-data-abstraction"></a>選擇資料抽象

早期的 Spark 版本使用 RDD 來抽象資料，Spark 1.3 和 1.6 分別引入了 DataFrame 和資料集集。 請考慮下列的相對優勢：

* **DataFrame**
    * 大部分情況下的最佳選擇。
    * 透過 Catalyst 提供查詢最佳化。
    * 整體階段程式碼產生。
    * 直接記憶體存取。
    * 低記憶體回收 (GC) 額外負荷。
    * 不像 DataSets 適合開發人員使用，因為沒有任何編譯時間檢查或網域物件程式設計。
* **資料**
    * 適合可接受效能影響的複雜 ETL 管線。
    * 不適合效能影響可能相當大的彙總。
    * 透過 Catalyst 提供查詢最佳化。
    * 提供網域物件程式設計和編譯時間檢查，因此適合開發人員使用。
    * 新增序列化/還原序列化額外負荷。
    * 高 GC 額外負荷。
    * 中斷整體階段程式碼產生。
* **RDDs**
    * 除非需要構建新的自訂 RDD，否則無需使用 RDD。
    * 沒有透過 Catalyst 的任何查詢最佳化。
    * 沒有整體階段程式碼產生。
    * 高 GC 額外負荷。
    * 必須使用 Spark 1.x 舊版 API。

## <a name="use-optimal-data-format"></a>使用最佳的資料格式

Spark 支援許多格式，例如 csv、json、xml、parquet、orc 和 avro。 Spark 可以擴充來支援外部資料來源的多種格式，如需詳細資訊，請參閱 [Apache Spark 封裝](https://spark-packages.org)。

效能的最佳格式是 *snappy 壓縮*的 parquet，這是 Spark 2.x 的預設格式。 Parquet 以單欄式格式儲存資料，而且在 Spark 中高度最佳化。

## <a name="select-default-storage"></a>選取預設儲存體

創建新 Spark 群集時，可以選擇 Azure Blob 存儲或 Azure 資料湖存儲作為群集的預設存儲。 這兩個選項都使您能夠為瞬態群集提供長期存儲的好處，因此刪除群集時不會自動刪除資料。 您可以重新建立暫時性叢集，而且仍然可以存取您的資料。

| 存放區類型 | 檔案系統 | 速度 | 暫時性 | 使用案例 |
| --- | --- | --- | --- | --- |
| Azure Blob 儲存體 | **wasb:**//url/ | **標準** | 是 | 暫時性叢集 |
| Azure Blob 存儲（安全） | **參數：**//url/ | **標準** | 是 | 暫時性叢集 |
| Azure Data Lake Storage Gen 2| **abfs：**//url/ | **更快** | 是 | 暫時性叢集 |
| Azure 資料存儲第 1 代| **adl:**//url/ | **更快** | 是 | 暫時性叢集 |
| 本機 HDFS | **hdfs:**//url/ | **最快** | 否 | 互動式全天候叢集 |

有關可用於 HDInsight 群集的存儲選項的完整說明，請參閱[比較與 Azure HDInsight 群集一起使用的存儲選項](../hdinsight-hadoop-compare-storage-options.md)。

## <a name="use-the-cache"></a>使用快取

Spark 提供本身的原生快取機制，可透過 `.persist()`、`.cache()` 和 `CACHE TABLE` 等不同的方式使用。 對於較小型資料集，以及需要快取中繼結果的 ETL 管線，此原生快取相當有效。 但是，Spark 本機緩存當前不能很好地用於分區，因為緩存的表不會保留分區資料。 較通用且可靠的快取技術是「儲存層快取」**。

* 原生 Spark 快取 (不建議使用)
    * 適用於小型資料集。
    * 不與分區配合使用，這可能會在將來的 Spark 版本中更改。

* 儲存層級快取 (建議使用)
    * 可以使用[IO 緩存](apache-spark-improve-performance-iocache.md)功能在 HDInsight 上實現。
    * 使用記憶體內和 SSD 快取。

* 本機 HDFS (建議)
    * `hdfs://mycluster` 路徑。
    * 使用 SSD 快取。
    * 您刪除叢集時，快取的資料將遺失，因此需要重建快取。

## <a name="use-memory-efficiently"></a>有效率地使用記憶體

Spark 的運作方式是將資料放入記憶體，因此管理記憶體資源視 Spark 工作執行最佳化的重要層面。  有數種技術可以有效使用叢集的記憶體。

* 偏好較小的資料分割區，並在分割策略中考量資料大小、類型和發佈。
* 考慮更新、更有效率的 [Kryo 資料序列化](https://github.com/EsotericSoftware/kryo)，而不是預設的 Java 序列化。
* 習慣使用 YARN，因為它會按批次區分 `spark-submit`。
* 監視和微調 Spark 組態設定。

下圖顯示 Spark 記憶體結構，以及部分索引鍵執行程式記憶體參數，以供您參考。

### <a name="spark-memory-considerations"></a>Spark 記憶體考量

如果您使用的是[Apache Hadoop YARN，](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)則 YARN 控制每個 Spark 節點上所有容器使用的最大記憶體總和。  下圖顯示索引鍵物件及其關聯性。

![YARN Spark 記憶體管理](./media/apache-spark-perf/apache-yarn-spark-memory.png)

若要處理「記憶體不足」訊息，請嘗試：

* 檢閱 DAG 管理重組。 以對應端減少、預先分割 (或貯體分類) 來源資料加以減少、最大化單一重組，並減少傳送的資料量。
* 偏好固定記憶體限制為 `GroupByKey` 的 `ReduceByKey`，這會提供彙總、視窗化和其他功能，但是有無限制的記憶體限制。
* 偏好對於執行程式或資料分割進行較多作業的 `TreeReduce`，而不偏好對於驅動程式進行所有作業的 `Reduce`。
* 運用資料框架，而非運用較低層級 RDD 物件。
* 建立封裝動作的 ComplexTypes，例如「前 N 項」、各種彙總或視窗化作業。

有關其他故障排除步驟，請參閱[Azure HDInsight 中 Apache Spark 的記憶體錯誤異常](apache-spark-troubleshoot-outofmemory.md)。

## <a name="optimize-data-serialization"></a>最佳化資料序列化

Spark 作業相當分散，因此適當資料序列化對於達到最佳效能相當重要。  Spark 序列化有兩個選項：

* 預設為 Java 序列化。
* Kryo 序列化是較新的格式，可進行比 Java 更快且更精簡的序列化。  Kryo 要求您在程式中註冊類，並且它尚未支援所有可序列化類型。

## <a name="use-bucketing"></a>使用貯體

貯體類似於資料分割，但是每個貯體都可以保存一組資料行值，而非其中一個值。 貯體適用於大量 (數百萬個以上) 值的資料分割，例如產品識別碼。 貯體是由資料列貯體索引鍵的雜湊所決定。 貯體資料表提供唯一的最佳化，因為這些資料表會儲存有關如何將資料表儲存和排序的中繼資料。

一些進階貯體功能如下：

* 以中繼資訊的貯體為基礎的查詢最佳化。
* 最佳化彙總。
* 最佳化聯結。

您可以同時使用資料分割和貯體。

## <a name="optimize-joins-and-shuffles"></a>最佳化聯結和重組

如果在聯結或重組有緩慢的作業，原因可能是「資料扭曲」**，也就是作業資料不對稱。 例如，對應作業可能需要 20 秒，但是執行連結或重組資料的作業需要數小時。 若要修正資料扭曲，您應該將整個索引鍵設定為 salt，或僅針對一部分的索引鍵使用「隔離 salt」**。 如果使用隔離鹽，則應進一步篩選以隔離地圖聯接中的鹽鍵子集。 另一個選項是導入貯體資料行並先在貯體中預先彙總。

造成緩慢聯結的另一個因素可能是聯結類型。 Spark 預設使用 `SortMerge` 聯結類型。 這種聯結最適用於大型資料集，但是佔用大量運算資源，因為它必須先將左邊和右邊的資料排序，再合併資料。

`Broadcast` 聯結最適合使用較小的資料集，或聯結的一端遠小於另一端的情況。 這種聯結會將一端廣播到所有的執行程式，因此一般而言需要較多記憶體用於廣播。

您也可以設定 `spark.sql.autoBroadcastJoinThreshold` 在您的設定中變更聯結類型，也可以使用資料框架 API (`dataframe.join(broadcast(df2))`) 設定聯結提示。

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

如果使用存儲桶表，則有第三個聯接類型，即`Merge`聯接。 經過正確預先資料分割和預先排序的資料集將略過 `SortMerge` 聯結中佔用大量資源的排序階段。

聯結順序相當重要，尤其是更複雜的查詢。 先從大多數選擇性聯結開始。 此外，盡可能移動彙總之後會增加資料列數目的聯結。

要管理笛卡爾聯接的並行性，可以添加嵌套結構、視窗，並可能跳過 Spark 作業中的一個或多個步驟。

## <a name="customize-cluster-configuration"></a>自訂叢集設定

端視您的 Spark 叢集工作負載而定，您可能會決定非預設 Spark 設定達到更最佳化的 Spark 作業執行。  使用範例工作負載執行基準測試，以驗證任何非預設叢集設定。

以下是您可以調整的一些常見參數：

* `--num-executors` 設定適當數量的執行程式。
* `--executor-cores` 設定每個執行程式的核心數目。 一般而言，您應該有中型執行程式，因為其他處理序會耗用一些可用的記憶體。
* `--executor-memory` 設定每個執行程式的記憶體大小，控制 YARN 的堆積大小。 您應該對於執行額外負荷保留一些記憶體。

### <a name="select-the-correct-executor-size"></a>選取正確的執行程式大小

決定執行程式設定時，請考慮 Java 記憶體回收 (GC) 額外負荷。

* 減少執行程式大小的因素：
    1. 將堆積大小減少到 32 GB 以下，使 GC 額外負荷低於 10%。
    2. 減少核心數，使 GC 額外負荷低於 10%。

* 增加執行程式大小的因素：
    1. 減少執行程式之間的通訊額外負擔。
    2. 減少較大叢集 (大於 100 個執行程式) 上的執行程式 (N2) 之間的開啟連接數。
    3. 增加堆積大小以配合需要大量記憶體的工作。
    4. 選用：減少每個執行程式的記憶體額外負荷。
    5. 選用：超載使用 CPU 以增加使用率和並行。

依照一般經驗法則，選取執行程式大小時：

1. 首先每個執行程式 30 GB，並發佈可用的機器核心。
2. 增加較大叢集 (> 100 個執行程式) 的執行程式核心數。
3. 根據試運行和上述因素（如 GC 開銷）修改大小。

執行並行查詢時，請考慮下列各項：

1. 首先每個執行程式和所有機器核心 30 GB。
2. 超載使用 CPU (大約延遲提升 30%) 來建立多個平行 Spark 應用程式。
3. 分散平行應用程式的查詢。
4. 根據試運行和上述因素（如 GC 開銷）修改大小。

有關使用 Ambari 配置執行器的詳細資訊，請參閱[Apache Spark 設置 - Spark 執行器](apache-spark-settings.md#configuring-spark-executors)。

藉由查看時間軸檢視、SQL 圖形、作業的統計資料等等，監視極端值的查詢效能或其他效能問題。 有關使用 YARN 和 Spark 歷史記錄伺服器調試 Spark 作業的資訊，請參閱[在 Azure HDInsight 上運行的調試 Apache Spark 作業](apache-spark-job-debugging.md)。 有關使用 YARN 時間表伺服器的提示，請參閱[訪問 Apache Hadoop YARN 應用程式日誌](../hdinsight-hadoop-access-yarn-app-logs-linux.md)。

有時一或數個執行程式比其他執行程式慢，而且工作花較長的執行時間。 較大的叢集 (> 30 個節點) 通常會發生這種情況。 在此情況下，將工作分割為較多的工作，讓排程程式彌補緩慢的工作。 例如，應用程式中的核心數目至少是工作數目的兩倍。 您也可以使用 `conf: spark.speculation = true` 啟用工作的推測性執行。

## <a name="optimize-job-execution"></a>最佳化作業執行

* 必要時進行快取，例如，如果您使用資料兩次，則快取資料。
* 將變數廣播到所有執行程式。 變數只會序列化一次，因此查閱更快。
* 使用驅動程式上的執行緒集區，會使許多工作的作業加快。

定期監視執行中工作的效能問題。 如果您需要深入了解特定問題，請考慮下列其中一個效能分析工具：

* [Intel PAL Tool](https://github.com/intel-hadoop/PAT) 可監視 CPU、儲存和網路頻寬使用率。
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) 可分析 Spark 和執行程式的程式碼。

Spark 2.x 查詢效能的關鍵是 Tungsten 引擎，這取決於整體階段程式碼產生。 在某些情況下，可能會停用整體階段程式碼產生。 例如，如果您在彙總運算式中使用不可變類型 (`string`)，會出現 `SortAggregate` 而非 `HashAggregate`。 例如，為了提升效能，請嘗試下列方法，然後重新啟用程式碼產生：

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>後續步驟

* [對 Azure HDInsight 上執行的 Apache Spark 作業進行偵錯](apache-spark-job-debugging.md)
* [在 HDInsight 上管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [使用 Apache Spark REST API 將遠端作業提交至 Apache Spark 叢集](apache-spark-livy-rest-interface.md)
* [調整 Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [如何實際調整 Apache Spark 作業，以便這些作業運作](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo 序列化](https://github.com/EsotericSoftware/kryo)
