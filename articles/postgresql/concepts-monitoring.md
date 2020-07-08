---
title: 監視和調整 - 適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器
description: 本文說明適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的監視和微調功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 89450e5518d854fbc31b43324ecb05f47e1ab5ac
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970989"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>監視和調整「適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器」
監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 「適用於 PostgreSQL 的 Azure 資料庫」提供各種監視選項，可讓您深入了解伺服器的行為。

## <a name="metrics"></a>計量
適用於 PostgreSQL 的 Azure 資料庫提供多種計量，可讓您深入了解支援 PostgreSQL 伺服器資源的行為。 每個計量都會以一分鐘的頻率發出，且最多可有[93 天的歷程記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#retention-of-metrics)。 您可以在計量上設定警示。 如需逐步指引，請參閱[如何設定警示](howto-alert-on-metric.md)。 其他工作包含設定自動化動作、執行進階分析，以及封存記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>計量清單
這些計量可供適用於 PostgreSQL 的 Azure 資料庫使用：

|計量|計量顯示名稱|單位|Description|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用中的 CPU 百分比。|
|memory_percent|記憶體百分比|百分比|使用中記憶體的百分比。|
|io_consumption_percent|IO 百分比|百分比|使用中 IO 的百分比。 （不適用於基本層伺服器）。|
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
|pg_replica_log_delay_in_bytes|複本之間的最大延隔時間|位元組|主要和最延遲複本之間的延遲（以位元組為單位）。 此計量僅適用於主要伺服器。|
|pg_replica_log_delay_in_seconds|複本延隔時間|秒|上次重新執行交易之後的時間。 此度量僅適用于複本伺服器。|

## <a name="server-logs"></a>伺服器記錄
您可以在伺服器上啟用記錄功能。 這些資源記錄可以傳送至[Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)、事件中樞和儲存體帳戶。 若要深入了解記錄，請造訪[伺服器記錄](concepts-server-logs.md)頁面。

## <a name="query-store"></a>查詢存放區
[查詢存放區](concepts-query-store.md)會持續追蹤一段時間的查詢效能，包括查詢執行時間統計資料和等候事件。 此功能會將查詢執行階段效能資訊保留在名稱為 **azure_sys** 的系統資料庫之中的 query_store 結構描述下。 您可以透過各種設定旋鈕控制資料的收集和儲存。

## <a name="query-performance-insight"></a>查詢效能深入解析
[查詢效能深入解析](concepts-query-performance-insight.md)可搭配查詢存放區提供可從 Azure 入口網站存取的視覺效果。 這些圖表可讓您識別影響效能的關鍵查詢。 查詢效能深入解析可從適用於 PostgreSQL 的 Azure 資料庫伺服器入口網站頁面的 [**支援 + 疑難排解**] 區段存取。

## <a name="performance-recommendations"></a>效能建議
[效能建議](concepts-performance-recommendations.md)功能可識別改善工作負載效能的機會。 效能建議會提供您建立新索引的建議，讓您有機會改善工作負載的效能。 若要產生索引建議，此功能會考量各種資料庫特性，包括查詢存放區所報告的結構描述和工作負載。 實作任何效能建議後，客戶應測試效能，以評估這些變更的影響。 

## <a name="planned-maintenance-notification"></a>規劃的維護通知

**規劃的維護通知**可讓您在適用於 PostgreSQL 的 Azure 資料庫單一伺服器上，收到即將進行的規劃維護警示。 這些通知會與[服務健康狀態規劃的](../service-health/overview.md)維護整合，並可讓您在一處查看訂用帳戶的所有排程維護。 它也有助於將通知調整為不同資源群組的適當物件，因為您可能會有不同的連絡人負責不同的資源。 您將會在事件發生前，收到有關即將進行之維護72小時的通知。

> [!Note]
> 我們會每次嘗試針對所有事件提供**預定的維護通知**72 時數通知。 不過，在發生重大或安全性修補程式的情況下，可能會將通知傳送到接近事件或省略。

### <a name="to-receive-planned-maintenance-notification"></a>若要接收預定的維護通知

1. 在[入口網站](https://portal.azure.com)中，選取 [服務健康情況]****。
2. 在 [警示]**** 區段中，選取 [健康情況警示]****。
3. 選取 [ **+ 新增服務健康情況警示**]，並填寫欄位。
4. 填寫必要欄位。 
5. 選擇 [**事件種類**]，選取 [**預定的維護**] 或 [全**選**]
6. 在 [**動作群組**] 中，定義您想要如何接收警示（取得電子郵件、觸發邏輯應用程式等）。  
7. 確定 [在建立時啟用規則] 設定為 [是]。
8. 選取 [**建立警示規則**] 以完成警示

如需有關如何建立**服務健康狀態警示**的詳細步驟，請參閱[建立服務通知的活動記錄警示](../service-health/alerts-activity-log-service-notifications.md)。

> [!IMPORTANT]
> 已規劃的維護通知目前在所有區域都可供預覽，美國中西部**除外**

## <a name="next-steps"></a>後續步驟
- 如需有關建立計量警示的指引，請參閱[如何設定警示](howto-alert-on-metric.md)。
- 如需如何使用 Azure 入口網站、REST API 或 CLI 存取及匯出計量的詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
- 請參閱有關[監視伺服器的最佳做法](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/) \(英文\) 的部落格。
