---
title: 將 Azure HDInsight 中的 Hive 查詢最佳化
description: 本文說明如何在 HDInsight 中最佳化 Hadoop 的 Apache Hive 查詢。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 89c276ffe6059a61323755eaf928d525ab5ea416
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085288"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>將 Azure HDInsight 中的 Apache Hive 查詢最佳化

在 Azure HDInsight 中，有數種叢集類型與技術可執行 Apache Hive查詢。 選擇適當的叢集類型，以協助針對您的工作負載需求將效能優化。

例如，選擇 [**互動式查詢**叢集類型]，以針對 `ad hoc` 互動式查詢進行優化。 選擇 Apache **Hadoop** 叢集類型，將作為批次程序使用的 Hive 查詢最佳化。 **Spark** 與 **HBase** 叢集類型也可以執行 Hive 查詢。 如需針對不同 HDInsight 叢集類型執行 Hive 查詢的詳細資訊，請參閱[Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](hadoop/hdinsight-use-hive.md)。

根據預設，Hadoop 叢集類型的 HDInsight 叢集不會針對效能優化。 本文說明幾個將 Hive 效能最佳化的最常見方法，您可將這些方法套用於查詢。

## <a name="scale-out-worker-nodes"></a>相應放大背景工作節點

增加 HDInsight 叢集中的背景工作節點數目，可讓工作使用更多的對應程式和歸納器以平行方式執行。 在 HDInsight 中您有兩種方法可相應放大：

