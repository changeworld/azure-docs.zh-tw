---
title: 阿帕奇哈多普&視覺化工作室資料湖工具 - Azure HDInsight
description: 了解如何安裝和使用 Data Lake Tools for Visual Studio 來連線到 Azure HDInsight 中的 Apache Hadoop 叢集，然後執行 Hive 查詢。
keywords: hadoop 工具,hive 查詢,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272782"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢

瞭解如何使用 Microsoft Azure 資料湖和視覺化工作室的流分析工具（也稱為資料湖工具）連接到[Azure HDInsight 中的 Apache Hadoop 群集](apache-hadoop-introduction.md)並提交 Hive 查詢。  

有關使用 HDInsight 的詳細資訊，請參閱[使用 HDInsight 入門](apache-hadoop-linux-tutorial-get-started.md)。  

有關連接到 Apache 風暴群集的詳細資訊，請參閱使用[Visual Studio 的資料湖工具為 Apache 風暴開發 C# 拓撲](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。

您可以使用 Data Lake Tools for Visual Studio 來存取 Azure Data Lake Analytics 和 HDInsight。 如需 Data Lake Tools 的相關資訊，請參閱[使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="prerequisites"></a>Prerequisites

要完成本文並使用視覺化工作室的資料湖工具，您需要以下專案：

* Azure HDInsight 叢集。 若要建立 HDInsight 叢集，請參閱[在 Azure HDInsight 中開始使用 Apache Hadoop](apache-hadoop-linux-tutorial-get-started.md)。 若要執行互動式 Apache Hive 查詢，您需要 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集。  

* [視覺工作室](https://visualstudio.microsoft.com/downloads/)。 [視覺工作室社區版](https://visualstudio.microsoft.com/vs/community/)是免費的。 此處顯示的說明適用于[視覺工作室 2019](https://visualstudio.microsoft.com/downloads/)。

## <a name="install-data-lake-tools-for-visual-studio"></a>安裝 Data Lake Tools for Visual Studio  

按照相應的說明為您的視覺工作室版本安裝資料湖工具：

- 對於視覺工作室 2017 或視覺工作室 2019：

    在視覺化工作室安裝期間，請確保包括**Azure 開發**工作負荷或**資料存儲和處理**工作負荷。  

    對於現有的 Visual Studio 安裝，請轉到 IDE 功能表列，然後選擇 **"工具** > **獲取工具和功能**"以打開視覺化工作室安裝程式。 在 **"工作負載"** 選項卡中，至少選擇**Azure 開發**工作負荷（在**Web &雲**下）或**資料存儲和處理**工作負荷（**在其他工具集**下）。

  ![工作負載選擇，視覺化工作室安裝程式](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- 對於視覺工作室 2015：

    [下載資料湖工具](https://www.microsoft.com/download/details.aspx?id=49504)。 選擇與您的 Visual Studio 版本相符的 Data Lake Tools 版本。

## <a name="update-data-lake-tools-for-visual-studio"></a>更新用於視覺化工作室的資料湖工具  

接下來，請確保將"資料湖工具"更新到最新版本。

1. 開啟 Visual Studio。

2. 在 **"開始"** 視窗中，選擇"**繼續不使用代碼**"。

3. 在視覺化工作室 IDE 功能表列中，選擇**擴展** > **管理擴展**。

4. 在 **"管理擴展"** 對話方塊中，展開**更新**節點。

5. 如果可用更新清單包括**Azure 資料湖和流分析工具**，請選擇它。 然後選擇其**更新**按鈕。 **"下載和安裝**"對話方塊出現並消失後，Visual Studio 會將**Azure 資料湖和流分析工具**擴展添加到更新計畫。

6. 關閉所有視覺化工作室視窗。 將顯示 **"VSIX 安裝程式**"對話方塊。

7. 選擇 **"許可證**"以讀取許可證條款，然後選擇 **"關閉"** 以返回到**VSIX 安裝程式**對話方塊。

8. 選取 [修改]****。 擴展更新的安裝開始。 一段時間後，對話方塊將更改以顯示已完成修改。 選擇 **"關閉**"，然後重新開機 Visual Studio 以完成安裝。

> [!NOTE]  
> 您只可以使用 Data Lake Tools 2.3.0.0 版或更新版本，連線到互動式查詢叢集及執行互動式 Hive 查詢。

## <a name="connect-to-azure-subscriptions"></a>連線到 Azure 訂用帳戶

您可以使用 Visual Studio 的資料湖工具連接到 HDInsight 群集、執行一些基本管理操作以及運行 Hive 查詢。

> [!NOTE]  
> 有關連接到通用 Hadoop 群集的資訊，請參閱[如何使用 Visual Studio 編寫和提交 Hive 查詢](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/)。

### <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶

若要連線到您的 Azure 訂用帳戶：

1. 開啟 Visual Studio。

2. 在 **"開始"** 視窗中，選擇"**繼續不使用代碼**"。

3. 在 IDE 功能表列中，選擇 **"查看** > **伺服器資源管理器**"。

4. 在**伺服器資源管理器**中，按右鍵**Azure**，選擇"**連接到 Microsoft Azure 訂閱**"，然後完成身份驗證過程。 從**伺服器資源管理器**，展開**Azure** > **HDInsight**以查看現有 HDInsight 群集的清單。

5. 如果沒有任何群集，請使用 Azure 門戶、Azure PowerShell 或 HDInsight SDK 創建群集。 有關詳細資訊，請參閱在[HDInsight 中設置群集](../hdinsight-hadoop-provision-linux-clusters.md)。

   ![HDInsight 群集清單、伺服器資源管理器、視覺化工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. 展開某個 HDInsight 叢集。 該群集包含**Hive 資料庫**、預設存儲帳戶、任何其他連結存儲帳戶和**Hadoop 服務日誌**的節點。 您可以進一步展開這些實體。

在連線到您的 Azure 訂用帳戶之後，您可以執行下列工作。

### <a name="connect-to-azure-from-visual-studio"></a>從視覺化工作室連接到 Azure

若要從 Visual Studio 連線到 Azure 入口網站：

1. 在**伺服器資源管理器中**，展開**Azure** > **HDInsight**並選擇群集。

2. 按右鍵 HDInsight 群集，然後選擇**Azure 門戶中的管理群集**。

### <a name="offer-questions-and-feedback-from-visual-studio"></a>提供來自視覺工作室的問題和回饋

要提出問題和/或提供來自 Visual Studio 的回饋，

1. 從伺服器資源管理器中，選擇**Azure** > **HDInsight**。

2. 按右鍵**HDInsight**並選擇**MSDN 論壇**提出問題，或**提供回饋**以提供回饋。

## <a name="link-to-or-edit-a-cluster"></a>連結到或編輯群集

> [!NOTE]
> 目前，您可以連結到的 HDInsight 群集的唯一類型是 Hive 類型。

要連結 HDInsight 群集，可以：

1. 按右鍵**HDInsight**，然後選擇 **"連結 HDInsight 群集**"以顯示 **"連結 HDInsight 群集"** 對話方塊。

2. 在形式*\://\<HTTPs 群集&nbsp;名稱>.azurehdinsight.net*中輸入連接**Url。** 轉到其他欄位時，**群集名稱**會自動使用 URL 的群集名稱部分填充。 然後輸入**使用者名和密碼****，然後**選擇 **"下一步**"。

    ![連結群集、HDInsight、視覺工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. 選取 [完成]****。 如果群集連結成功，則群集將列在**HDInsight**節點下。

要更新連結的群集，請按右鍵群集並選擇 **"編輯**"。 然後，您可以更新群集資訊。

![編輯連結的群集、HDInsight、視覺工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>瀏覽連結的資源
從 [伺服器總管] 中，您可以看到預設的儲存體帳戶，以及任何連結的儲存體帳戶。 如果您展開預設儲存體帳戶，您可以看到儲存體帳戶上的容器。 預設儲存體帳戶和預設容器皆已標示。

![用於視覺化工作室的資料湖工具在伺服器資源管理器中連結資源](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

按右鍵容器並選擇 **"查看容器**"以查看容器的內容。 打開容器後，可以使用工具列按鈕**刷新**內容清單、**上載 Blob、****刪除選定的 Blob、****打開 Blob**和下載（**另存為**）選定的 Blob。

![容器清單和 Blob 操作、HDInsight 群集、視覺化工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>執行互動式 Apache Hive 查詢
[Apache Hive](https://hive.apache.org) 是以 Hadoop 為基礎的資料倉儲基礎結構。 Hive 用於進行資料彙整、查詢及分析。 您可以使用 Data Lake Tools for Visual Studio 從 Visual Studio 執行 Hive 查詢。 有關 Hive 的詳細資訊，請參閱[Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](hdinsight-use-hive.md)

[Azure HDInsight 中的互動式查詢](../interactive-query/apache-interactive-query-get-started.md)在 Apache Hive 2.1 中的[LLAP 上](https://cwiki.apache.org/confluence/display/Hive/LLAP)使用 Hive。 互動式查詢為大型存儲資料集上複雜的資料倉儲樣式查詢帶來交互性。 在互動式查詢上運行 Hive 查詢比傳統的 Hive 批次處理作業快得多。 

> [!NOTE]  
> 只有在您連線到 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集時，您才可以執行互動式 Hive 查詢。

您也可以使用 Data Lake Tools for Visual Studio 來查看 Hive 作業的內容。 Data Lake Tools for Visual Studio 會收集和呈現特定 Hive 作業的 Yarn 記錄。

從**伺服器資源管理器**中選擇**Azure** > **HDInsight**並選擇群集。  此節點是**伺服器資源管理器**中要遵循的部分的起點。

### <a name="view-hivesampletable"></a>檢視 hivesampletable

所有 HDInsight 群集都有一個預設示例`hivesampletable`Hive 表，稱為 。  

從群集中，選擇**Hive 資料庫** > **預設** > **蜂巢示例表**。

- 要查看`hivesampletable`架構：

    展開**蜂巢表**。 將顯示`hivesampletable`列的名稱和資料類型。

- 要查看`hivesampletable`資料：

    按右鍵**蜂巢，** 然後選擇 **"查看前 100 行**"。 100 個結果的清單顯示在**Hive 表：hive 示例視窗**中。 此操作等效于使用 Hive ODBC 驅動程式運行以下 Hive 查詢：

    `SELECT * FROM hivesampletable LIMIT 100`

    您可以通過更改**行數**來自訂行計數;您可以從下拉清單中選擇 50 行、100 行、200 行或 1000 行。

### <a name="create-hive-tables"></a>建立 Hive 資料表
若要建立 Hive 資料表，您可以使用 GUI 或使用 Hive 查詢。 有關使用 Hive 查詢的資訊，請參閱[創建和運行 Hive 查詢](#create-and-run-hive-queries)。

1. 從群集中，選擇**Hive 資料庫** > **預設值**。

2. 按右鍵**預設值**，然後選擇 **"創建表**"。

3. 設定資料表。

4. 選擇 **"創建表"** 按鈕以提交作業，這將創建新的 Hive 表。

    ![創建表視窗、蜂巢、HDInsight 群集、視覺化工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>創建和運行蜂巢查詢
您有兩個選項可建立和執行 Hive 查詢：

* 建立特定查詢
* 建立 Hive 應用程式

#### <a name="create-an-ad-hoc-query"></a>創建臨時查詢

要創建和運行臨時查詢，請進行：

1. 按右鍵要執行查詢的群集，然後選擇 **"編寫 Hive 查詢**"。  

2. 輸入蜂巢查詢。

    Hive 編輯器支援 Intellisense。 Data Lake Tools for Visual Studio 支援在編輯 Hive 指令碼時載入遠端中繼資料。 例如，如果鍵入`SELECT * FROM`，IntelliSense 將列出所有建議的表名稱。 若已指定資料表名稱，IntelliSense 會列出資料行名稱。 此工具支援大部分的 Hive DML 陳述式、子查詢及內建 UDF。

    ![智慧感知示例 1，Hive 臨時查詢、HDInsight 群集、視覺化工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense 示例 2，Hive 臨時查詢、HDInsight 群集、視覺工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense 只建議 HDInsight 工具列中已選取的叢集中繼資料。

    下面是可以使用的依例查詢：

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. 選擇執行模式：

    * **互動**  

        在第一個下拉清單中，選擇 **"互動式** **"，然後選擇"執行**"。

        ![交互模式、Hive 臨時查詢、HDInsight 群集、視覺化工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **批**  

        在第一個下拉清單中，選擇 **"批次**"，然後選擇 **"提交**"（或選擇 **"提交"** 旁邊的下拉圖示，然後選擇 **"高級**"。

        ![批次處理模式、Hive 臨時查詢、HDInsight 群集、視覺化工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        如果選擇高級提交選項，將顯示 **"提交腳本"** 對話方塊。 為腳本配置**作業名稱**、**參數**、**其他配置**和**狀態目錄**。

        ![提交腳本對話方塊、Hive 臨時查詢、HDInsight 群集、視覺化工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > 不能將批次處理提交到互動式查詢群集。  您必須使用互動式模式。

#### <a name="create-a-hive-application"></a>建立 Hive 應用程式

若要建立和執行 Hive 解決方案：

1. 從功能表列中，選擇 **"檔** > **新專案** > **"。**

2. 在"**創建新專案**"視窗中，選擇搜索框並鍵入**Hive**。 然後選擇**Hive 應用程式**並選擇 **"下一步**"。

3. 在 **"配置新專案**"視窗中，輸入**專案名稱**，選擇或創建專案**位置**，然後選擇"**創建**"。

    ![新的蜂巢應用程式，配置您的新專案視窗，HDInsight視覺化工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. 在 [方案總管]**** 中，按兩下 **Script.hql** 來開啟指令碼。

### <a name="view-job-summary-and-output"></a>查看作業摘要和輸出

作業摘要在**批次處理**模式和**互動式**模式之間略有不同。

![蜂巢工作摘要視窗，批次處理和互動式模式，視覺化工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

使用 **"刷新**"圖示更新狀態，直到作業狀態更改為 **"已完成**"。  

* 有關**批次處理**模式下的作業詳細資訊，請選擇底部的連結以查看**作業查詢**、**作業輸出**或**作業日誌**，或**查看紗線日誌**。

* 有關**互動式**模式下的作業詳細資訊，請參閱**輸出**和**HiveServer2 輸出**窗格。

    ![蜂巢互動式作業輸出、HDInsight 集群、視覺工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>查看作業圖

目前，作業圖僅顯示使用 Tez 作為執行引擎的 Hive 作業。  有關啟用 Tez 的資訊，請參閱[Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](hdinsight-use-hive.md)  另請參閱，[使用 Apache Tez 而不是映射減少](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)。  

要查看頂點內的所有運算子，請按兩下作業圖的頂點。 您也可以指向特定運算子，以查看有關運算子的更多詳細資料。

即使 Tez 被指定為執行引擎，如果未啟動 Tez 應用程式，作業圖也可能不會顯示。  出現這種情況的原因是作業不包含 DML 語句，或者 DML 語句可以在不啟動 Tez 應用程式的情況下返回。 例如，`SELECT * FROM table1`不會啟動 Tez 應用程式。

![阿帕奇蜂巢工作圖，視覺工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>查看任務執行詳細資訊

從作業圖中，您可以選擇 **"任務執行詳細資訊"，** 以獲取有關 Hive 作業的結構化和視覺化資訊。 您還可以獲得更多的工作詳細資訊。 如果發生效能問題，您可以使用此檢視來取得有關問題的更多詳細資料。 例如，您可以檢索有關每個任務如何操作的資訊以及有關每個任務的詳細資訊（資料讀取/寫入、計畫/開始/結束時間等）。 使用此資訊，根據視覺化資訊來微調作業組態或系統架構。

![任務執行視圖視窗，資料湖視覺化工作室工具](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>檢視 Hive 工作

您可以檢視 Hive 工作的工作查詢、工作輸出、工作記錄和 Yarn 記錄。

在工具的最新版本中，您可以藉由收集和呈現 Yarn 記錄來查看 Hive 作業的內容。 Yarn 記錄可協助您調查效能問題。 有關 HDInsight 如何收集紗線日誌的詳細資訊，請參閱[訪問 Apache Hadoop YARN 應用程式日誌](../hdinsight-hadoop-access-yarn-app-logs-linux.md)。

若要檢視 Hive 作業：

1. 按右鍵 HDInsight 群集，然後選擇 **"查看作業**"。

    ![查看作業、阿帕奇蜂巢、HDInsight 群集、視覺工作室](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    在該叢集上執行的 Hive 作業清單隨即出現。  

2. 選取一個工作。 在 **"蜂巢作業摘要"** 視窗中，選擇以下連結之一：
    - **作業查詢**
    - **作業輸出**
    - **作業日誌**  
    - **紗線日誌**

## <a name="run-apache-pig-scripts"></a>執行 Apache Pig 指令碼

1. 從功能表列中，選擇 **"檔** > **新專案** > **"。**

2. 在 **"開始"** 視窗中，選擇搜索框並輸入**Pig**。 然後選擇**Pig 應用程式**並選擇 **"下一步**"。

3. 在"**配置新專案**"視窗中，輸入**專案名稱**，並為專案選擇或創建**位置**。 然後選擇 **"創建**"。

4. 在 IDE**解決方案資源管理器**窗格中，按兩下**Script.pig**以打開腳本。

## <a name="feedback-and-known-issues"></a>意見反應和已知問題

* 尚未修正以下問題：未顯示以 null 值開頭的結果。 如果您因為此問題而遭到封鎖，請連絡支援小組。

* Visual Studio 所建立的 HQL 指令碼是根據使用者的所在區域設定進行編碼。 如果您將指令碼當作二進位檔案上傳到叢集，則指令碼不會正確執行。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用 Data Lake Tools for Visual Studio 套件從 Visual Studio 連線到 HDInsight 叢集。 您也了解如何執行 Hive 查詢。 如需詳細資訊，請參閱這些文章：

* [使用 Data Lake Tools for Visual Studio 執行 Apache Hive 查詢](apache-hadoop-use-hive-visual-studio.md)
* [Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](hdinsight-use-hive.md)
* [建立 Apache Hadoop 叢集 - 範本](apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中提交 Apache Hadoop 作業](submit-apache-hadoop-jobs-programmatically.md)
* [在 HDInsight 上使用 Apache Hive 與 Apache Hadoop 分析 Twitter 資料](../hdinsight-analyze-twitter-data-linux.md)
