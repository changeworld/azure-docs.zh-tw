---
title: 使用 Azure HDInsight 為 Storm 進行疑難排解
description: 取得有關使用 Apache Storm 和 Azure HDInsight 的常見問題解答。
keywords: Azure HDInsight, Storm, 常見問題集, 疑難排解指南, 常見問題
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: fc08adb2f71c49fad3c8574e9cfd5315f13d7f44
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532759"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>使用 Azure HDInsight 為 Apache Storm 進行疑難排解

瞭解在[Apache Ambari](https://ambari.apache.org/)中使用[Apache Storm](https://storm.apache.org/)承載的最重要問題及其解決方法。

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>如何在叢集上存取 Storm UI？

您有兩個選項可從瀏覽器存取 Storm UI：

### <a name="apache-ambari-ui"></a>Apache Ambari UI

1. 移至 Ambari 儀表板。
2. 在服務清單中，選取 [Storm]  。
3. 在 [快速連結]  功能表中，選取 [Storm UI]  。

### <a name="direct-link"></a>直接連結

您可以在下列 URL 存取 Storm UI：

`https://<cluster DNS name>/stormui`

範例： `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>如何將 Storm 事件中樞 Spout 檢查點資訊從一個拓撲傳輸到另一個拓撲？

當您使用 HDInsight Storm 事件中樞 Spout .jar 檔案開發讀取自 Azure 事件中樞的拓撲時，您必須在新的叢集上部署具有相同名稱的拓撲。 不過，您必須在舊叢集上保留認可至 [Apache ZooKeeper](https://zookeeper.apache.org/) 的檢查點資料。

### <a name="where-checkpoint-data-is-stored"></a>檢查點資料的儲存位置

事件中樞 Spout 會將位移的檢查點資料儲存在 ZooKeeper 的兩個根路徑中：

- 非交易式 spout 檢查點會儲存在中 `/eventhubspout` 。

- 交易式 spout 檢查點資料儲存在中 `/transactional` 。

### <a name="how-to-restore"></a>還原方式

若要取得您用來從 ZooKeeper 匯出資料，再以新名稱將資料匯回 ZooKeeper 的指令碼和程式庫，請參閱 [HDInsight Storm 範例](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)。

lib 資料夾的 .jar 檔案包含匯出/匯入作業的實作。 bash 資料夾的範例指令碼，示範如何從舊叢集的 ZooKeeper 伺服器匯出資料，再將它匯回新叢集的 ZooKeeper 伺服器。

從 ZooKeeper 節點執行 [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) 指令碼以匯出再匯入資料。 將指令碼更新為正確的 Hortonworks Data Platform (HDP) 版本。 (我們正致力於讓這些指令碼在 HDInsight 中成為泛型。 泛型指令碼可以從叢集的任何節點執行，不需要使用者修改。)

匯出命令會將中繼資料寫入至您設定之位置上的 Apache Hadoop 分散式檔案系統 (HDFS) 路徑 (位於 Azure Blob 儲存體或 Azure Data Lake Storage)。

### <a name="examples"></a>範例

#### <a name="export-offset-metadata"></a>匯出位移中繼資料

1. 使用 SSH 移至叢集的 ZooKeeper 叢集，該舊叢集需要匯出檢查點位移。
2. 在您更新 HDP 版本字串之後，執行下列命令 () 將 ZooKeeper 位移資料匯出至 `/stormmetadta/zkdata` HDFS 路徑：

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>匯入位移中繼資料

1. 使用 SSH 移至叢集的 ZooKeeper 叢集，該舊叢集需要匯入檢查點位移。
2. 在您更新 HDP 版本字串之後，執行下列命令 () 將 ZooKeeper 位移資料從 HDFS 路徑匯入到 `/stormmetadata/zkdata` 目標叢集上的 ZooKeeper 伺服器：

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>刪除位移中繼資料，以便拓撲可以從頭開始處理資料，或從使用者選擇的時間戳記開始處理

1. 使用 SSH 移至叢集的 ZooKeeper 叢集，該舊叢集需要刪除檢查點位移。
2. 執行下列命令 (在您更新 HDP 版本字串之後)，刪除目前叢集中的所有 ZooKeeper 位移資料：

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>如何找出叢集上的 Storm 二進位檔？

目前 HDP 堆疊的風暴二進位檔位於 `/usr/hdp/current/storm-client` 。 前端節點和背景工作節點使用相同的位置。

/Usr/hdp 中的特定 HDP 版本可能會有多個二進位檔 (例如 `/usr/hdp/2.5.0.1233/storm`) 。 此 `/usr/hdp/current/storm-client` 資料夾會 symlinked 至叢集中正在執行的最新版本。

如需詳細資訊，請參閱[使用 SSH 連線到 HDInsight 叢集](../hdinsight-hadoop-linux-use-ssh-unix.md)和 [Apache Storm](https://storm.apache.org/) \(英文\)。

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>如何判斷 Storm 叢集的部署拓撲？

首先，找出所有與 HDInsight Storm 一起安裝的元件。 Storm 叢集包含四個節點類別：

* 閘道節點
* 前端節點
* ZooKeeper 節點
* 背景工作節點

### <a name="gateway-nodes"></a>閘道節點

閘道節點是閘道與反向 Proxy 服務，可讓您啟用使用中的 Ambari 管理服務公用存取。 它也可以處理 Ambari 前置選擇。

### <a name="head-nodes"></a>前端節點

Storm 前端節點執行下列服務：
* Nimbus
* Ambari 伺服器
* Ambari 計量伺服器
* Ambari 計量收集器
 
### <a name="zookeeper-nodes"></a>ZooKeeper 節點

HDInsight 隨附一個三節點的 ZooKeeper 仲裁。 仲裁大小是固定的，且無法重新設定。

叢集中的 Storm 服務已設定為自動使用 ZooKeeper 仲裁。

### <a name="worker-nodes"></a>背景工作節點

Storm 背景工作節點執行下列服務：
* 監督員
* 背景工作 Java 虛擬機器 (JVM)，用於執行拓樸
* Ambari 代理程式

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>如何找出用於開發的 Storm 事件中樞 Spout 二進位檔？

如需對您的拓撲使用 Storm 事件中樞 Spout .jar 檔案的詳細資訊，請參閱下列資源。

### <a name="java-based-topology"></a>Java 型拓撲

[使用 Apache Storm on HDInsight 處理 Azure 事件中樞的事件 (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# 型拓撲 (HDInsight 3.4+ Linux Storm 叢集上的 Mono)

[使用 Apache Storm on HDInsight 處理 Azure 事件中樞的事件 (C#)](./apache-storm-develop-csharp-event-hub-topology.md)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>適用於 HDInsight 3.5+ Linux Storm 叢集的最新 Apache Storm 事件中樞 Spout 二進位檔

若要瞭解如何使用適用于 HDInsight 3.5 + Linux 風暴叢集的最新風暴事件中樞 spout，請參閱 mvn 存放庫的 [讀我檔案](https://github.com/hdinsight/mvn-repo/blob/master/README.md)。

### <a name="source-code-examples"></a>原始程式碼範例

請參閱[範例](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)，以了解如何使用 Azure HDInsight 叢集上的 Apache Storm 拓撲 (以 Java 撰寫)，來從 Azure 事件中樞進行讀取和寫入。

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>如何找出叢集上的 Storm Log4J 2 設定檔？

識別 Storm 服務的 [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) 設定檔。

### <a name="on-head-nodes"></a>在前端節點上

從讀取 Nimbus Log4J 設定 `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` 。

### <a name="on-worker-nodes"></a>在背景工作節點上

您可以從讀取監督員 Log4J 設定 `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` 。

背景工作 Log4J 設定檔會從讀取 `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml` 。

例子： `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>不是領導者例外狀況

提交拓撲時，使用者可能會收到類似下列的錯誤訊息： `Topology submission exception, cause not a leader, the current leader is NimbusInfo` 。

若要解決這個問題，使用者可能需要提出票證，讓節點重新開機/重新開機。 如需詳細資訊，請參閱 [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html)。

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

- 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

- 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。