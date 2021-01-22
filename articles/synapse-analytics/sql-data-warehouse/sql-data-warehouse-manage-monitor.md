---
title: 使用 Dmv 監視您專用的 SQL 集區工作負載
description: 瞭解如何使用 Dmv 監視 Azure Synapse Analytics 專用的 SQL 集區工作負載和查詢執行。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 62064eaae6aa7fb3438845170497035473227d30
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685203"
---
# <a name="monitor-your-azure-synapse-analytics-dedicated-sql-pool-workload-using-dmvs"></a>使用 Dmv 監視您 Azure Synapse Analytics 專用的 SQL 集區工作負載

本文說明如何使用動態管理檢視 (Dmv) 來監視您的工作負載，包括調查 SQL 集區中的查詢執行。

## <a name="permissions"></a>權限

若要查詢這篇文章中的 Dmv，您需要 **VIEW DATABASE STATE** 或 **CONTROL** 許可權。 通常，授與 **VIEW 資料庫狀態** 是較嚴格的許可權。

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>監視連接

資料倉儲的所有登入都會記錄至 [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。  這個 DMV 會包含最後 10,000 筆登入。  Session_id 是主索引鍵，並依序指派給每個新的登入。

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>監視查詢執行

在 SQL 集區上執行的所有查詢都會記錄至 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。  這個 DMV 會包含最後 10,000 筆執行的查詢。  Request_id 可唯一識別每筆查詢，而且是此 DMV 的主索引鍵。  Request_id 會依序指派給每筆新查詢，並加上 QID 代表查詢識別碼。  查詢此 DMV 來尋找指定的 session_id，即會顯示指定登入的所有查詢。

> [!NOTE]
> 預存程序會使用多個要求 ID。  要求 ID 是依序指派。

請遵循以下步驟來調查特定查詢的查詢執行計畫和時間。

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>步驟 1︰識別您想要調查的查詢

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

從前述的查詢結果中，記下您想要調查之查詢的 **要求 ID** 。

處於 **暫停** 狀態的查詢可能因為大量使用中的執行中查詢而排入佇列。 這些查詢也會出現在 UserConcurrencyResourceType 類型的 [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 等候查詢中。 如需並行限制的詳細資訊，請參閱 [記憶體和並行限制](memory-concurrency-limits.md) ，或 [工作負載管理的資源類別](resource-classes-for-workload-management.md)。 查詢也會因其他原因 (例如物件鎖定) 而等候。  如果您的查詢正在等候資源，請參閱本文稍後的[檢查正在等候資源的查詢](#monitor-waiting-queries)。

若要簡化 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 資料表中查詢的查閱，請使用 [LABEL](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 將批註指派給您的查詢，您可以在 sys.dm_pdw_exec_requests 視圖中查閱。

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>步驟 2︰ 調查查詢計劃

使用要求識別碼，從[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)取得查詢的分散式 SQL (DSQL) 方案

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

當 DSQL 計劃所花的時間超出預期時，有可能是含有許多 DSQL 步驟的複雜計劃所導致，或只是某個步驟需要長時間處理。  如果計劃是含有數個移動作業的許多步驟，請考慮最佳化您的資料表散發以減少資料移動。 [資料表散發](sql-data-warehouse-tables-distribute.md)文章說明為何必須移動資料才能解決查詢。 本文也會說明將資料移動降至最低的一些散發策略。

進一步調查單一步驟 (長時間執行查詢步驟的 *operation_type* 資料行) 的詳細資料，並且記下 **步驟索引**：

* 繼續進行 **SQL 作業** 的步驟3： OnOperation、RemoteOperation、ReturnOperation。
* 繼續進行 **資料移動作業** 的步驟4： ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation。

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>步驟3：調查分散式資料庫上的 SQL

使用要求識別碼及步驟索引，從 [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 擷取詳細資料，其中包含所有分散式資料庫上查詢步驟的執行資訊。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

當查詢步驟正在執行時，可以使用 [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 針對正在特定散發上執行的步驟，從 SQL Server 計畫快取擷取 SQL Server 預估計畫。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>步驟4：調查分散式資料庫上的資料移動

使用要求識別碼和步驟索引，從 [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 擷取在每個散發上執行之資料移動步驟的相關資訊。

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* 檢查 *total_elapsed_time* 資料行，查看是否有特定散發，在資料移動上比其他散發用了更多時間。
* 如果是長時間執行的散發，請檢查 *rows_processed* 資料行，查看從該散發移動的資料列數是否遠多過其他散發。 若是如此，這個結果可能表示基礎資料的扭曲。 資料扭曲的其中一個原因是在具有許多 Null 值的資料行上散發 (其資料列將全都落在相同的散發) 中。 避免在這些類型的資料行上散發，或篩選您的查詢以在可能的情況下排除 Null，以防止查詢變慢。 

如果查詢正在執行，您可以使用 [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ，從特定散發內目前執行中 SQL 步驟的 SQL Server 計畫快取中，取得 SQL Server 的預估計畫。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>監視等候中的查詢

如果您發現您的查詢因為正在等候資源而沒有進度，以下查詢可顯示查詢正在等候的所有資源。

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

如果查詢正在主動等候另一個查詢的資源，則狀態會是 **AcquireResources**。  如果查詢具有全部的所需資源，則狀態會是 **Granted**。

## <a name="monitor-tempdb"></a>監視 tempdb

Tempdb 可用來保存查詢執行期間的中繼結果。 Tempdb 資料庫的高使用率可能會導致查詢效能變慢。 針對每個設定的 DW100c，會配置 399 GB 的 tempdb 空間 (DW1000c 會有 3.99 TB 的總 tempdb 空間) 。  以下是監視 tempdb 使用方式的秘訣，以及減少查詢中 tempdb 使用量的秘訣。

### <a name="monitoring-tempdb-with-views"></a>使用 views 監視 tempdb

若要監視 tempdb 使用方式，請先從適用于[sql 集區的 Microsoft 工具](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring)組安裝[microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) view。 然後，您可以執行下列查詢，以查看所有已執行查詢的每個節點 tempdb 使用量：

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

如果您的查詢正在耗用大量的記憶體，或收到與 tempdb 配置相關的錯誤訊息，可能是因為 [SELECT (CTAS) ](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 或在最後的資料移動作業中執行的 [插入 select](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 語句失敗所造成的 CREATE TABLE。 這通常可在分散式查詢計畫中識別為最後一個 INSERT SELECT 之前的 ShuffleMove 作業。  使用 [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 來監視 ShuffleMove 作業。

最常見的緩和措施是將您的 CTAS 或 INSERT SELECT 語句分割成多個 load 語句，如此一來，資料磁片區將不會超過每節點 tempdb 限制的1TB。 您也可以將叢集調整為較大的大小，以將 tempdb 大小分散到更多節點，以減少每個個別節點上的 tempdb。

除了 CTAS 和 INSERT SELECT 語句，以足夠記憶體執行的大型、複雜查詢可能會溢出到 tempdb，導致查詢失敗。  請考慮以較大的 [資源類別](resource-classes-for-workload-management.md) 執行，以避免溢出到 tempdb。

## <a name="monitor-memory"></a>監視記憶體

記憶體是效能緩慢及記憶體不足問題的根本原因。 如果您在查詢執行期間發現 SQL Server 記憶體使用量達到其上限，請考慮調整您的資料倉儲。

下列查詢會傳回每個節點的 SQL Server 記憶體使用量和記憶體不足壓力：

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>監視交易記錄大小

下列查詢會傳回每個發佈上的交易記錄大小。 如果其中一個記錄檔達到 160 GB，您應該考慮將您的執行個體相應放大或限制您交易的大小。

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>監視交易記錄復原

如果您的查詢失敗或需要長時間才能繼續，您可以檢查及監視是否有任何交易復原。

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>監視 PolyBase 負載

下列查詢會針對負載的進度提供大約的估計。 此查詢只會顯示目前正在處理的檔案。

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>下一步

如需 DMV 的詳細資訊，請參閱[系統檢視](../sql/reference-tsql-system-views.md)。
