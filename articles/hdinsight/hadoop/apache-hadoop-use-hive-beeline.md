---
title: 使用 Apache Beeline 搭配 Apache Hive - Azure HDInsight
description: 了解如何使用 Beeline 用戶端以 Hadoop on HDInsight 執行 Hive 查詢。 Beeline 是透過 JDBC 與 HiveServer2 搭配作業的公用程式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 3614fac027dd32ab5f5d70f5835432ac3b9b512d
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207745"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>使用 Apache Beeline 用戶端搭配 Apache Hive

了解如何使用 [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 在 HDInsight 上執行 Apache Hive 查詢。

Beeline 是 Hive 用戶端，隨附於您的 HDInsight 叢集的前端節點。 若要連線到您的 HDInsight 叢集上安裝的 Beeline 用戶端，或在本機安裝 Beeline，請參閱連線[至或安裝 Apache Beeline](connect-install-beeline.md)。 Beeline 會使用 JDBC 連線至 HiveServer2，它是裝載在 HDInsight 叢集上的服務。 您也可以使用 Beeline 透過網際網路從遠端存取 HDInsight 上的 Hive。 下列範例提供最常用來從 Beeline 連線至 HDInsight 的連接字串。

## <a name="prerequisites-for-examples"></a>範例的必要條件

* HDInsight 上的 Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。

* 請注意叢集主要儲存體的 URI 配置。 例如，針對 `wasb://` Azure 儲存體、 `abfs://` Azure Data Lake Storage Gen2，或 `adl://` 用於 Azure Data Lake Storage Gen1。 如果已啟用 Azure 儲存體的安全傳輸，URI 為 `wasbs://` 。 如需詳細資訊，請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

* 選項1： SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。 本檔中的大部分步驟都假設您使用的是從 SSH 會話到叢集的 Beeline。

* 選項2：本機 Beeline 用戶端。

## <a name="run-a-hive-query"></a>執行 HIVE 查詢

此範例是以從 SSH 連線使用 Beeline 用戶端為基礎。

1. 使用下列程式碼開啟與叢集的 SSH 連線。 將 `sshuser` 取代為叢集的 SSH 使用者，並將 `CLUSTERNAME` 取代為叢集的名稱。 出現提示時，請輸入 SSH 使用者帳戶的密碼。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 輸入下列命令，從開啟的 SSH 會話連接到 HiveServer2 與您的 Beeline 用戶端：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline 命令以 `!` 字元開頭，例如 `!help` 顯示說明。 不過，一些命令可以省略 `!`。 例如，`help` 也能運作。

    有 `!sql` ，這是用來執行 HiveQL 語句。 不過，HiveQL 如此常用，因此您可以省略前面的 `!sql`。 下列兩個陳述式是相等的：

    ```hiveql
    !sql show tables;
    show tables;
    ```

    新的叢集上只會列出一個資料表：**hivesampletable**。

4. 使用下列命令來顯示 hivesampletable 的結構描述：

    ```hiveql
    describe hivesampletable;
    ```

    此命令會傳回下列資訊：

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    此資訊描述資料表中的資料行。

5. 輸入下列語句，以使用 HDInsight 叢集所提供的範例資料來建立名為**log4jLogs**的資料表： (根據您的 URI 配置，視需要進行修訂。 ) 

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    這些語句會執行下列動作：

    |引數 |描述 |
    |---|---|
    |DROP TABLE|如果資料表存在，則會予以刪除。|
    |CREATE EXTERNAL TABLE|在 Hive 中建立**外部**資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。|
    |資料列格式|資料的格式化方式。 在此情況下，每個記錄中的欄位會以空格隔開。|
    |儲存為 TEXTFILE 位置|資料的儲存位置，以及何種檔案格式。|
    |SELECT|選取資料行**t4**包含 **[ERROR]** 值的所有資料列計數。 此查詢會傳回值 **3** ，因為有 3 個資料列包含此值。|
    |INPUT__FILE__NAME LIKE '% .log '|Hive 會嘗試將架構套用至目錄中的所有檔案。 在此情況下，目錄包含不符合架構的檔案。 若要防止結果中出現亂碼資料，此陳述式會告訴 Hive 只應該從檔名以 .log 結尾的檔案傳回資料。|

   > [!NOTE]  
   > 當您預期會由外部來源來更新基礎資料時，請使用外部資料表。 例如，自動化的資料上傳程序，或透過其他 MapReduce 作業。
   >
   > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。

    此命令的輸出類似下列文字：

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. 結束 Beeline：

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>執行 HiveQL 檔案

這個範例是先前範例中的接續。 使用下列步驟建立檔案，然後利用執行該檔案。

1. 使用以下命令，建立名為 **query.hql** 的檔案：

    ```bash
    nano query.hql
    ```

1. 使用下列文字做為檔案的內容。 此查詢將建立名為 **errorLogs** 的新「內部」資料表：

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    這些語句會執行下列動作：

    |引數 |描述 |
    |---|---|
    |CREATE TABLE （如果不存在）|如果資料表尚不存在，則會建立它。 因為未使用**EXTERNAL**關鍵字，所以這個語句會建立內部資料表。 內部資料表儲存在 Hive 資料倉儲中，並完全由 Hive 管理。|
    |儲存為 ORC|以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 ORC 格式是高度最佳化且有效率的 Hive 資料儲存格式。|
    |插入覆寫 .。。請|從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。|

    > [!NOTE]  
    > 與外部資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

1. 若要儲存檔案，請使用**Ctrl** + **X**，然後輸入**Y**，最後**輸入**。

1. 使用下列命令，以使用 Beeline 來執行檔案：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` 參數會啟動 Beeline 並執行 `query.hql` 檔案中的陳述式。 當查詢完成時，您會看到 `jdbc:hive2://headnodehost:10001/>` 提示字元。 您也可以使用 `-f` 參數執行檔案，它會在查詢完成後結束 Beeline。

1. 若要確認 **errorLogs** 資料表已建立，請使用下列陳述式傳回 **errorLogs** 的所有資料列：

    ```hiveql
    SELECT * from errorLogs;
    ```

    應該傳回三個資料列，且在資料行 t4 中全部包含 **[ERROR]** ：

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>後續步驟

* 如需 HDInsight 中 Hive 的一般資訊，請參閱[在 hdinsight 上搭配使用 Apache Hive 與 Apache Hadoop](hdinsight-use-hive.md)

* 如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊，請參閱[在 hdinsight 上搭配使用 MapReduce 與 Apache Hadoop](hdinsight-use-mapreduce.md)
