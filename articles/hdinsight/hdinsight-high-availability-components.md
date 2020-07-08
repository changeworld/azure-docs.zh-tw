---
title: Azure HDInsight 中的高可用性元件
description: 瞭解 HDInsight 叢集所使用的各種高可用性元件。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: e1da26d9067427734d407451bdb53e51ba1e6243
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84609160"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Azure HDInsight 支援高可用性服務

 為了為您的分析元件提供最佳的可用性層級，HDInsight 是以獨特的架構來開發，以確保重要服務的高可用性（HA）。 此架構的某些元件是由 Microsoft 所開發，以提供自動容錯移轉。 其他元件則是部署來支援特定服務的標準 Apache 元件。 本文說明 HDInsight 中 HA 服務模型的架構、HDInsight 如何支援 HA 服務的容錯移轉，以及從其他服務中斷中復原的最佳作法。
 
> [!NOTE]
> 偏差-免費通訊
>
> Microsoft 支援多樣化和 inclusionary 的環境。 本文包含對_一詞的_參考。 [適用于無偏差通訊的 Microsoft 樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)可辨識此為 exclusionary 單字。 本文中會使用這個字來進行一致性，因為它目前是出現在軟體中的單字。 當軟體更新為移除此單字時，此文章將會更新為對齊。
>


## <a name="high-availability-infrastructure"></a>高可用性基礎結構

HDInsight 提供自訂的基礎結構，以確保四個主要服務具有自動容錯移轉功能的高可用性：

- Apache Ambari 伺服器
- 適用于 Apache YARN 的應用程式時間軸伺服器
- Hadoop MapReduce 的作業歷程記錄伺服器
- Apache Livy

此基礎結構是由許多服務和軟體元件所組成，其中有些是由 Microsoft 所設計。 下列是 HDInsight 平臺獨有的元件：

- 從屬容錯移轉控制器
- 主要容錯移轉控制器
- 從屬高可用性服務
- 主要高可用性服務

![高可用性基礎結構](./media/hdinsight-high-availability-components/high-availability-architecture.png)

另外還有其他高可用性服務，由開放原始碼 Apache 可靠性元件支援。 這些元件也會出現在 HDInsight 叢集上：

- Hadoop 檔案系統（HDFS） NameNode
- YARN ResourceManager
- HBase Master

下列各節將提供有關這些服務如何搭配使用的詳細資訊。

## <a name="hdinsight-high-availability-services"></a>HDInsight 高可用性服務

Microsoft 會在 HDInsight 叢集的下表中提供這四種 Apache 服務的支援。 為了與 Apache 中元件所支援的高可用性服務區別，它們稱為「 *HDINSIGHT HA 服務*」。

| 服務 | 叢集節點 | 叢集類型 | 目的 |
|---|---|---|---|
| Apache Ambari 伺服器| 現用前端節點 | 全部 | 監視及管理叢集。|
| 適用于 Apache YARN 的應用程式時間軸伺服器 | 現用前端節點 | 除了 Kafka 以外的所有 | 維護有關在叢集上執行之 YARN 作業的調試資訊。|
| Hadoop MapReduce 的作業歷程記錄伺服器 | 現用前端節點 | 除了 Kafka 以外的所有 | 維護 MapReduce 工作的偵錯工具資料。|
| Apache Livy | 現用前端節點 | Spark | 透過 REST 介面讓 Spark 叢集輕鬆進行互動 |

>[!Note]
> HDInsight 企業安全性套件（ESP）叢集目前僅提供 Ambari 伺服器高可用性。

### <a name="architecture"></a>架構

每個 HDInsight 叢集分別有兩個前端節點處於作用中和待命模式。 HDInsight HA 服務只會在前端節點上執行。 這些服務應該一律在使用中的前端節點上執行，並在待命前端節點上停止和進入維護模式。

