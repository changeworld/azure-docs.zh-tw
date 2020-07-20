---
title: 使用 Spark 以讀取及寫入 HBase 資料 - Azure HDInsight
description: 使用 Spark HBase Connector 將資料從 Spark 叢集讀取及寫入至 HBase 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 3ddb8734a3d15a6cd5f4a43ee069d6364f7523ed
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087480"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>使用 Apache Spark 來讀取和寫入 Apache HBase 資料

Apache HBase 通常會使用其低階 API (scan、get、put) 或者使用 Apache Phoenix 以 SQL 語法來查詢。 Apache 也提供 Apache Spark HBase 連接器。 連接器是查詢及修改 HBase 所儲存資料的方便且高效能的替代方案。

## <a name="prerequisites"></a>必要條件

* 在相同的[虛擬網路](./hdinsight-plan-virtual-network-deployment.md)中部署兩個不同的 HDInsight 叢集。 一個 HBase，以及至少已安裝 Spark 2.1 （HDInsight 3.6）的一個 Spark。 如需詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。

* 您叢集主要儲存體的 URI 配置。 針對 `abfs://` Azure Data Lake Storage Gen1 的 Azure Data Lake Storage Gen2 或 adl://Azure Blob 儲存體，此配置會 wasb://。 如果已啟用 Blob 儲存體的安全傳輸，則 URI 會是 `wasbs://` 。  另請參閱[安全傳輸](../storage/common/storage-require-secure-transfer.md)。

## <a name="overall-process"></a>整體程序

啟用您的 Spark 叢集以查詢 HDInsight 叢集的高階程序如下：

1. 在 HBase 中準備一些範例資料。
2. 從您的 HBase 叢集設定資料夾 (/etc/hbase/conf) 取得 hbase-site.xml 檔案。
3. 將 hbase-site.xml 的複本放置在您的 Spark 2 設定資料夾 (/etc/spark2/conf)。
4. 根據 `packages` 選項中的 Maven 座標，執行參照 Spark HBase Connector 的 `spark-shell`。
5. 定義目錄，該目錄將結構描述從 Spark 對應至 HBase。
6. 使用 RDD 或 DataFrame API 與 HBase 資料進行互動。

## <a name="prepare-sample-data-in-apache-hbase"></a>在 Apache HBase 中準備範例資料

在此步驟中，您會在 Apache HBase 中建立並填入資料表，然後您就可以使用 Spark 來進行查詢。

1. 使用 `ssh` 命令連接到您的 HBase 叢集。 以 HBase 叢集的名稱取代來編輯下面的命令 `HBASECLUSTER` ，然後輸入命令：

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. 使用 `hbase shell` 命令來啟動 HBase 互動式 shell。 在您的 SSH 連線中輸入下列命令：

    ```bash
    hbase shell
    ```

3. 使用 `create` 命令來建立具有兩個數據行系列的 HBase 資料表。 輸入下列命令：

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. 您 `put` 可以使用命令，在特定資料表的指定資料列中的指定資料行插入值。 輸入下列命令：

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. 使用 `exit` 命令來停止 HBase 互動式 shell。 輸入下列命令：

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>將 hbase-site.xml 複製到 Spark 叢集

將 hbase-site.xml 從本機儲存體複製到 Spark 叢集預設儲存體的根目錄。  編輯下列命令以反映您的設定。  然後，從您開啟的 SSH 會話到 HBase 叢集，輸入下列命令：

