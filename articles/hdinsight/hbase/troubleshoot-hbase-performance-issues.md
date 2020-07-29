---
title: 針對 Azure HDInsight 上的 Apache HBase 效能問題進行疑難排解
description: 取得 Azure HDInsight 最佳效能的各種 Apache HBase 效能微調指導方針和秘訣。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75887150"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>針對 Azure HDInsight 上的 Apache HBase 效能問題進行疑難排解

本文說明各種 Apache HBase 效能微調指導方針，以及在 Azure HDInsight 上取得最佳效能的秘訣。 其中有許多秘訣取決於特定的工作負載和讀取/寫入/掃描模式。 將設定變更套用到生產環境之前，請先徹底測試。

## <a name="hbase-performance-insights"></a>HBase 效能深入解析

大部分 HBase 工作負載中最重要的瓶頸是「先行寫入記錄」（WAL）。 它會嚴重影響寫入效能。 HDInsight HBase 具有分隔的儲存體計算模型。 即使虛擬機器裝載區域伺服器，資料還是會從遠端儲存在 Azure 儲存體上。 到目前為止，WAL 也會寫入 Azure 儲存體。 在 HDInsight 中，此行為會擴大此瓶頸。 [加速寫入](./apache-hbase-accelerated-writes.md)功能的設計目的是要解決此問題。 它會將 WAL 寫入 Azure 進階 SSD 受控磁片。 這麼做可大幅提升寫入效能，並協助許多大量寫入工作負載所面臨的問題。

若要大幅改善讀取作業，請使用[Premium 區塊 Blob 儲存體帳戶](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/)作為遠端存放區。 只有在已啟用 WAL 功能的情況下，才可以選擇此選項。

## <a name="compaction"></a>壓縮

「壓縮」是一種在「社區」中基本上同意的另一個潛在瓶頸。 根據預設，HDInsight HBase 叢集上會停用主要壓縮。 已停用壓縮，因為即使是需要大量資源的程式，客戶還是可以根據其工作負載，擁有完整的彈性來排程。 例如，他們可能會在離峰時段進行排程。 此外，資料位置不是問題，因為我們的儲存體是遠端（Azure 儲存體），而不是本機 Hadoop 分散式檔案系統（HDFS）。

客戶應該在方便時排程主要壓縮。 如果沒有進行這項維護，壓縮會對長期執行的讀取效能造成負面影響。

對於掃描工作而言，大於100毫秒的平均延遲應該是問題的原因。 檢查是否已正確排程主要壓縮。

## <a name="apache-phoenix-workload"></a>Apache Phoenix 工作負載

回答下列問題可協助您更瞭解 Apache Phoenix 的工作負載：

* 您的所有「讀取」轉譯為掃描嗎？
    * 若是如此，這些掃描的特性有哪些？
    * 您是否已將這些掃描的 Phoenix 資料表架構優化，包括適當的編制索引？
* 您是否已使用 `EXPLAIN` 語句來瞭解「讀取」產生的查詢計劃？
* 您的寫入是否「upsert」？
    * 若是如此，它們也會進行掃描。 掃描的預期延遲大約是100毫秒，相較于 HBase 中的 point 到達10毫秒。  

## <a name="test-methodology-and-metrics-monitoring"></a>測試方法和計量監視

如果您使用的是效能評定，例如 Yahoo！ 雲端服務基準測試、JMeter 或 Pherf 若要測試和微調效能，請確定：

- 用戶端機器不會成為瓶頸。 若要這樣做，請檢查用戶端電腦上的 CPU 使用量。

- 用戶端設定（例如執行緒數目）會適當地調整，以讓用戶端頻寬飽和。

- 測試結果會正確且有系統地記錄。

如果您的查詢突然開始比之前更糟，請檢查應用程式代碼中的潛在 bug。 它是否會突然產生大量的無效資料？ 如果是，它可能會增加讀取延遲。

## <a name="migration-issues"></a>遷移問題

如果您要遷移至 Azure HDInsight，請確定您的遷移是以有系統的方式正確地完成，最好是透過自動化進行。 避免手動遷移。 請確認：

