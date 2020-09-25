---
title: Azure 中邏輯伺服器的資源限制
description: 本文概要說明 Azure 中的邏輯伺服器在 Azure SQL Database 和 Azure Synapse Analytics 所使用的資源限制。 它也提供抵達或超過那些資源限制時所會發生之情況的相關資訊。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 09/15/2020
ms.openlocfilehash: 32c5135629bf56645ea39a8eee991de3cae6dca0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325329"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Azure SQL Database 與 Azure Synapse Analytics 伺服器的資源限制
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文概述 Azure SQL Database 和 Azure Synapse Analytics 所使用之邏輯伺服器的資源限制。 它會提供達到或超過這些資源限制時所發生情況的相關資訊，並說明用來強制執行這些限制的資源治理機制。

> [!NOTE]
> 如需 Azure SQL 受控執行個體限制，請參閱 [SQL Database 受控實例的資源限制](../managed-instance/resource-limits.md)。

## <a name="maximum-resource-limits"></a>資源限制上限

| 資源 | 限制 |
| :--- | :--- |
| 每一伺服器的資料庫 | 5000 |
| 任何區域中每個訂用帳戶的伺服器預設數目 | 20 |
| 任何區域中每個訂用帳戶的伺服器最大數目 | 200 |  
| 每一伺服器的 DTU/eDTU 配額 | 54,000 |  
| 每一伺服器/執行個體的 vCore 配額 | 540 |
| 每一伺服器的集區數目上限 | 受限於 DTU 或 vCore 數目。 例如，如果每個集區是 1000 DTU，則伺服器可以支援 54 集區。|
|||

> [!IMPORTANT]
> 每當資料庫數量接近每台伺服器的限制時，可能會出現下列情況：
>
> - 使用 master 資料庫執行查詢時，延遲狀況增加。  這包含資源使用率統計資料的檢視，例如 sys.resource_stats。
> - 管理作業以及涉及列舉伺服器中資料庫入口網站檢視點的轉譯作業，皆增加延遲狀況。

> [!NOTE]
> 若要取得更多 DTU/eDTU 配額、vCore 配額或超過預設數量的伺服器，請在 Azure 入口網站中提交新的支援要求。 如需詳細資訊，請參閱 [Azure SQL Database 的要求配額增加](quota-increase-request.md)。

### <a name="storage-size"></a>儲存體大小

如需單一資料庫的資源儲存體大小，請參閱以 [DTU 為基礎的資源限制](resource-limits-dtu-single-databases.md) 或以 [vCore 為基礎的資源限制](resource-limits-vcore-single-databases.md) ，以瞭解每個定價層的儲存體大小限制。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>達到資料庫資源限制時，會發生什麼事？

### <a name="compute-cpu"></a>計算 CPU

當資料庫計算 CPU 使用率變高時，查詢延遲會增加，而且查詢甚至可以產生時間。在這些情況下，服務可能會將查詢排入佇列，並提供資源以供執行，因為資源會變成可用。
遇到高計算使用率時，緩和選項包括：

- 提高資料庫或彈性集區的計算大小，以提供更多計算資源給資料庫。 請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。
- 將查詢優化，以降低每個查詢的 CPU 資源使用量。 如需詳細資訊，請參閱[查詢微調/提示](performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>儲存體

當使用的資料庫空間達到大小上限，若資料庫的插入和更新作業會增加資料大小，動作即會失敗，且用戶端會收到[錯誤訊息](troubleshoot-common-errors-issues.md)。 SELECT 和 DELETE 子句會繼續成功。

遇到高空間使用率時，緩和選項包括：

- 增加資料庫或彈性集區的大小上限，或新增更多儲存體。 請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。
- 如果資料庫是在彈性集區中，則您也可以將資料庫移至集區外部，使其儲存空間不會與其他資料庫共用。
- 壓縮資料庫以回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](file-space-manage.md)。

### <a name="sessions-and-workers-requests"></a>工作階段和背景工作角色 (要求)

會話和背景工作角色的數目上限取決於服務層級和計算大小 (Dtu/Edtu 或虛擬核心) 。 達到工作階段或背景工作角色的限制時，會拒絕新要求，而且用戶端會收到錯誤訊息。 雖然應用程式能控制可用的連線數目，但並行背景工作角色的數目通常難以預估及控制。 當達到資料庫資源限制，且工作者因為長時間執行的查詢、大型封鎖鏈或過多的查詢平行處理而堆積時，在尖峰負載期間更是如此。

