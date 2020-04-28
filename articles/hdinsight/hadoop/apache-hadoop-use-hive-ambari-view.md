---
title: 在 Azure HDInsight 中搭配 Apache Hadoop 使用 Apache Ambari Hive View
description: 了解如何從網頁瀏覽器使用 Hive 檢視來提交 Hive 查詢。 Hive 檢視是以 Linux 為基礎的 HDInsight 叢集隨附的 Ambari 檢視的一部分。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 832caaed0816e3dd7d177f634209dea42e5312c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192675"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>在 HDInsight 中搭配 Apache Hadoop 使用 Apache Ambari Hive 檢視

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

了解如何使用 Apache Ambari Hive 檢視執行 Hive 查詢。 Hive 檢視可讓您從網頁瀏覽器編寫、最佳化及執行 Hive 查詢。

## <a name="prerequisites"></a>Prerequisites

HDInsight 上的 Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。

## <a name="run-a-hive-query"></a>執行 HIVE 查詢

1. 從 [ [Azure 入口網站](https://portal.azure.com/)] 中，選取您的叢集。  如需相關指示，請參閱[列出和顯示](../hdinsight-administer-use-portal-linux.md#showClusters)叢集。 叢集會在新的入口網站視圖中開啟。

1. 從叢集**儀表板**中，選取 [ **Ambari views**]。 出現驗證的提示時，請使用您在建立叢集時提供的叢集登入 (預設為 `admin`) 帳戶名稱和密碼。 您也可以在流覽`https://CLUSTERNAME.azurehdinsight.net/#/main/views`器中流覽至`CLUSTERNAME` ，其中是您的叢集名稱。

1. 從檢視清單中，選取 [Hive 檢視]____。

    ![Apache Ambari 選取 Apache Hive 視圖](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Hive 檢視頁面類似於下圖：

    ![[Hive 檢視] 的查詢工作表影像](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. 從 [查詢]____ 索引標籤中，將下列 HiveQL 陳述式貼到工作表中：

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    這些語句會執行下列動作：

    |引數 | 描述 |
    |---|---|
    |DROP TABLE|刪除資料表和資料檔 (如果資料表已經存在)。|
    |CREATE EXTERNAL TABLE|在 Hive 中建立新的「外部」資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。|
    |資料列格式|顯示設定資料格式的方式。 在此情況下，每個記錄中的欄位會以空格隔開。|
    |儲存為 TEXTFILE 位置|顯示資料的儲存位置，以及資料是儲存為文字。|
    |SELECT|選取 t4 資料行包含 [ERROR] 值之所有資料列的計數。|

   > [!IMPORTANT]  
   > 將 [資料庫]____ 選取項目保留為 [預設]____。 本文件中的範例使用 HDInsight 隨附的預設資料庫。

1. 若要啟動查詢，請選取工作表下方的 [**執行**]。 按鈕會變成橘色，而且文字會變更為 [停止]****。

1. 查詢完成之後，[結果]**** 索引標籤會顯示作業的結果。 下列文字是查詢結果：

        loglevel       count
        [ERROR]        3

    您可以使用 [**記錄**檔] 索引標籤來查看作業所建立的記錄資訊。

   > [!TIP]  
   > 從 [**結果**] 索引標籤下的 [**動作**] 下拉對話方塊下載或儲存結果。

### <a name="visual-explain"></a>視覺解說

若要顯示查詢計劃的視覺效果，請選取工作表下方的 [視覺說明]**** 索引標籤。

查詢的 [視覺解說]**** 檢視有助於了解複雜查詢的流程。

### <a name="tez-ui"></a>Tez UI

若要顯示查詢的 Tez UI，請選取工作表下方的 [ **TEZ ui** ] 索引標籤。

> [!IMPORTANT]  
> Tez 的用途並非解析所有查詢。 您不需要使用 Tez 便可解析許多查詢。

## <a name="view-job-history"></a>檢視作業記錄

[作業]____ 索引標籤會顯示 Hive 查詢的歷程記錄。

![Apache Hive 查看作業] 索引標籤歷程記錄](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>資料庫資料表

您可以使用 [資料表]____ 索引標籤，在 Hive 資料庫中使用資料表。

![[Apache Hive 資料表] 索引標籤的影像](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>儲存的查詢

在 [**查詢**] 索引標籤中，您可以選擇性地儲存查詢。 儲存查詢之後，您就可以從 [儲存的查詢]____ 索引標籤重複使用它。

![Apache Hive views [已儲存的查詢] 索引標籤](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> 已儲存的查詢會存放在預設叢集儲存體中。 您可以路徑 `/user/<username>/hive/scripts` 下找到儲存的查詢。 這些查詢會儲存為純文字 `.hql` 檔案。
>
> 如果您刪除該叢集，但保留儲存體，您可以使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)或 Data Lake 儲存體總管 (從 [Azure 入口網站](https://portal.azure.com)) 之類的公用程式來擷取查詢。

## <a name="user-defined-functions"></a>使用者定義的函式

您可以透過使用者定義函式 (UDF) 來擴充 Hive 。 使用 UDF 在 HiveQL 中實作不易模型化的功能或邏輯。

使用 [Hive 檢視] 頂端的 [UDF]**** 索引標籤來宣告並儲存一組 UDF。 這些 UDF 可以在 [查詢編輯器]**** 中使用。

![Apache Hive view Udf] 索引標籤顯示](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

[**插入 udf** ] 按鈕會出現在 [**查詢編輯器**] 的底部。 此專案會顯示 Hive 視圖中所定義 Udf 的下拉式清單。 選取 UDF 會將 HiveQL 陳述式新增至查詢以啟用 UDF。

例如，如果您已定義具有下列屬性的 UDF：

* 資源名稱：myudfs

* 資源路徑︰/myudfs.jar

* UDF 名稱：myawesomeudf

* UDF 類別名稱：com.myudfs.Awesome

使用 [插入 udf]**** 按鈕會顯示名為 [myudfs]**** 的項目，以及針對該資源定義的每個 UDF 的另一個下拉式清單。 在此案例中為 **myawesomeudf**。 選取此項目會將下列項目新增至查詢的開頭：

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

您接著可以在查詢中使用 UDF。 例如： `SELECT myawesomeudf(name) FROM people;` 。

如需在 HDInsight 上搭配 Hive 使用 UDF 的詳細資訊，請參閱下列文章：

* [在 HDInsight 中搭配 Apache Hive 和 Apache Pig 使用 Python](python-udf-hdinsight.md)
* [在 HDInsight 中搭配使用 Java UDF 和 Apache Hive](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Hive 設定

您可以變更各種 Hive 設定，例如將 Hive 的執行引擎從 Tez (預設) 變更為 MapReduce。

## <a name="next-steps"></a>後續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [在 HDInsight 上將 Apache Hive 與 Apache Hadoop 搭配使用](hdinsight-use-hive.md)
* [使用 Apache Beeline 用戶端搭配 Apache Hive](apache-hadoop-use-hive-beeline.md)
