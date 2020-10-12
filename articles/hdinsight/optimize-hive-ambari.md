---
title: 使用 Azure HDInsight 的 Apache Ambari 優化 Apache Hive
description: 使用 Apache Ambari web UI 來設定和優化 Apache Hive。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 33c2ee7bc477d3c9d3823642dbdd974650017822
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084353"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>使用 Azure HDInsight 的 Apache Ambari 優化 Apache Hive

Apache Ambari 是用來管理和監視 HDInsight 叢集的 web 介面。 如需 Ambari Web UI 的簡介，請參閱 [使用 Apache Ambari WEB ui 來管理 HDInsight](hdinsight-hadoop-manage-ambari.md)叢集。

下列各節會說明將 Apache Hive 整體效能最佳化的設定選項。

1. 若要修改 Hive 組態參數，請選取 [服務] 資訊看板中的 [Hive]****。
1. 瀏覽至 [設定]**** 索引標籤。

## <a name="set-the-hive-execution-engine"></a>設定 Hive 執行引擎

Hive 提供兩個執行引擎： Apache Hadoop MapReduce 和 Apache TEZ。 Tez 比 MapReduce 更快。 HDInsight Linux 叢集有 Tez 做為預設執行引擎。 變更執行引擎：

1. 在 Hive [設定]**** 索引標籤的 [篩選] 方塊中，輸入**執行引擎**。

    ![Apache Ambari 搜尋執行引擎](./media/optimize-hive-ambari/ambari-search-execution.png)

1. [最佳化]**** 屬性的預設值是 **Tez**。

    ![優化-Apache Tez 引擎](./media/optimize-hive-ambari/optimization-apache-tez.png)

## <a name="tune-mappers"></a>微調對應程式

Hadoop 會嘗試將單一檔案分割 (*對應*) 為多個檔案，並且平行處理產生的檔案。 對應程式的數目取決於分割的數目。 下列兩個組態參數會影響 Tez 執行引擎的分割數目：

* `tez.grouping.min-size`：群組分割大小下限，預設值是 16 MB (16777216 位元組)。
* `tez.grouping.max-size`：群組分割大小上限，預設值為 1 GB (1,073,741,824 位元組)。

作為效能指導方針，請降低這兩個參數來改善延遲，增加更多輸送量。

例如，若要設定 128 MB 資料大小的四個對應程式工作，您會將這兩個參數分別設定為 32 MB (33,554,432 位元組)。

1. 若要修改限制參數，請瀏覽至 Tez 服務的 [設定]**** 索引標籤。 展開 [一般]**** 面板，並找出 `tez.grouping.max-size` 和 `tez.grouping.min-size` 參數。

1. 將這兩個參數設定為 **33,554,432** 位元組 (32 MB)。

    ![Apache Ambari Tez 分組大小](./media/optimize-hive-ambari/apache-tez-grouping-size.png)

這些變更會影響伺服器的所有 Tez 作業。 若要得到最佳結果，請選擇適當的參數值。

## <a name="tune-reducers"></a>微調歸納器

Apache ORC 和 Snappy 均可達到高效能。 不過，Hive 預設的歸納器數量可能會太少，因而導致瓶頸。

舉例而言，假設您有 50 GB 的輸入資料大小。 該資料為 ORC 格式，而且經過 Snappy 壓縮的處理後成為 1GB。 Hive 估計所需的歸納器數目為：(對應程式中的位元組輸入數目/`hive.exec.reducers.bytes.per.reducer`)。

使用預設設定時，此範例為四個歸納器。

`hive.exec.reducers.bytes.per.reducer` 參數指定每個歸納器處理的位元組數目。 預設值是 64 MB。 降低這個值會增加平行處理原則，可能會改善效能。 過度降低也會產生過多歸納器，而可能嚴重影響效能。 此參數根據您的特定資料需求、壓縮設定和其他環境因素而定。

1. 若要修改參數，請瀏覽至 Hive [設定]**** 索引標籤，並尋找 [設定] 頁面上的 [各歸納器的資料]**** 參數。

    ![每個歸納器的 Apache Ambari 資料](./media/optimize-hive-ambari/ambari-data-per-reducer.png)

