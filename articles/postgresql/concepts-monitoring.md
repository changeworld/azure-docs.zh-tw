---
title: 監視和調整 - 適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器
description: 本文介紹了 Azure 資料庫中的監視和調優功能，用於 PostgreSQL - 單伺服器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 2d2bb5440e6d23a4cb5781244ba33ab4c5be2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252573"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>監視和調整「適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器」
監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 「適用於 PostgreSQL 的 Azure 資料庫」提供各種監視選項，可讓您深入了解伺服器的行為。

## <a name="metrics"></a>計量
適用於 PostgreSQL 的 Azure 資料庫提供多種計量，可讓您深入了解支援 PostgreSQL 伺服器資源的行為。 每個計量都會以一分鐘的頻率發出，而且可保留最多 30 天的歷程記錄。 您可以在計量上設定警示。 如需逐步指引，請參閱[如何設定警示](howto-alert-on-metric.md)。 其他工作包含設定自動化動作、執行進階分析，以及封存記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>計量清單
這些計量可供適用於 PostgreSQL 的 Azure 資料庫使用：

|計量|計量顯示名稱|單位|描述|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用中的 CPU 百分比。|
|memory_percent|記憶體百分比|百分比|使用中記憶體的百分比。|
|io_consumption_percent|IO 百分比|百分比|使用中 IO 的百分比。|
|storage_percent|儲存體百分比|百分比|使用的儲存體佔伺服器最大值的百分比。|
|storage_used|已使用儲存體|位元組|使用中的儲存體數量。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。|
|storage_limit|儲存體限制|位元組|此伺服器的儲存體上限。|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|使用的伺服器記錄儲存體佔伺服器記錄儲存體上限的百分比。|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|使用中的伺服器記錄儲存體數量。|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|此伺服器的伺服器記錄儲存體上限。|
|active_connections|作用中的連線|Count|伺服器的使用中連線數量。|
|connections_failed|失敗的連線|Count|伺服器的失敗連線數量。|
|network_bytes_egress|Network Out|位元組|跨作用中連線的網路輸出。|
|network_bytes_ingress|Network In|位元組|跨作用中連線的網路輸入。|
|backup_storage_used|已使用的備份儲存體|位元組|已使用的備份儲存體數量。|
|pg_replica_log_delay_in_bytes|複本之間的最大延隔時間|位元組|主副本和最滯後副本之間的位元組延遲。 此計量僅適用於主要伺服器。|
|pg_replica_log_delay_in_seconds|複本延隔時間|秒|自上次重播事務以來的時間。 此指標僅適用于副本伺服器。|

## <a name="server-logs"></a>伺服器記錄
您可以在伺服器上啟用記錄功能。 這些日誌也可通過[Azure 監視器日誌](../azure-monitor/log-query/log-query-overview.md)、事件中心和存儲帳戶中的 Azure 診斷日誌提供。 若要深入了解記錄，請造訪[伺服器記錄](concepts-server-logs.md)頁面。

## <a name="query-store"></a>查詢存放區
[查詢存儲](concepts-query-store.md)跟蹤查詢性能隨時間而過，包括查詢運行時統計資訊和等待事件。 此功能會將查詢執行階段效能資訊保留在名稱為 **azure_sys** 的系統資料庫之中的 query_store 結構描述下。 您可以透過各種設定旋鈕控制資料的收集和儲存。

## <a name="query-performance-insight"></a>查詢效能深入解析
[查詢效能深入解析](concepts-query-performance-insight.md)可搭配查詢存放區提供可從 Azure 入口網站存取的視覺效果。 這些圖表可讓您識別影響效能的關鍵查詢。 查詢性能見解可從 Azure 資料庫的 **"支援 + 疑難排解**"部分訪問 PostgreSQL 伺服器門戶頁面。

## <a name="performance-recommendations"></a>效能建議
[效能建議](concepts-performance-recommendations.md)功能可識別改善工作負載效能的機會。 性能建議為您提供了創建新索引的建議，這些索引有可能提高工作負載的性能。 若要產生索引建議，此功能會考量各種資料庫特性，包括查詢存放區所報告的結構描述和工作負載。 實作任何效能建議後，客戶應測試效能，以評估這些變更的影響。 

## <a name="planned-maintenance-notification"></a>計畫維護通知

**計畫維護通知**允許您接收即將計畫對 Azure 資料庫的警報，以便 PostgreSQL - 單伺服器。 這些通知與[服務運行狀況的計畫](../service-health/overview.md)維護集成，允許您在一個位置查看訂閱的所有計劃維護。 它還有助於將通知擴展到不同資源組的正確訪問群體，因為您可能有不同的連絡人負責不同的資源。 您將在活動前 72 小時收到有關即將進行的維護的通知。

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
- [瞭解如何設置警報](howto-alert-on-metric.md)，以提供有關在指標上創建警報的指導。
- 如需如何使用 Azure 入口網站、REST API 或 CLI 存取及匯出計量的詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
- 請參閱有關[監視伺服器的最佳做法](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/) \(英文\) 的部落格。
