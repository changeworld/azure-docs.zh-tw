---
title: 工作負載重要性
description: 在 Azure Synapse 分析中設置 Synapse SQL 池查詢的重要性的指南。
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
ms.openlocfilehash: 43ee14784b6049e9b5c1a78e733e72bbc45f915d
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744049"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure 突觸分析工作負荷重要性

本文介紹工作負載重要性如何影響 Azure Synapse 中 Synapse SQL 池請求的執行順序。

## <a name="importance"></a>重要性

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

業務需求可能要求數據倉庫工作負載比其他工作負載更重要。  考慮在會計期間結束前載入關鍵任務銷售數據的情況。  其他源(如天氣數據)的數據載入沒有嚴格的 SLL。 將載入銷售數據的請求設置為高重要性,對載入天氣數據的請求的重要性較低,可確保銷售數據負載能夠首先訪問資源並更快地完成。

## <a name="importance-levels"></a>重要性等級

有五個重要性級別:低、below_normal、正常、above_normal和高。  未設置重要性的請求將分配正常值的預設級別。 具有相同重要性級別的請求具有與當前相同的調度行為。

## <a name="importance-scenarios"></a>重要性方案

除了上述銷售和天氣數據的基本重要性方案外,還有其他一些方案,其中工作負載的重要性有助於滿足數據處理和查詢需求。

### <a name="locking"></a>鎖定

訪問讀取和寫入活動的鎖是自然爭用的一個領域。 [分區切換](sql-data-warehouse-tables-partition.md)或[重新命名物件](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)等活動需要提升的鎖。  Azure Synapse 中的 Synapse SQL 池不具有工作負載重要性,可優化輸送量。 優化輸送量意味著當運行和排隊的請求具有相同的鎖定需求和資源可用時,排隊的請求可以繞過具有更高鎖定需求的請求,這些請求會更早到達請求佇列中。 將工作負載重要性應用於具有更高鎖定需求的請求。 具有較高重要性的請求將在較低重要性的請求之前運行。

請考慮下列範例：

- Q1 正在主動運行並從 SalesFact 中選擇數據。
- Q2 排隊等待第 1 季度完成。  它於上午 9 點提交,並嘗試將新數據分區到 SalesFact。
- Q3 在上午 9:01 提交,希望從 SalesFact 中選擇數據。

如果 Q2 和 Q3 具有相同的重要性,並且 Q1 仍在執行,則第 3 季度將開始執行。 第 2 季度將繼續等待 SalesFact 上的獨佔鎖。  如果 Q2 的重要性高於第 3 季度,則第 3 季度將等到第 2 季度完成後才能開始執行。

### <a name="non-uniform-requests"></a>非統一要求

重要性可以幫助滿足查詢需求的另一種情況是提交具有不同資源類的請求。  如前所述,在同等重要性下,Azure 突觸中的 Synapse SQL 池可優化輸送量。 當混合大小請求(如小號或中型請求)排隊時,Synapse SQL 池將選擇適合可用資源的最早到達請求。 如果應用了工作負載重要性,則下一步將安排最高重要性請求。
  
請考慮 DW500c 上的以下範例:

- 第1季度、第2季度、第3季度和第4季度運行小型查詢。
- Q5 在上午 9 點隨 mediarc 資源類一起提交。
- Q6 在上午 9:01 時使用小 RC 資源類提交。

由於 Q5 是介質,因此需要兩個併發槽。 Q5 需要等待兩個正在運行的查詢完成。  但是,當其中一個正在運行的查詢 (Q1-Q4) 完成時,將立即計劃 Q6,因為存在資源來執行查詢。  如果 Q5 的重要性高於第 6 季度,則 Q6 將等到第 5 季度運行後才能開始執行。

## <a name="next-steps"></a>後續步驟

- 有關創建分類器的詳細資訊,請參閱[創建任務程式(轉算 SQL)。](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  
- 有關工作負載分類的詳細資訊,請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)。  
- 有關如何建立工作負載分類器,請參閱快速入門[建立工作負載分類器](quickstart-create-a-workload-classifier-tsql.md)。
- 請參閱[設定工作負載的重要性](sql-data-warehouse-how-to-configure-workload-importance.md)的操作說明文章，以及如何[管理並監視工作負載管理](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)。
- 請參閱 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 以檢視查詢和所指派的重要性。
