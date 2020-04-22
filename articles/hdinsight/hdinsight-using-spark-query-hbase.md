---
title: 使用 Spark 以讀取及寫入 HBase 資料 - Azure HDInsight
description: 使用 Spark HBase Connector 將資料從 Spark 叢集讀取及寫入至 HBase 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 4f2e8b2a691a6b17b5ed075745d556db4e330535
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682470"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>使用 Apache Spark 來讀取和寫入 Apache HBase 資料

Apache HBase 通常會使用其低階 API (scan、get、put) 或者使用 Apache Phoenix 以 SQL 語法來查詢。 阿帕契還提供阿帕契火花HBase連接器。 連接器是查詢和修改 HBase 儲存的數據的一種方便且性能的替代方法。

## <a name="prerequisites"></a>Prerequisites

* 在同一[虛擬網路中](./hdinsight-plan-virtual-network-deployment.md)部署的兩個單獨的 HDInsight 叢集。 一個 HBase 和一個至少安裝了 Spark 2.1 (HDInsight 3.6) 的火花。 如需詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。

* 您叢集主要儲存體的 URI 配置。 此方案將針對 Azure Blob 存儲`abfs://`、Azure 資料存儲第 2 代或 Azure 數據存儲第 1 代adl://wasb://。 如果為 Blob 儲存啟用了安全傳輸`wasbs://`,則 URI 將為 。  另請參閱[安全傳輸](../storage/common/storage-require-secure-transfer.md)。

## <a name="overall-process"></a>整體程序

啟用您的 Spark 叢集以查詢 HDInsight 叢集的高階程序如下：

1. 在 HBase 中準備一些範例資料。
2. 從您的 HBase 叢集設定資料夾 (/etc/hbase/conf) 取得 hbase-site.xml 檔案。
3. 將 hbase-site.xml 的複本放置在您的 Spark 2 設定資料夾 (/etc/spark2/conf)。
4. 根據 `packages` 選項中的 Maven 座標，執行參照 Spark HBase Connector 的 `spark-shell`。
5. 定義目錄，該目錄將結構描述從 Spark 對應至 HBase。
6. 使用 RDD 或 DataFrame API 與 HBase 資料進行互動。

## <a name="prepare-sample-data-in-apache-hbase"></a>在 Apache HBase 中準備範例資料

在此步驟中,您將在 Apache HBase 中創建和填充一個表,然後可以使用 Spark 進行查詢。

1. 使用`ssh`該命令連接到 HBase 群集。 使用 HBase`HBASECLUSTER`叢集 的名稱取代編輯下面的指令,然後輸入以下命令:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. 使用`hbase shell`指令啟動 HBase 互動式 shell。 在您的 SSH 連線中輸入下列命令：

    ```bash
    hbase shell
    ```

3. 使用`create`命令建立具有兩列族的 HBase 表。 輸入下列命令：

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. 使用`put`指令在特定表中指定列中的指定列插入值。 輸入下列命令：

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

5. 使用`exit`命令停止 HBase 互動式外殼。 輸入下列命令：

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>將 hbase 網站.xml 複製到 Spark 群集

將 hbasesite.xml 從本地儲存複製到 Spark 群集預設儲存的根目錄。  編輯下面的命令以反映您的配置。  然後,從打開的 SSH 工作階段到 HBase 群集,輸入命令:

| 語法值 | 新值|
|---|---|
|[URI 配置](hdinsight-hadoop-linux-information.md#URI-and-scheme) | 修改以反映您的存儲。  以下語法用於啟用安全傳輸的 Blob 儲存。|
|`SPARK_STORAGE_CONTAINER`|替換為用於Spark群集的預設儲存容器名稱。|
|`SPARK_STORAGE_ACCOUNT`|替換為用於 Spark 群集的預設存儲帳戶名稱。|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

然後退出與 HBase 群集的連接。

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>將 hbase-site.xml 放置在您的 Spark 叢集

1. 使用 SSH 連線到 Spark 叢集的前端節點。 使用 Spark 叢集`SPARKCLUSTER`的名稱 取代編輯下面的指令,然後輸入以下命令:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. 輸入以下指令,從`hbase-site.xml`Spark 群組的預設儲存複製到叢集本地儲存上的 Spark 2 設定資料夾:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>執行參照 Spark HBase Connector 的 Spark Shell

1. 從打開的 SSH 工作階段到 Spark 群集,請輸入下面的命令以啟動火花殼:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. 讓此 Spark Shell 執行個體保持開啟，並且繼續進行下一個步驟。

## <a name="define-a-catalog-and-query"></a>定義目錄和查詢

在這個步驟中，您會定義目錄物件，該目錄物件將結構描述從 Apache Spark 對應至 Apache HBase。  

1. 在開啟的 Spark 外殼`import`中, 輸入以下語句:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. 輸入下面的指令,為在 HBase 中建立的「連絡人」表定義目錄:

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

    該程式碼執行以下操作:  

     a. 針對名為 `Contacts` 的 HBase 資料表定義目錄結構描述。  
     b. 將 rowkey 識別為 `key`，並且將 Spark 中使用的資料行名稱對應至 HBase 中使用的資料行系列、資料行名稱以及資料行類型。  
     c. rowkey 也必須詳細定義為具名資料行 (`rowkey`)，其具有 `rowkey` 的特定資料行系列 `cf`。  

1. 輸入下面的指令以定義在 HBase`Contacts`中 以表提供資料的格格的方法:

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

1. 通過輸入以下命令關閉火花殼:

    ```scala
    :q
    ```

## <a name="next-steps"></a>後續步驟

* [Apache Spark HBase Connector](https://github.com/hortonworks-spark/shc)
