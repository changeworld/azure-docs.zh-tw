---
title: 適用于 Visual Studio 的 Apache Hive & Data Lake 工具-Azure HDInsight
description: 了解如何使用 Data Lake Tools for Visual Studio 在 Azure HDInsight 上搭配 Apache Hadoop 執行 Apache Hive 查詢。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3e54991f6afd2ace19bf7b70e57bda6f551f7780
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943167"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>使用 Data Lake Tools for Visual Studio 執行 Apache Hive 查詢

了解如何使用 Data Lake Tools for Visual Studio 查詢 Apache Hive。 Data Lake Tools 可讓您在 Azure HDInsight 上輕鬆地建立、提交和監視對 Apache Hadoop 的 Hive 查詢。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Hadoop 叢集。 如需建立此專案的詳細資訊，請參閱 [使用 Resource Manager 範本在 Azure HDInsight 中建立 Apache Hadoop](./apache-hadoop-linux-tutorial-get-started.md)叢集。

* [Visual Studio](https://visualstudio.microsoft.com/vs/)。 本文中的步驟使用 Visual Studio 2019。

* HDInsight tools for Visual Studio 或 Azure Data Lake tools for Visual Studio。 如需安裝和設定工具的詳細資訊，請參閱 [安裝 Data Lake tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)。

## <a name="run-apache-hive-queries-using-the-visual-studio"></a> 使用 Visual Studio 執行 Apache Hive 查詢

您有兩個選項可建立和執行 Hive 查詢：

* 建立特定查詢。
* 建立 Hive 應用程式。

### <a name="create-an-ad-hoc-hive-query"></a>建立特定的 Hive 查詢

臨機操作查詢可在 **批次** 或 **互動** 模式中執行。

1. 啟動 **Visual Studio** ，然後選取 [ **繼續但不** 撰寫程式碼]。

2. 在 **伺服器總管** 中，以滑鼠右鍵按一下 [ **Azure**]，選取 **[連線到 Microsoft Azure 訂** 用帳戶]，然後完成登入程式。

3. 展開 [ **HDInsight**]，在您要執行查詢的叢集上按一下滑鼠右鍵，然後選取 [ **撰寫 Hive 查詢**]。

4. 輸入下列 hive 查詢：

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. 選取 [執行]。 執行模式預設為 **互動式**。

    ![執行互動式 Hive 查詢，Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. 若要在 **批次** 模式中執行相同的查詢，請將下拉式清單從 **互動式** 切換到 **批次**。 [執行] 按鈕會從 [ **執行** ] 變更為 [ **提交**]。

    ![提交 batch Hive 查詢，Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive 編輯器支援 Intellisense。 Data Lake Tools for Visual Studio 支援在編輯 Hive 指令碼時載入遠端中繼資料。 例如，如果您輸入 `SELECT * FROM` ，IntelliSense 會列出所有建議的資料表名稱。 若已指定資料表名稱，IntelliSense 會列出資料行名稱。 此工具支援大部分的 Hive DML 陳述式、子查詢及內建 UDF。 IntelliSense 只建議 HDInsight 工具列中已選取的叢集中繼資料。

7. 在 [查詢] 工具列中 ([查詢] 索引標籤下方的區域，並在查詢文字) 上方選取 [ **提交**]，或選取 [ **提交** ] 旁的下拉箭號，然後從下拉清單中選擇 [ **Advanced** ]。 如果您選取第二個選項，

8. 如果您選取 [advanced submit] 選項，請在 [**提交腳本**] 對話方塊中設定 **工作名稱**、**引數**、**其他** 設定和 **狀態目錄**。 然後選取 **[提交]**。

    ![提交腳本對話方塊、HDInsight Hadoop Hive 查詢](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>建立 Hive 應用程式

若要建立 Hive 應用程式來執行 Hive 查詢，請遵循下列步驟：

1. 開啟 **Visual Studio**。

2. 在 [開始] 視窗中，選取 [建立新專案]。

3. 在 [ **建立新專案** ] 視窗的 [ **搜尋範本** ] 方塊中，輸入 *Hive*。 然後選擇 [ **Hive 應用程式** ] 並選取 **[下一步]**。

4. 在 [ **設定您的新專案** ] 視窗中，輸入 **專案名稱**，選取或建立新專案的 **位置** ，然後選取 [ **建立**]。

5. 開啟使用此專案所建立的 **Script.hql** 檔案，並貼入下列 HiveQL 陳述式中：

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    這些語句會執行下列動作：

    * `DROP TABLE`：刪除資料表（如果有的話）。

    * `CREATE EXTERNAL TABLE`在 Hive 中建立新的「外部」資料表。 外部資料表只會將資料表定義儲存在 Hive 中。  (資料會保留在原始位置。 ) 

        > [!NOTE]  
        > 當您預期以外部來源更新基礎資料（例如 MapReduce 作業或 Azure 服務）時，應該使用外部資料表。
        >
        > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。

    * `ROW FORMAT`：告訴 Hive 如何格式化資料。 在此情況下，每個記錄中的欄位會以空格隔開。

    * `STORED AS TEXTFILE LOCATION`：告訴 Hive 資料儲存在 *example/data* 目錄中，而且會儲存為文字。

    * `SELECT`：選取資料行包含值的所有資料列計數 `t4` `[ERROR]` 。 這個語句會傳回的值 `3` ，因為有三個數據列包含此值。

    * `INPUT__FILE__NAME LIKE '%.log'`：告訴 Hive 只從結尾為 .log 的檔案傳回資料。 這個子句會將搜尋限制為包含資料的 *範例 .log* 檔案。

6. 從查詢檔工具列 (與臨機操作查詢工具列的外觀相似) ，選取您要用於此查詢的 HDInsight 叢集。 然後，視) 需要將 **Interactive** 變更為 **Batch** (，然後選取 [ **提交** ] 以將語句作為 Hive 作業執行。

   [Hive 工作摘要] 將會出現並顯示執行中工作的相關資訊。 使用 [重新整理] 連結來重新整理工作資訊，直到 [工作狀態] 變更為 [已完成] 為止。

   ![完成的 Hive 作業摘要、Hive 應用程式、Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. 選取 [ **工作輸出** ] 以查看此作業的輸出。 它會顯示 `[ERROR] 3`，這是此查詢所傳回的值。

### <a name="additional-example"></a>其他範例

下列範例會依賴上 `log4jLogs` 一個程式中所建立的資料表， [建立 Hive 應用程式](#create-a-hive-application)。

1. 在 **伺服器總管** 中，以滑鼠右鍵按一下您的叢集，然後選取 [ **撰寫 Hive 查詢**]。

2. 輸入下列 hive 查詢：

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    這些語句會執行下列動作：

    * `CREATE TABLE IF NOT EXISTS`：建立資料表（如果尚未存在）。 因為 `EXTERNAL` 未使用關鍵字，所以此語句會建立內部資料表。 內部資料表儲存在 Hive 資料倉儲中，並受到 Hive 所管理。

        > [!NOTE]  
        > 與 `EXTERNAL` 資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

    * `STORED AS ORC`：將資料儲存在 *優化的資料列單欄式* (ORC) 格式。 ORC 是高度最佳化且有效率的 Hive 資料儲存格式。

    * `INSERT OVERWRITE ... SELECT`︰從含有 `[ERROR]`的 `log4jLogs` 資料表選取資料列，然後將資料插入 `errorLogs` 資料表。

3. 必要時，將 **Interactive** 變更為 **Batch** ，然後選取 [ **提交**]。

4. 若要確認作業已建立資料表，請移至 **伺服器總管** 並展開 [ **Azure**  >  **HDInsight**]。 展開您的 HDInsight 叢集，然後展開 [ **Hive 資料庫**  >  **預設值**]。 會列出 **errorLogs** 資料表和 **log4jLogs** 資料表。

## <a name="next-steps"></a>後續步驟

如您所見，HDInsight tools for Visual Studio 提供簡單的方法，可在 HDInsight 上使用 Hive 查詢。

* 如需 HDInsight 中 Hive 的一般資訊，請參閱 [Azure HDInsight 的 Apache Hive 和 HiveQL？](hdinsight-use-hive.md)

* 如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊，請參閱在 [hdinsight 上的 Apache Hadoop 中使用 MapReduce](hdinsight-use-mapreduce.md)

* 如需 HDInsight tools for Visual Studio 的詳細資訊，請參閱[使用 Visual Studio 的 Data Lake 工具連接到 Azure HDInsight 和執行 Apache Hive 查詢](apache-hadoop-visual-studio-tools-get-started.md)
