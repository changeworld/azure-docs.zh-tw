---
title: Apache Spark 與 Hive - Hive Warehouse Connector - Azure HDInsight
description: 了解如何在 Azure HDInsight 上將 Apache Spark 和 Apache Hive 與 Hive Warehouse Connector 整合起來。
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 24968511d038b2cea41a59187c0a361684c6720e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511886"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>在 Azure HDInsight 上將 Apache Spark 和 Apache Hive 與 Hive Warehouse Connector 整合起來

Apache Hive Warehouse Connector (HWC) 是一個程式庫，可讓您更輕鬆地使用 Apache Spark 和 Apache Hive。 其支援在 Spark 資料框架和 Hive 資料表之間移動資料之類的工作。 此外，藉由將 Spark 串流資料導向 Hive 資料表。 Hive Warehouse Connector 的作用就像是 Spark 和 Hive 之間的橋樑。 其也支援以 Scala、Java 和 Python 作為開發的程式設計語言。

Hive Warehouse Connector 可讓您利用 Hive 和 Spark 的獨特功能來建置功能強大的巨量資料應用程式。

Apache Hive 可支援不可部分完成、一致、隔離和耐用 (ACID) 的資料庫交易。 如需 Hive 中 ACID 和交易的詳細資訊，請參閱 [Hive 交易](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)。 Hive 也會透過 Apache Spark 中未提供的 Apache Ranger 和低延遲分析處理 (LLAP) 來提供詳細的安全性控制。

Apache Spark 具有結構化串流 API，可提供 Apache Hive 中無法使用的串流功能。 從 HDInsight 4.0 開始，Apache Spark 2.3.1 和 Apache Hive 3.1.0 已有不同的中繼存放區。 不同的中繼存放區可能會使互通變得麻煩。 Hive Warehouse Connector 可讓您更輕鬆地一起使用 Spark 和 Hive。 HWC 程式庫會以平行方式將 LLAP 精靈中的資料載入到 Spark 執行程式。 此程序的效率和適應性會比 Spark 到 Hive 的標準 JDBC 連線還要好。

