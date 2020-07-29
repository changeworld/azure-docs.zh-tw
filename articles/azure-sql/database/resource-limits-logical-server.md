---
title: Azure 中邏輯伺服器的資源限制
description: 本文概述 Azure SQL Database 和 Azure Synapse 分析所使用之 Azure 中邏輯伺服器的資源限制。 它也提供抵達或超過那些資源限制時所會發生之情況的相關資訊。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 06/10/2020
ms.openlocfilehash: eac5814eb977a01135ad2fcd9551b3475673dbca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84691725"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Azure SQL Database 和 Azure Synapse Analytics 伺服器的資源限制
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文概述 Azure SQL Database 和 Azure Synapse 分析所使用之邏輯伺服器的資源限制。 其中提供當達到或超過這些資源限制時所發生狀況的資訊，並說明用來強制執行這些限制的資源管理機制。

> [!NOTE]
> 如需 Azure SQL 受控執行個體限制，請參閱[SQL Database 受控實例的資源限制](../managed-instance/resource-limits.md)。

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
> 若要取得更多 DTU/eDTU 配額、vCore 配額或超過預設數量的伺服器，請在 Azure 入口網站中提交新的支援要求。 如需詳細資訊，請參閱[Azure SQL Database 的要求配額增加](quota-increase-request.md)。

### <a name="storage-size"></a>儲存體大小

針對單一資料庫資源儲存體大小，請參閱以[DTU 為基礎的資源限制](resource-limits-dtu-single-databases.md)或以[vCore 為基礎的資源限制](resource-limits-vcore-single-databases.md)，以瞭解每個定價層的儲存體大小限制。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>達到資料庫資源限制時，會發生什麼事？

### <a name="compute-cpu"></a>計算 CPU

當資料庫計算 CPU 使用率變高時，查詢延遲會增加，而且查詢甚至可以準時完成。在這些情況下，查詢可能會由服務排入佇列，並提供資源以供執行，因為資源會變成可用。
遇到高計算使用率時，緩和選項包括：

- 提高資料庫或彈性集區的計算大小，以提供更多計算資源給資料庫。 請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。
- 將查詢優化，以降低每個查詢的 CPU 資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>儲存體

當使用的資料庫空間達到大小上限，若資料庫的插入和更新作業會增加資料大小，動作即會失敗，且用戶端會收到[錯誤訊息](troubleshoot-common-errors-issues.md)。 SELECT 和 DELETE 子句會繼續成功。

遇到高空間使用率時，緩和選項包括：

- 增加資料庫或彈性集區的大小上限，或新增更多儲存體。 請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。
- 如果資料庫位於彈性集區中，則可以將資料庫移至集區之外，使其儲存空間不會與其他資料庫共用。
- 壓縮資料庫以回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](file-space-manage.md)。

### <a name="sessions-and-workers-requests"></a>工作階段和背景工作角色 (要求)

會話和背景工作角色的最大數目取決於服務層級和計算大小（Dtu/Edtu 或虛擬核心）。 達到工作階段或背景工作角色的限制時，會拒絕新要求，而且用戶端會收到錯誤訊息。 雖然應用程式能控制可用的連線數目，但並行背景工作角色的數目通常難以預估及控制。 在尖峰負載期間，如果達到資料庫資源限制，而背景工作角色因為較長的執行查詢、大型封鎖鏈或過度查詢平行處理原則而進行匯總，這就更是如此。

當工作階段或背景工作角色出現高使用率時，緩和選項包括：

