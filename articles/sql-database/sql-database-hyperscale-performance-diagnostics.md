---
title: 超大規模中的性能診斷
description: 本文介紹如何解決 Azure SQL 資料庫中的超大規模性能問題。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75615072"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL 超大規模性能故障排除診斷

要解決超大規模資料庫中的性能問題，Azure SQL 資料庫計算節點上的[一般性能調優方法是](sql-database-monitor-tune-overview.md)性能調查的起點。 但是，鑒於超大規模的[分散式體系結構](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)，添加了其他診斷來提供説明。 本文介紹了超大規模特定的診斷資料。

## <a name="log-rate-throttling-waits"></a>日誌速率限制等待

每個 Azure SQL 資料庫服務等級都有通過[日誌速率治理強制執行的](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)日誌生成速率限制。 在 Hyperscale 中，日誌生成限制當前設置為 100 MB/秒，而不考慮服務等級。 但是，有時必須限制主計算副本上的日誌生成速率，以保持可恢復性 SL。 當[頁面伺服器或其他計算副本](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)顯著落後于從 Log 服務應用新日誌記錄時，就會發生此限制。

以下等待類型（在[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)）描述了在主計算副本上限制日誌速率的原因：

|等候類型    |描述                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | 當超大規模資料庫主計算節點日誌生成速率由於頁面伺服器延遲日誌消耗而受到限制時，就會發生這種情況。         |
|RBIO_RG_DESTAGE        | 當超大規模資料庫計算節點日誌生成速率由於長期日誌存儲延遲使用而受到限制時，就會發生這種情況。         |
|RBIO_RG_REPLICA        | 當超大規模資料庫計算節點日誌生成速率由於可讀輔助副本延遲使用日誌而受到限制時，就會發生這種情況。         |
|RBIO_RG_LOCALDESTAGE   | 當超大規模資料庫計算節點日誌生成速率由於日誌服務延遲使用而受到限制時，就會發生這種情況。         |

## <a name="page-server-reads"></a>頁面伺服器讀取

計算副本不會在本機快取資料庫的完整副本。 計算副本的本地資料存儲在緩衝區池（記憶體中）和本地彈性緩衝區池擴展 （RBPEX） 緩存中，該緩存是資料頁的部分（非覆蓋）緩存。 此本地 RBPEX 緩存的大小與計算大小成比例，是計算層記憶體的三倍。 RBPEX 與緩衝集區類似，因為它具有最常訪問的資料。 另一方面，每個頁面伺服器都有一個覆蓋的 RBPEX 緩存，用於維護資料庫部分。
 
在計算副本上發出讀取時，如果緩衝區池或本地 RBPEX 緩存中不存在資料，則會發出 getPage（頁面 Id、LSN）函式呼叫，並從相應的頁面伺服器提取該頁。 從頁面伺服器讀取是遠端讀取，因此比從本地 RBPEX 讀取慢。 在排除與 IO 相關的性能問題時，我們需要能夠判斷通過相對較慢的遠端頁面伺服器讀取完成了多少 IO。

多個 DMV 和擴展事件具有列和欄位，用於指定從頁面伺服器讀取的遠端讀取數，這些讀取數可與總讀取進行比較。 查詢存儲還捕獲遠端讀取作為查詢運行時統計資訊的一部分。

- 報表頁伺服器讀取的列在執行 DMV 和目錄檢視中可用，例如：
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- 頁面伺服器讀取將添加到以下擴展事件：
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - 查詢-store_execution_runtime_info
- 實際頁面伺服器讀取/實際頁面伺服器閱讀前行將添加到實際計畫的查詢計劃 XML 中。 例如：

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> 要查看查詢計劃屬性視窗中的這些屬性，需要 SSMS 18.3 或更高版本。

## <a name="virtual-file-stats-and-io-accounting"></a>虛擬檔案統計資訊和 IO 核算

