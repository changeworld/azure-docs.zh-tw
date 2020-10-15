---
title: 教學課程：使用 Apache Spark 來載入資料和執行查詢 - Azure HDInsight
description: 教學課程 - 了解如何在 Azure HDInsight 中的 Spark 叢集上載入資料和執行互動式查詢。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: 5eb6788a558e4429296731f1693edd18bf92f98f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77198883"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>教學課程：在 Azure HDInsight 中的 Apache Spark 叢集上載入資料和執行查詢

在本教學課程中，您將了解如何從 csv 檔案建立資料框架，以及如何在 Azure HDInsight 中對 [Apache Spark](https://spark.apache.org/) 叢集執行互動式 Spark SQL 查詢。 在 Spark 中，資料框架是組織成具名資料行的分散式資料集合。 資料框架在概念上等同於關聯式資料庫中的資料表或 R/Python 中的資料框架。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 從 csv 檔案建立資料框架
> * 在資料框架上執行查詢

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Apache Spark 叢集。 請參閱[建立 Apache Spark 叢集](./apache-spark-jupyter-spark-sql-use-portal.md)。

## <a name="create-a-jupyter-notebook"></a>建立 Jupyter Notebook

Jupyter Notebook 是支援各種程式設計語言的互動式 Notebook 環境。 Notebook 可讓您與資料互動、將程式碼與 Markdown 文字相結合，並執行簡單的視覺效果。

1. 將 `SPARKCLUSTER` 取代為 Spark 叢集的名稱，以編輯 URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter`。 然後，在網頁瀏覽器中輸入已編輯的 URL。 出現提示時，輸入叢集的叢集登入認證。

2. 在 Jupyter 網頁中選取 [新增]   > [PySpark]  ，以建立 Notebook。

   ![建立 Jupyter Notebook 以執行互動式 Spark SQL 查詢](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "建立 Jupyter Notebook 以執行互動式 Spark SQL 查詢")

   系統會建立新的 Notebook，並以 Untitled (`Untitled.ipynb`) 的名稱開啟。

    > [!NOTE]  
    > 透過使用 PySpark 核心來建立 Notebook，當您執行第一個程式碼儲存格時，系統便會自動為您建立 `spark` 工作階段。 您不需要明確建立工作階段。

## <a name="create-a-dataframe-from-a-csv-file"></a>從 csv 檔案建立資料框架

應用程式可以直接建立資料框架，其建立來源可以是遠端儲存體 (例如，Azure 儲存體或 Azure Data Lake Storage) 上的檔案或資料夾；Hive 資料表；或是 Spark 所支援的其他資料來源 (例如，Cosmos DB、Azure SQL DB、DW 等等)。 下列螢幕擷取畫面顯示本教學課程中使用之 HVAC.csv 檔案的快照集。 該 csv 檔案會隨附於所有 HDInsight Spark 叢集。 資料會擷取相同建築物的溫度變化。

![互動式 Spark SQL 查詢資料的快照集](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "互動式 Spark SQL 查詢資料的快照集")

1. 將以下程式碼貼入 Jupyter Notebook 的空白資料格，然後按 **SHIFT + ENTER** 以執行此程式碼。 此程式碼會匯入此案例所需的類型：

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    在 Jupyter 中執行互動式查詢時，網頁瀏覽器視窗或索引標籤標題都會顯示 [(忙碌)]  狀態，以及 Notebook 的標題。 您也會在右上角的 **PySpark** 文字旁看到一個實心圓。 作業完成後，實心圓將變成空心圓。

    ![互動式 Spark SQL 查詢的狀態](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "互動式 Spark SQL 查詢的狀態")

1. 請記下傳回的工作階段識別碼。 在上圖中，工作階段識別碼為 0。 如有需要，您可以瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` 來擷取工作階段詳細資料，其中 CLUSTERNAME 是您的 Spark 叢集名稱，而 ID 是您的工作階段識別碼。

1. 執行下列程式碼，以建立資料框架和暫存資料表 (**hvac**)。

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>在資料框架上執行查詢

建立資料表之後，您可以對資料執行互動式查詢。

1. 在 Notebook 的空白儲存格中，執行下列程式碼：

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   此時會顯示下列表格式輸出。

     ![互動式 Spark 查詢結果的資料表輸出](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "互動式 Spark 查詢結果的資料表輸出")

2. 您也可以查看其他視覺效果中的結果。 若要查看相同輸出的區域圖表，請選取 [區域]  ，然後設定其他值，如下所示。

    ![互動式 Spark 查詢結果的區域圖](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "互動式 Spark 查詢結果的區域圖")

3. 從 [Notebook] 功能表列中，瀏覽至 [檔案]   > [儲存與檢查點]  。

4. 如果您現在要開始進行[下一個教學課程](apache-spark-use-bi-tools.md)，請讓 Notebook 保持開啟。 如果還沒有要進行，請關閉 Notebook 以釋出叢集資源：從 [Notebook] 功能表列，瀏覽至 [檔案]   >  [關閉並終止]  。

## <a name="clean-up-resources"></a>清除資源

使用 HDInsight 時，資料和 Jupyter Notebook 會儲存於「Azure 儲存體」或 Azure Data Lake Storage 中，以便您在叢集未處於使用中時安全地將其刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。 如果您打算立即進行下一個教學課程，則可能想要保留叢集。

在 Azure 入口網站中開啟叢集，然後選取 [刪除]  。

![刪除 HDInsight 叢集](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "刪除 HDInsight 叢集")

您也可以選取資源群組名稱來開啟資源群組頁面，然後選取 [刪除資源群組]  。 刪除資源群組時，會同時刪除 HDInsight Spark 叢集及預設儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何從 csv 檔案建立資料框架，以及如何在 Azure HDInsight 中對 Apache Spark 叢集執行互動式 Spark SQL 查詢。 前往下一篇文章，以查看如何將您在 Apache Spark 中註冊的資料提取至 BI 分析工具，例如 Power BI。

> [!div class="nextstepaction"]
> [使用 BI 工具分析資料](apache-spark-use-bi-tools.md)
