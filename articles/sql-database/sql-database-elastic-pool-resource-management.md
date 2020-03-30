---
title: 密集彈性池中的資源管理 |微軟文檔
description: 管理具有許多資料庫的 Azure SQL 彈性池中的計算資源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473722"
---
# <a name="resource-management-in-dense-elastic-pools"></a>密集彈性集區中的資源管理

Azure SQL 資料庫[彈性池](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)是一種經濟高效的解決方案，用於管理資源使用不同的許多資料庫。 彈性池中的所有資料庫共用相同的資源配置，如 CPU、記憶體、工作執行緒、存儲空間、tempdb，前提是**只有池中的資料庫子集將在任何給定時間使用計算資源**。 此假設允許彈性池具有成本效益。 客戶無需為每個資料庫可能可能需要的所有資源付費，而是為池中所有資料庫之間共用的少量資源付費。

## <a name="resource-governance"></a>資源管理

資源分享要求系統仔細控制資源使用方式，以儘量減少"嘈雜鄰居"效應，其中資源消耗高的資料庫會影響同一彈性池中的其他資料庫。 同時，系統必須為高可用性和災害復原 （HADR）、備份和恢復、監視、查詢存儲、自動調優等功能提供足夠的資源，才能可靠地運行。

Azure SQL 資料庫通過使用多個資源治理機制實現這些目標，包括用於進程級資源的治理的 Windows[工作物件](https://docs.microsoft.com/windows/win32/procthread/job-objects)、用於存儲配額管理的 Windows[檔案伺服器資源管理員 （FSRM）](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview)以及用於在 Azure SQL 資料庫中運行的每個 SQL Server 實例中實現資源治理的修訂和擴展版本的 SQL Server[資源管理器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)。

彈性池的主要設計目標是具有成本效益。 因此，系統有意允許客戶創建_密集的_池，即資料庫數接近或達到允許的最大池，但計算資源配置適中。 出於同樣的原因，系統不為其內部進程保留所有可能需要的資源，但允許內部進程和使用者工作負載之間的資源分享。

這種方法允許客戶使用密集的彈性池，以實現足夠的性能和重大成本節約。 但是，如果密集池中許多資料庫的工作負載足夠密集，則資源爭用將變得十分嚴重。 資源爭用會會降低使用者工作負載性能，並可能對內部進程產生負面影響。

> [!IMPORTANT]
> 在具有許多活動資料庫的密集池中，將池中的資料庫數量增加到[DTU](sql-database-dtu-resource-limits-elastic-pools.md)和[vCore](sql-database-vcore-resource-limits-elastic-pools.md)彈性池記錄的最大資料庫數可能不可行。
> 
> 放置在密集池中而不引起資源爭用和性能問題的資料庫數取決於併發活動資料庫的數量以及每個資料庫中使用者工作負載的資源消耗。 此數位可能會隨著使用者工作負載的變化而變化。

當資源爭用發生在密集擁擠的池中時，客戶可以選擇以下一個或多個操作來緩解這種情況：
- 調整查詢工作負載以減少資源消耗，或隨著時間的推移跨多個資料庫分散資源消耗。
- 通過將某些資料庫移動到其他池，或者使它們成為獨立資料庫來降低池密度。
- 向上擴展池以獲取更多資源。

有關如何實現最後兩個操作的建議，請參閱本文後面的[操作建議](#operational-recommendations)。 減少資源爭用既有利於使用者工作負載和內部進程，也使系統能夠可靠地保持預期的服務等級。

## <a name="monitoring-resource-consumption"></a>監視資源消耗

為了避免資源爭用導致性能下降，使用密集彈性池的客戶應主動監視資源消耗，並在資源爭用增加時及時採取行動，從而影響工作負載。 持續監視很重要，因為由於使用者工作負荷的變化、資料卷和分佈的變化、池密度的變化以及 Azure SQL 資料庫服務的變化，池中的資源使用量會隨時間而變化。 

Azure SQL 資料庫提供了與此類監視相關的幾個指標。 超過每個指標的建議平均值表示池中的資源爭用，應使用前面提到的操作之一進行解決。

|度量名稱|描述|推薦平均值| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|與彈性池關聯的 SQL Server 進程的 CPU 利用率，由基礎作業系統衡量。 在每個資料庫中的[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)視圖中以及`master`資料庫中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖中可用。 此指標也會發送到 Azure 監視器，其中[命名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`sqlserver_process_core_percent`，可以在 Azure 門戶中查看。 此值對於同一彈性池中的每個資料庫都相同。|低於70%。 偶爾短峰值高達 90% 可能是可以接受的。|
|`max_worker_percent`|[輔助執行緒]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide)利用率。 為池中的每個資料庫以及池本身提供。 資料庫級別和池級別對輔助執行緒的數量有不同的限制，因此建議在這兩個級別監視此指標。 在每個資料庫中的[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)視圖中以及`master`資料庫中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖中可用。 此指標也會發送到 Azure 監視器，其中[命名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`workers_percent`，可以在 Azure 門戶中查看。|低於80%。 峰值高達 100% 將導致連接嘗試和查詢失敗。|
|`avg_data_io_percent`|讀取和寫入物理 IO 的 IOPS 利用率。 為池中的每個資料庫以及池本身提供。 資料庫級別和池級別對 IOPS 的數量有不同的限制，因此建議在這兩個級別監視此指標。 在每個資料庫中的[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)視圖中以及`master`資料庫中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖中可用。 此指標也會發送到 Azure 監視器，其中[命名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`physical_data_read_percent`，可以在 Azure 門戶中查看。|低於80%。 偶爾短峰值高達 100% 是可以接受的。|
|`avg_log_write_percent`|事務日誌寫入 IO 的輸送量利用率。 為池中的每個資料庫以及池本身提供。 資料庫級別和池級別的日誌輸送量存在不同的限制，因此建議在這兩個級別監視此指標。 在每個資料庫中的[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)視圖中以及`master`資料庫中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖中可用。 此指標也會發送到 Azure 監視器，其中[命名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`log_write_percent`，可以在 Azure 門戶中查看。 當此指標接近 100% 時，所有資料庫修改（插入、更新、刪除、合併語句、選擇 ... 進、裝插入等）會變慢。|低於90%。 偶爾短峰值高達 100% 是可以接受的。|
|`oom_per_second`|彈性池中記憶體不足 （OOM） 錯誤的速度，這是記憶體壓力的指示器。 可在[dm_resource_governor_resource_pools_history_ex 視圖中](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current)使用。 有關依例查詢的示例，請參閱[示例](#examples)查詢以計算此指標。|0|
|`avg_storage_percent`|彈性池級別的存儲空間利用率。 在`master`資料庫中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)視圖中可用。 此指標也會發送到 Azure 監視器，其中[命名為](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`storage_percent`，可以在 Azure 門戶中查看。|低於80%。 對於沒有資料增長的池，可以接近 100%。|
|`tempdb_log_used_percent`|`tempdb`資料庫中的事務日誌空間利用率。 儘管在一個資料庫中創建的臨時物件在同一彈性池中的其他資料庫中不可見，`tempdb`但是同一池中所有資料庫的共用資源。 從池中的一個資料庫啟動`tempdb`的長運行或空閒事務可能會佔用大部分事務日誌，並導致同一池中其他資料庫中的查詢失敗。 可在[系統dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql)視圖中。 此指標也會發送到 Azure 監視器，並且可以在 Azure 門戶中查看。 有關依例查詢的示例，請參閱[示例](#examples)查詢以返回此指標的當前值。|低於50%。 偶爾的峰值高達 80% 是可以接受的。|
|||

除了這些指標之外，Azure SQL 資料庫還提供一個視圖，該視圖返回實際資源治理限制，以及返回資源池級別和工作負載組級別的資源利用率統計資訊的其他視圖。

|檢視表名稱|描述|  
|-----------------|--------------------------------|  
|[系統dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|返回當前資料庫或彈性池中資源治理機制使用的實際配置和容量設置。|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|返回有關當前資源池狀態、資源池當前配置和累積資源池統計資訊的資訊。|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|返回累積工作負載組統計資訊和工作負載組的當前配置。 此視圖可以與`pool_id`列上的 sys.dm_resource_governor_resource_pools聯接，以獲取有關資源池資訊的資訊。|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|返回過去 32 分鐘的資源池利用率統計資訊。 每行表示 20 秒間隔。 列`delta_`在間隔內返回每個統計值中的更改。|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|返回過去 32 分鐘的工作負載組利用率統計資訊。 每行表示 20 秒間隔。 列`delta_`在間隔內返回每個統計值中的更改。|
|||

這些視圖可用於監視資源利用率，並近乎即時地排除資源爭用問題。 主副本和可讀輔助副本（包括地理副本）上的使用者工作負荷被分類到`SloSharedPool1`資源池和`UserPrimaryGroup.DBId[N]`工作負載組中，其中`N`代表資料庫 ID 值。

除了監視當前資源利用率外，使用密集池的客戶還可以在單獨的資料存儲中維護歷史資源利用率資料。 這些資料可用於預測分析，以便根據歷史和季節趨勢主動管理資源利用率。

## <a name="operational-recommendations"></a>業務建議

**留出足夠的資源空間**。 如果發生資源爭用和性能下降，緩解可能涉及將某些資料庫移出受影響的彈性池，或向上擴展池，如前面所述。 但是，這些操作需要額外的計算資源才能完成。 特別是，對於高級池和業務關鍵型池，這些操作需要傳輸要移動的資料庫的所有資料，或者如果擴展池，則傳輸彈性池中的所有資料庫的所有資料。 資料傳輸是一項長時間運行且資源密集型的操作。 如果池已經處於高資源壓力下，緩解操作本身將進一步降低性能。 在極端情況下，可能無法通過資料庫移動或池擴展來解決資源爭用，因為所需的資源不可用。 在這種情況下，暫時減少受影響的彈性池上的查詢工作負載可能是唯一的解決方案。

使用密集池的客戶應如前面所述，密切監視資源利用率趨勢，並在指標保持在建議範圍內且彈性池中仍有足夠資源時採取緩解措施。

資源利用率取決於每個資料庫和每個彈性池隨時間變化的多個因素。 在密集池中實現最佳性價比需要持續監控和再平衡，即將資料庫從利用率更高的池遷移到利用率較低的池，並根據需要創建新池，以適應增加的工作負載。

**不要移動"熱"資料庫**。 如果池級別的資源爭用主要是由少數高度利用的資料庫引起的，則將這些資料庫移動到利用率較低的池或使它們成為獨立資料庫可能很誘人。 但是，不建議在資料庫仍然高度使用時執行此操作，因為移動操作將進一步降低移動資料庫和整個池的性能。 相反，請等到高利用率降低，或者移動利用率較低的資料庫，以緩解池級別的資源壓力。 但是，在這種情況下，移動利用率極低的資料庫不會帶來任何好處，因為它不會實質性地降低池級別的資源利用率。

**在"隔離"池中創建新資料庫**。 在頻繁創建新資料庫（例如使用每個資料庫的租戶應用程式）的情況下，現有彈性池中的新資料庫可能會意外消耗大量資源並影響其他資料庫和池中的內部進程。 為了減輕這種風險，創建一個單獨的"隔離"池，並分配充足的資源。 對於具有未知資源消耗模式的新資料庫，使用此池。 一旦資料庫在此池中停留了一段時間（例如一周或一個月）並且其資源消耗已知，就可以將其移動到具有足夠容量的池中，以適應此額外的資源使用方式。

**避免邏輯伺服器過於密集**。 Azure SQL 資料庫[支援](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)每個邏輯伺服器最多 5000 個資料庫。 使用具有數千個資料庫的彈性池的客戶可以考慮在單個伺服器上放置多個彈性池，資料庫總數達到支援的限制。 但是，具有數千個資料庫的邏輯伺服器會帶來操作挑戰。 需要枚舉伺服器上的所有資料庫的操作（例如查看門戶中的資料庫）將較慢。 操作錯誤（如伺服器級登錄名或防火牆規則的不正確修改）將影響大量資料庫。 意外刪除邏輯伺服器需要 Microsoft 支援的説明來恢復已刪除的伺服器上的資料庫，並且將導致所有受影響的資料庫長時間中斷。

我們建議將每個邏輯伺服器的資料庫數限制為低於支援的最大資料庫數。 在許多情況下，每個伺服器最多使用 1000-2000 個資料庫是最佳選擇。 為了減少意外刪除伺服器的可能性，我們建議在邏輯伺服器或其資源組上放置[刪除鎖](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。

過去，某些涉及在同一邏輯伺服器上的彈性池中、外或之間的彈性池中移動資料庫的情況比在邏輯伺服器之間移動資料庫時要快。 目前，無論源伺服器和目標邏輯伺服器如何，所有資料庫移動都以相同的速度執行。

## <a name="examples"></a>範例

### <a name="monitoring-memory-utilization"></a>監視記憶體利用率

此查詢計算每個資源`oom_per_second`池的指標，在過去 32 分鐘中。 此查詢可以在彈性池中的任何資料庫中執行。

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>監視`tempdb`日誌空間利用率

此查詢返回指標的`tempdb_log_used_percent`當前值。 此查詢可以在彈性池中的任何資料庫中執行。

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>後續步驟

* 有關彈性池的簡介，請參閱[彈性池可説明您管理和擴展多個 Azure SQL 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)。
* 有關調整查詢工作負載以減少資源利用率的資訊，請參閱[監視和調優]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)以及[監視和性能調優](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)。
