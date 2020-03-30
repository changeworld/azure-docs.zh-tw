---
title: Azure SQL 資料庫資源限制 |微軟文檔
description: 本文概述了單個資料庫和彈性池的 Azure SQL 資料庫資源限制。 它也提供抵達或超過那些資源限制時所會發生之情況的相關資訊。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: 550c315023c0ae907c369778c81b16e137004bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067255"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL 資料庫資源限制和資源治理

此文章針對管理單一資料庫和彈性集區的 SQL Database 伺服器，提供 SQL Database 資源限制的概觀。 它提供有關命中或超出這些資源限制時會發生什麼情況的資訊，並描述了用於強制實施這些限制的資源治理機制。

> [!NOTE]
> 如需受控執行個體限制，請參閱[適用於受控執行個體的 SQL Database 資源限制](sql-database-managed-instance-resource-limits.md)。

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
> 每當資料庫數量接近每台 SQL Database 伺服器的限制時，可能會出現下列情況：
>
> - 使用 master 資料庫執行查詢時，延遲狀況增加。  這包含資源使用率統計資料的檢視，例如 sys.resource_stats。
> - 管理作業以及涉及列舉伺服器中資料庫入口網站檢視點的轉譯作業，皆增加延遲狀況。

> [!NOTE]
> 要獲取比預設數量更多的 DTU/eDTU 配額、vCore 配額或更多伺服器，請在 Azure 門戶中提交新的支援請求。 有關詳細資訊，請參閱請求[Azure SQL 資料庫的配額增加](quota-increase-request.md)。

### <a name="storage-size"></a>儲存體大小

對於單個資料庫資源存儲大小，請參閱基於[DTU 的資源限制](sql-database-dtu-resource-limits-single-databases.md)或[基於 vCore 的資源限制](sql-database-vcore-resource-limits-single-databases.md)，以表示每個定價層的存儲大小限制。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>達到資料庫資源限制時，會發生什麼事？

### <a name="compute-dtus-and-edtus--vcores"></a>計算 (DTU 和 eDTU / 虛擬核心)

當資料庫計算利用率（由 DT 和 eDUS 或 vCore 來衡量）變得高時，查詢延遲會增加，查詢甚至會超時。在這些情況下，查詢可能由服務排隊，並在資源空閒時提供執行資源。
遇到高計算使用率時，緩和選項包括：

- 提高資料庫或彈性集區的計算大小，以提供更多計算資源給資料庫。 請參閱[調整單一資料庫資源](sql-database-single-database-scale.md)和[調整彈性集區資源](sql-database-elastic-pool-scale.md)。
- 優化查詢以減少每個查詢的資源利用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存放裝置

當使用的資料庫空間達到大小上限，若資料庫的插入和更新作業會增加資料大小，動作即會失敗，且用戶端會收到[錯誤訊息](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)。 選擇和刪除語句繼續成功。

遇到高空間使用率時，緩和選項包括：

- 增加資料庫或彈性池的最大大小，或添加更多存儲。 請參閱[調整單一資料庫資源](sql-database-single-database-scale.md)和[調整彈性集區資源](sql-database-elastic-pool-scale.md)。
- 如果資料庫在彈性集區，也可以將資料庫移出集區，如此便不會與其他資料庫共用儲存空間。
- 壓縮資料庫以回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="sessions-and-workers-requests"></a>工作階段和背景工作角色 (要求)

會話和輔助工作的最大數量由服務層和計算大小（DTUs/eDT或 vCore）決定。 達到工作階段或背景工作角色的限制時，會拒絕新要求，而且用戶端會收到錯誤訊息。 雖然應用程式能控制可用的連線數目，但並行背景工作角色的數目通常難以預估及控制。 在達到資料庫資源限制且由於運行較長的查詢、大型阻塞鏈或查詢並行性過大而堆積在高峰負載期間尤其如此。

當工作階段或背景工作角色出現高使用率時，緩和選項包括：

- 提高資料庫或彈性集區的服務層級或計算大小。 請參閱[調整單一資料庫資源](sql-database-single-database-scale.md)和[調整彈性集區資源](sql-database-elastic-pool-scale.md)。
- 如果背景工作角色的使用率增加是爭用計算資源所造成，則可將查詢最佳化，以減少每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。
- 減小[MAXDOP（](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)最大並行度）設置。
- 優化查詢工作負載，以減少查詢阻塞的次數和持續時間。

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>按使用者工作負載和內部進程消耗資源

