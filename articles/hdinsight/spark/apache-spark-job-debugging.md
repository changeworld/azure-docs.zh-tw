---
title: 對 Azure HDInsight 上執行的 Apache Spark 作業進行偵錯
description: 使用 YARN UI、Spark UI 和 Spark 歷程記錄伺服器，追蹤和偵錯在 Azure HDInsight 中的 Spark 叢集上執行的作業
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: bcf2f97e855126c86dbb1d74cd430704e2af3af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932144"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>對 Azure HDInsight 上執行的 Apache Spark 作業進行偵錯

在本文中，您將了解如何使用 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI、Spark UI 和 Spark 歷程記錄伺服器，對 HDInsight 叢集上執行的 [Apache Spark](https://spark.apache.org/) 作業進行追蹤和偵錯。 我們會使用 Spark 叢集中可用的 Notebook 啟動 Spark 作業，**機器學習服務︰使用 MLLib 對食物檢查資料進行預測分析**。 您可以使用下列步驟來追蹤您使用任何其他方法提交的應用程式，例如，**spark-submit**。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

* 您應該開始執行 Notebook， **[機器學習服務︰使用 MLLib 對食物檢查資料進行預測分析](apache-spark-machine-learning-mllib-ipython.md)**。 如需有關如何執行此 Notebook 的指示，請依照下列連結。  

## <a name="track-an-application-in-the-yarn-ui"></a>追蹤 YARN UI 中的應用程式

1. 啟動 YARN UI。 在**群集儀表板**下選擇**紗線**。

    ![Azure 門戶啟動 YARN UI](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > 或者，您也可以從 Ambari UI 啟動 YARN UI。 要啟動 Ambari UI，請在**群集儀表板**下選擇**Ambari 主頁**。 從 Ambari UI，導航到**YARN** > **快速連結**>活動資源管理器>**資源管理器 UI**。

2. 因為您使用 Jupyter Notebook 啟動 Spark 作業，應用程式具有名稱 **remotesparkmagics** (這是從 Notebook 啟動之所有應用程式的名稱)。 根據應用程式名稱選擇應用程式 ID 以獲取有關作業的詳細資訊。 這會啟動應用程式檢視。

    ![火花歷史記錄伺服器 查找 Spark 應用程式 ID](./media/apache-spark-job-debugging/find-application-id1.png)

    對於從 Jupyter Notebook 啟動的應用程式，狀態一律是 [執行中] **** ，直到您結束 Notebook。

3. 從應用程式檢視中，您可以進一步向下鑽研以找出與應用程式和記錄 (stdout/stderr) 相關聯的容器。 您也可以藉由按一下對應至 [追蹤 URL] **** 的連結，即可啟動 Spark UI，如下所示。

    ![Spark 歷史記錄伺服器下載容器日誌](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>追蹤 Spark UI 中的應用程式

在 Spark UI 中，您可以向下鑽研至您先前啟動的應用程式所繁衍的 Spark 作業。

1. 要啟動 Spark UI，請從應用程式視圖中選擇針對**跟蹤 URL**的連結，如上面的螢幕截圖所示。 您可以看到應用程式啟動的所有 Spark 作業在 Jupyter Notebook 中執行。

    ![火花歷史記錄伺服器作業選項卡](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. 選擇 **"執行器**"選項卡以查看每個執行器的處理和存儲資訊。 還可以通過選擇**執行緒轉儲**連結來檢索呼叫堆疊。

    ![火花歷史記錄伺服器執行器選項卡](./media/apache-spark-job-debugging/view-spark-executors.png)

3. 選擇 **"階段"** 選項卡以查看與應用程式關聯的階段。

    ![火花歷史記錄伺服器階段選項卡](./media/apache-spark-job-debugging/view-apache-spark-stages.png "檢視 Spark 階段")

    每個階段可以有多個工作，您可以檢視其執行統計資料，如下所示。

    ![Spark 歷史記錄伺服器階段選項卡詳細資訊](./media/apache-spark-job-debugging/view-spark-stages-details.png "查看火花階段詳細資訊")

4. 從階段詳細資料頁面上，您可以啟動 DAG 視覺效果。 展開頁面頂端的 [DAG 視覺效果] **** 連結，如下所示。

    ![檢視 Spark 階段 DAG 視覺效果](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG 或 Direct Aclyic Graph 代表應用程式中的不同階段。 每個圖形中的藍色方塊表示從應用程式叫用的 Spark 作業。

5. 您也可以從階段詳細資料頁面上，啟動應用程式時間軸檢視。 展開頁面頂端的 [事件時間軸] **** 連結，如下所示。

    ![檢視 Spark 階段事件時間軸](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    這會以時間軸的形式顯示 Spark 事件。 時間軸檢視有三個層級，跨作業、作業內以及階段內。 以上的映像擷取指定階段的時間軸檢視。

   > [!TIP]  
   > 如果您選取 [啟用縮放功能]**** 核取方塊，您可以跨時間軸檢視左右捲動。

6. Spark UI 中的其他索引標籤也提供 Spark 執行個體的實用資訊。

   * 存儲選項卡 - 如果應用程式創建了 RDD，則可以在"存儲"選項卡中找到有關這些操作的資訊。
   * 環境選項卡 - 此選項卡提供有關 Spark 實例的有用資訊，例如：
     * Scala 版本
     * 與叢集相關聯的事件記錄檔目錄
     * 應用程式的執行程式核心數目
     * 等。

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>使用 Spark 歷程記錄伺服器尋找已完成作業的相關資訊

完成作業後，作業的相關資訊會保存在 Spark 歷程記錄伺服器。

1. 要啟動 Spark 歷史記錄伺服器，請從 **"概述"** 頁中選擇 **"群集儀表板**"下的**Spark 歷史記錄伺服器**。

    ![Azure 門戶啟動 Spark 歷史記錄伺服器](./media/apache-spark-job-debugging/launch-spark-history-server.png "啟動火花歷史記錄伺服器1")

   > [!TIP]  
   > 或者，您也可以從 Ambari UI 啟動 Spark 歷程記錄伺服器 UI。 要啟動 Ambari UI，請從"概述"邊欄選項卡中選擇"**群集儀表板**"下的**Ambari 主頁**。 從 Ambari UI，導航到**Spark2** > **快速連結** > **Spark2 歷史記錄伺服器 UI**。

2. 您會看到所有已完成應用程式列出。 有關詳細資訊，請選擇應用程式 ID 以向下切入到應用程式中。

    ![Spark 歷史記錄伺服器已完成的應用程式](./media/apache-spark-job-debugging/view-completed-applications.png "啟動火花歷史記錄伺服器2")

## <a name="see-also"></a>另請參閱

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [使用擴充的 Spark 歷程記錄伺服器進行 Apache Spark 作業偵錯](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>針對資料分析師

* [Apache Spark 和機器學習服務：使用 HDInsight 中的 Spark，使用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)
* [在 HDInsight 中使用 Apache Spark 的 Application Insight 遙測資料分析](apache-spark-analyze-application-insight-logs.md)


### <a name="for-spark-developers"></a>針對 Spark 開發人員

* [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)
* [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用適用於 IntelliJ IDEA 的 HDInsight 工具外掛程式遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)