在 Azure SQL 資料庫中[，sys.dm_io_virtual_file_stats（）](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF 是監視 SQL Server IO 的主要方式。 超大規模中的IO特性由於其[分散式架構](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)而不同。 在本節中，我們重點介紹 IO（讀取和寫入）到資料檔案，如本 DMF 所示。 在超大規模中，此 DMF 中可見的每個資料檔案對應于遠端頁面伺服器。 此處提到的 RBPEX 緩存是基於本地 SSD 的緩存，即計算副本上的非覆蓋緩存。

### <a name="local-rbpex-cache-usage"></a>本地 RBPEX 緩存使用方式

本地 RBPEX 緩存存在於本地 SSD 存儲的計算副本上。 因此，針對此緩存的 IO 比針對遠端頁面伺服器的 IO 快。 目前，超大規模資料庫中的[sys.dm_io_virtual_file_stats（）](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/)具有一個特殊的行，用於報告 IO 與計算副本上的本地 RBPEX 緩存。 此行的值為 和`database_id``file_id`列。 例如，下面的查詢返回自資料庫啟動以來的 RBPEX 使用方式統計資訊。

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

在 RBPEX 上完成的讀取與所有其他資料檔案上完成的聚合讀取的比率提供 RBPEX 緩存命中率。

### <a name="data-reads"></a>資料讀取

- 當 SQL Server 引擎在計算副本上發出讀取時，它們可能由本地 RBPEX 緩存或遠端頁面伺服器提供，或者通過讀取多個頁面時由兩者的組合提供。
- 當計算副本從特定檔讀取某些頁面（例如file_id 1 時，如果此資料僅駐留在本地 RBPEX 緩存上，則此讀取的所有 IO 都會根據 file_id 0 （RBPEX） 進行核算。 如果資料的某些部分位於本地 RBPEX 緩存中，並且某些部分位於遠端頁面伺服器上，則 IO 會計入從 RBPEX 提供的部件的 0 file_id，並且從遠端頁面伺服器提供的部件計入file_id 1。 
- 當計算副本從頁面伺服器請求特定[LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/)上的頁面時，如果頁面伺服器未趕上請求的 LSN，則計算副本上的讀取將等待頁面伺服器趕上，然後再將頁面返回到計算副本。 對於從計算副本上的頁面伺服器讀取的任何內容，如果等待該 IO，您將看到PAGEIOLATCH_* 等待類型。 在 Hyperscale 中，此等待時間既包括將頁面伺服器上的請求頁面趕上所需的 LSN 所需的時間，也包括將頁面從頁面伺服器傳輸到計算副本所需的時間。
- 大型讀取（如預讀）通常使用["散點-收集"讀取](/sql/relational-databases/reading-pages/)來完成。 這允許一次最多讀取 4 MB 的頁面，這被認為是 SQL Server 引擎中的單個讀取。 但是，當正在讀取的資料在 RBPEX 中時，這些讀取被記為多個單獨的 8 KB 讀取，因為緩衝集區和 RBPEX 始終使用 8 KB 頁。 因此，針對 RBPEX 看到的讀取 I 數可能大於發動機執行的實際 I 數。

### <a name="data-writes"></a>資料寫入

- 主計算副本不會直接寫入頁面伺服器。 相反，日誌服務的日誌記錄在相應的頁面伺服器上重播。 
- 在計算副本上發生的寫入主要寫入本地 RBPEX （file_id 0）。 對於大於 8 KB 的邏輯檔（換言之，使用[收集寫入](/sql/relational-databases/writing-pages/)）寫入，每個寫入操作將轉換為多個 8 KB 的單獨寫入 RBPEX，因為緩衝集區和 RBPEX 始終使用 8 KB 頁。 因此，針對 RBPEX 看到的寫入 I 數可能大於引擎執行的實際 I 數。
- 非 RBPEX 檔或與頁面伺服器相對應file_id 0 以外的資料檔案也會顯示寫入。 在 Hyperscale 服務層中，這些寫入是類比的，因為計算副本從不直接寫入頁面伺服器。 寫入 IOPS 和輸送量在計算副本上發生時會進行核算，但file_id 0 以外的資料檔案的延遲並不反映頁面伺服器寫入的實際延遲。

### <a name="log-writes"></a>日誌寫入

- 在主計算中，日誌寫入在 sys.dm_io_virtual_file_stats 的file_id 2 中占。 主計算上的日誌寫入是寫入日誌著陸區。
- 提交上的輔助副本上未強化日誌記錄。 在超大規模中，日誌服務非同步地應用於輔助副本。 由於日誌寫入實際上不會發生在輔助副本上，因此對輔助副本上的 Log I 的任何記帳僅用於跟蹤目的。

## <a name="data-io-in-resource-utilization-statistics"></a>資源利用率統計中的資料 IO

在非超大規模資料庫中，相對於[資源治理](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance)資料 IOPS 限制的資料檔案組合讀取和寫入 IOPS`avg_data_io_percent`在[列中dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)和[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)視圖中報告。 在門戶中報告的值與_資料 IO 百分比 相同_。 

在超大規模資料庫中，此列報告資料 IOPS 相對於僅計算副本上的本機存放區限制的資料 IOPS 利用率，特別是針對 RBPEX 和`tempdb`的 IO。 此列中的 100% 值表示資源治理正在限制本機存放區 IOPS。 如果這與性能問題相關，請調整工作負載以生成較少的 IO，或增加資料庫服務目標以增加資源治理_Max Data IOPS_ [限制](sql-database-vcore-resource-limits-single-databases.md)。 對於 RBPEX 讀取和寫入的資源治理，系統計算單個 8 KB I，而不是 SQL Server 引擎可能頒發的較大 I。 

與遠端頁面伺服器的資料 IO 不會在資源利用率視圖或閘戶中報告，而是在[sys.dm_io_virtual_file_stats（） DMF](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/)中報告，如前面所述。


## <a name="additional-resources"></a>其他資源

- 有關超大規模單個資料庫的 vCore 資源限制，請參閱[超大規模服務層 vCore 限制](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- 有關 Azure SQL 資料庫性能調優，請參閱[Azure SQL 資料庫中的查詢性能](sql-database-performance-guidance.md)
- 有關使用查詢存儲進行性能調優，請參閱[使用查詢存儲的效能監控](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- 有關 DMV 監視腳本，請參閱[使用動態管理檢視監視性能 Azure SQL 資料庫](sql-database-monitoring-with-dmvs.md)
