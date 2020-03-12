---
title: 管理能力和監視-查詢活動、資源使用率
description: 瞭解可用來管理和監視 Azure Synapse 分析的功能。 使用 Azure 入口網站和動態管理檢視 (DMV) 來了解資料倉儲的查詢活動和資源使用率。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 82bf6f9a78a46659cc2e0955895c6e1a6e6eb3aa
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096619"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>監視 Azure Synapse 分析中的資源使用率和查詢活動
Azure Synapse 分析提供 Azure 入口網站內豐富的監視體驗，以深入瞭解您的資料倉儲工作負載。 Azure 入口網站是監視資料倉儲的建議工具，因為其提供可設定的保留期、警示、建議，以及可自訂的計量與記錄圖表及儀表板。 入口網站也可讓您與其他 Azure 監視服務（例如 Azure 監視器（記錄））與 Log analytics 整合，以同時針對您的資料倉儲提供整體監視體驗，同時為您的整個 Azure 分析平臺進行整合監視體驗。 本檔說明哪些監視功能可讓您使用 SQL 分析來優化和管理您的分析平臺。 

## <a name="resource-utilization"></a>資源使用率 
SQL 分析的 Azure 入口網站中提供下列計量。 我們透過 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)提供這些計量。


| 標準名稱             | 描述                                                  | 彙總類型 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 百分比          | 資料倉儲上所有節點的 CPU 使用率      | Avg、Min、Max    |
| 資料 IO 百分比      | 資料倉儲上所有節點的 IO 使用率       | Avg、Min、Max    |
| 記憶體百分比       | 資料倉儲所有節點的記憶體使用率（SQL Server） | Avg、Min、Max   |
| 現用查詢          | 在系統上執行的使用中查詢數目             | Sum              |
| 佇列查詢          | 等待開始執行的已排入佇列查詢數目          | Sum              |
| 成功的連線  | 資料的成功連數目                 | Sum、Count       |
| 失敗的連線      | 資料倉儲的失敗連線數量           | Sum、Count       |
| 遭到防火牆封鎖     | 資料倉儲的已封鎖登入數目     | Sum、Count       |
| DWU 限制               | 資料倉儲的服務等級目標                | Avg、Min、Max    |
| DWU 百分比          | CPU 百分比與資料 IO 百分比之間的最大值        | Avg、Min、Max    |
| 已使用 DWU                | DWU 限制 * DWU 百分比                                   | Avg、Min、Max    |
| 快取命中的百分比    | (快取命中數 / 快取遺漏) * 100  其中快取命中數是在本機 SSD 快取中所有資料行存放區的區段命中數總和，而快取遺漏是本機 SSD 快取中資料行存放區的區段遺漏數 (從所有節點上加總) | Avg、Min、Max    |
| 已用快取的百分比   | (已用快取 / 快取容量) * 100 其中已用快取是本機 SSD 快取 (跨所有節點) 中的所有位元組總和，而快取容量是本機 SSD 快取 (跨所有節點) 的儲存體容量總和 | Avg、Min、Max    |
| 本機 tempdb 百分比 | 跨所有計算節點的本機 tempdb 使用率 - 此值每五分鐘發出一次 | Avg、Min、Max    |
| 資料儲存體大小 | 載入資料庫的資料大小總計。 這包括位於 CCI 和非 CCI 資料表中的資料，其中非 CCI 資料表的大小是以資料庫檔案總大小來測量。 | Sum |
| 嚴重損壞修復大小 | 每24小時進行異地備份的總大小 | Sum |
| 快照集儲存體大小 | 用來提供資料庫還原點的快照集大小總計。 這包括自動化和使用者定義的快照集。 | Sum |

在查看計量和設定警示時所要考慮的事項：

- 使用的 DWU 只代表跨 SQL 集區**使用的高階標記法**，而不是完整的使用率指示器。 若要判斷要相應增加或相應減少，請考慮可能會受到 DWU 影響的所有因素，例如平行存取、記憶體、tempdb 和調適型快取容量。 我們建議[您在不同的 DWU 設定上執行您的工作負載](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units)，以判斷最符合您業務目標的工作。
- 系統會針對特定資料倉儲回報失敗和成功的連線，而不是邏輯伺服器的連接
- 即使資料倉儲處於閒置狀態，記憶體百分比也會反映使用率，而不會反映作用中的工作負載記憶體耗用量。 使用並追蹤此計量，以及其他人（tempdb、gen2 快取），以在調整額外的快取容量時，全面決定是否會增加工作負載效能以符合您的需求。


## <a name="query-activity"></a>查詢活動
如需透過 T-sql 監視 SQL 分析時的程式設計體驗，服務會提供一組動態管理檢視（Dmv）。 若要對您的工作負載進行主動式疑難排解和識別效能瓶頸時，這些檢視十分實用。

若要查看 SQL 分析提供的 Dmv 清單，請參閱這份[檔](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>計量和診斷記錄
計量和記錄都可以匯出至 Azure 監視器，特別是[Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)檔元件，而且可以透過[記錄查詢](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)以程式設計方式存取。 SQL 分析的記錄延遲大約10-15 分鐘。 如需影響延遲的因素的詳細資訊，請造訪下列檔。


## <a name="next-steps"></a>後續步驟
下列使用說明指南會說明監視和管理資料倉儲時的常見案例及使用案例：

- [使用 DMV 監視您的資料倉儲工作負載](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