1. 選取 [編輯]**** 將值修改為 128 MB (134,217,728 位元組)，然後按下 **Enter** 儲存。

    ![Ambari 每個歸納器的資料-編輯](./media/optimize-hive-ambari/data-per-reducer-edited.png)
  
    假設輸入大小為 1024 MB，每個歸納器有 128 MB 的資料，則有八個歸納器 (1024/128) 。

1. [各歸納器的資料]**** 參數的值不正確會導致歸納器的數目增加，而對於查詢效能造成負面影響。 若要限制歸納器數目上限，請將 `hive.exec.reducers.max` 設定為合適的值。 預設值是 1009。

## <a name="enable-parallel-execution"></a>啟用平行執行

在一個或多個階段中執行 Hive 查詢。 如果可以平行執行獨立階段，即可提升查詢效能。

1. 若要啟用平行查詢執行，請瀏覽至 Hive [設定]**** 索引標籤，並搜尋 `hive.exec.parallel` 屬性。 預設值為 false。 將值變更為 true，然後按下 **Enter** 儲存值。

1. 若要限制平行執行的作業數目，請修改 `hive.exec.parallel.thread.number` 屬性。 預設值為 8。

    ![Apache Hive exec 平行顯示](./media/optimize-hive-ambari/apache-hive-exec-parallel.png)

## <a name="enable-vectorization"></a>啟用向量化

Hive 會逐列處理資料。 向量化會指示 Hive 處理 1,024 列的資料，而非一次處理一個資料列。 向量化只適用於 ORC 檔案格式。

1. 若要啟用向量化查詢執行，請瀏覽至 Hive [設定]**** 索引標籤，並搜尋 `hive.vectorized.execution.enabled` 參數。 Hive 0.13.0 或更新版本的預設值為 true。

1. 若要對於查詢的歸納器端啟用向量化執行，將 `hive.vectorized.execution.reduce.enabled` 參數設定為 true。 預設值為 false。

    ![Apache Hive 向量化執行](./media/optimize-hive-ambari/hive-vectorized-execution.png)

## <a name="enable-cost-based-optimization-cbo"></a>啟用以成本為基礎的最佳化 (CBO)

Hive 預設會遵循一組規則，找出一個最佳的查詢執行計畫。 以成本為基礎的優化 (CBO) 評估多個計畫來執行查詢。 然後將成本指派給每個方案，然後決定執行查詢的最便宜計畫。

若要啟用 CBO，請流覽至**Hive**  >  **Configs**  >  **設定設定**，並尋找 [**啟用以成本為基礎的優化**工具]，然後將切換按鈕切換為 [**開啟**]。

![HDInsight 以成本為基礎的優化工具](./media/optimize-hive-ambari/hdinsight-cbo-config.png)

CBO 啟用時，下列的其他設定參數會提高 Hive 查詢效能：