當工作階段或背景工作角色出現高使用率時，緩和選項包括：

- 提高資料庫或彈性集區的服務層級或計算大小。 請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。
- 如果背景工作角色的使用率增加是爭用計算資源所造成，則可將查詢最佳化，以減少每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](performance-guidance.md#query-tuning-and-hinting)。
- 減少 [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (平行處理原則的最大程度) 設定。
- 優化查詢工作負載，減少查詢封鎖的出現次數和持續時間。

### <a name="memory"></a>記憶體

與其他資源不同 (CPU、背景工作、儲存體) ，達到記憶體限制並不會對查詢效能造成負面影響，也不會造成錯誤和失敗。 如 [記憶體管理架構指南](https://docs.microsoft.com/sql/relational-databases/memory-management-architecture-guide)中的詳細說明，SQL Server 資料庫引擎通常會依設計使用所有可用的記憶體。 記憶體主要用於快取資料，以避免更昂貴的儲存體存取。 因此，較高的記憶體使用量通常可改善查詢效能，因為從記憶體讀取的速度較快，而不是從儲存體進行較慢的讀取。

在資料庫引擎啟動後，當工作負載開始從儲存體讀取資料時，資料庫引擎會積極地將資料快取到記憶體中。 在這個初始加速期間之後，通常會看到 `avg_memory_usage_percent` sys.dm_db_resource_stats 中的和資料 `avg_instance_memory_percent` 行接近或等於[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 100% （特別是針對非閒置的資料庫），而且不會完全納入記憶體中。

除了資料快取之外，還會在 database engine 的其他元件中使用記憶體。 當資料快取已使用記憶體和所有可用記憶體的需求時，資料庫引擎會動態地壓縮資料快取大小，以將記憶體提供給其他元件，並在其他元件釋放記憶體時，動態地成長資料快取。

在罕見的情況下，充分要求的工作負載可能會導致記憶體不足的狀況，導致記憶體不足的錯誤。 這可能會發生在0% 到100% 之間的任何記憶體使用量層級。 這較有可能發生在具有比例較小記憶體限制的較小計算大小，以及（或）使用更多記憶體進行查詢處理的工作負載，例如在 [密集的彈性](elastic-pool-resource-management.md)集區中。

遇到記憶體不足的錯誤時，緩和選項包括：
- 提高資料庫或彈性集區的服務層級或計算大小。 請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。
- 優化查詢和設定以減少記憶體使用量。 下表說明常見的解決方案。

|解決方法|描述|
| :----- | :----- |
|減少記憶體授與的大小|如需有關記憶體授與的詳細資訊，請參閱 [瞭解 SQL Server 記憶體授](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) 與的 blog 文章。 避免過度大量的記憶體授與的常見解決方案，是將 [統計資料](https://docs.microsoft.com/sql/relational-databases/statistics/statistics) 保持在最新狀態。 這會導致查詢引擎更精確地估計記憶體耗用量，以避免不必要的大型記憶體授與。</br></br>在使用相容性層級140和更新版本的資料庫中，database engine 可能會使用 [批次模式記憶體授與意見](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback)反應，自動調整記憶體授與大小。 在使用相容性層級150和更新版本的資料庫中，資料庫引擎同樣使用資料 [列模式記憶體授與意見](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)反應，以進行更常見的資料列模式查詢。 這項內建功能可避免由於不必要的大型記憶體授與，而避免發生記憶體不足的錯誤。|
|縮減查詢計劃快取的大小|資料庫引擎會在記憶體中快取查詢計劃，以避免針對每個查詢執行編譯查詢計劃。 若要避免因為僅使用一次的快取計畫所造成的查詢計劃快取膨脹，請啟用 OPTIMIZE_FOR_AD_HOC_WORKLOADS [資料庫範圍](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)的設定。|
|減少鎖定記憶體的大小|資料庫引擎會使用記憶體來進行 [鎖定](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine)。 可能的話，請避免可能取得大量鎖定的大型交易，並導致高鎖定記憶體耗用量。|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>使用者工作負載和內部進程的資源耗用量

每個資料庫中使用者工作負載的 CPU 和記憶體耗用量會在[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)中報告，並在和資料行中報告 `avg_cpu_percent` `avg_memory_usage_percent` 。 針對彈性集區， [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 視圖中會報告集區層級的資源耗用量。 `cpu_percent`針對集區層級的[單一資料庫](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[彈性](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)集區，也會透過 Azure 監視器計量回報使用者工作負載 CPU 耗用量。

Azure SQL Database 需要計算資源來執行核心服務功能，例如高可用性和嚴重損壞修復、資料庫備份與還原、監視、查詢存放區、自動調整等等。系統會使用 [資源管理](#resource-governance) 機制，將這些內部進程的部分整體資源部分設定為有限的部分，讓使用者工作負載可以使用其餘的資源。 當內部進程未使用計算資源時，系統會將它們提供給使用者工作負載使用。

使用者工作負載和內部進程的 CPU 和記憶體耗用量總計，會在 [ [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) ] 和 [ [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 視圖] 中報告， `avg_instance_cpu_percent` 以及資料行中 `avg_instance_memory_percent` 。 這項資料也會透過 `sqlserver_process_core_percent` 和 `sqlserver_process_memory_percent` Azure 監視器計量報告，適用于集區層級的 [單一資料庫](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) 和 [彈性](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) 集區。

使用者工作負載和內部進程的最近資源耗用量詳細資訊，會在 [sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) 和 [sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) 視圖中報告。 如需這些視圖所參考資源集區和工作負載群組的詳細資訊，請參閱 [資源管理](#resource-governance)。 這些視圖會報告使用者工作負載的資源使用率，以及相關聯的資源集區和工作負載群組中的特定內部進程。

在效能監視和疑難排解的內容中，請務必考慮使用者 **CPU 耗用量** (`avg_cpu_percent` 、 `cpu_percent`) 和使用者工作負載的 **總 CPU 耗用量** ，以及 () 的內部進程 `avg_instance_cpu_percent` `sqlserver_process_core_percent` 。

**使用者 CPU 耗用量** 的計算方式是以每個服務目標的使用者工作負載限制百分比表示。 **使用者 CPU 使用率** （100%）表示使用者工作負載已達到服務目標的限制。 不過，當 **總 CPU 耗用量** 達到70-100% 的範圍時，即使回報的 **使用者 CPU 耗用量** 明顯低於100%，還是可以看到使用者工作負載輸送量簡維和查詢延遲增加。 使用較小的服務目標搭配一般配置的計算資源，但相對密集的使用者工作負載（例如 [密集的彈性](elastic-pool-resource-management.md)集區）時，可能會發生這種情況。 當內部進程暫時需要額外的資源時（例如，在建立資料庫的新複本時），較小的服務目標也會發生這種情況。

當 **CPU 總耗用量** 很高時，風險降低選項會與先前所述的相同，並包含服務目標增加及/或使用者工作負載優化。

## <a name="resource-governance"></a>資源管理

為了強制執行資源限制，Azure SQL Database 會使用以 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)、修改和擴充以在 Azure SQL Database 中執行的資源治理實行。 在 SQL Database 中，多個 [資源](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) 集區和 [工作負載群組](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)（具有在集區和群組層級設定的資源限制）提供 [平衡的資料庫即服務](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)。 使用者工作負載和內部工作負載會分類為個別的資源集區和工作負載群組。 主要和可讀取次要複本上的使用者工作負載（包括地理複本）會分類為 `SloSharedPool1` 資源集區和 `UserPrimaryGroup.DBId[N]` 工作負載群組，其中 `N` 代表資料庫識別碼值。 此外，也有多個資源集區和工作負載群組適用于各種內部工作負載。

除了使用 Resource Governor 來管理 SQL 進程內的資源之外，Azure SQL Database 也會使用 Windows [工作物件](https://docs.microsoft.com/windows/win32/procthread/job-objects) 來處理常式層級的資源治理，而 Windows [檔案伺服器 Resource Manager (FSRM) ](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) 用於儲存配額管理。

Azure SQL Database 資源治理本質上是階層式的。 從上到下，限制是在作業系統層級和儲存磁片區層級，使用作業系統資源治理機制和 Resource Governor，然後在資源集區層級使用 Resource Governor，然後在工作負載群組層級使用 Resource Governor 來強制執行。 目前資料庫或彈性集區的有效資源治理限制會顯示在 [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 視圖中。

### <a name="data-io-governance"></a>資料 IO 治理

資料 IO 治理是 Azure SQL Database 中的程式，用來限制對資料庫資料檔案的讀取和寫入實體 IO。 針對每個服務等級設定 IOPS 限制，以最小化「雜訊鄰近」效果，以提供多租使用者服務中的資源配置公平，以及保持在基礎硬體和儲存體的功能內。

針對單一資料庫，工作負載群組限制會套用至資料庫的所有儲存體 IO，而資源集區限制則適用于所有儲存體 IO （針對相同 SQL 集區上的所有資料庫，包括 `tempdb` 資料庫）。 針對彈性集區，工作負載群組限制會套用至集區中的每個資料庫，而資源集區限制會套用至整個彈性集區，包括 `tempdb` 資料庫，該資料庫會在集區中的所有資料庫之間共用。 一般情況下，工作負載可能無法針對資料庫 (單一或集區) 來達成資源集區限制，因為工作負載群組的限制低於資源集區限制，且更快地限制 IOPS/輸送量。 不過，合併的工作負載可能會針對相同集區上的多個資料庫達到集區限制。

例如，如果查詢在沒有任何 IO 資源管理的情況下產生 1000 IOPS，但工作負載群組的最大 IOPS 限制設定為 900 IOPS，則查詢將無法產生超過900的 IOPS。 但是，如果資源集區的最大 IOPS 限制設定為 1500 IOPS，且所有與資源集區相關聯之工作負載群組的 IO 總數超過 1500 IOPS，則相同查詢的 IO 可能會降低到低於 900 IOPS 的工作組限制。

[Sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) view 所傳回的 IOPS 和輸送量最小/最大值可作為限制/上限，而不是保證。 此外，資源管理不保證任何特定的儲存體延遲。 指定使用者工作負載最能實現的延遲、IOPS 和輸送量，不僅取決於 IO 資源治理限制，也會混合使用的 IO 大小，以及基礎儲存體的功能。 SQL Database 會使用大小會在 512 KB 和 4 MB 之間變化的 IOs。 基於強制執行 IOPS 限制的目的，每個 IO 都是依其大小來計算，但在 Azure 儲存體中，資料庫的資料檔案除外。 在此情況下，大於 256 KB 的 IOs 會以多個 256-KB IOs 的形式來表示，以配合 Azure 儲存體 IO 帳戶處理。

若為基本、標準和一般用途的資料庫（使用 Azure 儲存體中的資料檔案）， `primary_group_max_io` 如果資料庫沒有足夠的資料檔案可累積提供此數量的 IOPS，或資料未平均分散于檔案，或基礎 blob 的效能層級限制低於資源治理限制的 iops/輸送量，則可能無法達到此值。 同樣地，如果是由頻繁的交易認可所產生的小型記錄 Io， `primary_max_log_rate` 由於基礎 Azure 儲存體 blob 上的 IOPS 限制，工作負載可能無法達到此值。

資源使用率值（例如 `avg_data_io_percent` 和 `avg_log_write_percent` ）會在 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、  [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)和 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) views 中回報，以最大資源治理限制的百分比計算。 因此，當資源治理以外的因素限制 IOPS/輸送量時，即使回報的資源使用率仍低於100%，還是可以看到 IOPS/輸送量簡維和隨著工作負載增加而增加的延遲。

若要查看每個資料庫檔案的讀取和寫入 IOPS、輸送量和延遲，請使用 [sys.dm_io_virtual_file_stats ( # B1 ](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 函數。 此函式會針對資料庫顯示所有 IO，包括未進行的背景 IO， `avg_data_io_percent` 但會使用基礎儲存體的 IOPS 和輸送量，而且可能會影響觀察到的儲存體延遲。 函式也會在和資料行中，顯示 IO 資源治理針對讀取和寫入所引進的額外延遲 `io_stall_queued_read_ms` `io_stall_queued_write_ms` 。

### <a name="transaction-log-rate-governance"></a>交易記錄速率治理

交易記錄速率治理是 Azure SQL Database 中的程式，用來限制大量插入、SELECT INTO 和索引組建等工作負載的高內嵌費率。 這些限制會在次秒層級進行追蹤和強制執行，以產生記錄檔記錄的速率，並限制輸送量，而不論可以針對資料檔案發出多少 IOs。  交易記錄產生速率目前會以線性方式調整到與硬體相依的點，且最大記錄檔速率可達 96 MB/秒，且具有 vCore 購買模型。

> [!NOTE]
> 實際的實體 IOs 至交易記錄檔不受管制或限制。

對數速率的設定可讓它們在各種案例中達成和持續，而整體系統可以維持其功能，對使用者負載的影響降到最低。 記錄速率治理可確保交易記錄備份會維持在已發佈的復原性 Sla 內。  這種治理也可防止次要複本的過度待處理專案。

產生記錄檔記錄時，系統會評估每項作業，並評估是否應該延遲，以維持每秒所需的最大記錄速率 (MB/秒) 。 當記錄檔記錄排清至儲存體時，不會新增延遲，而是在記錄速率產生本身期間套用記錄速率治理。

在執行時間所加諸的實際記錄產生速率可能也會受到意見反應機制的影響，暫時減少允許的記錄速率，讓系統能夠穩定。 記錄檔空間管理，避免發生記錄空間不足的狀況和可用性群組複寫機制，可能會暫時降低整體的系統限制。

記錄速率管理員流量成形是透過下列等候類型來呈現， (在 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 視圖中公開) ：

| 等候類型 | 注意 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 資料庫限制 |
| POOL_LOG_RATE_GOVERNOR | 集區限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 實例層級限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 意見反應控制，Premium/Business Critical 中的可用性群組實體複寫未趕上 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 意見反應控制，限制速率以避免記錄空間不足的情況 |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | 異地複寫回饋控制，限制記錄速率，以避免資料延遲高且無法使用異地次要資料庫|
|||

遇到阻礙需要的擴充性的記錄速率限制時，請考慮下列選項：

- 擴大至較高的服務層級，以取得最大 96 MB/s 記錄速率，或切換至不同的服務層級。 無論選擇的服務層級為何， [超大規模](service-tier-hyperscale.md) 服務層級都會提供 100 MB/s 的記錄速率。
- 如果要載入的資料是暫時性的，例如在 ETL 程式中暫存資料，則可以載入至 tempdb (，這是最小限度的記錄) 。
- 針對分析案例，請載入叢集資料行存放區涵蓋的資料表。 這可減少由於壓縮所需的記錄速率。 這項技術會增加 CPU 使用率，而且只適用于受益于叢集資料行存放區索引的資料集。

### <a name="storage-space-governance"></a>儲存空間治理

在 Premium 和業務關鍵服務層級中，資料和交易記錄檔會儲存在裝載資料庫或彈性集區之電腦的本機 SSD 磁片區上。 這可提供高 IOPS 和輸送量，以及低 IO 延遲。 此本機磁片區的大小取決於硬體功能，而且是有限的。 在指定的電腦上，客戶資料庫會使用本機磁片區空間，包括 `tempdb` 作業系統、管理軟體、監視資料、記錄等。當資料庫建立、刪除及增加/減少其空間使用量時，機器上的本機空間耗用量會隨時間波動。 

如果系統偵測到電腦上的可用空間不足，且資料庫或彈性集區有空間不足的風險，則會將資料庫或彈性集區移至具有足夠可用空間的不同電腦上，以允許所設定之服務目標的最大大小限制成長。 這項移動會以線上方式進行，類似于資料庫調整作業，而且具有類似的 [影響](single-database-scale.md#impact)，包括在作業結束時) 容錯移轉的短暫 (秒。 此容錯移轉會終止開啟的連接並回復交易，可能會在當時使用資料庫影響應用程式。

因為資料會實際複製到不同的電腦，所以移動大型資料庫可能需要相當長的時間。 在這段期間內，如果大型使用者資料庫或彈性集區的本機空間耗用量，或 `tempdb` 資料庫的成長速度很快，則空間的風險會增加。 系統會以平衡的方式起始資料庫移動，以避免發生空間不足的錯誤，並避免不必要的容錯移轉。

## <a name="next-steps"></a>後續步驟

- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 如需 DTU 與 eDTU 的相關資訊，請參閱 [DTU 與 eDTU](purchasing-models.md#dtu-based-purchasing-model)。
- 如需 tempdb 大小限制的相關資訊，請參閱 [Azure SQL Database 中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
 