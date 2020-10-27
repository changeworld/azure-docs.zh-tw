---
title: 設定 Spark 設定 - Azure HDInsight
description: 如何查看和設定 Azure HDInsight 叢集的 Apache Spark 設定
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 231ab5cc93d98d7356d47472b7e160ddd3ade790
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545934"
---
# <a name="configure-apache-spark-settings"></a>設定 Apache Spark 設定

HDInsight Spark 叢集包含 Apache Spark 程式庫的安裝。  每個 HDInsight 叢集都包含其所有已安裝服務的預設組態參數，包括 Spark。  管理 HDInsight Apache Hadoop 叢集的重要層面，就是監視工作負載，包括 Spark 作業。 若要最佳執行 Spark 作業，請在判斷叢集的邏輯設定時考慮實體叢集設定。

預設 HDInsight Apache Spark 叢集包含下列節點：三個 Apache ZooKeeper 節點、兩個前端節點，以及一或多個背景工作節點：

![Spark HDInsight 架構](./media/apache-spark-settings/spark-hdinsight-arch.png)

您 HDInsight 叢集中節點的 Vm 數目和 VM 大小可能會影響您的 Spark 設定。 非預設 HDInsight 組態值通常需要非預設 Spark 組態值。 當您建立 HDInsight Spark 叢集時，您會看到每個元件的建議 VM 大小。 目前，Azure 的[記憶體最佳化 Linux 虛擬機器大小](../../virtual-machines/sizes-memory.md)是 D12 v2 或更高。

## <a name="apache-spark-versions"></a>Apache Spark 版本

使用最適合您的叢集的 Spark 版本。  HDInsight 服務包含數種版本的 Spark 和 HDInsight。  每個版本的 Spark 都包含一組預設叢集設定。  

在您建立新的叢集時，有多個 Spark 版本可供選擇。 以查看完整清單、  [HDInsight 元件和版本](../hdinsight-component-versioning.md)。

> [!NOTE]  
> HDInsight 服務中的預設 Apache Spark 版本可能會變更，且不另行通知。 如需依存於某個版本，Microsoft 建議您在使用 .NET SDK、Azure PowerShell 與 Azure 傳統 CLI 建立叢集時指定該特定版本。

Apache Spark 有三個系統組態位置：

* Spark 屬性會控制大部分的應用程式參數，可使用 `SparkConf` 物件或透過 Java 系統屬性來設定。
* 環境變數可讓您透過每個節點上的 `conf/spark-env.sh` 指令碼來設定個別機器的設定，例如 IP 位址，。
* 記錄可透過 `log4j.properties` 進行設定。

當您選取特定版本的 Spark 時，叢集將會包含預設組態設定。  您可以藉由使用自訂的 Spark 設定檔，來變更預設 Spark 設定值。  範例如下所示。

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

