---
title: 阿帕奇火花&蜂巢 - 蜂巢倉庫連接器 - Azure HDInsight
description: 瞭解如何將 Apache Spark 和 Apache 蜂巢與 Azure HDInsight 上的蜂巢倉庫連接器集成。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252413"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>整合 Apache Spark 和 Apache Hive 與 Hive Warehouse Connector

Apache Hive 倉庫連接器 （HWC） 是一個庫，通過支援諸如在 Spark DataFrame 和 Hive 表之間移動資料以及將 Spark 流資料定向到 Hive 表等任務，使您能夠更輕鬆地使用 Apache Spark 和 Apache Hive。 蜂巢倉庫連接器就像火花和蜂巢之間的橋樑。 它支援 Scala、JAVA 和 Python 進行開發。

Hive 倉庫連接器允許您利用 Hive 和 Spark 的獨特功能構建強大的大資料應用程式。 Apache Hive 支援原子、一致、隔離和持久 （ACID） 的資料庫事務。 有關 Hive 中的 ACID 和交易的詳細資訊，請參閱[Hive 交易](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)。 Hive 還通過 Apache 遊俠和低延遲分析處理提供詳細的安全控制，在 Apache Spark 中不可用。

Apache Spark，具有結構化流式處理 API，提供 Apache 蜂巢中不可用的流式處理功能。 從 HDInsight 4.0 開始，Apache Spark 2.3.1 和 Apache Hive 3.1.0 具有單獨的元存儲，這會使互通性變得困難。 Hive Warehouse Connector 可讓您更輕鬆地搭配使用 Spark 和 Hive。 HWC 庫並行將資料從 LLAP 守護進程載入到 Spark 執行器，使其比使用從 Spark 到 Hive 的標準 JDBC 連接更高效、更具可擴充性。