![Hive Warehouse Connector 架構](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive Warehouse Connector 所支援的部分作業如下：

* 描述資料表
* 建立 ORC 格式資料的資料表
* 選取 Hive 資料並擷取資料框架
* 以批次方式將資料框架寫入至 Hive
* 執行 Hive UPDATE 陳述式
* 從 Hive 讀取資料表資料、在 Spark 中進行轉換，然後寫入至新的 Hive 資料表
* 使用 HiveStreaming 將資料框架或 Spark 串流寫入至 Hive

## <a name="hive-warehouse-connector-setup"></a>Hive Warehouse Connector 設定

> [!IMPORTANT]
> 在 Spark 2.4 企業安全性套件叢集上安裝的 HiveServer2 互動式實例不支援與 Hive Warehouse Connector 搭配使用。 相反地，您必須設定個別的 HiveServer2 互動式叢集，以裝載 HiveServer2 的互動式工作負載。 不支援利用單一 Spark 2.4 叢集的 Hive Warehouse Connector 設定。

Hive Warehouse Connector 需要為 Spark 和 Interactive Query 工作負載準備不同叢集。 請遵循下列步驟，在 Azure HDInsight 中設定這些叢集。

### <a name="create-clusters"></a>建立叢集

1. 使用儲存體帳戶和自訂的 Azure 虛擬網路建立 HDInsight Spark **4.0** 叢集。 如需在 Azure 虛擬網路中建立叢集的相關資訊，請參閱[將 HDInsight 新增至現有虛擬網路](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)。

1. 使用與 Spark 叢集相同的儲存體帳戶和 Azure 虛擬網路，建立 HDInsight Interactive Query (LLAP) **4.0** 叢集。

### <a name="configure-hwc-settings"></a>設定 HWC 設定

#### <a name="gather-preliminary-information"></a>收集初步資訊

1. 從網頁瀏覽器瀏覽至 `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE`，其中 LLAPCLUSTERNAME 是您 Interactive Query 叢集的名稱。

1. 瀏覽至 [摘要] > [HiveServer2 互動式 JDBC URL] 並記下該值。 此值可能類似於：`jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive`。

1. 瀏覽至 [設定] > [進階] > [進階 Hive 網站] > [hive.zookeeper.quorum] 並記下該值。 此值可能類似於：`zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`。

1. 瀏覽至 [設定] > [進階] > [一般] > [hive.metastore.uris]，並記下該值。 此值可能類似於：`thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`。

1. 瀏覽至 [設定] > [進階] > [進階 Hive 互動式網站] > [hive.llap.daemon.service.hosts] 並記下該值。 此值可能類似於：`@llap0`。

#### <a name="configure-spark-cluster-settings"></a>設定 Spark 叢集設定

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`，其中 CLUSTERNAME 是您 Apache Spark 叢集的名稱。

1. 展開 [自訂 spark2-defaults]。

    ![Apache Ambari Spark2 設定](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

1. 選取 [新增屬性...] 以新增下列設定：

    | 組態 | 值 |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> 設定為適當的 HDFS 相容暫存目錄。 如果您有兩個不同的叢集，則暫存目錄必須是 LLAP 叢集儲存體帳戶的暫存目錄所含資料夾，HiveServer2 才能存取該目錄。  將 `STORAGE_ACCOUNT_NAME` 取代為叢集所使用儲存體帳戶的名稱，並將 `STORAGE_CONTAINER_NAME` 取代為儲存體容器的名稱。 |
    |`spark.sql.hive.hiveserver2.jdbc.url`| 您先前從 **HiveServer2 Interactive JDBC URL** 取得的值 |
    |`spark.datasource.hive.warehouse.metastoreUri`| 您先前從 **hive.metastore.uris** 取得的值。 |
    |`spark.security.credentials.hiveserver2.enabled`|若為 YARN 叢集模式則為 `true`，若為 YARN 用戶端模式則為 `false`。 |
    |`spark.hadoop.hive.zookeeper.quorum`| 您先前從 **hive.zookeeper.quorum** 取得的值。 |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| 您先前從 **hive.llap.daemon.service.hosts** 取得的值。 |

1. 儲存變更並重新啟動所有受影響的元件。

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>設定企業安全性套件 (ESP) 叢集的 HWC

企業安全性套件 (ESP) 會為 Azure HDInsight 中的 Apache Hadoop 叢集提供企業級的功能，例如 Active Directory 型驗證、多使用者支援和角色型存取控制。 如需 ESP 的詳細資訊，請參閱[在 HDInsight 中使用企業安全性套件](../domain-joined/apache-domain-joined-architecture.md)。

除了上一節提到的設定外，請再新增下列設定以在 ESP 叢集上使用 HWC。

1. 從 Spark 叢集的 Ambari Web UI，瀏覽至 [Spark2] > [設定] > [自訂 Spark2-defaults]。

1. 更新下列屬性。

    | 組態 | 值 |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * 從網頁瀏覽器中，流覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` CLUSTERNAME 是 Interactive Query 叢集的名稱。 按一下 [ **HiveServer2 Interactive**]。 您會看到 LLAP 執行所在前端節點的完整功能變數名稱 (FQDN) ，如螢幕擷取畫面所示。 取代 `<llap-headnode>` 為此值。

        ![hive 倉儲連接器前端節點](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md) 連線到您的 Interactive Query 叢集。 在檔案 `default_realm` 中尋找參數 `/etc/krb5.conf` 。 以 `<AAD-DOMAIN>` 這個值取代為大寫字串，否則找不到認證。

        ![hive 倉儲連接器 AAD 網域](./media/apache-hive-warehouse-connector/aad-domain.png)

    * 例如， `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` 。
    
1. 儲存變更並視需要重新啟動元件。

## <a name="hive-warehouse-connector-usage"></a>Hive Warehouse Connector 使用方式

您可以選擇幾種不同的方法來連線到 Interactive Query 叢集，並使用 Hive Warehouse Connector 執行查詢。 支援的方法包括下列工具：

* [Spark-shell / PySpark](../spark/apache-spark-shell.md)
* [Spark-submit](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


以下是從 Spark 連線到 HWC 的一些範例。

### <a name="spark-shell"></a>Spark-shell

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)來連線到 Apache Spark 叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 從 ssh 工作階段執行下列命令，以記下 `hive-warehouse-connector-assembly` 版本：

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. 使用上面所識別的 `hive-warehouse-connector-assembly` 版本來編輯下列程式碼。 然後執行命令以啟動 Spark 殼層：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. 啟動 Spark 殼層之後，您可以使用下列命令來啟動 Hive Warehouse Connector 執行個體：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-submit

一旦您將 scala/java 程式碼和相依性建置到組件 jar 後，請使用下列命令來啟動 Spark 應用程式。 以實際的值取代 `<VERSION>` 和 `<APP_JAR_PATH>`。

* YARN 用戶端模式
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* YARN 叢集模式
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

針對 Python，請一併新增下列設定。 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>在企業安全性套件 (ESP) 叢集上執行查詢

在啟動 spark-shell 或 spark-submit 之前，請先使用 `kinit`。 將 USERNAME 取代為具有叢集存取權限的網域帳戶名稱，然後執行下列命令：

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>保護 Spark ESP 叢集上的資料

1. 輸入下列命令，以建立具有一些範例資料的資料表 `demo`：

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 使用下列命令來檢視資料表的內容。 在套用原則之前，`demo` 資料表會顯示完整的資料行。

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![套用 ranger 原則之前的示範資料表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 套用資料行遮罩原則以便只顯示資料行的最後四個字元。  
    1. 移至 `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/` 上的 Ranger 管理員 UI。
    1. 在 [Hive] 底下按一下叢集的 Hive 服務。
        ![ranger service manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. 依序按一下 [遮罩] 索引標籤和 [新增原則]

        ![hive warehouse connector ranger hive 原則清單](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    1. 提供所需的原則名稱。 選取資料庫：[預設]、Hive 資料表：[示範]、Hive 資料行：[名稱]、使用者：[rsadmin2]、存取類型：[選取] 和部分遮罩：[顯示最後 4 個] (從 [選取遮罩選項] 功能表中)。 按一下 [新增] 。
                ![建立原則](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 再次檢視資料表的內容。 套用 ranger 原則之後，我們只能看到資料行的最後四個字元。

    ![套用 ranger 原則後的示範資料表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>後續步驟

* [HWC 和 Apache Spark 作業](./apache-hive-warehouse-connector-operations.md)
* [搭配 HDInsight 使用互動式查詢](./apache-interactive-query-get-started.md)
* [HWC 與 Apache Zeppelin 的整合](./apache-hive-warehouse-connector-zeppelin.md)
* [使用 Zeppelin、Livy、spark-submit 和 pyspark 來與 Hive Warehouse Connector 互動的範例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
