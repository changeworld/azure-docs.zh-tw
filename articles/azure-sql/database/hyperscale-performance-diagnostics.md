---
title: 超大規模資料庫中的效能診斷
description: 本文說明如何針對 Azure SQL Database 中的超大規模資料庫效能問題進行疑難排解。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019 sqldbrb=1
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 7bd2b404627e21a80fc41a4561300d7252d1519c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84324382"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL 超大規模資料庫效能疑難排解診斷
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

若要對超大規模資料庫資料庫中的效能問題進行疑難排解，Azure SQL Database 計算節點上的[一般效能微調方法](monitor-tune-overview.md)是效能調查的起點。 不過，假設超大規模資料庫的[分散式架構](service-tier-hyperscale.md#distributed-functions-architecture)，則已新增額外的診斷來提供協助。 本文說明超大規模資料庫特有的診斷資料。

## <a name="log-rate-throttling-waits"></a>記錄速率節流等候

每個 Azure SQL Database 服務層級都會透過[記錄速率治理](resource-limits-logical-server.md#transaction-log-rate-governance)來強制執行記錄產生率限制。 在超大規模資料庫中，不論服務層級為何，記錄檔產生的限制目前都設定為 100 MB/秒。 不過，有時主要計算複本上的記錄產生速率必須經過節流處理，以維持復原能力 Sla。 當[頁面伺服器或另一個計算複本](service-tier-hyperscale.md#distributed-functions-architecture)大幅落後套用記錄服務的新記錄檔記錄時，就會發生此節流。

下列等候類型（在[sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)）描述在主要計算複本上進行記錄速率節流的原因：

|等候類型    |Description                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | 當超大規模資料庫資料庫主要計算節點記錄產生速率因為頁面伺服器的延遲記錄耗用量而受到節流時發生。         |
|RBIO_RG_DESTAGE        | 當超大規模資料庫資料庫計算節點記錄產生速率因為長期記錄儲存體的延遲記錄耗用量而受到節流時發生。         |
|RBIO_RG_REPLICA        | 當超大規模資料庫資料庫計算節點記錄產生速率因為可讀取的次要複本延遲記錄耗用量而受到節流時發生。         |
|RBIO_RG_LOCALDESTAGE   | 當超大規模資料庫資料庫計算節點記錄產生速率因為記錄服務的延遲記錄耗用量而受到節流時發生。         |

## <a name="page-server-reads"></a>頁面伺服器讀取

計算複本不會在本機快取資料庫的完整複本。 計算複本的本機資料會儲存在緩衝集區（記憶體中）和本機復原緩衝集區延伸模組（RBPEX）快取中，這是資料頁的部分（非涵蓋）快取。 這個本機 RBPEX 快取的大小會按比例調整成計算大小，而且是計算層記憶體的三倍。 RBPEX 類似于緩衝集區，因為它具有最常存取的資料。 另一方面，每個頁面伺服器都有一個涵蓋的 RBPEX 快取，可供其維護的資料庫部分使用。

在計算複本上發出讀取時，如果資料不存在於緩衝集區或本機 RBPEX 快取中，就會發出 getPage （pageId，LSN）函式呼叫，而且會從對應的頁面伺服器提取該頁面。 從頁面伺服器讀取是遠端讀取，因此速度比從本機 RBPEX 讀取還慢。 針對 IO 相關的效能問題進行疑難排解時，我們必須能夠透過相對較慢的遠端頁面伺服器讀取來得知已完成的 Io 數目。

數個動態管理的視圖（Dmv）和擴充事件都有資料行和欄位，可指定頁面伺服器的遠端讀取數目，而這些資料可以與讀取總數進行比較。 查詢存放區也會在查詢執行時間統計資料中捕捉遠端讀取。

- 報表頁面伺服器讀取的資料行可在執行 Dmv 和目錄檢視中使用，例如：

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- 頁面伺服器讀取會加入至下列擴充事件：
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - 查詢-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads 會加入至實際計畫的查詢計劃 XML。 例如：

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> 若要在 [查詢計劃屬性] 視窗中查看這些屬性，則需要 SSMS 18.3 或更新版本。

## <a name="virtual-file-stats-and-io-accounting"></a>虛擬檔案統計資料和 IO 帳戶處理

在 Azure SQL Database 中， [dm_io_virtual_file_stats （）](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF 是監視 SQL Database io 的主要方式。 超大規模資料庫中的 IO 特性因其[分散式架構](service-tier-hyperscale.md#distributed-functions-architecture)而有所不同。 在本節中，我們將焦點放在資料檔案的 IO （讀取和寫入），如此 DMF 中所示。 在超大規模資料庫中，此 DMF 中可見的每個資料檔都會對應至遠端頁面伺服器。 此處所提及的 RBPEX 快取是以本機 SSD 為基礎的快取，這是計算複本上的非涵蓋快取。

### <a name="local-rbpex-cache-usage"></a>本機 RBPEX 快取使用量

本機 RBPEX 快取存在於計算複本上的本機 SSD 儲存體上。 因此，針對此快取的 IO 比遠端頁面伺服器的 IO 更快速。 目前，超大規模資料庫資料庫中的[dm_io_virtual_file_stats sys.databases](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/)會針對計算複本上的本機 RBPEX 快取報告 io 的特殊資料列。 此資料列的值為0，且適用于和資料行 `database_id` `file_id` 。 例如，下列查詢會傳回自資料庫啟動後的 RBPEX 使用量統計資料。

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

在 RBPEX 上完成的讀取比例與所有其他資料檔上完成的匯總讀取會提供 RBPEX 快取點擊率。

### <a name="data-reads"></a>資料讀取

- 當讀取是由計算複本上的 SQL Server 資料庫引擎發出時，它們可能是由本機 RBPEX 快取或遠端頁面伺服器提供，或是由兩個讀取多個頁面的組合所組成。
- 當計算複本從特定檔案讀取某些頁面時（例如 file_id 1），如果此資料僅位於本機 RBPEX 快取中，則會針對此讀取的所有 IO 考慮 file_id 0 （RBPEX）。 如果該資料的某些部分是在本機 RBPEX 快取中，而某些部分位於遠端頁面伺服器上，則 IO 會針對從 RBPEX 提供的部分進行 file_id 0，而從遠端頁面伺服器提供的元件則會計入 file_id 1。
- 當計算複本從頁面伺服器要求特定[lsn](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/)的頁面時，如果頁面伺服器未趕上到所要求的 lsn，計算複本上的讀取將會等到頁面伺服器一直到達之後，才會將頁面傳回到計算複本。 如果是從計算複本上的頁面伺服器讀取，您會看到 PAGEIOLATCH_ * 等候類型（如果它正在等待該 IO）。 在超大規模資料庫中，此等候時間包含將頁面伺服器上要求的頁面攔截到所需 LSN 的時間，以及將頁面從頁面伺服器傳輸到計算複本所需的時間。
- 大量讀取（例如預先讀取）通常是使用「[散佈-收集」讀取](/sql/relational-databases/reading-pages/)來完成。 這允許一次讀取最多 4 MB 的頁面，視為 SQL Server 資料庫引擎中的單一讀取。 不過，當讀取的資料在 RBPEX 時，這些讀取會視為多個個別的 8 KB 讀取，因為緩衝集區和 RBPEX 一律會使用 8 KB 的頁面。 因此，針對 RBPEX 看到的讀取 Io 數目可能會大於引擎所執行的實際 Io 數目。

### <a name="data-writes"></a>資料寫入

- 主要計算複本不會直接寫入頁面伺服器。 相反地，記錄服務的記錄檔記錄會在對應的頁面伺服器上重新執行。
- 在計算複本上發生的寫入主要是寫入本機 RBPEX （file_id 0）。 對於大於 8 KB 的邏輯檔案寫入（換句話說，使用[收集-write](/sql/relational-databases/writing-pages/)完成），每個寫入作業都會轉譯為多個 8 kb 的個別寫入至 RBPEX，因為緩衝集區和 RBPEX 一律使用 8 kb 的頁面。 因此，針對 RBPEX 所見的寫入 Io 數目可能會大於引擎所執行的實際 Io 數目。
- 非 RBPEX 檔案，或對應至頁面伺服器 file_id 0 以外的資料檔案，也會顯示寫入。 在超大規模資料庫服務層級中，這些寫入會進行模擬，因為計算複本永遠不會直接寫入頁面伺服器。 寫入 IOPS 和輸送量是在計算複本上發生的，但 file_id 0 以外的資料檔案延遲並不會反映頁面伺服器寫入的實際延遲。

### <a name="log-writes"></a>記錄檔寫入

- 在主要計算上，會在 dm_io_virtual_file_stats 的 file_id 2 中考慮記錄寫入。 主要計算上的記錄寫入是記錄登陸區域的寫入。
- 認可上的次要複本上不會強化記錄檔記錄。 在超大規模資料庫中，記錄服務會以非同步方式將記錄套用至次要複本。 由於記錄檔寫入實際上不會在次要複本上進行，因此次要複本上的任何記錄檔 Io 會計僅供追蹤之用。

## <a name="data-io-in-resource-utilization-statistics"></a>資源使用量統計資料中的資料 IO

在非超大規模資料庫資料庫中，系統會在資料行中，將讀取和寫入 IOPS （相對於[資源治理](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance)資料 IOPS 限制）報告在 sys.databases 中。 [dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)和[sys.databases resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) views。 `avg_data_io_percent` 在 Azure 入口網站中，會將相同的值報告為_資料 IO 百分比_。

在超大規模資料庫資料庫中，此資料行會報告相對於計算複本上本機儲存體限制的資料 IOPS 使用率，特別是針對 RBPEX 和的 IO `tempdb` 。 此資料行中的100% 值表示資源管理限制了本機儲存體 IOPS。 如果這與效能問題相互關聯，請調整工作負載以產生較少的 IO，或增加資料庫服務目標，以增加資源治理的_最大資料 IOPS_ [限制](resource-limits-vcore-single-databases.md)。 針對 RBPEX 讀取和寫入的資源管理，系統會計算個別的 8 KB Io，而不是由 SQL Server 資料庫引擎發行的較大 IOs。

針對遠端頁面伺服器的資料 IO 不會在資源使用率視圖或入口網站中報告，但如先前所述，在[dm_io_virtual_file_stats （）](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF 中回報。

## <a name="additional-resources"></a>其他資源

- 如需超大規模資料庫單一資料庫的 vCore 資源限制，請參閱[超大規模資料庫服務層 VCore 限制](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)
- 如需 Azure SQL Database 的效能微調，請參閱[Azure SQL Database 中的查詢效能](performance-guidance.md)
- 如需使用查詢存放區進行效能調整，請參閱[使用查詢存放區的效能監視](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- 如需 DMV 監視腳本，請參閱[使用動態管理檢視監視效能 Azure SQL Database](monitoring-with-dmvs.md)