* `hive.compute.query.using.stats`

    設定為 true 時，Hive 會使用儲存在其中繼存放區的統計資料，來回答類似 `count(*)` 的簡單查詢。

    ![使用統計資料 Apache Hive 計算查詢](./media/optimize-hive-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    CBO 啟用時，會建立資料行統計資料。 Hive 會使用儲存在中繼存放區的資料將查詢最佳化。 資料行數目較高時，擷取每個資料行的資料行統計資料會花費較長的時間。 設定為 false 時，此設定會停用從中繼存放區擷取資料行統計資料。

    ![Apache Hive 統計資料集資料行統計資料](./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    基本的分割區統計資料 (例如資料列、資料大小和檔案大小) 儲存在中繼存放區。 如果設定為 true，則會從中繼存放區提取分割區統計資料。 若為 false，則會從檔案系統提取檔案大小。 而且會從資料列架構中提取資料列的數目。

    ![Hive 統計資料集合分割區統計資料](./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png)

## <a name="enable-intermediate-compression"></a>啟用中繼壓縮

對應工作會建立歸納器工作使用的中繼檔案。 中繼壓縮會壓縮中繼檔案大小。

Hadoop 工作通常出現 I/O 瓶頸。 壓縮資料可以加快 I/O 和整體網路傳輸。

可用的壓縮類型包括：

| [格式] | 工具 | 演算法 | 副檔名 | 可分割？ |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | DEFLATE | `.gz` | 否 |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | 是 |
| LZO | `Lzop` | LZO | `.lzo` | 是，如果已編製索引 |
| Snappy | N/A | Snappy | Snappy | 否 |

一般來說，壓縮方法可分割很重要，否則會建立少數的對應程式數目。 如果輸入資料為文字，`bzip2` 是最佳選擇。 若為˙ ORC 格式，Snappy 是最快的壓縮選項。

1. 若要啟用中繼壓縮，請瀏覽至 Hive [設定]**** 索引標籤，然後將 `hive.exec.compress.intermediate` 參數設定為 true。 預設值為 false。

    ![' Hive exec 壓縮中繼 '](./media/optimize-hive-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > 若要壓縮中繼檔案，即使轉碼器沒有高壓縮輸出，也請選擇較低 CPU 成本的壓縮轉碼器。

1. 若要設定中繼壓縮轉碼器，請將自訂屬性 `mapred.map.output.compression.codec` 新增至 `hive-site.xml` 或 `mapred-site.xml` 檔案。

1. 若要新增自訂設定：

    a. 流覽至**Hive 配置**  >  **Configs**  >  **Advanced**  >  **的 Advanced 自訂 hive 網站**。

    b. 選取 [自訂 hive 網站] 窗格底部的 [ **加入屬性** ]。

    c. 在 [新增屬性] 視窗中，輸入 `mapred.map.output.compression.codec` 做為索引鍵，並輸入 `org.apache.hadoop.io.compress.SnappyCodec` 做為值。

    d. 選取 [新增]。

    ![' Apache Hive 自訂屬性新增 '](./media/optimize-hive-ambari/hive-custom-property.png)

    此設定會使用 Snappy 壓縮來壓縮中繼檔案。 新增屬性後，屬性會出現在 [自訂 Hive 網站] 窗格中。

    > [!NOTE]  
    > 此程序會修改 `$HADOOP_HOME/conf/hive-site.xml` 檔案。

## <a name="compress-final-output"></a>壓縮最終輸出

最終 Hive 輸出也可壓縮。

1. 若要壓縮最終 Hive 輸出，請瀏覽至 Hive [設定]**** 索引標籤，然後將 `hive.exec.compress.output` 參數設定為 true。 預設值為 false。

1. 若要選擇輸出壓縮轉碼器，請將 `mapred.output.compression.codec` 自訂屬性新增至 [自訂 Hive 網站] 窗格，如上一節的步驟 3 中所述。

    ![Apache Hive 自訂屬性 add2](./media/optimize-hive-ambari/hive-custom-property2.png)

## <a name="enable-speculative-execution"></a>啟用推測性執行

「推測執行」會啟動特定數目的重複工作，以偵測和拒絕列出執行緩慢的工作追蹤程式。 藉由優化個別工作結果來改善整體作業執行。

對於有大量輸入的長時間執行 MapReduce 工作，不應該開啟推測性執行。

* 若要啟用推測性執行，請瀏覽至 Hive [設定]**** 索引標籤，然後將 `hive.mapred.reduce.tasks.speculative.execution` 參數設定為 true。 預設值為 false。

    ![「Hive mapred 減少工作推測執行」](./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

## <a name="tune-dynamic-partitions"></a>微調動態分割區

Hive 可在將記錄插入資料表時，建立動態資料分割，而不會預先定義每個資料分割。 這項功能是一項功能強大的功能。 雖然可能會導致建立大量的資料分割。 而且每個分割區都有大量的檔案。

1. 若要 Hive 執行動態分割，`hive.exec.dynamic.partition` 參數值應該為 true (預設值)。

1. 將動態分割模式變更為 [嚴格]**。 在嚴格模式中，至少一個分割區必須為靜態。 這項設定可防止 WHERE 子句中沒有分割篩選的查詢，也就是 *strict* 會防止掃描所有分割區的查詢。 瀏覽至 Hive [設定]**** 索引標籤，然後將 `hive.exec.dynamic.partition.mode` 設定為 [嚴格]****。 預設值為 **nonstrict**。

1. 若要限制建立的動態分割區數目，請修改 `hive.exec.max.dynamic.partitions` 參數。 預設值為 5000。

1. 若要限制每個節點的動態分割區總數，請修改 `hive.exec.max.dynamic.partitions.pernode`。 預設值為 2000。

## <a name="enable-local-mode"></a>啟用本機模式

原生模式可讓 Hive 在單一電腦上進行作業的所有工作。 或有時在單一進程中。 如果輸入資料很小，此設定可改善查詢效能。 而啟動查詢工作的額外負荷會耗用大量百分比的整體查詢執行。

若要啟用本機模式，請將 `hive.exec.mode.local.auto` 參數新增至 [自訂 Hive 網站] 面板，如「[啟用中繼壓縮](#enable-intermediate-compression)」一節的步驟 3 所述。

![Apache Hive exec 模式本機自動](./media/optimize-hive-ambari/hive-exec-mode-local-auto.png)

## <a name="set-single-mapreduce-multigroup-by"></a>設定單一 MapReduce MultiGROUP BY

這個屬性設定為 true 時，一般群組依據索引鍵的 MultiGROUP BY 查詢會產生單一 MapReduce 工作。  

若要啟用此行為，請將 `hive.multigroupby.singlereducer` 參數新增至 [自訂 Hive 網站] 面板，如「[啟用中繼壓縮](#enable-intermediate-compression)」一節的步驟 3 所述。

![Hive 設定單一 MapReduce MultiGROUP BY](./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png)

## <a name="additional-hive-optimizations"></a>其他 Hive 最佳化

下列各節說明可以設定的其他 Hive 相關的最佳化。

### <a name="join-optimizations"></a>聯結最佳化

Hive 的預設聯結類型是*隨機聯結*。 在 Hive 中，特殊對應程式會讀取輸入，並將聯結索引鍵/值組發送至中繼檔案。 Hadoop 會在隨機階段中將這些組排序並合併。 這個隨機階段需要相當高的成本。 依照您的資料選取正確的聯結可大幅提升效能。

| 聯結類型 | 當 | 方式 | Hive 設定 | 註解 |
| --- | --- | --- | --- | --- |
| 隨機聯結 | <ul><li>預設選擇</li><li>一律運作</li></ul> | <ul><li>讀取其中一個資料表的一部分</li><li>聯結索引鍵的貯體和排序</li><li>將一個貯體傳送至各項減少</li><li>在減少端完成聯結</li></ul> | 不需要特別的 Hive 設定 | 每次運作 |
| 對應聯結 | <ul><li>一個資料表可放入記憶體中</li></ul> | <ul><li>將小型資料表讀入記憶體雜湊表</li><li>透過大型檔案的一部分進行串流</li><li>聯結雜湊資料表中的每筆記錄</li><li>聯結由對應程式單獨完成</li></ul> | `hive.auto.confvert.join=true` | 快速但有限 |
| 排序合併貯體 | 如果兩個資料表是： <ul><li>排序相同</li><li>貯體相同</li><li>對於排序/貯體資料行進行聯結</li></ul> | 每個處理序： <ul><li>讀取每個資料表中的貯體</li><li>處理最小值的資料列</li></ul> | `hive.auto.convert.sortmerge.join=true` | 有效率 |

### <a name="execution-engine-optimizations"></a>執行引擎最佳化

最佳化 Hive 執行引擎的其他建議：

| 設定 | 建議 | HDInsight 預設 |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | True = 較安全、較慢；false = 較快 | false |
| `tez.am.resource.memory.mb` | 最大 4 GB 上限 | 自動調整 |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="next-steps"></a>接下來的步驟

* [使用 Apache Ambari Web UI 管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [將 Azure HDInsight 中的 Apache Hive 查詢最佳化](./hdinsight-hadoop-optimize-hive-query.md)
* [將叢集最佳化](./optimize-hive-ambari.md)
* [將 Apache HBase 最佳化](./optimize-hbase-ambari.md)
* [將 Apache Pig 最佳化](./optimize-pig-ambari.md)
