---
title: 用於 Eclipse 的 Azure 工具組：為 HDInsight Spark 創建 Scala 應用
description: 使用 HDInsight 工具 (位於 Eclipse 的 Azure 工具組中) 來開發以 Scala 撰寫的 Spark 應用程式，並直接從 Eclipse IDE 將它們提交到 HDInsight Spark 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: 07ed22879180d8126711eba9af0a2cac5b7b2953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272106"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>使用 Azure Toolkit for Eclipse 為 HDInsight 叢集建立 Apache Spark 應用程式

使用 Azure Toolkit for [Eclipse](https://www.eclipse.org/) 中的 HDInsight 工具來開發以 [Scala](https://www.scala-lang.org/) 撰寫的 [Apache Spark](https://spark.apache.org/) 應用程式，並直接從 Eclipse IDE 將它們提交到 Azure HDInsight Spark 叢集。 您能以數種不同的方式使用 HDInsight 工具外掛程式：

* 在 HDInsight Spark 叢集上開發並提交 Scala Spark 應用程式。
* 存取您的 Azure HDInsight Spark 叢集資源。
* 在本機開發並執行 Scala Spark 應用程式。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

* [JAVA 開發人員工具組 （JDK） 版本 8](https://aka.ms/azure-jdks).

* [Eclipse IDE](https://www.eclipse.org/downloads/). 本文為 JAVA 開發人員使用 Eclipse IDE。

## <a name="install-required-plug-ins"></a>安裝所需的外掛程式

### <a name="install-azure-toolkit-for-eclipse"></a>安裝適用於 Eclipse 的 Azure 工具組

如需安裝指示，請參閱[安裝適用於 Eclipse 的 Azure 工具組](https://docs.microsoft.com/azure/java/eclipse/azure-toolkit-for-eclipse-installation)。

### <a name="install-the-scala-plug-in"></a>安裝 Scala 外掛程式

當您開啟 Eclipse 時，HDInsight 工具會自動偵測您是否已安裝 Scala 外掛程式。 選取 [確定]**** 以繼續，然後遵循指示從 Eclipse Marketplace 安裝外掛程式。 安裝完成後重新開機 IDE。

![自動安裝 Scala 外掛程式](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

### <a name="confirm-plug-ins"></a>確認外掛程式

1. 導航到**説明** > **Eclipse市場...**

1. 選取 [已安裝]**** 索引標籤。

1. 您至少應該看到：
    * 用於Eclipse\<版本的 Azure 工具組>。
    * scala \<IDE 版本>。

## <a name="sign-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶：

1. 開始Eclipse IDE。

1. 導航到**視窗** >  **顯示視圖** > **其他...** > **登錄.**. . .

1. 在 **"顯示視圖"** 對話方塊中，導航到**Azure** > **資源管理器**，然後選擇 **"打開**"。

   ![阿帕奇火花Eclipse顯示視圖](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. 從**Azure 資源管理器**中，按右鍵**Azure**節點，然後選擇 **"登錄**"。

1. 在**Azure 登錄對話方塊中**，選擇身份驗證方法，選擇 **"登錄**"並完成登錄過程。

   ![阿帕奇火花Eclipse Azure標誌](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. 登錄後，"**訂閱"** 對話方塊將列出與憑據關聯的所有 Azure 訂閱。 按 **"選擇"** 關閉對話方塊。

   ![[選取訂用帳戶] 對話方塊](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. 從**Azure 資源管理器**，導航到**Azure** >  **HDInsight**以查看訂閱下的 HDInsight Spark 群集。

   ![Azure 資源管理器中的 HDInsight 火花群集3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. 您可以進一步展開叢集名稱節點，查看與叢集相關聯的資源 (例如，儲存體帳戶)。

   ![展開叢集名稱以查看資源](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>連結叢集

您可以使用 Ambari 受控使用者名稱來連結一般的叢集。 同樣地，對於已加入網域的 HDInsight 叢集，您可以使用網域和使用者名稱進行連結，例如 `user1@contoso.com`。

1. 從**Azure 資源管理器**中，按右鍵**HDInsight**，然後選擇**連結群集**。

   ![Azure 資源管理器連結群集功能表](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. 輸入**群集名稱**、**使用者名**和**密碼**，然後選擇 **"確定**"。 您也可以選擇輸入 [儲存體帳戶]、[儲存體金鑰]，然後選取 [儲存體容器]，讓儲存體總管在左側樹狀檢視中工作

   ![連結新 HDInsight 群集對話方塊](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > 如果叢集已登入 Azure 訂用帳戶並連結叢集，我們會使用連結的儲存體金鑰、使用者名稱和密碼。
   > ![Azure Explorer 儲存體帳戶](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > 對於僅鍵盤使用者，當當前焦點位於**存儲鍵**時，您需要使用**Ctrl_TAB**來關注對話方塊中的下一個欄位。

1. 您可以在**HDInsight**下看到連結的群集。 您現在可以將應用程式提交至此連結的叢集。

   ![Azure 資源管理器 hdi 連結群集](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. 您也可以從 [Azure 總管]**** 取消連結叢集。

   ![Azure Explorer 取消連結的叢集](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>設定 HDInsight Spark 叢集的 Spark Scala 專案

1. 從 Eclipse IDE 工作區中，選擇 **"檔** > **新專案** > **..."。**

1. 在 **"新專案**"嚮導中，選擇**HDInsight 專案** > **Spark 上的 HDInsight （Scala）**。 然後選擇 **"下一步**"。

   ![選取 Spark HDInsight (Scala) 專案](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. 在 [新增 HDInsight Scala 專案]**** 對話方塊中，提供下列值，然後選取 [下一步]****：
   * 輸入專案的名稱。
   * 在 [JRE]**** 區域中，請確定已將 [使用執行環境 JRE]**** 設為 [JavaSE-1.7]**** 或更新版本。
   * 在 [Spark 程式庫]**** 區域中，您可以選擇 [使用 Maven 設定 Spark SDK ]**** 選項。  我們的工具為 Spark SDK 和 Scala SDK 整合正確的版本。 您還可以手動選擇 **"添加 Spark SDK"** 選項、下載和添加 Spark SDK。

   ![[新增 HDInsight Scala 專案] 對話方塊](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. 在下一個對話方塊中，查看詳細資訊，然後選擇 **"完成**"。

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>建立 HDInsight Spark 叢集的 Scala 應用程式

1. 從**包資源管理器**，展開您之前創建的專案。 按右鍵**src**，選擇 **"新** > **其他..."**

1. 在 **"選擇嚮導"對話方塊中**，選擇**Scala 嚮導** > **Scala 物件**。 然後選擇 **"下一步**"。

   ![選擇嚮導 創建 Scala 物件](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)

1. 在 [建立新檔案]**** 對話方塊中輸入物件的名稱，然後選取 [完成]****。 將打開文字編輯器。

   ![新檔嚮導創建新檔](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)

1. 在文字編輯器中，將當前內容替換為以下代碼：

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. 在 HDInsight Spark 叢集上執行應用程式：

   a. 從 [封裝總管] 中，以滑鼠右鍵按一下專案名稱，然後選取 [將 Spark 應用程式提交給 HDInsight]****。

   b. 在 **"Spark 提交"** 對話方塊中，提供以下值，然後選擇"**提交**：

   * 針對 **叢集名稱**，選取您要在其上執行應用程式的 HDInsight Spark 叢集。
   * 從 Eclipse 專案中選取構件，或從硬碟中選取一個。 預設值取決於您在套件總管中以滑鼠右鍵按一下的項目。
   * 在 [主要類別名稱]**** 下拉式清單中，提交精靈會顯示您的所有物件名稱。 選取或輸入您需要執行的物件名稱。 如果您從硬碟選取構件，就必須手動輸入主要的類別名稱。 
   * 由於此示例中的應用程式代碼不需要任何命令列參數或引用 JAR 或檔，因此可以將剩餘的文字方塊留空。

     ![阿帕奇火花提交對話方塊](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. [Spark Submission (提交 Spark)] **** 索引標籤應會開始顯示進度。 您可以選取 [提交 Spark]**** 視窗中的紅色按鈕，就能將應用程式停止。 您也可以選取全球圖示 (以映像中的藍色方塊表示)，檢視此特定應用程式執行的記錄。

   ![Apache Spark 提交視窗](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>使用適用於 Eclipse 的 Azure 工具組中的 HDInsight 工具來存取和管理 HDInsight Spark 叢集

您可以使用 HDInsight 工具來執行各種作業，包括存取作業輸出。

### <a name="access-the-job-view"></a>存取作業檢視

1. 在**Azure 資源管理器**中，展開**HDInsight**，然後展開 Spark 群集名稱，然後選擇**作業**。

   ![Azure 資源管理器 Eclipse 工作檢視節點](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. 選取 [作業]**** 節點。 如果 Java 版本低於 **1.8**，HDInsight 工具會自動提醒您安裝 **E(fx)clipse** 外掛程式。 選取 [確定]**** 繼續作業，然後遵循精靈安裝 Eclipse Marketplace 並重新啟動 Eclipse。

   ![安裝缺少的外掛程式 E（fx）剪輯](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. 從 [作業]**** 節點開啟 [作業] 檢視。 在右窗格中，[Spark 作業檢視] **** 索引標籤會顯示已在叢集上執行的所有應用程式。 選取您想要查看更多詳細資料的應用程式名稱。

   ![阿帕奇 Eclipse 視圖作業日誌詳細資訊](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   然後您可以採取下列任何動作：

   * 將滑鼠停留於作業圖表。 它會顯示關於執行中作業的基本資訊。 選取作業圖表，就可以看到每項作業產生的階段和資訊。

     ![阿帕奇火花作業圖階段資訊](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * 選取 [記錄]**** 索引標籤可檢視經常使用的記錄 (包括「驅動程式 Stderr」****、「驅動程式 Stdout」**** 和「目錄資訊」****)。

     ![阿帕奇火花Eclipse作業日誌資訊](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * 選取視窗頂端的超連結，在應用程式層級開啟 Spark 歷程記錄 UI 和 Apache Hadoop YARN UI。

### <a name="access-the-storage-container-for-the-cluster"></a>存取叢集的儲存體容器

1. 在 [Azure Explorer] 中展開 [HDInsight]**** 根節點，查看可用的 HDInsight Spark 叢集清單。

1. 展開叢集名稱以查看叢集的儲存體帳戶和預設儲存體容器。

   ![儲存體帳戶和預設儲存體容器](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. 選取與叢集相關聯的儲存體容器名稱。 在右窗格中，按兩下 **HVACOut** 資料夾。 開啟其中一個 **part-** 檔案，可查看應用程式的輸出。

### <a name="access-the-spark-history-server"></a>存取 Spark 歷程記錄伺服器

1. 在 [Azure Explorer] 中，以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟 Spark 歷程記錄 UI]****。 出現提示時，輸入叢集的系統管理員認證。 在佈建叢集時，您已指定這些項目。

1. 在 [Spark 歷程記錄伺服器] 儀表板中，您可以使用應用程式名稱，尋找您剛完成執行的應用程式。 在上述程式碼中，您可以使用 `val conf = new SparkConf().setAppName("MyClusterApp")`來設定應用程式名稱。 因此，Spark 應用程式名稱為 **MyClusterApp**。

### <a name="start-the-apache-ambari-portal"></a>啟動 Apache Ambari 入口網站

1. 在 [Azure Explorer] 中，以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟叢集管理入口網站 (Ambari)]****。

1. 出現提示時，輸入叢集的系統管理員認證。 在佈建叢集時，您已指定這些項目。

### <a name="manage-azure-subscriptions"></a>管理 Azure 訂用帳戶

根據預設，Azure 工具組中適用於 Eclipse 的 HDInsight 工具會列出您所有 Azure 訂用帳戶中的 Spark 叢集。 如有必要，您可以指定要存取其叢集的訂用帳戶。

1. 在 [Azure Explorer] 中，以滑鼠右鍵按一下 [Azure]**** 根節點，然後選取 [管理訂用帳戶]****。

1. 在對話方塊中，清除您不需要存取的訂用帳戶核取方塊，然後選取 [關閉]****。 如果您想要登出 Azure 訂用帳戶，也可以選取 [登出]****。

## <a name="run-a-spark-scala-application-locally"></a>在本機執行 Spark Scala 應用程式

您可以使用適用於 Eclipse 的 Azure 工具組中之 HDInsight 工具，在工作站上本機執行 Spark Scala 應用程式。 一般而言，這些應用程式不需要存取叢集資源 (例如儲存體容器)，而且您可在本機上執行並測試。

### <a name="prerequisite"></a>必要條件

在 Windows 電腦上運行本地 Spark Scala 應用程式時，可能會收到一個異常，如[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)中所述。 發生這個例外狀況是因為 Windows 中遺失 **WinUtils.exe**。

要解決此錯誤，您需要[Winutils.exe](https://github.com/steveloughran/winutils)到**C：\WinUtils\bin**這樣的位置，然後將環境變數**HADOOP_HOME**並將變數的值設置為**C_WinUtils**。

### <a name="run-a-local-spark-scala-application"></a>執行本機 Spark Scala 應用程式

1. 啟動 Eclipse，然後建立專案。 在 [新增專案]**** 對話方塊中選取下列選項，然後選取 [下一步]****。

1. 在 **"新專案**"嚮導中，在**HDInsight 本地運行示例 （Scala） 上**選擇**HDInsight 專案** > Spark。 然後選擇 **"下一步**"。

   ![新專案選擇嚮導對話方塊](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. 若要提供專案的詳細資料，請遵循稍早的[設定 HDInsight Spark 叢集的 Spark Scala 專案](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)一節中的步驟 3 到 6。

1. 範本會在 **src** 資料夾下方新增可在電腦本機執行的程式碼範例 (**LogQuery**)。

   ![LogQuery 本地 scala 應用程式的位置](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. 按右鍵**LogQuery.scala** ，然後選擇"**以** > **1"身份運行應用程式**。 在 [主控台]**** 索引標籤上隨即顯示像這樣的輸出：

   ![Spark 應用程式本機執行結果](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>僅限讀取者角色

使用者使用僅限讀取者角色權限將作業提交至叢集時，必須要有 Ambari 認證。

### <a name="link-cluster-from-context-menu"></a>從操作功能表連結叢集

1. 使用僅限讀取者角色帳戶登入。

2. 在 [Azure Explorer]**** 中展開 [HDInsight]****，檢視您訂用帳戶中的 HDInsight 叢集。 標示為 **"Role:Reader"** 的叢集僅具有僅限讀取者角色權限。

    ![Azure 資源管理器角色讀取器中的 HDInsight Spark 群集](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. 以滑鼠右鍵按一下具有僅限讀取者角色權限的叢集。 從操作功能表中選取 [連結此叢集]****，以連結叢集。 輸入 Ambari 使用者名和密碼。

    ![Azure 資源管理器連結中的 HDInsight Spark 群集](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. 如果已成功連結叢集，HDInsight 將會重新整理。
   叢集的階段會成為「已連結」。
  
    ![Azure 資源管理器中的 HDInsight Spark 群集連結](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>藉由展開作業節點來連結叢集

1. 按一下 [作業]**** 節點，[叢集作業存取遭拒]**** 視窗隨即快顯。

2. 按一下 [連結此叢集]**** 以連結叢集。

    ![Azure 資源管理器中的 HDInsight 火花群集9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>從 Spark 提交視窗連結群集

1. 創建 HDInsight 專案。

2. 按右鍵包。 然後選擇**將火花應用程式提交到 HDInsight**。

   ![Azure 資源管理器中的 HDInsight Spark 群集提交](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. 選擇具有**群集名稱**僅讀取器角色許可權的群集。 警告訊息顯示出來。可以按一下 **"連結此群集**"連結群集。

   ![Azure 資源管理器中的 HDInsight Spark 群集連結此](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>檢視儲存體帳戶

* 針對具有僅限讀取者角色權限的叢集，按一下 [儲存體帳戶]**** 節點，[儲存體存取遭拒]**** 視窗隨即快顯。

   ![Azure 資源管理器存儲中的 HDInsight Spark 群集](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![Azure 資源管理器中的 HDInsight Spark 群集被拒絕](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* 針對連結的叢集，按一下 [儲存體帳戶]**** 節點，[儲存體存取遭拒]**** 視窗隨即快顯。

   ![Azure 資源管理器中的 HDInsight 火花群集被拒絕2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>已知的問題

使用**連結群集**時，我建議您提供存儲憑據。

![連結群集與存儲憑據日食](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

提交工作有兩種模式。 如果提供儲存體認證，將使用批次模式提交工作。 否則，將使用互動模式。 如果叢集忙碌，您可能會收到以下錯誤。

![Eclipse 會在叢集忙碌時收到錯誤](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "Eclipse 會在叢集忙碌時收到錯誤")

![日食得到錯誤時集群忙紗](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "日食得到錯誤時集群忙紗")

## <a name="see-also"></a>另請參閱

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>案例

* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：使用 HDInsight 中的 Spark，使用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>建立和執行應用程式

* [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能

* [使用適用於 IntelliJ 的 Azure 工具組中來建立和提交 Spark Scala 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 透過 VPN 遠端偵錯 Apache Spark 應用程式](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 透過 SSH 遠端偵錯 Apache Spark 應用程式](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
