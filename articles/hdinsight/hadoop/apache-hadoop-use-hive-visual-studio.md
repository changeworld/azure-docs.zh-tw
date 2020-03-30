---
title: Apache Hive &視覺化工作室的資料湖工具 - Azure HDInsight
description: 了解如何使用 Data Lake Tools for Visual Studio 在 Azure HDInsight 上搭配 Apache Hadoop 執行 Apache Hive 查詢。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687792"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>使用 Data Lake Tools for Visual Studio 執行 Apache Hive 查詢

了解如何使用 Data Lake Tools for Visual Studio 查詢 Apache Hive。 Data Lake Tools 可讓您在 Azure HDInsight 上輕鬆地建立、提交和監視對 Apache Hadoop 的 Hive 查詢。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Hadoop 叢集。 有關創建此專案的資訊，請參閱[使用資源管理器範本在 Azure HDInsight 中創建 Apache Hadoop 群集](./apache-hadoop-linux-tutorial-get-started.md)。

* [視覺工作室](https://visualstudio.microsoft.com/vs/)。 本文中的步驟使用 Visual Studio 2019。

* HDInsight tools for Visual Studio 或 Azure Data Lake tools for Visual Studio。 有關安裝和組態工具的資訊，請參閱[為視覺化工作室安裝資料湖工具](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)。

## <a name="run-apache-hive-queries-using-the-visual-studio"></a> 使用 Visual Studio 執行 Apache Hive 查詢

您有兩個選項可建立和執行 Hive 查詢：

* 創建臨時查詢。
* 創建蜂巢應用程式。

### <a name="create-an-ad-hoc-hive-query"></a>創建臨時 Hive 查詢

臨時查詢可以在**批次處理**或**互動式**模式下執行。

1. 啟動**視覺化工作室**並選擇 **"繼續無代碼**"。

2. 從**伺服器資源管理器**中，按右鍵**Azure，** 選擇 **"連接到 Microsoft Azure 訂閱..."，** 然後完成登錄過程。

3. 展開**HDInsight**，按右鍵要執行查詢的群集，然後選擇 **"編寫 Hive 查詢**"。

4. 輸入以下配置單元查詢：

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. 選取 [執行] ****。 執行模式預設為**互動式**。

    ![執行互動式蜂巢查詢，視覺化工作室](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. 要在**批次處理**模式下運行同一查詢，請從 **"互動式**"切換下拉清單到**批次處理**。 執行按鈕從 **"執行"更改為"****提交**"。

    ![提交批次處理蜂巢查詢，視覺化工作室](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    Hive 編輯器支援 Intellisense。 Data Lake Tools for Visual Studio 支援在編輯 Hive 指令碼時載入遠端中繼資料。 例如，如果鍵入`SELECT * FROM`，IntelliSense 將列出所有建議的表名稱。 若已指定資料表名稱，IntelliSense 會列出資料行名稱。 此工具支援大部分的 Hive DML 陳述式、子查詢及內建 UDF。 IntelliSense 只建議 HDInsight 工具列中已選取的叢集中繼資料。

7. 在查詢工具列（查詢選項卡下方的區域和查詢文本上方的區域）中，選擇 **"提交**"或選擇 **"提交"** 旁邊的下拉箭頭，然後從下拉清單中選擇 **"高級**"。 如果選擇後一個選項，

8. 如果選擇了高級提交選項，請在 **"提交腳本"** 對話方塊中配置**作業名稱**、**參數**、**其他配置**和**狀態目錄**。 然後選取 **[提交]**。

    ![提交腳本對話方塊，HDInsight Hadoop Hive 查詢](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>建立 Hive 應用程式

要通過創建 Hive 應用程式運行 Hive 查詢，請按照以下步驟操作：

1. 打開**視覺工作室**。

2. 在 [開始]**** 視窗中，選取 [建立新專案]****。

3. 在"**創建新專案**"視窗中，在 **"搜索範本"** 框中，輸入*Hive*。 然後選擇**Hive 應用程式**並選擇 **"下一步**"。

4. 在 **"配置新專案**"視窗中，輸入**專案名稱**，選擇或為新專案創建**位置**，然後選擇"**創建**"。

5. 開啟使用此專案所建立的 **Script.hql** 檔案，並貼入下列 HiveQL 陳述式中：

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    這些語句執行以下操作：

    * `DROP TABLE`：如果表存在，則將其刪除。

    * `CREATE EXTERNAL TABLE`在 Hive 中建立新的「外部」資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 （資料保留在原始位置。

        > [!NOTE]  
        > 當您期望外部源（如 MapReduce 作業或 Azure 服務）更新基礎資料時，應使用外部表。
        >
        > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。

    * `ROW FORMAT`：告訴 Hive 如何格式化資料。 在此情況下，每個記錄中的欄位會以空格隔開。

    * `STORED AS TEXTFILE LOCATION`：告訴 Hive 資料存儲在*示例/資料*目錄中，並且以文本形式存儲。

    * `SELECT`： 選擇列`t4`包含值`[ERROR]`的所有行的計數。 此語句返回 的值`3`，因為三行包含此值。

    * `INPUT__FILE__NAME LIKE '%.log'`：告訴 Hive 僅從 以 .log 結尾的檔返回資料。 此子句將搜索限制為包含資料的*sample.log*檔。

6. 從查詢檔工具列（其外觀與臨時查詢工具列類似）中選擇要用於此查詢的 HDInsight 群集。 然後，將 **"交互"** 更改為 **"批次處理**"（如有必要），然後選擇 **"提交"** 以將語句作為 Hive 作業運行。

   [Hive 工作摘要]**** 將會出現並顯示執行中工作的相關資訊。 使用 [重新整理]**** 連結來重新整理工作資訊，直到 [工作狀態]**** 變更為 [已完成]**** 為止。

   ![完成蜂巢工作摘要，蜂巢應用程式，視覺工作室](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. 選擇**作業輸出**以查看此作業的輸出。 它會顯示 `[ERROR] 3`，這是此查詢所傳回的值。

### <a name="additional-example"></a>其他範例

下面的示例依賴于在上一`log4jLogs`個過程中創建的表"創建[Hive 應用程式](#create-a-hive-application)"。

1. 從**伺服器資源管理器**中，按右鍵群集並選擇 **"寫入 Hive 查詢**"。

2. 輸入以下配置單元查詢：

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    這些語句執行以下操作：

    * `CREATE TABLE IF NOT EXISTS`：如果表不存在，則創建表。 `EXTERNAL`由於關鍵字未使用，因此此語句將創建一個內部表。 內部資料表儲存在 Hive 資料倉儲中，並受到 Hive 所管理。

        > [!NOTE]  
        > 與 `EXTERNAL` 資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

    * `STORED AS ORC`：以*優化的行列*（ORC） 格式存儲資料。 ORC 是高度最佳化且有效率的 Hive 資料儲存格式。

    * `INSERT OVERWRITE ... SELECT`︰從含有 `[ERROR]`的 `log4jLogs` 資料表選取資料列，然後將資料插入 `errorLogs` 資料表。

3. 如有必要，將 **"交互"** 更改為 **"批次**"，然後選擇"**提交**"。

4. 要驗證作業是否創建了表，請轉到**伺服器資源管理器**並展開**Azure** > **HDInsight**。 展開 HDInsight 群集，然後展開**Hive 資料庫** > **預設值**。 會列出 **errorLogs** 資料表和 **log4jLogs** 資料表。

## <a name="next-steps"></a>後續步驟

如您所見，HDInsight tools for Visual Studio 提供簡單的方法，可在 HDInsight 上使用 Hive 查詢。

* 有關 HDInsight 中的 Hive 的一般資訊，請參閱[Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](hdinsight-use-hive.md)

* 有關在 HDInsight 上使用 Hadoop 的其他方法的資訊，請參閱[在 HDInsight 上使用 Apache Hadoop 中的地圖減少](hdinsight-use-mapreduce.md)

* 有關 Visual Studio 的 HDInsight 工具的詳細資訊，請參閱[使用視覺化工作室的資料湖工具連接到 Azure HDInsight 並運行 Apache Hive 查詢](apache-hadoop-visual-studio-tools-get-started.md)
