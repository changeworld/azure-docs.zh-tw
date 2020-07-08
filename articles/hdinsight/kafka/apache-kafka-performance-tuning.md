---
title: Apache Kafka HDInsight 叢集的效能最佳化
description: 提供在 Azure HDInsight 上優化 Apache Kafka 工作負載的技術總覽。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75494919"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight 叢集的效能最佳化

本文提供在 HDInsight 中將 Apache Kafka 工作負載的效能優化的一些建議。 重點在於調整生產者和 broker 設定。 測量效能的方法有很多種，而您所套用的優化將取決於您的業務需求。

## <a name="architecture-overview"></a>架構概觀

Kafka 主題是用來組織記錄。 記錄是由「產生者」  產生，並由「取用者」  取用。 產生者會將記錄傳送至 Kafka 訊息代理程式，然後再儲存資料。 HDInsight 叢集中的每個背景工作節點都是 Kafka 訊息代理程式。

主題會將記錄分割至各個訊息代理程式。 取用記錄時，您可以針對每個分割區最多使用一個取用者，以達到資料平行處理。

複寫是用來跨節點複製資料分割。 這可防止節點（訊息代理程式）中斷。 複本群組之間的單一資料分割會指定為分割區的領導者。 使用 ZooKeeper 所管理的狀態，可將生產者流量路由傳送至每個節點的前端項目。

## <a name="identify-your-scenario"></a>識別您的案例

Apache Kafka 效能有兩個主要層面–輸送量和延遲。 輸送量是可以處理資料的最大速率。 較高的輸送量通常較佳。 延遲是儲存或抓取資料所花費的時間。 較低的延遲通常較佳。 在輸送量、延遲和應用程式基礎結構的成本之間尋找適當的平衡，可能是一項挑戰。 您的效能需求可能會符合下列三種常見情況的其中一種，取決於您是否需要高輸送量、低延遲或兩者皆是：

* 高輸送量、低延遲。 此案例需要高輸送量和低延遲（~ 100 毫秒）。 這類應用程式的其中一個範例是服務可用性監視。
* 高輸送量、高延遲。 此案例需要高輸送量（~ 1.5 GBps），但可容忍較高的延遲（< 250 毫秒）。 這類應用程式的其中一個範例是針對近乎即時的進程（例如安全性和入侵偵測應用程式），內嵌遙測資料。
* 低輸送量，低延遲。 此案例需要低延遲（< 10 毫秒）來進行即時處理，但可容忍較低的輸送量。 這類應用程式的範例為線上拼寫與文法檢查。

## <a name="producer-configurations"></a>生產者設定

