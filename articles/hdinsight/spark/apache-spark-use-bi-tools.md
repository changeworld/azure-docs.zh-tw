---
title: 教學課程：使用 Power BI 分析 Azure HDInsight Apache Spark 資料
description: 教學課程 - 使用 Microsoft Power BI 將儲存在 HDInsight 叢集中的 Apache Spark 資料視覺化
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4eb10298ac683c991835f86582d82fb952b314b2
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "82195106"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>教學課程：在 HDInsight 中使用 Power BI 來分析 Apache Spark 資料

在本教學課程中，了解如何使用 Microsoft Power BI 在 Azure HDInsight 中將 Apache Spark 叢集中的資料視覺化。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 使用 Power BI 將 Spark 資料視覺化

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* 完成[教學課程：在 Azure HDInsight 中的 Apache Spark 叢集上載入資料和執行查詢](./apache-spark-load-data-run-query.md)一文。

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)。

* 選擇性：[Power BI 試用訂用帳戶](https://app.powerbi.com/signupredirect?pbi_source=web)。

## <a name="verify-the-data"></a>驗證資料

您在[上一個教學課程](apache-spark-load-data-run-query.md)中建立的 [Jupyter Notebook](https://jupyter.org/) 包含可建立 `hvac` 資料表的程式碼。 本資料表根據的是 `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv` 上所有 HDInsight Spark 叢集上的可用 CSV 檔案。 使用下列程序來驗證資料。

1. 從 Jupyter Notebook 貼上下列程式碼，然後按下 **SHIFT + ENTER** 鍵。 該程式碼會驗證資料表是否存在。

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    輸出看起來如下：

    ![顯示 Spark 中的資料表](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    如果在開始本教學課程之前關閉 Notebook，則會清除 `hvactemptable`，因此其不會包含在輸出中。  從 BI 工具只可以存取儲存在中繼存放區的 Hive 資料表 (在 **isTemporary** 資料行下表示為 **False**)。 在此教學課程中，您會連線到您所建立的 **hvac** 資料表。

2. 將以下程式碼貼入空白儲存格，然後按下 **SHIFT + ENTER** 鍵。 該程式碼會驗證資料表中的資料。

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    輸出看起來如下：

    ![顯示 Spark 中 hvac 資料表的資料列](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. 從 Notebook 的 [檔案]  功能表中，選取 [關閉並終止]  。 關閉 Notebook 來釋放資源。

## <a name="visualize-the-data"></a>將資料視覺化

在本節中，您會使用 Power BI 從 Spark 叢集資料建立視覺效果、報表及儀表板。

### <a name="create-a-report-in-power-bi-desktop"></a>在 Power BI Desktop 中建立報表

使用 Spark 的第一步是連接到 Power BI Desktop 中的叢集，從叢集載入資料，並根據該資料建立基本視覺效果。

1. 開啟 Power BI Desktop。 關閉啟動顯示畫面 (如果它已開啟)。

2. 從 [首頁]  索引標籤，巡覽至 [取得資料]   > [更多...]  。

    ![將資料從 HDInsight Apache Spark 送入 Power BI Desktop](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "將資料從 Apache Spark BI 送入 Power BI")

3. 在搜尋方塊中輸入 `Spark`，選取 [Azure HDInsight Spark]  ，然後選取 [連線]  。

    ![將資料從 Apache Spark BI 送入 Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "將資料從 Apache Spark BI 送入 Power BI")

4. 在 [伺服器]  文字方塊中輸入您的叢集 URL (格式為 `mysparkcluster.azurehdinsight.net`)。

5. 在 [資料連線模式]  之下，選取 [DirectQuery]  。 然後選取 [確定]  。

    您可以搭配 Spark 使用任一種資料連線模式。 如果您使用 DirectQuery，變更會反映在報表中，而不需重新整理整個資料集。 如果您是匯入資料，則必須重新整理資料集，才能看到變更。 如需如何使用以及該在何時使用 DirectQuery 的詳細資訊，請參閱[使用 Power BI 中的 DirectQuery](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/)。

6. 輸入 HDInsight 登入帳戶資訊，然後選取 [連線]  。 預設帳戶名稱為 *admin*。

7. 選取 `hvac` 資料表，等待查看資料的預覽，然後選取 [載入]  。

    ![Spark 叢集使用者名稱和密碼](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark 叢集使用者名稱和密碼")

    Power BI Desktop 擁有連線至 Spark 叢集以及從 `hvac` 資料表載入資料所需的資訊。 資料表及其資料行會顯示在 [欄位]  窗格中。

8. 將每棟建築物之目標溫度和實際溫度間的差異視覺化：

    1. 在 [視覺效果]  窗格中，選取 [區域圖]  。

    2. 將 [BuildingID]  欄位拖曳至 [軸]  ，並將 [ActualTemp]  和 [TargetTemp]  欄位拖曳至 [值]  。

        ![新增值資料行](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "新增值資料行")

        圖表看起來如下：

        ![區域圖總和](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "區域圖總和")

        根據預設，視覺效果會顯示 **ActualTemp** 和 **TargetTemp** 的總和。 在 [視覺效果] 窗格中，選取 [ActualTemp]  與 [TargetTemp]  旁的向下箭頭，您會看到已選取 [總和]  。

    3. 在 [視覺效果] 窗格中，選取 [ActualTemp]  與 [TargetTemp]  旁的向下箭頭，選取 [平均值]  以取得每棟建築物之實際溫度與目標溫度的平均值。

        ![平均值](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "平均值")

        您的資料視覺效果應該類似於螢幕擷取畫面中的視覺效果。 在視覺效果上移動游標可取得相關資料的工具提示。

        ![區域圖](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "區域圖")

9. 巡覽至 [檔案]   > [儲存]  ，輸入檔案的名稱 `BuildingTemperature`，然後選取 [儲存]  。

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>將報告發佈至 Power BI 服務 (選用)

Power BI 服務可讓您跨組織共用報表和儀表板。 在本節中，您會首次發佈資料集和報表。 然後，您會將報表釘選到儀表板。 儀表板通常用來專注於報表中的資料子集。 您的報表中只有一個視覺效果，但逐步執行這些步驟仍然很有用。

1. 開啟 Power BI Desktop。

1. 在 [首頁]  索引標籤上，選取 [發佈]  。

    ![從 Power BI Desktop 發佈](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "從 Power BI Desktop 發行")

1. 選取要發佈資料集和報表的目標工作區，然後選取 [選取]  。 在下列映像中，已選取預設值 [我的工作區]  。

    ![選取要作為資料集和報告發佈目標的工作區](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "選取要作為資料集和報告發佈目標的工作區")

1. 在成功發佈後，選取 [在 Power BI 中開啟 'BuildingTemperature.pbix']  。

    ![發佈成功，按一下以輸入認證](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "發佈成功，按一下以輸入認證")

1. 在 Power BI 服務中，選取 [輸入認證]  。

    ![在 Power BI 服務中輸入認證](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "在 Power BI 服務中輸入認證")

1. 選取 [編輯認證]  。

    ![在 Power BI 服務中編輯認證](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "在 Power BI 服務中編輯認證")

1. 輸入 HDInsight 登入帳戶資訊，然後選取 [登入]  。 預設帳戶名稱為 *admin*。

    ![登入 Spark 叢集](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "登入 Spark 叢集")

1. 在左側窗格中，移至 [工作區]   > [我的工作區]   > [報表]  ，然後選取 [BuildingTemperature]  。

    ![在左窗格的 [報告] 底下列出的報告](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "在左窗格的 [報告] 底下列出的報告")

    您也應該會看到 [BuildingTemperature]  列於左側窗格中 [資料集]  底下。

    現在您在 Power BI Desktop 中建立的視覺效果已可於 Power BI 服務中使用。

1. 將游標暫留在視覺效果上，然後選取右上角的釘選圖示。

    ![Power BI 服務中的報告](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Power BI 服務中的報告")

1. 選取 [新的儀表板]，輸入名稱 `Building temperature`，然後選取 [釘選]  。

    ![釘選到新的儀表板](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "釘選到新的儀表板")

1. 在報表中，選取 [移至儀表板]  。

您的視覺效果已釘選至儀表板 - 您可以將其他視覺效果新增至報告，並將其釘選至相同的儀表板。 如需關於報告與儀表板的詳細資訊，請參閱 [Power BI 中的報告](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)和 [Power BI 中的儀表板](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)。

## <a name="clean-up-resources"></a>清除資源

完成本教學課程之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地刪除該叢集。 您也需支付 HDInsight 叢集的費用 (即使未使用該叢集)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

若要刪除叢集，請參閱[使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Microsoft Power BI 在 Azure HDInsight 中將 Apache Spark 叢集中的資料視覺化。 前進到下一篇文章，以查看您如何建立機器學習應用程式。

> [!div class="nextstepaction"]
> [建立機器學習應用程式](./apache-spark-ipython-notebook-machine-learning.md)