---
title: 密集彈性集區中的資源管理
description: 使用許多資料庫來管理 Azure SQL Database 彈性集區中的計算資源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 9674c319cfd51726218e2c6a20197ea15d0ee683
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330718"
---
# <a name="resource-management-in-dense-elastic-pools"></a>密集彈性集區中的資源管理
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database [彈性](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) 集區是一種符合成本效益的解決方案，可用於管理具有不同資源使用量的許多資料庫。 彈性集區中的所有資料庫都會共用相同的資源配置（例如 CPU、記憶體、背景工作執行緒、儲存空間、tempdb），假設 **集區中的資料庫子集只會在任何指定時間使用計算資源**。 這種假設可讓彈性集區符合成本效益。 客戶不需要支付每個個別資料庫可能需要的所有資源，而是在集區中的所有資料庫之間共用一組較小的資源。

## <a name="resource-governance"></a>資源管理

資源分享需要系統謹慎地控制資源使用情形，以將「雜訊鄰近」效果降到最低，其中具有高資源耗用量的資料庫會影響相同彈性集區中的其他資料庫。 同時，系統必須針對高可用性和嚴重損壞修復 (HADR) 、備份與還原、監視、查詢存放區、自動調整等功能提供足夠的資源，才能可靠地運作。

Azure SQL Database 藉由使用多個資源治理機制（包括進程層級資源管理的 Windows [工作物件](https://docs.microsoft.com/windows/win32/procthread/job-objects) ）、Windows [檔案伺服器 Resource Manager (FSRM) ](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) 進行儲存配額管理，以及修改和延伸版本的 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 在 SQL Database 中執行資源管理，來達成這些目標。

彈性集區的主要設計目標是符合成本效益。 基於這個理由，系統刻意讓客戶建立 _密集_ 的集區，這是以接近或最大允許的資料庫數目為集區，但具有一般的計算資源配置。 基於相同的原因，系統不會保留其內部程式的所有可能需要的資源，但可讓內部進程和使用者工作負載之間的資源分享。

這種方法可讓客戶使用密集的彈性集區，以達成足夠的效能和主要成本節約。 但是，如果密集集區中許多資料庫的工作負載夠大，資源爭用就會變得很明顯。 資源爭用可減少使用者工作負載的效能，而且可能會對內部進程造成負面影響。

> [!IMPORTANT]
> 在具有許多作用中資料庫的密集集區中，將集區中的資料庫數目增加到針對 [DTU](resource-limits-dtu-elastic-pools.md) 和 [vCore](resource-limits-vcore-elastic-pools.md) 彈性集區記錄的最大值可能不可行。
>
> 可放置在密集集區而不會造成資源爭用和效能問題的資料庫數目，取決於同時使用中的資料庫數目，以及每個資料庫中使用者工作負載的資源耗用量。 當使用者工作負載變更時，此數目可能會隨著時間變更。

當密集封裝的集區中發生資源爭用時，客戶可以選擇下列一或多個動作來降低風險：

- 調整查詢工作負載，以降低資源耗用量，或在一段時間內跨多個資料庫散佈資源耗用量。
- 藉由將某些資料庫移至另一個集區，或將它們設為獨立資料庫，來降低集區密度。
- 擴大集區以取得更多資源。

如需如何執行最後兩個動作的建議，請參閱本文稍後的 [操作建議](#operational-recommendations) 。 減少資源爭用可同時享有使用者工作負載和內部程式，並可讓系統可靠地維護預期的服務層級。

## <a name="monitoring-resource-consumption"></a>監視資源耗用量

為了避免因資源爭用而造成效能降低，使用密集彈性集區的客戶應該主動監視資源耗用量，並在增加資源爭用開始影響工作負載時，及時採取動作。 持續監視很重要，因為集區中的資源使用量會隨著時間而改變，因為使用者工作負載的變更、資料磁片區和散發的變更、集區密度的變更，以及 Azure SQL Database 服務中的變更。

Azure SQL Database 提供與這種監視相關的數個計量。 超過每個計量的建議平均值表示集區中的資源爭用，而且應該使用先前所述的其中一個動作來解決。

|度量名稱|描述|建議的平均值|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|與彈性集區相關聯之 SQL 進程的 CPU 使用率（以基礎作業系統測量）。 可以在每個資料庫的 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 視圖，以及資料庫的 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 視圖中使用 `master` 。 此計量也會發出至 Azure 監視器，並將其 [命名](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)為 `sqlserver_process_core_percent` ，並可在 Azure 入口網站中查看。 相同彈性集區中的每個資料庫都有相同的值。|低於70%。 可能可以接受最多90% 的短暫尖峰。|
|`max_worker_percent`|[工作者執行緒]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) 使用率。 針對集區中的每個資料庫，以及集區本身提供。 在資料庫層級上，背景工作執行緒數目會有不同的限制，因此，建議您在這兩個層級監視此度量。 可以在每個資料庫的 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 視圖，以及資料庫的 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 視圖中使用 `master` 。 此計量也會發出至 Azure 監視器，並將其 [命名](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)為 `workers_percent` ，並可在 Azure 入口網站中查看。|低於80%。 最高100% 的尖峰會導致連接嘗試和查詢失敗。|
|`avg_data_io_percent`|讀取和寫入實體 IO 的 IOPS 使用率。 針對集區中的每個資料庫，以及集區本身提供。 資料庫層級的 IOPS 數目有不同的限制，因此在集區層級，建議您在這兩個層級監視此度量。 可以在每個資料庫的 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 視圖，以及資料庫的 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 視圖中使用 `master` 。 此計量也會發出至 Azure 監視器，並將其 [命名](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)為 `physical_data_read_percent` ，並可在 Azure 入口網站中查看。|低於80%。 可能可以接受最多100% 的短暫尖峰。|
|`avg_log_write_percent`|交易記錄寫入 IO 的輸送量使用率。 針對集區中的每個資料庫，以及集區本身提供。 資料庫層級的記錄輸送量有不同的限制，因此在集區層級，建議您在這兩個層級監視此度量。 可以在每個資料庫的 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 視圖，以及資料庫的 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 視圖中使用 `master` 。 此計量也會發出至 Azure 監視器，並將其 [命名](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)為 `log_write_percent` ，並可在 Azure 入口網站中查看。 當此計量接近100% 時，所有資料庫修改 (INSERT、UPDATE、DELETE、MERGE 語句，請選取 .。。 在中，BULK INSERT 等 ) 將會變慢。|低於90%。 可能可以接受最多100% 的短暫尖峰。|
|`oom_per_second`|記憶體不足 (在彈性集區中 OOM) 錯誤的速率，這是記憶體壓力的指標。 可在 [sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) 視圖中取得。 請 [參閱範例查詢範例，](#examples) 以計算此度量。|0|
|`avg_storage_percent`|彈性集區中所有資料庫的資料所使用的總儲存空間。 不包含資料庫檔案中的空白空間。 適用于資料庫中的 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 視圖 `master` 。 此計量也會發出至 Azure 監視器，並將其 [命名](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)為 `storage_percent` ，並可在 Azure 入口網站中查看。|低於80%。 針對沒有資料成長的集區，可採用100% 的方法。|
|`avg_allocated_storage_percent`|彈性集區中所有資料庫之儲存體中的資料庫檔案所使用的總儲存空間。 在資料庫檔案中包含空白空間。 適用于資料庫中的 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 視圖 `master` 。 此計量也會發出至 Azure 監視器，並將其 [命名](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)為 `allocated_data_storage_percent` ，並可在 Azure 入口網站中查看。|低於90%。 針對沒有資料成長的集區，可採用100% 的方法。|
|`tempdb_log_used_percent`|資料庫中的交易記錄空間使用量 `tempdb` 。 即使在某個資料庫中建立的暫存物件不會顯示在相同彈性集區中的其他資料庫中， `tempdb` 也是相同集區中所有資料庫的共用資源。 從集區中的一個資料庫開始，長時間執行或孤立的交易 `tempdb` 可能會耗用大量交易記錄，並導致相同集區中其他資料庫的查詢失敗。 衍生自 [sys.dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) 和 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) 的視圖。 此計量也會發出至 Azure 監視器，並可在 Azure 入口網站中查看。 請 [參閱範例查詢範例，](#examples) 以傳回此度量的目前值。|低於50%。 最高可接受80% 的尖峰。|
|||

除了這些計量之外，Azure SQL Database 還提供一個會傳回實際資源治理限制的視圖，以及會在資源集區層級和工作負載群組層級傳回資源使用量統計資料的額外視圖。

|檢視表名稱|描述|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|傳回目前資料庫或彈性集區中的資源治理機制所使用的實際設定和容量設定。|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|傳回目前資源集區狀態的相關資訊、資源集區的目前設定，以及累計資源集區統計資料。|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|傳回累計工作負載群組統計資料和目前的工作負載群組設定。 此視圖可以與資料行上的 sys.dm_resource_governor_resource_pools 聯結 `pool_id` ，以取得資源集區資訊。|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|傳回過去32分鐘的資源集區使用率統計資料。 每個資料列都代表20秒的間隔。 資料行會在 `delta_` 間隔期間傳回每個統計資料中的變更。|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|傳回過去32分鐘的工作負載群組使用率統計資料。 每個資料列都代表20秒的間隔。 資料行會在 `delta_` 間隔期間傳回每個統計資料中的變更。|
|||

這些視圖可以用來監視資源使用率，並以近乎即時的方式針對資源爭用進行疑難排解。 主要和可讀取次要複本上的使用者工作負載（包括地理複本）會分類為 `SloSharedPool1` 資源集區和 `UserPrimaryGroup.DBId[N]` 工作負載群組，其中 `N` 代表資料庫識別碼值。

除了監視目前的資源使用率之外，使用密集集區的客戶還可以在個別的資料存放區中維護歷程記錄資源使用量資料。 這項資料可用於預測性分析，以根據歷程記錄和季節性趨勢主動管理資源使用率。

## <a name="operational-recommendations"></a>操作建議

**保留足夠的資源空間**。 如果發生資源爭用和效能降低，風險降低可能牽涉到將一些資料庫移出受影響的彈性集區，或相應增加集區，如先前所述。 不過，這些動作需要額外的計算資源才能完成。 尤其是對於高階和業務關鍵集區，這些動作需要傳輸要移動之資料庫的所有資料，或如果集區已相應增加，則需要針對彈性集區中的所有資料庫傳輸所有資料。 資料傳輸是一項長時間執行且耗用大量資源的作業。 如果集區已低於高資源壓力，則緩和作業本身將會更進一步降低效能。 在極端情況下，可能無法透過資料庫移動或集區擴大來解決資源爭用，因為無法使用必要的資源。 在此情況下，暫時減少受影響之彈性集區上的查詢工作負載，可能是唯一的解決方案。

使用密集集區的客戶應該仔細監視資源使用量趨勢（如先前所述），並在計量維持在建議的範圍內，且彈性集區中仍有足夠的資源時，採取緩和措施。

資源使用率取決於每個資料庫和每個彈性集區一段時間會變更的多個因素。 在密集集區中達到最佳的價格/效能比例需要持續監視和重新平衡，也就是將資料庫從更多使用的集區移至較少使用的集區，並視需要建立新的集區以容納增加的

**請勿移動「熱」資料庫**。 如果集區層級的資源爭用主要是由少量高度使用的資料庫所造成，則將這些資料庫移至較低使用率的集區，或將它們設為獨立資料庫，可能會很有吸引力。 但是，不建議在資料庫維持高度使用的情況下執行此作業，因為移動作業將會進一步降低效能，這兩者適用于移動的資料庫，以及整個集區。 相反地，請等候高使用率消退，或改為移動較少使用的資料庫，以減輕集區層級的資源壓力。 但是在此情況下，使用非常低使用率來移動資料庫並不會提供任何好處，因為它不會大幅降低集區層級的資源使用率。

**在「隔離」集區中建立新的資料庫**。 在經常建立新資料庫的情況下（例如使用租使用者個別資料庫模型的應用程式），有一個新的資料庫放入現有的彈性集區時，可能會意外耗用大量資源，並影響集區中的其他資料庫和內部進程。 若要降低此風險，請建立具有充足資源配置的個別「隔離」集區。 針對新的資料庫使用此集區，但資源耗用量模式未知。 一旦資料庫維持在此集區中，以供商務週期使用，例如一周或一個月，而且已知其資源耗用量，就可以移至具有足夠容量的集區來容納此額外的資源使用量。

**監視已使用和**已配置的空間。 當配置的集區空間 (集區中所有資料庫的所有資料庫檔案大小總計，) 達到集區大小上限時，可能會發生空間不足的錯誤。 如果配置的空間趨勢偏高，且進度達到最大集區大小，緩和選項包括：
- 將部分資料庫移出集區，以減少配置的空間總計
- [壓縮](file-space-manage.md) 資料庫檔案以減少檔案中的空白配置空間
- 將集區擴大至具有較大集區大小的服務目標

如果使用集區空間 (集區中所有資料庫中的資料大小總計，不包括檔案中的空白空間) 趨勢，並已在追蹤以達到最大集區大小，緩和選項包括：
- 將部分資料庫移出集區，以減少已使用的空間總計
- 將 (封存) 資料移至資料庫外部，或刪除不再需要的資料
- 執行 [資料壓縮](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression)
- 將集區擴大至具有較大集區大小的服務目標

**避免過度密集的伺服器**。 Azure SQL Database [支援](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) 每個伺服器最多5000個資料庫。 使用具有上千個資料庫之彈性集區的客戶，可考慮將多個彈性集區放在單一伺服器上，並將資料庫總數限制在支援的限制內。 不過，具有數千個資料庫的伺服器會產生操作方面的挑戰。 需要列舉伺服器上所有資料庫的作業（例如，在入口網站中查看資料庫）將會較慢。 操作錯誤（例如不正確地修改伺服器層級登入或防火牆規則）將會影響大量的資料庫。 若意外刪除伺服器，將需要 Microsoft 支援服務的協助來復原已刪除伺服器上的資料庫，而且會導致所有受影響資料庫的長時間中斷。

建議您將每個伺服器的資料庫數目限制為低於支援的最大數目。 在許多案例中，每個伺服器最多使用1000-2000 個資料庫是最佳的。 若要降低意外刪除伺服器的可能性，建議您在伺服器或其資源群組上放置 [刪除鎖定](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) 。

在過去，牽涉到在相同伺服器上的彈性集區之間移動資料庫的某些案例，比在伺服器之間移動資料庫更快。 目前，不論來源和目的地伺服器為何，所有資料庫移動都會以相同的速度執行。

## <a name="examples"></a>範例

### <a name="monitoring-memory-utilization"></a>監視記憶體使用量

此查詢會計算 `oom_per_second` 過去32分鐘內每個資源集區的度量。 您可以在彈性集區中的任何資料庫中執行此查詢。

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

此查詢會傳回度量的目前值 `tempdb_log_used_percent` ，顯示 `tempdb` 相對於其允許大小上限的交易記錄相對使用率。 您可以在彈性集區中的任何資料庫中執行此查詢。

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

- 如需彈性集區的簡介，請參閱彈性集區可 [協助您在 Azure SQL Database 中管理及調整多個資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)。
- 如需微調查詢工作負載以降低資源使用率的詳細資訊，請參閱 [監視和微調]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)，以及 [監視和效能調整](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)。
