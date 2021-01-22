---
title: 工作負載重要性
description: 針對 Azure Synapse Analytics 中的專用 SQL 集區查詢設定重要性的指引。
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
ms.openlocfilehash: 06d1957d182f2cabc336afcfc47a790442a3cb9a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678401"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure Synapse Analytics 工作負載重要性

本文說明工作負載重要性如何影響 Azure Synapse 中專用 SQL 集區要求的執行順序。

## <a name="importance"></a>重要性

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

商務需求可能需要資料倉儲工作負載比其他工作負載更重要。  請考慮在會計週期結束之前載入任務關鍵性銷售資料的案例。  其他來源的資料載入（例如天氣資料）沒有嚴格的 Sla。 針對載入銷售資料的要求設定高重要性，並針對載入氣象資料的要求設定低重要性，可確保銷售資料負載可先取得資源的存取權，並以更快的速度完成。

## <a name="importance-levels"></a>重要性層級

有五個重要性層級：低、below_normal、正常、above_normal 和高。  未設定重要性的要求會指派預設層級為 [正常]。 具有相同重要性層級的要求具有目前存在的相同排程行為。

## <a name="importance-scenarios"></a>重要性案例

除了上述與銷售和氣象資料相關的基本重要性案例之外，還有工作負載重要性有助於符合資料處理和查詢需求的其他案例。

### <a name="locking"></a>鎖定

讀取和寫入活動的鎖定存取是自然爭用的一個區域。 [分割切換](sql-data-warehouse-tables-partition.md)或[重新命名物件](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)等活動需要較高的鎖定。  若沒有工作負載重要性，Azure Synapse 中的專用 SQL 集區可針對輸送量進行優化。 針對輸送量進行優化表示當執行中和佇列的要求具有相同的鎖定需求，而且有資源可用時，佇列的要求可以略過要求佇列中抵達要求佇列的要求。 一旦工作負載重要性套用至具有較高鎖定需求的要求。 具有較高重要性的要求會在具有較低重要性的要求之前執行。

請考慮下列範例：

- Q1 正在主動執行，並從 [Salesfact 中選取資料。
- Q2 已排入佇列等候 Q1 完成。  它是在上午9點提交，並嘗試將新的資料分割成 [Salesfact。
- Q3 于9：01am 提交，而且想要從 [Salesfact 選取資料。

如果 Q2 和 Q3 具有相同的重要性，而且 Q1 仍在執行，則 Q3 將會開始執行。 Q2 將繼續等待 [Salesfact 上的獨佔鎖定。  如果 Q2 的重要性高於 Q3，則第3季會等到第2季完成，然後才能開始執行。

### <a name="non-uniform-requests"></a>非統一要求

另一個重要性可協助滿足查詢需求的案例，就是提交具有不同資源類別的要求時。  如同先前所述，Azure Synapse 中的專用 SQL 集區可針對輸送量進行優化。 當混合大小要求 (（例如 smallrc 或 mediumrc) ）已排入佇列時，專用的 SQL 集區將會選擇符合可用資源的最早抵達要求。 如果套用工作負載重要性，下一步是排定最高重要性的要求。
  
在 DW500c 上，請考慮下列範例：

- Q1、Q2、Q3 和 Q4 正在執行 smallrc 查詢。
- Q5 會以 mediumrc 資源類別在上午9點提交。
- Q6 是以 smallrc 資源類別提交，于9：01am。

因為 Q5 是 mediumrc，所以它需要兩個平行存取插槽。 Q5 需要等候兩個正在執行的查詢完成。  不過，當其中一個正在執行的查詢 (Q1-Q4) 完成時，Q6 會立即排程，因為執行查詢的資源存在。  如果 Q5 的重要性高於 Q6，則 Q6 會等到 Q5 正在執行，才能開始執行。

## <a name="next-steps"></a>下一步

- 如需建立分類器的詳細資訊，請參閱 [建立工作負載分類器 (transact-sql) ](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。  
- 如需工作負載分類的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)。  
- 請參閱快速入門 [建立工作負載分類器](quickstart-create-a-workload-classifier-tsql.md) ，以瞭解如何建立工作負載分類器。
- 請參閱[設定工作負載的重要性](sql-data-warehouse-how-to-configure-workload-importance.md)的操作說明文章，以及如何[管理並監視工作負載管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)。
- 請參閱 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 以檢視查詢和所指派的重要性。
