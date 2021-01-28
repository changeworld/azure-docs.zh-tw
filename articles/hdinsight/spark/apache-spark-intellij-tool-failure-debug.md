---
title: 使用 IntelliJ 的 Azure 工具組來 Debug Spark 作業 (預覽版) -HDInsight
description: 在 Azure Toolkit for IntelliJ 中使用 HDInsight 工具來對應用程式進行偵錯工具的指引
keywords: 遠端偵錯 intellij, 進行 intellij 遠端偵錯, ssh, intellij, hdinsight, 偵錯 intellij, 偵錯
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5abbb26e7582d8ddabb73f3a178cf6d87e24db21
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942545"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>使用 Azure Toolkit for IntelliJ (preview) 的 spark 作業無法進行調試

本文提供逐步指引，說明如何使用 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) 中的 HDInsight 工具來執行 **Spark 失敗的調試** 程式。

## <a name="prerequisites"></a>先決條件

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。 本教學課程使用 Java 8.0.202 版。
  
* IntelliJ IDEA。 本文使用 [INTELLIJ 構想 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows)。
  
* Azure Toolkit for IntelliJ。 請參閱[安裝 Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation)。

* 連接到您的 HDInsight 叢集。 請參閱 [連接到您的 HDInsight](apache-spark-intellij-tool-plugin.md)叢集。

* Microsoft Azure 儲存體總管。 請參閱 [下載 Microsoft Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="create-a-project-with-debugging-template"></a>使用調試範本建立專案

建立 spark 2.3.2 專案以繼續失敗的偵測，請在本檔中進行失敗的工作調試範例。

1. 開啟 IntelliJ IDEA。 開啟 [ **新增專案** ] 視窗。

   a. 選取左窗格中的 [Azure Spark/HDInsight]。

   b. 從主視窗中選取 [ **具有失敗工作調試的 Spark 專案] (預覽) # B2 Scala)** 。

     ![Intellij 建立 debug 專案](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. 選取 [下一步]  。

2. 在 [New Project]  \(新增專案\) 視窗中，執行下列步驟：

   ![Intellij 新增專案選取 Spark 版本](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. 輸入專案名稱和專案位置。

   b. 在 [**專案 SDK** ] 下拉式清單中，選取 [適用于 **Spark 2.3.2** 叢集的 **JAVA 1.8** ]。

   c. 在 [ **Spark 版本** ] 下拉式清單中，選取 [ **Spark 2.3.2 (Scala 2.11.8)**。

   d. 選取 [完成]。

3. 選取 **src**  >  **main**  >  **scala** 以開啟專案中的程式碼。 此範例使用 **AgeMean_Div ( # B1** 腳本。

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>在 HDInsight 叢集上執行 Spark Scala/JAVA 應用程式

建立 spark Scala/JAVA 應用程式，然後執行下列步驟，在 Spark 叢集上執行應用程式：

1. 按一下 [ **新增** 設定] 以開啟 [ **執行/調試** 設定] 視窗。

   ![HDI Intellij 新增設定](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. 在 [Run/Debug Configurations] \(執行/偵錯設定) 對話方塊中，選取加號 (**+**)。 然後選取 [ **HDInsight 上的 Apache Spark** ] 選項。

   ![Intellij 新增設定](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. 在 [叢集] 索引標籤中切換至 [ **遠端執行** ]。輸入 **名稱**、 **Spark** 叢集和 **主要類別名稱** 的資訊。 我們的工具支援使用 **執行程式** 進行偵錯。 **NumExectors**，預設值為5，而您最好不要設定高於3。 若要減少執行時間，您可以將 **yarn** 新增至 **工作** 設定，並將值設定為1。 按一下 **[確定]** 按鈕以儲存設定。

   ![Intellij 執行調試的設定新](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. 設定現在會使用您提供的名稱儲存。 若要檢視設定詳細資訊，請選取設定名稱。 若要進行變更，請選取 [Edit Configurations] \(編輯設定\)。

5. 完成設定之後，您可以對遠端叢集執行專案。

   ![Intellij Debug Remote Spark Job Remote run 按鈕](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. 您可以從 [輸出] 視窗中檢查應用程式識別碼。

   ![Intellij Debug Remote Spark Job Remote run result](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>下載失敗的作業設定檔

如果作業提交失敗，您可以將失敗的作業設定檔下載到本機電腦，以進行進一步的偵錯工具。

1. 開啟 **Microsoft Azure 儲存體總管**、找出失敗作業之叢集的 HDInsight 帳戶、將失敗的工作資源從對應的位置下載： **\hdp\spark2-events \\ \\ \<application ID> 至** 本機資料夾。 [ **活動** ] 視窗會顯示下載進度。

   ![Azure 儲存體總管下載失敗](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Azure 儲存體總管下載成功](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>設定本機調試的環境，並在失敗時進行調試

1. 開啟原始專案或建立新專案，並將其與原始原始程式碼建立關聯。 目前只有 spark 2.3.2 版本支援失敗的偵錯工具。

1. 在 IntelliJ 構想中，建立 **Spark 失敗的調試** 程式設定檔，從先前下載的 [ **Spark 作業失敗內容位置** ] 欄位的失敗作業資源中選取 FTD 檔案。

   ![建立失敗設定](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. 按一下工具列中的 [本機執行] 按鈕，錯誤就會顯示在 [執行] 視窗中。

   ![執行失敗-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![執行失敗-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. 在記錄檔中設定中斷點，然後按一下 [本機偵錯工具] 按鈕來進行本機偵錯工具，就像您在 IntelliJ 中的一般 Scala/JAVA 專案一樣。

1. 在偵錯工具之後，如果專案成功完成，您可以將失敗的作業重新提交至 HDInsight 叢集上的 spark。

## <a name="next-steps"></a><a name="seealso"></a>後續步驟

* [總覽： Debug Apache Spark 應用程式](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>示範

* 建立 Scala 專案 (影片)：[Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (建立 Apache Spark Scala 應用程式)
* 遠端偵錯 (影片)：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (使用 Azure Toolkit for IntelliJ 遠端偵錯 HDInsight 叢集上的 Apache Spark 應用程式)

### <a name="scenarios"></a>案例

* [使用 BI 的 Apache Spark：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中利用 HVAC 資料使用 Spark 分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式

* [使用 Scala 建立獨立應用程式](./apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和延伸模組

* [使用 Azure Toolkit for IntelliJ 為 HDInsight 叢集建立 Apache Spark 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 透過 VPN 遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具建立 Apache Spark 應用程式](./apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
* [適用于 HDInsight 的 Apache Spark 叢集中 Jupyter Notebook 的核心](apache-spark-jupyter-notebook-kernels.md)
* [使用 Jupyter 筆記本的外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)