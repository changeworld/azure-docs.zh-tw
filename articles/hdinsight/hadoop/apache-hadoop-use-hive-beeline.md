---
title: 使用 Apache Beeline 搭配 Apache Hive - Azure HDInsight
description: 了解如何使用 Beeline 用戶端以 Hadoop on HDInsight 執行 Hive 查詢。 Beeline 是透過 JDBC 與 HiveServer2 搭配作業的公用程式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 13c51f0db468c1591ca29de17f1744752589a1c8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663740"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>使用 Apache Beeline 用戶端搭配 Apache Hive

了解如何使用 [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 在 HDInsight 上執行 Apache Hive 查詢。

Beeline 是 Hive 用戶端，隨附於您的 HDInsight 叢集的前端節點。 若要在本機安裝 Beeline，請參閱下方的[install Beeline client](#install-beeline-client)。 Beeline 會使用 JDBC 連線至 HiveServer2，它是裝載在 HDInsight 叢集上的服務。 您也可以使用 Beeline 透過網際網路從遠端存取 HDInsight 上的 Hive。 下列範例提供最常用來從 Beeline 連線至 HDInsight 的連接字串。

## <a name="types-of-connections"></a>連線類型

### <a name="from-an-ssh-session"></a>從 SSH 會話

從 SSH 會話連線到叢集前端節點時，您可以連接到埠 `10001`上的 `headnodehost` 位址：

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>透過 Azure 虛擬網路

透過 Azure 虛擬網路從用戶端連接到 HDInsight 時，您必須提供叢集前端節點的完整功能變數名稱（FQDN）。 由於此連線是直接連線到叢集節點，因此會使用連接埠 `10001` 進行連線：

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

將 `<headnode-FQDN>` 取代為叢集前端節點的完整功能變數名稱。 若要找出前端節點的完整網域名稱，請利用[使用 Apache Ambari REST API 管理 HDInsight](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 文件中的資訊。

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>使用 Kerberos 的 HDInsight 企業安全性套件（ESP）叢集

從用戶端連線企業安全性套件到叢集相同領域之電腦上的 Azure Active Directory （AAD）-DS 時，您也必須指定功能變數名稱 `<AAD-Domain>` 以及具有叢集存取權之網域使用者帳戶的名稱 `<username>`如下所示：

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

以網域上具備叢集存取權限的帳戶名稱取代 `<username>`。 將 `<AAD-DOMAIN>` 取代為叢集所加入 Azure Active Directory （AAD）的名稱。 請使用大寫字串作為 `<AAD-DOMAIN>` 值，否則找不到認證。 如有需要，請檢查 `/etc/krb5.conf` 的領域名稱。

---

### <a name="over-public-or-private-endpoints"></a>透過公用或私用端點

使用公用或私人端點連接到叢集時，您必須提供叢集登入帳戶名稱（預設 `admin`）和密碼。 例如，使用 Beeline 從用戶端系統連線到 `clustername.azurehdinsight.net` 位址。 此連接是透過埠 `443`進行，並使用 SSL 進行加密。

將 `clustername` 替換為 HDInsight 叢集的名稱。 將 `admin` 取代為叢集的叢集登入帳戶。 針對 ESP 叢集，請使用完整的 UPN （例如 user@domain.com）。 將 `password` 取代為叢集登入帳戶的密碼。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

或適用于私人端點：

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

私人端點會指向基本負載平衡器，只能從相同區域中的 Vnet 對等互連存取。 如需詳細資訊，請參閱[全域 VNet 對等互連的條件約束和負載平衡](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)器。 您可以使用 `curl` 命令搭配 `-v` 選項，針對公用或私用端點的任何連線問題進行疑難排解，然後再使用 beeline。

---

### <a name="use-beeline-with-apache-spark"></a>搭配 Apache Spark 使用 Beeline

Apache Spark 提供自己的 HiveServer2 (有時稱為 Spark Thrift 伺服器) 實作。 此服務會使用 Spark SQL 來解析查詢而不是 Hive，並可能提供更佳的效能 (視您的查詢而定)。

#### <a name="through-public-or-private-endpoints"></a>透過公用或私用端點

使用的連接字串稍有不同。 與其 `httpPath/sparkhive2`，而不是包含 `httpPath=/hive2`。 將 `clustername` 替換為 HDInsight 叢集的名稱。 將 `admin` 取代為叢集的叢集登入帳戶。 針對 ESP 叢集，請使用完整的 UPN （例如 user@domain.com）。 將 `password` 取代為叢集登入帳戶的密碼。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

或適用于私人端點：

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

私人端點會指向基本負載平衡器，只能從相同區域中的 Vnet 對等互連存取。 如需詳細資訊，請參閱[全域 VNet 對等互連的條件約束和負載平衡](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)器。 您可以使用 `curl` 命令搭配 `-v` 選項，針對公用或私用端點的任何連線問題進行疑難排解，然後再使用 beeline。

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>從叢集標頭或內部的 Azure 虛擬網路與 Apache Spark

直接從叢集前端節點，或是從 Azure 虛擬網路 (與 HDInsight 叢集相同) 內的資源進行連線時，應對 Spark Thrift 伺服器使用連接埠 `10002`，而非 `10001`。 下列範例顯示如何直接連接到前端節點：

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>範例的必要條件

* HDInsight 上的 Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。

* 請注意叢集主要儲存體的[URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 例如，Azure 儲存體的 `wasb://`、Azure Data Lake Storage Gen2 的 `abfs://`，或 `adl://` 的 Azure Data Lake Storage Gen1。 如果已啟用 Azure 儲存體的安全傳輸，則 URI 會 `wasbs://`。 如需詳細資訊，請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

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

    `!sql`，是用來執行 HiveQL 語句。 不過，HiveQL 如此常用，因此您可以省略前面的 `!sql`。 下列兩個陳述式是相等的：

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

    此資訊描述資料表中的資料行。

5. 輸入下列語句，以使用 HDInsight 叢集所提供的範例資料來建立名為**log4jLogs**的資料表：（根據您的[URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)，視需要修訂）。

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

    |帳單明細 |描述 |
    |---|---|
    |DROP TABLE|如果資料表存在，則會予以刪除。|
    |建立外部資料表|在 Hive 中建立**外部**資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。|
    |資料列格式|資料的格式化方式。 在此情況下，每個記錄中的欄位會以空格隔開。|
    |儲存為 TEXTFILE 位置|資料的儲存位置，以及何種檔案格式。|
    |SELECT|選取在資料行 **t4** 中包含 **[ERROR]** 值的所有資料列。 此查詢會傳回值 **3** ，因為有 3 個資料列包含此值。|
    |INPUT__FILE__NAME LIKE '% .log '|Hive 會嘗試將架構套用至目錄中的所有檔案。 在此情況下，目錄包含不符合架構的檔案。 若要防止結果中出現亂碼資料，此陳述式會告訴 Hive 只應該從檔名以 .log 結尾的檔案傳回資料。|

   > [!NOTE]  
   > 當您預期會由外部來源來更新基礎資料時，請使用外部資料表。 例如，自動化的資料上傳程序，或透過其他 MapReduce 作業。
   >
   > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。

    此命令的輸出類似下列文字：

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

6. 結束 Beeline：

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>執行 HiveQL 檔案

這是先前範例中的接續。 使用下列步驟建立檔案，然後利用執行該檔案。

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

    |帳單明細 |描述 |
    |---|---|
    |CREATE TABLE （如果不存在）|如果資料表尚不存在，則會建立它。 因為未使用**EXTERNAL**關鍵字，所以這個語句會建立內部資料表。 內部資料表儲存在 Hive 資料倉儲中，並完全由 Hive 管理。|
    |儲存為 ORC|以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 ORC 格式是高度最佳化且有效率的 Hive 資料儲存格式。|
    |插入覆寫 .。。請|從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。|

    > [!NOTE]  
    > 與外部資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

1. 若要儲存檔案，請使用**Ctrl**+**X**，然後輸入**Y**，最後**輸入**。

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

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>安裝 beeline 用戶端

雖然 Beeline 包含在 HDInsight 叢集的前端節點上，但您可能會想要將它安裝在本機電腦上。  在本機電腦上安裝 Beeline 的步驟是以[適用于 Linux 的 Windows 子系統](https://docs.microsoft.com/windows/wsl/install-win10)為基礎。

1. 更新封裝清單。 在您的 bash shell 中輸入下列命令：

    ```bash
    sudo apt-get update
    ```

1. 如果未安裝，請安裝 JAVA。 您可以使用 `which java` 命令來檢查。

    1. 如果未安裝任何 java 封裝，請輸入下列命令：

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. 開啟 .bashrc 檔案（通常位於 ~/.bashrc）： `nano ~/.bashrc`。

    1. 修改 .bashrc 檔案。 在檔案結尾處加入下列這一行︰

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        然後按下**Ctrl + X**，再按**Y**，然後按 enter。

1. 下載 Hadoop 和 Beeline 封存，輸入下列命令：

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. 將封存解壓縮後，輸入下列命令：

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. 進一步修改 .bashrc 檔。 您必須識別封存已解壓縮的路徑。 如果使用[適用于 Linux 的 Windows 子系統](https://docs.microsoft.com/windows/wsl/install-win10)，且您完全遵循這些步驟，您的路徑會是 `/mnt/c/Users/user/`，其中 `user` 是您的使用者名稱。

    1. 開啟檔案： `nano ~/.bashrc`

    1. 以適當的路徑修改下列命令，然後在 .bashrc 檔案的結尾輸入它們：

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. 然後按下**Ctrl + X**，再按**Y**，然後按 enter。

1. 關閉並重新開啟您的 bash 會話。

1. 測試您的連接。 使用上方[公用或私用端點](#over-public-or-private-endpoints)的連接格式。

## <a name="next-steps"></a>後續步驟

* 如需 HDInsight 中 Hive 的一般資訊，請參閱[在 hdinsight 上搭配使用 Apache Hive 與 Apache Hadoop](hdinsight-use-hive.md)

* 如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊，請參閱[在 hdinsight 上搭配使用 MapReduce 與 Apache Hadoop](hdinsight-use-mapreduce.md)
