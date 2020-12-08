---
title: 教學課程 - Spark & Hive Tools for VSCode (Spark 應用程式)
description: 教學課程 - 使用 Spark & Hive Tools for VSCode 開發以 Python 撰寫的 Spark 應用程式，並將其提交至無伺服器 Apache Spark 集區。
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: eb4a4c2c8d1d52690a07b784640d20d96ff2d600
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445681"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>教學課程：使用 Synapse 工作區搭配 VSCode 來建立 Apache Spark 應用程式

了解如何使用 Apache Spark & Hive Tools for Visual Studio Code。 使用工具建立並提交適用於 Apache Spark 的 Apache Hive 批次作業、互動式 Hive 查詢和 PySpark 指令碼。 首先，我們將說明如何在 Visual Studio Code 中安裝 Spark & Hive Tools。 然後，我們將逐步解說如何將作業提交至 Spark & Hive Tools。

您可以在 Visual Studio Code 支援的平台上安裝 Spark & Hive Tools。 請留意下列適用於不同平台的必要條件。

## <a name="prerequisites"></a>必要條件

若要完成此文章中的步驟，將會需要下列項目：

- 無伺服器 Apache Spark 集區。 若要建立無伺服器 Apache Spark 集區，請參閱[使用 Azure 入口網站建立 Apache Spark 集區](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)。
- [Visual Studio Code](https://code.visualstudio.com/) \(英文\)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/) \(英文\)。 只有 Linux 和 macOS 才需要 Mono。
- [Visual Studio Code 的 PySpark 互動式環境](../../hdinsight/set-up-pyspark-interactive-environment.md)。
- 本機目錄。 本文使用 **C:\HD\Synaseexample**。

## <a name="install-spark--hive-tools"></a>安裝 Spark & Hive Tools

符合必要條件後，您即可依照下列步驟安裝 Spark & Hive Tools for Visual Studio Code：

1. 開啟 Visual Studio Code。

2. 從功能表列，瀏覽至 [檢視] > [擴充功能]。

3. 在搜尋方塊中，輸入 **Spark & Hive**。

4. 從搜尋結果中選取 [Spark & Hive Tools]，然後選取 [安裝]：

     ![Spark & Hive for Visual Studio Code Python 安裝](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. 視需要選取 [重新載入]。

## <a name="open-a-work-folder"></a>開啟工作資料夾

若要在 Visual Studio Code 中開啟工作資料夾並建立檔案，請執行下列步驟：

1. 從功能表列中，瀏覽至 [檔案] > [開啟資料夾...] > **C:\HD\Synaseexample**，然後選取 [選取資料夾] 按鈕。 該資料夾會出現在左側的 [檔案總管] 檢視中。

2. 在 [檔案總管] 檢視中選取 [Synaseexample] 資料夾，然後選取工作資料夾旁的 **新增檔案** 圖示：

     ![Visual Studio Code 的新增檔案圖示](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. 使用 `.py` (Spark 指令碼) 副檔名為新檔案命名。 此範例會使用 **HelloWorld.py**。

## <a name="connect-to-your-spark-pools"></a>連線到 Spark 集區

登入 Azure 訂用帳戶以連線到 Spark 集區。

### <a name="sign-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶：

依照下列步驟連線至 Azure：

1. 從功能表列中，瀏覽至 [檢視] >  [命令選擇區...]，然後輸入 **Azure:Sign In**：

     ![Spark & Hive Tools for Visual Studio Code 登入](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. 依照登入指示登入 Azure。 連線之後，您的 Azure 帳戶名稱會顯示在 Visual Studio Code 視窗底部的狀態列上。

## <a name="set-the-default-spark-pool"></a>設定預設 Spark 集區

1. 重新開啟 [先前](#open-a-work-folder)所討論的 **Synaseexample** 資料夾 (如果已關閉)。  

2. 選取 [先前](#open-a-work-folder)所建立的 **HelloWorld.py** 檔案。 檔案會在指令碼編輯器中開啟。

3. 以滑鼠右鍵按一下指令碼編輯器，然後選取 [Synapse:設定預設 Spark 集區]。  

4. [連線](#connect-to-your-spark-pools)到 Azure 帳戶 (如果您尚未這樣做)。

5. 選取 Spark 集區作為目前指令檔的預設 Spark 集區。 工具會自動更新 **.VSCode\settings.json** 組態檔：

     ![設定預設叢集設定](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>將互動式 Synapse PySpark 查詢提交至 Spark 集區

使用者可以透過下列方式，在 Spark 集區上執行 Synapse PySpark interactive：

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>在 PY 檔案中使用 Synapse PySpark interactive 命令
使用 PySpark interactive 命令提交查詢，並執行下列步驟：

1. 重新開啟 [先前](#open-a-work-folder)所討論的 **Synaseexample** 資料夾 (如果已關閉)。  

2. 依照 [先前的](#open-a-work-folder)步驟建立新檔案 **HelloWorld.py**。

3. 將下列程式碼複製並貼到該指令檔：

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. 視窗右下角會顯示安裝 PySpark/Synapse Pyspark 核心的提示。 您可以按一下 [安裝] 按鈕以繼續進行 PySpark/Synapse Pyspark 安裝，或按一下 [略過] 按鈕以略過此步驟。

     ![安裝 pyspark 核心](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. 如果後續需要安裝，您可以瀏覽至 [檔案] > [喜好設定] > [設定]，然後取消核取設定中的 [Hdinsight：啟用略過 Pyspark 安裝]。 
    
     ![啟用略過 pyspark 安裝](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. 如果步驟 4 中的安裝成功，則會在視窗右下角顯示 [已成功安裝 PySpark/Synapse Pyspark] 訊息方塊。 按一下 [重新載入] 按鈕以重新載入視窗。

     ![已成功安裝 pyspark](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. 從功能表列中瀏覽至 [檢視] > [命令選擇區...]，或使用 **Shift + Ctrl + P** 鍵盤快速鍵，然後輸入 **Python:Select Interpreter to start Jupyter Server**。

     ![選取解譯器以啟動 Jupyter 伺服器](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. 選取下列 Python 選項。

     ![選擇下列選項](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. 從功能表列中瀏覽至 [檢視] > [命令選擇區...]，或使用 **Shift + Ctrl + P** 鍵盤快速鍵，然後輸入 [Developer:重新載入視窗]。

     ![重新載入視窗](./media/vscode-tool-synapse/reload-window.png)

10. [連線](#connect-to-your-spark-pools)到 Azure 帳戶 (如果您尚未這樣做)。

11. 選取所有程式碼，以滑鼠右鍵按一下指令碼編輯器，選取 [Spark:Pyspark Interactive] 以提交查詢。 

     ![[PySpark 互動式] 內容功能表](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. 如果您尚未指定預設 Spark 集區，請選取 Spark 集區。 幾分鐘後，[Python 互動式] 結果便會出現在新的索引標籤中。按一下 PySpark 將核心切換至 **Synapse PySpark**，然後再次提交選取的程式碼，程式碼就會成功執行。 工具也可讓您使用內容功能表來提交程式碼區塊，而非整個指令檔：

     ![互動式](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>使用 #%% 註解在 PY 檔案中執行互動式查詢

1. 在程式碼前面加上 **#%%** 以取得筆記本體驗。

     ![加上 #%%](./media/vscode-tool-synapse/run-cell.png)

2. 按一下 [執行資料格]。 幾分鐘後，Python Interactive 結果就會出現在新的索引標籤中。按一下 PySpark 將核心切換至 **Synapse PySpark**，然後再按一下 [執行資料格]，程式碼就會成功執行。 

     ![執行資料格結果](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>運用 Python 延伸模組的 IPYNB 支援

1. 您可以從命令選擇區使用命令建立 Jupyter Notebook，或藉由在工作區中建立新的 .ipynb 檔案來建立。 如需詳細資訊，請參閱[在 Visual Studio Code 中使用 Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support)

2. 按一下 [執行資料格] 按鈕，依照提示 **設定預設 Spark 集區** (強烈建議在每次開啟筆記本之前都設定預設叢集/集區)，然後 **重新載入** 視窗。

     ![設定預設 Spark 集區並重新載入](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. 按一下 PySpark 將核心切換至 **Synapse PySpark**，然後按一下 [執行資料格]，不久後就會顯示結果。

     ![執行 ipynb 結果](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. 此延伸模組不支援 2020.5.78807 版或更新版本的 ms-python，這是[已知問題](#known-issues)。
>  
>2. 切換至 Synapse Pyspark 核心，建議在 Azure 入口網站中停用自動設定。 否則，喚醒叢集並設定 Synapse 核心以供首次使用，可能需要很長的時間。 
>
>    ![自動設定](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>將 PySpark 批次作業提交至 Spark 集區

1. 重新開啟您 [先前](#open-a-work-folder)討論的 **Synaseexample** 資料夾 (如果已關閉)。  

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

4. [連線](#connect-to-your-spark-pools)到 Azure 帳戶 (如果您尚未這樣做)。

5. 以滑鼠右鍵按一下指令碼編輯器，然後選取 [Synapse:PySpark 批次]。

6. 選取您的 PySpark 作業要提交到的 Spark 集區：

     ![提交 Python 作業結果輸出](./media/vscode-tool-synapse/submit-pythonjob-result.png)

將批次作業提交至 Spark 集區之後，提交記錄便會出現在 Visual Studio Code 的 [輸出] 視窗中。 此外也會顯示 **Spark UI** URL 和 **Spark 作業應用程式 UI** URL。 您可以在網頁瀏覽器中開啟該 URL 來追蹤作業狀態。

## <a name="access-and-manage-synapse-workspace"></a>存取和管理 Synapse 工作區

您可以在 Spark & Hive Tools for VSCode 內的 Azure Explorer 中執行不同的作業。 從 Azure Explorer。

![Azure 影像](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>啟動工作區

1. 從 Azure Explorer 瀏覽至 **SYNAPSE** 並加以展開，然後顯示 Synapse 訂用帳戶清單。

     ![Synapse 總管](./media/vscode-tool-synapse/synapse-explorer.png)

2. 按一下 Synapse 工作區的訂用帳戶並加以展開，然後顯示工作區清單。

3. 以滑鼠右鍵按一下工作區，然後選取 [檢視 Apache Spark 應用程式]，Synapse Studio 網站中的 [Apache Spark 應用程式] 頁面將會開啟。

     ![以滑鼠右鍵按一下工作區](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![Synapse Studio 應用程式](./media/vscode-tool-synapse/synapse-studio-application.png)

4. 展開工作區，會顯示 [預設儲存體] 和 [Spark 集區]。

5. 以滑鼠右鍵按一下 [預設儲存體]，會顯示 [複製完整路徑] 和 [在 Synapse Studio 中開啟]。 

     ![以滑鼠右鍵按一下預設儲存體](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - 按一下 [複製完整路徑]，將會複製主要 ADLS Gen2 帳戶 URL，您可以將其貼到您需要的位置。

     - 按一下 [在 Synapse Studio 中開啟]，將會在 Synapse Studio 中開啟主要儲存體帳戶。

     ![Synapse Studio 中的預設儲存體](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. 展開 [預設儲存體]，會顯示主要儲存體帳戶。

7. 展開 [Spark 集區]，會顯示工作區中的所有 Spark 集區。

     ![展開儲存體集區](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>已知問題

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>此延伸模組不支援 2020.5.78807 版或更新版本的 ms-python 

「無法連線至 Jupyter Notebook。」 是 2020.5.78807 或更新 Python 版本的已知問題。 建議使用者使用 **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** 版的 ms-python，以避免發生此問題。

![已知問題](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>後續步驟

- [Azure Synapse Analytics](../overview-what-is.md)
- [為 Azure Synapse Analytics 工作區建立新的 Apache Spark 集區](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
