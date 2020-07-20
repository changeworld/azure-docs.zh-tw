---
title: 密集彈性集區中的資源管理
description: 以多個資料庫管理 Azure SQL Database 彈性集區中的計算資源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 06/29/2020
ms.openlocfilehash: d35b4691bcf6e40edd57d4caeae00e18a8298925
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85558874"
---
# <a name="resource-management-in-dense-elastic-pools"></a>密集彈性集區中的資源管理
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database[彈性](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)集區是一種符合成本效益的解決方案，可讓您以不同的資源使用量來管理許多資料庫。 彈性集區中的所有資料庫會共用相同的資源配置（例如 CPU、記憶體、背景工作執行緒、儲存空間、tempdb），假設**集區中只有一個資料庫子集會在任何指定時間使用計算資源**。 這種假設可讓彈性集區符合成本效益。 客戶不需支付每個個別資料庫可能需要的所有資源，而是針對集區中的所有資料庫共用一組較小的資源來支付費用。

## <a name="resource-governance"></a>資源管理

資源分享會要求系統仔細控制資源使用方式，將「雜訊鄰近」的效果降到最低，其中具有高資源耗用量的資料庫會影響相同彈性集區中的其他資料庫。 同時，系統必須提供足夠的資源來取得高可用性和嚴重損壞修復（HADR）、備份與還原、監視、查詢存放區、自動調整等功能，才能可靠地運作。

