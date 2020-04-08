---
title: 澤佩林筆記本&阿帕奇火花集群 - Azure HDInsight
description: 如何在 Azure HDInsight 上搭配使用 Zeppelin Notebook 和 Apache Spark 叢集的逐步指示。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 3c1369e813ba6518f6cd4b27082020ae36a24c82
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811196"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集

HDInsight Spark 集群包括[阿帕奇·澤佩林](https://zeppelin.apache.org/)筆記本。 使用筆記本運行 Apache [Spark](https://spark.apache.org/)作業。 在本文中，您將學習如何在 HDInsight 叢集上使用 Zeppelin Notebook。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。
* 您叢集主要儲存體的 URI 配置。 該方案適用於`wasb://`Azure Blob 儲存、Azure`abfs://`資料`adl://`湖儲存第 2 代或 Azure 資料湖儲存第 1 代。 如果為 Blob 儲存啟用了安全傳輸`wasbs://`,則 URI 將為 。  有關詳細資訊,請參閱在[Azure 儲存中要求安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

## <a name="launch-an-apache-zeppelin-notebook"></a>啟動 Apache Zeppelin Notebook

1. 從 Spark 叢集**概述**中,從**叢集儀錶板**中選擇**Zeppelin 筆記本**。 輸入群集的管理員憑據。  

   > [!NOTE]  
   > 您也可以在瀏覽器中開啟下列 URL，來連接到您叢集的 Zeppelin Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 建立新的 Notebook。 從標題窗格中導航到 **「筆記本** > **創建新便箋**」。。

    ![建立新的 Zeppelin Notebook](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "建立新的 Zeppelin Notebook")

    輸入筆記本的名稱,然後選擇 **"創建註釋**"。

3. 確保筆記本頭顯示已連接狀態。 它由右上角的綠點表示。

    ![Zeppelin Notebook 狀態](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Notebook 狀態")

4. 將範例資料載入暫存資料表。 在 HDInsight 中創建 Spark 群集時`hvac.csv`,範例資料 檔將`\HdiSamples\SensorSampleData\hvac`複製到 下的相關 儲存帳戶。

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

    按**SHIFT = ENTER**或為段落選擇 **「播放」** 按鈕以執行代碼段。 段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。 輸出會顯示在同一個段落的底部。 螢幕截圖如下所示:

    ![從原始資料建立暫存資料表](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "從原始資料建立暫存資料表")

    您也可以為每個段落提供標題。 從段落的右角,選擇 **「設定」** 圖示(鏈輪),然後選擇 **「顯示標題**」。。  

    > [!NOTE]  
    > 所有 HDInsight 版本的 Zeppelin Notebook 中都不支援 %spark2 解譯器，而 HDInsight 4.0 以後的版本不支援 %sh 解譯器。

5. 您現在可以在表上運行`hvac`Spark SQL 語句。 將以下查詢貼入新段落。 查詢檢索建築物 ID。 此外,給定日期每個建築物的目標和實際溫度之間的差異。 按**SHIFT = 輸入**。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    開頭的 **%Sql** 陳述式會告訴 Notebook 使用 Livy Scala 解譯器。

6. 選擇 **「條形圖」** 圖示以更改顯示。  **設定**, 選擇**項目**圖 後顯示 , 讓您選擇**鍵**與**值**。  以下螢幕擷取畫面顯示輸出。

    ![使用筆記本執行 Spark SQL 語句1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "使用筆記本執行 Spark SQL 語句1")

7. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。 下一個程式碼段演示如何在查詢中`Temp`定義 變數 ,以及要查詢的可能值。 當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    將此程式碼片段貼到新的段落中，然後按下 **SHIFT + ENTER**。 然後從 **「臨時**」下拉清單中選擇**65。**

8. 選擇 **「條形圖」** 圖示以更改顯示。  然後選擇**設定**並進行以下變更:

   * **群組:** 新增**目標溫度**。  
   * **值:** 1. 刪除**日期**。  2. 新增**temp_diff**。  3.  將聚合器從**SUM**更改為**AVG**。  

     以下螢幕擷取畫面顯示輸出。

     ![使用筆記本執行 Spark SQL 語句2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "使用筆記本執行 Spark SQL 語句2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>如何搭配 Notebook 使用外部套件？

HDInsight 上的 Apache Spark 群集中的 Zepelin 筆記本可以使用群集中未包括的外部社區貢獻包。 在[Maven 儲存庫](https://search.maven.org/)中搜索可用的包的完整清單。 您也可以從其他來源取得可用套件清單。 例如，從 [Spark 套件](https://spark-packages.org/)可以取得社群提供套件的完整清單。

在本文中,您將瞭解如何將[Spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)包與 Jupyter 筆記本一起使用。

1. 開啟解譯器設定。 從右上角,選擇登錄的使用者名,然後選擇 **「解釋器**」。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 捲軸到**livy2,** 然後選擇**編輯**。

    ![變更直譯器設定1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "變更直譯器設定1")

3. 導覽到`livy.spark.jars.packages`鍵 ,`group:id:version`並在格式中設定值 。 因此，如果您想要使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件，您必須將金鑰值設為 `com.databricks:spark-csv_2.10:1.4.0`。

    ![變更直譯器設定2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "變更直譯器設定2")

    選擇 **「儲存****」,然後選擇「確定」** 以重新啟動 Livy 解釋器。

4. 如果您想要了解如何得出上面所輸入的金鑰值，其方法如下。

    a. 在「Maven 儲存機制」中找出套件。 在本文中,我們使用[火花-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。

    b. 從儲存機制收集 [GroupId]****、[ArtifactId]**** 及 [版本]**** 的值。

    ![搭配 Jupyter Notebook 使用外部套件](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "搭配 Jupyter Notebook 使用外部套件")

    c. 串連三個值，其中以冒號分隔 (**:**)。

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 儲存在哪裡？

Zeppelin Notebook 會儲存到叢集前端節點。 因此，如果您刪除叢集，Notebook 會一併刪除。 如果您想要保留 Notebook 以供稍後用於其他叢集上，您必須在作業執行完成後將 Notebook 匯出。 要匯出筆記本,請選擇「**匯出**」圖示,如下圖所示。

![下載 Notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "下載筆記本")

此操作將筆記本保存為下載位置中的 JSON 檔。

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>設定`Shiro`企業安全套件 (ESP) 群組集中對 Zeppelin 解譯器的存取

如上所述,HDInsight `%sh` 4.0 以後不支援解釋器。 此外,由於`%sh`解釋器引入了潛在的安全問題,例如使用shell命令存取密鑰表,因此也將其從HDInsight 3.6 ESP群集中刪除。 這意味著`%sh`默認情況下,按一下 **「創建新註釋」** 或「解釋器 UI」時,解釋器不可用。

特權域使用者可以使用該檔`Shiro.ini`來控制對解釋器 UI 的訪問。 只有這些用戶可以創建新`%sh`的解釋器並為每個`%sh`新 解釋器設置許可權。 要控制使用該檔案的存`shiro.ini`取 ,請使用以下步驟:

1. 使用現有域組名稱定義新角色。 在下面的範例中,`adminGroupName`是 AAD 中的一組特權使用者。 不要在群組名稱中使用特殊字元或空格。 後`=`字元授予此角色的許可權。 `*`表示組具有完全許可權。

    ```
    [roles]
    adminGroupName = *
    ```

2. 增加新的角色,以訪問澤佩林口譯員。 在下面的示例中,中的所有`adminGroupName`使用者都可以訪問 Zepelin 口譯員,並可以創建新的口譯員。 您可以在 括弧之間放置多個`roles[]`角色 ,用逗號分隔。 然後,具有必要許可權的用戶可以訪問 Zeppelin 解釋器。

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy 工作階段管理

Zepelin 筆記本中的第一個代碼段落會在群集中創建新的 Livy 工作階段。 此會話在您以後創建的所有 Zeppelin 筆記本中共用。 如果 Livy 工作階段因任何原因被終止,則作業不會從 Zepelin 筆記本上運行。

在這種情況下,您必須執行以下步驟,然後才能從 Zepelin 筆記本開始運行作業。  

1. 從 Zeppelin Notebook 重新啟動 Livy 解譯器。 為此,請從右上角選擇登錄的使用者名打開解釋器設置,然後選擇 **「解釋器**」。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 捲軸到**livy2,** 然後選擇**重新啟動**。

    ![重新啟動利維直譯器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重新啟動齊柏林譯員")

3. 從現有的 Zeppelin Notebook 執行程式碼單元。 此代碼在 HDInsight 群集中創建新的 Livy 工作階段。

## <a name="general-information"></a>一般資訊

### <a name="validate-service"></a>驗證服務

要驗證來自 Ambari 的服務`https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`,請瀏覽到 CLUSTERNAME 是叢集名稱的位置。

要驗證從命令行的服務,SSH 到頭節點。 使用`sudo su zeppelin`命令將用戶切換到澤佩林。 狀態命令:

|Command |描述 |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|服務狀態。|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|服務版本。|
|`ps -aux | grep zeppelin`|識別 PID。|

### <a name="log-locations"></a>紀錄位置

|服務 |Path |
|---|---|
|澤佩林-伺服器|/usr/hdp/電流/澤佩林-伺服器/|
|伺服器記錄|/var/日誌/澤佩林|
|設定直譯`Shiro`器, . site.xml, log4j|/usr/hdp/電流/澤佩林-伺服器/conf 或 /etc/澤佩林/conf|
|PID 目錄|/var/運行/澤佩林|

### <a name="enable-debug-logging"></a>啟用偵錯記錄

1. 導航到`https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary`CLUSTERNAME 是群集名稱的位置。

1. 導覽到**CONFIGS** > **進階 zeppelin-log4j 屬性** > **log4j_properties_content**。

1. 變更`log4j.appender.dailyfile.Threshold = INFO`為`log4j.appender.dailyfile.Threshold = DEBUG`。

1. 加入 `log4j.logger.org.apache.zeppelin.realm=DEBUG`。

1. 保存更改並重新啟動服務。

## <a name="next-steps"></a>後續步驟

[概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>案例

* [帶 BI 的 Apache Spark:使用 HDInsight 中的 Spark 與 BI 工具進行互動式資料分析](apache-spark-use-bi-tools.md)
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
