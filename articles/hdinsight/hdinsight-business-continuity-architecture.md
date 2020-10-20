---
title: Azure HDInsight 商務持續性架構
description: 本文討論適用于 HDInsight 的不同可能商務持續性架構
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop 高可用性
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: c2c5e5d0dc90f8f41882f6a63497a197cd74f0ce
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207575"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Azure HDInsight 商務持續性架構

本文提供一些您可能會考慮 Azure HDInsight 的商務持續性架構範例。 在發生嚴重損壞時，降低功能的承受度是從一個應用程式到下一個應用程式的商務決策。 某些應用程式可能會無法使用，或在一段期間內有較低的功能或延遲處理的部分可接受。 針對其他應用程式，可能無法接受任何精簡的功能。

> [!NOTE]
> 本文所呈現的架構並沒有詳盡的說明。 當您針對預期的商務持續性、營運複雜性和擁有權成本做出目標決定之後，您應該設計自己的獨特架構。

## <a name="apache-hive-and-interactive-query"></a>Apache Hive 和 Interactive Query

建議您在 HDInsight Hive 和互動式查詢叢集中進行商務持續性的[Hive 複寫 V2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) 。 需要複寫的獨立 Hive 叢集的持續區段是儲存層和 Hive 中繼存放區。 具有企業安全性套件的多重使用者案例中的 Hive 叢集需要 Azure Active Directory Domain Services 和 Ranger 中繼存放區。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Hive 和互動式查詢架構":::

以 Hive 事件為基礎的複寫是在主要與次要叢集之間進行設定。 這包括兩個不同的階段：啟動載入和增量執行：

* 啟動載入會將整個 Hive 倉儲（包括從主要的 Hive 中繼存放區資訊）複寫到次要。

* 增量執行會在主要叢集上自動執行，而在增量回合期間產生的事件會在次要叢集上播放。 次要叢集會與從主要叢集產生的事件一併捕捉，以確保在複寫執行之後，次要叢集與主要叢集的事件是一致的。

次要叢集只有在複寫時才需要執行分散式複製， `DistCp` 但儲存體和中繼存放區必須是永久性的。 您可以選擇在複寫之前依需求啟動已編寫腳本的次要叢集，並在其上執行複寫腳本，然後在成功複寫之後將其卸載。

次要叢集通常是唯讀的。 您可以將次要叢集設為讀寫，但這會增加額外的複雜度，包括將變更從次要叢集複寫至主要叢集。

### <a name="hive-event-based-replication-rpo--rto"></a>以 Hive 事件為基礎的複寫 RPO & RTO

* RPO：資料遺失會限制為從主要到次要的最新成功增量複寫事件。

* RTO：上游和下游交易與次要資料庫之間的失敗與繼續之間的時間。

### <a name="apache-hive-and-interactive-query-architectures"></a>Apache Hive 和 Interactive Query 架構

#### <a name="hive-active-primary-with-on-demand-secondary"></a>具有隨選次要的 Hive 主動主要

在 *具有隨選次要* 架構的作用中主要區域中，應用程式會寫入至使用中的主要區域，而不會在正常作業期間于次要區域中布建任何叢集。 次要區域中的 SQL 中繼存放區和儲存體是持續性的，而 HDInsight 叢集只會在排程的 Hive 複寫執行之前，視需要進行編寫和部署。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="Hive 和互動式查詢架構":::

#### <a name="hive-active-primary-with-standby-secondary"></a>具有待命次要資料庫的 Hive 主動主要

在 *具有待命次要資料庫*的作用中主要區域中，應用程式會寫入至使用中的主要區域，而在唯讀模式中的待命相應減少次要叢集則會在正常作業期間執行。 在正常作業期間，您可以選擇將區域特定的讀取作業卸載至次要。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="Hive 和互動式查詢架構":::

如需 Hive 複寫和程式碼範例的詳細資訊，請參閱[Azure HDInsight 叢集中的 Apache Hive](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-replication)複寫

## <a name="apache-spark"></a>Apache Spark

Spark 工作負載不一定會涉及 Hive 元件。 為了讓 Spark SQL 工作負載能夠從 Hive 讀取和寫入資料，HDInsight Spark 叢集會從相同區域中的 Hive/互動式查詢叢集共用 Hive 自訂中繼存放區。 在這種情況下，Spark 工作負載的跨區域複寫也必須伴隨 Hive 中繼存放區和儲存體的複寫。 本節中的容錯移轉案例適用于兩者：

* 使用[Hive Warehouse Connector (HWC) 安裝程式使用 HDInsight Interactive Query 叢集的 ACID 資料表上的 SPARK SQL](./interactive-query/apache-hive-warehouse-connector.md) 。
* 使用 HDInsight Hadoop 叢集的非 ACID 資料表上的 Spark SQL 工作負載。

在獨立模式中進行 Spark 的案例中，策劃資料和 Livy 作業的儲存 Spark Jar () 需要使用 Azure Data Factory 的定期從主要區域複寫到次要區域 `DistCP` 。

