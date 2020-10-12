---
title: 管理能力和監視 - 查詢活動、資源使用率
description: 了解哪些功能可用來管理和監視 Azure Synapse Analytics。 使用 Azure 入口網站和動態管理檢視 (DMV) 來了解資料倉儲的查詢活動和資源使用率。
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 334d37ad36336d1aa737894482fd8f66e929c822
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077676"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>監視 Azure Synapse Analytics 中的資源使用率和查詢活動

Azure Synapse Analytics 在 Azure 入口網站中提供豐富的監視體驗，讓您可看到有關資料倉儲工作負載的深入解析。 Azure 入口網站是監視資料倉儲的建議工具，因為其提供可設定的保留期、警示、建議，以及可自訂的計量與記錄圖表及儀表板。 入口網站也可讓您整合其他 Azure 監視服務 (例如 Azure 監視器 (記錄) 和記錄分析) 以提供全面性監視體驗，不只監視資料倉儲，還有您的整個 Azure 分析平台，讓您擁有整合的監視體驗。 本文件將說明哪些監視功能可用來最佳化及管理分析平台與 Synapse SQL。

## <a name="resource-utilization"></a>資源使用率

在 Azure 入口網站中，下列計量可供 Synapse SQL 使用。 我們透過 [Azure 監視器](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics)提供這些計量。

| 標準名稱             | 描述                                                  | 彙總類型 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 百分比          | 資料倉儲上所有節點的 CPU 使用率      | 平均值、最小值、最大值    |
| 資料 IO 百分比      | 資料倉儲上所有節點的 IO 使用率       | 平均值、最小值、最大值    |
| 記憶體百分比       | 資料倉儲上所有節點之間的記憶體使用率 (SQL Server) | 平均值、最小值、最大值   |
| 使用中查詢          | 在系統上執行的使用中查詢數目             | Sum              |
| 已排入佇列的查詢          | 等待開始執行的已排入佇列查詢數目          | Sum              |
| 成功的連線  | 資料庫的成功連線 (登入) 數目 | 加總、計數       |
| 失敗的連線      | 資料庫的失敗連線 (登入) 數目 | 加總、計數       |
| 遭到防火牆封鎖     | 資料倉儲的已封鎖登入數目     | 加總、計數       |
| DWU 限制               | 資料倉儲的服務等級目標                | 平均值、最小值、最大值    |
| DWU 百分比          | CPU 百分比與資料 IO 百分比之間的最大值        | 平均值、最小值、最大值    |
| 已使用 DWU                | DWU 限制 * DWU 百分比                                   | 平均值、最小值、最大值    |
| 快取命中的百分比    | (快取命中數 / 快取遺漏) * 100  其中快取命中數是在本機 SSD 快取中所有資料行存放區的區段命中數總和，而快取遺漏是本機 SSD 快取中資料行存放區的區段遺漏數 (從所有節點上加總) | 平均值、最小值、最大值    |
| 已用快取的百分比   | (已用快取 / 快取容量) * 100 其中已用快取是本機 SSD 快取 (跨所有節點) 中的所有位元組總和，而快取容量是本機 SSD 快取 (跨所有節點) 的儲存體容量總和 | 平均值、最小值、最大值    |
| 本機 tempdb 百分比 | 跨所有計算節點的本機 tempdb 使用率 - 此值每五分鐘發出一次 | 平均值、最小值、最大值    |

在檢視計量和設定警示時要考量的事項：

- 使用的 DWU 只代表 SQL 集區之間**使用量的高階表示法**，而不是完整的使用率指標。 若要判斷要擴大或縮小，請考量會受到 DWU 影響的所有因素，例如並行存取、記憶體、tempdb 和調適型快取容量。 建議[在不同的 DWU 設定中執行您的工作負載](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units)，以判斷最符合您營運目標的方式。
- 針對特定的資料倉儲報告失敗和成功的連線，而非伺服器本身的連接。
- 即使資料倉儲處於閒置狀態，記憶體百分比也會反映使用率，而不會反映使用中工作負載記憶體耗用量。 使用並追蹤此計量，以及其他項目 (tempdb、gen2 快取)，在調整額外快取容量會增加工作負載效能的情況下，制定整體決策以符合您的需求。

## <a name="query-activity"></a>查詢活動

為了在透過 T-SQL 監視 Synapse SQL 時有程式設計體驗，此服務提供一組動態管理檢視 (DMV)。 若要對您的工作負載進行主動式疑難排解和識別效能瓶頸時，這些檢視十分實用。

若要檢視適用於 Synapse SQL 的 DMV 清單，請參閱此[文件](../sql/reference-tsql-system-views.md#sql-pool-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>計量和診斷記錄 

計量和記錄都可以匯出至 Azure 監視器 (尤其是 [Azure 監視器記錄](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)元件)，而且可以透過[記錄查詢](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)以程式設計方式進行存取。 Synapse SQL 的記錄延遲大約是 10 到 15 分鐘。 如需影響延遲因素的詳細資訊，請參閱下列文件。

## <a name="next-steps"></a>後續步驟

下列使用說明指南會說明監視和管理資料倉儲時的常見案例及使用案例：

- [使用 DMV 監視您的資料倉儲工作負載](sql-data-warehouse-manage-monitor.md)
