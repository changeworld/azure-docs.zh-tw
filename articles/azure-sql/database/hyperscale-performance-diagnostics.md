---
title: 超大規模中的效能診斷
description: 本文說明如何針對 Azure SQL Database 中的超大規模效能問題進行疑難排解。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84324382"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL 超大規模效能疑難排解診斷
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

若要針對超大規模資料庫中的效能問題進行疑難排解，Azure SQL Database 計算節點上的 [一般效能微調方法](monitor-tune-overview.md) 是效能調查的起點。 不過，在超大規模的 [分散式架構](service-tier-hyperscale.md#distributed-functions-architecture) 中，已加入額外的診斷來協助。 本文描述超大規模特定的診斷資料。

## <a name="log-rate-throttling-waits"></a>記錄速率節流等候

每個 Azure SQL Database 服務層級都有透過 [記錄速率治理](resource-limits-logical-server.md#transaction-log-rate-governance)強制執行的記錄產生速率限制。 在超大規模中，記錄產生限制目前設定為 100 MB/秒，而不管服務層級為何。 不過，在某些情況下，主要計算複本上的記錄產生速率必須進行節流處理，以維持復原能力 Sla。 當 [頁面伺服器或其他計算複本](service-tier-hyperscale.md#distributed-functions-architecture) 大幅落後套用記錄服務的新記錄檔記錄時，就會發生此節流。

下列等候類型 (于 [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) 說明在主要計算複本上進行記錄速率節流的原因：

|等候類型    |說明                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | 當超大規模資料庫主要計算節點記錄產生速率因為頁面伺服器 () 的延遲記錄耗用量而受到節流時發生。         |
|RBIO_RG_DESTAGE        | 當超大規模資料庫計算節點記錄產生速率因為長期記錄儲存體的延遲記錄耗用量而受到節流時，就會發生這種情況。         |
|RBIO_RG_REPLICA        | 在超大規模資料庫計算節點記錄產生速率因為可讀取次要複本 (s 的延遲記錄檔耗用量而受到節流處理時發生。) 。         |
|RBIO_RG_LOCALDESTAGE   | 在超大規模資料庫計算節點記錄產生速率因為記錄服務的延遲記錄耗用量而受到節流時發生。         |

## <a name="page-server-reads"></a>頁面伺服器讀取

計算複本不會在本機快取資料庫的完整複本。 計算複本的本機資料會儲存在緩衝集區 (記憶體) 和本機復原緩衝集區延伸 (RBPEX) 快取，這是部分 (非涵蓋的資料頁面快取部分) 。 此本機 RBPEX 快取會依比例調整為計算大小，而且是計算層的記憶體三倍。 RBPEX 類似于緩衝區集區，因為它具有最常存取的資料。 另一方面，每個頁面伺服器都會有一個涵蓋 RBPEX 快取，以供其維護的資料庫部分使用。

在計算複本上發出讀取時，如果資料不存在於緩衝集區或本機 RBPEX 快取中，則會發出 getPage (pageId、LSN) 函式呼叫，然後從對應的頁面伺服器提取該頁面。 從頁面伺服器讀取是遠端讀取，因此速度會比從本機 RBPEX 讀取更慢。 針對 IO 相關效能問題進行疑難排解時，我們必須能夠透過相對較慢的遠端頁面伺服器讀取來分辨已完成的 Io 數目。

 (Dmv) 和擴充事件的數個動態受控視圖都有資料行和欄位，可指定頁面伺服器的遠端讀取數目，可與讀取總數進行比較。 查詢存放區也會在查詢執行時間統計資料中捕捉遠端讀取。

- 報表頁面伺服器讀取的資料行可在執行 Dmv 和目錄檢視中使用，例如：

  - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
  - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
  - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
  - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
  - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- 頁面伺服器讀取會新增至下列擴充事件：
  - sql_statement_completed
  - sp_statement_completed
  - sql_batch_completed
  - rpc_completed
  - scan_stopped
  - query_store_begin_persist_runtime_stat
  - 查詢-store_execution_runtime_info
- >actualpageserverreads/>actualpageserverreadaheads 會新增至實際計畫的查詢計劃 XML。 例如：

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> 若要在 [查詢計劃屬性] 視窗中查看這些屬性，則需要 SSMS 18.3 或更新版本。

## <a name="virtual-file-stats-and-io-accounting"></a>虛擬檔案統計資料和 IO 帳戶處理

在 Azure SQL Database 中， [sys.dm_io_virtual_file_stats ( # B1 ](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF 是監視 SQL Database io 的主要方式。 超大規模中的 IO 特性因為其 [分散式架構](service-tier-hyperscale.md#distributed-functions-architecture)而有所不同。 在本節中，我們將焦點放在 IO (讀取和寫入) 到資料檔案中，如這項 DMF 所示。 在超大規模中，這個 DMF 中可見的每個資料檔都對應到遠端頁面伺服器。 此處所提及的 RBPEX 快取是以本機 SSD 為基礎的快取，也就是計算複本上的非涵蓋快取。

### <a name="local-rbpex-cache-usage"></a>本機 RBPEX 快取使用量

本機 SSD 儲存體上的計算複本上有本機 RBPEX 快取。 因此，針對此快取所進行的 IO 比針對遠端頁面伺服器的 IO 更快。 目前，超大規模資料庫中 [ ( # B1 sys.dm_io_virtual_file_stats ](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) 有一個特殊資料列，針對計算複本上的本機 RBPEX 快取報告 io。 此資料列的值為0，且適用于和資料行 `database_id` `file_id` 。 例如，下列查詢會在資料庫啟動後傳回 RBPEX 使用量統計資料。

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

在 RBPEX 上完成的讀取比率與所有其他資料檔案上完成的匯總讀取，提供 RBPEX 快取點擊率。

### <a name="data-reads"></a>資料讀取

- 當計算複本上的 SQL Server 資料庫引擎發出讀取時，它們可能會由本機 RBPEX 快取或遠端頁面伺服器提供服務，或是在讀取多個頁面時，透過兩個組合來提供。
- 當計算複本從特定檔案讀取某些頁面時（例如 file_id 1），如果這項資料僅在本機 RBPEX 快取上，則此讀取的所有 IO 都會針對 file_id 0 (RBPEX) 進行考慮。 如果該資料的某些部分是在本機 RBPEX 快取中，而某些部分是在遠端頁面伺服器上，則 IO 會針對 RBPEX 所提供的部分 file_id 0，而從遠端頁面伺服器提供的元件則是 file_id 1。
- 當計算複本從頁面伺服器要求特定 [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) 的頁面時，如果頁面伺服器尚未到達所要求的 lsn，則在計算複本上的讀取將會等候，直到頁面伺服器在頁面回到計算複本之前都有趕上為止。 如果是從計算複本上的頁面伺服器讀取的任何資訊，您將會看到 PAGEIOLATCH_ * 等候類型（如果正在等候該 IO）。 在超大規模中，此等候時間包括將頁面伺服器上要求的頁面趕上所需 LSN 的時間，以及將頁面從頁面伺服器傳送到計算複本所需的時間。
- 大量讀取（例如預先讀取）通常是使用「 [散佈收集」讀取](/sql/relational-databases/reading-pages/)來完成。 這可讓您一次讀取最多 4 MB 的頁面，視為 SQL Server database engine 中的單一讀取。 不過，當讀取的資料是在 RBPEX 時，這些讀取會視為多個個別的 8 KB 讀取，因為緩衝集區和 RBPEX 一律會使用 8 KB 的頁面。 如此一來，針對 RBPEX 所看到的讀取 Io 數目可能會大於引擎所執行的實際 Io 數目。

### <a name="data-writes"></a>資料寫入

- 主要計算複本不會直接寫入頁面伺服器。 相反地，會在對應的頁面伺服器上重新執行記錄服務的記錄檔記錄。
- 在計算複本上發生的寫入主要是寫入本機 RBPEX (file_id 0) 。 針對大於 8 KB 的邏輯檔案的寫入（換句話說，使用「 [收集-寫入](/sql/relational-databases/writing-pages/)」完成），每個寫入作業都會轉譯成多個 8 kb 個別寫入至 RBPEX，因為緩衝集區和 RBPEX 一律使用 8 kb 的頁面。 如此一來，針對 RBPEX 所看到的寫入 Io 數目可能會大於引擎所執行的實際 Io 數目。
- 非 RBPEX 檔案或與頁面伺服器對應的 file_id 0 以外的資料檔案也會顯示寫入。 在超大規模服務層中，系統會模擬這些寫入，因為計算複本永遠不會直接寫入頁面伺服器。 寫入 IOPS 和輸送量會在計算複本上進行，但 file_id 0 以外的資料檔案延遲不會反映頁面伺服器寫入的實際延遲。

### <a name="log-writes"></a>記錄寫入

- 在主要計算上，sys.dm_io_virtual_file_stats file_id 2 中記錄寫入。 主要計算的記錄寫入是記錄登陸區域的寫入。
- 認可上的次要複本上不會強化記錄檔記錄。 在超大規模中，記錄服務會以非同步方式將記錄檔套用至次要複本。 因為記錄寫入實際上不是在次要複本上進行，所以次要複本上的任何記錄 Io 會計僅供追蹤之用。

## <a name="data-io-in-resource-utilization-statistics"></a>資源使用量統計資料中的資料 IO

在非超大規模資料庫中，資料檔案的相關讀取和寫入 IOPS （相對於 [資源治理](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) 資料 IOPS 限制）會在資料行中 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 視圖中報告 `avg_data_io_percent` 。 Azure 入口網站中的相同值回報為 _資料 IO 百分比_。

在超大規模資料庫中，此資料行會報告相對於計算複本上的本機儲存體限制的資料 IOPS 使用量，特別是針對 RBPEX 和的 IO `tempdb` 。 此資料行中的100% 值表示資源管理會限制本機儲存體 IOPS。 如果這與效能問題相互關聯，請調整工作負載以產生較少的 IO，或增加資料庫服務目標來增加資源管理的 _最大資料 IOPS_ [限制](resource-limits-vcore-single-databases.md)。 針對 RBPEX 讀取和寫入的資源管理，系統會計算個別的 8 KB IOs，而不是由 SQL Server 資料庫引擎所發出的較大 IOs。

針對遠端頁面伺服器的資料 IO 不會在資源使用量視圖或入口網站中報告，但 [sys.dm_io_virtual_file_stats ( # B1 ](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF 中回報，如先前所述。

## <a name="additional-resources"></a>其他資源

- 如需超大規模單一資料庫的 vCore 資源限制，請參閱 [超大規模服務層 VCore 限制](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)
- 如 Azure SQL Database 效能調整，請參閱 [Azure SQL Database 中的查詢效能](performance-guidance.md)
- 若要使用查詢存放區進行效能微調，請參閱 [使用查詢存放區的效能監視](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- 針對 DMV 監視腳本，請參閱 [使用動態管理檢視監視效能 Azure SQL Database](monitoring-with-dmvs.md)