每個資料庫中按使用者工作負載進行的 CPU 和記憶體消耗報告在[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)和[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) `avg_cpu_percent`視圖`avg_memory_usage_percent`、中和列中。 對於彈性池，池級資源消耗報告在[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖中。 使用者工作負載 CPU 限定也會通過`cpu_percent`Azure 監視器指標報告，用於池級別的[單個資料庫](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[彈性池](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)。

Azure SQL 資料庫需要計算資源來實現核心服務功能，如高可用性和災害復原、資料庫備份和恢復、監視、查詢存儲、自動調優等。系統使用[資源治理](#resource-governance)機制為這些內部進程預留了一定有限部分的資源，使其餘資源可用於使用者工作負荷。 當內部進程不使用計算資源時，系統會向使用者工作負載提供這些資源。

在[系統dm_db_resource_stats和sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)視圖、輸入[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current)`avg_instance_cpu_percent`和`avg_instance_memory_percent`列中報告承載單個資料庫或彈性池的 SQL Server 實例上的使用者工作負載和內部進程的總 CPU 和記憶體消耗。 此資料還通過`sqlserver_process_core_percent`和`sqlserver_process_memory_percent`Azure 監視器指標報告池級別的[單個資料庫](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[彈性池](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)。

[系統dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)和[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)視圖中報告了按使用者工作負載和內部進程劃分的最新資源消耗的更詳細細分。 有關這些視圖中引用的資源池和工作負載組的詳細資訊，請參閱[資源治理](#resource-governance)。 這些視圖報告使用者工作負荷的資源利用率以及關聯資源池和工作負荷組中的特定內部進程。

在效能監控和故障排除的上下文中，考慮**使用者 CPU 限定**`avg_cpu_percent` `cpu_percent`（、 ） 以及使用者工作負載和內部進程 （） 的總 CPU`avg_instance_cpu_percent``sqlserver_process_core_percent`**消耗**（）非常重要。

**使用者 CPU 限定**量按每個服務目標中使用者工作負載限制的百分比計算。 **100% 的使用者 CPU 利用率**表示使用者工作負載已達到服務目標的限制。 但是，當**總 CPU 限定**率達到 70-100% 範圍時，即使報告的**使用者 CPU 限定**率仍明顯低於 100%，使用者工作負載輸送量也會展平，查詢延遲也會增加。 當使用較小的服務目標，適度分配計算資源，但使用者工作負載相對密集時，就會發生這種情況，例如[在密集的彈性池](sql-database-elastic-pool-resource-management.md)中。 當內部進程暫時需要額外的資源時，例如創建資料庫的新副本時，也會發生較小的服務目標。"

當**總 CPU 限定**量高時，緩解選項與前面所述選項相同，包括服務目標增加和/或使用者工作負載優化。

## <a name="resource-governance"></a>資源管理

為了實施資源限制，Azure SQL 資料庫使用基於 SQL Server[資源調控器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)的資源治理實現，該實現進行了修改並擴展，以在 Azure 中運行 SQL Server 資料庫服務。 在服務中的每個 SQL Server 實例上，有多個[資源池](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool)和[工作負載組](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)，在池和組級別設置資源限制，以提供[平衡的資料庫即服務](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)。 使用者工作負荷和內部工作負荷分為單獨的資源池和工作負載組。 主副本和可讀輔助副本（包括地理副本）上的使用者工作負荷被分類到`SloSharedPool1`資源池和`UserPrimaryGroup.DBId[N]`工作負載組中，其中`N`代表資料庫 ID 值。 此外，還有多個資源池和工作負載組用於各種內部工作負荷。

除了使用資源調控器來管理 SQL Server 進程中的資源外，Azure SQL 資料庫還使用 Windows[工作物件](https://docs.microsoft.com/windows/win32/procthread/job-objects)進行進程級資源治理，使用 Windows[檔案伺服器資源管理員 （FSRM）](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview)進行存儲配額管理。

Azure SQL 資料庫資源治理本質上是階層式。 從上到下，使用作業系統資源治理機制和資源調控器在作業系統級別和存儲卷級別實施限制，然後在資源池級別使用資源調控器，然後在使用工作負載組級別執行限制資源調控器。 當前資料庫或彈性池有效的資源治理限制在[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)視圖中顯示。 

### <a name="data-io-governance"></a>資料 IO 治理

資料 IO 治理是 Azure SQL 資料庫中用於限制資料庫資料檔案的讀取和寫入物理 IO 的過程。 為每個服務等級設置 IOPS 限制，以儘量減少"嘈雜鄰居"效果，在多租戶服務中提供資源配置公平性，並保持在底層硬體和存儲的功能範圍內。

對於單個資料庫，工作負載組限制應用於針對資料庫的所有存儲 IO，而資源池限制適用于同一 SQL Server 實例上的所有資料庫（包括`tempdb`資料庫）上的所有存儲 IO。 對於彈性池，工作負載組限制適用于池中的每個資料庫，而資源池限制適用于整個彈性池，包括`tempdb`在池中的所有資料庫之間共用的資料庫。 通常，針對資料庫（單個或池）的工作負載可能無法實現資源池限制，因為工作負載組限制低於資源池限制，並且更快地限制 IOPS/輸送量。 但是，組合工作負載可能會針對同一 SQL Server 實例上的多個資料庫達到池限制。

例如，如果查詢生成 1000 IOPS 而不進行任何 IO 資源治理，但工作負載組的最大 IOPS 限制設置為 900 IOPS，則查詢將無法生成超過 900 個 IOPS。 但是，如果資源池的最大 IOPS 限制設置為 1500 IOPS，並且與資源池關聯的所有工作負荷組的總 IO 超過 1500 IOPS，則同一查詢的 IO 可能會減少到 900 IOPS 的工作組限制之下。

[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)視圖返回的 IOPS 和輸送量最小/最大值充當限制/上限，而不是保證。 此外，資源治理不保證任何特定的存儲延遲。 給定使用者工作負載的最佳可實現延遲、IOPS 和輸送量不僅取決於 IO 資源治理限制，還取決於使用的 IO 大小組合以及基礎存儲的功能。 SQL Server 使用大小在 512 KB 和 4 MB 之間不同的 I。 為了實施 IOPS 限制，除 Azure 存儲中具有資料檔案的資料庫外，都會考慮每個 IO 的大小。 在這種情況下，大於 256 KB 的 IO 將記為多個 256 KB IO，以便與 Azure 存儲 IO 核算保持一致。

對於在 Azure 存儲中使用資料檔案的基本、標準和通用資料庫，如果資料庫沒有足夠的資料`primary_group_max_io`檔來累積提供此數量的 IOPS，或者如果資料在檔之間分佈不均勻，或者如果基礎 blob 的性能層將 IOPS/輸送量限制在資源治理限制以下，則可能無法實現該值。 同樣，由於基礎 Azure 存儲 Blob 上的 IOPS 限制，`primary_max_log_rate`因此工作負荷可能無法實現小日誌 I。

在[sys.dm_db_resource_stats、sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)和[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖中報告的資源利用率值（如[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)`avg_data_io_percent`和`avg_log_write_percent`）按最大資源治理限制的百分比計算。 因此，當資源治理以外的因素限制 IOPS/輸送量時，即使報告的資源利用率保持在 100% 以下，IOPS/輸送量也會隨著工作負載的增加而展平和延遲增加。 

要查看每個資料庫檔案的讀取和寫入 IOPS、輸送量和延遲，請使用[sys.dm_io_virtual_file_stats（）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)函數。 此函數針對資料庫顯示所有 IO，包括未計入`avg_data_io_percent`的背景 IO，而是使用 IOPS 和基礎存儲的輸送量，並可能影響觀察到的存儲延遲。 該函數還顯示 IO 資源治理可能為讀取和寫入分別引入`io_stall_queued_read_ms``io_stall_queued_write_ms`的額外延遲。

### <a name="transaction-log-rate-governance"></a>事務日誌速率治理

事務日誌速率治理是 Azure SQL 資料庫中用於限制工作負載（如批量插入、SELECT INTO 和索引生成）的高引入率的過程。 這些限制在次秒級別跟蹤和強制執行到日誌記錄生成速率，從而限制輸送量，而不管有多少 I 可以針對資料檔案發出。  事務日誌生成速率當前線性擴展到依賴于硬體的點，vCore 購買模型允許的最大日誌速率為 96 MB/s。 

> [!NOTE]
> 事務日誌檔的實際物理 I 不受控制或限制。

日誌速率的設置可以在各種方案中實現和維持，而整個系統可以保持其功能，從而將使用者負載的影響降至最低。 日誌速率治理可確保事務記錄備份保持在已發佈的可恢復性 SL 範圍內。  此治理還可防止輔助副本上過多的積壓工作。

生成日誌記錄時，將評估和評估每個操作是否應延遲以保持所需的最大日誌速率（MB/s/秒）。 當將日誌記錄刷新到存儲時，不會添加延遲，而是在日誌速率生成過程中應用日誌速率治理。

運行時施加的實際日誌生成速率也可能受回饋機制的影響，從而暫時降低允許的日誌速率，以便系統能夠穩定下來。 日誌檔空間管理，避免運行在日誌空間條件不足和可用性組複製機制可以暫時減少整體系統限制。

日誌速率調速器流量整形通過以下等待類型（在[系統dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV 中公開） 浮出水面：

| 等候類型 | 注意 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 資料庫限制 |
| POOL_LOG_RATE_GOVERNOR | 池限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 實例級別限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 回饋控制，高級/業務關鍵版中的可用性組物理複製不跟上 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 回饋控制，限制速率以避免日誌空間不足的情況 |
|||

遇到妨礙所需可伸縮性的日誌速率限制時，請考慮以下選項：
- 向上擴展到更高的服務等級，以獲得最大 96 MB/s 的日誌速率，或切換到其他服務層。 [超大規模](sql-database-service-tier-hyperscale.md)服務層提供 100 MB/s 的日誌速率，而不考慮所選服務等級。
- 如果正在載入的資料是暫時性的（例如 ETL 進程中暫存資料），則可以將其載入到 tempdb（記錄最小）。 
- 對於分析方案，載入到群集列存儲覆蓋表。 這降低了由於壓縮而導致的所需日誌速率。 此技術確實提高了 CPU 利用率，並且僅適用于受益于群集列存儲索引的資料集。 

## <a name="next-steps"></a>後續步驟

- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 如需 DTU 與 eDTU 的相關資訊，請參閱 [DTU 與 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)。
- 如需 tempdb 大小限制的相關資訊，請參閱 [Azure SQL Database 中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