上方顯示的範例會覆寫五個 Spark 設定參數的數個預設值。  這些值是壓縮編解碼器，Apache Hadoop MapReduce 會分割大小下限和 parquet 區塊大小。 此外，Spar SQL 資料分割和開啟的檔案會將預設值調整為預設值。  選擇這些設定變更是因為在此範例中，基因數據) 的相關資料和工作 (有特定特性。 使用這些自訂設定時，這些特性會更好用。

---

## <a name="view-cluster-configuration-settings"></a>檢視叢集組態設定

在叢集上執行效能優化之前，請先確認目前的 HDInsight 叢集設定設定。 按一下 Spark 叢集窗格上的 [儀表板]  連結，從 Azure 入口網站啟動 HDInsight 儀表板。 使用叢集系統管理員的使用者名稱和密碼登入。

Apache Ambari Web UI 隨即出現，其中包含關鍵叢集資源使用計量的儀表板。  Ambari 儀表板會顯示 Apache Spark 設定和其他已安裝的服務。 儀表板包含 [設定歷程 **記錄** ] 索引標籤，您可以在其中查看已安裝服務的資訊，包括 Spark。

若要檢視 Apache Spark 的設定值，請選取 [設定歷程記錄]  ，然後選取 [Spark2]  。  選取 [設定]  索引標籤，然後選取服務清單中的 `Spark` (或 `Spark2`，視您的版本而定) 連結。  您會看到叢集的設定值清單：

![Spark 設定](./media/apache-spark-settings/spark-configurations.png)

若要查看及變更個別的 Spark 設定值，請選取標題中有 "Spark" 的任何連結。  Spark 的設定同時包含下列類別的自訂和進階設定值：

* 自訂 Spark2-defaults
* 自訂 Spark2-metrics-properties
* 進階 Spark2-defaults
* 進階 Spark2-env
* 進階 spark2-hive-site-override

如果您建立一組非預設的設定值，則會顯示您的更新歷程記錄。  此設定歷程記錄有助於查看哪一個非預設設定具有最佳效能。

> [!NOTE]  
> 若要檢視 (但不變更) 一般 Spark 叢集組態設定，請選取最上層 [Spark 作業 UI]  介面上的 [環境]  索引標籤。

## <a name="configuring-spark-executors"></a>設定 Spark 執行程式

下圖顯示主要 Spark 物件：驅動程式及其相關聯的 Spark 內容，和叢集管理員及其 *n* 個背景工作角色節點。  每個背景工作角色節點都包含一個執行程式、一個快取和 *n* 個工作執行個體。

![叢集物件](./media/apache-spark-settings/hdi-spark-architecture.png)

Spark 作業會使用背景工作角色資源 (尤其是記憶體)，因此，調整背景工作角色節點「執行程式」的 Spark 設定值，是很常見的動作。

為了微調 Spark 設定以改善應用程式需求而常會調整的三個主要參數為 `spark.executor.instances`、`spark.executor.cores` 和 `spark.executor.memory`。 執行程式是針對 Spark 應用程式啟動的程序。 執行程式會在背景工作角色節點上執行，並負責處理應用程式的工作。 背景工作節點和背景工作節點大小的數目會決定執行程式的數目，以及執行程式的大小。 這些值會儲存在叢集前端 `spark-defaults.conf` 節點上。  您可以在 Ambari web UI 中選取 [ **自訂 spark-預設** 值]，以在執行中的叢集中編輯這些值。  在您進行變更後，UI 會提示您 **重新啟動** 所有受影響的服務。

> [!NOTE]  
> 這三個設定參數可以在叢集層級設定 (適用於在叢集執行的所有應用程式)，也可以對個別的應用程式指定。

Spark 執行程式所使用之資源的另一項資訊來源是 Spark 應用程式 UI。  在 UI  **中，執行** 程式會顯示設定和取用資源的摘要和詳細資料檢視。  判斷是否要變更整個叢集的執行程式值，或一組特定的作業執行。

![Spark 執行程式](./media/apache-spark-settings/apache-spark-executors.png)

或者，您可以使用 Ambari REST API，以程式設計方式驗證 HDInsight 和 Spark 叢集設定。  您可以在 [GitHub 上的 Apache Ambari API 參考](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)取得詳細資訊。

根據您的 Spark 叢集工作負載，您可能會判定非預設 Spark 設定可提供最佳化程度較高的 Spark 作業執行。  使用範例工作負載進行基準測試，以驗證任何非預設叢集設定。  您可以考慮調整某些通用參數：

|參數 |描述|
|---|---|
|--num-executors|設定執行程式的數目。|
|--executor-cores|設定每個執行程式的核心數目。 建議您使用中型執行程式，因為其他程序也會耗用部分的可用記憶體。|
|--executor-memory|控制 Apache Hadoop YARN 上每個執行程式的記憶體大小 (堆積大小) ，您必須保留一些記憶體來執行額外負荷。|

下列範例說明兩個使用不同設定值的背景工作角色節點：

![兩個節點設定](./media/apache-spark-settings/executor-configuration.png)

下列清單顯示主要 Spark 執行程式記憶體參數。

|參數 |描述|
|---|---|
|spark.executor.memory|定義執行程式可用的記憶體總量。|
|memoryFraction| (預設值 ~60%) 會定義可用來儲存永續性 RDD 的記憶體數量。|
|spark. memoryFraction| (預設值 ~20%) 會定義為隨機作業保留的記憶體數量。|
|unrollFraction 和 spark. safetyFraction| (總計超過30% 的總記憶體) -這些值是由 Spark 在內部使用，不應變更。|

YARN 會控制每個 Spark 節點上所有容器所使用的記憶體最大總和。 下圖顯示每個節點中，YARN 設定物件與 Spark 物件之間的關聯性。

![YARN Spark 記憶體管理](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>變更在 Jupyter Notebook 中執行的應用程式所使用的參數

根據預設，HDInsight 中的 Spark 叢集會包含許多元件。 這些元件都包含可視需要覆寫的預設設定值。

|元件 |描述|
|---|---|
|Spark 核心|Spark Core、Spark SQL、Spark 串流 Api、GraphX 和 Apache Spark MLlib。|
|Anaconda|Python 套件管理員。|
|Apache Livy|Apache Spark REST API，用來將遠端作業提交至 HDInsight Spark 叢集。|
|Jupyter 和 Apache Zeppelin 筆記本|互動式瀏覽器型 UI，可與您的 Spark 叢集互動。|
|ODBC 驅動程式|將 HDInsight 中的 Spark 叢集連線至商業智慧 (BI) 工具，例如 Microsoft Power BI 和 Tableau。|

對於在 Jupyter Notebook 中執行的應用程式，請使用 `%%configure` 命令從 Notebook 本身進行設定變更。 這些設定變更將會套用至從您 Notebook 執行個體執行的 Spark 作業。 在您執行第一個程式碼儲存格之前，在應用程式的開頭進行這類變更。 變更的設定會在 Livy 工作階段建立時套用至其中。

> [!NOTE]  
> 若要變更應用程式後續階段的設定，請使用 `-f` (force) 參數。 不過，應用程式中的所有進度都將遺失。

下列程式碼顯示如何變更在 Jupyter Notebook 中執行之應用程式的設定。

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>結論

監視核心設定，以確保您的 Spark 作業以可預測且效能的方式執行。 這些設定有助於您決定特定工作負載最適用的 Spark 叢集設定。  您也需要監視長時間執行和或耗用資源的 Spark 作業執行。  最常見的挑戰，是因為不正確的設定，例如不正確調整大小的執行程式所造成的記憶體壓力。 此外，長時間執行的作業和工作會導致笛卡兒的作業。

## <a name="next-steps"></a>下一步

* [可以與 HDInsight 搭配使用的 Apache Hadoop 元件和版本？](../hdinsight-component-versioning.md)
* [在 HDInsight 上管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [Apache Spark 設定](https://spark.apache.org/docs/latest/configuration.html) \(英文\)
* [在 Apache Hadoop YARN 上執行 Apache Spark](https://spark.apache.org/docs/latest/running-on-yarn.html)