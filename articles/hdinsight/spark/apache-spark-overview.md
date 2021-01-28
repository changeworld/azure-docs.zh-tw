---
title: 什麼是 Apache Spark - Azure HDInsight
description: 本文提供 HDInsight 中的 Spark 簡介，以及您可以在 HDInsight 中使用 Spark 叢集的各種案例。
ms.service: hdinsight
ms.custom: contperf-fy21q1
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 9f5121feebbb516e148b0476d6c8280d461237bc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929986"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>什麼是 Azure HDInsight 中的 Apache Spark

Apache Spark 是一個平行處理架構，可支援記憶體內部處理，以大幅提升巨量資料分析應用程式的效能。 Azure HDInsight 中的 Apache Spark 是 Microsoft 在雲端的 Apache Spark 實作。 HDInsight 讓您能夠更輕鬆地在 Azure 中建立並設定 Spark 叢集。 HDInsight 中的 Spark 叢集與 [Azure Blob 儲存體](../../storage/common/storage-introduction.md)、[Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) 或 [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) 相容。 因此，您可以使用 HDInsight Spark 叢集來處理儲存於 Azure 的資料。 如需元件和版本資訊，請參閱 [Azure HDInsight 中的 Apache Hadoop 元件和版本](../hdinsight-component-versioning.md)。