建議您使用版本控制系統來儲存 Spark 筆記本和程式庫，以便輕鬆地將它們部署在主要或次要叢集上。 確定筆記本型和非筆記本型解決方案已準備好在主要或次要工作區中載入正確的資料裝載。

如果有客戶專屬的程式庫不是 HDInsight 原生提供的程式庫，則必須加以追蹤並定期載入至待命次要叢集。  

### <a name="apache-spark-replication-rpo--rto"></a>Apache Spark replication RPO & RTO

* RPO：資料遺失的限制為上一次成功的增量複寫 (Spark 和 Hive) 從主要到次要。

* RTO：上游和下游交易與次要資料庫之間的失敗與繼續之間的時間。

### <a name="apache-spark-architectures"></a>Apache Spark 架構

#### <a name="spark-active-primary-with-on-demand-secondary"></a>具有隨選次要的 Spark active primary

在一般作業期間，應用程式會在主要區域中讀取和寫入 Spark 和 Hive 叢集，而不會在次要區域中布建任何叢集。 SQL 中繼存放區、Hive 儲存體和 Spark 儲存體在次要區域中是持續性的。 Spark 和 Hive 叢集會依需求進行編寫和部署。 當 Azure Data Factory `DistCP` 可以用來複製獨立 Spark 儲存體時，會使用 hive 複寫來複寫 Hive 儲存體和 hive 中繼存放區。 由於相依性計算的關係，hive 叢集必須在每個 Hive 複寫執行之前部署 `DistCp` 。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="Hive 和互動式查詢架構":::

#### <a name="spark-active-primary-with-standby-secondary"></a>具有待命次要資料庫的 Spark active primary

在一般作業期間，應用程式會讀取及寫入主要區域中的 Spark 和 Hive 叢集，而在唯讀模式中，則會在次要區域中執行，以唯讀模式來擴充 Hive 和 Spark 叢集。 在正常作業期間，您可以選擇將區域特定的 Hive 和 Spark 讀取作業卸載至次要。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="Hive 和互動式查詢架構":::

## <a name="apache-hbase"></a>Apache HBase (英文)

HBase 匯出和 HBase 複寫是在 HDInsight HBase 叢集之間啟用商務持續性的常見方式。

