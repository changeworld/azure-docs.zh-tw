---
title: HDInsight 上 Hadoop 服務所使用的連接埠 - Azure
description: 本文提供了在 Azure HDInsight 執行的 Apache Hadoop 服務使用的連接埠清單
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/06/2020
ms.openlocfilehash: fe2cb04f36026740dc54f4668d3c3188592bd8ae
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754218"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>HDInsight 上 Apache Hadoop 服務所使用的連接埠

本文件提供了在 HDInsight 群集上運行的 Apache Hadoop 服務使用的連接埠的清單。 此外，也提供用來連線到使用 SSH 之叢集的連接埠相關資訊。

## <a name="public-ports-vs-non-public-ports"></a>公用連接埠與非公用連接埠

基於 Linux 的 HDInsight 群集僅公開公開公開公開顯示 Internet 上的三個埠:22、23 和 443。 這些埠使用通過安全 HTTPS 協定公開的 SSH 和服務來保護群集訪問。

HDInsight 由在 Azure 虛擬網路上運行的多個 Azure 虛擬機器(群集節點)實現。 您可以從虛擬網路存取不是透過網際網路公開的連接埠。 如果通過 SSH 連接到頭節點,則可以直接訪問群集節點上運行的服務。

> [!IMPORTANT]  
> 如果您沒有將 Azure 虛擬網路指定為 HDInsight 的設定選項，則會自動建立一個。 但是,不能將其他電腦(如其他 Azure 虛擬機器或用戶端開發電腦)加入到此虛擬網路。

若要將其他電腦加入至虛擬網路，您必須先建立虛擬網路，然後在建立 HDInsight 叢集時進行指定。 有關詳細資訊,請參閱為[HDInsight 規劃虛擬網路](hdinsight-plan-virtual-network-deployment.md)。

## <a name="public-ports"></a>公用連接埠

HDInsight 群集中的所有節點都位於 Azure 虛擬網路中。 無法直接從 Internet 訪問節點。 公用閘道提供下列連接埠 (常見於所有的 HDInsight 叢集類型) 的網際網路存取。

