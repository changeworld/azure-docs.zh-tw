---
title: Azure HDInsight 中的高可用性元件
description: 概述 HDInsight 叢集所使用的各種高可用性元件。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: ac63846e2679e9b4a51cb26b32415eb81a4b76ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842575"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Azure HDInsight 支援的高可用性服務

為了提供您分析元件的最佳可用性層級，HDInsight 是以獨特的架構開發而成，以確保高可用性 (HA) 的重要服務。 此架構的部分元件是由 Microsoft 所開發，以提供自動容錯移轉。 其他元件是部署來支援特定服務的標準 Apache 元件。 本文說明 HDInsight 中 HA 服務模型的架構、HDInsight 如何支援 HA 服務的容錯移轉，以及從其他服務中斷復原的最佳做法。
 
> [!NOTE]
> 無偏差通訊
>
> Microsoft 支援多樣化且 inclusionary 的環境。 本文包含單字 _從屬_的參考。 [適用于無偏差通訊的 Microsoft 樣式指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)會將此視為排他性行為單字。 本文中會使用這個字來保持一致性，因為它目前是出現在軟體中的單字。 當軟體更新為移除該字時，將會更新本文以進行調整。
>

## <a name="high-availability-infrastructure"></a>高可用性基礎結構

HDInsight 提供自訂的基礎結構，以確保四個主要服務具有自動容錯移轉功能的高可用性：

- Apache Ambari 伺服器
- 適用于 Apache YARN 的應用程式時間軸伺服器
- Hadoop MapReduce 的作業歷程記錄伺服器
- Apache Livy

此基礎結構包含許多服務和軟體元件，其中有些是由 Microsoft 所設計。 下列元件對 HDInsight 平臺而言是唯一的：

- 從屬容錯移轉控制器
- 主要容錯移轉控制器
- 從屬高可用性服務
- 主要高可用性服務

![高可用性基礎結構](./media/hdinsight-high-availability-components/high-availability-architecture.png)

另外還有其他高可用性服務，可由開放原始碼的 Apache 可靠性元件支援。 這些元件也會出現在 HDInsight 叢集上：

- Hadoop 檔案系統 (HDFS) NameNode
- YARN ResourceManager
- HBase Master

下列各節將提供有關這些服務如何搭配運作的詳細資料。

## <a name="hdinsight-high-availability-services"></a>HDInsight 高可用性服務

Microsoft 會在 HDInsight 叢集的下表中提供四種 Apache 服務的支援。 為了區別它們與 Apache 元件所支援的高可用性服務，它們稱為「 *HDINSIGHT HA 服務*」。

| 服務 | 叢集節點 | 叢集類型 | 目的 |
|---|---|---|---|
| Apache Ambari 伺服器| 主動前端節點 | 全部 | 監視和管理叢集。|
| 適用于 Apache YARN 的應用程式時間軸伺服器 | 主動前端節點 | Kafka 以外的所有 | 維護在叢集上執行之 YARN 作業的調試資訊。|
| Hadoop MapReduce 的作業歷程記錄伺服器 | 主動前端節點 | Kafka 以外的所有 | 維護 MapReduce 工作的偵錯工具資料。|
| Apache Livy | 主動前端節點 | Spark | 可透過 REST 介面輕鬆與 Spark 叢集互動 |

>[!Note]
> HDInsight 企業安全性套件 (ESP) 叢集目前僅提供 Ambari 伺服器高可用性。

### <a name="architecture"></a>架構

每個 HDInsight 叢集會分別有兩個作用中和待命模式的前端節點。 HDInsight HA 服務只會在前端節點上執行。 這些服務應該一律在作用中的前端節點上執行，並在待命前端節點上停止並進入維護模式。

