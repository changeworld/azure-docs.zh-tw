---
title: 工作負載管理入口網站監視
description: Azure Synapse Analytics 中的工作負載管理入口網站監視指導方針。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 4f46ed1890bb62acc92eea28c55bf9abd6153e8b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208683"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics - 工作負載管理入口網站監視

本文說明如何監視[工作負載群組](sql-data-warehouse-workload-isolation.md#workload-groups)資源使用率和查詢活動。
如需如何設定 Azure 計量瀏覽器的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)文章。  如需如何監視系統資源耗用量的詳細資訊，請參閱 Azure Synapse Analytics 監視文件中的[資源使用率](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization)一節。
有兩種不同的工作負載群組計量分類可用於監視工作負載管理：資源配置和查詢活動。  這些計量可以依工作負載群組分割和篩選。  計量可以根據系統定義 (資源類別工作負載群組) 或使用者定義 (由使用者以 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 語法建立) 來進行分割和篩選。

## <a name="workload-management-metric-definitions"></a>工作負載管理計量定義

|標準名稱                    |描述  |彙總類型 |
|-------------------------------|-------------|-----------------|
|有效的容量資源百分比 | 有效的容量資源百分比是工作負載群組可存取的資源百分比固定限制，此數值會將配置給其他工作負載群組的「有效的最低資源百分比」列入考量。 有效的容量資源百分比計量是使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 語法中的 `CAP_PERCENTAGE_RESOURCE` 參數來設定。  有效值如此處所述。<br><br>例如，如果工作負載群組 `DataLoads` 是以 `CAP_PERCENTAGE_RESOURCE` = 100 建立，而且另一個工作負載群組是以 25% 的有效最小資源百分比所建立，則 `DataLoads` 工作負載群組的「有效容量上限資源百分比」 為75%。<br><br>有效的容量資源百分比會決定工作負載群組可以達成的平行存取上限 (以及因而導致的潛在輸送量)。  如果需要的額外輸送量超過「有效容量資源百分比」計量目前所回報的值，請增加 `CAP_PERCENTAGE_RESOURCE`、減少其他工作負載群組的 `MIN_PERCENTAGE_RESOURCE`，或擴大執行個體以新增更多資源。  減少 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可能會增加並行，但可能不會增加整體輸送量。| Min、Avg、Max |
|有效的最低資源百分比 |有效的最低資源百分比是針對工作負載群組將服務層級所需的最低資源列入考量，所保留和隔離的最低資源百分比。  有效的最低資源百分比計量是使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 語法中的 `MIN_PERCENTAGE_RESOURCE` 參數來設定。  有效值如[此處](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values)所述。<br><br>當此計量未經過篩選或分割時，請使用 Sum 匯總類型以監視系統上設定的總工作負載隔離。<br><br>「有效的最低資源百分比」會決定工作負載群組可以達成的保證平行存取較低繫結 (以及因而導致的保證輸送量)。  如果需要的額外保證資源數量比「有效的最低資源百分比」計量目前回報的數量多，請增加針對工作負載群組設定的 `MIN_PERCENTAGE_RESOURCE` 參數。  減少 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可能會增加並行，但可能不會增加整體輸送量。 |Min、Avg、Max|
|工作負載群組使用中查詢  |此計量會回報工作負載群組內的使用中查詢。  使用未篩選或分矵的此計量可顯示在系統上執行的所有使用中查詢。|Sum         |
|依最大資源百分比配置的工作負載群組 |此標準會顯示相對於每個工作負載群組的「有效容量資源百分比」的資源配置百分比。  此計量可提供工作負載群組的有效使用率。<br><br>假設有一個工作負載群組 `DataLoads` 其「有效的容量資源百分比」為 75%，而 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 設定為25%。  如果單一查詢在此工作負載群組中執行，則篩選為 `DataLoads` 的「依資源百分比上限配置的工作負載群組」值會是 33% (25%/75%)。<br><br>使用此計量來識別工作負載群組的使用率。  接近 100% 的值表示工作負載群組可用的所有資源均在使用中。  此外，顯示值大於零之相同工作負載群組的「已排入佇列的工作負載群組查詢計量」會指出若已配置，工作負載群組會使用的額外資源。  相反地，如果此計量一致較低，且「工作負載群組使用中查詢」 也較低，則表示系統未使用工作負載群組。  如果「有效的容量資源百分比」大於零，表示[工作負載隔離的使用量過低](#underutilized-workload-isolation)，這種情況會特別有問題。|Min、Avg、Max |
|依系統百分比配置的工作負載群組 | 此計量會顯示相對於整個系統的資源配置百分比。<br><br>假設有一個 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 設定為 25% 的工作負載群組 `DataLoads`。  如果單一查詢在此工作負載群組中執行，則篩選為 `DataLoads` 的「依系統百分比配置的工作負載群組」值會是 25% (25%/100%)。|Min、Avg、Max |
|工作負載群組查詢逾時 |已逾時的工作負載群組查詢。此計量所報告的查詢逾時，只包括在查詢開始執行之後的查詢逾時 (不包括因鎖定或資源等候而造成的等候時間)。<br><br>系統會使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 語法中的 `QUERY_EXECUTION_TIMEOUT_SEC` 設定查詢逾時。  增加值可能會減少查詢逾時的次數。<br><br>請考慮增加工作負載群組的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 參數，以減少逾時的次數，並為每個查詢配置更多資源。  請注意，增加 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可減少工作負載群組的並行處理量。 |Sum |
|已排入佇列的工作負載群組查詢 | 查詢目前已排入佇列等待開始執行的工作負載群組。  查詢可以排入佇列，因為它們正在等候資源或鎖定。<br><br>查詢可能因為許多原因而正在等候。  如果系統多載，而並行需求大於可用的數量，查詢將會排入佇列。<br><br>請考慮在 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 陳述式中增加 `CAP_PERCENTAGE_RESOURCE` 參數，以將更多資源新增至工作負載群組。  如果 `CAP_PERCENTAGE_RESOURCE` 大於「有效的容量資源百分比」計量，則為其他工作負載群組設定的工作負載隔離會影響配置給此工作負載群組的資源。  請考慮降低其他工作負載群組的 `MIN_PERCENTAGE_RESOURCE`，或擴大執行個體以新增更多資源。 |Sum |

## <a name="monitoring-scenarios-and-actions"></a>監視案例和動作

以下是一系列的圖表組態，將工作負載管理計量使用量反白顯示以進行疑難排解，同時提供解決問題的相關動作。

### <a name="underutilized-workload-isolation"></a>使用量過低的工作負載隔離

請考慮下列工作負載群組和分類器組態，其中會建立名為 `wgPriority` 的工作負載群組，並使用 `wcCEOPriority` 工作負載分類器，將 TheCEO `membername` 與其對應。  `wgPriority` 工作負載群組的工作負載隔離已設定為 25% (`MIN_PERCENTAGE_RESOURCE` = 25)。  TheCEO 提交的每個查詢都會獲得 5% 的系統資源 (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5)。

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

下列圖表設定如下：<br>
計量 1：有效的最低資源百分比 (Avg 彙總，`blue line`)<br>
計量 2：依系統百分比配置的工作負載群組 (Avg 彙總，`purple line`)<br>
篩選：[Workload Group] = `wgPriority`<br>
![underutilized-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) 圖表顯示 25% 的工作負載隔離中，平均使用率只有 10%。  在此情況下，`MIN_PERCENTAGE_RESOURCE` 參數值可能會降到 10 或 15 之間，並允許系統上的其他工作負載耗用資源。

### <a name="workload-group-bottleneck"></a>工作負載群組瓶頸

請考慮下列工作負載群組和分類器組態，其中會建立名為 `wgDataAnalyst` 的工作負載群組，並使用 `wcDataAnalyst` 工作負載分類器，將 DataAnalyst `membername` 與其對應。  `wgDataAnalyst` 工作負載群組的工作負載隔離已設定為 6% (`MIN_PERCENTAGE_RESOURCE` = 6)，資源且資源限制設定為 9% (`CAP_PERCENTAGE_RESOURCE` = 9)。  DataAnalyst 提交的每個查詢都會獲得 3% 的系統資源 (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3)。

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

下列圖表設定如下：<br>
計量 1：有效的容量資源百分比 (Avg 彙總，`blue line`)<br>
計量 2：依最大資源百分比配置的工作負載群組 (Avg 彙總，`purple line`)<br>
計量 3：已排入佇列的工作負載群組查詢 (Sum 彙總，`turquoise line`)<br>
篩選：[Workload Group] = `wgDataAnalyst`<br>
![bottle-necked-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) 圖表顯示在資源上有 9% 的容量，工作負載群組已使用90% 以上 (來自「依最大資源百分比計量配置的工作負載群組」)。  如「已排入佇列的工作負載群組查詢計量」所示，查詢佇列十分穩定。  在此情況下，將 `CAP_PERCENTAGE_RESOURCE` 增加為大於 9% 的值，可允許同時執行更多查詢。  增加 `CAP_PERCENTAGE_RESOURCE` 會假設有足夠的資源可供使用，而且不會被其他工作負載群組隔離。  檢查「有效的容量資源百分比計量」，以確認已增加容量。  如果需要更多輸送量，也請考慮將 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 增加為大於 3 的值。  增加 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可能會讓查詢的執行速度更快。

## <a name="next-steps"></a>後續步驟

- [快速入門：使用 T-SQL 設定工作負載隔離](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [監視資源使用率](sql-data-warehouse-concept-resource-utilization-query-activity.md)
