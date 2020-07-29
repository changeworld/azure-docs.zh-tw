---
title: 監視-適用於 MariaDB 的 Azure 資料庫
description: 本文說明適用於 MariaDB 的 Azure 資料庫之監視和警示的計量，包括 CPU、儲存體和連線統計資料。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 708b243d9db16ee8454b4bc0f5c136b9f4399916
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85413190"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>在適用於 MariaDB 的 Azure 資料庫中監視
監視伺服器的相關資料，可協助您疑難排解並最佳化您的工作負載。 適用於 MariaDB 的 Azure 資料庫提供多種計量，可讓您深入了解您伺服器的行為。

## <a name="metrics"></a>計量
所有 Azure 計量都有一分鐘頻率，且每個計量皆提供 30 天的記錄。 您可以在計量上設定警示。 其他工作包含設定自動化動作、執行進階分析，以及封存記錄。 如需詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

如需逐步指引，請參閱[如何設定警示](howto-alert-metric.md)。

### <a name="list-of-metrics"></a>計量清單
這些計量可供適用於 MariaDB 的 Azure 資料庫使用：

|計量|計量顯示名稱|單位|Description|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用中的 CPU 百分比。|
|memory_percent|記憶體百分比|百分比|使用中記憶體的百分比。|
|io_consumption_percent|IO 百分比|百分比|使用中 IO 的百分比。 （不適用於基本層伺服器）。|
|storage_percent|儲存體百分比|百分比|使用的儲存體佔伺服器最大值的百分比。|
|storage_used|已使用儲存體|位元組|使用中的儲存體數量。 此服務所使用的儲存體可能包括資料庫檔案、交易記錄和伺服器記錄。|
|serverlog_storage_percent|伺服器記錄儲存體百分比|百分比|使用的伺服器記錄儲存體佔伺服器記錄儲存體上限的百分比。|
|serverlog_storage_usage|使用的伺服器記錄儲存體|位元組|使用中的伺服器記錄儲存體數量。|
|serverlog_storage_limit|伺服器記錄儲存體限制|位元組|此伺服器的伺服器記錄儲存體上限。|
|storage_limit|儲存體限制|位元組|此伺服器的儲存體上限。|
|active_connections|作用中的連線|Count|伺服器的使用中連線數量。|
|connections_failed|失敗的連線|Count|伺服器的失敗連線數量。|
|network_bytes_egress|Network Out|位元組|跨作用中連線的網路輸出。|
|network_bytes_ingress|Network In|位元組|跨作用中連線的網路輸入。|

## <a name="server-logs"></a>伺服器記錄

您可以在伺服器上啟用慢速查詢記錄。 您也可以透過 Azure 監視器記錄、事件中樞和儲存體帳戶中的 Azure 診斷記錄來取得這些記錄。 若要深入了解記錄，請瀏覽 [伺服器記錄](concepts-server-logs.md)頁面。

## <a name="query-store"></a>查詢存放區

[查詢存放區](concepts-query-store.md)會持續追蹤一段時間的查詢效能，包括查詢執行時間統計資料和等候事件。 此功能會將查詢執行時間效能資訊保存在**mysql**架構中。 您可以透過各種設定旋鈕控制資料的收集和儲存。

## <a name="query-performance-insight"></a>查詢效能深入解析

[查詢效能深入解析](concepts-query-performance-insight.md)可搭配查詢存放區提供可從 Azure 入口網站存取的視覺效果。 這些圖表可讓您識別影響效能的關鍵查詢。 查詢效能深入解析可在適用於 MariaDB 的 Azure 資料庫伺服器入口網站頁面的 [**智慧效能**] 區段中存取。

## <a name="performance-recommendations"></a>效能建議

[效能建議](concepts-performance-recommendations.md)功能可識別改善工作負載效能的機會。 效能建議會提供您建立新索引的建議，讓您有機會改善工作負載的效能。 若要產生索引建議，此功能會考量各種資料庫特性，包括查詢存放區所報告的結構描述和工作負載。 實作任何效能建議後，客戶應測試效能，以評估這些變更的影響。

## <a name="planned-maintenance-notification"></a>規劃的維護通知

**規劃的維護通知**可讓您收到即將進行的適用於 MariaDB 的 Azure 資料庫規劃維護的警示。 這些通知會與[服務健康狀態規劃的](../service-health/overview.md)維護整合，並可讓您在一處查看訂用帳戶的所有排程維護。 它也有助於將通知調整為不同資源群組的適當物件，因為您可能會有不同的連絡人負責不同的資源。 您將會在事件發生前，收到有關即將進行之維護72小時的通知。

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

- 如需如何使用 Azure 入口網站、REST API 或 CLI 存取及匯出計量的詳細資訊，請參閱 [Azure 計量概觀](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
  - 請參閱[如何設定警示](howto-alert-metric.md)，取得根據計量來建立警示的指引。