| 語法值 | 新值|
|---|---|
|[URI 配置](hdinsight-hadoop-linux-information.md#URI-and-scheme) | 修改以反映您的儲存體。  以下語法適用于已啟用安全傳輸的 blob 儲存體。|
|`SPARK_STORAGE_CONTAINER`|以用於 Spark 叢集的預設儲存體容器名稱取代。|
|`SPARK_STORAGE_ACCOUNT`|以用於 Spark 叢集的預設儲存體帳戶名稱取代。|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

然後結束您與 HBase 叢集的 ssh 連線。

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>將 hbase-site.xml 放置在您的 Spark 叢集

1. 使用 SSH 連線到 Spark 叢集的前端節點。 藉由將取代為您的 Spark 叢集名稱來編輯下面的命令 `SPARKCLUSTER` ，然後輸入命令：

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. 輸入下列命令， `hbase-site.xml` 從 spark 叢集的預設儲存體複製到叢集本機儲存體上的 spark 2 設定資料夾：

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>執行參照 Spark HBase Connector 的 Spark Shell

完成上述步驟之後，您應該能夠執行 Spark shell，並參考 Spark HBase 連接器的適當版本。 若要尋找適用于您叢集案例的最新適當 Spark HBase 連接器核心版本，請參閱[SHC core 存放庫](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/)。

例如，下表列出 HDInsight 小組目前使用的兩個版本和對應的命令。 如果 HBase 和 Spark 的版本與資料表中所示相同，您可以針對您的叢集使用相同的版本。 


1. 在您對 Spark 叢集開啟的 SSH 會話中，輸入下列命令以啟動 Spark shell：

    |Spark 版本| HDI HBase 版本  | SHC 版本    |  Command  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3.6 （HBase 1.1） | 1.1.0.3.1.2.2-1    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2.4    | HDI 4.0 （HBase 2.0） | 1.1.1-2.1-s_2. 11  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. 讓此 Spark shell 實例保持開啟，並繼續[定義目錄和查詢](#define-a-catalog-and-query)。 如果您在 SHC Core 存放庫中找不到與您的版本對應的 jar，請繼續閱讀。 

您可以直接從[spark-hbase-connector](https://github.com/hortonworks-spark/shc) GitHub 分支建立 jar。 例如，如果您正在使用 Spark 2.3 和 HBase 1.1 執行，請完成下列步驟：

1. 複製儲存機制：

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. 移至分支-2.3：

    ```bash
    git checkout branch-2.3
    ```

3. 從分支組建（建立 .jar 檔案）：

    ```bash
    mvn clean package -DskipTests
    ```
    
3. 執行下列命令（請務必變更對應于您所建立之 .jar 檔案的 .jar 名稱）：

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. 讓此 Spark shell 實例保持開啟，然後繼續進行下一節。 



## <a name="define-a-catalog-and-query"></a>定義目錄和查詢

在這個步驟中，您會定義目錄物件，該目錄物件將結構描述從 Apache Spark 對應至 Apache HBase。  

1. 在您的 open Spark Shell 中，輸入下列 `import` 語句：

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. 輸入下列命令，為您在 HBase 中建立的 [連絡人] 資料表定義目錄：

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    程式碼：  

    1. 定義名為之 HBase 資料表的目錄架構 `Contacts` 。  
    1. 將 rowkey 識別為 `key` ，並將 Spark 中使用的資料行名稱對應至資料行系列、資料行名稱，以及 HBase 中使用的資料行類型。  
    1. 將詳細 rowkey 定義為命名資料行（ `rowkey` ），其具有的特定資料行系列 `cf` `rowkey` 。  

1. 輸入下列命令以定義方法，以 `Contacts` 在 HBase 中提供資料表的資料框架：

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. 建立 DataFrame 的執行個體：

    ```scala
    val df = withCatalog(catalog)
    ```  

1. 查詢 DataFrame：

    ```scala
    df.show()
    ```

    您應該會看到兩個資料列的資料：

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. 註冊暫存資料表，以便使用 Spark SQL 查詢 HBase 資料表：

    ```scala
    df.createTempView("contacts")
    ```

1. 根據 `contacts` 資料表發出 SQL 查詢：

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    您應該會看到如下的結果：

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>插入新資料

1. 若要插入新的連絡人記錄，請定義 `ContactRecord` 類別：

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. 建立 `ContactRecord` 的執行個體並將其放置在陣列中：

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. 將新資料的陣列儲存至 HBase：

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. 檢查結果︰

    ```scala  
    df.show()
    ```

    您應該會看到如下的輸出：

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. 輸入下列命令以關閉 spark shell：

    ```scala
    :q
    ```

## <a name="next-steps"></a>下一步

* [Apache Spark HBase Connector](https://github.com/hortonworks-spark/shc)
