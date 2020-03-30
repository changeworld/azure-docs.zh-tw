---
title: 可管理性和監控 - 查詢活動、資源利用率
description: 瞭解可用於管理和監視 Azure 突觸分析的功能。 使用 Azure 入口網站和動態管理檢視 (DMV) 來了解資料倉儲的查詢活動和資源使用率。
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d2a9bb0fcaab38f897987a1922e9c95497821d4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350677"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>監視 Azure 突觸分析中的資源利用率和查詢活動
Azure Synapse 分析在 Azure 門戶中提供了豐富的監視體驗，以顯示有關資料倉儲工作負荷的見解。 Azure 入口網站是監視資料倉儲的建議工具，因為其提供可設定的保留期、警示、建議，以及可自訂的計量與記錄圖表及儀表板。 該門戶還使您能夠與其他 Azure 監視服務（如 Azure 監視器（日誌）與日誌分析集成，不僅為您的資料倉儲提供整體監視體驗，還可以為集成的整個 Azure 分析平臺提供整體監控體驗監控經驗。 本文檔介紹使用 SQL Analytics 優化和管理分析平臺的監控功能。 

## <a name="resource-utilization"></a>資源使用率 
以下指標在 Azure 門戶中可用於 SQL 分析。 我們透過 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)提供這些計量。


| 標準名稱             | 描述                                                  | 彙總類型 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 百分比          | 資料倉儲上所有節點的 CPU 使用率      | 平均，最小，最大值    |
| 資料 IO 百分比      | 資料倉儲上所有節點的 IO 使用率       | 平均，最小，最大值    |
| 記憶體百分比       | 資料倉儲所有節點的記憶體利用率 （SQL Server） | 平均，最小，最大值   |
| 活動查詢          | 在系統上執行的活動查詢數             | Sum              |
| 排隊查詢          | 等待開始執行的排隊查詢數          | Sum              |
| 成功的連線  | 資料的成功連數目                 | 總和，計數       |
| 失敗的連線      | 資料倉儲的失敗連線數量           | 總和，計數       |
| 遭到防火牆封鎖     | 資料倉儲的已封鎖登入數目     | 總和，計數       |
| DWU 限制               | 資料倉儲的服務等級目標                | 平均，最小，最大值    |
| DWU 百分比          | CPU 百分比與資料 IO 百分比之間的最大值        | 平均，最小，最大值    |
| 已使用 DWU                | DWU 限制 * DWU 百分比                                   | 平均，最小，最大值    |
| 快取命中的百分比    | (快取命中數 / 快取遺漏) * 100  其中快取命中數是在本機 SSD 快取中所有資料行存放區的區段命中數總和，而快取遺漏是本機 SSD 快取中資料行存放區的區段遺漏數 (從所有節點上加總) | 平均，最小，最大值    |
| 已用快取的百分比   | (已用快取 / 快取容量) * 100 其中已用快取是本機 SSD 快取 (跨所有節點) 中的所有位元組總和，而快取容量是本機 SSD 快取 (跨所有節點) 的儲存體容量總和 | 平均，最小，最大值    |
| 本機 tempdb 百分比 | 跨所有計算節點的本機 tempdb 使用率 - 此值每五分鐘發出一次 | 平均，最小，最大值    |
| 資料存儲大小 | 載入到資料庫中的資料的總大小。 這包括駐留在 CCI 和非 CCI 表中的資料，其中非 CCI 表的大小由資料庫檔案總大小衡量 | Sum |
| 災害復原大小 | 每 24 小時進行一次的地理備份的總大小 | Sum |
| 快照存儲大小 | 為提供資料庫還原點而拍攝的快照的總大小。 這包括自動快照和使用者定義的快照。 | Sum |

查看指標和設置警報時需要考慮的事項：

- 使用的 DWU 僅表示整個 SQL 池的**使用方式的高級表示形式**，並不表示綜合利用率指標。 要確定是向上還是向下擴展，請考慮所有可能受 DWU 影響的因素，如併發性、記憶體、tempdb 和自我調整緩存容量。 我們建議[在不同的 DWU 設置下運行您的工作負載](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units)，以確定哪些工作最符合您的業務目標。
- 報告特定資料倉儲的失敗和成功連接 - 不適用於邏輯伺服器
- 即使資料倉儲處於空閒狀態，記憶體百分比也會反映利用率 - 它不反映活動工作負載記憶體消耗。 與其他指標（tempdb，gen2 緩存）一起使用此指標並跟蹤此指標，以做出整體決策，以決定是否為其他緩存容量進行擴展將提高工作負載性能以滿足您的要求。


## <a name="query-activity"></a>查詢活動
對於通過 T-SQL 監視 SQL 分析的程式設計體驗，該服務提供了一組動態管理檢視 （DMV）。 若要對您的工作負載進行主動式疑難排解和識別效能瓶頸時，這些檢視十分實用。

要查看 SQL Analytics 提供的 DMV 清單，請參閱[本文檔](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>計量和診斷記錄
指標和日誌都可以匯出到 Azure 監視器，特別是 Azure[監視器日誌](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)元件，並且可以通過[日誌查詢](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)以程式設計方式訪問 。 SQL 分析的日誌延遲約為 10-15 分鐘。 有關影響延遲的因素的更多詳細資訊，請訪問以下文檔。


## <a name="next-steps"></a>後續步驟
下列使用說明指南會說明監視和管理資料倉儲時的常見案例及使用案例：

- [使用 DMV 監視您的資料倉儲工作負載](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
