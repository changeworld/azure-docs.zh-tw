---
title: Apache Kafka HDInsight 叢集的效能最佳化
description: 概述了在 Azure HDInsight 上優化 Apache Kafka 工作負載的技術。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494919"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight 叢集的效能最佳化

本文提供了一些建議，用於優化 HDInsight 中 Apache Kafka 工作負載的性能。 重點是調整生產者和代理配置。 衡量性能的方法各不相同，您應用的優化將取決於您的業務需求。

## <a name="architecture-overview"></a>架構概觀

卡夫卡主題用於組織記錄。 記錄是由「產生者」產生，並由「取用者」取用。 生產者將記錄發送給 Kafka 經紀人，然後後者存儲資料。 HDInsight 叢集中的每個背景工作節點都是 Kafka 訊息代理程式。

主題會將記錄分割至各個訊息代理程式。 取用記錄時，您可以針對每個分割區最多使用一個取用者，以達到資料平行處理。

複製用於跨節點複製分區。 這樣可以防止節點（代理）中斷。 副本組之間的單個分區被指定為分區引線。 使用 ZooKeeper 所管理的狀態，可將生產者流量路由傳送至每個節點的前端項目。

## <a name="identify-your-scenario"></a>識別您的案例

Apache Kafka 性能有兩個主要方面 - 輸送量和延遲。 輸送量是處理資料的最大速率。 更高的輸送量通常更好。 延遲是存儲或檢索資料所需的時間。 較低的延遲通常更好。 在輸送量、延遲和應用程式基礎結構成本之間找到適當的平衡可能具有挑戰性。 性能要求可能與以下三種常見情況之一相匹配，具體取決於您是否需要高輸送量、低延遲或兩者：

* 高輸送量，低延遲。 此方案需要高輸送量和低延遲（±100 毫秒）。 這種類型的應用程式的一個示例是服務可用性監視。
* 高輸送量，高延遲。 此方案需要高輸送量（±1.5 GBps），但可以容忍更高的延遲（< 250 ms）。 這種類型的應用程式的一個示例是遙測資料引入，用於近即時進程（如安全和入侵偵測應用程式）。
* 輸送量低，延遲低。 此方案需要低延遲（< 10 ms）才能進行即時處理，但可以容忍較低的輸送量。 這種類型的應用程式的一個示例是線上拼寫和語法檢查。

## <a name="producer-configurations"></a>生產者配置