![Spark：統一架構](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>什麼是 Apache Spark？

Spark 提供用於記憶體內部叢集運算的基本項目。 Spark 作業可將資料載入並快取到記憶體，以便重複查詢。 記憶體內部計算速度優於磁碟型應用程式，例如，會透過 Hadoop 分散式檔案系統 (HDFS) 共用資料的 Hadoop。 Spark 也會整合到 Scala 程式設計語言中，讓您處理分散式資料集 (像是本機集合)。 您不需要將一切建構成對應和縮減作業。

![傳統 MapReduce 與Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

HDInsight 中的 Spark 叢集可提供完全受控的 Spark 服務。 以下列出在 HDInsight 中建立 Spark 叢集的優點。

| 功能 | 描述 |
| --- | --- |
| 輕鬆建立 |您可以使用 Azure 入口網站、Azure PowerShell 或 HDInsight .NET SDK，在幾分鐘內便能於 HDInsight 中建立新的 Spark 叢集。 請參閱[開始使用 HDInsight 中的 Apache Spark 叢集](apache-spark-jupyter-spark-sql-use-portal.md)。 |
| 容易使用 |HDInsight 中的 Spark 叢集包含 Jupyter Notebook 和 Apache Zeppelin Notebook。 您可以使用這些 Notebook 來進行互動式的資料處理和視覺化。 請參閱[搭配使用 Apache Zeppelin 筆記本與 Apache Spark](apache-spark-zeppelin-notebook.md) 和[在 Apache Spark 叢集上載入資料和執行查詢](apache-spark-load-data-run-query.md)。|
| REST API |HDInsight 中的 Spark 叢集包含 [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)，它是 REST-API 型 Spark 作業伺服器，可用來遠端提交及監視作業。 請參閱[使用 Apache Spark REST API 將遠端作業提交至 HDInsight Spark 叢集](apache-spark-livy-rest-interface.md)。|
| Azure 儲存體的支援 | HDInsight 中的 Spark 叢集可以使用 Azure Data Lake Storage Gen1/Gen2 作為主要儲存體或額外的儲存體。 如需 Data Lake Storage Gen1 的詳細資訊，請參閱 [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md)。 如需 Data Lake Storage Gen2 的詳細資訊，請參閱 [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)。|
| Azure 服務整合 |HDInsight 中的 Spark 叢集隨附連至 Azure 事件中樞的連接器。 您可以使用事件中樞建立串流應用程式。 包括 Apache Kafka (已是 Spark 的一部分)。 |
| ML Server 的支援 | 在 HDInsight 中會以 **ML 服務** 叢集類型的形式提供 ML Server 的支援。 您可以設定 ML 服務叢集，以 Spark 叢集所承諾的速度來執行分散式 R 運算。 如需詳細資訊，請參閱[什麼是 Azure HDInsight 中的 ML 服務](../r-server/r-server-overview.md)。 |
| 第三方 IDE 整合 | HDInsight 提供數個 IDE 外掛程式，以用來建立應用程式，並將應用程式提交至 HDInsight Spark 叢集。 如需詳細資訊，請參閱[使用 Azure Toolkit for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)、[使用 Spark & Hive Tools for VSCode](../hdinsight-for-vscode.md) 和[使用 Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md)。|
| 並行查詢 |HDInsight 中的 Spark 叢集支援並行查詢。 此功能可讓來自單一使用者的多個查詢或來自不同使用者與應用程式的多個查詢，一起共用相同的叢集資源。 |
| SSD 快取 |您可以選擇將資料快取在記憶體中，或快取在連接叢集節點的 SSD 中。 記憶體快取能提供最高的查詢效能，但可能所費不貲。 SSD 快取提供改善查詢效能的絕佳選項，而且您不需要建立大小可讓整個資料集納入記憶體的叢集。 請參閱[使用 Azure HDInsight IO 快取改進 Apache Spark 工作負載效能](apache-spark-improve-performance-iocache.md)。 |
| BI 工具整合 |HDInsight 中的 Spark 叢集會為資料分析提供 BI 工具 (例如 Power BI) 的連接器。 |
| 預先載入的 Anaconda 程式庫 |HDInsight 中的 Spark 叢集隨附預先安裝的 Anaconda 程式庫。 [Anaconda](https://docs.continuum.io/anaconda/) 為機器學習、資料分析、視覺化等主題提供將近 200 個程式庫。 |
| 適應性 | HDInsight 可讓您使用自動調整功能來動態變更叢集節點的數目。 請參閱[自動調整 Azure HDInsight 叢集規模](../hdinsight-autoscale-clusters.md)。 此外，由於所有資料都儲存在 Azure Blob 儲存體、[Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) 或 [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) 中，因此您可以在不遺失資料的情況下卸除 Spark 叢集。 |
| SLA |HDInsight 中的 Spark 叢集隨附全天候支援，以及保證正常運作時間達 99.9% 的 SLA。 |

依預設，HDInsight 中的 Apache Spark 叢集能經由叢集提供下列元件。

* [Spark Core](https://spark.apache.org/docs/latest/)。 包括 Spark Core、Spark SQL、Spark 串流 API、GraphX 及 MLlib。
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Apache Zeppelin Notebook](http://zeppelin-project.org/)

HDInsight Spark 叢集會納入一個 [ODBC 驅動程式](https://go.microsoft.com/fwlink/?LinkId=616229)以便從 BI 工具 (例如 Microsoft Power BI) 進行連線。

## <a name="spark-cluster-architecture"></a>Spark 叢集架構

![HDInsight Spark 的架構](./media/apache-spark-overview/hdi-spark-architecture.png)

您可以藉由了解 Spark 在 HDInsight 叢集上的執行方式，輕鬆地了解 Spark 的元件。

Spark 應用程式在叢集上是以獨立的多組程序在執行。 由主要程式 (稱為驅動程式) 中的 SparkContext 物件協調。

SparkContext 可以連線到數種類型的叢集管理員，而叢集管理員可以給予各個應用程式資源。 這些叢集管理員包括 Apache Mesos、Apache Hadoop YARN 或 Spark 叢集管理員。 在 HDInsight 中，使用 YARN 叢集管理員可執行 Spark。 一旦連線之後，Spark 就會取得叢集中背景工作節點上的執行程式，也就是為應用程式執行運算和儲存資料的處理序。 接下來，它會將您的應用程式程式碼 (由傳遞到 SparkContext 的 JAR 或 Python 檔案所定義) 傳送到執行程式。 最後，SparkContext 會將工作傳送到執行程式來執行。

SparkContext 會執行使用者的主要函式，並在背景工作節點上執行各種平行作業。 然後，SparkContext 會收集作業的結果。 背景工作節點會在 Hadoop 分散式檔案系統中讀取和寫入資料。 背景工作節點也會將記憶體內部已轉換的資料快取為彈性分散式資料集 (RDD)。

SparkCoNtext 會連線至 Spark 主節點，並負責將應用程式轉換為個別工作的有向圖形 (DAG)。 在背景工作節點上的執行程式程序內執行的作業。 每個應用程式都有自己的執行程式程序。 這些程序在整個應用程式的持續時間保持運行，並在多個執行緒中執行工作。

## <a name="spark-in-hdinsight-use-cases"></a>HDInsight 中的 Spark 使用案例

HDInsight 中的 Spark 叢集適用於下列重要案例：

### <a name="interactive-data-analysis-and-bi"></a>互動式資料分析和 BI

HDInsight 中的 Apache Spark 會將資料儲存在 Azure 儲存體、Azure Data Lake Gen1 或 Azure Data Lake Storage Gen2 中。 商務專家和關鍵決策者可以就該資料分析並建立報告。 並且使用 Microsoft Power BI 從分析的資料建立互動式報表。 分析師可以從叢集儲存體中的非結構化/半結構化資料著手、使用 Notebook 來定義資料的結構描述，然後再使用 Microsoft Power BI 來建置資料模型。 HDInsight 中的 Spark 叢集也支援許多協力廠商 BI 工具。 例如 Tableau，可讓資料分析師、商務專家、重要決策者更輕鬆。

* [教學課程：使用 Power BI 將 Spark 資料視覺化](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark 機器學習服務

Apache Spark 隨附 [ MLlib](https://spark.apache.org/mllib/)。 MLlib 是以 Spark 為基礎的機器學習程式庫，您可以從 HDInsight 中的 Spark 叢集使用 MLlib。 HDInsight 中的 Spark 叢集也包含 Anaconda，這是提供不同機器學習套件的 Python 發行版本。 加上內建的 Jupyter 和 Zeppelin Notebook 支援，就能擁有適用於建立機器學習應用程式的環境。

* [教學課程：利用 HVAC 資料來預測建築物的溫度](apache-spark-ipython-notebook-machine-learning.md)  
* [教學課程：預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark 串流和即時資料分析

HDInsight 上的 Spark 叢集提供豐富的支援供您建置即時分析解決方案。 Spark 已經有連接器可吸納許多來源的資料，例如 Kafka、Flume、Twitter、ZeroMQ 或 TCP 通訊端。 HDInsight 中的 Spark 新增可從 Azure 事件中樞吸納資料的第一級支援。 事件中樞是 Azure 上最廣泛使用的佇列服務。 擁有完整的事件中樞支援，讓 HDInsight 中的 Spark 叢集成為建置即時分析管線的理想平台。

* [Apache Spark 串流概觀](apache-spark-streaming-overview.md)
* [Apache Spark 結構化串流的概觀](apache-spark-structured-streaming-overview.md)

## <a name="next-steps"></a>後續步驟

在本概觀中，您已對 Azure HDInsight 中的 Apache Spark 有了基本了解。  您可以使用下列文章來深入瞭解 HDInsight 中的 Apache Spark，也可以建立 HDInsight Spark 叢集並進一步執行一些範例 Spark 查詢：

* [快速入門：在 HDInsight 中建立 Apache Spark 叢集並利用 Jupyter 執行互動式查詢](./apache-spark-jupyter-spark-sql-use-portal.md)
* [教學課程：在 Apache Spark 叢集上使用 Jupyter 載入資料和執行查詢](./apache-spark-load-data-run-query.md)
* [教學課程：使用 Power BI 將 Spark 資料視覺化](apache-spark-use-bi-tools.md)
* [教學課程：利用 HVAC 資料來預測建築物的溫度](apache-spark-ipython-notebook-machine-learning.md)
* [最佳化 Spark 作業的效能](apache-spark-perf.md)