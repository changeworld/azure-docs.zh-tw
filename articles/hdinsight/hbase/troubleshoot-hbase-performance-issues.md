---
title: 針對 Azure HDInsight 上的 Apache HBase 效能問題進行疑難排解
description: 各種 Apache HBase 性能調優指南和提示，用於在 Azure HDInsight 上獲得最佳性能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887150"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>針對 Azure HDInsight 上的 Apache HBase 效能問題進行疑難排解

本文介紹了各種 Apache HBase 性能調優指南和在 Azure HDInsight 上獲得最佳性能的提示。 其中許多提示取決於特定的工作負載和讀取/寫入/掃描模式。 在生產環境中應用配置更改之前，請對其進行徹底測試。

## <a name="hbase-performance-insights"></a>HBase 性能洞察

大多數 HBase 工作負載中最大的瓶頸是預寫記錄檔 （WAL）。 它嚴重影響寫入性能。 HDInsight HBase 具有獨立的存儲計算模型。 資料遠端存放在 Azure 存儲上，即使虛擬機器承載區域伺服器也是如此。 直到最近，WAL 還寫入 Azure 存儲。 在 HDInsight 中，這種行為放大了這一瓶頸。 [加速寫入](./apache-hbase-accelerated-writes.md)功能旨在解決此問題。 它將 WAL 寫入 Azure 高級 SSD 管理的磁片。 這極大地有利於編寫性能，並且它有助於某些寫入密集型工作負載面臨的許多問題。

要顯著改善讀取操作，請使用[高級塊 Blob 存儲帳戶](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/)作為遠端存放。 僅當啟用了 WAL 功能時，才可以使用此選項。

## <a name="compaction"></a>壓 實

壓縮是社區中基本商定的另一個潛在的瓶頸。 預設情況下，在 HDInsight HBase 群集上禁用主要壓縮。 壓縮被禁用，因為即使它是一個資源密集型過程，客戶還是完全靈活地根據工作負載進行計畫。 例如，他們可能安排在非高峰時間。 此外，資料局部性不是問題，因為我們的存儲是遠端的（由 Azure 存儲支援），而不是本地 Hadoop 分散式檔案系統 （HDFS）。

客戶應在方便時安排主要壓實。 如果他們不這樣做的維護，壓實將長期對讀取性能產生負面影響。

對於掃描操作，平均延遲數遠高於 100 毫秒，應引起關注。 檢查是否準確安排了主要壓實。

## <a name="apache-phoenix-workload"></a>阿帕奇鳳凰工作負載

回答以下問題將説明您更好地瞭解您的阿帕奇鳳凰工作負載：

* 您的所有"讀取"都轉換為掃描？
    * 如果是，這些掃描的特徵是什麼？
    * 您是否針對這些掃描優化了 Phoenix 表架構，包括適當的索引？
* 您是否使用 該`EXPLAIN`語句來瞭解"讀取"生成的查詢計劃？
* 您的書寫是"向上選擇"嗎？
    * 如果是這樣，他們也會做掃描。 掃描的預期延遲平均約為 100 毫秒，而 HBase 中的點延遲為 10 毫秒。  

## <a name="test-methodology-and-metrics-monitoring"></a>測試方法和指標監控

如果你使用的是雅虎這樣的基準測試。 雲服務基準、JMeter 或 Pherf 以測試和調整性能，確保：

- 用戶端電腦不會成為瓶頸。 為此，請檢查用戶端電腦上 CPU 的 CPU 使用方式。

- 用戶端配置（如執行緒數）經過適當調整以使用戶端頻寬飽和。

- 準確系統地記錄測試結果。

如果您的查詢突然變得比以前糟糕得多，請檢查應用程式代碼中是否有潛在的錯誤。 是否突然生成大量無效資料？ 如果是，它可以增加讀取延遲。

## <a name="migration-issues"></a>遷移問題

如果要遷移到 Azure HDInsight，請確保遷移是系統且準確的，最好是通過自動化完成的。 避免手動遷移。 請確認：

