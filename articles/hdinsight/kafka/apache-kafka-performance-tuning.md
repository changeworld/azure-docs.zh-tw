---
title: Apache Kafka HDInsight 叢集的效能最佳化
description: 提供在 Azure HDInsight 上將 Apache Kafka 工作負載優化的技術總覽。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75494919"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight 叢集的效能最佳化

本文提供一些建議，讓您在 HDInsight 中將 Apache Kafka 工作負載的效能優化。 重點在於調整生產者和 broker 設定。 有不同的方式可測量效能，而您套用的優化將取決於您的業務需求。

## <a name="architecture-overview"></a>架構概觀

Kafka 主題可用來組織記錄。 記錄是由「產生者」產生，並由「取用者」取用。 生產者會將記錄傳送至 Kafka 訊息代理程式，然後再儲存資料。 HDInsight 叢集中的每個背景工作節點都是 Kafka 訊息代理程式。

主題會將記錄分割至各個訊息代理程式。 取用記錄時，您可以針對每個分割區最多使用一個取用者，以達到資料平行處理。

複寫可用來跨節點複製資料分割。 這可防止 node (broker) 中斷。 複本群組中的單一分割區會指定為分割區的領導者。 使用 ZooKeeper 所管理的狀態，可將生產者流量路由傳送至每個節點的前端項目。

## <a name="identify-your-scenario"></a>識別您的案例

Apache Kafka 效能有兩個主要層面–輸送量和延遲。 輸送量是可處理資料的最大速率。 更高的輸送量通常更好。 延遲是儲存或抓取資料所花費的時間。 較低的延遲通常更好。 在輸送量、延遲和應用程式基礎結構的成本之間尋找適當的平衡可能是一項挑戰。 根據您是否需要高輸送量、低延遲或兩者，效能需求可能會符合下列三種常見情況的其中一種：

