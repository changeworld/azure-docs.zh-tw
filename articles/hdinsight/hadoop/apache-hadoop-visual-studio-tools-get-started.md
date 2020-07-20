---
title: Apache Hadoop & Visual Studio Data Lake 工具-Azure HDInsight
description: 瞭解如何安裝和使用適用于 Visual Studio 的 Data Lake 工具。 使用工具連接到 Azure HDInsight 中的 Apache Hadoop 叢集，然後執行 Hive 查詢。
keywords: hadoop 工具,hive 查詢,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: 5fce1a8e2f4dda8ea84cfd66aa9dc13768ab17e7
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077080"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢

瞭解如何使用 Microsoft Azure Data Lake 和串流分析 Visual Studio （Data Lake 工具）工具。 使用工具來連線到[Azure HDInsight 中的 Apache Hadoop](apache-hadoop-introduction.md)叢集，並提交 Hive 查詢。  

如需使用 HDInsight 的詳細資訊，請參閱[開始使用 hdinsight](apache-hadoop-linux-tutorial-get-started.md)。  

如需連接到 Apache Storm 的詳細資訊，請參閱[使用 Data Lake 工具開發 Apache Storm 的 c # 拓撲](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。

您可以使用 Data Lake Tools for Visual Studio 來存取 Azure Data Lake Analytics 和 HDInsight。 如需 Data Lake Tools 的相關資訊，請參閱[使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="prerequisites"></a>必要條件

若要完成本文並使用適用于 Visual Studio 的 Data Lake 工具，您需要下列專案：

* Azure HDInsight 叢集。 若要建立 HDInsight 叢集，請參閱[在 Azure HDInsight 中開始使用 Apache Hadoop](apache-hadoop-linux-tutorial-get-started.md)。 若要執行互動式 Apache Hive 查詢，您需要 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集。  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)。 [Visual Studio Community 版本](https://visualstudio.microsoft.com/vs/community/)是免費的。 此處所示的指示適用于[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。

## <a name="install-data-lake-tools-for-visual-studio"></a>安裝 Data Lake Tools for Visual Studio  

請遵循適當的指示，為您的 Visual Studio 版本安裝 Data Lake 工具：

* 針對 Visual Studio 2017 或 Visual Studio 2019：

    在 Visual Studio 安裝期間，請確定您包含**Azure 開發**工作負載或**資料儲存和處理**工作負載。  

    針對現有的 Visual Studio 安裝，請移至 IDE 功能表列，然後選取 [**工具**] [  >  **取得工具和功能**] 以開啟 [Visual Studio 安裝程式]。 在 [**工作負載**] 索引標籤中，選取至少 [ **Azure 開發**] 工作負載（在 [ **Web & 雲端**] 底下）。 或選取**資料儲存和處理**工作負載（在 [**其他工具**組] 底下）。

  ![工作負載選取，Visual Studio 安裝程式](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* 針對 Visual Studio 2015：

    [下載 Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)。 選擇與您的 Visual Studio 版本相符的 Data Lake Tools 版本。

## <a name="update-data-lake-tools-for-visual-studio"></a>更新 Visual Studio 的 Data Lake 工具  

接下來，請務必將 Data Lake 工具更新為最新版本。

1. 開啟 Visual Studio。

2. 在 [**開始**] 視窗中，選取 [**不需要程式碼即可繼續**]。

3. 在 Visual Studio IDE] 功能表列中，選擇 [**擴充**功能] [  >  **管理擴充**功能]。

4. 在 [**管理擴充**功能] 對話方塊中，展開 [**更新**] 節點。

5. 如果可用更新的清單包含**Azure Data Lake 和串流分析工具**，請選取它。 然後選取其 [**更新**] 按鈕。 [**下載並安裝**] 對話方塊出現並消失之後，Visual Studio 將 [ **Azure Data Lake] 和 [串流分析工具**] 延伸模組新增至更新排程。

6. 關閉所有 Visual Studio 視窗。 [ **VSIX 安裝程式**] 對話方塊隨即出現。

7. 選取 [**授權**] 以閱讀授權條款，然後選取 [**關閉**] 以返回 [ **VSIX 安裝程式**] 對話方塊。

8. 選取 [修改]。 延伸模組更新的安裝隨即開始。 一段時間之後，對話方塊會變更，以顯示其已完成修改。 選取 [**關閉**]，然後重新開機 Visual Studio 以完成安裝。

> [!NOTE]  
> 您只可以使用 Data Lake Tools 2.3.0.0 版或更新版本，連線到互動式查詢叢集及執行互動式 Hive 查詢。

## <a name="connect-to-azure-subscriptions"></a>連線到 Azure 訂用帳戶

您可以使用適用于 Visual Studio 的 Data Lake 工具連接到 HDInsight 叢集、執行一些基本管理作業，以及執行 Hive 查詢。

> [!NOTE]  
> 如需連線到一般 Hadoop 叢集的相關資訊，請參閱[如何使用 Visual Studio 撰寫和提交 Hive 查詢](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/)。

### <a name="connect-to-an-azure-subscription"></a>連線到 Azure 訂用帳戶

若要連線到您的 Azure 訂用帳戶：

1. 開啟 Visual Studio。

2. 在 [**開始**] 視窗中，選取 [**不需要程式碼即可繼續**]。

3. 在 IDE 功能表列中，選擇 [ **View**  >  **伺服器總管**]。

4. 在**伺服器總管**中，以滑鼠右鍵按一下 [ **Azure**]，選取 **[連線到 Microsoft Azure 訂**用帳戶]，然後完成驗證程式。 在**伺服器總管**中，展開 [ **Azure**  >  **HDInsight** ] 以查看現有 HDInsight 叢集的清單。

5. 如果您沒有任何叢集，請使用 Azure 入口網站、Azure PowerShell 或 HDInsight SDK 建立一個。 如需詳細資訊，請參閱[在 HDInsight 中設定](../hdinsight-hadoop-provision-linux-clusters.md)叢集。

   ![HDInsight 叢集清單、伺服器總管、Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. 展開某個 HDInsight 叢集。 叢集包含**Hive 資料庫**的節點。 此外，預設儲存體帳戶、任何其他連結的儲存體帳戶，以及**Hadoop 服務記錄**。 您可以進一步展開這些實體。

在連線到您的 Azure 訂用帳戶之後，您可以執行下列工作。

### <a name="connect-to-azure-from-visual-studio"></a>從 Visual Studio 連接到 Azure

若要從 Visual Studio 連線到 Azure 入口網站：

1. 在**伺服器總管**中，展開 [ **Azure**  >  **HDInsight** ]，然後選取您的叢集。

2. 以滑鼠右鍵按一下 HDInsight 叢集，然後選取 [ **Azure 入口網站中的 [管理**叢集]。

### <a name="offer-questions-and-feedback-from-visual-studio"></a>提供 Visual Studio 的問題和意見反應

若要提出問題和，或提供來自 Visual Studio 的意見反應：

1. 從伺服器總管選擇 [ **Azure**  >  **HDInsight**]。

2. 以滑鼠右鍵按一下 [ **HDInsight** ]，然後選取 [ **MSDN 論壇**] 來提出問題，或**提供意見**反應以提供意見反應。

## <a name="link-to-or-edit-a-cluster"></a>連結至或編輯叢集

> [!NOTE]
> 您目前唯一可以連結的 HDInsight 叢集類型是 Hive 類型。

若要連結 HDInsight 叢集：

1. 以滑鼠右鍵按一下 [ **hdinsight**]，然後選取 [**連結 hdinsight**叢集] 以顯示 [**連結 hdinsight**叢集] 對話方塊。

2. 在表單中輸入**連接 Url** `https://CLUSTERNAME.azurehdinsight.net` 。 當您移至另一個欄位時，叢集**名稱**會自動填入您的 URL 的叢集名稱部分。 然後輸入使用者**名稱**和**密碼**，然後選取 **[下一步]**。

    ![連結叢集、HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. 選取 [完成]。 如果叢集連結成功，叢集就會列在 [ **HDInsight** ] 節點底下。

若要更新連結的叢集，請在叢集上按一下滑鼠右鍵，然後選取 [**編輯**]。 然後，您可以更新叢集資訊。

![編輯連結的叢集、HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>瀏覽連結的資源

從 [伺服器總管] 中，您可以看到預設的儲存體帳戶，以及任何連結的儲存體帳戶。 如果您展開預設儲存體帳戶，您可以看到儲存體帳戶上的容器。 預設儲存體帳戶和預設容器皆已標示。

![在伺服器總管中 Visual Studio 連結資源的 Data Lake 工具](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

以滑鼠右鍵按一下容器，然後選取 [ **View container** ] 以查看容器的內容。 開啟容器之後，您可以使用工具列**按鈕來重新**整理 [內容] 清單、 **[上傳 Blob**]、[**刪除選取的 Blob**]、[**開啟 Blob**] 和 [下載（**另存**新檔）] 選取的 blob。

![容器清單和 blob 作業、HDInsight 叢集、Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>執行互動式 Apache Hive 查詢

[Apache Hive](https://hive.apache.org) 是以 Hadoop 為基礎的資料倉儲基礎結構。 Hive 用於進行資料彙整、查詢及分析。 您可以使用 Data Lake Tools for Visual Studio 從 Visual Studio 執行 Hive 查詢。 如需有關 Hive 的詳細資訊，請參閱[Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](hdinsight-use-hive.md)。

[Azure HDInsight 中的互動式查詢](../interactive-query/apache-interactive-query-get-started.md)會在 Apache Hive 2.1 的[LLAP 上使用 Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP) 。 互動式查詢可將互動性帶入大型儲存資料集上的複雜資料倉儲樣式查詢。 在互動式查詢上執行 Hive 查詢的速度會比傳統的 Hive 批次作業快很多。 

> [!NOTE]  
> 只有在您連線到 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集時，您才可以執行互動式 Hive 查詢。

您也可以使用適用于 Visual Studio 的 Data Lake 工具來查看 Hive 作業內的內容。 Data Lake Tools for Visual Studio 會收集和呈現特定 Hive 作業的 Yarn 記錄。

從**伺服器總管**選擇 [ **Azure**  >  **HDInsight** ]，然後選取您的叢集。  此節點是**伺服器總管**中要遵循之區段的起始點。

### <a name="view-hivesampletable"></a>檢視 hivesampletable

所有 HDInsight 叢集都有一個稱為的預設範例 Hive 資料表 `hivesampletable` 。  

從您的叢集中，選擇 [ **Hive 資料庫**] [  >  **預設**  >  **hivesampletable**]。

* 若要查看 `hivesampletable` 架構：

    展開 [ **hivesampletable**]。 欄位的名稱和資料類型 `hivesampletable` 會顯示出來。

* 若要查看 `hivesampletable` 資料：

    以滑鼠右鍵按一下 [ **hivesampletable**]，然後選取 [**查看前 100**個數據列]。 100結果的清單會出現在**Hive 資料表： hivesampletable**視窗中。 此動作相當於使用 Hive ODBC 驅動程式來執行下列 Hive 查詢：

    `SELECT * FROM hivesampletable LIMIT 100`

    您可以藉由變更資料**列數目**來自訂資料列計數;您可以從下拉式清單中選擇 [50]、[100]、[200] 或 [1000] 資料列。

### <a name="create-hive-tables"></a>建立 Hive 資料表

若要建立 Hive 資料表，您可以使用 GUI 或使用 Hive 查詢。 如需使用 Hive 查詢的相關資訊，請參閱[建立和執行 hive 查詢](#create-and-run-hive-queries)。

1. 從您的叢集中，選擇 [ **Hive 資料庫**] [  >  **預設**]。

2. 以滑鼠右鍵按一下 [**預設**]，然後選取 [**建立資料表**]。

3. 設定資料表。

4. 選取 [**建立資料表**] 按鈕以提交作業，這會建立新的 Hive 資料表。

    ![建立資料表視窗、Hive、HDInsight 叢集、Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>建立和執行 Hive 查詢

您有兩個選項可建立和執行 Hive 查詢：

* 建立特定查詢
* 建立 Hive 應用程式

#### <a name="create-an-ad-hoc-query"></a>建立特定查詢

若要建立並執行臨機操作查詢：

1. 在您要執行查詢的叢集上按一下滑鼠右鍵，然後選取 [**撰寫 Hive 查詢**]。  

2. 輸入 Hive 查詢。

    Hive 編輯器支援 Intellisense。 Data Lake Tools for Visual Studio 支援在編輯 Hive 指令碼時載入遠端中繼資料。 例如，如果您輸入 `SELECT * FROM` ，IntelliSense 會列出所有建議的資料表名稱。 若已指定資料表名稱，IntelliSense 會列出資料行名稱。 此工具支援大部分的 Hive DML 陳述式、子查詢及內建 UDF。

    ![IntelliSense 範例1，Hive 臨機操作查詢，HDInsight 叢集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense 範例2、Hive 臨機操作查詢、HDInsight 叢集 Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense 只建議 HDInsight 工具列中已選取的叢集中繼資料。

    以下是您可以使用的範例查詢：

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. 選擇 [執行模式]：

    * **互動式**  

        在第一個下拉式清單中，選擇 [ **Interactive**]，然後選取 [**執行**]。

        ![互動模式，Hive 臨機操作查詢，HDInsight 叢集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        在第一個下拉式清單中，選擇 [ **Batch**]，然後選取 [**提交**]。 或選取 [**提交**] 旁的下拉式圖示，然後選擇 [ **Advanced**]。

        ![批次模式、Hive 臨機操作查詢、HDInsight 叢集、Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        如果您選取 [advanced submit] 選項，就會出現 [**提交腳本**] 對話方塊。 設定腳本的**作業名稱**、**引數**、**其他**設定和**狀態目錄**。

        ![[提交腳本] 對話方塊、[Hive 臨機操作查詢]、[HDInsight 叢集] Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > 您無法將批次提交至互動式查詢叢集。  您必須使用互動模式。

#### <a name="create-a-hive-application"></a>建立 Hive 應用程式

若要建立和執行 Hive 解決方案：

1. 從功能表列中 **，選擇 [** 檔案] [新增] [  >  **New**  >  **專案**]。

2. 在 [**建立新專案**] 視窗中，選取 [搜尋] 方塊，然後輸入**Hive**。 然後選擇 [ **Hive 應用程式**] 並選取 **[下一步]**。

3. 在 [**設定您的新專案**] 視窗中輸入**專案名稱**，選取或建立專案**位置**，然後選取 [**建立**]。

    ![新的 Hive 應用程式、設定您的新專案視窗、HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. 在 [方案總管]**** 中，按兩下 **Script.hql** 來開啟指令碼。

### <a name="view-job-summary-and-output"></a>查看作業摘要和輸出

工作摘要在**批次**和**互動**模式之間有些許差異。

![Hive 作業摘要視窗、批次和互動模式、Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

使用 [重新整理 **] 圖示來**更新狀態，直到作業狀態變更為 [**已完成**] 為止。  

* 如需**批次**模式中的作業詳細資料，請選取底部的連結以查看**作業查詢**、**作業輸出**或**作業記錄**檔，或**查看 Yarn 記錄**。

* 如需**互動**模式的作業詳細資料，請參閱**輸出**和**HiveServer2 輸出**窗格。

    ![Hive 互動式作業輸出、HDInsight 叢集、Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>查看作業圖形

目前，只會針對使用 Tez 做為執行引擎的 Hive 作業顯示作業圖形。  如需啟用 Tez 的相關資訊，請參閱[Azure HDInsight 上的 Apache Hive 和 HiveQL？](hdinsight-use-hive.md)。  另請參閱[使用 Apache Tez，而非 Map 的縮減](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)。  

若要查看頂點內的所有運算子，請按兩下作業圖形的頂點。 您也可以指向特定運算子，以查看有關運算子的更多詳細資料。

即使將 Tez 指定為執行引擎，如果沒有啟動 Tez 應用程式，作業圖形可能不會出現。  發生這種情況的原因可能是作業未包含 DML 語句。 或者，因為 DML 語句可以在不啟動 Tez 應用程式的情況下傳回。 例如， `SELECT * FROM table1` 不會啟動 Tez 應用程式。

![Apache Hive 作業圖形，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>View task 執行詳細資料

從作業圖形中，您可以選取 [工作**執行詳細資料**]，以取得 Hive 作業的結構化和視覺化資訊。 您也可以取得更多作業詳細資料。 如果發生效能問題，您可以使用此檢視來取得有關問題的更多詳細資料。 例如，您可以取得每個工作運作方式的相關資訊，以及每個工作的詳細資訊（資料讀取/寫入、排程/開始/結束時間等等）。 使用此資訊，根據視覺化資訊來微調作業組態或系統架構。

![工作執行視圖視窗，Data Lake Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>檢視 Hive 工作

您可以檢視 Hive 工作的工作查詢、工作輸出、工作記錄和 Yarn 記錄。

在最新版本的工具中，您可以藉由收集和呈現 Yarn 記錄來查看 Hive 工作的內容。 Yarn 記錄可協助您調查效能問題。 如需 HDInsight 如何收集 Yarn 記錄的詳細資訊，請參閱[存取 Apache HADOOP Yarn 應用程式記錄](../hdinsight-hadoop-access-yarn-app-logs-linux.md)檔。

若要檢視 Hive 作業：

1. 以滑鼠右鍵按一下 HDInsight 叢集，然後選取 [**查看作業**]。

    ![View Job，Apache Hive，HDInsight cluster，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    在該叢集上執行的 Hive 作業清單隨即出現。  

2. 選取一個工作。 在 [ **Hive 作業摘要**] 視窗中，選取下列其中一個連結：
    - **作業查詢**
    - **作業輸出**
    - **作業記錄**  
    - **Yarn 記錄檔**

## <a name="run-apache-pig-scripts"></a>執行 Apache Pig 指令碼

1. 從功能表列中 **，選擇 [** 檔案] [新增] [  >  **New**  >  **專案**]。

2. 在 [**開始**] 視窗中，選取 [搜尋] 方塊並輸入**Pig**。 然後選取 [ **Pig 應用程式**] 並選取 **[下一步]**。

3. 在 [**設定您的新專案**] 視窗中輸入**專案名稱**，然後選取或建立專案的**位置**。 然後選取 [建立]。

4. 在 [IDE**方案總管**] 窗格中，按兩下 [ **pig** ] 以開啟腳本。

## <a name="feedback-and-known-issues"></a>意見反應和已知問題

* 尚未修正以下問題：未顯示以 null 值開頭的結果。 如果您因為此問題而遭到封鎖，請連絡支援小組。

* Visual Studio 所建立的 HQL 腳本會根據使用者的本機區域設定進行編碼。 如果您將指令碼當作二進位檔案上傳到叢集，則指令碼不會正確執行。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用 Data Lake Tools for Visual Studio 套件從 Visual Studio 連線到 HDInsight 叢集。 您也了解如何執行 Hive 查詢。 

* [使用 Data Lake Tools for Visual Studio 執行 Apache Hive 查詢](apache-hadoop-use-hive-visual-studio.md)
* [Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](hdinsight-use-hive.md)
* [建立 Apache Hadoop 叢集 - 範本](apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中提交 Apache Hadoop 作業](submit-apache-hadoop-jobs-programmatically.md)
* [在 HDInsight 上使用 Apache Hive 與 Apache Hadoop 分析 Twitter 資料](../hdinsight-analyze-twitter-data-linux.md)
