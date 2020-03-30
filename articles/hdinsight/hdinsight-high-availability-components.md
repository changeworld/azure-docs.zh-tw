---
title: Azure HDInsight 中的高可用性元件
description: HDInsight 群集使用的各種高可用性元件概述。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74069636"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Azure HDInsight 支援的高可用性服務

 為了為您的分析元件提供最佳的可用性，HDInsight 開發了獨特的體系結構，可確保關鍵服務的高可用性 （HA）。 此體系結構的某些元件由 Microsoft 開發，以提供自動容錯移轉。 其他元件是部署以支援特定服務的標準 Apache 元件。 本文介紹了 HDInsight 中的 HA 服務模型的體系結構、HDInsight 如何支援 HA 服務的容錯移轉，以及從其他服務中斷中恢復的最佳做法。

## <a name="high-availability-infrastructure"></a>高可用性基礎架構

HDInsight 提供自訂基礎架構，以確保四個主要服務具有高可用性，具有自動容錯移轉功能：

- 阿帕奇·安巴里伺服器
- 適用于 Apache YARN 的應用程式時間表伺服器
- Hadoop 地圖減少的作業歷史記錄伺服器
- Apache Livy

此基礎結構由許多服務和軟體元件組成，其中一些元件由 Microsoft 設計。 以下元件是 HDInsight 平臺獨有的：

- 從機容錯移轉控制器
- 主容錯移轉控制器
- 從屬高可用性服務
- 主高可用性服務

![高可用性基礎架構](./media/hdinsight-high-availability-components/high-availability-architecture.png)

還有其他高可用性服務，這些服務由開源 Apache 可靠性元件支援。 HDInsight 群集上也存在這些元件：

- 哈多普檔案系統 （HDFS） 名稱節點
- YARN 資源管理器
- HBase 母版

以下各節將提供有關這些服務如何協同工作的更多細節。

## <a name="hdinsight-high-availability-services"></a>HDInsight 高可用性服務

Microsoft 在 HDInsight 群集中為下表中的四個 Apache 服務提供支援。 為了將它們與元件支援的高可用性服務與 Apache 支援的高可用性服務區分開來，它們稱為*HDInsight HA 服務*。

| 服務 | 叢集節點 | 叢集類型 | 目的 |
|---|---|---|---|
| 阿帕奇·安巴里伺服器| 活動頭節點 | 全部 | 監視和管理群集。|
| 適用于 Apache YARN 的應用程式時間表伺服器 | 活動頭節點 | 除卡夫卡外 | 維護有關在群集上運行的 YARN 作業的調試資訊。|
| Hadoop 地圖減少的作業歷史記錄伺服器 | 活動頭節點 | 除卡夫卡外 | 維護 MapReduce 作業的調試資料。|
| Apache Livy | 活動頭節點 | Spark | 通過 REST 介面輕鬆與 Spark 群集交互 |

>[!Note]
> HDInsight 企業安全包 （ESP） 群集目前僅提供 Ambari 伺服器高可用性。

### <a name="architecture"></a>架構

每個 HDInsight 群集分別在活動模式和待機模式下具有兩個頭節點。 HDInsight HA 服務僅在頭節點上運行。 這些服務應始終在活動頭節點上運行，並在備用頭節點上停止並置於維護模式。

為了保持 HA 服務的正確狀態並提供快速容錯移轉，HDInsight 利用 Apache ZooKeeper（分散式應用程式的協調服務）進行主動頭節點選擇。 HDInsight 還提供了一些後臺 JAVA 進程，這些進程協調 HDInsight HA 服務的容錯移轉過程。 這些服務如下：主容錯移轉控制器、從屬容錯移轉控制器、*主 ha 服務*和*從機哈服務*。

### <a name="apache-zookeeper"></a>阿帕奇動物園管理員

Apache 動物園管理員是分散式應用程式的高性能協調服務。 在生產中，ZooKeeper 通常以複製模式運行，其中複製的動物園管理員伺服器組形成仲裁。 每個 HDInsight 群集都有三個 ZooKeeper 節點，允許三個 ZooKeeper 伺服器形成仲裁。 HDInsight 有兩個動物園管理員仲裁同時運行。 一個仲裁決定運行 HDInsight HA 服務的群集中的活動頭節點。 另一個仲裁用於協調 Apache 提供的 HA 服務，如後面的章節所述。

### <a name="slave-failover-controller"></a>從機容錯移轉控制器

從機轉移控制器在 HDInsight 群集中的每個節點上運行。 此控制器負責在每個節點上啟動 Ambari 代理和*從機位服務*。 它定期查詢有關活動頭節點的第一個動物園管理員仲裁。 當活動頭節點和備用頭節點更改時，從屬容錯移轉控制器執行以下操作：

1. 更新主機設定檔。
1. 重新開機 Ambari 代理。

*從機哈服務*負責停止待機頭節點上的 HDInsight HA 服務（Ambari 伺服器除外）。

### <a name="master-failover-controller"></a>主容錯移轉控制器

主容錯移轉控制器在兩個頭節點上運行。 兩個主容錯移轉控制器都與第一個 ZooKeeper 仲裁通信，以指定他們運行的頭節點作為活動頭節點。

例如，如果 head節點 0 上的主容錯移轉控制器贏得選舉，則進行以下更改：

1. 頭節點 0 變為活動狀態。
1. 主容錯移轉控制器啟動 Ambari 伺服器和頭節點 0 上的*主 ha 服務*。
1. 另一個主容錯移轉控制器停止安巴里伺服器和頭節點 1 上的*主 ha 服務*。

