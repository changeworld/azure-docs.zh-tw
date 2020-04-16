---
title: 可管理性和監控 - 查詢活動、資源利用率
description: 瞭解可用於管理和監視 Azure 突觸分析的功能。 使用 Azure 入口網站和動態管理檢視 (DMV) 來了解資料倉儲的查詢活動和資源使用率。
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d38c0df45da3a751a456846813543a4ce5de98eb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416206"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>監視 Azure 突觸分析中的資源利用率與查詢活動

Azure Synapse 分析在 Azure 門戶中提供了豐富的監視體驗,以顯示有關數據倉庫工作負荷的見解。 Azure 入口網站是監視資料倉儲的建議工具，因為其提供可設定的保留期、警示、建議，以及可自訂的計量與記錄圖表及儀表板。 該門戶還使您能夠與其他 Azure 監視服務(如 Azure 監視器 (log) 與日誌分析集成,不僅為您的數據倉庫提供整體監視體驗,還可以為集成監視體驗的整個 Azure 分析平臺提供整體監控體驗。 本文件介紹使用 SQL Analytics 優化和管理分析平臺的監控功能。

## <a name="resource-utilization"></a>資源使用率

以下指標在 Azure 門戶中可用於 SQL 分析。 我們透過 [Azure 監視器](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics)提供這些計量。

| 標準名稱             | 描述                                                  | 彙總類型 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 百分比          | 資料倉儲上所有節點的 CPU 使用率      | 平均,最小,最大值    |
| 資料 IO 百分比      | 資料倉儲上所有節點的 IO 使用率       | 平均,最小,最大值    |
| 記憶體百分比       | 資料倉儲所有節點的記憶體利用率 (SQL Server) | 平均,最小,最大值   |
| 活動查詢          | 在系統上執行的活動查詢數             | Sum              |
| 佇列查詢          | 等待開始執行的佇列查詢數          | Sum              |
| 成功的連線  | 針對資料庫的成功連線(登入次數) | 總和,計數       |
| 失敗的連線      | 針對資料庫的失敗連線(登入次數) | 總和,計數       |
| 遭到防火牆封鎖     | 資料倉儲的已封鎖登入數目     | 總和,計數       |
| DWU 限制               | 資料倉儲的服務等級目標                | 平均,最小,最大值    |
| DWU 百分比          | CPU 百分比與資料 IO 百分比之間的最大值        | 平均,最小,最大值    |
| 已使用 DWU                | DWU 限制 * DWU 百分比                                   | 平均,最小,最大值    |
| 快取命中的百分比    | (快取命中數 / 快取遺漏) * 100  其中快取命中數是在本機 SSD 快取中所有資料行存放區的區段命中數總和，而快取遺漏是本機 SSD 快取中資料行存放區的區段遺漏數 (從所有節點上加總) | 平均,最小,最大值    |
| 已用快取的百分比   | (已用快取 / 快取容量) * 100 其中已用快取是本機 SSD 快取 (跨所有節點) 中的所有位元組總和，而快取容量是本機 SSD 快取 (跨所有節點) 的儲存體容量總和 | 平均,最小,最大值    |
| 本機 tempdb 百分比 | 跨所有計算節點的本機 tempdb 使用率 - 此值每五分鐘發出一次 | 平均,最小,最大值    |
| 資料儲存大小 (GB) | 資料庫的總大小。 這包括已使用、保留和未分配的空間。 保留未分配的空間供資料庫優化查詢和載入性能。 | Sum |
| 災害回復大小 (GB) | 每 24 小時進行一次的地理備份的總大小 | Sum |
| 快照儲存大小 (GB) | 為提供資料庫還原點而拍攝的快照的總大小。 這包括自動快照和使用者定義的快照。 | Sum |

檢視指標與設定警示時需要考慮的事項:

- 使用的 DWU 僅表示整個 SQL 池的**使用方式的高級表示形式**,並不表示綜合利用率指標。 要確定是向上還是向下擴展,請考慮所有可能受 DWU 影響的因素,如併發性、記憶體、tempdb 和自適應緩存容量。 我們建議[在不同的 DWU 設置下運行您的工作負載](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units),以確定哪些工作最符合您的業務目標。
- 回報特定資料倉儲失敗與成功連線 ─ 不適用於邏輯伺服器
- 即使數據倉庫處於空閒狀態,記憶體百分比也會反映利用率 - 它不反映活動工作負載記憶體消耗。 與其他指標(tempdb,gen2 快取)一起使用此指標並跟蹤此指標,以做出整體決策,以決定是否為其他緩存容量進行擴展將提高工作負載性能以滿足您的要求。

## <a name="query-activity"></a>查詢活動

對於透過 T-SQL 監視 SQL 分析的程式設計體驗,該服務提供了一組動態管理視圖 (DMV)。 若要對您的工作負載進行主動式疑難排解和識別效能瓶頸時，這些檢視十分實用。

要檢視適用於 Synapse SQL 的 DMV 清單,請參考[此文件](../sql/reference-tsql-system-views.md#sql-pool-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>計量和診斷記錄 

指標和日誌都可以匯出到 Azure 監視器,特別是 Azure[監視器紀錄](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)元件,並且可以透過[日誌查詢](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)以程式設計方式存取 。 SQL 分析的日誌延遲約為 10-15 分鐘。 有關影響延遲的因素的更多詳細資訊,請訪問以下文檔。

## <a name="next-steps"></a>後續步驟

以下操作指南描述了監視和管理資料主目錄時的常見方案和用例:

- [使用 DMV 監視您的資料倉儲工作負載](sql-data-warehouse-manage-monitor.md)
