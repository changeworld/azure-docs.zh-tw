---
title: 使用 Apache Beeline 搭配 Apache Hive - Azure HDInsight
description: 了解如何使用 Beeline 用戶端以 Hadoop on HDInsight 執行 Hive 查詢。 Beeline 是透過 JDBC 與 HiveServer2 搭配作業的公用程式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 4f2b192765aab4c7cf18c62988ae2f6080b4b17c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436898"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>使用 Apache Beeline 用戶端搭配 Apache Hive

了解如何使用 [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 在 HDInsight 上執行 Apache Hive 查詢。

Beeline 是 Hive 用戶端，隨附於您的 HDInsight 叢集的前端節點。 要在本地安裝蜂線,請參閱[安裝蜂線用戶端](#install-beeline-client),如下所示。 Beeline 會使用 JDBC 連線至 HiveServer2，它是裝載在 HDInsight 叢集上的服務。 您也可以使用 Beeline 透過網際網路從遠端存取 HDInsight 上的 Hive。 以下範例提供用於從 Beeline 連接到 HDInsight 的最常見連接字串。

## <a name="types-of-connections"></a>連線類型

### <a name="from-an-ssh-session"></a>從 SSH 工作階段

從 SSH 工作階段連線到叢集頭節點時,可以連接到`headnodehost``10001`連接埠 上的位址:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>透過 Azure 虛擬網路

通過 Azure 虛擬網路從用戶端連接到 HDInsight 時,必須提供群集頭節點的完全限定功能變數名稱 (FQDN)。 由於此連線是直接連線到叢集節點，因此會使用連接埠 `10001` 進行連線：

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

替換為`<headnode-FQDN>`群集頭節點的完全限定的功能變數名稱。 若要找出前端節點的完整網域名稱，請利用[使用 Apache Ambari REST API 管理 HDInsight](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 文件中的資訊。

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>使用 Kerberos 向 HDInsight 企業安全套件 (ESP) 叢集

當從客戶端連接到在群組同一區域的電腦上連接到 Azure 活動目錄 (AAD)-DS) 的企業安全包 (ESP) 叢集時,還`<AAD-Domain>``<username>`必須指定具有存取群集 許可權的網域使用者帳戶的網域名稱和名稱:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

以網域上具備叢集存取權限的帳戶名稱取代 `<username>`。 替換為`<AAD-DOMAIN>`群集加入的 Azure 活動目錄 (AAD) 的名稱。 使用`<AAD-DOMAIN>`值大寫字串,否則將找不到憑據。 如果需要`/etc/krb5.conf`,請檢查功能變數名稱。

要從安巴里找到 JDBC URL:

1. 從 Web 瀏覽器`https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`導航`CLUSTERNAME`到 , 群集的名稱在哪裡。 確保蜂巢伺服器 2 正在運行。

1. 使用剪貼簿複製 HiveServer2 JDBC URL。

---

### <a name="over-public-or-private-endpoints"></a>在公共或專用終結點上

使用公共終結點或專用終結點連接到群集時,必須提供群集登錄帳戶名稱(預設`admin`)和密碼。 例如，使用 Beeline 從用戶端系統連線到 `clustername.azurehdinsight.net` 位址。 此連接透過連接埠`443`進行,並使用 TLS/SSL 進行加密。

將 `clustername` 替換為 HDInsight 叢集的名稱。 將 `admin` 取代為叢集的叢集登入帳戶。 對於 ESP 群集,請使用完整的 UPN(例如user@domain.com, 將 `password` 取代為叢集登入帳戶的密碼。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

或專用終結點:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

專用終結點指向基本負載均衡器,該平衡器只能從同一區域中對等 VNET 訪問。 有關詳細資訊[,請參閱全域 VNet 對等互連和負載均衡器的約束](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。 在使用蜂線`curl`之前`-v`, 可以使用 該命令選項解決公共或專用終結點的任何連接問題。

---

### <a name="use-beeline-with-apache-spark"></a>使用 Beeline 搭配 Apache Spark

Apache Spark 提供自己的 HiveServer2 (有時稱為 Spark Thrift 伺服器) 實作。 此服務會使用 Spark SQL 來解析查詢而不是 Hive，並可能提供更佳的效能 (視您的查詢而定)。

#### <a name="through-public-or-private-endpoints"></a>通過公共或專用終結點

使用的連接字串略有不同。 而不是包含`httpPath=/hive2`它`httpPath/sparkhive2`。 將 `clustername` 替換為 HDInsight 叢集的名稱。 將 `admin` 取代為叢集的叢集登入帳戶。 對於 ESP 群集,請使用完整的 UPN(例如user@domain.com, 將 `password` 取代為叢集登入帳戶的密碼。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

或專用終結點:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

專用終結點指向基本負載均衡器,該平衡器只能從同一區域中對等 VNET 訪問。 有關詳細資訊[,請參閱全域 VNet 對等互連和負載均衡器的約束](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。 在使用蜂線`curl`之前`-v`, 可以使用 該命令選項解決公共或專用終結點的任何連接問題。

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>從群集頭或 Azure 虛擬網路內部使用 Apache Spark

直接從叢集前端節點，或是從 Azure 虛擬網路 (與 HDInsight 叢集相同) 內的資源進行連線時，應對 Spark Thrift 伺服器使用連接埠 `10002`，而非 `10001`。 下面的範例簡報如何直接連接到頭節點:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>範例的必要條件

* HDInsight 上的 Hadoop 群集。 請參閱[在 Linux 上開始使用 HDInsight。](./apache-hadoop-linux-tutorial-get-started.md)

* 請注意叢集家儲存的[URI 專案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 例如,`wasb://`對於 Azure 儲存`abfs://`、Azure`adl://`資料湖儲存第 2 代或 Azure 數據湖儲存第 1 代。 如果為 Azure 儲存啟用了安全傳輸`wasbs://`,則 URI 為 。 有關詳細資訊,請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

* 選項 1:SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。 此文件中的大多數步驟假定您使用的是從 SSH 會話到群集的 Beeline。

* 選項 2:本地蜂線用戶端。

## <a name="run-a-hive-query"></a>執行 HIVE 查詢

此示例基於使用 SSH 連接中的 Beeline 用戶端。

1. 使用以下代碼打開與群集的 SSH 連接。 將 `sshuser` 取代為叢集的 SSH 使用者，並將 `CLUSTERNAME` 取代為叢集的名稱。 出現提示時,輸入 SSH 使用者帳戶的密碼。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 以輸入以下指令,從開啟的 SSH 工作階段與蜂林用戶端連接到 HiveServer2:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline 命令以 `!` 字元開頭，例如 `!help` 顯示說明。 不過，一些命令可以省略 `!`。 例如，`help` 也能運作。

    用於`!sql`執行 HiveQL 語句的 。 不過，HiveQL 如此常用，因此您可以省略前面的 `!sql`。 下列兩個陳述式是相等的：

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

5. 輸入以下語句,使用 HDInsight 群集提供的範例資料創建名為**log4jLogs**的表:(根據需要根據[URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)進行修訂。

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

    這些文句執行以下操作:

    |引數 |描述 |
    |---|---|
    |DROP TABLE|如果表存在,則將其刪除。|
    |建立外部表|在蜂巢中創建**外部**表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。|
    |列格式|數據的格式設置方式。 在此情況下，每個記錄中的欄位會以空格隔開。|
    |儲存為文字檔案位置|資料儲存的位置和檔案格式。|
    |SELECT|選擇列**t4**包含值 **[ERROR]** 的所有行的計數。 此查詢會傳回值 **3** ，因為有 3 個資料列包含此值。|
    |INPUT__FILE__NAME喜歡".log"|Hive 嘗試將架構應用於目錄中的所有檔。 在這種情況下,目錄包含與架構不匹配的檔。 若要防止結果中出現亂碼資料，此陳述式會告訴 Hive 只應該從檔名以 .log 結尾的檔案傳回資料。|

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

6. 離開蜜蜂線:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>執行 HiveQL 檔案

這是上一個示例的延續。 使用下列步驟建立檔案，然後利用執行該檔案。

1. 使用以下命令，建立名為 **query.hql** 的檔案：

    ```bash
    nano query.hql
    ```

1. 使用下列文字做為檔案的內容。 此查詢將建立名為 **errorLogs** 的新「內部」資料表：

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    這些文句執行以下操作:

    |引數 |描述 |
    |---|---|
    |建立表(如果不存在)|如果表不存在,則創建該表。 由於不使用**外部**關鍵字,因此此語句將創建一個內部表。 內部資料表儲存在 Hive 資料倉儲中，並完全由 Hive 管理。|
    |儲存為 ORC|以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 ORC 格式是高度最佳化且有效率的 Hive 資料儲存格式。|
    |插入覆寫...選擇|從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。|

    > [!NOTE]  
    > 與外部資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

1. 要儲存檔案,請使用**Ctrl**+**X**,然後輸入**Y,** 最後**輸入**。

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

## <a name="install-beeline-client"></a>安裝蜂線客戶端

儘管 Beeline 包含在 HDInsight 群集的頭部節點上,但您可能希望將其安裝在本地電腦上。  下面在本地電腦上安裝 Beeline 的步驟基於[Linux 的 Windows 子系統](https://docs.microsoft.com/windows/wsl/install-win10)。

1. 更新包清單。 在 bash shell 中輸入以下指令:

    ```bash
    sudo apt-get update
    ```

1. 如果未安裝,請安裝 Java。 您可以檢查該`which java`命令。

    1. 如果未安裝 Java 套件,請輸入以下指令:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. 開啟 bashrc 檔案(通常位於 */.bashrc`nano ~/.bashrc`中): 。

    1. 修改 bashrc 檔。 在檔案結尾處加入下列這一行︰

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        然後按**Ctrl+X**,然後**按 Y**,然後輸入。

1. 下載 Hadoop 和 Beeline 檔,輸入以下指令:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. 解壓縮封檔,輸入以下命令:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. 進一步修改 bashrc 檔。 您需要確定存檔解壓縮位置的路徑。 如果使用[Windows 子系統進行 Linux](https://docs.microsoft.com/windows/wsl/install-win10),並且您完全按照這些步驟執行`/mnt/c/Users/user/`,`user`則您的路徑 將是 ,您的使用者名在哪裡。

    1. 開啟檔案:`nano ~/.bashrc`

    1. 使用適當的路徑修改下面的命令,然後在 bashrc 檔案末尾輸入這些命令:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. 然後按**Ctrl+X**,然後**按 Y**,然後輸入。

1. 關閉,然後重新打開 bash 會話。

1. 測試您的連接。 使用[上面的「過公共或專用終結點](#over-public-or-private-endpoints)」的連接格式。

## <a name="next-steps"></a>後續步驟

* 有關 HDInsight 中的 Hive 的更一般資訊,請參閱[在 HDInsight 上使用阿帕奇蜂巢和 Apache Hadoop](hdinsight-use-hive.md)

* 有關在 HDInsight 上使用 Hadoop 的其他方法的詳細資訊,請參閱[在 HDInsight 上使用 Map 減少與 Apache Hadoop](hdinsight-use-mapreduce.md)
