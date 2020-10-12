---
title: OutOfMemoryError Azure HDInsight 中 Apache Spark 的例外狀況
description: Azure HDInsight 中 Apache Spark 叢集的各種 OutOfMemoryError 例外狀況
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84709040"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>OutOfMemoryError Azure HDInsight 中 Apache Spark 的例外狀況

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>案例： Apache Spark 的 OutOfMemoryError 例外狀況

### <a name="issue"></a>問題

因為 OutOfMemoryError 未處理的例外狀況，所以您的 Apache Spark 應用程式失敗。 您可能會收到類似下列的錯誤訊息：

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

此例外狀況最可能發生的原因，就是未配置足夠的堆積記憶體給 Java 虛擬機器 (JVM)。 這些 Jvm 會在 Apache Spark 應用程式中以執行程式或驅動程式的形式啟動。

### <a name="resolution"></a>解決方案

1. 決定 Spark 應用程式將處理的資料大小上限。 根據輸入資料的大小上限、轉換輸入資料所產生的中繼資料，以及進一步轉換中繼資料所產生的輸出資料，來預估大小。 如果初始估計值不足，請稍微增加大小，然後反覆運算至記憶體錯誤減少。

1. 請確定要使用的 HDInsight 叢集具有足夠的記憶體資源及核心，才能採用 Spark 應用程式。 您可以藉由在叢集的 YARN UI 的 [叢集計量] 區段中，針對 [已使用的**記憶體****總計**] 和 [已**使用的虛擬核心**] 與 [**虛擬核心總計**] 的值，查看叢集計量區段。

    ![yarn 核心記憶體視圖](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. 將下列 Spark 設定設定為適當的值。 使用叢集中的可用資源來平衡應用程式需求。 這些值不應超過90% 的可用記憶體和核心（由 YARN 查看），也應符合 Spark 應用程式的最小記憶體需求：

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

開啟 Spark 歷程記錄伺服器中的事件時，您會收到下列錯誤：

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>原因

此問題通常是在開啟大型 spark 事件檔案時，因資源不足所造成。 Spark 堆積大小預設會設定為 1 GB，但大型 Spark 事件檔可能需要超過此值。

如果您想要驗證您嘗試載入的檔案大小，您可以執行下列命令：

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>解決方案

您可以藉由編輯 `SPARK_DAEMON_MEMORY` spark 設定中的屬性，並重新啟動所有服務，來增加 spark 歷程記錄伺服器記憶體。

您可以藉由選取 Spark2/Config/Advanced Spark2-env 區段，從 Ambari 瀏覽器 UI 內進行此作業。

![Advanced spark2-env 區段](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

新增下列屬性以將 Spark 歷程記錄伺服器記憶體從1g 變更為4g： `SPARK_DAEMON_MEMORY=4g` 。

![Spark 屬性](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

請務必從 Ambari 重新開機所有受影響的服務。

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>案例： Livy 伺服器無法在 Apache Spark 叢集上啟動

### <a name="issue"></a>問題

無法在 Apache Spark [ (Linux 上的 Spark 2.1 (HDI 3.6) ] 上啟動 Livy 伺服器。 嘗試從 Livy 記錄中重新開機下列錯誤堆疊的結果：

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

`java.lang.OutOfMemoryError: unable to create new native thread` 重點：作業系統無法指派更多原生執行緒給 Jvm。 確認此例外狀況是因為違反每個進程的執行緒計數限制所造成。

當 Livy Server 意外終止時，與 Spark 叢集的所有連線也會終止，這表示將會遺失所有的作業和相關資料。 在引進的 HDP 2.6 會話復原機制中，Livy 會將會話詳細資料儲存在 Zookeeper 中，以在 Livy 伺服器回復之後復原。

當透過 Livy 提交大量的作業時，Livy 伺服器的高可用性會將這些會話狀態儲存在 HDInsight 叢集上的 ZK (中) 並在 Livy 服務重新開機時復原那些會話。 在非預期的終止後重新開機時，Livy 會為每個會話建立一個執行緒，這會累積特定數目的復原會話，導致建立的執行緒太多。

### <a name="resolution"></a>解決方案

使用下面詳述的步驟來刪除所有專案。

1. 使用 < 取得 zookeeper 節點的 IP 位址

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. 上述命令列出我的叢集的所有 zookeeper

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. 使用 ping 取得 zookeeper 節點的所有 IP 位址，或者您也可以使用 zk 名稱從前端節點連線至 zookeeper

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. 連接至 zookeeper 之後，請執行下列命令來列出所有嘗試重新開機的會話。

    1. 大部分情況下，這可能是超過8000個會話的清單####

        ```bash
        ls /livy/v1/batch
        ```

    1. 下列命令會移除所有復原中的會話。 #####

        ```bash
        rmr /livy/v1/batch
        ```

1. 等候上述命令完成，並將游標傳回提示，然後從 Ambari 重新開機 Livy 服務，這應該會成功。

> [!NOTE]
> `DELETE` livy 會話完成其執行之後。 當 spark 應用程式完成（依設計）時，將不會自動刪除 Livy 批次會話。 Livy 會話是針對 Livy Rest 伺服器的 POST 要求所建立的實體。 `DELETE`需要呼叫才能刪除該實體。 或者，我們應該等候 GC 啟動。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* [Spark 記憶體管理總覽](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)。

* [在 HDInsight 叢集上將 Spark 應用程式進行偵錯工具](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)。

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