Azure SQL Database 使用多個資源管理機制來達到這些目標，包括處理常式層級資源管理的 Windows[工作物件](https://docs.microsoft.com/windows/win32/procthread/job-objects)、用於儲存配額管理的 Windows[檔案伺服器 Resource Manager （FSRM）](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) ，以及 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)的已修改和擴充版本，以在 SQL Database 內執行資源管理。

彈性集區的主要設計目標是符合成本效益。 基於這個理由，系統會刻意允許客戶建立_密集_集區，其中包含接近或達到允許上限的資料庫數目，但具有一般的計算資源配置。 基於相同的理由，系統不會為其內部程式保留所有可能需要的資源，但允許內部進程和使用者工作負載之間的資源分享。

這種方法可讓客戶使用密集的彈性集區，以達到適當的效能和主要成本節約。 不過，如果密集集區中的多個資料庫的工作負載夠密集，資源爭用就會變得很重要。 資源爭用會降低使用者工作負載的效能，而且可能會對內部進程造成負面影響。

> [!IMPORTANT]
> 在具有許多作用中資料庫的密集集區中，將集區中的資料庫數目增加至針對[DTU](resource-limits-dtu-elastic-pools.md)和[vCore](resource-limits-vcore-elastic-pools.md)彈性集區記載的最大值可能不可行。
>
> 可以放在密集集區中的資料庫數目，而不會造成資源爭用和效能問題，取決於並行作用中資料庫的數目，以及每個資料庫中使用者工作負載的資源耗用量。 當使用者工作負載變更時，這個數位可能會隨著時間而改變。

當密集封裝集區中發生資源爭用時，客戶可以選擇下列一個或多個動作來減輕此問題：

- 微調查詢工作負載以減少資源耗用量，或在一段時間後將資源耗用量分散到多個資料庫。
- 將一些資料庫移至另一個集區，或將其設為獨立資料庫，以降低集區密度。
- 相應增加集區以取得更多資源。

如需如何執行最後兩個動作的建議，請參閱本文稍後的[操作建議](#operational-recommendations)。 減少資源爭用會同時受益于使用者工作負載和內部進程，並可讓系統可靠地維護預期的服務層級。

## <a name="monitoring-resource-consumption"></a>監視資源耗用量

為避免因為資源競爭而降低效能，使用密集彈性集區的客戶應該主動監視資源耗用量，並在增加資源爭用開始影響工作負載時，及時採取行動。 持續監視很重要，因為集區中的資源使用量會隨著使用者工作負載的變更、資料量和散發中的變更、集區密度的變更，以及 Azure SQL Database 服務中的變更而變更。

Azure SQL Database 提供數個與這種監視相關的計量。 超過每個度量的建議平均值表示集區中的資源爭用，應使用先前所述的其中一個動作來解決。

|度量名稱|Description|建議的平均值|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|與彈性集區相關聯之 SQL 進程的 CPU 使用率（以基礎作業系統來測量）。 適用于每個資料庫的[sys.databases dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)視圖，以及資料庫中的[sys.databases elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖 `master` 。 此計量也會發出至 Azure 監視器，其[名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` ，並可在 Azure 入口網站中查看。 相同彈性集區中的每個資料庫都有相同的此值。|低於70%。 可能可以接受最多90% 的偶爾短尖峰。|
|`max_worker_percent`|[工作者執行緒]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide)使用率。 針對集區中的每個資料庫以及集區本身提供。 資料庫層級的背景工作執行緒數目和集區層級有不同的限制，因此建議您在這兩個層級監視此計量。 適用于每個資料庫的[sys.databases dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)視圖，以及資料庫中的[sys.databases elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖 `master` 。 此計量也會發出至 Azure 監視器，其[名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent` ，並可在 Azure 入口網站中查看。|低於80%。 最多100% 的尖峰會導致連接嘗試和查詢失敗。|
|`avg_data_io_percent`|讀取和寫入實體 IO 的 IOPS 使用率。 針對集區中的每個資料庫以及集區本身提供。 資料庫層級的 IOPS 數目和集區層級上有不同的限制，因此建議您在這兩個層級監視此計量。 適用于每個資料庫的[sys.databases dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)視圖，以及資料庫中的[sys.databases elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖 `master` 。 此計量也會發出至 Azure 監視器，其[名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent` ，並可在 Azure 入口網站中查看。|低於80%。 可能可以接受最多100% 的偶爾短尖峰。|
|`avg_log_write_percent`|交易記錄寫入 IO 的輸送量使用率。 針對集區中的每個資料庫以及集區本身提供。 資料庫層級的記錄輸送量有不同的限制，並且在集區層級，因此建議在這兩個層級監視此計量。 適用于每個資料庫的[sys.databases dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)視圖，以及資料庫中的[sys.databases elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖 `master` 。 此計量也會發出至 Azure 監視器，其[名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent` ，並可在 Azure 入口網站中查看。 當此度量接近100% 時，所有資料庫修改（INSERT、UPDATE、DELETE、MERGE 語句、SELECT .。。 INTO、BULK INSERT 等等）會變慢。|低於90%。 可能可以接受最多100% 的偶爾短尖峰。|
|`oom_per_second`|彈性集區中記憶體不足（OOM）錯誤的速率，這是記憶體壓力的指標。 可在[sys.databases dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current)視圖中取得。 如需計算此度量的範例查詢，請參閱[範例](#examples)。|0|
|`avg_storage_percent`|彈性集區層級的儲存空間使用量。 適用于資料庫中的[elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖 `master` 。 此計量也會發出至 Azure 監視器，其[名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent` ，並可在 Azure 入口網站中查看。|低於80%。 對於沒有資料成長的集區，可以使用100%。|
|`tempdb_log_used_percent`|資料庫中的交易記錄空間使用量 `tempdb` 。 即使在某個資料庫中建立的暫存物件不會顯示在相同彈性集區中的其他資料庫中，但 `tempdb` 是相同集區中所有資料庫的共用資源。 從集區中的一個資料庫啟動的長時間執行或孤立交易 `tempdb` ，可能會耗用大量的交易記錄，並導致相同集區中其他資料庫的查詢失敗。 衍生自[sys.databases dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql)和[sys.databases database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) views。 此計量也會發出至 Azure 監視器，並可在 Azure 入口網站中查看。 請參閱範例查詢[範例](#examples)，以傳回此度量的目前值。|低於50%。 最多可接受80% 的偶爾尖峰。|
|||

除了這些計量以外，Azure SQL Database 提供的視圖會傳回實際的資源治理限制，以及傳回資源集區層級和工作負載群組層級之資源使用率統計資料的其他視圖。

|檢視表名稱|Description|  
|-----------------|--------------------------------|  
|[sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|傳回目前資料庫或彈性集區中資源管理機制所使用的實際設定和容量設定。|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|傳回目前資源集區狀態、資源集區的目前設定，以及累計資源集區統計資料的相關資訊。|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|傳回累計工作負載群組統計資料和工作負載群組的目前設定。 此視圖可以與資料行上的 dm_resource_governor_resource_pools sys.databases 聯結 `pool_id` ，以取得資源集區資訊。|
|[sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|傳回過去32分鐘的資源集區使用率統計資料。 每個資料列都代表20秒的間隔。 資料行會在 `delta_` 間隔期間傳回每個統計資料中的變更。|
|[sys. dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|傳回過去32分鐘的工作負載群組使用率統計資料。 每個資料列都代表20秒的間隔。 資料行會在 `delta_` 間隔期間傳回每個統計資料中的變更。|
|||

這些 views 可以用來監視資源使用率，並以近乎即時的方式對資源競爭進行疑難排解。 主要和可讀取次要複本（包括異地複本）的使用者工作負載會分類到 `SloSharedPool1` 資源集區和 `UserPrimaryGroup.DBId[N]` 工作負載群組中，其中 `N` 代表資料庫識別碼值。

除了監視目前的資源使用率以外，使用密集集區的客戶也可以在個別的資料存放區中維護歷程記錄資源使用量資料。 這項資料可用於預測性分析，以根據歷程記錄和季節性趨勢主動管理資源使用率。

## <a name="operational-recommendations"></a>操作建議

**保留足夠的資源空間**。 如果發生資源爭用和效能降低的情況，風險降低可能牽涉到將一些資料庫移出受影響的彈性集區，或相應增加集區（如先前所述）。 不過，這些動作需要額外的計算資源才能完成。 特別是，對於 Premium 和商務關鍵集區，這些動作需要針對要移動的資料庫，或針對彈性集區中的所有資料庫（如果已相應增加）來傳送所有資料。 資料傳輸是長時間執行且需要大量資源的作業。 如果集區已在高資源壓力之下，則緩和作業本身將會降低效能。 在極端情況下，可能無法透過資料庫移動或集區相應增加來解決資源爭用，因為所需的資源無法使用。 在此情況下，暫時減少受影響彈性集區上的查詢工作負載可能是唯一的解決方案。

使用密集集區的客戶應該仔細監視先前所述的資源使用率趨勢，並在計量保留在建議範圍內，且彈性集區中仍有足夠的資源時，採取緩和措施。

資源使用率取決於每個資料庫和每個彈性集區會隨著時間而變更的多個因素。 在密集集區中達到最佳的價格/效能比例需要持續監視和重新平衡，將資料庫從更充分利用的集區移至較少使用的集區，並視需要建立新的集區來配合增加的工作負載。

**請勿移動「熱」資料庫**。 如果集區層級的資源爭用主要是由少量高度使用的資料庫所造成，則將這些資料庫移至較低使用量的集區，或將它們設為獨立資料庫可能會很有吸引力。 不過，不建議在資料庫保持高度使用的情況下執行這項操作，因為移動作業會進一步降低效能，這兩者都是針對要移動的資料庫和整個集區進行。 相反地，請等到 [高使用率] contosoconcerthall，或移動較少使用的資料庫，以減輕集區層級的資源壓力。 但是，在這種情況下移動具有極低使用率的資料庫並不會提供任何好處，因為它不會在集區層級上大幅降低資源使用率。

**在「隔離」集區中建立新的資料庫**。 在經常建立新資料庫的情況下（例如，使用每個資料庫的租使用者模型的應用程式），將新的資料庫放入現有的彈性集區會導致意外耗用大量資源，並影響集區中的其他資料庫和內部進程。 若要降低此風險，請建立個別的「隔離」集區，並配置足夠的資源。 針對新的資料庫使用此集區，但資源耗用量模式不明。 一旦資料庫在此集區中保持為商務週期（例如一周或一個月），且其資源耗用量已知，就可以移至具有足夠容量的集區，以容納這項額外的資源使用量。

**避免過度密集的伺服器**。 Azure SQL Database[支援](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)每部伺服器最多5000個資料庫。 使用具有數千個資料庫的彈性集區的客戶可以考慮將多個彈性集區放在單一伺服器上，並將資料庫總數設定為支援的限制。 不過，具有數千個資料庫的伺服器會建立營運挑戰。 需要列舉伺服器上所有資料庫的作業（例如，在入口網站中查看資料庫）將會變慢。 操作錯誤（例如不正確的伺服器層級登入或防火牆規則修改）將會影響更多的資料庫。 意外刪除伺服器需要協助 Microsoft 支援服務復原已刪除伺服器上的資料庫，而且會導致所有受影響的資料庫發生長時間中斷。

建議您將每個伺服器的資料庫數目限制為小於支援的最大值。 在許多情況下，每個伺服器最多使用1000-2000 個資料庫是最佳做法。 若要降低意外刪除伺服器的可能性，建議您在伺服器或其資源群組上放置[刪除鎖定](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。

在過去，牽涉到在相同伺服器上的彈性集區中移動資料庫的特定案例，速度比在伺服器之間移動資料庫更快。 目前，所有資料庫移動都是以相同的速度執行，不論來源和目的地伺服器為何。

## <a name="examples"></a>範例

### <a name="monitoring-memory-utilization"></a>監視記憶體使用量

此查詢會計算 `oom_per_second` 每個資源集區在過去32分鐘的度量。 此查詢可以在彈性集區中的任何資料庫中執行。

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>監視 `tempdb` 記錄檔空間使用量

此查詢會傳回度量的目前值 `tempdb_log_used_percent` ，顯示 `tempdb` 相對於其允許大小上限的交易記錄檔的相對使用量。 此查詢可以在彈性集區中的任何資料庫中執行。

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>後續步驟

- 如需彈性集區的簡介，請參閱彈性集區[可協助您管理和擴充 Azure SQL Database 中的多個資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)。
- 如需微調查詢工作負載以減少資源使用率的詳細資訊，請參閱[監視和微調]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)，以及[監視和效能調整](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)。