主 ha 服務僅在活動頭節點上運行，它停止待機頭節點上的 HDInsight HA 服務（Ambari 伺服器除外），並在活動頭節點上啟動這些服務。

### <a name="the-failover-process"></a>容錯移轉過程

![容錯移轉程序](./media/hdinsight-high-availability-components/failover-steps.png)

運行狀況監視器在每個頭節點上運行，並運行主容錯移轉控制器，以便向動物園管理員仲裁發送聽到的通知。 在這種情況下，頭節點被視為 HA 服務。 運行狀況監視器檢查每個高可用性服務是否正常，以及是否已準備好參加領導選舉。 如果是，這個頭節點將參加競選。 否則，它將退出選舉，直到它再次準備就緒。

如果備用頭節點達到領導狀態並變為活動狀態（例如，如果前一個活動節點發生故障，則主容錯移轉控制器將啟動其上的所有 HDInsight HA 服務。 主容錯移轉控制器也會停止其他頭節點上的這些服務。

對於 HDInsight HA 服務故障（如服務關閉或不正常），主容錯移轉控制器應根據頭節點狀態自動重新開機或停止服務。 使用者不應在兩個頭節點上手動啟動 HDInsight HA 服務。 相反，允許自動或手動容錯移轉來説明服務恢復。

### <a name="inadvertent-manual-intervention"></a>無意手動干預

HDInsight HA 服務應僅在活動頭節點上運行，並在必要時自動重新開機。 由於單個 HA 服務沒有自己的運行狀況監視器，因此無法在單個服務等級觸發容錯移轉。 容錯移轉在節點級別確保，而不是在服務等級。

### <a name="some-known-issues"></a>一些已知問題

- 在備用頭節點上手動啟動 HA 服務時，在下一次容錯移轉發生之前，它不會停止。 當兩個頭節點上運行 HA 服務時，一些潛在問題包括：Ambari UI 無法訪問，Ambari 引發錯誤，YARN、Spark 和 Oozie 作業可能會卡住。

- 當活動頭節點上的 HA 服務停止時，在下一次容錯移轉或主容錯移轉控制器/主容錯移轉控制器/主哈服務重新開機之前，它不會重新開機。 當一個或多個 HA 服務停止在活動頭節點上時，特別是當 Ambari 伺服器停止時，Ambari UI 無法訪問，其他潛在問題包括 YARN、Spark 和 Oozie 作業失敗。

## <a name="apache-high-availability-services"></a>阿帕奇高可用性服務

Apache 為 HDFS NameNode、YARN 資源管理器和 HBase 主版提供高可用性，它們在 HDInsight 群集中也提供。 與 HDInsight HA 服務不同，ESP 群集中支援這些服務。 Apache HA 服務與第二個 ZooKeeper 仲裁（如上一節所述）進行通信，以選擇活動/待機狀態並執行自動容錯移轉。 以下各節詳細介紹了這些服務的工作原理。

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop 分散式檔案系統 （HDFS） 名稱節點

基於 Apache Hadoop 2.0 或更高版本的 HDInsight 群集提供 NameNode 高可用性。 頭節點上運行兩個 NameNodes，它們配置為自動容錯移轉。 NameNodes 使用*ZKFailover 控制器*與 Zookeeper 通信，以選擇活動/待機狀態。 *ZKFailover 控制器*在兩個頭節點上運行，其工作方式與上面的主容錯移轉控制器相同。

第二個動物園管理員仲裁獨立于第一個仲裁，因此活動 NameNode 可能不會在活動頭節點上運行。 當活動 NameNode 已死或不正常時，備用 NameNode 將贏得選舉並變為活動狀態。

### <a name="yarn-resourcemanager"></a>YARN 資源管理器

基於 Apache Hadoop 2.4 或更高版本的 HDInsight 群集支援 YARN 資源管理器高可用性。 有兩個資源管理器，rm1 和 rm2，分別在頭節點 0 和頭節點 1 上運行。 與 NameNode 一樣，YARN 資源管理器也配置為自動容錯移轉。 當當前活動資源管理器出現故障或無回應時，將自動選擇另一個資源管理器處於活動狀態。

YARN 資源管理器使用其嵌入式*主動備用選擇器*作為故障檢測器和領導者選舉器。 與 HDFS NameNode 不同，YARN 資源管理器不需要單獨的 ZKFC 守護進程。 活動資源管理器將其狀態寫入 Apache 動物園管理員。

YARN 資源管理器的高可用性獨立于 NameNode 和其他 HDInsight HA 服務。 活動資源管理器可能無法在活動 NameNode 運行的活動頭節點或頭節點上運行。 有關 YARN 資源管理器高可用性的詳細資訊，請參閱[資源管理器高可用性](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)。

### <a name="hbase-master"></a>HBase 母版

HDInsight HBase 集群支援 HBase 主硬碟高可用性。 與其他在頭節點上運行的 HA 服務不同，HBase Master 在三個 Zookeeper 節點上運行，其中一個是活動主機，另兩個是備用節點。 與 NameNode 一樣，HBase Master 與 Apache Zookeeper 協調以進行領導者選舉，並在當前活動主機出現問題時自動進行容錯移轉。 在任何時候只有一個活動 HBase 母版。

## <a name="next-steps"></a>後續步驟

- [HDInsight 中 Apache Hadoop 叢集的可用性和可靠性](hdinsight-high-availability-linux.md)
- [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)