以下各節將重點介紹一些最重要的配置屬性，以優化 Kafka 生產商的性能。 有關所有配置屬性的詳細說明，請參閱[Apache Kafka 有關生產者配置的文檔](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="batch-size"></a>批次大小

Apache Kafka 生產者會收集消息組（稱為批次處理），這些消息組作為一個單元發送，以存儲在單個存儲分區中。 批次處理大小表示在傳輸該組之前必須存在的位元組數。 增加`batch.size`參數可以增加輸送量，因為它減少了網路和 IO 請求的處理開銷。 在輕負載下，隨著生產者等待批次處理準備就緒，增加的批次處理大小可能會增加 Kafka 發送延遲。 在重負載下，建議增加批次處理大小以提高輸送量和延遲。

### <a name="producer-required-acknowledgments"></a>生產者所需的確認

在認為寫入`acks`請求完成之前，生產者所需的配置確定分區引線所需的確認數。 此設置會影響資料的可靠性，它需要`0`的值 。 `1` `-1` 的值`-1`表示在完成寫入之前，必須從所有副本接收確認。 設置`acks = -1`提供了更強的資料丟失保證，但它也會導致更高的延遲和更低的輸送量。 如果應用程式要求更高的輸送量，請嘗試設置`acks = 0`或`acks = 1`。 請記住，不確認所有副本可能會降低資料可靠性。

### <a name="compression"></a>壓縮

Kafka 產生器可以配置為在將消息發送給代理之前壓縮消息。 該`compression.type`設置指定要使用的壓縮編解碼器。 支援的壓縮編解碼器是"gzip"、"snappy"和"lz4"。 壓縮是有益的，如果磁片容量有限制，則應考慮壓縮。

在兩個常用的壓縮編解碼器`gzip`中`snappy`，`gzip`和 的壓縮率較高，因此以更高的 CPU 負載為代價，降低了磁片使用率。 編`snappy`解碼器提供較少的壓縮，更少的 CPU 開銷。 您可以根據代理磁片或生產者 CPU 限制決定使用哪種編解碼器。 `gzip`可以壓縮資料的速率比`snappy`高五倍。

使用資料壓縮會增加可存儲在磁片上的記錄數。 如果生產者和代理使用的壓縮格式不匹配，它也可能增加 CPU 開銷。 因為在發送之前必須壓縮資料，然後在處理之前進行解壓縮。

## <a name="broker-settings"></a>經紀商設置

以下各節將重點介紹一些最重要的設置，以優化 Kafka 經紀人的性能。 有關所有代理設置的詳細說明，請參閱[Apache Kafka 有關生產者配置的文檔](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="number-of-disks"></a>磁碟數量

存儲磁片的 IOPS（每秒輸入/輸出操作）和每秒的讀/寫位元組有限。 創建新分區時，Kafka 將每個新分區存儲在磁片上，現有分區最少，以便跨可用磁片進行平衡。 儘管存儲策略，在處理每個磁片上的數百個分區副本時，Kafka 可以輕鬆地使可用的磁片輸送量飽和。 此處的權衡是在輸送量和成本之間。 如果應用程式需要更高的輸送量，請創建每個代理具有更多託管磁片的群集。 HDInsight 目前不支援將託管磁片添加到正在運行的群集。 有關如何配置託管磁片數量的詳細資訊，請參閱[在 HDInsight 上配置 Apache Kafka 的存儲和可擴充性](apache-kafka-scalability.md)。 瞭解增加群集中節點的存儲空間的成本影響。

### <a name="number-of-topics-and-partitions"></a>主題和分區數

卡夫卡製作人寫主題。 卡夫卡消費者從主題中閱讀。 主題與日誌相關聯，日誌是磁片上的資料結構。 Kafka 將記錄從生產者追加到主題日誌的末尾。 主題日誌由分佈在多個檔上的許多分區組成。 這些檔又分佈在多個 Kafka 叢集節點上。 消費者在卡夫卡主題的節奏下閱讀，可以在主題日誌中選擇自己的位置（偏移）。

每個 Kafka 分區都是系統上的日誌檔，生產者執行緒可以同時寫入多個日誌。 同樣，由於每個消費者執行緒從一個分區讀取消息，因此使用多個分區也會並行處理。

增加分區密度（每個代理的分區數）會增加與中繼資料操作相關的開銷以及分區主管及其關注者之間的每個分區請求/回應。 即使在沒有資料流程經的情況下，分區副本仍會從引線獲取資料，從而導致通過網路發送和接收請求的額外處理。

對於 HDInsight 中的 Apache Kafka 群集 1.1 及以上，我們建議您為每個代理最多具有 1000 個分區，包括副本。 增加每個代理的分區數會降低輸送量，並可能導致主題不可用。 有關 Kafka 分區支援的詳細資訊，請參閱[Apache Kafka 官方博客文章，瞭解版本 1.1.0 中受支援的分區數量的增加](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)。 有關修改主題的詳細資訊，請參閱[Apache Kafka：修改主題](https://kafka.apache.org/documentation/#basic_ops_modify_topic)。

### <a name="number-of-replicas"></a>複本數目

較高的複製因數會導致分區引線和關注者之間的其他請求。 因此，較高的複製係數會消耗更多的磁片和 CPU 來處理其他請求，從而增加寫入延遲並降低輸送量。

我們建議您在 Azure HDInsight 中使用至少 3 倍的 Kafka 複製。 大多數 Azure 區域有三個容錯域，但在只有兩個容錯域的區域，使用者應使用 4 倍複製。

有關複製的詳細資訊，請參閱[Apache Kafka：複製](https://kafka.apache.org/documentation/#replication)和[Apache Kafka：增加複製因數](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 上使用 Apache Kafka 每日處理數兆個事件](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [什麼是 HDInsight 上的 Apache Kafka？](apache-kafka-introduction.md)