- 提高資料庫或彈性集區的服務層級或計算大小。 請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。
- 如果背景工作角色的使用率增加是爭用計算資源所造成，則可將查詢最佳化，以減少每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](performance-guidance.md#query-tuning-and-hinting)。
- 減少[MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) （平行處理原則的最大程度）設定。
- 優化查詢工作負載，以減少發生次數和查詢封鎖的持續時間。

### <a name="memory"></a>Memory

不同于其他資源（CPU、背景工作、儲存體），達到記憶體限制並不會對查詢效能造成負面影響，也不會造成錯誤和失敗。 如[記憶體管理架構指南](https://docs.microsoft.com/sql/relational-databases/memory-management-architecture-guide)中的詳細說明，SQL Server 資料庫引擎通常會根據設計來使用所有可用的記憶體。 記憶體主要用於快取資料，以避免較昂貴的儲存體存取。 因此，較高的記憶體使用率通常會改善查詢效能，因為從記憶體讀取的速度較快，而不是從儲存體讀取速度較慢。

當資料庫引擎啟動之後，當工作負載開始從儲存體讀取資料時，資料庫引擎會積極地將資料快取到記憶體中。 在這個初始的提升時間週期之後，通常會看到 `avg_memory_usage_percent` sys.databases 中的和資料 `avg_instance_memory_percent` 行， [dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)為接近或等於100%，特別是針對非閒置的資料庫，而且不會完全納入記憶體中。

除了資料快取之外，記憶體也會用於 database engine 的其他元件。 當記憶體需求和所有可用的記憶體都已由資料快取使用時，資料庫引擎會動態壓縮資料快取大小，讓其他元件可使用記憶體，而當其他元件釋放記憶體時，會動態地增加資料快取。

在罕見的情況下，充分要求的工作負載可能會造成記憶體不足的狀況，導致記憶體不足的錯誤。 這可能發生在0% 到100% 之間的任何記憶體使用率層級。 這較可能發生在具有比例較小的記憶體限制的較小計算大小，以及/或使用更多記憶體來進行查詢處理的工作負載（例如，在[密集彈性](elastic-pool-resource-management.md)集區中）。

遇到記憶體不足的錯誤時，緩和選項包括：
- 提高資料庫或彈性集區的服務層級或計算大小。 請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。
- 優化查詢和設定以減少記憶體使用率。 下表說明常見的解決方案。

|解決方法|描述|
| :----- | :----- |
|減少記憶體授權的大小|如需記憶體授與的詳細資訊，請參閱[瞭解 SQL Server 記憶體授](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595)與的 blog 文章。 避免過度龐大的記憶體授與的常見解決方案是將[統計資料](https://docs.microsoft.com/sql/relational-databases/statistics/statistics)保持在最新狀態。 這會導致查詢引擎更精確地估計記憶體耗用量，以避免不必要的大型記憶體授與。</br></br>在使用相容性層級140和更新版本的資料庫中，資料庫引擎可能會使用[批次模式記憶體授與意見](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?view=sql-server-ver15#batch-mode-memory-grant-feedback)反應，自動調整記憶體授與大小。 在使用相容性層級150和更新版本的資料庫中，資料庫引擎同樣會使用資料[列模式記憶體授與意見](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?view=sql-server-ver15#row-mode-memory-grant-feedback)反應，以進行更常見的資料列模式查詢。 這項內建功能有助於避免因為不必要的大型記憶體授與而發生記憶體不足的錯誤。|
|縮小查詢計劃快取的大小|資料庫引擎會在記憶體中快取查詢計劃，以避免針對每個查詢執行編譯查詢計劃。 若要避免因為快取計畫所造成的查詢計劃快取膨脹只會使用一次，請啟用 OPTIMIZE_FOR_AD_HOC_WORKLOADS[資料庫範圍](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)設定。|
|減少鎖定記憶體的大小|資料庫引擎會使用記憶體來進行[鎖定](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine)。 可能的話，請避免可能取得大量鎖定並導致高鎖定記憶體耗用量的大型交易。|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>使用者工作負載和內部進程的資源耗用量

每個資料庫中使用者工作負載的 CPU 和記憶體耗用量，會在和資料行中的[dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)和[sys.databases resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) views 中報告 `avg_cpu_percent` `avg_memory_usage_percent` 。 針對彈性集區，系統會在 [ [elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) ] 視圖中回報集區層級資源耗用量。 `cpu_percent`針對集區層級的[單一資料庫](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[彈性](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)集區，也會透過 Azure 監視器計量來回報使用者工作負載 CPU 耗用量。

Azure SQL Database 需要計算資源來執行核心服務功能，例如高可用性和嚴重損壞修復、資料庫備份與還原、監視、查詢存放區、自動調整等等。系統會使用[資源管理](#resource-governance)機制，為這些內部進程設定特定有限部分的整體資源，讓使用者工作負載可使用其餘的資源。 有時，當內部進程未使用計算資源時，系統會將它們提供給使用者工作負載。

使用者工作負載和內部進程的 CPU 和記憶體耗用量總計會報告在和資料行中的[dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)和[sys.databases resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) views `avg_instance_cpu_percent` `avg_instance_memory_percent` 。 此資料也會 `sqlserver_process_core_percent` `sqlserver_process_memory_percent` 針對集區層級上的[單一資料庫](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[彈性](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)集區，透過和 Azure 監視器計量來回報。

使用者工作負載和內部進程最近資源耗用量的詳細細目會在[dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)和[sys.databases dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)視圖中回報。 如需這些視圖中所參考資源集區和工作負載群組的詳細資訊，請參閱[資源管理](#resource-governance)。 這些視圖會報告使用者工作負載的資源使用率，以及相關聯資源集區和工作負載群組中的特定內部進程。

在效能監視和疑難排解的內容中，請務必考慮**使用者的 cpu 耗用量**（ `avg_cpu_percent` 、 `cpu_percent` ）和使用者工作負載和內部進程的**cpu 總耗用量**（ `avg_instance_cpu_percent` 、 `sqlserver_process_core_percent` ）。

**使用者 CPU 耗用量**是以每個服務目標中使用者工作負載限制的百分比計算。 100% 的**使用者 CPU 使用率**表示使用者工作負載已達到服務目標的限制。 不過，當**CPU 總耗用量**達到70-100% 的範圍時，就可以看到使用者工作負載輸送量簡維化和查詢延遲增加，即使回報的**使用者 CPU 耗用量**明顯低於100%。 使用較小的服務目標與一般的計算資源配置，但相對密集的使用者工作負載（例如，在[密集的彈性](elastic-pool-resource-management.md)集區中）時，可能會發生這種情況。 當內部進程暫時需要額外的資源時（例如，建立資料庫的新複本時），也可能會發生這種情況，但服務目標較小。

當**CPU 耗用量總計**偏高時，風險降低選項會與先前所述相同，並包含服務目標增加和/或使用者工作負載優化。

## <a name="resource-governance"></a>資源管理

若要強制執行資源限制，Azure SQL Database 會使用以 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)為基礎的資源治理執行，並加以修改和擴充，以在 Azure SQL Database 中執行。 在 SQL Database 中，多個[資源](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool)集區和[工作負載群組](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)，同時在集區和群組層級設定資源限制，提供[平衡的資料庫即服務](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)。 使用者工作負載和內部工作負載會分類成不同的資源集區和工作負載群組。 主要和可讀取次要複本（包括異地複本）的使用者工作負載會分類到 `SloSharedPool1` 資源集區和 `UserPrimaryGroup.DBId[N]` 工作負載群組中，其中 `N` 代表資料庫識別碼值。 此外，有多個資源集區和工作負載群組適用于各種內部工作負載。

除了使用 Resource Governor 來管理 SQL 程式中的資源之外，Azure SQL Database 也會針對進程層級資源管理使用 Windows[工作物件](https://docs.microsoft.com/windows/win32/procthread/job-objects)，並將 Windows[檔案伺服器 Resource Manager （FSRM）](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview)用於儲存配額管理。

Azure SQL Database 的資源管理本質上是階層式的。 從上到下，限制會在 OS 層級和儲存磁片區層級強制執行，使用作業系統資源管理機制和 Resource Governor，然後使用 Resource Governor，然後在使用 Resource Governor 的工作負載群組層級。 目前資料庫或彈性集區的資源管理限制會顯示在 [ [dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) ] 視圖中。

### <a name="data-io-governance"></a>資料 IO 治理

資料 IO 治理是 Azure SQL Database 中用來針對資料庫的資料檔案限制讀取和寫入實體 IO 的進程。 系統會針對每個服務等級設定 IOPS 限制，以將「雜訊鄰近」的效果降到最低，以提供多租使用者服務中的資源配置公平，並維持在基礎硬體和存放裝置的功能中。

針對單一資料庫，工作負載群組限制會針對資料庫套用至所有儲存體 IO，而資源集區限制則適用于所有儲存體 IO （針對相同 SQL 集區上的所有資料庫，包括 `tempdb` 資料庫）。 針對彈性集區，工作負載群組限制會套用至集區中的每個資料庫，而資源集區限制則適用于整個彈性集區，包括 `tempdb` 資料庫（在集區中的所有資料庫之間共用）。 一般而言，工作負載對資料庫（單一或集區）可能無法達到資源集區限制，因為工作負載群組限制低於資源集區限制，並會更快限制 IOPS/輸送量。 不過，合併的工作負載可能會與同一個集區上的多個資料庫達到集區限制。

例如，如果查詢在沒有任何 IO 資源管理的情況下產生 1000 IOPS，但工作負載群組的最大 IOPS 限制設定為 900 IOPS，則查詢將無法產生超過900個 IOPS。 不過，如果資源集區的最大 IOPS 限制設定為 1500 IOPS，而且與資源集區相關聯之所有工作負載群組的 IO 總數超過 1500 IOPS，則相同查詢的 IO 可能會降到低於工作組的 900 IOPS 限制。

[Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) view 傳回的 IOPS 和輸送量最小/最大值會作為限制/上限，而不是保證。 此外，資源管理不保證任何特定的儲存體延遲。 針對指定的使用者工作負載，最佳可達成的延遲、IOPS 和輸送量，不僅取決於 IO 資源治理限制，同時也會混合使用的 IO 大小，以及基礎儲存體的功能。 SQL Database 使用不同于 512 KB 和 4 MB 的 Io 大小。 基於強制執行 IOPS 限制的目的，每個 IO 不論其大小為何都會進行計算，但具有 Azure 儲存體中資料檔案的資料庫除外。 在此情況下，大於 256 KB 的 IOs 會計算為多個 256 KB 的 io，以配合 Azure 儲存體 IO 帳戶處理。

針對在 Azure 儲存體中使用資料檔案的基本、標準和一般用途資料庫， `primary_group_max_io` 如果資料庫沒有足夠的資料檔案可累積提供此 IOPS 數，或如果資料未平均分散到檔案，或基礎 blob 的效能層級限制低於資源治理限制的 IOPS/輸送量，則此值可能無法達到。 同樣地，使用經常交易認可所產生的小型記錄 Io， `primary_max_log_rate` 由於基礎 Azure 儲存體 blob 的 IOPS 限制，工作負載可能無法達到此值。

`avg_data_io_percent` `avg_log_write_percent` [系統](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)會以資源治理限制的最大百分比來計算 dm_db_resource_stats、 [sys.databases resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)和[sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) views 中所報告的資源使用量值（例如和）。 因此，當資源管理以外的因素限制 IOPS/輸送量時，即使報告的資源使用率低於100%，仍有可能看到 IOPS/輸送量簡維和隨著工作負載增加而增加的延遲。

若要查看每個資料庫檔案的讀取和寫入 IOPS、輸送量和延遲，請使用[dm_io_virtual_file_stats （）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)函數。 此函式會針對資料庫呈現所有 IO，包括未考慮的背景 IO， `avg_data_io_percent` 但會使用基礎儲存體的 IOPS 和輸送量，而且可能會影響觀察到的儲存延遲。 此函式也會在和資料行中，針對讀取和寫入，分別呈現 IO 資源治理可能引進的額外延遲 `io_stall_queued_read_ms` `io_stall_queued_write_ms` 。

### <a name="transaction-log-rate-governance"></a>交易記錄速率治理

交易記錄速率治理是 Azure SQL Database 中的程式，用來限制大量插入、選取 INTO 和索引組建等工作負載的高內嵌速率。 這些限制會在次秒層級追蹤並強制執行，以產生記錄檔記錄的速率、限制輸送量，而不論可能針對資料檔案發出多少 Io。  交易記錄產生速率目前已線性相應增加至與硬體相依的點，而且記錄速率上限為 96 MB/s，並具有 vCore 購買模型。

> [!NOTE]
> 實際的實體 IOs 到交易記錄檔不受管理或限制。

記錄速率的設定可讓您在各種情況下達成和持續，而整體系統可以維持其功能，並將對使用者負載的影響降至最低。 記錄速率治理可確保交易記錄備份保留在已發佈的復原能力 Sla 內。  這種治理也會防止次要複本上有過多的待處理專案。

產生記錄檔記錄時，系統會評估每個作業並評估是否應延遲，以維持最大所需的記錄速率（每秒 MB/秒）。 當記錄檔記錄排清至儲存體時，不會新增延遲，而是在產生記錄速率時套用記錄速率治理。

在執行時間加諸的實際記錄產生速率可能也會受到意見反應機制的影響，暫時減少允許的記錄檔速率，讓系統能夠穩定。 記錄檔空間管理，避免遇到記錄空間不足的情況，以及可用性群組複寫機制，可以暫時降低整體系統限制。

記錄速率管理員流量成形會透過下列等候類型來呈現（在 sys.databases 中公開[dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)和[sys.databases dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)視圖）：

| 等候類型 | 備註 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 資料庫限制 |
| POOL_LOG_RATE_GOVERNOR | 集區限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 實例層級限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 意見反應控制，Premium/Business Critical 中的可用性群組實體複寫未趕上 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 意見反應控制，限制速率以避免發生記錄空間不足的狀況 |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | 異地複寫意見反應控制，限制記錄速率以避免高資料延遲和異地次要資料庫無法利用|
|||

當遇到阻礙所需擴充性的記錄速率限制時，請考慮下列選項：

- 相應增加至較高的服務層級，以取得最大 96 MB/秒的記錄速率，或切換至不同的服務層。 無論選擇的服務等級為何，[超大規模資料庫](service-tier-hyperscale.md)服務層級都會提供 100 MB/s 的記錄速率。
- 如果載入的資料是暫時性的（例如 ETL 進程中的暫存資料），則可以將它載入至 tempdb （這是最低限度記錄）。
- 針對分析案例，載入叢集資料行存放區涵蓋的資料表。 這會減少因為壓縮而需要的記錄速率。 這項技術會增加 CPU 使用率，而且僅適用于從叢集資料行存放區索引獲益的資料集。

## <a name="next-steps"></a>後續步驟

- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 如需 DTU 與 eDTU 的相關資訊，請參閱 [DTU 與 eDTU](purchasing-models.md#dtu-based-purchasing-model)。
- 如需 tempdb 大小限制的相關資訊，請參閱 [Azure SQL Database 中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
 