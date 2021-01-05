---
title: 針對叢集建議程式建議優化
titleSuffix: Azure HDInsight
description: 針對 Azure HDInsight 中的叢集建議程式建議優化 Apache HBase。
author: ramkrish86
ms.author: ramvasu
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: 36d40215f759190cc9e6c6e3f4918dcbc384f94f
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97893262"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache HBase 諮詢

本文說明數個可協助您將 Azure HDInsight 中的 Apache HBase 效能優化的諮詢。 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>優化 HBase 以讀取最近寫入的資料

當您在 Azure HDInsight 中使用 Apache HBase 時，您可以針對應用程式讀取最近寫入資料的案例，將 HBase 的設定優化。 為了達到高效能，最好是從 memstore （而不是從遠端儲存體）提供 HBase 讀取。

查詢諮詢指出資料表中指定的資料行系列有 > 的75% 讀取，且從 memstore 取得服務。 此指標表示即使在 memstore 上發生排清，也需要存取最近使用的檔案，且必須在快取中。 系統會先將資料寫入 memstore 系統存取最近的資料。 內部 HBase 清除器執行緒可能會偵測到指定區域已達到 128M (預設) 大小，並且可以觸發排清。 這種情況也會發生在 memstore 大約128M 時所寫入的最新資料。 因此，稍後閱讀這些最近的記錄可能需要讀取檔案，而不是 memstore。 因此最好將最近排清的最新資料優化，以快取的方式來進行優化。

若要將快取中的最新資料優化，請考慮下列設定：

1. 將 `hbase.rs.cacheblocksonwrite` 設定為 `true`。 這是 HDInsight HBase 中的預設 `true` 設定，所以請檢查是否未重設為 `false` 。

2. 增加 `hbase.hstore.compactionThreshold` 值，讓您可以避免開始中的壓縮。 根據預設，此值為 `3`。 您可以將它增加為較高的值，例如 `10` 。

3. 如果您遵循步驟2並設定 compactionThreshold，然後變更 `hbase.hstore.compaction.max` 為較高的值（例如），並同時將設定的 `100` 值增加 `hbase.hstore.blockingStoreFiles` 為較高的值 `300` 。

4. 如果您確定只需要讀取最近的資料，請將 [設定] 設定 `hbase.rs.cachecompactedblocksonwrite` 為 [ **開啟**]。 這項設定會告訴系統即使發生壓縮，資料仍會保留在快取中。 您也可以在系列層級設定這些設定。 

   在 HBase Shell 中，執行下列命令：
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. 您可以針對資料表中的指定系列關閉 [封鎖快取]。 確定已針對具有最新資料 **讀取的系列開啟此功能。** 依預設，會針對資料表中的所有系列開啟 [封鎖快取]。 如果您已停用某系列的區塊快取，且需要將其開啟，請使用 hbase shell 中的 alter 命令。

   這些設定有助於確保資料在快取中，而且最近的資料不會進行壓縮。 如果您的案例可能會有 TTL，請考慮使用「資料層級壓縮」。 如需詳細資訊，請參閱 [Apache HBase 參考指南：資料分層式壓縮](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>優化清除佇列

優化排清佇列諮詢指出 HBase 排清可能需要微調。 清除處理常式的設定可能不夠高。

在區域伺服器 UI 中，請注意清除佇列是否成長超過100。 此閾值表示排清的速度很慢，您可能必須微調設定   `hbase.hstore.flusher.count` 。 根據預設，此值為2。 確定清除器執行緒的最大值不會增加到6以上。

此外，請查看您是否有區域計數微調的建議。 如果是的話，請先嘗試進列區域調整，看看這是否有助於加快排清的速度。 微調清除器執行緒可能會有多種方法，例如 

## <a name="region-count-tuning"></a>區域計數調整

區域計數微調諮詢指出 HBase 有封鎖的更新，而區域計數可能超過最佳支援的堆積大小。 您可以調整堆積大小、memstore 大小和區域計數。

範例案例：

- 假設區域伺服器的堆積大小為 10 GB。 依預設， `hbase.hregion.memstore.flush.size` 是 `128M` 。 `hbase.regionserver.global.memstore.size` 的預設值為 `0.4`。 這表示，在 10 GB 內，會為 memstore (全域) 配置 4 GB。

- 假設在所有區域上都有寫入負載的平均分佈，假設每個區域只會增加到 128 MB，則此設定中的區域數目上限為 `32` 區域。 如果指定的區域伺服器設定為具有32區域，則系統可更妥善地避免封鎖更新。

- 設定好這些設定後，區域數目就是100。 4 GB 的全域 memstore 現已在100個區域中分割。 因此，有效的每個區域都只會取得 40 MB 的 memstore。 當寫入是一致的時，系統會頻繁地排清和較小的順序 < 40 MB 的大小。 有許多清除器執行緒可能會提高排清速度 `hbase.hstore.flusher.count` 。

此諮詢表示要重新考慮每台伺服器的區域數目、堆積大小和全域 memstore 大小設定，以及排清執行緒微調，以便避免這類更新遭到封鎖。

## <a name="compaction-queue-tuning"></a>壓縮佇列微調

如果 HBase 壓縮佇列成長至超過2000，而且會定期發生，則您可以將壓縮執行緒增加為較大的值。

當壓縮的檔案數目過多時，可能會產生與檔案與 Azure 檔案系統互動的更多堆積使用量。 因此最好能儘快完成壓縮。 在較舊叢集中的一些時間，與節流相關的壓縮設定可能會導致壓縮速率較慢。

檢查設定 `hbase.hstore.compaction.throughput.lower.bound` 和 `hbase.hstore.compaction.throughput.higher.bound` 。 如果已設定為50M 和100M，則保持原狀。 但是，如果您將這些設定設定為較低的值 (這是較舊叢集) 的情況，請將限制分別變更為50M 和100M。

設定為 `hbase.regionserver.thread.compaction.small` ， `hbase.regionserver.thread.compaction.large` (預設值為1，每個) 。
將此設定的最大值上限設為小於3。

## <a name="full-table-scan"></a>完整資料表掃描

完整的資料表掃描諮詢指出所發出的掃描超過75% 是完整的資料表/區域掃描。 您可以重新流覽程式碼呼叫掃描的方式，以改善查詢效能。 請考慮下列作法：

* 為每個掃描設定適當的開始和停止資料列。

* 使用 **MultiRowRangeFilter** API，讓您可以在一個掃描呼叫中查詢不同的範圍。 如需詳細資訊，請參閱 [MULTIROWRANGEFILTER API 檔](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html)。

* 如果您需要完整的資料表或區域掃描，請檢查是否有可能避免這些查詢的快取使用量，讓使用快取的其他查詢可能無法收回熱的區塊。 若要確保掃描不會使用快取，請在您的程式碼中使用 **掃描** API 搭配 **setCaching (false)** 選項： 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>後續步驟

[使用 Ambari 將 Apache HBase 優化](../optimize-hbase-ambari.md)