為了維護 HA 服務的正確狀態並提供快速容錯移轉，HDInsight 會利用 Apache ZooKeeper （這是分散式應用程式的協調服務）來進行主動前端節點選舉。 HDInsight 也會布建一些背景 JAVA 程式，這會協調 HDInsight HA 服務的容錯移轉程式。 這些服務如下：主要容錯移轉控制器、從屬容錯移轉控制器、*主要-ha 服務*和*從屬-ha 服務*。

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper 是分散式應用程式的高效能協調服務。 在生產環境中，ZooKeeper 通常會在複寫模式中執行，其中 ZooKeeper 伺服器的複寫群組會形成仲裁。 每個 HDInsight 叢集都有三個 ZooKeeper 節點，可讓三部 ZooKeeper 伺服器形成仲裁。 HDInsight 有兩個彼此平行執行的 ZooKeeper 仲裁。 一個仲裁會決定要在其中執行 HDInsight HA 服務的叢集中使用中的前端節點。 另一個仲裁用於協調 Apache 所提供的 HA 服務，如後續章節所述。

### <a name="slave-failover-controller"></a>從屬容錯移轉控制器

從屬容錯移轉控制器會在 HDInsight 叢集中的每個節點上執行。 此控制器負責啟動每個節點上的 Ambari 代理程式和*從屬-ha 服務*。 它會定期查詢有關作用中前端節點的第一個 ZooKeeper 仲裁。 當 [作用中] 和 [待命] 前端節點變更時，從屬容錯移轉控制器會執行下列動作：

1. 更新主機設定檔。
1. 重新開機 Ambari 代理程式。

*從屬-ha 服務*負責停止待命前端節點上的 HDInsight ha 服務（Ambari 伺服器除外）。

### <a name="master-failover-controller"></a>主要容錯移轉控制器

主要容錯移轉控制器會在這兩個前端節點上執行。 這兩個主要容錯移轉控制器都會與第一個 ZooKeeper 仲裁通訊，以提名作為作用中前端節點的前端節點。

例如，如果前端節點上的主要容錯移轉控制器是在選舉的 wins 上進行，則會發生下列變更：

1. 前端節點0會變成作用中狀態。
1. 主要容錯移轉控制器會啟動 Ambari 伺服器，以及前端節點0上的*主要-ha 服務*。
1. 另一個主要容錯移轉控制器會停止 Ambari 伺服器和前端節點1上的*主要-ha 服務*。

主要-ha 服務只會在作用中的前端節點上執行，它會停止待命前端節點上的 HDInsight HA 服務（除了 Ambari 伺服器以外），並在作用中的前端節點上啟動它們。

### <a name="the-failover-process"></a>容錯移轉程式

![容錯移轉程序](./media/hdinsight-high-availability-components/failover-steps.png)

健全狀況監視器會在每個前端節點上執行，以及主要容錯移轉控制器，以將活動訊號通知傳送至 Zookeeper 仲裁。 在此案例中，前端節點會被視為 HA 服務。 健全狀況監視會檢查每個高可用性服務是否狀況良好，以及是否已準備好加入領導性選舉。 如果是，此前端節點會參與選舉。 如果沒有，則會結束選舉，直到它再次準備就緒為止。

如果待命前端節點已達到領導能力並變成作用中（例如，在前一個作用中節點發生失敗的情況下），其主要容錯移轉控制器將會啟動其上的所有 HDInsight HA 服務。 主要容錯移轉控制器也會在其他前端節點上停止這些服務。

對於 HDInsight HA 服務失敗，例如服務已關閉或狀況不良，主要容錯移轉控制器應該會根據前端節點狀態自動重新開機或停止服務。 使用者不應在這兩個前端節點上手動啟動 HDInsight HA 服務。 相反地，請允許自動或手動容錯移轉，以協助服務復原。

### <a name="inadvertent-manual-intervention"></a>意外的手動介入

HDInsight HA 服務應該只在使用中的前端節點上執行，並會在必要時自動重新開機。 因為個別的 HA 服務沒有自己的健康狀態監視器，所以無法在個別服務的層級觸發容錯移轉。 容錯移轉會在節點層級進行，而不是在服務層級。