- 資料表屬性會正確遷移。 屬性可以包含做為壓縮、bloom 篩選等。

- Phoenix 資料表中的進行 salt 處理設定會適當地對應到新的叢集大小。 例如，salt 值區的數目應該是叢集中背景工作節點數目的倍數。 而且您應該使用多個，這是熱找出量的因素。

## <a name="server-side-configuration-tunings"></a>伺服器端設定 tunings

在 HDInsight HBase 中，HFiles 會儲存在遠端存放區。 當快取遺漏時，讀取成本會高於內部部署系統，因為內部部署系統上的資料是由本機 HDFS 所支援。 在大部分的情況下，智慧型使用 HBase 快取（區塊快取和 bucket 快取）的設計是為了避免這個問題。 在無法規避問題的情況下，使用 premium 區塊 blob 帳戶可能有助於此問題。 Windows Azure 儲存體 Blob 驅動程式依賴某些屬性（例如 `fs.azure.read.request.size` ）根據其判斷為讀取模式（順序與隨機）來提取區塊中的資料，因此可能會繼續使用讀取的延遲較高的實例。 透過經驗實驗，我們發現將讀取要求區塊大小（ `fs.azure.read.request.size` ）設為 512 KB，並將 HBase 資料表的區塊大小比對為相同大小，會產生最佳的結果。

對於大部分的大型節點叢集而言，HDInsight HBase `bucketcache` 會將本機進階 SSD 上的檔案，提供給連接至虛擬機器的檔案（執行） `regionservers` 。 改為使用脫離堆積快取可能會提供一些改進。 這個因應措施有使用可用記憶體的限制，而且可能小於檔案型快取，因此不一定是最佳選擇。

以下是我們已調整的一些其他特定參數，而且似乎有助於不同程度：

- `memstore`將 [大小] 從預設值 128 mb 增加到 256 MB。 通常，這種設定對於繁重的寫入案例是建議的做法。

- 增加專用於壓縮的執行緒數目，從預設值**1**到**4**。 如果我們觀察到經常的次要 compactions，則此設定是相關的。

- 避免 `memstore` 因為存放區限制而封鎖清除。 若要提供此緩衝區，請將 `Hbase.hstore.blockingStoreFiles` 設定增加至**100**。

- 若要控制排清，請使用下列設定：

    - `Hbase.regionserver.maxlogs`： **140** （因 WAL 限制而避免清除）

    - `Hbase.regionserver.global.memstore.lowerLimit`： **0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`： **0.60**

- 執行緒集區調整的 Phoenix 特有設定：

    - `Phoenix.query.queuesize`： **10000**

    - `Phoenix.query.threadpoolsize`： **512**

- 其他 Phoenix 特有的設定：

    - `Phoenix.rpc.index.handler.count`： **50** （如果有大型或多個索引查閱）

    - `Phoenix.stats.updateFrequency`： **1 小時**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`： **1 小時**

    - `Phoenix.coprocessor.maxmetadatacachesize`： **50 MB**

- RPC 超時： **3 分鐘**

   - RPC 超時包括 HBase RPC timeout、HBase 用戶端掃描器超時和 Phoenix 查詢超時。 
   - 請確定在 `hbase.client.scanner.caching` 伺服器端和用戶端上，將參數設定為相同的值。 如果兩者不相同，則此設定會導致與相關的用戶端錯誤 `OutOfOrderScannerException` 。 這種設定應該設定為較低的值來進行大型掃描。 我們會將此值設定為**100**。

## <a name="other-considerations"></a>其他考量

以下是要考慮微調的其他參數：

- `Hbase.rs.cacheblocksonwrite`–根據預設，在 HDI 上，此設定會設定為**true**。

- 允許稍後延遲次要壓縮的設定。

- 實驗性設定，例如調整保留給讀取和寫入要求的佇列百分比。

## <a name="next-steps"></a>後續步驟

如果您的問題仍然無法解決，請流覽下列其中一個通道以取得更多支援：

- 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

- 聯繫 [@AzureSupport](https://twitter.com/azuresupport)。 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 它會將 Azure 社區連接到正確的資源：解答、支援和專家。

- 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
