---
title: 使用 psql 來大量載入至 Apache Phoenix - Azure HDInsight
description: 使用 psql 工具，將大量載入資料載入 Azure HDInsight 的 Apache Phoenix 資料表中
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d56c9b33d53be0e30769aef8934a2dbf43d06867
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944843"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>使用 psql 將資料大量載入至 Apache Phoenix

[Apache Phoenix](https://phoenix.apache.org/) \(英文\) 是一個建置在 [Apache HBase](../hbase/apache-hbase-overview.md) 上的開放原始碼、大規模平行關聯式資料庫。 Phoenix 可透過 HBase 提供類似 SQL 的查詢。 Phoenix 使用 JDBC 驅動程式，可讓使用者建立、刪除及更改 SQL 資料表、索引、檢視表和序列，以及個別或大量更新插入資料列。 Phoenix 使用 noSQL 原生編譯而不是使用 MapReduce 來編譯查詢，可在 HBase 上建立低延遲的應用程式。 Phoenix 新增了協同處理器，可支援在伺服器的位址空間中執行用戶端提供的程式碼，執行與資料共置的程式碼。 這可將用戶端/伺服器資料傳輸降到最低。  若要在 HDInsight 中使用 Phoenix 來處理資料，請先建立資料表，然後將資料載入至這些資料表。

## <a name="bulk-loading-with-apache-phoenix"></a>使用 Apache Phoenix 進行大量載入

有多個可將資料載入至 HBase 的方法，包括使用用戶端 API、搭配 TableOutputFormat 的 MapReduce 作業，或使用 HBase 殼層來手動輸入資料。 Phoenix 提供兩個可將 CSV 資料載入至 Phoenix 資料表的方法：一個名為 `psql` 的用戶端載入工具，以及一個以 MapReduce 為基礎的大量載入工具。

`psql` 工具是單一執行緒工具，最適用於載入數 MB 或 GB 的資料。 所有要載入的 CSV 檔案都必須具有 '.csv' 副檔名。  您也可以在 `psql` 命令列中指定含有 '.sql' 副檔名的 SQL 指令碼檔。

使用 MapReduce 進行的大量載入會用於規模大許多的資料磁碟區，通常是在生產環境案例中使用，因為 MapReduce 會使用多個執行緒。

在您開始載入資料之前，請先確認已啟用 Phoenix 且查詢逾時設定與預期的一樣。  存取 HDInsight 叢集 [Apache Ambari](https://ambari.apache.org/) 儀表板，選取 [HBase]，然後選取 [設定] 索引標籤。 向下滾動，確認 Apache Phoenix 設定為， `enabled` 如下所示：

![Apache Phoenix HDInsight 叢集設定](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>使用 `psql` 來大量載入資料表

1. 建立名為的檔案 `createCustomersTable.sql` ，並將下列程式碼複製到檔案中。 然後儲存並關閉檔案。

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. 建立名為的檔案 `listCustomers.sql` ，並將下列程式碼複製到檔案中。 然後儲存並關閉檔案。

    ```sql
    SELECT * from Customers;
    ```

1. 建立名為的檔案 `customers.csv` ，並將下列程式碼複製到檔案中。 然後儲存並關閉檔案。

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. 建立名為的檔案 `customers2.csv` ，並將下列程式碼複製到檔案中。 然後儲存並關閉檔案。

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. 開啟命令提示字元，並將目錄變更為新建立之檔案的位置。 將下方的 CLUSTERNAME 取代為您 HBase 叢集的實際名稱。 然後執行程式碼，將檔案上傳至叢集的前端節點：

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)來連線到您的叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 從 ssh 會話，將目錄變更為 **psql** 工具的位置。 執行下列命令：

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. 大量載入資料。 下列程式碼會建立 **Customers** 資料表，然後上傳資料。

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    作業 `psql` 完成之後，您應該會看到類似下面的訊息：

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. 您可以繼續使用 `psql` 以查看 Customers 資料表的內容。 執行下列程式碼：

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    或者，您也可以使用 [HBase shell](./query-hbase-with-hbase-shell.md)或 [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) 來查詢資料。

1. 上傳額外的資料。 現在資料表已經存在，此命令會指定資料表。 執行下列命令：

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>使用 MapReduce 來大量載入資料表

若要進行遍佈整個叢集的更高輸送量載入，請使用 MapReduce 載入工具。 此載入器會先將所有資料轉換成 HFile，然後將所建立的 HFile 提供給 HBase。

1. 本節會繼續進行 ssh 會話，以及稍早建立的物件。 使用上述步驟，視需要建立 **Customers** 資料表和 **customers.csv** 檔案。 如有必要，請重新建立您的 ssh 連接。

1. 截斷 **Customers** 資料表的內容。 從開啟的 ssh 會話中，執行下列命令：

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. 從前端節點將檔案複製 `customers.csv` 到 Azure 儲存體。

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. 變更至 MapReduce 大量載入命令的執行目錄：

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. 使用 `hadoop` 命令搭配 Phoenix 用戶端 Jar 來啟動 CSV MapReduce 載入器：

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    上傳完成後，您應該會看到類似下面的訊息：

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. 若要搭配使用 MapReduce 與 Azure Data Lake Storage，請找出 Azure Data Lake Storage 根目錄，即 `hbase-site.xml` 中的 `hbase.rootdir` 值。 在下列命令中，Data Lake Storage 根目錄是 `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`。 在此命令中，指定 Data Lake Storage 輸入和輸出資料夾作為參數：

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. 若要查詢和查看資料，您可以使用先前所述的 **psql** 。 您也可以使用 [HBase shell](./query-hbase-with-hbase-shell.md)或 [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md)。

## <a name="recommendations"></a>建議

* 對輸入和輸出資料夾使用相同的儲存媒體 (Azure 儲存體 (WASB) 或 Azure Data Lake Storage (ADL))。 若要從 Azure 儲存體傳輸資料至 Data Lake Storage，您可以使用 `distcp` 命令：

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* 使用更大型的背景工作節點。 MapReduce 大量複製程序的 map 程序會產生大量的暫時輸出，這會填滿可用的非 DFS 空間。 若要進行大量的大量載入，請使用更多且更大型的背景工作節點。 您配置給叢集的背景工作節點數目會直接影響處理速度。

* 將輸入檔切割成大約 10 GB 的區塊。 大量載入是一個需要大量儲存體的作業，因此將輸入檔分割成多個區塊可以提升效能。

* 避免產生區域伺服器作用區。 如果您的資料列索引鍵以單純方式遞增，HBase 循序寫入可能會導致產生區域伺服器作用區。 對資料列索引鍵進行 *Salt* 處理將可減少循序寫入次數。 Phoenix 提供一個可在背景中以 Salt 位元組對特定資料表的資料列索引鍵進行 Salt 處理的方法，如以下參考資料所述。

## <a name="next-steps"></a>後續步驟

* [使用 Apache Phoenix 來進行大量資料載入](https://phoenix.apache.org/bulk_dataload.html) \(英文\)
* [在 HDInsight 中搭配 Linux 型 Apache HBase 叢集使用 Apache Phoenix](../hbase/apache-hbase-query-with-phoenix.md)
* [以 Salt 處理的資料表](https://phoenix.apache.org/salted.html)
* [Apache Phoenix 文法](https://phoenix.apache.org/language/index.html)