![蜂巢倉庫連接器體系結構](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive Warehouse Connector 所支援的部分作業包括：

* 描述資料表
* 為 ORC 格式的資料創建表
* 選取 Hive 資料並擷取資料架構
* 將資料架構批次寫入至 Hive
* 執行 Hive 更新陳述式
* 從 Hive 讀取資料表資料、在 Spark 中轉換它，並將它寫入至新的 Hive 資料表
* 使用 HiveStreaming 將資料架構或 Spark 串流寫入至 Hive

## <a name="hive-warehouse-connector-setup"></a>蜂巢倉庫連接器設置

按照以下步驟在 Azure HDInsight 中的 Spark 和互動式查詢群集之間設置 Hive 倉庫連接器：

### <a name="create-clusters"></a>建立叢集

1. 使用存儲帳戶和自訂 Azure 虛擬網路創建 HDInsight Spark **4.0**群集。 有關在 Azure 虛擬網路中創建群集的資訊，請參閱[將 HDInsight 添加到現有虛擬網路](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)。

1. 創建 HDInsight 互動式查詢 （LLAP） **4.0**群集，該群集與 Spark 群集具有相同的存儲帳戶和 Azure 虛擬網路。

### <a name="modify-hosts-file"></a>修改主機檔

從互動式查詢群集 headnode0 上的`/etc/hosts`檔中複製節點資訊，並將資訊串聯到 Spark 群集`/etc/hosts`頭節點 0 上的檔。 此步驟將允許 Spark 群集解析互動式查詢群集中節點的 IP 位址。 使用`cat /etc/hosts`查看更新檔的內容。 最終輸出應類似于下面的螢幕截圖中顯示的內容。

![蜂巢倉庫連接器主機檔](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>收集初步資訊

#### <a name="from-your-interactive-query-cluster"></a>從互動式查詢群集

1. 使用`https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs`互動式查詢群集的名稱導航`LLAPCLUSTERNAME`到群集的 Apache Ambari Hive 頁面。

1. 導航到**高級** > **常規** > **配置單元.metastore.uris**並記下該值。 該值可能類似于： `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`。

1. 導航到**高級** > **配置單元網站** > **配置單元.zookeeper.quorum**並記下該值。 該值可能類似于： `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`。

#### <a name="from-your-apache-spark-cluster"></a>從您的阿帕奇火花群集

1. 使用`https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs`Apache Spark 群集的名稱導航`SPARKCLUSTERNAME`到群集的 Apache Ambari Hive 頁面。

1. 導航到**高級** > **配置單元-互動式網站** > **hive.llap.daemon.服務.主機**並記下值。 該值可能類似于： `@llap0`。

### <a name="configure-spark-cluster-settings"></a>配置 Spark 群集設置

從 Spark Ambari Web UI 導航到**Spark2** > **CONFIGS** > **自訂 spark2 預設值**。

![阿帕奇安巴里火花2配置](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

根據需要選擇 **"添加屬性..."**

| Key | 值 |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|您之前從**hive.llap.daemon.服務.hosts**獲得的值。|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. 設置為 JDBC 連接字串，該字串連接到互動式查詢群集上的 Hiveserver2。 使用`LLAPCLUSTERNAME`互動式查詢群集的名稱進行替換。 替換為`PWD`實際密碼。|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. 設置為適當的 HDFS 相容臨時目錄。 如果您有兩個不同的群集，則臨時目錄應該是 LLAP 群集存儲帳戶臨時目錄中的資料夾，以便 HiveServer2 有權訪問它。  替換為`STORAGE_ACCOUNT_NAME`群集正在使用的存儲帳戶的名稱和`STORAGE_CONTAINER_NAME`存儲容器的名稱。|
|`spark.datasource.hive.warehouse.metastoreUri`|您之前從**hive.metastore.uris**獲得的值。|
|`spark.security.credentials.hiveserver2.enabled`|`false`用於 YARN 用戶端部署模式。|
|`spark.hadoop.hive.zookeeper.quorum`|您之前從**蜂巢**獲得的值.|

根據需要保存更改並重新啟動元件。

## <a name="using-the-hive-warehouse-connector"></a>使用蜂巢倉庫連接器

### <a name="connecting-and-running-queries"></a>連接和執行查詢

您可以選擇幾種不同的方法來連接到互動式查詢群集並使用 Hive 倉庫連接器執行查詢。 支援的方法包括以下工具：

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

本文提供的所有示例都將通過火花殼執行。

要啟動火花殼會話，執行以下步驟：

1. SSH 進入 Apache Spark 群集的頭節點。 有關使用 SSH 連接到群集的詳細資訊，請參閱使用[SSH 連接到 HDInsight（Apache Hadoop）。](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. 輸入以下命令以啟動火花殼：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    您將看到歡迎消息和提示，`scala>`您可以在其中輸入命令。

1. 啟動火花殼後，可以使用以下命令啟動 Hive 倉庫連接器實例：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>在企業安全包 （ESP） 群集上連接和執行查詢

企業安全包 （ESP） 為 Azure HDInsight 中的 Apache Hadoop 群集提供企業級功能，如基於 Active Directory 的身份驗證、多使用者支援和基於角色的存取控制。 有關 ESP 的詳細資訊，請參閱[在 HDInsight 中使用企業安全包](../domain-joined/apache-domain-joined-architecture.md)。

1. SSH 進入 Apache Spark 群集的頭節點。 有關使用 SSH 連接到群集的詳細資訊，請參閱使用[SSH 連接到 HDInsight（Apache Hadoop）。](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. 使用`kinit`域使用者鍵入和登錄。

1. 使用配置參數的完整清單啟動火花殼，如下所示。 角度括弧之間的所有大寫字母中的所有值都必須基於群集指定。 如果您需要找出要輸入以下任何參數的值，請參閱[Hive 倉庫連接器設置](#hive-warehouse-connector-setup)中的部分。

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>從蜂巢查詢創建火花資料幀

使用 HWC 庫的所有查詢的結果將作為資料幀返回。 以下示例演示如何創建基本查詢。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

查詢的結果是 Spark 資料幀，可用於 MLIB 和 SparkSQL 等 Spark 庫。

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>將火花資料幀寫入 Hive 表

Spark 在本機上不支援寫入 Hive 管理的 ACID 表。 但是，使用 HWC，您可以將任何資料幀寫入 Hive 表。 您可以在以下示例中看到此功能在起作用：

1. 創建一個表`sampletable_colorado`，使用以下命令指定其列：

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. 篩選列`hivesampletable``state`等於 的`Colorado`表。 Hive 表的此查詢將作為 Spark 資料幀返回。 然後，使用`sampletable_colorado``write`函數將資料幀保存在 Hive 表中。

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. 使用以下命令查看結果：

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![蜂巢倉庫連接器顯示蜂巢表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>結構化流式寫入

使用 Hive 倉庫連接器，可以使用 Spark 流將資料寫入 Hive 表。

按照以下步驟創建 Hive 倉庫連接器示例，該示例將本地主機埠 9999 上的 Spark 流中的資料引入 Hive 表。

1. 按照["連接"和"執行查詢](#connecting-and-running-queries)"下的步驟操作。

1. 使用以下命令啟動火花流：

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. 通過執行以下步驟，為創建的 Spark 流生成資料：
    1. 在同一 Spark 群集上打開第二個 SSH 會話。
    1. 在命令提示字元中，輸入 `nc -lk 9999`。 此命令使用 netcat 實用程式將資料從命令列發送到指定的埠。

1. 返回到第一個 SSH 會話並創建新的 Hive 表來保存流資料。 在火花殼處，輸入以下命令：

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 然後使用以下命令將流資料寫入新創建的表：

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri`由於`database`Apache Spark 中的已知問題，當前必須手動設置 和 選項。 有關此問題的詳細資訊，請參閱[SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)。

1. 返回到第二個 SSH 會話並輸入以下值：

    ```bash
    foo
    HiveSpark
    bar
    ```

1. 返回到第一個 SSH 會話並記下簡要活動。 使用以下命令查看資料：

    ```scala
    hive.table("stream_table").show()
    ```

使用**Ctrl + C**在第二個 SSH 會話上停止網貓。 用於`:q`在第一個 SSH 會話上退出火花殼。

### <a name="securing-data-on-spark-esp-clusters"></a>保護 Spark ESP 群集上的資料

1. 通過輸入以下`demo`命令創建包含一些示例資料的表：

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 使用以下命令查看表的內容。 在應用策略之前，`demo`該表將顯示整個列。

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![應用遊俠策略前的演示表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 應用僅顯示列最後四個字元的列遮罩策略。  
    1. 轉到 Ranger 管理員 UI。 `https://CLUSTERNAME.azurehdinsight.net/ranger/`
    1. 按一下 Hive 下的群集的**Hive**服務。
        ![護林員服務經理](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. 按一下 **"遮罩"** 選項卡，然後**添加新策略**

        ![蜂巢倉庫連接器遊俠蜂巢策略清單](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    a. 提供所需的策略名稱。 選擇資料庫：**預設**，Hive 表：**演示**、Hive 列：**名稱**、使用者 **：rsadmin2、** 訪問類型：**選擇**和**部分遮罩：** 從 **"選擇遮罩選項"** 功能表中顯示最後 4 個。 按一下 **[新增]**。
                ![創建策略](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 再次查看表的內容。 應用遊俠策略後，我們只能看到列的最後四個字元。

    ![應用遊俠策略後演示表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>後續步驟

* [搭配 HDInsight 使用互動式查詢](./apache-interactive-query-get-started.md)
* [使用 Zeppelin、Livy、火花提交和火花與 Hive 倉庫連接器交互的示例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
