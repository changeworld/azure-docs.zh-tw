---
title: Zeppelin 筆記本 & Apache Spark 叢集-Azure HDInsight
description: 如何在 Azure HDInsight 上搭配使用 Zeppelin Notebook 和 Apache Spark 叢集的逐步指示。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c5c8a41aef92876ceaa66fb23c01c6ece1609f91
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484803"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集

HDInsight Spark 叢集包含可用來執行 [Apache Spark](https://zeppelin.apache.org/) 作業的 [Apache Zeppelin](https://spark.apache.org/) Notebook。 在本文中，您將學習如何在 HDInsight 叢集上使用 Zeppelin Notebook。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。
* 叢集主要儲存體的 URI 配置。 這會針對 Azure Blob 儲存體 `wasb://`，`abfs://` 用於 `adl://` Azure Data Lake Storage Gen2 或 Azure Data Lake Storage Gen1。 如果已啟用 Blob 儲存體的安全傳輸，則 URI 會 `wasbs://`。  如需詳細資訊，請參閱[Azure 儲存體中需要安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

## <a name="launch-an-apache-zeppelin-notebook"></a>啟動 Apache Zeppelin Notebook

1. 從 Spark 叢集**總覽**中，從 [叢集**儀表板**] 選取 [ **Zeppelin 筆記本**]。 輸入叢集的系統管理員認證。  

   > [!NOTE]  
   > 您也可以在瀏覽器中開啟下列 URL，來連接到您叢集的 Zeppelin Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 建立新的 Notebook。 從標頭窗格中，流覽至 **筆記本** > **建立新便箋**。

    ![建立新的 Zeppelin 筆記本](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "建立新的 Zeppelin Notebook")

    輸入筆記本的名稱，然後選取 [**建立便箋**]。

3. 確定筆記本標頭顯示 [已連線] 狀態。 其以右上角的綠色點表示。

    ![Zeppelin 筆記本狀態](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Notebook 狀態")

4. 將範例資料載入暫存資料表。 當您在 HDInsight 中建立 Spark 叢集時，範例資料檔案 `hvac.csv`會複製到 `\HdiSamples\SensorSampleData\hvac`下相關聯的儲存體帳戶。

    將以下程式碼片段貼入新 Notebook 中預設建立的空白段落。

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    按**SHIFT + enter** ，或選取段落的 [**播放**] 按鈕來執行程式碼片段。 段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。 輸出會顯示在同一個段落的底部。 螢幕擷取畫面如下所示：

    ![從原始資料建立臨時表](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "從原始資料建立暫存資料表")

    您也可以為每個段落提供標題。 從段落的右上角，選取 [**設定**] 圖示（sprocket），然後選取 [**顯示標題**]。  

    > [!NOTE]  
    > 所有 HDInsight 版本的 Zeppelin Notebook 中都不支援 %spark2 解譯器，而 HDInsight 4.0 以後的版本不支援 %sh 解譯器。

5. 您現在可以在 `hvac` 資料表上執行 Spark SQL 語句。 將以下查詢貼入新段落。 此查詢會擷取建築物識別碼，以及在指定日期當天每棟建築物之目標溫度與實際溫度間的差異。 按下 **SHIFT + ENTER**。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    開頭的 **%Sql** 陳述式會告訴 Notebook 使用 Livy Scala 解譯器。

6. 選取**橫條圖**圖示以變更顯示。  [**設定**] 會在您選取 [**橫條圖**] 之後出現，可讓您選擇 [索引**鍵**] 和 [**值**]。  以下螢幕擷取畫面顯示輸出。

    ![使用 notebook1 執行 Spark SQL 語句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "使用 notebook1 執行 Spark SQL 語句")

7. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。 下一個程式碼片段示範如何使用您想要查詢的可能值，在查詢中定義變數 `Temp`。 當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    將此程式碼片段貼到新的段落中，然後按下 **SHIFT + ENTER**。 然後從 [**暫存**] 下拉式清單中選取 [ **65** ]。

8. 選取**橫條圖**圖示以變更顯示。  然後選取 [**設定**]，並進行下列變更：

   * **群組：** 新增**targettemp**。  
   * **值：** sha-1. 移除**日期**。  2. 新增**temp_diff**。  3.  將匯總工具從**SUM**變更為**AVG**。  

     以下螢幕擷取畫面顯示輸出。

     ![使用 notebook2 執行 Spark SQL 語句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "使用 notebook2 執行 Spark SQL 語句")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>如何搭配 Notebook 使用外部套件？

您可以在 HDInsight 上的 Apache Spark 叢集中設定 Zeppelin 筆記本，以使用不是叢集中現成可用的外部、提供社區的套件。 您可以搜尋 [Maven 儲存機制](https://search.maven.org/) 來取得可用套件的完整清單。 您也可以從其他來源取得可用套件清單。 例如，從 [Spark 套件](https://spark-packages.org/)可以取得社群提供套件的完整清單。

在本文中，您將瞭解如何搭配 Jupyter 筆記本使用[spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)套件。

1. 開啟解譯器設定。 從右上角選取登入的使用者名稱，然後選取 [**解釋**器]。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 流覽至**livy2**，然後選取 [**編輯**]。

    ![變更解譯器設定1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "變更解譯器設定1")

3. 流覽至 [金鑰 `livy.spark.jars.packages`]，並將其值設定為 [`group:id:version`] 格式。 因此，如果您想要使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件，您必須將金鑰值設為 `com.databricks:spark-csv_2.10:1.4.0`。

    ![變更解譯器 settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "變更解譯器 settings2")

    依序選取 [**儲存** **] 和 [確定]** 以重新開機 Livy 解譯器。

4. 如果您想要了解如何得出上面所輸入的金鑰值，其方法如下。

    a. 在「Maven 儲存機制」中找出套件。 在本文中，我們使用了[spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。

    b. 從儲存機制收集 [GroupId]、[ArtifactId] 及 [版本] 的值。

    ![搭配 Jupyter 筆記本使用外部套件](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "搭配 Jupyter Notebook 使用外部套件")

    c. 串連三個值，其中以冒號分隔 ( **:** )。

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 儲存在哪裡？

Zeppelin Notebook 會儲存到叢集前端節點。 因此，如果您刪除叢集，Notebook 會一併刪除。 如果您想要保留 Notebook 以供稍後用於其他叢集上，您必須在作業執行完成後將 Notebook 匯出。 若要匯出筆記本，請選取 [**匯出**] 圖示，如下圖所示。

![下載筆記本](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "下載筆記本")

這會將 Notebook 以 JSON 檔案的形式儲存在下載位置中。

## <a name="livy-session-management"></a>Livy 工作階段管理

當您在 Zeppelin Notebook 中執行第一個程式碼段落時，HDInsight Spark 叢集中便會建立新的 Livy 工作階段。 此工作階段可供您後續建立的所有 Zeppelin Notebook 共用。 如果基於某些原因而終止 Livy 會話（叢集重新開機等等），您將無法從 Zeppelin 筆記本執行作業。

在這種情況下，您必須先執行下列步驟，然後才能開始從 Zeppelin Notebook 執行作業。  

1. 從 Zeppelin Notebook 重新啟動 Livy 解譯器。 若要這麼做，請從右上角選取登入的使用者名稱，然後選取 [**解釋**器]，以開啟解譯器設定。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 流覽至**livy2**，然後選取 [**重新開機**]。

    ![重新開機 Livy 解譯器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重新開機 Zeppelin 解譯器")

3. 從現有的 Zeppelin Notebook 執行程式碼單元。 這會在 HDInsight 叢集中建立新的 Livy 工作階段。

## <a name="general-information"></a>一般資訊

### <a name="validate-service"></a>驗證服務

若要從 Ambari 驗證服務，請流覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`，其中 CLUSTERNAME 是您的叢集名稱。

若要從命令列驗證服務，請透過 SSH 連線到前端節點。 使用命令 `sudo su zeppelin`將使用者切換至 zeppelin。 狀態命令：

|Command |描述 |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|服務狀態。|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|服務版本。|
|`ps -aux | grep zeppelin`|識別 PID。|

### <a name="log-locations"></a>記錄檔位置

|服務 |Path |
|---|---|
|zeppelin-伺服器|/usr/hdp/current/zeppelin-server/|
|伺服器記錄|/var/log/zeppelin|
|設定解譯器，Shiro，log4j|/usr/hdp/current/zeppelin-server/conf 或/etc/zeppelin/conf|
|PID 目錄|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>啟用偵錯記錄

1. 流覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`，其中 CLUSTERNAME 是您的叢集名稱。

1. 流覽至 **[**  > **Advanced zeppelin-log4j-properties** ] > **log4j_properties_content**的 [內容]。

1. 修改 `log4j.appender.dailyfile.Threshold = INFO` 以 `log4j.appender.dailyfile.Threshold = DEBUG`。

1. 加入 `log4j.logger.org.apache.zeppelin.realm=DEBUG`。

1. 儲存變更並重新啟動服務。

## <a name="next-steps"></a>後續步驟

[概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>案例

* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：使用 HDInsight 中的 Spark，使用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式

* [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能

* [使用適用於 IntelliJ IDEA 的 Azure 工具組建立適用於 HDInsight 叢集的 Spark 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用適用於 IntelliJ IDEA 的 HDInsight 工具外掛程式遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