* 當您建立叢集時，您可以使用 Azure 入口網站、Azure PowerShell 或命令列介面來指定背景工作節點的數目。  如需詳細資訊，請參閱[管理 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。 下列畫面顯示 Azure 入口網站上的背景工作節點組態：
  
    ![Azure 入口網站叢集大小節點](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* 建立之後，您也可以編輯背景工作節點數目，以進一步相應放大叢集，而不必重新建立：

    ![Azure 入口網站調整叢集大小](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

如需調整 HDInsight 的詳細資訊，請參閱[調整 HDInsight 叢集](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>使用 Apache Tez 而非 Mapreduce

[Apache Tez](https://tez.apache.org/)是 MapReduce 引擎的替代執行引擎。 Linux 的 HDInsight 叢集預設會啟用 Tez。

![HDInsight Apache Tez 總覽圖表](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez 比較迅速，因為：

* **在 MapReduce 引擎中執行有向非循環圖 (DAG) 作為單一作業**。 DAG 要求每一組對應程式後面有一組歸納器。 這項需求會導致每個 Hive 查詢都有多個 MapReduce 工作的旋轉。 Tez 沒有這類條件約束，而且可以將複雜的 DAG 當做一項工作處理，將工作啟動額外負荷降到最低
* **避免不必要的寫入**。 使用多個作業，在 MapReduce 引擎中處理相同的 Hive 查詢。 每個 MapReduce 作業的輸出都會寫入 HDFS，作為中繼資料。 因為 Tez 會將每個 Hive 查詢的工作數目降至最低，所以能夠避免不必要的寫入。
* **將啟動延遲最小化**。 Tez 會減少需要啟動的對應器數目，同時提升整個最佳化，因此較能夠將啟動延遲降到最低。
* **重複使用容器**。 可能的話，Tez 會重複使用容器，以確保啟動容器的延遲會降低。
* **連續最佳化技巧**。 習慣上，是在編譯階段進行最佳化。 但是有更多關於輸入的資訊可用，所以在執行階段進行最佳化比較理想。 Tez 會使用連續最佳化技巧，進一步在執行階段將計劃最佳化。

如需這些概念的詳細資訊，請參閱 [Apache TEZ](https://tez.apache.org/)。

在查詢的前面加上下列設定命令，即可啟用任何 Hive 查詢 Tez：

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Hive 分割

I/O 作業是執行 Hive 查詢的主要效能瓶頸。 如果可以減少需要讀取的資料量，即可改善效能。 根據預設，Hive 查詢會掃描整個 Hive 資料表。 但是，對於只需要掃描少量資料的查詢 (例如具有篩選的查詢)，這種行為就會產生不必要的額外負荷。 Hive 分割可讓 Hive 查詢只存取 Hive 資料表中所需的資料量。

Hive 資料分割的實作方法是將未經處理的資料重新整理成新的目錄。 每個分割區都有自己的檔案目錄。 由使用者定義的資料分割。 下圖說明如何依據 *年度*資料行來分割 Hive 資料表。 每年都會建立新的目錄。

![HDInsight Apache Hive 分割](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

一些分割考量：

* **不要**在只有少數值的資料行下進行分割區分割，可能會造成少數的磁碟分割。 例如，根據性別進行分割時，只會建立兩個要建立的磁碟分割（男性和女性），因此請將延遲降到最多一半。
* **不要超過資料分割**-另一方面，在具有唯一值的資料行上建立資料分割（例如 userid）會造成多個分割區。 過度分割會在叢集 namenode 上造成太多壓力，因為它必須處理大量目錄。
* **避免資料扭曲** - 明智地選擇分割索引鍵，讓所有分割區的大小平均。 例如，「州/省」** 資料行上的資料分割可能會扭曲資料的分佈。 由於加州的人口幾乎是佛蒙特州的 30 倍，分割區大小可能會有偏差，且效能可能會有極大的差異。

若要建立分割資料表，請使用 *Partitioned By* 子句：

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

建立分割資料表後，您可以建立靜態分割或動態分割。

* **靜態資料分割**表示您在適當的目錄中已有分區化資料。 使用靜態資料分割，您可以根據目錄位置手動新增 Hive 資料分割。 下列範例為程式碼片段。
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **動態分割** 表示您要 Hive 為您自動建立分割區。 由於您已從臨時表建立分割資料表，您只需要將資料插入至分割資料表：
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

如需詳細資訊，請參閱[資料分割資料表](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables) \(英文\)。

## <a name="use-the-orcfile-format"></a>使用 ORCFile 格式

Hive 支援不同的檔案格式。 例如：

* **文字**：預設檔案格式且適用於大部分的案例。
* **Avro**：適用於互通性案例。
* **ORC/Parquet**：最適合處理效能。

ORC (最佳化的資料列單欄式) 格式是儲存 Hive 資料的高效率方式。 相較於其他格式，ORC 具有下列優點：

* 支援複雜的類型，包括 DateTime 和複雜和半結構化類型。
* 高達 70% 的壓縮。
* 每 10,000 個資料列的索引可讓您略過一些資料列。
* 執行階段的執行大幅減少。

若要啟用 ORC 格式，請先使用子句 *Stored as ORC*建立資料表：

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

接著，將資料從暫存資料表插入至 ORC 資料表。 例如：

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

您可以在 [Apache Hive 語言手冊](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC) (英文) 進一步了解 ORC 格式。

## <a name="vectorization"></a>向量化

向量化可讓 Hive 以批次方式同時處理 1024 個資料列，而不是一次處理一個資料列。 這表示，因為需要執行的內部程式碼較少，所以簡單的作業會更快完成。

若要啟用向量化，請在 Hive 查詢的前面加上以下列設定：

```hive
set hive.vectorized.execution.enabled = true;
```

如需詳細資訊，請參閱 [向量化查詢執行](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution)。

## <a name="other-optimization-methods"></a>其他最佳化方法

您有更多最佳化方法可以考慮，例如：

* **Hive 值區：** 能將大型資料集叢集化或分段以最佳化查詢效能的技術。
* **聯結最佳化：** Hive 的查詢執行計劃最佳化，可改善聯結的效率並減少使用者提示的需求。 如需詳細資訊，請參閱 [聯結最佳化](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)。
* **增加歸納器**。

## <a name="next-steps"></a>後續步驟

在本文中，您學到幾種常見的 Hive 查詢最佳化方法。 如需詳細資訊，請參閱下列文章：

* [在 HDInsight 中使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [將 Apache Hive 最佳化](./optimize-hive-ambari.md)
* [在 HDInsight 中使用互動式查詢來分析航班延誤資料](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [在 HDInsight 中使用 Apache Hive 分析 Twitter 資料](hdinsight-analyze-twitter-data-linux.md)
