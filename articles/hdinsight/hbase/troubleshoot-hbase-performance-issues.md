---
title: 針對 Azure HDInsight 上的 Apache HBase 效能問題進行疑難排解
description: 在 Azure HDInsight 上取得最佳效能的各種 Apache HBase 效能微調指導方針和秘訣。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 5be3f02a80524d9c4b633e1e34d581fc26bfd32d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547889"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>針對 Azure HDInsight 上的 Apache HBase 效能問題進行疑難排解

本文說明各種 Apache HBase 效能微調指導方針，以及在 Azure HDInsight 上取得最佳效能的秘訣。 其中許多秘訣都取決於特定的工作負載和讀取/寫入/掃描模式。 在生產環境中套用設定變更之前，請先徹底測試。

## <a name="hbase-performance-insights"></a>HBase 效能見解

大部分 HBase 工作負載的最大瓶頸是預先寫入記錄 (WAL) 。 它會嚴重影響寫入效能。 HDInsight HBase 有分開的儲存體計算模型。 即使虛擬機器裝載區域伺服器，資料仍會在遠端儲存在 Azure 儲存體上。 在最近，WAL 也會寫入 Azure 儲存體。 在 HDInsight 中，此行為擴大了這個瓶頸。 [加速寫入](./apache-hbase-accelerated-writes.md)功能的設計目的是要解決此問題。 它會將 WAL 寫入 Azure 進階 SSD 受控磁片。 這可大幅提升寫入效能，並且有助於許多需要大量寫入的工作負載所面臨的許多問題。

若要大幅改善讀取作業，請使用 [Premium 區塊 Blob 儲存體帳戶](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) 做為您的遠端儲存體。 只有在已啟用 WAL 功能時，才可能有此選項。

## <a name="compaction"></a>壓 實

「壓縮」是在「社區」中有基本同意的另一個潛在瓶頸。 根據預設，HDInsight HBase 叢集上的主要壓縮已停用。 壓縮已停用，因為即使是需要大量資源的程式，客戶仍有充分的彈性可根據工作負載進行排程。 例如，他們可能會在離峰時段進行排程。 此外，資料位置並不是問題，因為我們的儲存體是遠端 (Azure 儲存體) ，而不是本機 Hadoop 分散式檔案系統 (HDFS) 。

客戶應該在其便利的情況之下安排重大壓縮。 如果未進行這項維護，壓縮會對長時間的讀取效能造成負面影響。

針對掃描工作，平均延遲若大於100毫秒，則應考慮原因。 檢查是否已正確排程主要壓縮。

## <a name="apache-phoenix-workload"></a>Apache Phoenix 工作負載

回答下列問題可協助您更瞭解 Apache Phoenix 工作負載：

* 您是否會將所有「讀取」轉換成掃描？
    * 若是如此，這些掃描的特性有哪些？
    * 您是否已將這些掃描的 Phoenix 資料表架構優化，包括適當的索引編制？
* 您是否已使用 `EXPLAIN` 語句來瞭解「讀取」所產生的查詢計劃？
* 您的寫入「upsert-選取」？
    * 如果是的話，也會進行掃描。 掃描的預期延遲大約是100毫秒，相較于 HBase 中的 point 取得的10毫秒。  

## <a name="test-methodology-and-metrics-monitoring"></a>測試方法和計量監視

如果您使用的是效能評定，例如 Yahoo！ 雲端服務基準測試、JMeter 或 Pherf，以測試和微調效能，請確定：

- 用戶端電腦不會成為瓶頸。 若要這樣做，請檢查用戶端電腦上的 CPU 使用量。

- 用戶端設定（例如執行緒數目）會適當地調整以使用戶端頻寬飽和。

- 測試結果會準確且有系統地記錄。

如果您的查詢突然開始比之前更糟，請檢查應用程式程式碼中是否有潛在的錯誤。 它突然產生大量不正確資料嗎？ 如果是，則可能會增加讀取延遲。

## <a name="migration-issues"></a>移轉問題

如果您要遷移至 Azure HDInsight，請確定您的遷移是以系統的方式和正確的方式進行，最好是透過自動化進行。 避免手動遷移。 請確認：

