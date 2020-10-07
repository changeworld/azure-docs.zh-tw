---
title: 教學課程 - Azure Toolkit for IntelliJ (Spark 應用程式)
description: 教學課程 - 使用 Azure Toolkit for IntelliJ 來開發以 Scala 撰寫的 Spark 應用程式，並將其提交至 Apache Spark 集區 (預覽)。
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: c17addc313954fbca5b81c4594d7317065350c09
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249564"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>教學課程：使用 Synapse 工作區搭配 IntelliJ 來建立 Apache Spark 應用程式

本教學課程說明如何使用 Azure Toolkit for IntelliJ 外掛程式來開發以 [Scala](https://www.scala-lang.org/) 撰寫的 Apache Spark 應用程式，然後直接從 IntelliJ 整合式開發環境 (IDE) 將其提交至 Spark 集區 (預覽)。 您可以利用數個方式來使用此外掛程式：

- 在 Spark 集區上開發並提交 Scala Spark 應用程式。
- 存取 Spark 集區資源。
- 在本機開發並執行 Scala Spark 應用程式。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
>
> - 使用 Azure Toolkit for IntelliJ 外掛程式
> - 開發 Apache Spark 應用程式
> - 將應用程式提交至 Spark 集區

## <a name="prerequisites"></a>必要條件

- [IntelliJ IDEA 社群版本](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC)。
- Azure 工具組外掛程式 3.27.0-2019.2 - 從 [IntelliJ 外掛程式存放庫](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)來安裝
- [JDK (1.8 版)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。
- Scala 外掛程式 - 從 [IntelliJ 外掛程式存放庫](/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea)來安裝。
- 下列必要條件僅適用於 Windows 使用者：

  在 Windows 電腦上執行本機 Spark Scala 應用程式時，可能會發生如 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的例外狀況。 發生這個例外狀況是因為 Windows 上遺失 WinUtils.exe。
  若要解決這個錯誤，請將 [WinUtils 可執行檔](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)下載至 **C:\WinUtils\bin** 之類的位置。 然後，新增環境變數 **HADOOP_HOME**，並將變數的值設為 **C:\WinUtils**。

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>建立適用於 Spark 集區的 Spark Scala 應用程式

1. 啟動 IntelliJ IDEA，然後選取 [建立新專案] 來開啟 [新增專案] 視窗。
2. 選取左窗格中的 [Apache Spark/HDInsight]。
3. 選取主視窗中的 [Spark 專案與範例 (Scala)]。
4. 從 [建置工具] 下拉式清單中，選取下列其中一種類型：

   - **Maven**：建立 Scala 專案精靈支援。
   - **SBT**：可供管理相依性並建置 Scala 專案。

    ![IntelliJ IDEA 的新增專案對話方塊](./media/intellij-tool-synapse/create-synapse-application01.png)

5. 選取 [下一步] 。
6. 在 [新增專案] 視窗中，提供下列資訊：

    | 屬性 | 描述 |
    | ----- | ----- |
    |專案名稱| 輸入名稱。 本教學課程使用 `myApp`。|
    |專案&nbsp;位置| 輸入所要的位置以儲存您的專案。|
    |專案 SDK| 您第一次使用 IDEA 時，這可能是空白的。 選取 [新增...] 並瀏覽至您的 JDK。|
    |Spark 版本|建立精靈會為 Spark SDK 和 Scala SDK 整合正確的版本。 Synapse 僅支援 **Spark 2.4.0**。|
    |||

    ![選取 Apache Spark SDK](./media/intellij-tool-synapse/create-synapse-application02.png)

7. 選取 [完成]。 可能需要幾分鐘的時間，專案才會變成可用。
8. Spark 專案會自動為您建立成品。 若要檢視該成品，請執行下列操作：

   a. 從功能表中，瀏覽至 [檔案] > [專案結構...]。

   b. 從 [專案結構] 視窗中，選取 [成品]。

   c. 檢視成品之後，請選取 [取消]。

    ![對話方塊中的成品資訊](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. 從 **myApp** > **src** > **main** > **scala**> **sample**> **LogQuery** 尋找 **LogQuery**。 本教學課程使用 **LogQuery** 來執行。

   ![從專案中建立 Scala 類別的命令](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>連線到 Spark 集區

登入 Azure 訂用帳戶以連線到 Spark 集區。

### <a name="sign-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶：

1. 從功能表列中，瀏覽至 [檢視] > [工具視窗] > [Azure Explorer]。

   ![IntelliJ IDEA 顯示 Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. 在 Azure Explorer 中，以滑鼠右鍵按一下 [Azure] 節點，然後選取 [登入]。

   ![IntelliJ IDEA 總管以滑鼠右鍵按一下 Azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. 在 [Azure 登入] 對話方塊中選擇 [裝置登入]，然後選取 [登入]。

    ![IntelliJ IDEA Azure 登入](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. 在 [Azure 裝置登入] 對話方塊中，選取 [複製並開啟]。

   ![IntelliJ IDEA Azure 裝置登入](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. 在瀏覽器介面中貼上程式碼，然後選取 [下一步]。

   ![適用於 HDI 的 Microsoft 輸入驗證碼對話方塊](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. 輸入您的 Azure 認證，然後關閉瀏覽器。

   ![Microsoft 輸入 HDI 的電子郵件對話方塊](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. 登入之後，[選取訂用帳戶] 對話方塊會列出與認證建立關聯的所有 Azure 訂用帳戶。 選取您的訂用帳戶，然後選取 [選取]。

    ![[選取訂用帳戶] 對話方塊](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. 從 **Azure Explorer** 中展開 [Synapse 上的 Apache Spark]，以檢視訂用帳戶中的工作區。

    ![IntelliJ IDEA Azure Explorer 主要檢視](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. 若要檢視 Spark 集區，您可以進一步展開工作區。

    ![Azure Explorer 儲存體帳戶](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>在 Spark 集區上遠端執行 Spark Scala 應用程式

在建立 Scala 應用程式之後，便可以從遠端加以執行。

1. 選取圖示以開啟 [執行/偵錯設定] 視窗。

    ![[將 Spark 應用程式提交給 HDInsight] 命令 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. 在 [執行/偵錯設定] 對話方塊視窗中，選取 [+]，然後選取 [Synapse 上的 Apache Spark]。

    ![[將 Spark 應用程式提交給 HDInsight] 命令 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. 在 [執行/偵錯設定] 視窗中提供下列值，然後選取 [確定]：

    |屬性 |值 |
    |----|----|
    |Spark 集區|選取您要在其上執行應用程式的 Spark 集區。|
    |選取要提交的成品|保留預設值。|
    |Main class name (主要類別名稱)|預設值是所選取檔案中的主要類別。 您可以選取省略號 ( **...** ) 並選擇另一個類別來變更類別。|
    |作業設定|您可以變更預設的金鑰和值。 如需詳細資訊，請參閱 [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html)。|
    |命令列引數|如有需要，您可以為主類別輸入以空格隔開的引數。|
    |參考的 Jar 和參考的檔案|如果有任何要參考的 Jar 或檔案，您可以輸入其路徑。 您也可以在 Azure 虛擬檔案系統中瀏覽檔案；此系統目前僅支援 ADLS Gen2 叢集。 其他資訊：[Apache Spark 設定](https://spark.apache.org/docs/latest/configuration.html#runtime-environment)和[如何將資源上傳至叢集](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。|
    |作業上傳儲存體|展開以顯示其他選項。|
    |儲存類型|從下拉式清單中選取 [使用 Azure Blob 上傳] 或 [使用叢集預設儲存體帳戶上傳]。|
    |儲存體帳戶|輸入儲存體帳戶。|
    |儲存體金鑰|輸入儲存體金鑰。|
    |儲存體容器|輸入**儲存體帳戶**和**儲存體金鑰**後，從下拉式清單中選取您的儲存體容器。|

    ![[提交 Spark] 對話方塊 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. 選取 [SparkJobRun] 圖示將您的專案提交至選取的 Spark 集區。 [Remote Spark Job in Cluster] \(叢集中的遠端 Spark 作業\) 索引標籤在底部顯示作業執行進度。 您可以選取紅色按鈕來停止應用程式。

    ![Apache Spark 提交視窗](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![[提交 Spark] 對話方塊 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>本機執行/偵錯 Apache Spark 應用程式

您可以遵循下列指示來為 Apache Spark 作業設定本機執行和本機偵錯。

### <a name="scenario-1-do-local-run"></a>案例 1：本機執行

1. 開啟 [執行/偵錯設定] 對話方塊，選取加號 ( **+** )。 然後選取 [Synapse 上的 Apache Spark] 選項。 輸入要儲存的 [名稱]、[主要類別名稱] 資訊。

    ![Intellij 執行偵錯設定本機執行 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - 環境變數和 WinUtils.exe 位置僅適用於 Windows 使用者。
    - 環境變數：如果您之前有設定系統環境變數，系統會自動偵測到，無須手動新增。
    - [WinUtils.exe 位置](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)：您可以選取右邊的資料夾圖示來指定 WinUtils 位置。

2. 然後選取 [本機播放] 按鈕。

    ![Intellij 執行偵錯設定本機執行 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. 本機執行完成後，如果指令碼包含輸出，則可以從 **data** > **__default__** 檢查輸出檔案。

    ![Intellij 專案本機執行結果 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>案例 2：執行本機偵錯

1. 開啟 **LogQuery** 指令碼，設定中斷點。
2. 選取 [本機偵錯] 圖示以進行本機偵錯。

    ![Intellij 專案本機執行結果 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>存取和管理 Synapse 工作區

您可以在 Azure Toolkit for IntelliJ 內的 Azure Explorer 中執行不同作業。 從功能表列中，瀏覽至 [檢視] > [工具視窗] > [Azure Explorer]。

### <a name="launch-workspace"></a>啟動工作區

1. 從 Azure Explorer 瀏覽至 [Synapse 上的 Apache Spark]，然後將其展開。

    ![IntelliJ IDEA Azure Explorer 主要檢視](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. 以滑鼠右鍵按一下工作區，然後選取 [啟動工作區]，網站便會開啟。

    ![Spark 作業檢視應用程式詳細資料 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Spark 作業檢視應用程式詳細資料 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark 主控台

您可以執行 Spark 本機主控台 (Scala) 或執行 Spark Livy 互動式工作階段主控台 (Scala)。

### <a name="spark-local-console-scala"></a>Spark 本機主控台 (Scala)

請確定您已滿足 WINUTILS.EXE 的先決條件。

1. 從功能表列，瀏覽至 [Run] \(執行\) > [Edit Configurations] \(編輯設定\)。
2. 從 [執行/偵錯設定] 視窗的左側窗格瀏覽至 [Synapse 上的 Apache Spark] > [[Synapse 上的 Spark] myApp]。
3. 在主視窗中，選取 [Locally Run] \(在本機執行\) 索引標籤。
4. 提供下列值，然後選取 [OK] \(確定\)：

    |屬性 |值 |
    |----|----|
    |環境變數|請確定 HADOOP_HOME 的值是正確的。|
    |WINUTILS.exe 位置|請確定路徑是正確的。|

    ![本機主控台設定組態](./media/intellij-tool-synapse/local-console-synapse01.png)

5. 在 [Project] \(專案\) 中，瀏覽至 [myApp] > [src] > [main] > [scala] > [myApp]。
6. 在功能表列中，瀏覽至 [工具] > [Spark 主控台] > [執行 Spark 本機主控台 (Scala)]。
7. 然後可能會出現兩個對話方塊，詢問您是否要自動修正相依性。 如果需要，請選取 [Auto Fix] \(自動修正\)。

    ![IntelliJ IDEA Spark 自動修正對話方塊1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark 自動修正對話方塊2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. 主控台看起來應該類似下面的圖片。 在主控台視窗中，輸入 `sc.appName`，然後按 Ctrl+Enter。 系統將會顯示結果。 您可以選取紅色按鈕來停止本機主控台。

    ![IntelliJ IDEA 本機主控台結果](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark Livy 互動式工作階段主控台 (Scala)

僅在 IntelliJ 2018.2 和 2018.3 上提供支援。

1. 從功能表列，瀏覽至 [Run] \(執行\) > [Edit Configurations] \(編輯設定\)。

2. 從 [執行/偵錯設定] 視窗的左側窗格瀏覽至 [Synapse 上的 Apache Spark] > [[Synapse 上的 Spark] myApp]。

3. 在主視窗中，選取 [Remotely Run in Cluster] \(在叢集中遠端執行\) 索引標籤。

4. 提供下列值，然後選取 [OK] \(確定\)：

    |屬性 |值 |
    |----|----|
    |Main class name (主要類別名稱)| 選取主要類別名稱。| 
    |Spark 集區|選取您要在其上執行應用程式的 Spark 集區。|
    ||

    ![互動式主控台設定組態](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. 在 [Project] \(專案\) 中，瀏覽至 [myApp] > [src] > [main] > [scala] > [myApp]。

6. 在功能表列中，瀏覽至 [工具] > [Spark 主控台] > [執行 Spark Livy 互動式工作階段主控台 (Scala)]。
7. 主控台看起來應該類似下面的圖片。 在主控台視窗中，輸入 `sc.appName`，然後按 Ctrl+Enter。 系統將會顯示結果。 您可以選取紅色按鈕來停止本機主控台。

    ![IntelliJ IDEA 互動式主控台結果](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>傳送所選項目至 Spark 主控台

您可能想要將一些程式碼傳送至本機主控台或 Livy 互動式工作階段主控台 (Scala) 來查看指令碼結果。 若要這樣做，您可以將 Scala 檔案中的某些程式碼反白顯示，然後以滑鼠右鍵按一下 [傳送所選項目至 Spark 主控台]。 所選的程式碼將會傳送至主控台並執行。 結果會在主控台中顯示於程式碼之後。 主控台會檢查現有錯誤。

   ![傳送所選項目至 Spark 主控台](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>後續步驟

- [Azure Synapse Analytics](../overview-what-is.md)
- [為 Azure Synapse Analytics 工作區建立新的 Apache Spark 集區](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