| 服務 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- |
| sshd |22 |SSH |將用戶端連接到主要前端節點上的 sshd。 有關詳細資訊,請參閱將[SSH 與 HDInsight 一起使用](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
| sshd |22 |SSH |將用戶端連接至邊緣節點上的 sshd。 有關詳細資訊,請參閱將[SSH 與 HDInsight 一起使用](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
| sshd |23 |SSH |將用戶端連接到次要前端節點上的 sshd。 有關詳細資訊,請參閱將[SSH 與 HDInsight 一起使用](hdinsight-hadoop-linux-use-ssh-unix.md)。 |
| Ambari |443 |HTTPS |Ambari Web UI。 請參閱 [使用 Apache Ambari Web UI 來管理 HDInsight](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API。 請參閱[使用 Apache Ambari REST API 來管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog REST API。 請參考[使用帶捲曲的映射縮減](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |使用 ODBC 連接至 Hive。 請參閱 [使用 Microsoft ODBC 驅動程式將 Excel 連接到 HDInsight](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。 |
| HiveServer2 |443 |JDBC |使用 JDBC 來連線至 Hive。 請參閱[使用 Hive JDBC 驅動程式來連線至 HDInsight 上的 Apache Hive](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

下列各項適用於特定叢集類型︰

| 服務 | 連接埠 | 通訊協定 | 叢集類型 | 描述 |
| --- | --- | --- | --- | --- |
| `Stargate` |443 |HTTPS |hbase |HBase REST API。 請參閱[開始使用 Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark REST API。 請參閱[使用 Apache Livy 從遠端提交 Apache Spark 作業](spark/apache-spark-livy-rest-interface.md) |
| Spark Thrift 伺服器 |443 |HTTPS |Spark |用來提交 Hive 查詢的 Spark Thrift 伺服器。 請參閱[使用 Beeline 搭配 HDInsight 上的 Apache Hive](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm Web UI。 請參閱[部署和管理 HDInsight 上的 Apache Storm 拓撲](storm/apache-storm-deploy-monitor-topology-linux.md) |
| 卡夫卡休息代理 |443 |HTTPS |Kafka |卡夫卡 REST API. 請參考[使用 REST 代理在 Azure HDInsight 中與 Apache Kafka 叢集進行互動](kafka/rest-proxy.md) |

### <a name="authentication"></a>驗證

在網際網路上公開的所有服務都必須經過驗證︰

| 連接埠 | 認證 |
| --- | --- |
| 22 或 23 |在叢集建立期間指定的 SSH 使用者認證 |
| 443 |在叢集建立期間設定的登入名稱 (預設值：admin) 和密碼 |

## <a name="non-public-ports"></a>非公用連接埠

> [!NOTE]  
> 部分服務只能在特定叢集類型上使用。 例如，HBase 只能在 HBase 叢集類型上使用。

> [!IMPORTANT]  
> 某些服務一次只能在一個前端節點上執行。 如果您嘗試連接到主要前端節點上的服務並收到錯誤，請使用次要前端節點重試。

### <a name="ambari"></a>Ambari

| 服務 | 節點 | 連接埠 | URL 路徑 | 通訊協定 |
| --- | --- | --- | --- | --- |
| Ambari Web UI | 前端節點 | 8080 | / | HTTP |
| Ambari REST API | 前端節點 | 8080 | /api/v1 | HTTP |

範例：

* Ambari REST API：`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- | --- |
| NameNode Web UI |前端節點 |30070 |HTTPS |用以檢視狀態的 Web UI |
| NameNode 中繼資料服務 |前端節點 |8020 |IPC |檔案系統中繼資料 |
| DataNode |所有背景工作節點 |30075 |HTTPS |Web UI 以查看狀態、日誌等。 |
| DataNode |所有背景工作節點 |30010 |&nbsp; |資料傳輸 |
| DataNode |所有背景工作節點 |30020 |IPC |中繼資料作業 |
| 次要 NameNode |前端節點 |50090 |HTTP |NameNode 中繼資料的檢查點 |

### <a name="yarn-ports"></a>YARN 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- | --- |
| Resource Manager |前端節點 |8088 |HTTP |適用於 Resource Manager 的 Web UI |
| Resource Manager |前端節點 |8090 |HTTPS |適用於 Resource Manager 的 Web UI |
| Resource Manager 系統管理介面 |前端節點 |8141 |IPC |對於應用程式提交(Hive、Hive 伺服器、Pig等)。 |
| Resource Manager 排程器 |前端節點 |8030 |HTTP |系統管理介面 |
| Resource Manager 應用程式介面 |前端節點 |8050 |HTTP |應用程式管理員介面的位址 |
| NodeManager |所有背景工作節點 |30050 |&nbsp; |容器管理員的位址 |
| NodeManager Web UI |所有背景工作節點 |30060 |HTTP |資源管理員介面 |
| Timeline 位址 |前端節點 |10200 |RPC |Timeline 服務 RPC 服務。 |
| Timeline Web UI |前端節點 |8188 |HTTP |Timeline 服務 Web UI |

### <a name="hive-ports"></a>Hive 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- | --- |
| HiveServer2 |前端節點 |10001 |Thrift |要連接到 Hive 的服務 (Thrift/JDBC) |
| Hive 中繼存放區 |前端節點 |9083 |Thrift |要連接到 Hive 中繼資料的服務 (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- | --- |
| WebHCat 伺服器 |前端節點 |30111 |HTTP |以 HCatalog 和其他 Hadoop 服務為基礎的 Web API |

### <a name="mapreduce-ports"></a>MapReduce 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- | --- |
| JobHistory |前端節點 |19888 |HTTP |MapReduce JobHistory Web UI |
| JobHistory |前端節點 |10020 |&nbsp; |MapReduce JobHistory 伺服器 |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |將中繼對應輸出傳輸至要求的歸納器 |

### <a name="oozie"></a>Oozie

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- | --- |
| Oozie 伺服器 |前端節點 |11000 |HTTP |Oozie 服務的 URL |
| Oozie 伺服器 |前端節點 |11001 |HTTP |Oozie 系統管理的連接埠 |

### <a name="ambari-metrics"></a>Ambari 度量

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- | --- |
| TimeLine (應用程式歷程記錄) |前端節點 |6188 |HTTP |Timeline 服務 Web UI |
| TimeLine (應用程式歷程記錄) |前端節點 |30200 |RPC |Timeline 服務 Web UI |

### <a name="hbase-ports"></a>HBase 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- | --- |
| HMaster |前端節點 |16000 |&nbsp; |&nbsp; |
| HMaster 資訊 Web UI |前端節點 |16010 |HTTP |HBase 主要 Web UI 的連接埠 |
| 區域伺服器 |所有背景工作節點 |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |用戶端用於連接至 ZooKeeper 的連接埠 |

### <a name="kafka-ports"></a>Kafka 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| --- | --- | --- | --- | --- |
| Broker |背景工作節點 |9092 |[Kafka Wire Protocol (Kafka 有線通訊協定)](https://kafka.apache.org/protocol.html) |用於用戶端通訊 |
| &nbsp; |Zookeeper 節點 |2181 |&nbsp; |用戶端用於連接至 ZooKeeper 的連接埠 |
| REST 代理程式 | 卡夫卡管理節點 |9400 |HTTPS |[卡夫卡 REST 規格](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Spark 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | URL 路徑 | 描述 |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift 伺服器 |前端節點 |10002 |Thrift | &nbsp; | 要連接到 Spark SQL 的服務 (Thrift/JDBC) |
| Livy 伺服器 | 前端節點 | 8998 | HTTP | &nbsp; | 要執行陳述式、作業和應用程式的服務 |
| Jupyter 筆記本 | 前端節點 | 8001 | HTTP | &nbsp; | Jupyter Notebook 網站 |

範例：

* Livy：`curl -u admin -G "http://10.0.0.11:8998/"`。 在此範例中，`10.0.0.11` 是託管 Livy 服務之前端節點的 IP 位址。
