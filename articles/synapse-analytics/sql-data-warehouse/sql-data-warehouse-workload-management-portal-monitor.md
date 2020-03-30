---
title: 工作負載監管中心監控
description: Azure 突觸分析中工作負載監管中心監視指南。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f904619b1cea97849e631310cf303ed07194a01e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349924"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure 突觸分析 + 工作負載監管中心監視（預覽）
本文介紹如何監視[工作負載組](sql-data-warehouse-workload-isolation.md#workload-groups)資源利用率和查詢活動。 有關如何配置 Azure 指標資源管理器的詳細資訊，請參閱[使用 Azure 指標資源管理器入門](../../azure-monitor/platform/metrics-getting-started.md)一文。  有關如何監視系統資源消耗的詳細資訊，請參閱 Azure Synapse 分析監視文檔中的資源[利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization)部分。
為監視工作負載管理提供了兩種不同類別的工作負荷組指標：資源配置和查詢活動。  這些指標可以按工作負載組拆分和篩選。  指標可以拆分和篩選，具體取決於它們是系統定義的（資源類工作負載組）還是使用者定義的指標（由使用者使用[CREATE 工作組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語法創建）。

## <a name="workload-management-metric-definitions"></a>工作負載管理指標定義

|標準名稱                    |描述  |彙總類型 |
|-------------------------------|-------------|-----------------|
|有效上限資源百分比 | *有效上限資源百分比*是工作負載組可訪問的資源百分比的硬限制，同時考慮到分配給其他工作負載組*的有效最小資源百分比*。 使用[創建工作組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語法中的`CAP_PERCENTAGE_RESOURCE`參數配置*有效上限資源百分比*指標。  此處介紹了有效值。<br><br>`DataLoads`例如，如果使用`CAP_PERCENTAGE_RESOURCE`= 100 創建工作負荷組，並且創建另一個工作負荷組，有效最小資源百分比為 25%，`DataLoads`則工作負荷組*的有效上限資源百分比*為 75%。<br><br>*有效上限資源百分比*確定工作負載組可以達到的併發性（以及潛在輸送量）的上限。  如果需要超出*有效上限資源百分比*指標當前報告的額外輸送量，請增加`CAP_PERCENTAGE_RESOURCE`、減少`MIN_PERCENTAGE_RESOURCE`其他工作負荷組或向上擴展實例以添加更多資源。  減少`REQUEST_MIN_RESOURCE_GRANT_PERCENT`可能會增加併發性，但可能不會增加總體輸送量。| 最小值，平均，最大值 |
|有效最小資源百分比 |*有效最小資源百分比*是為工作負載組保留和隔離的資源的最小百分比，同時考慮到服務等級最小值。  使用[CREATE 工作組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語法中的`MIN_PERCENTAGE_RESOURCE`參數配置有效最小資源百分比指標。  [此處](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)介紹了有效值。<br><br>當此指標未篩選且未拆分時，請使用 Sum 聚合類型來監視系統上配置的總工作負載隔離。<br><br>*有效最小資源百分比*確定工作負載組可以實現的保證併發性（從而保證輸送量）的下限。  如果需要超出*有效最小資源百分比*指標當前報告的其他保證資源，則增加為工作負載組配置`MIN_PERCENTAGE_RESOURCE`的參數。  減少`REQUEST_MIN_RESOURCE_GRANT_PERCENT`可能會增加併發性，但可能不會增加總體輸送量。 |最小值，平均，最大值|
|工作負載組活動查詢  |此指標報告工作負荷組中的活動查詢。  使用此指標未篩選和未拆分將顯示系統上運行的所有活動查詢。|Sum         |
|按最大資源百分比分配工作負載組 |此指標顯示相對於每個工作負載組*的有效上限資源百分比*的資源配置的百分比。  此指標提供工作負載組的有效利用。<br><br>考慮有效*上限資源百分比為*75%且配置為 25% 的`REQUEST_MIN_RESOURCE_GRANT_PERCENT`工作負載組。 `DataLoads`  *按最大資源百分比值劃分的工作負載組分配*值`DataLoads`將篩選為 33%（25% / 75%）如果此工作負荷組中運行了單個查詢。<br><br>使用此指標可以標識工作負荷組的利用率。  接近 100% 的值表示正在使用工作負載組的所有可用資源。  此外，顯示值大於零的同一工作負荷組的*工作負載組排隊查詢指標*將指示工作負荷組在分配時將利用其他資源。  相反，如果此指標始終較低，並且*工作負載組活動查詢*較低，則未使用工作負載組。  如果*有效上限資源百分比*大於零，則這種情況尤其成問題，因為這將指示[未充分利用的工作負載隔離](#underutilized-workload-isolation)。|最小值，平均，最大值 |
|按系統百分比分配工作負載組 | 此指標顯示相對於整個系統的資源配置的百分比。<br><br>考慮配置為`DataLoads`25% 的`REQUEST_MIN_RESOURCE_GRANT_PERCENT`工作負載組。  *按系統百分比值*篩選`DataLoads`為 25%（25% / 100%）的工作負載組分配如果此工作負荷組中運行了單個查詢。|最小值，平均，最大值 |
|工作負載組查詢超時 |已超時的工作負載組的查詢。 此指標報告的查詢超時僅在查詢開始執行後（不包括由於鎖定或資源等待而導致的等待時間）。<br><br>使用`QUERY_EXECUTION_TIMEOUT_SEC`[創建工作組](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語法中的參數配置查詢超時。  增加該值可以減少查詢超時數。<br><br>請考慮增加工作負載`REQUEST_MIN_RESOURCE_GRANT_PERCENT`組的參數，以減少超時量，並為每個查詢分配更多資源。  請注意，增加`REQUEST_MIN_RESOURCE_GRANT_PERCENT`可減少工作負載組的併發量。 |Sum |
|工作負載組排隊查詢 | 當前排隊等待開始執行的工作負載組的查詢。  查詢可以是佇列，因為它們正在等待資源或鎖。<br><br>查詢可能等待的原因有很多。  如果系統超載，並且併發需求大於可用需求，查詢將排隊。<br><br>請考慮通過增加`CAP_PERCENTAGE_RESOURCE`["創建工作群體"](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)語句中的參數，向工作負載組添加更多資源。  如果`CAP_PERCENTAGE_RESOURCE`大於*有效上限資源百分比*指標，則其他工作負載組的已配置工作負載隔離會影響分配給此工作負載組的資源。  請考慮降低`MIN_PERCENTAGE_RESOURCE`其他工作負荷組或向上擴展實例以添加更多資源。 |Sum |

## <a name="monitoring-scenarios-and-actions"></a>監視方案和操作
下面是一系列圖表配置，用於突出顯示用於故障排除的工作負載管理指標使用方式以及解決問題的相關操作。

### <a name="underutilized-workload-isolation"></a>未充分利用的工作負載隔離
請考慮以下`wgPriority`工作負載組和分類器配置，其中創建了名為工作負荷組，並使用`wcCEOPriority`工作負載分類器映射到*TheCEO。* `membername`  工作負載`wgPriority`組為其配置了 25% 的工作負載隔離`MIN_PERCENTAGE_RESOURCE`（= 25）。  *Ceo*提交的每個查詢都得到 5% 的系統資源`REQUEST_MIN_RESOURCE_GRANT_PERCENT`（= 5）。
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
下圖的配置如下：<br>
指標 1：*有效最小資源百分比*（平均聚合`blue line`，）<br>
指標 2：*按系統百分比分配的工作負載組*（平均聚合`purple line`， ）<br>
篩檢程式： [工作負載組] |`wgPriority`<br>
![](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)圖表顯示，在 25% 的工作負載隔離下，平均僅使用 10%。  在這種情況下，`MIN_PERCENTAGE_RESOURCE`參數值可以降低到 10 或 15 之間，並允許系統上的其他工作負載消耗資源。

### <a name="workload-group-bottleneck"></a>工作負載組瓶頸
請考慮以下`wgDataAnalyst`工作負載組和分類器配置，其中創建了名為工作負荷組，並使用`wcDataAnalyst`工作負載分類器將*DataAnalyst*`membername`映射到該配置。  工作負載`wgDataAnalyst`組為其配置了 6% 的工作負載隔離`MIN_PERCENTAGE_RESOURCE`（= 6），資源限制為 9%（=`CAP_PERCENTAGE_RESOURCE` 9）。  *資料分析師*提交的每個查詢都得到系統資源的 3% （=`REQUEST_MIN_RESOURCE_GRANT_PERCENT` 3）。

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
下圖的配置如下：<br>
指標 1：*有效上限資源百分比*（平均聚合`blue line`，）<br>
指標 2：*按最大資源百分比（平均聚合）劃分工作負載組分配*。 `purple line`<br>
指標 3：*工作負載組排隊查詢*（總和聚合）， `turquoise line`<br>
篩檢程式： [工作負載組] |`wgDataAnalyst`<br>
![瓶頸-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)圖表顯示，如果資源上限為 9%，則工作負載組利用率為 90%*（從*工作負載組分配中按最大資源百分比指標）。*  如*從工作負載組排隊查詢指標*中所示，查詢有穩定的佇列。  在這種情況下，將 的值`CAP_PERCENTAGE_RESOURCE`增加到高於 9% 的值將允許同時執行更多查詢。  增加`CAP_PERCENTAGE_RESOURCE`假定有足夠的可用資源，並且其他工作負荷組不隔離。  通過檢查*有效上限資源百分比指標*來驗證增加的上限。  如果需要更多輸送量，也應考慮將 增加到`REQUEST_MIN_RESOURCE_GRANT_PERCENT`大於 3 的值。  增加`REQUEST_MIN_RESOURCE_GRANT_PERCENT`可以允許查詢運行得更快。

## <a name="next-steps"></a>後續步驟
[快速入門：使用 T-SQL 配置工作負載隔離](quickstart-configure-workload-isolation-tsql.md)<br>
[CREATE WORKLOAD GROUP (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[CREATE WORKLOAD CLASSIFIER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[監視資源利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md)