### <a name="some-known-issues"></a>一些已知問題

- 在待命前端節點上手動啟動 HA 服務時，在下一次容錯移轉發生之前，它不會停止。 當 HA 服務同時在這兩個前端節點上執行時，部分可能的問題包括：無法存取 Ambari UI、Ambari 擲回錯誤、YARN、Spark 和 Oozie 工作可能會停滯。

- 當作用中前端節點上的 HA 服務停止時，它將不會重新開機，直到發生下一次容錯移轉或主要的容錯移轉控制器/主要-HA 服務重新開機為止。 當一個或多個 HA 服務在作用中的前端節點上停止時，特別是當 Ambari 伺服器停止時，無法存取 Ambari UI，其他可能的問題包括 YARN、Spark 和 Oozie 作業失敗。

## <a name="apache-high-availability-services"></a>Apache 高可用性服務

Apache 提供 HDFS NameNode、YARN ResourceManager 和 HBase Master 的高可用性，這些也適用于 HDInsight 叢集。 不同于 HDInsight HA 服務，ESP 叢集中支援它們。 Apache HA 服務會與第二個 ZooKeeper 仲裁（如上一節所述）進行通訊，以選擇作用中/待命狀態並進行自動容錯移轉。 下列各節將詳細說明這些服務的使用方式。

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop 分散式檔案系統（HDFS） NameNode

以 Apache Hadoop 2.0 或更高版本為基礎的 HDInsight 叢集，可提供 NameNode 的高可用性。 前端節點上有兩個 Namenode 正在執行，其設定為自動容錯移轉。 Namenode 會使用*ZKFailoverController*與 Zookeeper 進行通訊，以選擇作用中/待命狀態。 *ZKFailoverController*會在這兩個前端節點上執行，並以與上述主要容錯移轉控制器相同的方式運作。

第二個 Zookeeper 仲裁與第一個仲裁無關，因此作用中的 NameNode 可能無法在使用中的前端節點上執行。 當作用中的 NameNode 沒有作用或狀況不良時，待命 NameNode 會贏得選舉並變成作用中狀態。

### <a name="yarn-resourcemanager"></a>YARN ResourceManager

以 Apache Hadoop 2.4 或更高版本為基礎的 HDInsight 叢集，支援 YARN ResourceManager 高可用性。 有兩個 ResourceManagers （rm1 和 rm2）分別在前端節點0和前端節點1上執行。 如同 NameNode，YARN ResourceManager 也設定為自動容錯移轉。 當目前的作用中 ResourceManager 關閉或沒有回應時，會自動將另一個 ResourceManager 選擇為作用中狀態。

YARN ResourceManager 會使用其內嵌*ActiveStandbyElector*作為失敗偵測器和領導者 elector。 不同于 HDFS NameNode，YARN ResourceManager 不需要個別的 ZKFC daemon。 Active ResourceManager 會將其狀態寫入 Apache Zookeeper。

YARN ResourceManager 的高可用性與 NameNode 和其他 HDInsight HA 服務無關。 Active ResourceManager 可能無法在使用中的前端節點或作用中 NameNode 執行所在的前端節點上執行。 如需 YARN ResourceManager 高可用性的詳細資訊，請參閱[Resourcemanager 高可用性](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)。

### <a name="hbase-master"></a>HBase Master

HDInsight HBase 叢集支援 HBase Master 高可用性。 與其他在前端節點上執行的 HA 服務不同的是，HBase master 會在三個 Zookeeper 節點上執行，其中其中一個是作用中的主機，另兩個則是待命。 如同 NameNode，HBase Master 與 Apache Zookeeper 協調以進行領導者選擇，並在目前作用中的主伺服器發生問題時進行自動容錯移轉。 任何時候都只會有一個作用中的 HBase Master。

## <a name="next-steps"></a>後續步驟

- [HDInsight 中 Apache Hadoop 叢集的可用性和可靠性](hdinsight-high-availability-linux.md)
- [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)
