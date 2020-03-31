---
title: 監視 - MySQL 的 Azure 資料庫
description: 本文說明適用於 MySQL 的 Azure 資料庫之監視和警示的計量，包括 CPU、儲存體和連線統計資料。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2de04bbb1523151ac566b78bf99eba34c437fccd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537086"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>在適用於 MySQL 的 Azure 資料庫中監視
監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 適用於 MySQL 的 Azure 資料庫提供多種計量，可讓您深入了解您伺服器的行為。

## <a name="metrics"></a>計量
所有 Azure 計量都有一分鐘頻率，且每個計量皆提供 30 天的記錄。 您可以在計量上設定警示。 如需逐步指引，請參閱[如何設定警示](howto-alert-on-metric.md)。 其他工作包含設定自動化動作、執行進階分析，以及封存記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>計量清單
這些計量可供適用於 MySQL 的 Azure 資料庫使用：

|計量|計量顯示名稱|單位|描述|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用中的 CPU 百分比。|
|memory_percent|記憶體百分比|百分比|使用中記憶體的百分比。|
|io_consumption_percent|IO 百分比|百分比|使用中 IO 的百分比。|
|storage_percent|儲存體百分比|百分比|使用的儲存體佔伺服器最大值的百分比。|
|storage_used|已使用儲存體|位元組|使用中的儲存體數量。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|使用的伺服器記錄儲存體佔伺服器記錄儲存體上限的百分比。|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|使用中的伺服器記錄儲存體數量。|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|此伺服器的伺服器記錄儲存體上限。|
|storage_limit|儲存體限制|位元組|此伺服器的儲存體上限。|
|active_connections|作用中的連線|Count|伺服器的使用中連線數量。|
|connections_failed|失敗的連線|Count|伺服器的失敗連線數量。|
|seconds_behind_master|複寫延遲 (秒)|Count|複本伺服器與主要伺服器相較下延遲的秒數。|
|network_bytes_egress|Network Out|位元組|跨作用中連線的網路輸出。|
|network_bytes_ingress|Network In|位元組|跨作用中連線的網路輸入。|
|backup_storage_used|已使用的備份儲存體|位元組|已使用的備份儲存體數量。|

## <a name="server-logs"></a>伺服器記錄
您可以在伺服器上啟用慢速查詢和稽核記錄記錄。 這些日誌也可通過 Azure 監視器日誌、事件中心和存儲帳戶中的 Azure 診斷日誌提供。 要瞭解有關日誌記錄的更多詳細資訊，請訪問 [稽核記錄](concepts-audit-logs.md)並[緩慢查詢日誌](concepts-server-logs.md)文章。

## <a name="query-store"></a>查詢存放區
[查詢存儲](concepts-query-store.md)是一種功能，用於跟蹤查詢性能，包括查詢運行時統計資訊和等待事件。 該功能在**mysql**架構中保留查詢運行時性能資訊。 您可以透過各種設定旋鈕控制資料的收集和儲存。

## <a name="query-performance-insight"></a>查詢效能深入解析
[查詢效能深入解析](concepts-query-performance-insight.md)可搭配查詢存放區提供可從 Azure 入口網站存取的視覺效果。 這些圖表可讓您識別影響效能的關鍵查詢。 查詢性能見解可在 MySQL 伺服器門戶頁的 Azure 資料庫的**智慧性能**部分中訪問。

## <a name="performance-recommendations"></a>效能建議
[效能建議](concepts-performance-recommendations.md)功能可識別改善工作負載效能的機會。 性能建議為您提供了創建新索引的建議，這些索引有可能提高工作負載的性能。 若要產生索引建議，此功能會考量各種資料庫特性，包括查詢存放區所報告的結構描述和工作負載。 實作任何效能建議後，客戶應測試效能，以評估這些變更的影響。

## <a name="planned-maintenance-notification"></a>計畫維護通知

**計畫維護通知**允許您接收即將計畫對 MySQL Azure 資料庫進行的維護警報。 這些通知與[服務運行狀況的計畫](../service-health/overview.md)維護集成，允許您在一個位置查看訂閱的所有計劃維護。 它還有助於將通知擴展到不同資源組的正確訪問群體，因為您可能有不同的連絡人負責不同的資源。 您將在活動前 72 小時收到有關即將進行的維護的通知。

> [!Note]
> 我們將盡力為所有事件提供**計畫維護通知**72 小時通知。 但是，在嚴重或安全修補程式的情況下，可能會將通知發送得更接近事件或被省略。

### <a name="to-receive-planned-maintenance-notification"></a>接收計畫維護通知

1. 在[入口網站](https://portal.azure.com)中，選取 [服務健康情況]****。
2. 在 [警示]**** 區段中，選取 [健康情況警示]****。
3. 選擇 **= 添加服務運行狀況警報**並填寫欄位。
4. 填寫所需的欄位。 
5. 選擇**事件種類**，選擇 **"計畫維護**"或 **"全部"選擇**
6. 在 **"操作"組中**定義您希望如何接收警報（獲取電子郵件、觸發邏輯應用等）  
7. 確保創建時的啟用規則設置為"是"。
8. 選擇 **"創建警報規則**"以完成警報

有關如何創建**服務運行狀況警報**的詳細步驟，請參閱[在服務通知上創建活動日誌警報](../service-health/alerts-activity-log-service-notifications.md)。

> [!IMPORTANT]
> 計畫維護通知當前處於預覽狀態

## <a name="next-steps"></a>後續步驟
- 請參閱[如何設定警示](howto-alert-on-metric.md)，取得根據計量來建立警示的指引。
- 如需如何使用 Azure 入口網站、REST API 或 CLI 存取及匯出計量的詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
- 請參閱有關[監視伺服器的最佳做法](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/) \(英文\) 的部落格。