- 準確遷移表屬性。 屬性可以包括壓縮、綻放篩選器等。

- Phoenix 表中的鹽漬設置將適當地映射到新的群集大小。 例如，鹽桶的數量應是群集中輔助節點數的倍數。 您應該使用一個倍數，這是熱點數的一個因素。

## <a name="server-side-configuration-tunings"></a>伺服器端配置調優

在 HDInsight HBase 中，HFiles 存儲在遠端存放中。 當緩存出現誤報時，讀取成本高於本地系統，因為本地系統上的資料由本地 HDFS 支援。 對於大多數方案，智慧使用 HBase 緩存（塊緩存和存儲桶緩存）旨在規避此問題。 在問題未被規避的情況下，使用高級塊 Blob 帳戶可能有助於解決此問題。 Windows Azure 存儲 Blob 驅動程式依賴于某些屬性`fs.azure.read.request.size`，例如根據它確定為讀取模式（順序與隨機）在塊中獲取資料，因此可能繼續存在具有讀取的較高延遲實例。 通過經驗實驗，我們發現將讀取請求塊大小 （`fs.azure.read.request.size`） 設置為 512 KB 並將 HBase 表的塊大小匹配為相同大小，從而在實踐中產生最佳結果。

對於大多數大型節點群集，HDInsight HBase 在連接到`bucketcache`運行`regionservers`的虛擬機器的本地高級 SSD 上提供作為檔。 改用堆外緩存可能會帶來一些改進。 此解決方法具有使用可用記憶體的限制，並且可能小於基於檔的緩存，因此它並不總是最佳選擇。

以下是我們調整的其他一些特定參數，似乎在不同程度上有所説明：

- 將`memstore`大小從預設的 128 MB 增加到 256 MB。 通常，對於重寫入方案，建議此設置。

- 增加專用用於壓縮的執行緒數，從預設設置**1**增加到**4**。 如果我們經常觀察到輕微的壓縮，此設置是相關的。

- 避免由於`memstore`存儲限制而阻塞刷新。 要提供此緩衝區，請將`Hbase.hstore.blockingStoreFiles`設置增加到**100**。

- 要控制刷新，請使用以下設置：

    - `Hbase.regionserver.maxlogs`： **140** （由於 WAL 限制而避免沖洗）

    - `Hbase.regionserver.global.memstore.lowerLimit`： **0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`： **0.60**

- 用於執行緒池調優的特定于鳳凰的配置：

    - `Phoenix.query.queuesize`： **10000**

    - `Phoenix.query.threadpoolsize`： **512**

- 其他特定于鳳凰的配置：

    - `Phoenix.rpc.index.handler.count`： **50** （如果有大型或多個索引查找）

    - `Phoenix.stats.updateFrequency`： **1 小時**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`： **1 小時**

    - `Phoenix.coprocessor.maxmetadatacachesize`**：50 MB**

- RPC 超時 **：3 分鐘**

   - RPC 超時包括 HBase RPC 超時、HBase 用戶端掃描器超時和鳳凰查詢超時。 
   - 確保參數在`hbase.client.scanner.caching`伺服器端和用戶端都設置為相同的值。 如果它們不同，此設置會導致與`OutOfOrderScannerException`相關的用戶端端錯誤。 對於大型掃描，此設置應設置為較低的值。 我們將此值設置為**100**。

## <a name="other-considerations"></a>其他考量

以下是需要考慮調整的其他參數：

- `Hbase.rs.cacheblocksonwrite`• 預設情況下，在 HDI 上，此設置設置為**true**。

- 允許推遲次要壓縮的設置。

- 實驗設置，例如調整為讀取和寫入請求保留的佇列百分比。

## <a name="next-steps"></a>後續步驟

如果您的問題仍未解決，請訪問以下管道之一以尋求更多支援：

- 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

- 連接到[@AzureSupport](https://twitter.com/azuresupport)。 這是用於改善客戶體驗的官方 Microsoft Azure 帳戶。 它將 Azure 社區連接到正確的資源：答案、支援和專家。

- 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱包括訪問訂閱管理和計費支援，並通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
