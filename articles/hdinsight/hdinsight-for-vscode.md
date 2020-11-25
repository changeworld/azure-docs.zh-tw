---
title: Visual Studio Code 的 Azure HDInsight
description: 瞭解如何使用 Spark & Hive 工具 (Azure HDInsight) 進行 Visual Studio Code。 使用這些工具來建立及提交查詢和腳本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: 302f1a081ca44cf6436f2c318b03e227f6640489
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001961"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>使用 Spark & Hive Tools for Visual Studio Code

了解如何使用 Apache Spark & Hive Tools for Visual Studio Code。 使用工具建立並提交適用於 Apache Spark 的 Apache Hive 批次作業、互動式 Hive 查詢和 PySpark 指令碼。 首先，我們將說明如何在 Visual Studio Code 中安裝 Spark & Hive Tools。 然後，我們將逐步解說如何將作業提交至 Spark & Hive Tools。  

您可以在 Visual Studio Code 支援的平台上安裝 Spark & Hive Tools。 請留意下列適用於不同平台的必要條件。

## <a name="prerequisites"></a>必要條件

若要完成此文章中的步驟，將會需要下列項目：

- Azure HDInsight 叢集。 若要建立叢集，請參閱[開始使用 HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)。 或使用支援 Apache Livy 端點的 Spark 和 Hive 叢集。
- [Visual Studio Code](https://code.visualstudio.com/) \(英文\)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/) \(英文\)。 只有 Linux 和 macOS 才需要 Mono。
- [Visual Studio Code 的 PySpark 互動式環境](set-up-pyspark-interactive-environment.md)。
- 本機目錄。 此文章使用 **C:\HD\HDexample**。

## <a name="install-spark--hive-tools"></a>安裝 Spark & Hive Tools

符合必要條件後，您即可依照下列步驟安裝 Spark & Hive Tools for Visual Studio Code：

1. 開啟 Visual Studio Code。

2. 從功能表列，瀏覽至 [檢視] > [擴充功能]。

3. 在搜尋方塊中，輸入 **Spark & Hive**。

4. 從搜尋結果中選取 [Spark & Hive Tools]，然後選取 [安裝]：

   ![Spark & Hive for Visual Studio Code Python 安裝](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 視需要選取 [重新載入]。

## <a name="open-a-work-folder"></a>開啟工作資料夾

若要在 Visual Studio Code 中開啟工作資料夾並建立檔案，請執行下列步驟：

1. 從功能表列，**流覽至 [** 檔案  >  **開啟資料夾**  >  ]。**C:\HD\HDexample**，然後選取 [**選取資料夾**] 按鈕。 該資料夾會出現在左側的 [檔案總管] 檢視中。

2. 在 **Explorer** 視圖中，選取 [ **先前 >hdexample** ] 資料夾，然後選取工作資料夾旁的 [ **新增** 檔案] 圖示：

   ![Visual Studio Code 的新增檔案圖示](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. 使用 `.hql` (Hive 查詢) 或 `.py` (Spark 腳本) 副檔名來為新檔案命名。 這個範例使用 **HelloWorld.hql**。

## <a name="set-the-azure-environment"></a>設定 Azure 環境

針對國家雲端使用者，請遵循下列步驟來先設定 Azure 環境，然後使用 **azure：登入** 命令來登入 azure：

1. 流覽至 **[** 檔案  >  **喜好**  >  **設定**]。
2. 搜尋下列字串： **Azure：雲端**。
3. 從清單中選取國家/地區雲端：

   ![設定預設的登入項目組態](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>連線到 Azure 帳戶

您可以從 Visual Studio Code 將腳本提交至叢集之前，使用者可以登入 Azure 訂用帳戶，或 [連結 HDInsight](#link-a-cluster)叢集。 針對 ESP 叢集使用 Ambari 使用者名稱/密碼或加入網域的認證，以連線到您的 HDInsight 叢集。 依照下列步驟連線至 Azure：

1. 從功能表列中，瀏覽至 [檢視] >  [命令選擇區...]，然後輸入 **Azure:Sign In**：

   ![Spark & Hive Tools for Visual Studio Code 登入](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 依照登入指示登入 Azure。 連線之後，您的 Azure 帳戶名稱會顯示在 Visual Studio Code 視窗底部的狀態列上。  

## <a name="link-a-cluster"></a>連結叢集

### <a name="link-azure-hdinsight"></a>連結： Azure HDInsight

您可以使用 [Apache Ambari](https://ambari.apache.org/)管理的使用者名稱來連結一般叢集，也可以使用網域使用者 (名稱（例如：) ）來連結企業安全性套件保護 Hadoop 叢集 `user1@contoso.com` 。

1. 從功能表列，流覽至 [ **View**  >  **命令** 選擇區]，然後輸入 **Spark/Hive： Link a Cluster**。

   ![命令選擇區連結叢集命令](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 選取連結的叢集類型 **Azure HDInsight**。

3. 輸入 HDInsight 叢集 URL。

4. 輸入您的 Ambari 使用者名稱;預設值為 **admin**。

5. 輸入您的 Ambari 密碼。

6. 選取叢集類型。

7. 設定叢集的顯示名稱 (選擇性) 。

8. 檢閱 [輸出] 檢視以確認。

   > [!NOTE]  
   > 如果叢集已登入 Azure 訂用帳戶並連結叢集，則會使用連結的使用者名稱和密碼。  

### <a name="link-generic-livy-endpoint"></a>連結：泛型 Livy 端點

1. 從功能表列，流覽至 [ **View**  >  **命令** 選擇區]，然後輸入 **Spark/Hive： Link a Cluster**。

2. 選取連結的叢集類型 [泛型 Livy 端點]。

3. 輸入泛型 Livy 端點。 例如： HTTP \: //10.172.41.42：18080。

4. 選取授權類型 [基本] 或 [無]。  如果您選取 [ **基本**]：  
   
   1. 輸入您的 Ambari 使用者名稱;預設值為 **admin**。  

   2. 輸入您的 Ambari 密碼。

5. 檢閱 [輸出] 檢視以確認。

## <a name="list-clusters"></a>列出叢集

1. 從功能表列，流覽至 [ **View**  >  **命令** 選擇區]，然後輸入 **Spark/Hive： List Cluster**。

2. 選取您要的訂用帳戶。

3. 檢閱 [輸出] 檢視。 此視圖會顯示您的連結叢集 (或叢集) 和您 Azure 訂用帳戶下的所有叢集：

   ![設定預設叢集設定](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>設定預設叢集

1. 如果已關閉，請重新開啟稍 [早](#open-a-work-folder)所討論的 **先前 >hdexample** 資料夾。  

2. 選取稍 [早](#open-a-work-folder)建立的 **HelloWorld。** 檔案會在指令碼編輯器中開啟。

3. 以滑鼠右鍵按一下腳本編輯器，然後選取 [ **Spark/Hive：設定預設** 叢集]。  

4. [連接](#connect-to-an-azure-account) 到您的 Azure 帳戶，或連結叢集（如果您尚未這樣做）。

5. 選取某個叢集作為目前指令檔的預設叢集。 工具會自動更新 **.VSCode\settings.json** 組態檔：

   ![設定預設叢集設定](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>提交互動式 Hive 查詢和 Hive 批次腳本

使用 Spark & Hive Tools for Visual Studio Code，您可以將互動式 Hive 查詢和 Hive 批次腳本提交至您的叢集。

1. 如果已關閉，請重新開啟稍 [早](#open-a-work-folder)所討論的 **先前 >hdexample** 資料夾。  

2. 選取稍 [早](#open-a-work-folder)建立的 **HelloWorld。** 檔案會在指令碼編輯器中開啟。

3. 將下列程式碼複製並貼到 Hive 檔案中，然後儲存它：

   ```hiveql
   SELECT * FROM hivesampletable;
   ```

4. [連接](#connect-to-an-azure-account) 到您的 Azure 帳戶，或連結叢集（如果您尚未這樣做）。

5. 以滑鼠右鍵按一下腳本編輯器，然後選取 [ **Hive： Interactive** ] 來提交查詢，或使用 Ctrl + Alt + I 鍵盤快速鍵。  選取 [ **Hive：批次** ] 以提交腳本，或使用 Ctrl + Alt + H 鍵盤快速鍵。  

6. 如果您尚未指定預設叢集，請選取叢集。 這些工具也可讓您使用內容功能表來提交程式碼區塊，而非整個腳本檔案。 幾分鐘之後，查詢結果會出現在新的索引標籤中：

   ![互動式 Apache Hive 查詢結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

   - **結果** 面板：您可以將整個結果以 CSV、JSON 或 Excel 檔案的形式儲存到本機路徑，或只選取多行。

   - **訊息** 面板：當您選取 **行** 號時，它會跳至執行中腳本的第一行。

## <a name="submit-interactive-pyspark-queries"></a>提交互動式 PySpark 查詢

使用者可以透過下列方式執行 PySpark 互動：

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>在 .PY 檔案中使用 PySpark interactive 命令
使用 PySpark interactive 命令提交查詢，並執行下列步驟：

1. 如果已關閉，請重新開啟稍 [早](#open-a-work-folder)所討論的 **先前 >hdexample** 資料夾。  

2. 依照 [先前的](#open-a-work-folder)步驟建立新檔案 **HelloWorld.py**。

3. 將下列程式碼複製並貼到該指令檔：

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. 視窗右下角會顯示安裝 PySpark/Synapse Pyspark 核心的提示。 您可以按一下 [安裝] 按鈕以繼續進行 PySpark/Synapse Pyspark 安裝，或按一下 [略過] 按鈕以略過此步驟。

   ![螢幕擷取畫面顯示略過 PySpark 安裝的選項。](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. 如果您稍後需要安裝它，您可以 **流覽至檔案**  >  **喜好**  >  **設定**，然後取消核取 [設定] 中的 [ **HDInsight：啟用 Skip Pyspark 安裝**]。 
    
    ![螢幕擷取畫面顯示啟用 Skip Pyspark 安裝的選項。](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. 如果在步驟4中安裝成功，視窗的右下角就會顯示 [已成功安裝 PySpark] 訊息方塊。 按一下 [重新載入] 按鈕以重新載入視窗。

   ![已成功安裝 pyspark](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)

7. 從功能表列中瀏覽至 [檢視] > [命令選擇區...]，或使用 **Shift + Ctrl + P** 鍵盤快速鍵，然後輸入 **Python:Select Interpreter to start Jupyter Server**。

   ![選取解譯器以啟動 Jupyter 伺服器](./media/hdinsight-for-vscode/select-interpreter-to-start-jupyter-server.png)

8. 選取下列 Python 選項。

   ![選擇下列選項](./media/hdinsight-for-vscode/choose-the-below-option.png)
    
9. 從功能表列中瀏覽至 [檢視] > [命令選擇區...]，或使用 **Shift + Ctrl + P** 鍵盤快速鍵，然後輸入 [Developer:重新載入視窗]。

   ![重新載入視窗](./media/hdinsight-for-vscode/reload-window.png)

10. [連接](#connect-to-an-azure-account) 到您的 Azure 帳戶，或連結叢集（如果您尚未這樣做）。

11. 選取所有程式碼，以滑鼠右鍵按一下腳本編輯器，然後選取 [ **Spark： PySpark interactive/Synapse： PySpark interactive** ] 提交查詢。 

    ![[PySpark 互動式] 內容功能表](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

12. 如果您尚未指定預設叢集，請選取叢集。 經過幾分鐘之後， **Python 互動式** 結果會出現在新的索引標籤中。按一下 [PySpark] 將核心切換到 **PySpark/Synapse PySpark**，程式碼將會成功執行。 如果您想要切換到 Synapse Pyspark 核心，建議您在 Azure 入口網站中停用自動設定。 否則，喚醒叢集並設定 Synapse 核心以供首次使用，可能需要很長的時間。 如果工具也可讓您使用內容功能表來提交程式碼區塊，而非整個腳本檔案：

    ![PySpark 互動式 Python 互動式視窗](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

13. 輸入 **%% info**，然後按 Shift + Enter 以查看作業資訊 (選擇性) ：

    ![pyspark 互動視圖作業資訊](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

此工具也支援 **SPARK SQL** 查詢：

  ![pyspark 互動視圖結果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>使用 #%% 註解在 PY 檔案中執行互動式查詢

1. 在 **#%%** .py 程式碼之前新增以取得筆記本體驗。

   ![加上 #%%](./media/hdinsight-for-vscode/run-cell.png)

2. 按一下 [執行資料格]。 經過幾分鐘之後，Python 互動式結果會出現在新的索引標籤中。按一下 [PySpark] 將核心切換至 [PySpark]/[Synapse PySpark]，然後再按一下 [ **執行資料格** ]，程式碼就會順利執行。

   ![執行資料格結果](./media/hdinsight-for-vscode/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>運用 Python 延伸模組的 IPYNB 支援

1. 您可以從命令選擇區使用命令建立 Jupyter Notebook，或藉由在工作區中建立新的 .ipynb 檔案來建立。 如需詳細資訊，請參閱[在 Visual Studio Code 中使用 Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support)

2. 按一下 [執行資料格] 按鈕，依照提示 **設定預設 Spark 集區** (強烈建議在每次開啟筆記本之前都設定預設叢集/集區)，然後 **重新載入** 視窗。

   ![設定預設 Spark 集區並重新載入](./media/hdinsight-for-vscode/set-the-default-spark-pool-and-reload.png)

3. 按一下 [PySpark] 以將核心切換到 **PySpark/Synapse PySpark**，然後按一下 [ **執行資料格**]，然後在一段時間後顯示結果。

   ![執行 ipynb 結果](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


> [!NOTE]
> 此延伸模組不支援 2020.5.78807 版或更新版本的 ms-python，這是[已知問題](#known-issues)。

## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批次工作

1. 如果已關閉，請重新開啟您稍 [早](#open-a-work-folder)所討論的 **先前 >hdexample** 資料夾。  

2. 依照 [先前](#open-a-work-folder)的步驟建立新檔案 **BatchFile.py**。

3. 將下列程式碼複製並貼到該指令檔：

   ```python
   from __future__ import print_function
   import sys
   from operator import add
   from pyspark.sql import SparkSession
   if __name__ == "__main__":
       spark = SparkSession\
           .builder\
           .appName("PythonWordCount")\
           .getOrCreate()
   
       lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
       counts = lines.flatMap(lambda x: x.split(' '))\
                  .map(lambda x: (x, 1))\
                   .reduceByKey(add)
       output = counts.collect()
       for (word, count) in output:
           print("%s: %i" % (word, count))
       spark.stop()
   ```

4. [連接](#connect-to-an-azure-account) 到您的 Azure 帳戶，或連結叢集（如果您尚未這樣做）。

5. 以滑鼠右鍵按一下腳本編輯器，然後選取 [ **Spark： PySpark 批次**] 或 [ **Synapse： PySpark batch** _]。

6. 選取要提交 PySpark 作業的叢集/spark 集區：

   ![提交 Python 作業結果輸出](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

提交 Python 作業之後，提交記錄會出現在 Visual Studio Code 的 [_ *輸出*] 視窗中。 此外也會顯示 Spark UI URL 和 Yarn UI URL。 如果您將批次作業提交至 Apache Spark 集區，則也會顯示 Spark 歷程記錄 UI URL 和 Spark 作業應用程式 UI URL。 您可以在網頁瀏覽器中開啟該 URL 來追蹤作業狀態。

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>與 HDInsight Identity Broker (HIB) 整合

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>使用識別碼代理程式連接到您的 HDInsight ESP 叢集 (HIB) 

您可以遵循一般步驟來登入 Azure 訂用帳戶，以連線到具有 ID Broker 的 HDInsight ESP 叢集 (HIB) 。 登入之後，您會在 Azure Explorer 中看到叢集清單。 如需詳細，請參閱[連線至 HDInsight 叢集](#connect-to-an-azure-account)。

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>在識別碼為 Broker (HIB 的 HDInsight ESP 叢集上執行 Hive/PySpark 作業) 

若要執行 hive 作業，您可以遵循一般步驟，將作業提交至具有識別碼 Broker 的 HDInsight ESP 叢集 (HIB) 。 如需詳細指示，請參閱 [提交互動式 Hive 查詢和 Hive 批次腳本](#submit-interactive-hive-queries-and-hive-batch-scripts) 。

若要執行互動式 PySpark 作業，您可以遵循一般步驟，將作業提交至 HDInsight ESP 叢集（識別碼為 Broker） (HIB) 。 如需詳細指示，請參閱 [提交互動式 PySpark 查詢](#submit-interactive-pyspark-queries) 。

若要執行 PySpark 批次作業，您可以遵循一般步驟，將作業提交至 HDInsight ESP 叢集（識別碼為 Broker） (HIB) 。 如需詳細指示，請參閱 [提交 PySpark 批次作業](#submit-pyspark-batch-job) 。

## <a name="apache-livy-configuration"></a>Apache Livy 設定

支援[Apache Livy](https://livy.incubator.apache.org/)設定。 您可以在工作區資料夾的 **.VSCode\settings.js** 檔案中進行設定。 目前 Livy 設定僅支援 Python 指令碼。 如需詳細資訊，請參閱 [LIVY 讀我檔案](https://github.com/cloudera/livy/blob/master/README.rst )。

<a id="triggerlivyconf"></a>**如何觸發 Livy 設定**

### <a name="method-1"></a>方法 1  

1. 從功能表列，**流覽至 [** 檔案  >  **喜好**  >  **設定**]。
2. 在 [ **搜尋設定** ] 方塊中，輸入 **HDInsight 作業提交： Livy 會議**。  
3. 選取 [在 settings.json 中編輯] 以取得相關的搜尋結果。

### <a name="method-2"></a>方法 2

提交檔案，並注意 `.vscode` 資料夾會自動新增至工作資料夾。 您可以選取 [ **開啟.vscode\settings.js**] 來查看 Livy 設定。

- 專案設定：

  ![HDInsight Apache Livy 設定](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

  >[!NOTE]
  >針對 [ **driverMemory** ] 和 [ **executorMemory** ] 設定，設定值和單位。 例如：1g 或1024m。

- 支援的 Livy 設定：

  **POST /batches**
  
  **要求本文**

  | 名稱 | description | 類型 |
  | --- | --- | --- |
  | 檔案 | 包含要執行之應用程式的檔案 | 需要 (路徑)  |
  | proxyUser | 執行作業時要模擬的使用者 | String |
  | className | 應用程式 Java/Spark 主要類別 | String |
  | args | 應用程式的命令列引數 | 字串清單 |
  | jars | 要在此會話中使用的 jar | 字串清單 | 
  | pyFiles | 要用於此工作階段的 Python 檔案 | 字串清單 |
  | files | 要在此會話中使用的檔案 | 字串清單 |
  | driverMemory | 要用於驅動程式程序的記憶體數量 | String |
  | driverCores | 要用於驅動程式程序的核心數目 | Int |
  | executorMemory | 要用於每個執行程式程序的記憶體數量 | String |
  | executorCores | 要用於每個執行程式的核心數目 | Int |
  | numExecutors | 要針對此工作階段啟動的執行程式數目 | Int |
  | archives | 要用於此工作階段的封存 | 字串清單 |
  | queue | 要提交的 YARN 佇列名稱| 字串 |
  | NAME | 此會話的名稱 | String |
  | conf | Spark 設定屬性 | key=val 的對應 |

  **回應主體** 建立的批次物件。

  | 名稱 | description | 類型 |
  | --- | ---| --- |
  | 識別碼 | 工作階段識別碼 | Int |
  | appId | 此會話的應用程式識別碼 | String |
  | appInfo | 詳細的應用程式資訊 | key=val 的對應 |
  | log | 記錄行 | 字串清單 |
  | state |批次狀態 | String |

  > [!NOTE]
  > 當您提交腳本時，[輸出] 窗格中會顯示指派的 Livy 設定。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>從總管整合 Azure HDInsight

您可以直接透過 **Azure HDInsight** explorer，在叢集中預覽 Hive 資料表：

1. [連線](#connect-to-an-azure-account)到 Azure 帳戶 (如果您尚未這樣做)。

2. 從最左邊的資料行中選取 **Azure** 圖示。

3. 從左窗格中，展開 [ **AZURE： HDINSIGHT**]。 系統會列出可用的訂用帳戶和叢集。

4. 展開叢集以查看 Hive 中繼資料資料庫和資料表架構。

5. 以滑鼠右鍵按一下 Hive 資料表。 例如： **hivesampletable**。 選取 [預覽]  。

   ![適用于 Visual Studio Code preview hive 資料表的 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. [ **預覽結果** ] 視窗隨即開啟：

   ![Visual Studio Code 預覽結果視窗的 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- 結果面板

   您可以將整個結果以 CSV、JSON 或 Excel 檔案的形式儲存到本機路徑，或只選取多行。

- 訊息面板

  1. 當資料表中的資料列數目大於100時，您會看到下列訊息：「Hive 資料表的前100個數據列會顯示」。
  2. 當資料表中的資料列數目小於或等於100時，您會看到下列訊息：「Hive 資料表顯示的60資料列」。
  3. 當資料表中沒有內容時，您會看到下列訊息：「」 `0 rows are displayed for Hive table.`

     >[!NOTE]
     >
     >在 Linux 中，安裝 >xclip 以啟用複製資料表資料。
     >
     >![Linux 中適用于 Visual Studio 程式碼的 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>其他功能

適用于 Visual Studio Code 的 Spark & Hive 也支援下列功能：

- **IntelliSense 自動完成**。 針對關鍵字、方法、變數和其他程式設計項目的建議。 不同圖示代表不同類型的物件：

    ![Spark & Hive Tools for Visual Studio Code IntelliSense 物件](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense 錯誤標記**。 語言服務會將 Hive 腳本中的編輯錯誤加上底線。     
- **語法醒目顯示**。 語言服務會使用不同的色彩來區分變數、關鍵字、資料類型、函式和其他程式設計項目：

    ![適用於 Visual Studio Code 的 Spark & Hive Tools 語法醒目提示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>僅限讀取者角色

針對叢集指派僅限讀取者角色的使用者，無法將作業提交至 HDInsight 叢集，也無法查看 Hive 資料庫。 請聯絡叢集系統管理員，將您的角色升級為 [Azure 入口網站](https://portal.azure.com/)中的 [**HDInsight 叢集操作員**](./hdinsight-migrate-granular-access-cluster-configurations.md#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。 如果您有有效的 Ambari 認證，您可以使用下列指引手動連結叢集。

### <a name="browse-the-hdinsight-cluster"></a>流覽 HDInsight 叢集  

當您選取 Azure HDInsight explorer 來展開 HDInsight 叢集時，如果您有叢集的僅限讀取者角色，系統就會提示您連結叢集。 使用下列方法，透過您的 Ambari 認證連結至叢集。

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>將作業提交至 HDInsight 叢集

將作業提交至 HDInsight 叢集時，如果您是在叢集的僅限讀取者角色中，系統會提示您連結叢集。 使用下列步驟，使用 Ambari 認證連結到叢集。

### <a name="link-to-the-cluster"></a>連結至叢集

1. 輸入有效的 Ambari 使用者名稱。
2. 請輸入有效的密碼。

   ![適用于 Visual Studio Code 使用者名稱的 Spark & Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![適用于 Visual Studio Code 密碼的 Spark & Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

   > [!NOTE]
   >
   >您可以使用 `Spark / Hive: List Cluster` 來檢查連結的叢集：
   >
   >![適用于 Visual Studio Code 讀者連結的 Spark & Hive 工具](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>流覽 Data Lake Storage Gen2 帳戶

選取 Azure HDInsight explorer 來展開 Data Lake Storage Gen2 帳戶。 如果您的 Azure 帳戶沒有 Gen2 儲存體的存取權，系統就會提示您輸入儲存體存取金鑰。 存取金鑰經過驗證之後，就會自動展開 Data Lake Storage Gen2 帳戶。

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>使用 Data Lake Storage Gen2 將作業提交至 HDInsight 叢集

使用 Data Lake Storage Gen2 將作業提交至 HDInsight 叢集。 如果您的 Azure 帳戶沒有 Gen2 儲存體的寫入權限，系統就會提示您輸入儲存體存取金鑰。 存取金鑰經過驗證之後，就會成功提交作業。

![適用于 Visual Studio Code AccessKey 的 Spark & Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> 您可以從 Azure 入口網站取得儲存體帳戶的存取金鑰。 如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)。

## <a name="unlink-cluster"></a>將叢集取消連結

1. 從功能表列，移至 [ **View**  >  **命令** 選擇區]，然後輸入 **Spark/Hive：取消連結** 叢集。  

2. 選取要取消連結的叢集。  

3. 請參閱 **輸出** 視圖以進行驗證。  

## <a name="sign-out"></a>登出  

從功能表列，移至 [ **View**]  >  **命令** 選擇區，然後輸入 **Azure：登出**。

## <a name="known-issues"></a>已知問題

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>此延伸模組不支援 2020.5.78807 版或更新版本的 ms-python 

「無法連線至 Jupyter Notebook。」 是 2020.5.78807 或更新 Python 版本的已知問題。 建議使用者使用 **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** 版的 ms-python，以避免發生此問題。

![已知問題](./media/hdinsight-for-vscode/known-issue.png)

## <a name="next-steps"></a>後續步驟

如需示範如何使用 Spark & Hive 進行 Visual Studio Code 的影片，請參閱 [適用于 Visual Studio Code 的 spark & hive](https://go.microsoft.com/fwlink/?linkid=858706)。