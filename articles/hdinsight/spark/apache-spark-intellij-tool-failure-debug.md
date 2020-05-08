---
title: 使用 IntelliJ 進行 Spark 作業的 Azure 工具組（預覽）-HDInsight
description: 在 Azure Toolkit for IntelliJ 中使用 HDInsight 工具來進行偵錯工具的指導方針
keywords: 遠端偵錯 intellij, 進行 intellij 遠端偵錯, ssh, intellij, hdinsight, 偵錯 intellij, 偵錯
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 052b12817b788ff38f0fab72a5420896b062c732
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857426"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>使用 Azure Toolkit for IntelliJ （預覽）進行 spark 作業調試失敗

本文提供如何在[Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij)中使用 HDInsight 工具來執行**Spark 失敗的調試**程式的逐步指引。

## <a name="prerequisites"></a>必要條件

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。 本教學課程使用 Java 8.0.202 版。
  
* IntelliJ IDEA。 本文使用[INTELLIJ 觀念 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows)。
  
* Azure Toolkit for IntelliJ。 請參閱[安裝 Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/installation)。

* 連接到您的 HDInsight 叢集。 請參閱[連接到您的 HDInsight](apache-spark-intellij-tool-plugin.md)叢集。

* Microsoft Azure 儲存體總管。 請參閱[下載 Microsoft Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="create-a-project-with-debugging-template"></a>使用調試範本建立專案

建立 spark 2.3.2 專案以繼續進行失敗的 debug，並在本檔中執行失敗工作的調試範例檔案。

1. 開啟 IntelliJ IDEA。 開啟 [**新增專案**] 視窗。

   a. 選取左窗格中的 [Azure Spark/HDInsight]****。

   b. 從主視窗中選取 [**具有失敗工作的調試範例（預覽）（Scala）的 Spark 專案**]。

     ![Intellij 建立 debug 專案](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. 選取 [下一步]  。

2. 在 [New Project]  \(新增專案\) 視窗中，執行下列步驟：

   ![Intellij 新增專案選取 Spark 版本](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. 輸入專案名稱和專案位置。

   b. 在 [**專案 SDK** ] 下拉式清單中，選取 [ **JAVA 1.8** for **Spark 2.3.2** cluster]。

   c. 在 [ **Spark 版本**] 下拉式清單中，選取 [ **Spark 2.3.2 （Scala 2.11.8）**]。

   d. 選取 [完成]  。

3. 選取**src** > **main** > **scala**以在專案中開啟您的程式碼。 這個範例會使用**AgeMean_Div （）** 腳本。

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>在 HDInsight 叢集上執行 Spark Scala/JAVA 應用程式

建立 spark Scala/JAVA 應用程式，然後執行下列步驟，在 Spark 叢集上執行應用程式：

1. 按一下 [**新增**設定] 以開啟 [**執行/Debug**設定] 視窗。

   ![HDI Intellij 新增設定](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. 在 [Run/Debug Configurations] \(執行/偵錯設定)**** 對話方塊中，選取加號 (**+**)。 然後選取 [**在 HDInsight 上 Apache Spark** ] 選項。

   ![Intellij 新增設定](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. 切換至 [在叢集中**遠端執行**] 索引標籤。輸入 [**名稱**]、[ **Spark**叢集] 和 [**主要類別名稱**] 的資訊。 我們的工具支援使用**執行程式**進行偵錯。 **NumExectors**，預設值為5，而您最好不要設定高於3。 若要縮短執行時間，您可以將**maxAppAttempts**新增至**作業**設定，並將值設定為1。 按一下 **[確定]** 按鈕以儲存設定。

   ![Intellij 執行的調試設定新](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. 設定現在會使用您提供的名稱儲存。 若要檢視設定詳細資訊，請選取設定名稱。 若要進行變更，請選取 [Edit Configurations] \(編輯設定\)****。

5. 完成設定之後，您可以對遠端叢集執行專案。

   ![Intellij Debug Remote Spark 作業遠端執行按鈕](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. 您可以從 [輸出] 視窗中檢查 [應用程式識別碼]。

   ![Intellij Debug Remote Spark 作業遠端執行結果](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>下載失敗的作業設定檔

如果作業提交失敗，您可以將失敗的作業設定檔下載到本機電腦，以進行進一步的偵錯工具。

1. 開啟**Microsoft Azure 儲存體總管**，找出失敗作業的叢集 HDInsight 帳戶，從對應的位置下載失敗的工作資源： ** \\\hdp\spark2-events。 spark-失敗\\\<應用程式識別碼>** 到本機資料夾。 [**活動**] 視窗會顯示下載進度。

   ![Azure 儲存體總管下載失敗](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Azure 儲存體總管下載成功](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>在失敗時設定本機的調試環境和調試

1. 開啟原始專案，或建立新的專案，並將它與原始原始程式碼產生關聯。 目前僅支援 spark 2.3.2 版本來進行失敗的偵錯工具。

1. 在 IntelliJ 概念中，建立**Spark 失敗的 Debug** config 檔案，從先前下載的 [ **Spark 作業失敗內容位置**] 欄位的失敗作業資源中選取 FTD 檔案。

   ![建立失敗設定](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. 按一下工具列中的 [本機執行] 按鈕，錯誤就會顯示在 [執行] 視窗中。

   ![執行失敗-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![執行失敗-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. 將 [中斷點] 設定為 [記錄檔]，然後按一下 [本機偵錯工具] 按鈕來執行本機的偵錯工具，就像 IntelliJ 中的一般 Scala/JAVA 專案一樣。

1. 在偵測之後，如果專案成功完成，您可以將失敗的作業重新提交至 HDInsight 叢集上的 spark。

## <a name="next-steps"></a><a name="seealso"></a>後續步驟

* [總覽： Debug Apache Spark 應用程式](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>示範

* 建立 Scala 專案 (影片)：[Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (建立 Apache Spark Scala 應用程式)
* 遠端偵錯 (影片)：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (使用 Azure Toolkit for IntelliJ 遠端偵錯 HDInsight 叢集上的 Apache Spark 應用程式)

### <a name="scenarios"></a>案例

* [使用 BI Apache Spark：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中利用 HVAC 資料使用 Spark 分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式

* [使用 Scala 建立獨立應用程式](../hdinsight-apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能

* [使用 Azure Toolkit for IntelliJ 為 HDInsight 叢集建立 Apache Spark 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 透過 VPN 遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具建立 Apache Spark 應用程式](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
* [HDInsight 的 Apache Spark 叢集中 Jupyter Notebook 可用的核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源

* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
