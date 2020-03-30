---
title: 使用 IntelliJ Azure 工具組調試火花作業（預覽版） - HDInsight
description: 在 Azure 工具組中使用 HDInsight 工具，用於 IntelliJ 調試應用程式的指導
keywords: 遠端偵錯 intellij, 進行 intellij 遠端偵錯, ssh, intellij, hdinsight, 偵錯 intellij, 偵錯
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494600"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>使用用於 IntelliJ 的 Azure 工具組（預覽）失敗引發作業調試

本文提供有關如何在[Azure 工具組](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable)中使用 HDInsight 工具以運行**Spark 故障調試**應用程式的分步指南。

## <a name="prerequisites"></a>Prerequisites

* [甲骨文JAVA開發工具組](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。 本教學課程使用 Java 8.0.202 版。
  
* IntelliJ IDEA。 本文使用[IntelliJIDEA社區2019.1.3](https://www.jetbrains.com/idea/download/#section=windows)。
  
* Azure Toolkit for IntelliJ。 請參閱[安裝 Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)。

* 連接到您的 HDInsight 群集。 請參閱[連接到 HDInsight 群集](apache-spark-intellij-tool-plugin.md)。

* 微軟 Azure 存儲資源管理器。 請參閱[下載微軟 Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="create-a-project-with-debugging-template"></a>使用調試範本創建專案

創建 spark2.3.2 專案以繼續失敗調試，獲取本文檔中的失敗任務調試示例檔。

1. 開啟 IntelliJ IDEA。 打開 **"新專案"** 視窗。

   a. 選取左窗格中的 [Azure Spark/HDInsight]****。

   b. 從主視窗中選擇**具有失敗任務調試示例（預覽）（Scala）的火花專案**。

     ![Intellij 創建調試專案](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. 選取 [下一步]****。

2. 在 [New Project]**** \(新增專案\) 視窗中，執行下列步驟：

   ![Intellij 新專案選擇火花版本](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. 輸入專案名稱和專案位置。

   b. 在**專案 SDK**下拉清單中，為**Spark 2.3.2**群集選擇 JAVA **1.8。**

   c. 在**Spark 版本**下拉清單中，選擇**Spark 2.3.2（Scala 2.11.8）。**

   d. 選取 [完成]****。

3. 選擇**src** > **主** > **scala**以在專案中打開代碼。 此示例使用**AgeMean_Div（））** 腳本。

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>在 HDInsight 群集上運行 Spark Scala/JAVA 應用程式

創建 Spark Scala/JAVA 應用程式，然後通過執行以下步驟在 Spark 群集上運行應用程式：

1. 按一下"**添加配置**"以打開 **"運行/調試配置"** 視窗。

   ![HDI Intellij 添加配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. 在 [Run/Debug Configurations] \(執行/偵錯設定)**** 對話方塊中，選取加號 (**+**)。 然後選擇**HDInsight 上的 Apache Spark**選項。

   ![Intellij 添加新配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. 切換到**群集中的遠端運行**選項卡。輸入**名稱****、Spark 群集**和**主類名稱**的資訊。 我們的工具支援使用**執行程式**進行偵錯。 **numExector，** 預設值為5，你最好不要設置高於3。 為了縮短執行時間，可以將**spark.yarn.maxAppAtos**添加到**作業配置**中，並將該值設置為 1。 按一下 **"確定"** 按鈕以保存配置。

   ![Intellij 運行調試配置新](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. 設定現在會使用您提供的名稱儲存。 若要檢視設定詳細資訊，請選取設定名稱。 若要進行變更，請選取 [Edit Configurations] \(編輯設定\)****。

5. 完成配置設置後，可以針對遠端群集運行專案。

   ![Intellij 調試遠端火花作業遠端運行按鈕](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. 可以從輸出視窗中檢查應用程式 ID。

   ![Intellij 調試遠端火花作業遠端運行結果](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>下載失敗的作業設定檔

如果作業提交失敗，您可以將失敗的作業設定檔下載到本地電腦進行進一步調試。

1. 打開**Microsoft Azure 存儲資源管理器**，查找失敗作業群集的 HDInsight 帳戶，從相應位置下載失敗的作業資源 **：\hdp_spark2 事件\\.spark 失敗\\\<應用程式 ID>** 本地資料夾。 **活動**視窗將顯示下載進度。

   ![Azure 存儲資源管理器下載失敗](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Azure 存儲資源管理器下載成功](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>配置本地調試環境並在發生故障時進行調試

1. 打開原始專案或創建新專案，並將其與原始原始程式碼相關聯。 當前僅支援 spark2.3.2 版本進行故障調試。

1. 在 IntelliJ IDEA 中，創建**Spark 故障調試**設定檔，從以前下載的失敗作業資源中選擇"**火花作業失敗上下文位置"欄位**的 FTD 檔。

   ![克裡特故障配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. 按一下工具列中的本地運行按鈕，該錯誤將顯示在"運行"視窗中。

   ![運行故障配置1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![運行故障配置2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. 將中斷點設置為日誌指示，然後按一下本地調試按鈕以執行本地調試，就像 IntelliJ 中的正常 Scala/JAVA 專案一樣。

1. 調試後，如果專案成功完成，則可以將失敗的作業重新提交到 HDInsight 群集上的 Spark。

## <a name="next-steps"></a><a name="seealso"></a>後續步驟

* [概述：調試阿帕奇火花應用程式](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>示範

* 建立 Scala 專案 (影片)：[Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (建立 Apache Spark Scala 應用程式)
* 遠端偵錯 (影片)：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (使用 Azure Toolkit for IntelliJ 遠端偵錯 HDInsight 叢集上的 Apache Spark 應用程式)

### <a name="scenarios"></a>案例

* [使用 BI 的 Apache Spark：使用 BI 工具在 HDInsight 中使用 Spark 進行互動式資料分析](apache-spark-use-bi-tools.md)
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
