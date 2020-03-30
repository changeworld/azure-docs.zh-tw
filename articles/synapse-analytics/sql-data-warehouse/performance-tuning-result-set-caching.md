---
title: 使用結果集快取進行效能微調
description: Azure 突觸分析中 SQL 分析的結果集緩存功能概述
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 0c2190c29054301a8e21a9a27eb078802fbc9612
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350868"
---
# <a name="performance-tuning-with-result-set-caching"></a>使用結果集快取進行效能微調  
啟用結果集緩存後，SQL Analytics 會自動緩存使用者資料庫中的查詢結果，以便重複使用。  這允許後續查詢執行直接從持久緩存獲取結果，因此不需要重新計算。   結果集緩存提高了查詢性能，減少了計算資源使用。  此外，使用緩存結果集的查詢不使用任何併發槽，因此不計入現有併發限制。 出於安全考慮，使用者只有在具有與創建緩存結果的使用者具有相同的資料存取權限時才能訪問緩存的結果。  

## <a name="key-commands"></a>鍵命令
[打開/關閉使用者資料庫的結果集緩存](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[打開/關閉會話的結果集緩存](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[檢查緩存結果集的大小](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[清理緩存](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>未緩存的內容  

一旦開啟資料庫的結果集快取功能，除了這些查詢以外，會快取查詢的其他全部結果，直到快取滿為止：
- 使用 DateTime.Now() 等不具決定性函式的查詢
- 使用使用者定義函式的查詢
- 使用具有行級安全性或列級安全性的表的查詢
- 傳回資料的資料列大小超過 64KB 的查詢

> [!IMPORTANT]
> 創建結果集緩存並從緩存中檢索資料的操作發生在 SQL Analytics 實例的控制節點上。
> 當結果集快取開啟時，執行傳回大型結果集的查詢 (例如，> 1 百萬個資料列) 可能會導致控制節點上的 CPU 使用率過高，並使執行個體的整體查詢回應變慢。  這些查詢通常會在資料探索或 ETL 作業期間使用。 若要避免對控制節點造成壓力並導致效能問題，使用者應該先關閉資料庫的結果集快取，再執行這些類型的查詢。  

運行此查詢的結果集快取作業所佔用的時間：

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

下面是禁用結果集緩存的查詢的示例輸出。

![與 rsc 禁用的查詢步驟](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

下面是啟用結果集緩存後執行的查詢的示例輸出。

![啟用 rsc 的查詢步驟](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>使用緩存結果時

如果符合下列所有需求，則會對查詢重複使用快取結果集：
- 執行查詢的使用者可以存取查詢中所參考所有資料表。
- 新查詢與產生結果集快取的上一個查詢完全相符。
- 產生快取結果集的來源資料表中沒有資料或結構描述變更。

執行此命令來檢查所執行查詢的結果快取是否有命中。 如果快取命中，result_cache_hit 將傳回 1。

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>管理緩存的結果 

結果集快取的大小上限是每個資料庫 1 TB。  當基礎查詢資料發生更改時，緩存的結果將自動失效。  

緩存逐出由 SQL 分析按照此計畫自動管理： 
- 如果結果集尚未使用或已失效，則每 48 小時一次。 
- 當結果集緩存接近最大大小時。

使用者可以通過使用以下選項之一手動清空整個結果集緩存： 
- 關閉資料庫的結果集快取功能 
- 連接到資料庫時運行 DBCC DROPRESULTCACHE

暫停資料庫不會為緩存的結果集清空。  

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。 