為了維護 HA 服務的正確狀態，並提供快速的容錯移轉，HDInsight 會利用 Apache ZooKeeper （這是分散式應用程式的協調服務）來進行主動前端節點選舉。 HDInsight 也會提供一些背景 JAVA 進程，以協調 HDInsight HA 服務的容錯移轉程式。 這些服務如下：主要容錯移轉控制器、從屬容錯移轉控制器、 *主要-ha 服務*和 *從屬 ha 服務*。

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper 是適用于分散式應用程式的高效能協調服務。 在生產環境中，ZooKeeper 通常會在複寫模式下執行，其中 ZooKeeper 伺服器的複寫群組會形成仲裁。 每個 HDInsight 叢集都有三個 ZooKeeper 節點，可讓三個 ZooKeeper 伺服器形成仲裁。 HDInsight 有兩個與彼此平行執行的 ZooKeeper 仲裁。 其中一個仲裁會決定應執行 HDInsight HA 服務之叢集中的作用中節點。 另一個仲裁是用來協調 Apache 所提供的 HA 服務，如稍後章節所述。

### <a name="slave-failover-controller"></a>從屬容錯移轉控制器

從屬容錯移轉控制器會在 HDInsight 叢集中的每個節點上執行。 此控制器負責在每個節點上啟動 Ambari 代理程式和 *從屬 ha 服務* 。 它會定期查詢主動前端節點的第一個 ZooKeeper 仲裁。 當主動和待命前端節點變更時，從屬容錯移轉控制器會執行下列動作：

1. 更新主機設定檔。
1. 重新開機 Ambari 代理程式。

*從屬 ha 服務*負責停止 HDInsight ha 服務 (除了待命前端節點上的 Ambari 伺服器) 。

### <a name="master-failover-controller"></a>主要容錯移轉控制器

主要容錯移轉控制器會在這兩個前端節點上執行。 這兩個主要容錯移轉控制器都會與第一個 ZooKeeper 仲裁通訊，以提名作為作用中節點的前端節點。

例如，如果前端節點0上的主要容錯移轉控制器獲勝選舉，則會發生下列變更：

1. 前端節點0變成作用中狀態。
1. 主要容錯移轉控制器會在前端節點0上啟動 Ambari 伺服器和 *主要-ha 服務* 。
1. 另一個主要容錯移轉控制器會停止前端節點1上的 Ambari 伺服器和 *主要-ha 服務* 。

