---
title: Microsoft Cognitive Toolkit 與 Apache Spark Azure HDInsight
description: 了解如何使用 Azure HDInsight Spark 叢集中的 Spark Python API，將定型的 Microsoft 辨識工具組深入學習模型套用至資料集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: d94a4858b4c4fa99a09acc2bec88f94a64686c2d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539134"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>使用 Microsoft 辨識工具組深入了解模型與 Azure HDInsight Spark 叢集

在本文中，您會執行下列步驟。

1. 執行自訂腳本以在 Azure HDInsight Spark 叢集上安裝 [Microsoft Cognitive Toolkit](/cognitive-toolkit/) 。

2. 將 [Jupyter Notebook](https://jupyter.org/) 上傳至 [Apache Spark](https://spark.apache.org/) 叢集，以了解如何使用 [Spark Python API (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)，將定型的 Microsoft Cognitive Toolkit 深入學習模型套用至 Azure Blob 儲存體帳戶中的檔案

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 請參閱[建立 Apache Spark 叢集](./apache-spark-jupyter-spark-sql-use-portal.md)。

* 熟悉如何搭配使用 Jupyter Notebook 和 HDInsight 上的 Spark。 如需詳細資訊，請參閱[使用 HDInsight 上的 Apache Spark 載入資料及執行查詢](./apache-spark-load-data-run-query.md)。

## <a name="how-does-this-solution-flow"></a>此解決方案的流程為何？

此解決方案分為本文，以及您在本文中上傳的 Jupyter 筆記本。 在本文中，您必須完成下列步驟：

* 在 HDInsight Spark 叢集上執行指令碼動作來安裝 Microsoft 辨識工具組和 Python 套件。
* 將 Jupyter Notebook 上傳至執行此解決方案的 HDInsight Spark 叢集。

Jupyter Notebook 中涵蓋下列剩餘步驟。

* 將範例映射載入 Spark 復原分散式資料集或 RDD 中。
  * 載入模組並定義預設值。
  * 在 Spark 叢集上本機下載資料集。
  * 將資料集轉換成 RDD。
* 使用定型的 Cognitive Toolkit 模型對映像進行評分。
  * 將定型的 Cognitive Toolkit 模型下載至 Spark 叢集。
  * 定義可供背景工作節點使用的函式。
  * 在背景工作節點上對映像進行評分。
  * 評估模型精確度。

## <a name="install-microsoft-cognitive-toolkit"></a>安裝 Microsoft 辨識工具組

您可以使用指令碼動作在 Spark 叢集上安裝 Microsoft 辨識工具組。 腳本動作會使用自訂腳本，在叢集上安裝預設無法使用的元件。 您可以從 Azure 入口網站、使用 HDInsight .NET SDK 或使用 Azure PowerShell 來使用自訂腳本。 您也可以使用指令碼，在叢集建立期間安裝工具組，或在叢集已啟動並執行之後加以安裝。

在本文中，我們在建立叢集之後，使用入口網站來安裝工具組。 如需執行自訂指令碼的其他方式，請參閱[使用指令碼動作自訂 HDInsight 叢集](../hdinsight-hadoop-customize-cluster-linux.md)。

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

如需如何使用 Azure 入口網站來執行腳本動作的指示，請參閱 [使用腳本動作自訂 HDInsight](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)叢集。 確定您提供下列輸入來安裝 Microsoft 辨識工具組。 針對您的腳本動作使用下列值：

|屬性 |值 |
|---|---|
|指令碼類型|- 自訂|
|名稱| 安裝 MCT|
|Bash 指令碼 URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|節點類型：|Head、Worker|
|參數|None|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>將 Jupyter Notebook 上傳至 Azure HDInsight Spark 叢集

若要使用 Microsoft 辨識工具組搭配 Azure HDInsight Spark 叢集，您必須將 Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** 載入至 Azure HDInsight Spark 叢集。 此筆記本可在 GitHub 上取得 [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) 。

1. 下載並解壓縮 [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) 。

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/jupyter`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 從 Jupyter 筆記本中，選取右上角的 **[上傳** ]，然後流覽至 [下載] 並選取 [檔案] `CNTK_model_scoring_on_Spark_walkthrough.ipynb` 。

    ![將 Jupyter 筆記本上傳至 Azure HDInsight Spark 叢集](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "將 Jupyter 筆記本上傳至 Azure HDInsight Spark 叢集")

1. 再次選取 **[上傳** ]。

1. 上傳筆記本之後，請按一下筆記本的名稱，然後依照筆記本本身的指示操作，以瞭解如何載入資料集及執行此文章。

## <a name="see-also"></a>另請參閱

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>案例

* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：使用 HDInsight 中的 Spark，使用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)
* [在 HDInsight 中使用 Apache Spark 的 Application Insight 遙測資料分析](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式

* [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能

* [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用適用於 IntelliJ IDEA 的 HDInsight 工具外掛程式遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)