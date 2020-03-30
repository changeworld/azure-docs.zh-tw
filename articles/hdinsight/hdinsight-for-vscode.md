---
title: Azure HDInsight 視覺化工作室代碼
description: 瞭解如何使用 Spark & Hive 工具 （Azure HDInsight） 用於視覺化工作室代碼以創建和提交查詢和腳本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435675"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>使用火花&蜂巢工具視覺化工作室代碼

瞭解如何使用 Spark &視覺化工作室代碼的 Hive 工具為 Apache Spark 創建和提交 Apache Hive 批次處理作業、互動式 Hive 查詢和 PySpark 腳本。 首先，我們將介紹如何在視覺化工作室代碼中安裝 Spark & Hive 工具，然後介紹如何向 Spark & Hive Tools 提交作業。  

您可以在 Visual Studio Code 所支援的平台上安裝 Spark & Hive Tools，這包括 Windows、Linux 及 macOS。 請注意不同平臺的以下先決條件。

## <a name="prerequisites"></a>Prerequisites

若要完成此文章中的步驟，將會需要下列項目：

- Azure HDInsight 叢集。 若要建立叢集，請參閱[開始使用 HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)。 或者使用支援 Apache Livy 終結點的 Spark 和 Hive 群集。
- [視覺工作室代碼](https://code.visualstudio.com/)。
- [單聲道](https://www.mono-project.com/docs/getting-started/install/)。 單聲道僅適用于 Linux 和 macOS。
- [用於視覺工作室代碼的 PySpark 互動環境](set-up-pyspark-interactive-environment.md)。
- 本地目錄。 此文章使用 **C:\HD\HDexample**。

## <a name="install-spark--hive-tools"></a>安裝 Spark & Hive Tools

滿足先決條件後，您可以按照以下步驟安裝 Spark &視覺化工作室代碼蜂巢工具：

1. 開啟 Visual Studio Code。

2. 從功能表列導航到 **"查看** > **擴展**"。

3. 在搜尋方塊中，輸入 **Spark & Hive**。

4. 從搜尋結果中選擇 **"火花&蜂巢工具**，然後選擇 **"安裝**：

   ![火花&蜂巢用於視覺工作室代碼 Python 安裝](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 必要時選擇 **"重新載入**"。

## <a name="open-a-work-folder"></a>打開工作資料夾

要打開工作資料夾並在 Visual Studio 代碼中創建檔，請按照以下步驟操作：

1. 從功能表列導航到 **"檔** > **打開資料夾..."...** >  **C：\HD_HDexample，** 然後選擇 **"選擇資料夾**"按鈕。 該資料夾會出現在左側的 [檔案總管]**** 檢視中。

2. 在**資源管理器**視圖中，選擇 **"HDexample"** 資料夾，然後選擇工作資料夾旁邊的 **"新建檔**"圖示：

   ![視覺化工作室代碼新檔圖示](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. 使用`.hql`（Hive 查詢） 或`.py`（Spark 腳本） 檔副檔名新檔。 這個範例使用 **HelloWorld.hql**。

## <a name="set-the-azure-environment"></a>設定 Azure 環境

對於國家雲使用者，請按照以下步驟首先設置 Azure 環境，然後使用**Azure：登錄**命令登錄到 Azure：

1. 導航到**檔** > **首選項** > **設置**。
2. 搜索以下字串 **：Azure：雲**。
3. 從清單中選擇國家雲：

   ![設定預設的登入項目組態](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>連接到 Azure 帳戶

在可以從 Visual Studio Code 向群集提交腳本之前，必須連接到 Azure 帳戶或連結群集（使用 Apache Ambari 使用者名和密碼憑據或加入域的帳戶）。 按照以下步驟連接到 Azure：

1. 從功能表列中導航到 **"查看** > **命令調色板..."，** 然後輸入**Azure：登錄**：

    ![火花&蜂巢工具視覺化工作室代碼登錄](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 按照登錄說明登錄到 Azure。 連接後，Azure 帳戶名稱將顯示在視覺化工作室代碼視窗底部的狀態列上。  

## <a name="link-a-cluster"></a>連結叢集

### <a name="link-azure-hdinsight"></a>連結： Azure HDInsight

您可以使用[Apache Ambari](https://ambari.apache.org/)管理的使用者名連結普通群集，也可以使用域使用者名（如： `user1@contoso.com`） 連結企業安全包安全 Hadoop 群集。

1. 從功能表列中導航到 **"查看** > **命令調色板..."，** 然後輸入**Spark/Hive：連結群集**。

   ![命令調色板連結群集命令](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 選取連結的叢集類型 **Azure HDInsight**。

3. 輸入 HDInsight 群集 URL。

4. 輸入您的安巴里使用者名;預設值為**管理員**。

5. 輸入您的安巴里密碼。

6. 選擇群集類型。

7. 設置群集的顯示名稱（可選）。

8. 檢閱 [輸出]**** 檢視以確認。

   > [!NOTE]  
   > 如果群集登錄到 Azure 訂閱並連結群集，則使用連結的使用者名和密碼。  

### <a name="link-generic-livy-endpoint"></a>連結：通用利維終結點

1. 從功能表列中導航到 **"查看** > **命令調色板..."，** 然後輸入**Spark/Hive：連結群集**。

2. 選取連結的叢集類型 [泛型 Livy 端點]****。

3. 輸入通用的 Livy 終結點。 例如：HTTP\:/10.172.41.42：18080。

4. 選取授權類型 [基本]**** 或 [無]****。  如果選擇 **"基本**" ：  
    &emsp;a. 輸入您的安巴里使用者名;預設值為**管理員**。  
    &emsp;b. 輸入您的安巴里密碼。

5. 檢閱 [輸出]**** 檢視以確認。

## <a name="list-clusters"></a>列出叢集

1. 從功能表列導航到 **"查看** > **命令調色板..."，** 然後輸入**Spark/Hive：清單群集**。

2. 選擇所需的訂閱。

3. 檢閱 [輸出]**** 檢視。 此視圖顯示連結群集（或群集）和 Azure 訂閱下的所有群集：

    ![設定預設叢集設定](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>設置預設群集

1. 重新打開[前面](#open-a-work-folder)討論的**HDexample**資料夾（如果已關閉）。  

2. 選擇[之前](#open-a-work-folder)創建的**HelloWorld.hql**檔。 它在腳本編輯器中打開。

3. 按右鍵腳本編輯器，然後選擇**Spark/ Hive：設置預設群集**。  

4. [連接到](#connect-to-an-azure-account)Azure 帳戶，或連結群集（如果尚未連接）。

5. 選取某個叢集作為目前指令檔的預設叢集。 這些工具會自動更新 **。VSCode_settings.json**設定檔：

   ![設定預設叢集設定](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>提交互動式 Hive 查詢和 Hive 批次處理腳本

借助 Spark &視覺化工作室代碼的 Hive 工具，您可以將互動式 Hive 查詢和 Hive 批次處理腳本提交到群集。

1. 重新打開[前面](#open-a-work-folder)討論的**HDexample**資料夾（如果已關閉）。  

2. 選擇[之前](#open-a-work-folder)創建的**HelloWorld.hql**檔。 它在腳本編輯器中打開。

3. 複製以下代碼並將其粘貼到 Hive 檔中，然後將其保存：

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [連接到](#connect-to-an-azure-account)Azure 帳戶，或連結群集（如果尚未連接）。

5. 按右鍵腳本編輯器並選擇**Hive：互動式**以提交查詢，或使用 Ctrl_Alt_I 鍵盤快速鍵。  選擇**Hive：批次處理**以提交腳本，或使用 Ctrl_Alt_H 鍵盤快速鍵。  

6. 如果尚未指定預設群集，請選擇群集。 這些工具還允許您使用內容功能表提交代碼塊而不是整個指令檔。 幾分鐘後，查詢結果將顯示在新選項卡中：

   ![互動式阿帕奇蜂巢查詢結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **結果**面板：您可以將整個結果作為 CSV、JSON 或 Excel 檔保存到本地路徑或僅選擇多行。

    - **消息**面板：當您選擇**行**號時，它將跳轉到正在運行的腳本的第一行。

## <a name="submit-interactive-pyspark-queries"></a>提交互動式 PySpark 查詢

要提交互動式 PySpark 查詢，請按照以下步驟操作：

1. 重新打開[前面](#open-a-work-folder)討論的**HDexample**資料夾（如果已關閉）。  

2. 按照[前面的](#open-a-work-folder)步驟創建新**HelloWorld.py**檔。

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

4. [連接到](#connect-to-an-azure-account)Azure 帳戶，或連結群集（如果尚未連接）。

5. 選擇所有代碼，按右鍵腳本編輯器，然後選擇**Spark：PySpark 互動**以提交查詢。 或者，使用 Ctrl_Alt_I 快捷方式。

   ![[PySpark 互動式] 內容功能表](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 如果未指定預設群集，請選擇群集。 幾分鐘後 **，Python 互動式**結果將顯示在新選項卡中。這些工具還允許您使用內容功能表提交代碼塊而不是整個指令檔：

   ![PySpark 互動式 Python 互動式視窗](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. 輸入 **%%資訊**，然後按 Shift_Enter 以查看作業資訊（可選）：

   ![pyspark 互動式視圖作業資訊](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. 該工具還支援**Spark SQL**查詢：

   ![派斯派克互動式視圖結果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   執行查詢時，提交狀態將顯示在下部狀態列的左側。 當狀態為 [PySpark 核心 (忙碌)]**** 時，請勿提交其他查詢。  

   > [!NOTE]
   >
   > 在設置中清除啟用的**Python 擴展**（預設情況下已選中），提交的 pyspark 交互結果將使用舊視窗：
   >
   > ![PySpark 互動式 Python 擴充功能已停用](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批次工作

1. 重新打開[您前面](#open-a-work-folder)討論的**HDexample**資料夾（如果已關閉）。  

2. 按照[前面的](#open-a-work-folder)步驟創建新**BatchFile.py**檔。

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

4. [連接到](#connect-to-an-azure-account)Azure 帳戶，或連結群集（如果尚未連接）。

5. 按右鍵腳本編輯器，然後選擇**Spark：PySpark 批次處理**，或使用 Ctrl_Alt_H 鍵盤快速鍵。

6. 選擇要將 PySpark 作業提交到以下的群集：

   ![提交 Python 作業結果輸出](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

在您提交 Python 作業之後，提交記錄便會出現在 Visual Studio Code 的 [輸出]**** 視窗中。 還顯示了 Spark UI URL 和 Yarn UI URL。 您可以在網頁瀏覽器中開啟該 URL 來追蹤作業狀態。

## <a name="apache-livy-configuration"></a>Apache Livy 設定

支援[Apache Livy](https://livy.incubator.apache.org/)配置。 可以在 中配置它 **。工作區資料夾中的 VSCode_settings.json**檔。 目前 Livy 設定僅支援 Python 指令碼。 有關詳細資訊，請參閱[利維·裡德梅](https://github.com/cloudera/livy/blob/master/README.rst )。

<a id="triggerlivyconf"></a>**如何觸發利維配置**

方法 1  
1. 從功能表列導航到 **"檔** > **首選項** > **設置**"。
2. 在 **"搜索設置"** 框中，輸入**HDInsight 作業提交：Livy Conf**。  
3. 選取 [在 settings.json 中編輯]**** 以取得相關的搜尋結果。

方法 2 提交檔，並注意到 .vscode 資料夾會自動添加到工作資料夾中。 您可以通過選擇 **.vscode_settings.json**來查看利維配置。

+ 專案設定：

    ![HDInsight 阿帕奇利維配置](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >對於**驅動程式記憶體**和執行**器記憶體**設置，設置值和單位。 例如：1g 或 1024m。

+ 支援的利維配置：

    **POST /批量**請求正文

    | NAME | description | type |
    | :- | :- | :- |
    | 檔案 | 包含要執行之應用程式的檔案 | 路徑（必填） |
    | proxyUser | 執行作業時要模擬的使用者 | String |
    | className | 應用程式 Java/Spark 主要類別 | String |
    | args | 應用程式的命令列參數 | 字串清單 |
    | jars | 要在此會話中使用的 Jar | 字串清單 | 
    | pyFiles | 要用於此工作階段的 Python 檔案 | 字串清單 |
    | files | 在此會話中使用的檔 | 字串清單 |
    | driverMemory | 要用於驅動程式程序的記憶體數量 | String |
    | driverCores | 要用於驅動程式程序的核心數目 | Int |
    | executorMemory | 要用於每個執行程式程序的記憶體數量 | String |
    | executorCores | 要用於每個執行程式的核心數目 | Int |
    | numExecutors | 要針對此工作階段啟動的執行程式數目 | Int |
    | archives | 要用於此工作階段的封存 | 字串清單 |
    | queue | 要提交到的 YARN 佇列的名稱| String |
    | NAME | 此會話的名稱 | String |
    | conf | Spark 設定屬性 | key=val 的對應 |

    回應正文 已創建的批次處理物件。

    | NAME | description | type |
    | :- | :- | :- |
    | id | 工作階段識別碼 | Int |
    | appId | 此會話的應用程式 ID | String |
    | appInfo | 詳細的應用程式資訊 | key=val 的對應 |
    | log | 日誌行 | 字串清單 |
    | state |批次處理狀態 | String |

    > [!NOTE]
    > 提交腳本時，分配的 Livy 配置將顯示在輸出窗格中。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>從總管整合 Azure HDInsight

您可以通過**Azure HDInsight**資源管理器直接預覽群集中的 Hive 表：

1. [Connect](#connect-to-an-azure-account)如果尚未連接到 Azure 帳戶。

2. 從最左側列中選擇**Azure**圖示。

3. 從左側窗格中展開**AZURE：HDINSIGHT**。 列出了可用的訂閱和群集。

4. 展開群集以查看 Hive 中繼資料資料庫和表架構。

5. 按右鍵 Hive 表。 例如：**蜂巢表**。 選取 [預覽]****。

   ![用於視覺化工作室代碼預覽配置單元表的火花 &蜂巢](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. "**預覽結果**"視窗將打開：

   ![火花&蜂巢視覺化工作室代碼預覽結果視窗](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- 結果面板

   您可以將整個結果保存為 CSV、JSON 或 Excel 檔到本地路徑，或者僅選擇多行。

- 消息面板
   1. 當表中的行數大於 100 時，您將看到以下消息："前 100 行將顯示為 Hive 表。
   2. 當表中的行數小於或等於 100 時，您將看到一條消息："為 Hive 表顯示 60 行。
   3. 當表中沒有內容時，您將看到以下消息："為 Hive 表顯示 0 行。

        >[!NOTE]
        >
        >在 Linux 中，安裝 xclip 以啟用複製表資料。
        >
        >![Linux 中視覺工作室代碼的火花&蜂巢](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>其他功能

用於視覺化工作室代碼的火花&蜂巢還支援以下功能：

- **IntelliSense 自動完成**。 關鍵字、方法、變數和其他程式設計元素會彈出建議。 不同圖示代表不同類型的物件：

    ![用於視覺工作室代碼智慧感知物件的火花&蜂巢工具](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense 錯誤標記**。 語言服務在 Hive 腳本中突出顯示編輯錯誤。     
- **語法突出顯示**。 語言服務使用不同的顏色來區分變數、關鍵字、資料類型、函數和其他程式設計元素：

    ![適用於 Visual Studio Code 的 Spark & Hive Tools 語法醒目提示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>僅限讀取者角色

為群集分配僅讀者角色的使用者無法再將作業提交到 HDInsight 群集，也無法查看 Hive 資料庫。 請與群集管理員聯繫，將角色升級到[Azure 門戶](https://ms.portal.azure.com/)中的[**HDInsight 群集操作員**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。 如果您有有效的 Ambari 憑據，則可以使用以下指南手動連結群集。

### <a name="browse-the-hdinsight-cluster"></a>流覽 HDInsight 群集  

當您選擇 Azure HDInsight 資源管理器來擴展 HDInsight 群集時，如果群集具有僅讀者角色，系統將提示您連結群集。 使用以下方法使用 Ambari 憑據連結到群集。

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>將作業提交到 HDInsight 群集

將作業提交到 HDInsight 群集時，如果群集的僅讀者角色，系統將提示您連結群集。 使用以下步驟使用 Ambari 憑據連結到群集。

### <a name="link-to-the-cluster"></a>連結到群集

1. 輸入有效的 Ambari 使用者名。
2. 輸入有效的密碼。

   ![火花&蜂巢工具，用於視覺化工作室代碼使用者名](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![用於視覺化工作室代碼密碼的火花&蜂巢工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >可以使用`Spark / Hive: List Cluster`檢查連結的群集：
  >
  >![用於視覺化工作室代碼讀取器連結的火花&蜂巢工具](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>流覽資料存儲庫第 2 代帳戶

當您選擇 Azure HDInsight 資源管理器來擴展資料湖存儲 Gen2 帳戶時，如果 Azure 帳戶無法訪問 Gen2 存儲，系統將提示您輸入存儲訪問金鑰。 驗證訪問金鑰後，資料湖存儲 Gen2 帳戶將自動擴展。

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>將作業提交到具有資料湖存儲第 2 代的 HDInsight 群集

使用 Data Lake 存儲 Gen2 將作業提交到 HDInsight 群集時，如果 Azure 帳戶對 Gen2 存儲沒有寫入存取權限，系統將提示您輸入存儲訪問金鑰。 驗證訪問金鑰後，將成功提交作業。

![用於視覺化工作室代碼訪問金鑰的火花&蜂巢工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> 可以從 Azure 門戶獲取存儲帳戶的訪問金鑰。 如需詳細資訊，請參閱[管理儲存體帳戶存取金鑰](../storage/common/storage-account-keys-manage.md)。

## <a name="unlink-cluster"></a>將叢集取消連結

1. 從功能表列轉到 **"查看** > **命令調色板**"，然後輸入**Spark/ Hive：取消連結群集**。  

2. 選擇要取消連結的群集。  

3. 有關驗證，請參閱**輸出**視圖。  

## <a name="sign-out"></a>登出  

從功能表列轉到 **"查看** > **命令調色板**"，然後輸入**Azure：登出**。

## <a name="next-steps"></a>後續步驟

有關演示使用 Spark & Hive 用於視覺工作室代碼的視頻，請參閱[Visual Studio 代碼的 Spark & Hive。](https://go.microsoft.com/fwlink/?linkid=858706)
