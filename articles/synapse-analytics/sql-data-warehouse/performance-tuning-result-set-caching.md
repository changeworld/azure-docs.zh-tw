---
title: 使用結果集快取進行效能微調
description: Azure Synapse Analytics 中專用 SQL 集區的結果集快取功能總覽
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: d8c6c8d22c059c63fb4f84c84a02a70de30d4ebe
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678520"
---
# <a name="performance-tuning-with-result-set-caching"></a>使用結果集快取進行效能微調

啟用結果集快取時，專用的 SQL 集區會自動將查詢結果快取到使用者資料庫中，以供重複使用。  這可讓後續的查詢執行直接從永續性快取取得結果，因此不需要重新計算。   結果集快取可改善查詢效能並減少計算資源使用量。  此外，使用快取結果集的查詢不會使用任何並行位置，因此不會計入現有的並行限制。 基於安全性，如果使用者與建立快取結果的使用者具有相同的資料存取權限，則只能存取快取的結果。  

## <a name="key-commands"></a>主要命令

[開啟/關閉使用者資料庫的結果集快取](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

[開啟/關閉工作階段的結果集快取](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

[檢查快取結果集的大小](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  

[清除快取](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="whats-not-cached"></a>不會快取的項目  

一旦開啟資料庫的結果集快取功能，除了這些查詢以外，會快取查詢的其他全部結果，直到快取滿為止：

- 使用不具決定性的內建函數或運行時程表達式進行查詢，即使基表的資料或查詢沒有變更也是一樣。 例如，DateTime。現在 ( # A1，GetDate ( # A3。
- 使用使用者定義函式的查詢
- 使用已啟用資料列層級安全性或資料行層級安全性資料表的查詢
- 傳回資料的資料列大小超過 64KB 的查詢
- 傳回大型資料的查詢 (>10GB) 
>[!NOTE]
> - 某些不具決定性的函式和運行時程表達式可以具決定性，以針對相同的資料進行重複的查詢。 例如，ROW_NUMBER ( # A1。  
> - 如果查詢結果集中的資料列順序/順序對您的應用程式邏輯很重要，請在查詢中使用 ORDER BY。
> - 如果 ORDER BY 資料行中的資料不是唯一的，則在 ORDER BY 資料行中具有相同值的資料列不會有 garanteed 資料列順序，不論結果集快取是啟用或停用。

> [!IMPORTANT]
> 建立結果集快取並從快取取出資料的作業會在專用 SQL 集區實例的控制節點上進行。
> 當結果集快取開啟時，執行傳回大型結果集的查詢 (例如，>1GB) 可能會導致控制節點上的節流過高，並使執行個體的整體查詢回應變慢。  這些查詢通常會在資料探索或 ETL 作業期間使用。 若要避免對控制節點造成壓力並導致效能問題，使用者應該先關閉資料庫的結果集快取，再執行這些類型的查詢。  

針對查詢的結果集快取作業所花費時間執行此查詢：

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

以下是在已停用結果集快取情況下所執行查詢的範例輸出。

![螢幕擷取畫面會顯示查詢結果，包括位置類型和命令。](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

以下是在已啟用結果集快取情況下所執行查詢的範例輸出。

![螢幕擷取畫面顯示已選取命令的查詢結果 * from [D W ResultCache D b] 點 D b o 稱為 out。](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>使用快取的結果時

如果符合下列所有需求，則會對查詢重複使用快取結果集：

- 執行查詢的使用者可以存取查詢中所參考所有資料表。
- 新查詢與產生結果集快取的上一個查詢完全相符。
- 產生快取結果集的來源資料表中沒有資料或結構描述變更。

執行此命令來檢查所執行查詢的結果快取是否有命中。 result_cache_hit 資料行會針對快取命中傳回 1、針對快取遺漏傳回 0，並針對未使用結果集快取的原因傳回負值。 檢查 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 以取得詳細資料。

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>管理快取的結果

結果集快取的大小上限是每個資料庫 1 TB。  當基礎查詢資料變更時，快取的結果會自動失效。  

快取收回是由依此排程自動執行的專用 SQL 集區所管理：

- 若結果集尚未使用或已失效，則為每 48 小時收回一次。
- 當結果集快取接近大小上限時。

使用者可以使用下列其中一個選項，手動清空整個結果集快取：

- 關閉資料庫的結果集快取功能
- 在連線到資料庫時執行 DBCC DROPRESULTSETCACHE

暫停資料庫不會清空快取的結果集。  

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
