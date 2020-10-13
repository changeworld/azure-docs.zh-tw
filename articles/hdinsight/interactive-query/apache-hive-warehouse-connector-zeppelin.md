---
title: Hive Warehouse Connector - 使用 Livy 的 Apache Zeppelin - Azure HDInsight
description: 了解如何在 Azure HDInsight 上整合 Hive Warehouse Connector 與 Apache Zeppelin。
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 6ee1c70ec02af2a24f7867a6e6b06593361612b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86083112"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>在 Azure HDInsight 上將 Apache Zeppelin 與 Hive Warehouse Connector 整合起來

HDInsight Spark 叢集包含具有不同解譯器的 Apache Zeppelin 筆記本。 在本文中，我們只將焦點放在使用 Hive Warehouse Connector 從 Spark 存取 Hive 資料表的 Livy 解譯器。

## <a name="prerequisite"></a>必要條件

完成 [Hive Warehouse Connector 設定](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup)步驟。

## <a name="getting-started"></a>開始使用

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)來連線到 Apache Spark 叢集。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 從 ssh 工作階段執行下列命令，以記下 `hive-warehouse-connector-assembly` 和 `pyspark_hwc` 版本：

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    儲存輸出以供稍後在設定 Apache Zeppelin 時使用。

## <a name="configure-livy"></a>設定 Livy

下列是從 Zeppelin 使用 Livy 解譯器存取 hive 資料表時所需的設定。

### <a name="interactive-query-cluster"></a>互動式查詢叢集

1. 從網頁瀏覽器瀏覽至 `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs`，其中 LLAPCLUSTERNAME 是您互動式查詢叢集的名稱。

1. 流覽至 [進階] > [自訂核心網站]。 選取 [新增屬性] 以新增下列設定：

    | 組態                 | 值 |
    | ----------------------------- |-------|
    | hadoop.proxyuser.livy.groups  | *     |
    | hadoop.proxyuser.livy.hosts   | *     |

1. 儲存變更並重新啟動所有受影響的元件。

### <a name="spark-cluster"></a>Spark 叢集

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`，其中 CLUSTERNAME 是您 Apache Spark 叢集的名稱。

1. 展開 [自訂 livy2-conf]。 選取 [新增屬性] 以新增下列設定：

    | 組態                 | 值                                      |
    | ----------------------------- |------------------------------------------  |
    | livy.file.local-dir-whitelist | /usr/hdp/current/hive_warehouse_connector/ |

1. 儲存變更並重新啟動所有受影響的元件。

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>在 Zeppelin UI 中設定 Livy 解譯器 (Spark 叢集)

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter`，其中 `CLUSTERNAME` 是您 Apache Spark 叢集的名稱。

1. 瀏覽至 [livy2]。

1. 新增下列設定：

    | 組態                 | 值                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy.spark.hadoop.hive.llap.daemon.service.hosts | @llap0 |
    | livy.spark.security.credentials.hiveserver2.enabled | true |
    | livy.spark.sql.hive.llap | true |
    | livy.spark.yarn.security.credentials.hiveserver2.enabled | true |
    | livy.superusers | livy,zeppelin |
    | livy.spark.jars | 第 1 課：建立 Windows Azure 儲存體物件`file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`。<br>使用先前從[開始使用](#getting-started)取得的值取代 VERSION。 |
    | livy.spark.submit.pyFiles | 第 1 課：建立 Windows Azure 儲存體物件`file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`。<br>使用先前從[開始使用](#getting-started)取得的值取代 VERSION。 |
    | livy.spark.sql.hive.hiveserver2.jdbc.url | 將其設為互動式查詢叢集的 HiveServer2 互動式 JDBC URL。 |
    | spark.security.credentials.hiveserver2.enabled | true |

1. 請新增下列設定 (僅適用於 ESP 叢集)：

    | 組態| 值|
    |---|---|
    | livy.spark.sql.hive.hiveserver2.jdbc.url.principal | `hive/<llap-headnode>@<AAD-Domain>` |

    * 從網頁瀏覽器中，流覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary` CLUSTERNAME 是 Interactive Query 叢集的名稱。 按一下 [ **HiveServer2 Interactive**]。 您會看到 LLAP 執行所在前端節點的完整功能變數名稱 (FQDN) ，如螢幕擷取畫面所示。 取代 `<llap-headnode>` 為此值。

        ![hive 倉儲連接器前端節點](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md) 連線到您的 Interactive Query 叢集。 在檔案 `default_realm` 中尋找參數 `/etc/krb5.conf` 。 以 `<AAD-DOMAIN>` 這個值取代為大寫字串，否則找不到認證。

        ![hive 倉儲連接器 AAD 網域](./media/apache-hive-warehouse-connector/aad-domain.png)

    * 例如， `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET` 。

1. 儲存變更，重新啟動 Livy 解譯器。

如果無法存取 Livy 解譯器，請修改 Ambari 中 Zeppelin 元件內存在的 `shiro.ini` 檔案。 如需詳細資訊，請參閱[設定Apache Zeppelin 安全性](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html)。  


## <a name="running-queries-in-zeppelin"></a>在 Zeppelin 中執行查詢 

使用 Livy 解譯器啟動 Zeppelin 筆記本並執行下列程式

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>後續步驟

* [HWC 和 Apache Spark 作業](./apache-hive-warehouse-connector-operations.md)
* [HWC 與 Apache Spark 和 Apache Hive 的整合](./apache-hive-warehouse-connector.md)
* [搭配 HDInsight 使用互動式查詢](./apache-interactive-query-get-started.md)
