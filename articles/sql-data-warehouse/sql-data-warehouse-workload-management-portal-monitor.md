---
title: 工作負載管理入口網站監視
description: Azure Synapse Analytics 中的工作負載管理入口網站監視指導方針。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 69a200d4fda940f072960da9224f84a22db51647
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78193793"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse 分析–工作負載管理入口網站監視（預覽）
本文說明如何監視[工作負載群組](sql-data-warehouse-workload-isolation.md#workload-groups)的資源使用率和查詢活動。 如需如何設定 Azure 計量瀏覽器的詳細資訊，請參閱[開始使用 azure 計量瀏覽器](../azure-monitor/platform/metrics-getting-started.md)一文。  如需如何監視系統資源耗用量的詳細資訊，請參閱 Azure Synapse 分析監視檔中的[資源使用率](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization)一節。
有兩種不同的工作負載群組計量分類可用於監視工作負載管理：資源配置和查詢活動。  這些計量可以依工作負載群組分割和篩選。  計量可以根據系統定義（資源類別工作負載群組）或使用者定義（由具有[CREATE 工作負載群組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語法的使用者所建立），來進行分割和篩選。

## <a name="workload-management-metric-definitions"></a>工作負載管理度量定義

|標準名稱                    |描述  |彙總類型 |
|-------------------------------|-------------|-----------------|
|有效的 cap 資源百分比 | *有效的 cap 資源百分比*是工作負載群組可存取的資源百分比固定限制，將配置給其他工作負載群組的*有效最小資源百分比*列入考慮。 *有效的 cap 資源百分比*計量是使用[建立工作負載群組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語法中的 `CAP_PERCENTAGE_RESOURCE` 參數來設定。  有效值如下所述。<br><br>例如，如果工作負載群組 `DataLoads` 是以 `CAP_PERCENTAGE_RESOURCE` = 100 建立，而且另一個工作負載群組是以25% 的有效最小資源百分比所建立，則 `DataLoads` 工作負載群組的*有效上限資源百分比*為75%。<br><br>*有效的 cap 資源百分比*會決定工作負載群組可以達到的平行存取上限（因而導致潛在的輸送量）。  如果需要額外的輸送量超過「*有效上限資源百分比*」計量目前所回報的值，請增加 `CAP_PERCENTAGE_RESOURCE`、降低其他工作負載群組的 `MIN_PERCENTAGE_RESOURCE`，或相應增加實例以新增更多資源。  減少 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可能會增加並行，但可能不會增加整體輸送量。| Min、Avg、Max |
|有效的最低資源百分比 |*有效的最小資源百分比*是針對工作負載群組所保留和隔離的最小資源百分比，以考慮最低服務層級。  有效的最小資源百分比度量是使用[建立工作負載群組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語法中的 `MIN_PERCENTAGE_RESOURCE` 參數進行設定。  有效值[如下所述。](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)<br><br>當此計量未經過篩選，並解除以監視系統上設定的總工作負載隔離時，請使用 Sum 匯總類型。<br><br>*有效的最小資源百分比*會決定工作負載群組可以達到的保證平行存取下限（因而保證輸送量）。  如果需要其他保證資源，超過「*有效的最小資源百分比*」計量目前所回報的值，請增加針對工作負載群組設定的 `MIN_PERCENTAGE_RESOURCE` 參數。  減少 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可能會增加並行，但可能不會增加整體輸送量。 |Min、Avg、Max|
|工作負載群組使用中查詢  |此計量會報告工作負載群組內的使用中查詢。  使用未篩選的此計量和解除會顯示在系統上執行的所有使用中查詢。|Sum         |
|依最大資源百分比配置的工作負載群組 |此計量會顯示相對於每個工作負載群組*之有效 cap 資源百分比*的資源配置百分比。  此計量可提供工作負載群組的有效使用率。<br><br>假設有一個工作負載群組 `DataLoads`，其*有效的 cap 資源百分比*為75%，而 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 設定為25%。  以篩選為 `DataLoads` 的*最大資源百分比值配置的工作負載群組*會是33% （25%/75%）如果單一查詢正在此工作負載群組中執行，則為。<br><br>使用此度量來識別工作負載群組的使用率。  接近100% 的值表示正在使用工作負載群組可用的所有資源。  此外，顯示值大於零的相同工作負載群組的*工作負載群組佇列查詢*計量，會指出工作負載群組會在配置時使用額外的資源。  相反地，如果此計量一致，且*工作負載群組*作用中查詢較低，則不會使用工作負載群組。  如果*有效的 cap 資源百分比*大於零，則這種情況特別有問題，因為這會指出使用量過[低的工作負載隔離](#underutilized-workload-isolation)。|Min、Avg、Max |
|依系統百分比配置的工作負載群組 | 此度量會顯示相對於整個系統的資源配置百分比。<br><br>假設有一個 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 設定為25% 的工作負載群組 `DataLoads`。  已篩選為 `DataLoads`*之系統百分比值的工作負載群組配置*會是25% （25%/100%）如果單一查詢正在此工作負載群組中執行，則為。|Min、Avg、Max |
|工作負載群組查詢超時 |查詢已超時的工作負載群組。 此計量所報告的查詢超時，只有在查詢開始執行之後（不包括因鎖定或資源等候而造成的等候時間）。<br><br>查詢超時是使用[建立工作負載群組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語法中的 `QUERY_EXECUTION_TIMEOUT_SEC` 參數來設定。  增加值可能會減少查詢超時的次數。<br><br>請考慮增加工作負載群組的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 參數，以減少超時的數量，並為每個查詢配置更多資源。  請注意，增加 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可減少工作負載群組的並行處理量。 |Sum |
|工作負載群組佇列查詢 | 查詢目前排入佇列等待開始執行的工作負載群組。  查詢可以是佇列，因為它們正在等候資源或鎖定。<br><br>查詢可能正在等候許多原因。  如果系統超載，而並行需求大於可用的數目，查詢將會排在佇列中。<br><br>請考慮增加[CREATE 工作負載群組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語句中的 `CAP_PERCENTAGE_RESOURCE` 參數，以將更多資源新增至工作負載群組。  如果 `CAP_PERCENTAGE_RESOURCE` 大於*有效的 cap 資源百分比*計量，則為其他工作負載群組設定的工作負載隔離會影響配置給此工作負載群組的資源。  請考慮降低其他工作負載群組的 `MIN_PERCENTAGE_RESOURCE`，或相應增加實例以新增更多資源。 |Sum |

## <a name="monitoring-scenarios-and-actions"></a>監視案例和動作
以下是一系列的圖表設定，以反白顯示工作負載管理計量使用方式來進行疑難排解，以及解決問題的相關聯動作。

### <a name="underutilized-workload-isolation"></a>使用量過低的工作負載隔離
請考慮下列工作負載群組和分類器設定，其中會建立名為 `wgPriority` 的工作負載群組，並使用 `wcCEOPriority` 工作負載分類器來對應*TheCEO* `membername`。  `wgPriority` 工作負載群組已為其設定了25% 的工作負載隔離（`MIN_PERCENTAGE_RESOURCE` = 25）。  *TheCEO*所提交的每個查詢都會獲得5% 的系統資源（`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5）。
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
下圖設定如下：<br>
計量1：*有效的最小資源百分比*（平均匯總，`blue line`）<br>
度量2：*依系統百分比配置的工作負載群組*（平均匯總、`purple line`）<br>
篩選準則： [工作負載群組] = `wgPriority`<br>
![underutilized-wg](media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) 圖表顯示有25% 的工作負載隔離，平均僅使用10%。  在此情況下，`MIN_PERCENTAGE_RESOURCE` 參數值可能會降到10或15之間，並允許系統上的其他工作負載耗用資源。

### <a name="workload-group-bottleneck"></a>工作負載群組瓶頸
請考慮下列工作負載群組和分類器設定，其中會建立名為 `wgDataAnalyst` 的工作負載群組，並使用 `wcDataAnalyst` 工作負載分類器來對應*DataAnalyst* `membername`。  `wgDataAnalyst` 工作負載群組已設定6% 的工作負載隔離（`MIN_PERCENTAGE_RESOURCE` = 6），而資源限制為9% （`CAP_PERCENTAGE_RESOURCE` = 9）。  *DataAnalyst*所提交的每個查詢都會提供3% 的系統資源（`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3）。

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
下圖設定如下：<br>
度量1：*有效的 cap 資源百分比*（平均匯總，`blue line`）<br>
度量2：*依最大資源百分比配置的工作負載群組*（平均匯總，`purple line`）<br>
度量3：*工作負載群組佇列查詢*（Sum 匯總，`turquoise line`）<br>
篩選準則： [工作負載群組] = `wgDataAnalyst`<br>
![瓶-necked-wg](media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) 此圖表顯示，在資源上有9% 的上限，工作負載群組會使用 90% + （從*工作負載群組配置，依最大資源百分比*計量）。  如*工作負載群組佇列查詢*計量所示，有一個穩定的查詢佇列。  在此情況下，將 `CAP_PERCENTAGE_RESOURCE` 增加為高於9% 的值，會允許同時執行更多查詢。  增加 `CAP_PERCENTAGE_RESOURCE` 會假設有足夠的資源可供使用，而且不會被其他工作負載群組隔離。  檢查*有效的 cap 資源百分比*計量，以確認已增加上限。  如果需要更多輸送量，也請考慮將 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 增加為大於3的值。  增加 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可能會讓查詢的執行速度更快。

## <a name="next-steps"></a>後續步驟
[快速入門：使用 T-sql 設定工作負載隔離](quickstart-configure-workload-isolation-tsql.md)<br>
[建立工作負載群組（Transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[建立工作負載分類器（Transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[監視資源使用率](sql-data-warehouse-concept-resource-utilization-query-activity.md)