Master-ha 服務只會在作用中的前端節點上執行，它會在待命前端節點上停止 Ambari server) 以外的 HDInsight HA 服務 (，並在作用中的前端節點上啟動它們。

### <a name="the-failover-process"></a>容錯移轉程式

![容錯移轉程序](./media/hdinsight-high-availability-components/failover-steps.png)

健康狀態監視器會在每個前端節點和主要容錯移轉控制器上執行，以傳送通知給 Zookeeper 仲裁。 在此案例中，前端節點會被視為 HA 服務。 健全狀況監視器會檢查每個高可用性服務是否狀況良好，以及是否已準備好加入領導選舉。 如果是，此前端節點將會在選舉中競爭。 如果沒有，則會結束選舉，直到它再次準備就緒為止。

如果待命前端節點仍可達成領導性並變成作用中 (例如，在先前的作用中節點) 發生失敗的情況下，其主要容錯移轉控制器將會啟動其上的所有 HDInsight HA 服務。 主要容錯移轉控制器也會在其他前端節點上停止這些服務。

針對 HDInsight HA 服務失敗（例如服務關閉或狀況不良），主要容錯移轉控制器應該會根據前端節點狀態自動重新開機或停止服務。 使用者不應該在這兩個前端節點上手動啟動 HDInsight HA 服務。 相反地，允許自動或手動容錯移轉來協助服務復原。

### <a name="inadvertent-manual-intervention"></a>不慎手動介入

HDInsight HA 服務應該只會在作用中的前端節點上執行，而且會在必要時自動重新開機。 由於個別 HA 服務沒有自己的健康情況監視，因此無法在個別服務的層級觸發容錯移轉。 在節點層級，而不是在服務層級上，可確保容錯移轉。

### <a name="some-known-issues"></a>一些已知問題

- 當您在待命前端節點上手動啟動 HA 服務時，它將不會停止，直到下一次容錯移轉發生為止。 當 HA 服務在這兩個前端節點上執行時，有一些潛在的問題包括：無法存取 Ambari UI、Ambari 擲回錯誤、YARN、Spark 和 Oozie 工作可能會停滯。

- 當主動前端節點上的 HA 服務停止時，它將不會重新開機，直到發生下一次容錯移轉或主要容錯移轉控制器/主要-HA 服務重新開機為止。 當一或多個 HA 服務在使用中的前端節點上停止時（特別是在 Ambari 伺服器停止時），Ambari UI 無法存取，其他潛在的問題包括 YARN、Spark 和 Oozie 作業失敗。

## <a name="apache-high-availability-services"></a>Apache 高可用性服務

Apache 提供 HDFS NameNode、YARN ResourceManager 和 HBase Master 的高可用性，這些功能也可在 HDInsight 叢集中使用。 與 HDInsight HA 服務不同的是，ESP 叢集中支援它們。 Apache HA 服務會與上一節所述的第二個 ZooKeeper 仲裁 (進行通訊，) 選擇使用中/待命狀態並進行自動容錯移轉。 下列各節將詳細說明這些服務的運作方式。

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop 分散式檔案系統 (HDFS) NameNode

以 Apache Hadoop 2.0 或更高版本為基礎的 HDInsight 叢集可提供 NameNode 的高可用性。 前端節點上執行兩個 Namenode，其設定為自動容錯移轉。 Namenode 會使用 *ZKFailoverController* 與 Zookeeper 進行通訊，以選擇使用中/待命狀態。 *ZKFailoverController*會在兩個前端節點上執行，並以上述主要容錯移轉控制器的相同方式運作。

第二個 Zookeeper 仲裁與第一個仲裁無關，因此使用中的 NameNode 可能無法在作用中的前端節點上執行。 當作用中的 NameNode 為「無作用」或「狀況不良」時，待命 NameNode 會贏得選舉並變成作用中狀態。

### <a name="yarn-resourcemanager"></a>YARN ResourceManager

以 Apache Hadoop 2.4 或更高版本為基礎的 HDInsight 叢集，支援 YARN ResourceManager 高可用性。 前端節點0和前端節點1分別執行兩個 ResourceManagers、rm1 和 rm2。 如同 NameNode，YARN ResourceManager 也會設定為自動容錯移轉。 當目前作用中的 ResourceManager 停止或沒有回應時，會自動將另一個 ResourceManager 選擇為作用中狀態。

YARN ResourceManager 使用其內嵌 *ActiveStandbyElector* 作為失敗偵測器和領導者 elector。 不同于 HDFS NameNode，YARN ResourceManager 不需要個別的 ZKFC daemon。 作用中 ResourceManager 會將其狀態寫入至 Apache Zookeeper。

YARN ResourceManager 的高可用性與 NameNode 和其他 HDInsight HA 服務無關。 作用中的 ResourceManager 可能無法在作用中的前端節點或作用中 NameNode 執行所在的前端節點上執行。 如需 YARN ResourceManager 高可用性的詳細資訊，請參閱 [Resourcemanager 高可用性](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)。

### <a name="hbase-master"></a>HBase Master

HDInsight HBase 叢集支援 HBase Master 高可用性。 不同于在前端節點上執行的其他 HA 服務，HBase 主機會在三個 Zookeeper 節點上執行，其中一個是作用中的主機，而另二個是待命。 如同 NameNode，HBase Master 與 Apache Zookeeper 協調以進行領導人選舉，並在目前作用中的主機發生問題時進行自動容錯移轉。 每次只能有一個作用中的 HBase Master。

## <a name="next-steps"></a>後續步驟

- [HDInsight 中 Apache Hadoop 叢集的可用性和可靠性](hdinsight-high-availability-linux.md)
- [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)
