---
title: Apache Spark & Hive-Hive 倉儲連接器-Azure HDInsight
description: 瞭解如何在 Azure HDInsight 上將 Apache Spark 和 Apache Hive 與 Hive 倉儲連接器整合。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252413"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>將 Apache Spark 和 Apache Hive 與 Hive 倉儲連接器整合

Apache Hive 倉儲連接器（HWC）是一種程式庫，可讓您更輕鬆地使用 Apache Spark 和 Apache Hive，方法是支援在 Spark 資料框架和 Hive 資料表之間移動資料，以及將 Spark 串流資料導向 Hive 資料表的工作。 Hive 倉儲連接器的運作方式類似于 Spark 和 Hive 之間的橋樑。 它支援 Scala、JAVA 和 Python 以進行開發。

Hive 倉儲連接器可讓您利用 Hive 和 Spark 的獨特功能，來建立功能強大的大型資料應用程式。 Apache Hive 提供不可部分完成、一致、隔離且持久（ACID）的資料庫交易支援。 如需 Hive 中 ACID 和交易的詳細資訊，請參閱[Hive 交易](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)。 Hive 也會透過 Apache Ranger 提供詳細的安全性控制，且 Apache Spark 不提供低延遲分析處理。

Apache Spark，具有結構化串流 API，提供 Apache Hive 中無法使用的串流功能。 從 HDInsight 4.0 開始，Apache Spark 2.3.1 和 Apache Hive 3.1.0 有個別的中繼存放區，這可能會使互通性變得很棘手。 Hive 倉儲連接器可讓您更輕鬆地搭配使用 Spark 和 Hive。 HWC 程式庫會以平行方式將 LLAP 守護程式中的資料載入 Spark 執行程式，使其更有效率且更具擴充性，而不是使用從 Spark 到 Hive 的標準 JDBC 連接。

