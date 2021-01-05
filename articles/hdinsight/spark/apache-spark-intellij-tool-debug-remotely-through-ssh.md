---
title: Azure Toolkit for IntelliJ：使用 SSH 來對 Spark 應用程式進行偵錯工具-HDInsight
description: 逐步指引如何使用適用於 IntelliJ 之 Azure 工具組中的 HDInsight 工具，透過 SSH 對 HDInsight 叢集上的應用程式進行遠端偵錯
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 7a2044e9bb0110eda666d12e29a715547381e2be
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822330"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>使用 Azure Toolkit for IntelliJ 透過 SSH 在 HDInsight 叢集上進行 Apache Spark 應用程式的偵錯工具

本文提供逐步指引，說明如何使用 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) 中的 hdinsight 工具，從遠端在 HDInsight 叢集上進行應用程式的偵錯工具。 若要對您的專案進行偵錯，您也可以觀看 [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (使用適用於 IntelliJ 的 Azure 工具組對 HDInsight Spark 應用程式進行偵錯) 影片。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Spark 叢集。 請參閱[建立 Apache Spark 叢集](../spark/apache-spark-jupyter-spark-sql-use-portal.md)。

* 針對 Windows 使用者：當您在 Windows 電腦上執行本機 Spark Scala 應用程式時，您可能會收到例外狀況，如 [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)中所述。 發生這個例外狀況是因為 Windows 上遺失 WinUtils.exe。

    若要解決此錯誤，請將 [Winutils.exe](https://github.com/steveloughran/winutils) 下載至 **C:\WinUtils\bin** 之類的位置。 然後，新增環境變數 **HADOOP_HOME**，並將變數的值設為 **C:\WinUtils**。

*  (的[INTELLIJ 構想](https://www.jetbrains.com/idea/download/#section=windows)是免費的。 ) 。

* [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation)。

* [適用于 IntelliJ 的 Scala 外掛程式](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea)。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="create-a-spark-scala-application"></a>建立 Spark Scala 應用程式

1. 啟動 IntelliJ IDEA，然後選取 [建立新專案] 來開啟 [新增專案] 視窗。

1. 選取左窗格中的 [Apache Spark/HDInsight]。

1. 從主視窗中 **，選取包含範例 (Scala) 的 Spark 專案** 。

1. 從 [建置工具] 下拉式清單中，選取下列其中一項：

    * **Maven**：建立 Scala 專案精靈支援。
    * **SBT**：可供管理相依性並建置 Scala 專案。

     ![Intellij 建立新的專案 Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. 選取 [下一步]  。

1. 在下一個 [ **新增專案** ] 視窗中，提供下列資訊：

    |屬性 |描述 |
    |---|---|
    |專案名稱|輸入名稱。 本逐步解說使用 `myApp` 。|
    |專案位置|輸入所要的位置以儲存您的專案。|
    |專案 SDK|如果空白，請選取 [ **新增** ]，然後流覽至您的 JDK。|
    |Spark 版本|建立精靈會為 Spark SDK 和 Scala SDK 整合正確的版本。 如果 Spark 叢集版本早於 2.0，請選取 [Spark 1.x]  。 否則，請選取 [ **Spark** 2.x]。 此範例使用 **Spark 2.3.0 (Scala 2.11.8)** 。|

   ![Intellij 新增專案選取 Spark 版本](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. 選取 [完成]。 可能需要幾分鐘的時間，專案才會變成可用。 觀賞右下角進行進度。

1. 展開您的專案，然後流覽至 **src**  >  **main**  >  **scala**  >  **範例**。 按兩下 [ **SparkCore_WasbIOTest**]。

## <a name="perform-local-run"></a>執行本機執行

1. 從 **SparkCore_WasbIOTest** 腳本中，以滑鼠右鍵按一下腳本編輯器，然後選取 [執行 **' SparkCore_WasbIOTest '** ] 選項以執行本機執行。

1. 本機執行完成後，您可以看到輸出檔案儲存至目前的 project explorer **資料**  >  **__預設值__**。

    ![Intellij 專案本機執行結果](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. 我們的工具已在您執行本機執行和本機偵錯時自動設定預設本機執行設定。 在右上角開啟 [ **hdinsight 上的 spark] xxx 的設定** ，您可以看到 [hdinsight **上的 spark] xxx** 已在 **Apache Spark 的 hdinsight 上** 建立。 切換至 [本機執行] 索引標籤。

    ![Intellij 執行偵錯設定本機執行](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [環境變數](#prerequisites)：如果您已將系統環境變數 **HADOOP_HOME** 設定為 **C:\WinUtils**，它可以自動偵測，不需要手動新增。
    - [WinUtils.exe 位置](#prerequisites)：如果您尚未設定系統環境變數，您可以按一下此按鈕以找到位置。
    - 只要選擇兩個選項之一，在 MacOS 和 Linux 上不需要它們。

1. 您也可以在執行本機執行和本機偵錯之前手動設定。 在前面的螢幕擷取畫面中，選取加號 (**+**) 。 然後選取 [ **HDInsight 上的 Apache Spark** ] 選項。 輸入 [名稱] 的資訊、要儲存的 **主要類別名稱**，然後按一下本機執行按鈕。

## <a name="perform-local-debugging"></a>執行本機偵錯

1. 開啟 **SparkCore_wasbloTest** 指令碼，設定中斷點。

1. 以滑鼠右鍵按一下腳本編輯器，然後選取 [ **[HDInsight 上的 Spark] XXX]** 選項，以執行本機的調試。

## <a name="perform-remote-run"></a>執行遠端執行

1. 流覽至 [**執行**  >  **編輯設定 ...**]。從這個功能表中，您可以建立或編輯遠端偵錯程式的設定。

1. 在 [Run/Debug Configurations] \(執行/偵錯設定) 對話方塊中，選取加號 (**+**)。 然後選取 [ **HDInsight 上的 Apache Spark** ] 選項。

   ![Intellij 新增設定](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. 在 [叢集] 索引標籤中切換至 [ **遠端執行** ]。輸入 **名稱**、 **Spark** 叢集和 **主要類別名稱** 的資訊。 然後按一下 [ **Advanced configuration (遠端偵錯])**。 我們的工具支援使用 **執行程式** 進行偵錯。 **NumExectors**，預設值為 5。 您最好不要設定高於 3。

   ![Intellij 執行調試的設定](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. 在 [ **Advanced Configuration (遠端偵錯)** 部分，選取 [ **啟用 Spark 遠端偵錯**]。 輸入 SSH 使用者名稱，然後輸入密碼或使用私密金鑰檔案。 如果您想要執行遠端偵錯，您需要設定它。 如果您只想要使用遠端執行，則不需要設定它。

   ![Intellij Advanced Configuration enable spark remote debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. 設定現在會使用您提供的名稱儲存。 若要檢視設定詳細資訊，請選取設定名稱。 若要進行變更，請選取 [Edit Configurations] \(編輯設定\)。

1. 完成組態設定之後，您可以針對遠端叢集執行專案，或執行遠端偵錯。

   ![Intellij Debug Remote Spark Job Remote run 按鈕](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. 按一下送出記錄未出現在左面板中的 [中斷連線] 按鈕。 不過，它仍在後端執行。

   ![Intellij Debug Remote Spark Job Remote run result](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>執行遠端偵錯

1. 設定一個中斷點，然後按一下 [遠端偵錯] 圖示。 與遠端提交的差異是，必須設定 SSH 使用者名稱/密碼。

   ![Intellij Debug Remote Spark Job debug 圖示](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. 當程式執行觸及中斷點時，您會在 [偵錯工具] 窗格中看到一個 [驅動程式] 索引標籤和兩個 [執行程式] 索引標籤。 選取 **繼續程式** 圖示以繼續執行程式碼，然後到達下一個中斷點。 您必須切換到正確的 [執行程式] 索引標籤，才能尋找要偵錯的目標執行程式。 您可以在對應的 [主控台] 索引標籤中檢視執行記錄。

   ![Intellij Debug 遠端 Spark 作業調試索引標籤](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>執行遠端偵錯和錯誤修正

1. 設定兩個中斷點，然後選取 **偵錯** 圖示以啟動遠端偵錯程序。

1. 程式碼會在第一個中斷點停止，然後在 [變數] 窗格中顯示參數和變數資訊。

1. 選取 **繼續程式** 圖示以繼續。 程式碼會在第二個點停止。 正如預期，會攔截到例外狀況。

   ![Intellij Debug Remote Spark 作業擲回錯誤](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. 再次選取 **繼續程式** 圖示。 [HDInsight Spark Submission] \(HDInsight Spark 提交) 視窗會顯示「作業執行失敗」錯誤。

   ![Intellij Debug Remote Spark 作業錯誤提交](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. 若要使用 IntelliJ 偵錯功能動態更新變數值，請再次選取 [偵錯]。 [變數] 窗格會再次出現。

1. 以滑鼠右鍵按一下 [Debug] \(偵錯\) 索引標籤上的目標，然後選取 [Set Value] \(設定值\)。 接下來，輸入變數的新值。 然後選取 **Enter** 儲存值。

   ![Intellij Debug Remote Spark Job set 值](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. 選取 **繼續程式** 圖示以繼續執行程式。 此時，不會攔截到任何例外狀況。 您可以看到專案成功執行而未發生任何例外狀況。

   ![Intellij Debug Remote Spark 作業但不例外](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>後續步驟

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>示範

* 建立 Scala 專案 (影片)：[Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (建立 Apache Spark Scala 應用程式)
* 遠端偵錯 (影片)：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (使用 Azure Toolkit for IntelliJ 遠端偵錯 HDInsight 叢集上的 Apache Spark 應用程式)

### <a name="scenarios"></a>案例

* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中利用 HVAC 資料使用 Spark 分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式

* [使用 Scala 建立獨立應用程式](./apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和延伸模組

* [使用 Azure Toolkit for IntelliJ 為 HDInsight 叢集建立 Apache Spark 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 透過 VPN 遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具建立 Apache Spark 應用程式](./apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
* [適用于 HDInsight 的 Apache Spark 叢集中 Jupyter Notebook 的核心](apache-spark-jupyter-notebook-kernels.md)
* [使用 Jupyter 筆記本的外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)