* 高輸送量、低延遲。 此案例需要高輸送量和低延遲 (~ 100 毫秒) 。 這種應用程式類型的範例是服務可用性監視。
* 高輸送量、高延遲。 此案例需要高輸送量 (~ 1.5 GBps) 但可容忍 ( # A0 250 ms) 的延遲較高。 這類應用程式的其中一個範例是適用于近乎即時進程的遙測資料內嵌，例如安全性和入侵偵測應用程式。
* 低輸送量，低延遲。 此案例需要低延遲 ( # A0 10 ms) 以進行即時處理，但可容忍較低的輸送量。 這類應用程式的範例有線上拼寫和文法檢查。

## <a name="producer-configurations"></a>生產者設定

下列各節將反白顯示一些最重要的設定屬性，以將 Kafka 生產者的效能優化。 如需所有設定屬性的詳細說明，請參閱生產者設定的 [Apache Kafka 檔](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="batch-size"></a>批次大小

Apache Kafka 生產者會將訊息群組 (稱為「批次」) ，其會以一個單位的形式傳送，以儲存在單一儲存體分割區中。 [批次大小] 表示在傳輸該群組之前必須存在的位元組數目。 增加 `batch.size` 參數可能會增加輸送量，因為它可減少網路和 IO 要求的處理負荷。 在輕量負載下，增加的批次大小可能會增加 Kafka 傳送延遲，因為生產者會等待批次就緒。 在負載過重的情況下，建議您增加批次大小，以改善輸送量和延遲。

### <a name="producer-required-acknowledgments"></a>生產者要求的通知

產生者所需的設定 `acks` 會決定在將寫入要求視為完成之前，資料分割領導人所需的通知數目。 這項設定會影響資料的可靠性，而且會接受 `0` 、或的值 `1` `-1` 。 的值 `-1` 表示在寫入完成之前，必須先從所有複本接收認可。 設定 `acks = -1` 可針對資料遺失提供更強的保證，但它也會產生較高的延遲和較低的輸送量。 如果您的應用程式需求需要較高的輸送量，請嘗試設定 `acks = 0` 或 `acks = 1` 。 請記住，未認可所有複本可能會降低資料的可靠性。

### <a name="compression"></a>壓縮

Kafka 生產者可以設定為先壓縮訊息，再將訊息傳送給訊息代理程式。 此 `compression.type` 設定會指定要使用的壓縮編解碼器。 支援的壓縮編解碼器為 "gzip"、"snappy" 和 "lz4"。 壓縮很有説明，如果磁片容量有限制，則應加以考慮。

在兩個常用的壓縮編解碼器中， `gzip` 和 `snappy` `gzip` 都有較高的壓縮比率，以較高的 CPU 負載成本產生較低的磁片使用量。 `snappy`編解碼器提供較少的壓縮，而不會造成 CPU 額外負荷。 您可以根據 broker 磁片或生產者 CPU 限制來決定要使用哪個編解碼器。 `gzip` 可以用五倍以上的速率壓縮資料 `snappy` 。

使用資料壓縮將會增加可儲存在磁片上的記錄數目。 當生產者和 broker 使用的壓縮格式不相符時，也可能會增加 CPU 額外負荷。 因為必須先壓縮資料，然後再進行處理，然後再進行解壓縮。

## <a name="broker-settings"></a>Broker 設定

下列各節將反白顯示一些最重要的設定，以優化 Kafka 訊息代理程式的效能。 如需所有 broker 設定的詳細說明，請參閱生產者設定的 [Apache Kafka 檔](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="number-of-disks"></a>磁碟數量

儲存體磁片的 IOPS 限制 (每秒輸入/輸出作業) 和每秒讀取/寫入位元組數。 建立新的磁碟分割時，Kafka 會將磁片上的每個新磁碟分割儲存至最少的現有分割區，以在可用磁片之間進行平衡。 儘管是儲存策略，但在處理每個磁片上的數百個磁碟分割複本時，Kafka 很容易就會讓可用的磁片輸送量飽和。 此處的取捨是輸送量和成本之間的取捨。 如果您的應用程式需要更高的輸送量，請為每個 broker 建立具有更多受控磁片的叢集。 HDInsight 目前不支援將受控磁片新增至執行中的叢集。 如需有關如何設定受控磁片數目的詳細資訊，請參閱 [設定 HDInsight 上 Apache Kafka 的儲存體和擴充性](apache-kafka-scalability.md)。 瞭解為叢集中的節點增加儲存空間的成本含意。

### <a name="number-of-topics-and-partitions"></a>主題和資料分割的數目

Kafka 生產者會寫入至主題。 Kafka 取用者會從主題讀取。 主題與記錄檔相關聯，這是磁片上的資料結構。 Kafka 會將來自生產者 (s) 的記錄附加至主題記錄檔的結尾。 主題記錄檔是由許多分散于多個檔案的分割區所組成。 這些檔案接著會散佈到多個 Kafka 叢集節點。 取用者會依其步調從 Kafka 主題讀取，並可在主題記錄檔中挑選其位置 (位移) 。

每個 Kafka 分割區都是系統上的記錄檔，而生產者執行緒可以同時寫入多個記錄檔。 同樣地，由於每個取用者執行緒都會從一個資料分割讀取訊息，因此也會以平行方式處理多個資料分割的取用。

增加分割密度 (每個 broker 的資料分割數目) 會增加與中繼資料作業相關的額外負荷，以及分割區領導人和其關注者之間的每個分割區要求/回應。 即使在沒有資料流程經的情況下，資料分割複本仍會從領導人提取資料，而這會導致透過網路傳送和接收要求的額外處理。

若為 HDInsight 中的 Apache Kafka 叢集1.1 和更新版本，建議您針對每個訊息代理程式最多有1000個磁碟分割，包括複本。 增加每個 broker 的資料分割數目可減少輸送量，也可能造成主題無法使用。 如需有關 Kafka 分割區支援的詳細資訊，請參閱 [官方的 Apache Kafka blog 文章，以取得版本1.1.0 中支援](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)的分割區數目增加。 如需修改主題的詳細資訊，請參閱 [Apache Kafka：修改主題](https://kafka.apache.org/documentation/#basic_ops_modify_topic)。

### <a name="number-of-replicas"></a>複本數目

較高的複寫因素會導致分割區領導人和關注者之間的其他要求。 因此，較高的複寫因數會耗用更多的磁片和 CPU 來處理額外的要求，進而增加寫入延遲和降低輸送量。

建議您在 Azure HDInsight 中使用至少3倍的 Kafka 複寫。 大部分的 Azure 區域都有三個容錯網域，但在只有兩個容錯網域的區域中，使用者應該使用4x 複寫。

如需複寫的詳細資訊，請參閱 [Apache Kafka： replication](https://kafka.apache.org/documentation/#replication) 和 [Apache Kafka：提高複寫因數](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 上使用 Apache Kafka 每日處理數兆個事件](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [什麼是 HDInsight 上的 Apache Kafka？](apache-kafka-introduction.md)