HBase 匯出是一個批次複寫程式，它會使用 HBase 匯出公用程式，將資料表從主要 HBase 叢集匯出到其基礎 Azure Data Lake Storage Gen 2 儲存體。 然後，您可以從次要 HBase 叢集存取匯出的資料，並將其匯入到必須在次要資料庫中之外的資料表。 雖然 HBase 匯出會提供資料表層級的資料細微性，但是在累加式更新的情況下，匯出自動化引擎會控制每次執行時要包含的遞增資料列範圍。 如需詳細資訊，請參閱 [HDInsight HBase 備份和](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#export-then-import)複寫。

HBase 複寫會使用完全自動化的方式，在 HBase 叢集之間使用近乎即時的複寫。 複寫是在資料表層級進行。 所有資料表或特定資料表都可以做為複寫的目標。 HBase 複寫最終是一致的，這表示對主要區域中的資料表進行最近的編輯，可能無法立即供所有的次要資料庫使用。 次要複本保證最終會與主資料庫一致。 如果有下列情況，可以在兩個以上的 HDInsight HBase 叢集之間設定 HBase 複寫：

* 主要和次要資料庫位於相同的虛擬網路中。
* 主要和次要資料庫位於相同區域中的不同對等互連 Vnet。
* 主要和次要資料庫位於不同區域的不同對等互連 Vnet。

如需詳細資訊，請參閱 [在 Azure 虛擬網路中設定 Apache HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-replication)叢集複寫。

有一些其他方法可執行 HBase 叢集的備份，例如 [複製 hbase 資料夾](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-the-hbase-folder)、 [複製資料表](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-tables) 和 [快照](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#snapshots)集。

### <a name="hbase-rpo--rto"></a>HBase RPO & RTO

#### <a name="hbase-export"></a>HBase 匯出

* RPO：資料遺失限制為從主要複本最後一次成功的批次累加式匯入。
* RTO：次要上的主要和繼續 i/o 作業失敗之間的時間。

#### <a name="hbase-replication"></a>HBase 複寫

* RPO：資料遺失僅限於在次要位置收到的最後一個 WalEdit 運送。
* RTO：次要上的主要和繼續 i/o 作業失敗之間的時間。

### <a name="hbase-architectures"></a>HBase 架構

您可以在三種模式中設定 HBase 複寫：領導人型、Leader-Leader 和迴圈。

#### <a name="hbase-replication--leader--follower-model"></a>HBase 複寫：領導者-執行者模型

在此跨區域設定中，複寫是從主要區域到次要區域的單向複寫。 可以針對單向複寫找出主要複本上的所有資料表或特定資料表。 在正常作業期間，次要叢集可以用來在自己的區域中提供讀取要求。

次要叢集會以一般 HBase 叢集的形式運作，該叢集可裝載自己的資料表，並可提供來自區域應用程式的讀取和寫入。 不過，寫入至次要複本的複寫資料表或資料表不會複寫回主要複本。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="Hive 和互動式查詢架構":::

#### <a name="hbase-replication--leader--leader-model"></a>HBase Replication：領導者–領導人模型

此跨區域設定與單向設定非常類似，不同之處在于複寫會在主要區域和次要區域之間雙向。 應用程式可以使用讀取-寫入模式中的兩個叢集，而更新則會以非同步方式在兩者之間交換。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="Hive 和互動式查詢架構":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>HBase 複寫：多區域或迴圈

多區域/迴圈複寫模型是 HBase 複寫的延伸模組，可用來建立具有多個應用程式的全域重複 HBase 架構，以讀取和寫入區域特定的 HBase 叢集。 您可以根據商務需求，根據領導者/領導人或領導人/等的各種組合來設定叢集。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="Hive 和互動式查詢架構":::

## <a name="apache-kafka"></a>Apache Kafka

若要啟用跨區域可用性 HDInsight 4.0 支援 Kafka MirrorMaker，可用來維護不同區域中主要 Kafka 叢集的次要複本。 MirrorMaker 可做為高階消費者生產者組，從主要叢集中的特定主題取用，並產生在次要資料庫中具有相同名稱的主題。 使用 MirrorMaker 進行高可用性嚴重損壞修復的跨叢集複寫，會假設生產者和取用者需要容錯移轉至複本叢集。 如需詳細資訊，請參閱 [使用 MirrorMaker 搭配 HDInsight 上的 Kafka 來複寫 Apache Kafka 主題](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-mirroring)

根據在複寫開始時的主題存留期，MirrorMaker 主題複寫可能會導致來源與複本主題之間有不同的位移。 HDInsight Kafka 叢集也支援主題資料分割複寫，這是在個別叢集層級的高可用性功能。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Hive 和互動式查詢架構":::

### <a name="apache-kafka-architectures"></a>Apache Kafka 架構

#### <a name="kafka-replication-active--passive"></a>Kafka 複寫：主動-被動

Active-Passive 安裝程式會啟用從主動到被動的非同步單向鏡像。 生產者和取用者必須留意主動和被動叢集是否存在，而且必須準備好在主動失敗時容錯移轉至被動。 以下是 Active-Passive 設定的一些優點和缺點。

優點：

* 群集之間的網路延遲不會影響作用中叢集的效能。
* 單向複寫的簡化。

缺點：

* 被動叢集可能仍未充分利用。
* 在應用程式生產者和取用者中併入容錯移轉感知的設計複雜性。
* 在使用中叢集失敗期間可能遺失資料。
* 主動與被動叢集之間的主題之間的最終一致性。
* 容錯回復至主要可能會導致主題中的訊息不一致。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Hive 和互動式查詢架構":::

#### <a name="kafka-replication-active--active"></a>Kafka Replication： Active-Active

Active-Active 設定牽涉到兩個地區分隔的 VNet 對等互連 HDInsight Kafka 叢集與 MirrorMaker 的雙向非同步複寫。 在此設計中，主要取用者取用的訊息也可供次要取用者使用，反之亦然。 以下是 Active-Active 設定的一些優點和缺點。

優點：

* 因為容錯移轉和容錯回復的狀態重複，所以可讓您更輕鬆地執行容錯移轉和。

缺點：

* 設定、管理和監視比主動-被動更複雜。
* 迴圈複寫的問題必須解決。  
* 雙向複寫會導致較高的區域資料出口成本。

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Hive 和互動式查詢架構":::

## <a name="hdinsight-enterprise-security-package"></a>HDInsight 企業安全性套件

這項設定可用來在主要和次要資料庫中啟用多使用者功能，以及 [AZURE AD DS 複本集](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-replica-set) ，以確保使用者可以同時對兩個叢集進行驗證。 在正常作業期間，必須在次要資料庫中設定 Ranger 原則，以確保使用者只能讀取作業。 下列架構說明啟用 ESP 的 Hive 作用中主要-待命次要資料庫設定的方式。

Ranger 中繼存放區 replication：

Ranger 中繼存放區是用來持續儲存和提供 Ranger 原則來控制資料授權。 建議您在主要和次要中維護獨立的 Ranger 原則，並將次要複本維護為讀取複本。
  
如果需要在主要和次要之間保持 Ranger 原則的同步，請使用 [Ranger 匯入/匯出](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export#:~:text=Ranger%20has%20introduced%20a%20new,can%20import%20and%20export%20policies.&text=Also%20can%20export%2Fimport%20a,repositories\)%20via%20Ranger%20Admin%20UI) 定期備份，並將 Ranger 原則從主要備份匯入到次要。

在主要和次要之間複寫 Ranger 原則可能會導致次要複本變成啟用寫入，而導致資料不一致的次要複本上發生意外的寫入。  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="Hive 和互動式查詢架構":::

## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中討論的專案，請參閱：

* [Azure HDInsight 商務持續性](./hdinsight-business-continuity.md)
* [Azure HDInsight 高可用性解決方案架構案例研究](./hdinsight-high-availability-case-study.md)
* [Azure HDInsight 上的 Apache Hive 和 HiveQL 是什麼？](./hadoop/hdinsight-use-hive.md)