- 資料表屬性會正確地遷移。 屬性可以包括壓縮、bloom 篩選等。

- Phoenix 資料表中的進行 salt 處理設定會適當地對應到新的叢集大小。 例如，salt 值區的數目應該是叢集中的背景工作節點數目的倍數。 而且，您應該使用具有熱找出量因素的倍數。

## <a name="server-side-configuration-tunings"></a>伺服器端設定並

在 HDInsight HBase 中，HFiles 會儲存在遠端存放裝置上。 發生快取遺漏時，讀取的成本會高於內部部署系統，因為內部部署系統上的資料會受到本機 HDFS 的支援。 在大部分的情況下， (區塊快取和值區快取的 HBase 快取) 設計來規避這個問題。 如果不會規避問題，使用 premium 區塊 blob 帳戶可能會有説明。 Windows Azure 儲存體 Blob 驅動程式依賴某些屬性（例如）， `fs.azure.read.request.size` 以根據其判斷為讀取模式 (順序與隨機) 的方式，在區塊中提取資料，因此可能會繼續進行讀取的延遲較高的實例。 透過經驗實驗，我們發現將 [讀取要求區塊大小] (`fs.azure.read.request.size`) 設定為 512 KB，並比對 HBase 資料表的區塊大小為相同大小，會產生最佳的實際結果。

針對大部分的大型節點叢集，HDInsight HBase `bucketcache` 會以本機進階 SSD 上的檔案形式提供，這些檔案會連接到執行的虛擬機器 `regionservers` 。 改為使用外堆積快取可能會提供一些改進。 此因應措施具有使用可用記憶體的限制，而且可能小於以檔案為基礎的快取，因此它可能不是最佳選擇。

以下是我們已微調的一些其他特定參數，且似乎有助於不同的角度：

- `memstore`將大小從預設值 128 mb 增加至 256 mb。 一般來說，這項設定對於繁重的寫入案例是建議的做法。

- 從預設值 **1** 到 **4** ，增加專用於壓縮的執行緒數目。 如果我們發現頻繁的次要 compactions，則這項設定是相關的。

- 避免 `memstore` 因為存放區限制而造成封鎖清除。 若要提供此緩衝區，請將 `Hbase.hstore.blockingStoreFiles` 設定增加為 **100** 。

- 若要控制排清，請使用下列設定：

    - `Hbase.regionserver.maxlogs`： **140** (避免因為 WAL 限制而造成的清除) 

    - `Hbase.regionserver.global.memstore.lowerLimit`： **0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`： **0.60**

- 執行緒集區調整的 Phoenix 特定設定：

    - `Phoenix.query.queuesize`： **10000**

    - `Phoenix.query.threadpoolsize`： **512**

- 其他 Phoenix 特定設定：

    - `Phoenix.rpc.index.handler.count`： **50** (如果有大型或許多索引查閱) 

    - `Phoenix.stats.updateFrequency`： **1 小時**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`： **1 小時**

    - `Phoenix.coprocessor.maxmetadatacachesize`： **50 MB**

- RPC 超時： **3 分鐘**

   - RPC timeout 包括 HBase RPC timeout、HBase 用戶端掃描器 timeout 和 Phoenix 查詢 timeout。 
   - 請確定在 `hbase.client.scanner.caching` 伺服器端和用戶端端都將參數設定為相同的值。 如果兩者不同，此設定會導致與相關的用戶端錯誤 `OutOfOrderScannerException` 。 針對大型掃描，此設定應該設為較低的值。 我們將此值設定為 **100** 。

## <a name="other-considerations"></a>其他考量

以下是要考慮調整的其他參數：

- `Hbase.rs.cacheblocksonwrite` –根據預設，在 HDI 上，此設定會設定為 **true** 。

- 允許稍後延遲次要壓縮的設定。

- 實驗性設定，例如調整保留給讀取和寫入要求的佇列百分比。

## <a name="next-steps"></a>下一步

如果您的問題仍無法解決，請流覽下列其中一個通道以取得更多支援：

- 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

- 聯繫 [@AzureSupport](https://twitter.com/azuresupport)。 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 它會將 Azure 群體連接到正確的資源：解答、支援及專家。

- 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)提供技術支援。