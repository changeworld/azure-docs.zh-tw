---
title: 工作負載重要性
description: 在 Azure Synapse 分析中設定 SQL 分析查詢重要性的指引。
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
ms.openlocfilehash: de7bb28770bc356514c392c3478fd0e33658f878
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191759"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure Synapse 分析工作負載重要性

本文說明工作負載重要性如何影響 Azure Synapse 中 SQL 分析要求的執行順序。

## <a name="importance"></a>重要性

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

商務需求可能需要資料倉儲工作負載比其他專案更重要。  請考慮在會計週期結束前載入任務關鍵性銷售資料的案例。  其他來源的資料載入（例如天氣資料）沒有嚴格的 Sla。 針對載入銷售資料的要求設定高重要性，並針對載入天氣資料的要求提供低重要性，可確保銷售資料負載能夠先取得資源的存取權，並更快完成。

## <a name="importance-levels"></a>重要性層級

重要性共有五個層級： low、below_normal、normal、above_normal 和 high。  未設定重要性的要求會指派預設層級為 [一般]。 具有相同重要性層級的要求，具有與今天相同的排程行為。

## <a name="importance-scenarios"></a>重要性案例

除了上述與銷售和氣象資料相關的基本重要性案例之外，還有工作負載重要性有助於滿足資料處理和查詢需求的其他案例。

### <a name="locking"></a>鎖定

讀取和寫入活動鎖定的存取權是自然爭用的一個區域。 [分割區切換](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition)或[重新命名物件](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest)之類的活動都需要更高的鎖定。  如果沒有工作負載重要性，Azure Synapse 中的 SQL 分析會針對輸送量進行優化。 針對輸送量進行優化，表示當執行和佇列要求具有相同的鎖定需求和資源可用時，排入佇列的要求可以略過具有較高鎖定需求的要求，並在先前的要求佇列中抵達。 將工作負載重要性套用至具有較高鎖定需求的要求。 較高重要性的要求會在要求較低的情況下執行。

請考慮下列範例：

- Q1 正在主動執行，並從 Prd.salesfact 中選取資料。
- 第2季已排入佇列，等待 Q1 完成。  它已在上午9點提交，並嘗試將新資料分割成 Prd.salesfact。
- 第3季提交于9：01am，並想要從 Prd.salesfact 中選取資料。

如果 Q2 和 Q3 具有相同的重要性，而 Q1 仍在執行中，則第3季將開始執行。 Q2 會繼續等候 Prd.salesfact 的獨佔鎖定。  如果第2季的重要性高於 Q3，第3季會等到 Q2 完成後，才能開始執行。

### <a name="non-uniform-requests"></a>非統一要求

另一個重要性可以協助滿足查詢需求的案例，就是當提交具有不同資源類別的要求時。  如先前所述，在相同的重要性之下，Azure Synapse 中的 SQL 分析會針對輸送量進行優化。 當混合大小要求（例如 smallrc 或 mediumrc）排入佇列時，SQL 分析會選擇符合可用資源的最早抵達要求。 如果套用工作負載重要性，則會排定下一個最重要的要求。
  
請在 DW500c 上考慮下列範例：

- Q1、Q2、Q3 和 Q4 正在執行 smallrc 查詢。
- Q5 是以9的 mediumrc 資源類別提交。
- Q6 是以 smallrc 資源類別提交，網址為9：01am。

因為 Q5 是 mediumrc，所以它需要兩個平行存取插槽。 Q5 需要等候兩個正在執行的查詢完成。  不過，當其中一個執行中的查詢（Q1-Q4）完成時，會立即排程 Q6，因為這些資源是用來執行查詢。  如果 Q5 的重要性高於 Q6，Q6 會等到 Q5 執行後，才可開始執行。

## <a name="next-steps"></a>後續步驟

- 如需建立分類器的詳細資訊，請參閱[建立工作負載分類器（transact-sql）](/sql/t-sql/statements/create-workload-classifier-transact-sql)。  
- 如需工作負載分類的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)。  
- 如需如何建立工作負載分類器的詳細說明，請參閱快速入門[建立工作負載分類器](quickstart-create-a-workload-classifier-tsql.md)。 
- 請參閱[設定工作負載的重要性](sql-data-warehouse-how-to-configure-workload-importance.md)的操作說明文章，以及如何[管理並監視工作負載管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)。
- 請參閱 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 以檢視查詢和所指派的重要性。
