---
title: Zeppelin 筆記本 & Apache Spark 叢集-Azure HDInsight
description: 如何在 Azure HDInsight 上搭配使用 Zeppelin Notebook 和 Apache Spark 叢集的逐步指示。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: a692f4dd86d110f7f0a91a862a7b16ac28345de5
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86084523"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集

HDInsight Spark 叢集包含[Apache Zeppelin](https://zeppelin.apache.org/)筆記本。 使用筆記本來執行 Apache Spark 作業。 在本文中，您將學習如何在 HDInsight 叢集上使用 Zeppelin Notebook。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。
* 您叢集主要儲存體的 URI 配置。 配置 `wasb://` 適用于 Azure Blob 儲存體、 `abfs://` Azure Data Lake Storage Gen2 或 `adl://` Azure Data Lake Storage Gen1。 如果已啟用 Blob 儲存體的安全傳輸，則 URI 會是 `wasbs://` 。  如需詳細資訊，請參閱[Azure 儲存體中需要安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

## <a name="launch-an-apache-zeppelin-notebook"></a>啟動 Apache Zeppelin Notebook

1. 從 Spark 叢集**總覽**中，從 [叢集**儀表板**] 選取 [ **Zeppelin 筆記本**]。 輸入叢集的系統管理員認證。  

   > [!NOTE]  
   > 您也可以在瀏覽器中開啟下列 URL，來連接到您叢集的 Zeppelin Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 建立新的 Notebook。 從標頭窗格中，流覽至 [**筆記本**] [  >  **建立新便箋**]。

    ![建立新的 Zeppelin Notebook](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "建立新的 Zeppelin Notebook")

    輸入筆記本的名稱，然後選取 [**建立便箋**]。

3. 確定筆記本標頭顯示 [已連線] 狀態。 其以右上角的綠色點表示。

    ![Zeppelin Notebook 狀態](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Notebook 狀態")

4. 將範例資料載入暫存資料表。 當您在 HDInsight 中建立 Spark 叢集時，會將範例資料檔案 `hvac.csv` 複製到底下的相關聯儲存體帳戶 `\HdiSamples\SensorSampleData\hvac` 。

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

    按**SHIFT + enter** ，或選取段落的 [**播放**] 按鈕來執行程式碼片段。 段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。 輸出會顯示在同一個段落的底部。 螢幕擷取畫面看起來如下圖所示：

    ![從原始資料建立暫存資料表](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "從原始資料建立暫存資料表")

    您也可以為每個段落提供標題。 從段落的右上角，選取 [**設定**] 圖示（sprocket），然後選取 [**顯示標題**]。  

    > [!NOTE]  
    > 所有 HDInsight 版本的 Zeppelin Notebook 中都不支援 %spark2 解譯器，而 HDInsight 4.0 以後的版本不支援 %sh 解譯器。

5. 您現在可以在資料表上執行 Spark SQL 語句 `hvac` 。 將以下查詢貼入新段落。 查詢會抓取建築物識別碼。 此外，每個建築物在指定日期的目標和實際溫度之間的差異。 按下 **SHIFT + ENTER**。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    開頭的 **%Sql** 陳述式會告訴 Notebook 使用 Livy Scala 解譯器。

6. 選取**橫條圖**圖示以變更顯示。  **設定**：在您選取 [**橫條圖**] 之後出現，可讓您選擇 [索引**鍵**] 和 [**值**]。  以下螢幕擷取畫面顯示輸出。

    ![使用 notebook1 執行 Spark SQL 語句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "使用 notebook1 執行 Spark SQL 語句")

7. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。 下一個程式碼片段示範如何 `Temp` 在查詢中，使用您想要查詢的可能值來定義變數。 當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

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

HDInsight 上 Apache Spark 叢集中的 Zeppelin 筆記本可以使用不包含在叢集中的外部、由社區提供的套件。 搜尋[Maven 存放庫](https://search.maven.org/)，以取得可用套件的完整清單。 您也可以從其他來源取得可用套件清單。 例如，從 [Spark 套件](https://spark-packages.org/)可以取得社群提供套件的完整清單。

在本文中，您將瞭解如何搭配 Jupyter 筆記本使用[spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)套件。

1. 開啟解譯器設定。 從右上角選取登入的使用者名稱，然後選取 [**解釋**器]。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 流覽至**livy2**，然後選取 [**編輯**]。

    ![變更解譯器設定1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "變更解譯器設定1")

3. 流覽至 [索引鍵] `livy.spark.jars.packages` ，並以格式設定其值 `group:id:version` 。 因此，如果您想要使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件，您必須將金鑰值設為 `com.databricks:spark-csv_2.10:1.4.0`。

    ![變更解譯器 settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "變更解譯器 settings2")

    依序選取 [**儲存** **] 和 [確定]** 以重新開機 Livy 解譯器。

4. 如果您想要了解如何得出上面所輸入的金鑰值，其方法如下。

    a. 在「Maven 儲存機制」中找出套件。 在本文中，我們使用了[spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。

    b. 從儲存機制收集 [GroupId]****、[ArtifactId]**** 及 [版本]**** 的值。

    ![搭配 Jupyter Notebook 使用外部套件](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "搭配 Jupyter Notebook 使用外部套件")

    c. 串連三個值，其中以冒號分隔 (**:**)。

    ```
    com.databricks:spark-csv_2.10:1.4.0
    ```

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 儲存在哪裡？

Zeppelin Notebook 會儲存到叢集前端節點。 因此，如果您刪除叢集，Notebook 會一併刪除。 如果您想要保留 Notebook 以供稍後用於其他叢集上，您必須在作業執行完成後將 Notebook 匯出。 若要匯出筆記本，請選取 [**匯出**] 圖示，如下圖所示。

![下載 Notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "下載筆記本")

此動作會將筆記本以 JSON 檔案的形式儲存在您的下載位置。

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>用於 `Shiro` 設定企業安全性套件（ESP）叢集中 Zeppelin 解譯器的存取權

如先前所述， `%sh` 從 HDInsight 4.0 開始不支援解譯器。 此外，由於 `%sh` 解譯器引進了潛在的安全性問題，例如使用 shell 命令存取 keytabs，因此它也已從 HDInsight 3.6 ESP 叢集移除。 這表示 `%sh` 在預設情況下，按一下 [**建立新便箋**] 或 [解譯器] UI 時，就無法使用解譯器。

具有特殊許可權的網域使用者可以使用檔案 `Shiro.ini` 來控制解譯器 UI 的存取權。 只有這些使用者可以建立新的 `%sh` 解譯器，並設定每個新解譯器的許可權 `%sh` 。 若要使用檔案控制存取權 `shiro.ini` ，請使用下列步驟：

1. 使用現有的網域組名來定義新的角色。 在下列範例中， `adminGroupName` 是 AAD 中的特殊許可權使用者群組。 請勿在組名中使用特殊字元或空格。 後面的字元會 `=` 授與此角色的許可權。 `*`表示群組具有完整許可權。

    ```
    [roles]
    adminGroupName = *
    ```

2. 新增新角色以存取 Zeppelin 解譯器。 在下列範例中，中的所有使用者 `adminGroupName` 都會獲得 Zeppelin 解譯器的存取權，而且可以建立新的解譯器。 您可以將多個角色放在中的括弧之間，並以 `roles[]` 逗號分隔。 然後，具有必要許可權的使用者可以存取 Zeppelin 解譯器。

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy 工作階段管理

Zeppelin 筆記本中的第一個程式碼片段會在您的叢集中建立新的 Livy 會話。 此會話會在您稍後建立的所有 Zeppelin 筆記本之間共用。 如果 Livy 會話因任何原因而終止，則不會從 Zeppelin 筆記本執行作業。

在這種情況下，您必須先執行下列步驟，才能開始從 Zeppelin 筆記本執行作業。  

1. 從 Zeppelin Notebook 重新啟動 Livy 解譯器。 若要這麼做，請從右上角選取登入的使用者名稱，然後選取 [**解釋**器]，以開啟解譯器設定。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 流覽至**livy2**，然後選取 [**重新開機**]。

    ![重新開機 Livy 解譯器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重新開機 Zeppelin 解譯器")

3. 從現有的 Zeppelin Notebook 執行程式碼單元。 此程式碼會在 HDInsight 叢集中建立新的 Livy 會話。

## <a name="general-information"></a>一般資訊

### <a name="validate-service"></a>驗證服務

若要從 Ambari 驗證服務，請流覽至， `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` 其中 CLUSTERNAME 是您的叢集名稱。

若要從命令列驗證服務，請透過 SSH 連線到前端節點。 使用命令將使用者切換至 zeppelin `sudo su zeppelin` 。 狀態命令：

|Command |描述 |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|服務狀態。|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|服務版本。|
|`ps -aux | grep zeppelin`|識別 PID。|

### <a name="log-locations"></a>記錄檔位置

|服務 |路徑 |
|---|---|
|zeppelin-伺服器|/usr/hdp/current/zeppelin-server/|
|伺服器記錄|/var/log/zeppelin|
|設定解譯器、 `Shiro` 、site.xml、log4j|/usr/hdp/current/zeppelin-server/conf 或/etc/zeppelin/conf|
|PID 目錄|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>啟用偵錯記錄

1. 導覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` ，其中 CLUSTERNAME 是您的叢集名稱。

1. 流覽至 **[**  >  **zeppelin-log4j-properties**]  >  **log4j_properties_content**。

1. `log4j.appender.dailyfile.Threshold = INFO`將修改為 `log4j.appender.dailyfile.Threshold = DEBUG` 。

1. 加入 `log4j.logger.org.apache.zeppelin.realm=DEBUG`。

1. 儲存變更並重新啟動服務。

## <a name="next-steps"></a>後續步驟

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](apache-spark-jupyter-notebook-kernels.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)