![hive 倉儲連接器架構](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive 倉儲連接器所支援的部分作業包括：

* 描述資料表
* 建立 ORC 格式資料的資料表
* 選取 Hive 資料並抓取資料框架
* 在 batch 中將資料框架寫入 Hive
* 執行 Hive update 語句
* 從 Hive 讀取資料表資料、在 Spark 中轉換它，並將它寫入至新的 Hive 資料表
* 使用 HiveStreaming 將資料框架或 Spark 串流寫入 Hive

## <a name="hive-warehouse-connector-setup"></a>Hive 倉儲連接器設定

請遵循下列步驟，在 Azure HDInsight 中的 Spark 和互動式查詢叢集之間設定 Hive 倉儲連接器：

### <a name="create-clusters"></a>建立叢集

1. 建立具有儲存體帳戶和自訂 Azure 虛擬網路的 HDInsight Spark **4.0**叢集。 如需在 Azure 虛擬網路中建立叢集的詳細資訊，請參閱[將 HDInsight 新增至現有的虛擬網路](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)。

1. 使用與 Spark 叢集相同的儲存體帳戶和 Azure 虛擬網路，建立 HDInsight 互動式查詢（LLAP） **4.0**叢集。

### <a name="modify-hosts-file"></a>修改 hosts 檔案

從互動式查詢叢集 headnode0 上的 `/etc/hosts` 檔案複製節點資訊，並將資訊串連至 Spark 叢集 headnode0 上的 `/etc/hosts` 檔案。 此步驟可讓您的 Spark 叢集解析互動式查詢叢集中的節點 IP 位址。 使用 `cat /etc/hosts`來查看已更新檔案的內容。 最後的輸出應該看起來像下面螢幕擷取畫面中所顯示的內容。

![hive 倉儲連接器主控檔案](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>收集初步資訊

#### <a name="from-your-interactive-query-cluster"></a>從您的互動式查詢叢集中

1. 使用 `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` 流覽至叢集的 [Apache Ambari Hive] 頁面，其中 `LLAPCLUSTERNAME` 是互動式查詢叢集的名稱。

1. 流覽至**Advanced** > **General** > **中繼存放區. uri**並記下值。 此值可能類似于： `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`。

1. 流覽至**advanced** > **advanced hive-site** > **zookeeper. 仲裁**並記下值。 此值可能類似于： `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`。

#### <a name="from-your-apache-spark-cluster"></a>從您的 Apache Spark 叢集

1. 使用 `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` 流覽至叢集的 [Apache Ambari Hive] 頁面，其中 `SPARKCLUSTERNAME` 是 Apache Spark 叢集的名稱。

1. 流覽至**advanced** > **advanced hive-interactive** > **hive. llap** ，並記下值。 此值可能類似于： `@llap0`。

### <a name="configure-spark-cluster-settings"></a>設定 Spark 叢集設定

從您的 Spark Ambari web UI，流覽至 Spark2 ** > **  > **自訂 Spark2 的 [預設值**]。

![Apache Ambari Spark2 configuration](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

視需要選取 [**新增屬性**]，以新增/更新下列各項：

| Key | 值 |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|您先前從**hive. llap**取得的值。|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`第 1 課：建立 Windows Azure 儲存體物件{2}。 設定為 JDBC 連接字串，它會連接到互動式查詢叢集上的 Hiveserver2。 以互動式查詢叢集的名稱取代 `LLAPCLUSTERNAME`。 將 `PWD` 取代為實際密碼。|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`第 1 課：建立 Windows Azure 儲存體物件{2}。 將設定為適當的 HDFS 相容臨時目錄。 如果您有兩個不同的叢集，則臨時目錄應該是 LLAP 叢集之儲存體帳戶的預備目錄中的資料夾，讓 HiveServer2 有其存取權。  將 `STORAGE_ACCOUNT_NAME` 取代為叢集所使用的儲存體帳戶名稱，並以儲存體容器的名稱 `STORAGE_CONTAINER_NAME`。|
|`spark.datasource.hive.warehouse.metastoreUri`|您先前從**hive. 中繼存放區**取得的值。|
|`spark.security.credentials.hiveserver2.enabled`|適用于 YARN 用戶端部署模式的 `false`。|
|`spark.hadoop.hive.zookeeper.quorum`|您先前從**hive. zookeeper**取得的值。|

儲存變更並視需要重新開機元件。

## <a name="using-the-hive-warehouse-connector"></a>使用 Hive 倉儲連接器

### <a name="connecting-and-running-queries"></a>連接和執行查詢

您可以選擇幾種不同的方法來連接到您的互動式查詢叢集，並使用 Hive 倉儲連接器執行查詢。 支援的方法包括下列工具：

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* Spark-提交
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

本文中提供的所有範例都是透過 spark shell 來執行。

若要啟動 spark shell 會話，請執行下列步驟：

1. 透過 SSH 連線到您 Apache Spark 叢集的前端節點。 如需使用 SSH 連線到您的叢集的詳細資訊，請參閱[使用 ssh 連線到 HDInsight （Apache Hadoop）](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 輸入下列命令以啟動 spark shell：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    您會看到歡迎使用訊息和 `scala>` 提示字元，您可以在其中輸入命令。

1. 啟動 spark shell 之後，您可以使用下列命令來啟動 Hive 倉儲連接器實例：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>在企業安全性套件（ESP）叢集上連接及執行查詢

企業安全性套件（ESP）為 Azure HDInsight 中的 Apache Hadoop 叢集，提供企業級的功能，例如 Active Directory 型驗證、多使用者支援和角色型存取控制。 如需 ESP 的詳細資訊，請參閱[在 HDInsight 中使用企業安全性套件](../domain-joined/apache-domain-joined-architecture.md)。

1. 透過 SSH 連線到您 Apache Spark 叢集的前端節點。 如需使用 SSH 連線到您的叢集的詳細資訊，請參閱[使用 ssh 連線到 HDInsight （Apache Hadoop）](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 輸入 `kinit`，並使用網域使用者登入。

1. 使用完整的設定參數清單來啟動 spark-shell，如下所示。 在角括弧之間的所有大寫字母中的所有值都必須根據您的叢集來指定。 如果您需要找出下列任何參數的輸入值，請參閱[Hive 倉儲連接器設定](#hive-warehouse-connector-setup)上的一節：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>從 Hive 查詢建立 Spark 資料框架

所有使用 HWC 程式庫之查詢的結果都會以資料框架的形式傳回。 下列範例示範如何建立基本查詢。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

查詢的結果是 Spark 資料框架，可搭配 Spark 程式庫（例如 MLIB 和 SparkSQL）使用。

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>將 Spark 資料框架寫出至 Hive 資料表

Spark 原本就不支援寫入 Hive 的受控 ACID 資料表。 不過，您可以使用 HWC 將任何資料框架寫出至 Hive 資料表。 您可以在下列範例中看到這項功能的運作方式：

1. 建立名為 `sampletable_colorado` 的資料表，並使用下列命令來指定其資料行：

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. 篩選 `hivesampletable` 資料行 `state` 等於 `Colorado`的資料表。 此 Hive 資料表的查詢會以 Spark 資料框架的形式傳回。 然後，資料框架會使用 `write` 函數，儲存在 Hive 資料表 `sampletable_colorado`。

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. 使用下列命令來查看結果：

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![hive 倉儲連接器顯示 hive 資料表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>結構化串流寫入

使用 Hive 倉儲連接器，您可以使用 Spark 串流將資料寫入 Hive 資料表。

請遵循下列步驟來建立 Hive 倉儲連接器範例，以從 localhost 埠9999上的 Spark 資料流程將資料內嵌至 Hive 資料表。

1. 遵循 [[連接和執行查詢](#connecting-and-running-queries)] 底下的步驟。

1. 使用下列命令來開始 spark 串流：

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 執行下列步驟，為您建立的 Spark 資料流程產生資料：
    1. 在相同的 Spark 叢集中開啟第二個 SSH 會話。
    1. 在命令提示字元中，輸入 `nc -lk 9999`。 此命令會使用 netcat 公用程式，從命令列將資料傳送至指定的埠。

1. 返回第一個 SSH 會話，並建立新的 Hive 資料表來保存串流資料。 在 spark shell 中，輸入下列命令：

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 然後使用下列命令，將串流資料寫入至新建立的資料表：

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` 和 `database` 選項目前必須手動設定，因為 Apache Spark 中有已知問題。 如需有關此問題的詳細資訊，請參閱[SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)。

1. 返回第二個 SSH 會話，然後輸入下列值：

    ```bash
    foo
    HiveSpark
    bar
    ```

1. 返回第一個 SSH 會話，並記下簡短的活動。 使用下列命令來查看資料：

    ```scala
    hive.table("stream_table").show()
    ```

使用**Ctrl + C**在第二個 SSH 會話上停止 netcat。 使用 `:q`，在第一個 SSH 會話上結束 spark shell。

### <a name="securing-data-on-spark-esp-clusters"></a>保護 Spark ESP 叢集上的資料

1. 輸入下列命令，以建立具有一些範例資料的資料表 `demo`：

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 使用下列命令來查看資料表的內容。 套用原則之前，`demo` 資料表會顯示完整的資料行。

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![套用 ranger 原則之前的示範表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 套用只顯示資料行最後四個字元的資料行遮罩原則。  
    1. 移至 `https://CLUSTERNAME.azurehdinsight.net/ranger/`的 Ranger 管理 UI。
    1. 按一下 [ **hive**] 底下叢集的 hive 服務。
        ![ranger service manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. 按一下 [**遮罩**] 索引標籤，然後 [**新增原則**]

        ![hive 倉儲連接器 ranger hive 原則清單](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. 提供所需的原則名稱。 選取資料庫：**預設值**，hive 資料表：**示範**，hive 資料行：**名稱**，使用者： **rsadmin2**，存取類型：**選取**，以及部分遮罩：從**選取遮罩選項**功能表**顯示最後4個**。 按一下 [加入]。
                ![建立原則](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 再次查看資料表的內容。 套用 ranger 原則之後，我們只會看到資料行的最後四個字元。

    ![套用 ranger 原則後的示範資料表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>後續步驟

* [搭配 HDInsight 使用互動式查詢](./apache-interactive-query-get-started.md)
* [使用 Zeppelin、Livy、spark-submit 和 pyspark 與 Hive 倉儲連接器互動的範例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
