---
title: Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 例外狀況
description: Azure HDInsight 中 Apache Spark 叢集的各種 OutOfMemoryError 例外狀況
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395140"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 例外狀況

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和問題的可能解決方法。

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>案例： Apache Spark 的 OutOfMemoryError 例外狀況

### <a name="issue"></a>問題

您的 Apache Spark 應用程式因 OutOfMemoryError 未處理的例外狀況而失敗。 您可能會收到類似下列的錯誤訊息：

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>原因

此例外狀況最可能發生的原因，就是未配置足夠的堆積記憶體給 Java 虛擬機器 (JVM)。 這些 Jvm 會以執行程式或驅動程式的形式啟動，做為 Apache Spark 應用程式的一部分。

### <a name="resolution"></a>解決方案

1. 決定 Spark 應用程式將處理的資料大小上限。 根據輸入資料大小的最大值、轉換輸入資料所產生的中繼資料，以及進一步轉換中繼資料所產生的輸出資料，來預估大小。 如果初始估計值不足，請稍微增加大小，然後反復執行直到記憶體錯誤減少為止。

1. 請確定要使用的 HDInsight 叢集具有足夠的記憶體資源及核心，才能採用 Spark 應用程式。 這可以藉由在叢集的 YARN UI 的 [叢集計量] 區段中，查看已使用的**記憶體**值和 [**記憶體總計**] 和 [已**使用的虛擬核心**] 與 [**虛擬核心總計**]。

    ![yarn 核心記憶體視圖](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. 將下列 Spark 設定設為適當的值。 平衡應用程式需求與叢集中可用的資源。 這些值不應超過 YARN 所查看的可用記憶體和核心90%，而且也應該符合 Spark 應用程式的最小記憶體需求：

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    所有執行程式使用的記憶體總計 =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    驅動程式使用的記憶體總計 =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>案例：嘗試開啟 Apache Spark 歷程記錄伺服器時發生 JAVA 堆積空間錯誤

### <a name="issue"></a>問題

在 Spark 歷程記錄伺服器中開啟事件時，您會收到下列錯誤：

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>原因

此問題通常是因為在開啟大型 spark 事件檔案時發生資源不足所造成。 根據預設，Spark 堆積大小會設定為 1 GB，但大型 Spark 事件檔案可能需要超過此值。

如果您想要確認您嘗試載入的檔案大小，您可以執行下列命令：

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>解決方案

您可以藉由編輯 Spark 設定中的 [`SPARK_DAEMON_MEMORY`] 屬性，然後重新開機所有服務，來增加 Spark 歷程記錄伺服器記憶體。

您可以在 Ambari 瀏覽器 UI 中選取 Spark2/Config/Advanced Spark2-env 區段來執行此動作。

![Advanced spark2-env 區段](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

新增下列屬性，以將 Spark 歷程記錄伺服器記憶體從1g 變更為4g： `SPARK_DAEMON_MEMORY=4g`。

![Spark 屬性](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

請務必從 Ambari 重新開機所有受影響的服務。

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>案例： Apache Spark 叢集上的 Livy 伺服器無法啟動

### <a name="issue"></a>問題

無法在 Apache Spark [（Linux 上的 Spark 2.1 （HDI 3.6）] 上啟動 Livy 伺服器。 嘗試重新開機會導致下列錯誤堆疊中的 Livy 記錄檔：

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>原因

`java.lang.OutOfMemoryError: unable to create new native thread` 重點事項 OS 無法指派更多原生執行緒給 Jvm。 確認此例外狀況是因為違反每個進程的執行緒計數限制所造成。

當 Livy 伺服器意外終止時，Spark 叢集的所有連接也會終止，這表示所有作業和相關資料都會遺失。 在 HDP 2.6 中引進了會話復原機制，Livy 會將會話詳細資料儲存在 Zookeeper 中，以在 Livy 伺服器回復後復原。

透過 Livy 提交大量的作業時，在 Livy 伺服器的高可用性中，會將這些會話狀態儲存在 ZK （在 HDInsight 叢集上），並在 Livy 服務重新開機時復原那些會話。 在非預期終止後重新開機時，Livy 會為每個會話建立一個執行緒，而這會累積特定數目的待復原會話，而造成建立太多執行緒。

### <a name="resolution"></a>解決方案

使用下面詳述的步驟來刪除所有專案。

1. 使用取得 zookeeper 節點的 IP 位址

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. 上述命令會列出我的叢集的所有 zookeeper

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. 使用 ping 取得 zookeeper 節點的所有 IP 位址，或者也可以使用 zk 名稱從前端節點連接到 zookeeper

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. 連線到 zookeeper 之後，請執行下列命令來列出所有嘗試重新開機的會話。

    1. 大部分的情況下，這可能是超過8000個會話的清單####

        ```bash
        ls /livy/v1/batch
        ```

    1. 下列命令會移除所有要復原的會話。 #####

        ```bash
        rmr /livy/v1/batch
        ```

1. 等候上述命令完成，並傳回游標以傳回提示，然後從 Ambari 重新開機 Livy 服務，這應該會成功。

> [!NOTE]
> 在 livy 會話完成執行後，將其 `DELETE`。 Spark 應用程式完成後，就不會自動刪除 Livy 批次會話，這是設計的。 Livy 會話是針對 Livy Rest 伺服器的 POST 要求所建立的實體。 需要 `DELETE` 呼叫，才能刪除該實體。 或者，我們應該等待 GC 開始。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* [Spark 記憶體管理總覽](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)。

* [在 HDInsight 叢集上進行 Spark 應用程式的偵錯工具](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)。

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 與[@AzureSupport](https://twitter.com/azuresupport)進行連接-官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