下列各節將反白顯示一些最重要的設定屬性，以將 Kafka 產生者的效能優化。 如需所有設定屬性的詳細說明，請參閱[生產者設定上的 Apache Kafka 檔](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="batch-size"></a>批次大小

Apache Kafka 產生者會組合一組訊息（稱為批次），以一個單位的形式傳送，以儲存在單一儲存體分割區中。 [批次大小] 表示在傳輸該群組之前必須存在的位元組數目。 增加 `batch.size` 參數可能會增加輸送量，因為它可減少網路和 IO 要求的處理負擔。 在輕量負載下，增加的批次大小可能會增加 Kafka 傳送延遲，因為產生者會等待批次就緒。 在負載過重的情況下，建議您增加批次大小，以改善輸送量和延遲。

### <a name="producer-required-acknowledgments"></a>產生者所需的通知

產生者所需的設定 `acks` 會決定在將寫入要求視為完成之前，分割區領導者所需的通知數目。 這項設定會影響資料的可靠性，而且會接受 `0` 、或的值 `1` `-1` 。 的值 `-1` 表示必須從所有複本接收通知，才能完成寫入。 設定 `acks = -1` 可針對資料遺失提供更強的保證，但也會產生較高的延遲和較低的輸送量。 如果您的應用程式需求需要較高的輸送量，請嘗試設定 `acks = 0` 或 `acks = 1` 。 請記住，未確認所有複本的情況可能會降低資料的可靠性。

### <a name="compression"></a>壓縮

Kafka 產生者可以設定為在將訊息傳送至代理程式之前先進行壓縮。 `compression.type`設定會指定要使用的壓縮編解碼器。 支援的壓縮編解碼器為 "gzip"、"snappy" 和 "lz4"。 壓縮很有説明，如果磁片容量有限制，應該將其納入考慮。

在兩個常用的壓縮編解碼器中， `gzip` 和 `snappy` `gzip` 都具有較高的壓縮比率，這會導致較低的磁片使用量，代價是較高的 CPU 負載。 `snappy`編解碼器提供較少的壓縮，但 CPU 額外負荷較少。 您可以根據 broker 磁片或生產者 CPU 限制來決定要使用的編解碼器。 `gzip`可以用5倍以上的速率來壓縮資料 `snappy` 。

使用資料壓縮將會增加可儲存在磁片上的記錄數目。 當產生者和訊息代理程式所使用的壓縮格式不相符時，它也可能會增加 CPU 的額外負荷。 因為必須先壓縮資料，然後再進行處理，然後再進行解壓縮。

## <a name="broker-settings"></a>Broker 設定

下列各節將反白顯示一些最重要的設定，以將 Kafka 代理程式的效能優化。 如需所有 broker 設定的詳細說明，請參閱[生產者設定上的 Apache Kafka 檔](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="number-of-disks"></a>磁碟數量

儲存體磁片具有有限的 IOPS （每秒輸入/輸出作業）和每秒的讀取/寫入位元組數。 建立新的磁碟分割時，Kafka 會將每個新的分割區儲存在具有最少現有資料分割的磁片上，以在可用磁片之間進行平衡 儘管儲存策略，在處理每個磁片上的數百個數據分割複本時，Kafka 可以輕易地將可用的磁片輸送量飽和。 此處的缺點在於輸送量和成本之間。 如果您的應用程式需要更高的輸送量，請建立一個叢集，每個 broker 都有更多受控磁片。 HDInsight 目前不支援將受控磁片新增至執行中的叢集。 如需有關如何設定受控磁片數目的詳細資訊，請參閱[在 HDInsight 上設定 Apache Kafka 的儲存體和擴充性](apache-kafka-scalability.md)。 瞭解增加叢集中節點的儲存空間時的成本影響。

### <a name="number-of-topics-and-partitions"></a>主題和資料分割的數目

Kafka 生產者會寫入主題。 從主題讀取的 Kafka 取用者。 主題與記錄相關聯，這是磁片上的資料結構。 Kafka 會將來自生產者的記錄附加至主題記錄檔的結尾。 主題記錄包含多個分散于多個檔案的磁碟分割。 這些檔案接著會散佈到多個 Kafka 叢集節點。 取用者會以其步調閱讀 Kafka 主題，並可在主題記錄中挑選其位置（位移）。

每個 Kafka 資料分割都是系統上的記錄檔，而生產者執行緒可以同時寫入多個記錄。 同樣地，由於每個取用者執行緒都會從一個分割區讀取訊息，因此，從多個磁碟分割取用也會平行處理。

增加資料分割密度（每個訊息代理程式的磁碟分割數目）會增加與中繼資料作業相關的額外負荷，以及分割區領導者和其關注者之間的每個分割區要求/回應。 即使在沒有資料流程經的情況下，分割區複本仍然會從領導人提取資料，這會導致透過網路的傳送和接收要求進行額外的處理。

針對 HDInsight 中的 Apache Kafka 叢集1.1 和更新版本，建議您每個訊息代理程式最多有1000個磁碟分割，包括複本。 增加每個 broker 的分割區數目會降低輸送量，而且也可能造成主題無法用。 如需有關 Kafka 分割區支援的詳細資訊，請參閱[Kafka 版本中支援的磁碟分割數目增加中的官方 Apache blog 文章](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)。 如需修改主題的詳細資訊，請參閱[Apache Kafka：修改主題](https://kafka.apache.org/documentation/#basic_ops_modify_topic)。

### <a name="number-of-replicas"></a>複本數目

較高的複寫因數會導致分割區領導人和關注者之間的額外要求。 因此，較高的複寫因數會耗用更多的磁片和 CPU 來處理額外的要求、增加寫入延遲和減少輸送量。

我們建議您在 Azure HDInsight 中使用至少3倍的 Kafka 複寫。 大部分的 Azure 區域都有三個容錯網域，但在只有兩個容錯網域的區域中，使用者應該使用4x 複寫。

如需複寫的詳細資訊，請參閱[Apache Kafka： replication](https://kafka.apache.org/documentation/#replication) And [apache Kafka：增加複寫因數](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 上使用 Apache Kafka 每日處理數兆個事件](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [什麼是 HDInsight 上的 Apache Kafka？](apache-kafka-introduction.md)
