---
title: 使用結果集快取進行效能微調
description: Azure Synapse 分析中 SQL 分析的結果集快取功能總覽
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 3d204605e68cf8cf33f69d73fb20f3cc08674e44
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200527"
---
# <a name="performance-tuning-with-result-set-caching"></a>使用結果集快取進行效能微調  
啟用結果集快取時，SQL 分析會在使用者資料庫中自動快取查詢結果，以供重複使用。  這可讓後續的查詢執行直接從持續性快取取得結果，因此不需要重新計算。   結果集快取可改善查詢效能並減少計算資源使用量。  此外，使用快取結果集的查詢不會使用任何平行存取插槽，因此不會計算現有的並行限制。 基於安全性，如果使用者與建立快取結果的使用者具有相同的資料存取權限，則只能存取快取的結果。  

## <a name="key-commands"></a>主要命令
[開啟/關閉使用者資料庫的結果集快取](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[開啟/關閉會話的結果集快取](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[檢查快取結果集的大小](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[清除快取](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>未快取的內容  

一旦開啟資料庫的結果集快取功能，除了這些查詢以外，會快取查詢的其他全部結果，直到快取滿為止：
- 使用 DateTime.Now() 等不具決定性函式的查詢
- 使用使用者定義函式的查詢
- 使用已啟用資料列層級安全性或資料行層級安全性之資料表的查詢
- 傳回資料的資料列大小超過 64KB 的查詢

> [!IMPORTANT]
> 建立結果集快取，以及從快取中取出資料的作業，會發生在 SQL 分析實例的控制節點上。
> 當結果集快取開啟時，執行傳回大型結果集的查詢 (例如，> 1 百萬個資料列) 可能會導致控制節點上的 CPU 使用率過高，並使執行個體的整體查詢回應變慢。  這些查詢通常會在資料探索或 ETL 作業期間使用。 若要避免對控制節點造成壓力並導致效能問題，使用者應該先關閉資料庫的結果集快取，再執行這些類型的查詢。  

針對查詢的結果集快取作業所花費的時間執行此查詢：

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

以下是使用已停用結果集快取所執行之查詢的範例輸出。

![查詢-使用-rsc-已停用](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

以下是在啟用結果集快取的情況下執行之查詢的範例輸出。

![查詢-具有-rsc-已啟用的步驟](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>使用快取的結果時

如果符合下列所有需求，則會對查詢重複使用快取結果集：
- 執行查詢的使用者可以存取查詢中所參考所有資料表。
- 新查詢與產生結果集快取的上一個查詢完全相符。
- 產生快取結果集的來源資料表中沒有資料或結構描述變更。

執行此命令來檢查所執行查詢的結果快取是否有命中。 如果快取命中，result_cache_hit 將傳回 1。

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>管理快取的結果 

結果集快取的大小上限是每個資料庫 1 TB。  當基礎查詢資料變更時，快取的結果會自動失效。  

快取收回是由 SQL 分析根據此排程自動管理： 
- 如果結果集尚未使用或已失效，則每隔48小時。 
- 當結果集快取接近大小上限時。

使用者可以使用下列其中一個選項，手動清空整個結果集快取： 
- 關閉資料庫的結果集快取功能 
- 連接到資料庫時執行 DBCC DROPRESULTSETCACHE

暫停資料庫不會清空快取的結果集。  

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。 
