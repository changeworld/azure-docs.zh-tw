---
title: Azure HDInsight 中 Apache Spark 的記憶體錯誤異常
description: Azure HDInsight 中 Apache Spark 群集的各種記憶體外錯誤異常
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271963"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Spark 的記憶體錯誤異常

本文介紹了在 Azure HDInsight 群集中使用 Apache Spark 元件時，故障排除步驟和可能解決的問題。

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>方案：Apache Spark 的記憶體錯誤異常

### <a name="issue"></a>問題

Apache Spark 應用程式失敗，記憶體錯誤未處理異常。 您可能會收到類似于以下錯誤的錯誤訊息：

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

此例外狀況最可能發生的原因，就是未配置足夠的堆積記憶體給 Java 虛擬機器 (JVM)。 這些 JVM 作為 Apache Spark 應用程式的一部分作為執行器或驅動程式啟動。

### <a name="resolution"></a>解決方案

1. 決定 Spark 應用程式將處理的資料大小上限。 根據輸入資料的最大大小、通過轉換輸入資料和生成輸出資料的中間資料來估計大小，從而進一步轉換中間資料。 如果初始估計不夠，則稍微增加大小，並反覆運算直到記憶體錯誤消退。

1. 請確定要使用的 HDInsight 叢集具有足夠的記憶體資源及核心，才能採用 Spark 應用程式。 這可以通過查看群集 YARN UI 的"群集"的"群集"指標部分來確定，這些值為 **"使用的記憶體****總數**"和 **"使用的 VCore"****與"VCores 總計**"的值。

    ![紗線核心記憶體視圖](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. 將以下 Spark 配置設置為適當的值。 平衡應用程式要求與群集中的可用資源。 這些值不應超過 YARN 查看的可用記憶體和內核的 90%，並且還應滿足 Spark 應用程式的最低記憶體要求：

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

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>方案：嘗試打開 Apache Spark 歷史記錄伺服器時JAVA堆空間錯誤

### <a name="issue"></a>問題

在 Spark 歷史記錄伺服器中打開事件時，您會收到以下錯誤：

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>原因

此問題通常是由於打開大型火花事件檔時缺少資源造成的。 預設情況下，Spark 堆大小設置為 1 GB，但大型 Spark 事件檔可能需要的容量可能高於此。

如果要驗證嘗試載入的檔的大小，可以執行以下命令：

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>解決方案

您可以通過編輯 Spark 配置中`SPARK_DAEMON_MEMORY`的屬性並重新啟動所有服務來增加 Spark 歷史記錄伺服器記憶體。

您可以通過選擇 Spark2/Config/高級 spark2-env 部分，在 Ambari 瀏覽器 UI 中執行此操作。

![高級火花2-env部分](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

添加以下屬性以將 Spark 歷史記錄伺服器記憶體從 1g 更改為`SPARK_DAEMON_MEMORY=4g`4g： 。

![火花屬性](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

請確保從 Ambari 重新開機所有受影響的服務。

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>方案：利維伺服器無法在 Apache Spark 群集上啟動

### <a name="issue"></a>問題

利維伺服器無法在 Apache Spark 上啟動 [（Linux 上的 Spark 2.1 （HDI 3.6）]。 嘗試重新開機會導致以下錯誤堆疊，從 Livy 日誌：

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

`java.lang.OutOfMemoryError: unable to create new native thread`突出顯示作業系統無法將更多本機執行緒分配給 JVM。 確認此異常是由違反每個進程執行緒計數限制引起的。

當 Livy Server 意外終止時，與 Spark 群集的所有連接也將終止，這意味著所有作業和相關資料都將丟失。 在引入 HDP 2.6 會話恢復機制中，Livy 將會話詳細資訊存儲在 Zookeeper 中，以便在 Livy 伺服器返回後恢復。

當通過 Livy 提交大量作業時，作為 Livy 伺服器高可用性的一部分，這些會話狀態存儲在 ZK（HDInsight 群集上），並在重新開機 Livy 服務時恢復這些會話。 意外終止後重新開機時，Livy 會每個會話創建一個執行緒，這將累積一定數量的要恢復的會話，從而導致創建的執行緒過多。

### <a name="resolution"></a>解決方案

使用下面詳述的步驟刪除所有條目。

1. 使用獲取動物園管理員節點的 IP 位址

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. 上面的命令列出了我的集群的所有動物園管理員

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. 使用 ping 獲取動物園管理員節點的所有 IP 位址，或者您也可以使用 zk 名稱從頭節點連接到動物園管理員

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. 連接到動物園管理員後，將執行以下命令，列出嘗試重新開機的所有會話。

    1. 在大多數情況下，這可能是一個清單超過 8000 會話####

        ```bash
        ls /livy/v1/batch
        ```

    1. 以下命令是刪除所有要恢復的會話。 #####

        ```bash
        rmr /livy/v1/batch
        ```

1. 等待上述命令完成，游標返回提示，然後從 Ambari 重新開機 Livy 服務，該服務應成功。

> [!NOTE]
> `DELETE`livy 會話完成其執行後。 Spark 應用完成後，Livy 批次處理會話不會自動刪除，這是設計上。 Livy 會話是由 POST 請求針對 Livy Rest 伺服器創建的實體。 需要`DELETE`調用才能刪除該實體。 或者我們應該等待 GC 啟動。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* [火花記憶體管理概述](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)。

* [在 HDInsight 群集上調試 Spark 應用程式](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)。

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
