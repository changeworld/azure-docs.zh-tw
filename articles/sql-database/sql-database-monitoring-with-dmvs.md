---
title: 使用 Dmv 監視效能
description: 了解如何使用動態管理檢視監視 Microsoft Azure SQL Database 來偵測和診斷常見的效能問題。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 04/19/2020
ms.openlocfilehash: 6f33f49be74419a8f0cd31d973d64798f5d76a2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683009"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>使用動態管理檢視監視 Azure SQL Database 的效能

Microsoft Azure SQL Database 可使用動態管理檢視的子集來診斷可能因為封鎖或長時間執行的查詢、資源瓶頸、不佳的查詢計畫等等所造成的效能問題。 本主題提供有關如何使用動態管理檢視來偵測常見效能問題的資訊。

SQL Database 部分支援動態管理檢視的三個類別目錄：

- 資料庫相關的動態管理檢視。
- 執行相關的動態管理檢視。
- 交易相關的動態管理檢視。

如需動態管理檢視的詳細資訊，請參閱《SQL Server 線上叢書》中的 [動態管理檢視和函數 (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) 。

## <a name="permissions"></a>權限

在 SQL Database 中，查詢動態管理檢視需要 **VIEW DATABASE STATE** 權限。 **VIEW DATABASE STATE** 權限會傳回目前資料庫中所有物件的相關資訊。
若要授與 **VIEW DATABASE STATE** 權限給特定的資料庫使用者，請執行下列查詢：

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

在內部部署 SQL Server 的執行個體中，動態管理檢視會傳回伺服器狀態資訊。 在 SQL Database 中，僅會傳回與您目前的邏輯資料庫相關的資訊。

本文包含 DMV 查詢的集合，您可以使用 SQL Server Management Studio 或 Azure Data Studio 來執行，以偵測下列類型的查詢效能問題：

- [識別與過多 CPU 耗用量相關的查詢](#identify-cpu-performance-issues)
- [PAGELATCH_ * 和 WRITE_LOG 與 IO 瓶頸相關的等候](#identify-io-performance-issues)
- [PAGELATCH_ * 等候導致 bytTempDB 爭用](#identify-tempdb-performance-issues)
- [記憶體授與等候問題所造成的 RESOURCE_SEMAHPORE 等候](#identify-memory-grant-wait-performance-issues)
- [識別資料庫和物件大小](#calculating-database-and-objects-sizes)
- [正在抓取作用中會話的相關資訊](#monitoring-connections)
- [取得全系統和資料庫資源使用量資訊](#monitor-resource-use)
- [正在抓取查詢效能資訊](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>識別 CPU 效能問題

如果 CPU 耗用量長的時間高於 80%，請考慮下列疑難排解步驟：

### <a name="the-cpu-issue-is-occurring-now"></a>CPU 問題現在正在發生

如果正在發生問題，有兩個可能的案例：

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>許多累積耗用大量 CPU 的個別查詢

使用下列查詢識別常見的查詢雜湊：

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>耗用 CPU 的長時間執行的查詢仍在執行

使用下列查詢識別下列查詢：

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>發生在過去的 CPU 問題

如果問題發生在過去，而且您想要執行根本原因分析，請使用[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。 具有資料庫存取權的使用者可以使用 T-SQL 查詢「查詢存放區」資料。 「查詢存放區」預設組態使用 1 小時的細微性。 使用下列查詢查看耗用大量 CPU 查詢的活動。 此查詢會傳回前 15 個耗用 CPU 的查詢。 請務必變更 `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`：

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

一旦您識別有問題的查詢之後，就可以調整這些查詢，以減少 CPU 使用率。  如果您沒有時間調整查詢，也可以選擇升級資料庫的 SLO，以解決此問題。

## <a name="identify-io-performance-issues"></a>識別 IO 效能問題

識別 IO 效能問題時，與 IO 問題相關聯的常見等候類型為：

- `PAGEIOLATCH_*`

  資料檔案 IO 問題 (包括 `PAGEIOLATCH_SH`、`PAGEIOLATCH_EX`、`PAGEIOLATCH_UP`)。  如果等候類型名稱中有 **IO**，則指向 IO 問題。 如果在頁面閂鎖等候名稱中沒有任何 **IO**，則指向不同類型的問題 (例如，tempdb 爭用)。

- `WRITE_LOG`

  交易記錄 IO 問題。

### <a name="if-the-io-issue-is-occurring-right-now"></a>如果正在發生 IO 問題

使用 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 或 [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 查看 `wait_type` 和 `wait_time`。

#### <a name="identify-data-and-log-io-usage"></a>識別資料及記錄 IO 使用量

使用下列查詢識別資料及記錄 IO 使用量。 如果資料或記錄 IO 高於 80%，表示使用者已經將可用 IO 用於 SQL DB 服務層級。

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

如果已達到 IO 限制，您會有兩個選項：

- 選項 1：升級計算大小或服務層級
- 選項 2：識別並調整耗用最多 IO 的查詢。

#### <a name="view-buffer-related-io-using-the-query-store"></a>使用「查詢存放區」檢視與緩衝區相關的 IO

對於選項 2，您可以針對「查詢存放區」，將下列查詢用於與緩衝區相關的 IO，以檢視過去兩個小時追蹤的活動：

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>檢視 WRITELOG 等候的總記錄 IO

如果等候類型是 `WRITELOG`，使用下列查詢可依陳述式檢視總記錄 IO：

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>識別 `tempdb` 效能問題

識別 IO 效能問題時，與 `tempdb` 問題相關聯的常見等候類型為 `PAGELATCH_*` (而非 `PAGEIOLATCH_*`)。 不過，`PAGELATCH_*` 等候不一定表示您有 `tempdb` 爭用。  這個等候也表示您有使用者物件資料頁面爭用，因為針對相同的資料頁面進行並行要求。 若要進一步`tempdb`確認爭用，請使用[dm_exec_requests sys.databases](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)來確認 wait_resource 值的開頭是`2:x:y` ，其中 2 `tempdb`是資料庫識別碼， `x`是檔案識別碼，而`y`是頁面識別碼。  

對於 tempdb 爭用，常見的方法是減少或重新撰寫依賴 `tempdb` 的應用程式程式碼。  常見的 `tempdb` 使用區域包括：

- 暫存資料表
- 資料表變數
- 資料表值參數
- 版本存放區使用量 (特別是與長時間執行的交易相關聯)
- 具有使用排序、雜湊聯結和多工緩衝處理之查詢計劃的查詢

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>使用資料表變數和暫存資料表的常見查詢

使用下列查詢識別使用資料表變數和暫存資料表的常見查詢：

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>識別長時間執行的交易

使用下列查詢識別長時間執行的交易。 長時間執行的交易可防止版本存放區清除。

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>識別記憶體授與等候效能問題

如果您的常見等候類型為 `RESOURCE_SEMAHPORE`，而且沒有高 CPU 使用量問題，則可能有記憶體授與等候問題。

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>判斷 `RESOURCE_SEMAHPORE` 等候是否為常見等候

使用下列查詢判斷 `RESOURCE_SEMAHPORE` 等候是否為常見等候

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>識別高記憶體耗用陳述式

使用下列查詢識別高記憶體耗用陳述式：

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>識別前 10 個有效記憶體授與

使用下列查詢識別前 10 個有效記憶體授與：

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>計算資料庫和物件的大小

下列查詢會傳回資料庫的大小 (以 MB 為單位)：

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

下列查詢會傳回您資料庫中個別物件的大小 (以 MB 為單位)：

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>監視連線

您可以使用 [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) 檢視，擷取與特定 Azure SQL Database 伺服器建立之連接和每個連接之詳細資料的相關資訊。 此外，[sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) 檢視有助於擷取所有作用中使用者的連接資訊和內部工作。
下列查詢可擷取目前的連接資訊：

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> 執行 **sys.dm_exec_requests** 和 **sys.dm_exec_sessions views** 時，如果您具備資料庫的 **VIEW DATABASE STATE** 權限，您就會看到該資料庫上所有正在執行的工作階段；否則您只會看到目前的工作階段。

## <a name="monitor-resource-use"></a>監視資源使用量

您可以使用 [SQL Database 查詢效能深入解析](sql-database-query-performance.md)和[查詢存放區](https://msdn.microsoft.com/library/dn817826.aspx)來監視資源用量。

您也可以使用以下兩種檢視來監視用量：

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

您可以在每一個 SQL Database 中使用 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)檢視。 **sys.dm_db_resource_stats** 檢視可顯示相對於服務層級的最新資源使用量資料。 每隔 15 秒鐘就會記錄一次 CPU、資料 IO、記錄檔寫入和記憶體的平均百分比，並且會維持 1 小時。

因為此檢視會提供更細微的資源使用量資訊，請先使用 **sys.dm_db_resource_stats** 來進行任何現狀分析或疑難排解。 例如，下列查詢會顯示目前的資料庫在過去一小時的平均和最大資源使用量：

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

如需其他查詢的資訊，請參閱 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 中的範例。

### <a name="sysresource_stats"></a>sys.resource_stats

**master** 資料庫中的 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 檢視有其他資訊可協助您監視 SQL Database 在其特定服務層級和計算大小的效能。 這項資料每隔 5 分鐘就會收集一次，並且會維持大約 14 天。 這個檢視適合用於進行 SQL Database 如何使用資源的長期歷史分析。

下圖顯示在一週中 P2 計算大小之高階資料庫每小時的 CPU 資源使用量。 此圖從星期一開始，顯示 5 個工作天，然後顯示較少發生在應用程式的週末。

![SQL Database 的資源使用量](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

從資料中，這個資料庫目前相對於 P2 計算大小的尖峰 CPU 負載剛好超過 50% 的 CPU 使用量 (星期二中午)。 如果 CPU 是應用程式資源設定檔中的主要因素，您可能會決定 P2 是正確的計算大小，以確保工作負載永遠符合。 如果您預期應用程式會隨著時間成長，最好預留額外的資源緩衝，讓應用程式永遠不會達到效能等級限制。 如果您提升計算大小，則可協助避免資料庫沒有足夠能力來有效處理要求時可能發生的客戶可見錯誤，尤其是在延遲敏感的環境中。 其中一例便是支援可根據資料庫呼叫結果繪製網頁之應用程式的資料庫。

其他應用程式類型可能會以不同方式解譯相同的圖形。 例如，如果應用程式嘗試每天處理薪資資料而且具有同一張圖表，這種「批次作業」模型可能會在 P1 計算大小中正常執行。 P1 計算大小有 100 個 DTU，相較之下，P2 計算大小則有 200 個 DTU。 P1 計算大小提供的效能是 P2 計算大小的一半。 因此，P2 中 50% 的 CPU 使用量等於 P1 中 100% 的 CPU 使用量。 如果應用程式沒有逾時，就算作業花了 2 個小時或 2.5 個小時才能完成也沒關係，只要它在今天內完成即可。 這個類別中的應用程式或許可以使用 P1 計算大小。 一天中有好幾個時段的資源使用量較低，您可以善用這個事實，讓任何「巨量尖峰」可以溢出到當天稍後的一個低谷。 只要作業可以每天及時完成，P1 計算大小可能就很適合這類應用程式 (並節省經費)。

Azure SQL Database 會在每個伺服器 **master** 資料庫的 **sys.resource_stats** 檢視中公開每個作用中資料庫的耗用資源資訊。 資料表中的資料會以 5 分鐘的間隔彙總。 利用基本、標準和進階服務層級，資料可能要花 5 分鐘以上的時間才會出現在資料表中，因此這項資料比較適合進行歷程記錄分析而不是近乎即時的分析。 查詢 **sys.resource_stats** 檢視可查看資料庫的近期歷程記錄，並驗證所選的保留是否會在必要時提供所需的效能。

> [!NOTE]
> 您必須連接到 SQL Database 伺服器的 **master** 資料庫，才能在下列範例中查詢 **sys.resource_stats**。

下列範例會示範如何公開此檢視中的資料：

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![sys.resource_stats 目錄檢視](./media/sql-database-performance-guidance/sys_resource_stats.png)

下列範例示範不同方式，以供您用來使用 **sys.resource_stats** 目錄檢視取得有關 SQL Database 如何使用資源的相關資訊：

1. 若要查看過去一周針對資料庫 userdb1 所使用的資源，您可以執行此查詢：

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. 若要評估您的工作負載與計算大小的符合程度，您必須鑽研資源度量的每個層面：CPU、讀取、寫入、背景工作角色數目和工作階段數目。 以下是使用 **sys.resource_stats** 修訂過的查詢，可報告這些資源度量的平均值和最大值：

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. 利用這項有關各資源度量平均值和最大值的資訊，您可以評估您的工作負載與您所選之計算大小的符合程度。 通常，來自 **sys.resource_stats** 的平均值可提供您對目標大小所使用的理想基準。 它應該是您主要的量尺。 例如，您可能使用標準服務層級搭配 S2 計算大小。 CPU 以及 IO 讀取和寫入的平均使用量百分比低於 40%，背景工作角色平均數目低於 50，而且工作階段平均數目低於 200。 您的工作負載可能符合 S1 計算大小。 要看到您的資料庫是否符合背景工作和工作階段限制範圍內非常容易。 若要查看資料庫在 CPU、讀取和寫入方面是否符合較低的計算大小，請將較低計算大小的 DTU 數目除以目前計算大小的 DTU 數目，然後將結果乘以 100：

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    此結果是以百分比表示之兩個計算大小的相對效能差異。 如果您的資源使用量未超過這個數量，您的工作負載可能符合較低的計算大小。 不過，您需要查看所有範圍的資源使用量值，並以百分比判斷資料庫工作負載符合較低計算大小的頻率。 下列查詢會根據我們在此範例中計算的 40% 臨界值，輸出每個資源維度的相符百分比：

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    根據資料庫服務層級，您可以決定工作負載是否符合較低的計算大小。 如果資料庫工作負載目標是 99.9%，且上述查詢針對三個資源維度傳回的值都大於 99.9，則工作負載可能會符合較低的計算大小。

    查看相符百分比也可讓您深入了解是否必須移到下一個較高的計算大小來滿足目標。 例如，userdb1 會顯示過去一週的下列 CPU 使用量：

   | 平均 CPU 百分比 | 最大 CPU 百分比 |
   | --- | --- |
   | 24.5 |100.00 |

    平均 CPU 大約是計算大小限制的四分之一，完全符合資料庫的計算大小。 不過，此最大值會顯示資料庫達到計算大小的限制。 您需要移至下一個較高的計算大小嗎？ 請查看工作負載達到 100% 的次數，然後將其與您的資料庫工作負載目標做比較。

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    如果此查詢針對三個資源維度傳回的值小於 99.9%，請考慮移到下一個較高的計算大小或使用應用程式微調技術減少 SQL Database 的負載。

4. 此練習也會考慮您預計的未來工作負載增加量。

若為彈性集區，您可以監視其中的個別資料庫，技巧如本節所述。 但您也可以監視集區整體。 如需詳細資訊，請參閱 [監視和管理彈性集區](sql-database-elastic-pool-manage-portal.md)。

### <a name="maximum-concurrent-requests"></a>並行要求數上限

若要查看並行要求數目，請在 SQL Database 上執行下列 Transact-SQL 查詢：

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

若要分析內部部署 SQL Server 資料庫的工作負載，請修改此查詢來篩選您要分析的特定資料庫。 比方說，如果您擁有名為 MyDatabase 的內部部署資料庫，則下列 Transact-SQL 查詢會傳回該資料庫中並行要求的計數：

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

這只是單一時間點的快照。 若要進一步了解您的工作負載和並行要求需求，您必須收集一段時間內的許多範例。

### <a name="maximum-concurrent-logins"></a>並行登入數上限

您可以分析您的使用者和應用程式模式以了解登入頻率。 您也可以在測試環境中執行真實世界的負載，藉此確定您不會達到我們在本文中討論的這項限制或其他限制。 沒有單一查詢或動態管理檢視（DMV）可顯示並行登入計數或歷程記錄。

如果這些用戶端使用相同的連接字串，服務仍會驗證每一個登入。 如果有 10 位使用者同時以相同的使用者名稱和密碼連接到資料庫，將會有 10 個並行登入。 這項限制只適用於登入和驗證期間。 如果相同的 10 位使用者依序連接到資料庫，並行登入數目絕對不會大於 1。

> [!NOTE]
> 這項限制目前不適用於彈性集區中的資料庫。

### <a name="maximum-sessions"></a>工作階段數上限

若要查看目前的作用中工作階段數目，請在 SQL Database 上執行下列 Transact-SQL 查詢：

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections
```

如果您要分析內部部署 SQL Server 的工作負載，請修改查詢以專注於特定資料庫。 如果您考慮將資料庫移至 Azure SQL Database，此查詢可協助您判斷該資料庫可能的工作階段需求。

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase'
```

同樣地，這些查詢傳回的是某一時間點的計數。 如果您在一段時間後收集多個範例，您將會充分瞭解會話的使用。

若要進行 SQL Database 分析，您可以查詢 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)檢視，並檢閱 **active_session_count** 資料行，以取得工作階段的歷史統計資料。

## <a name="monitoring-query-performance"></a>監視查詢效能

速度慢或長時間執行的查詢可能會耗用大量系統資源。 本節示範如何使用動態管理檢視偵測幾個常見的查詢效能問題。 在 Microsoft TechNet 上的文章 [疑難排解 SQL Server 2008 的效能問題](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) 雖然較舊，但仍是針對疑難排解相當有用的參考資料。

### <a name="finding-top-n-queries"></a>尋找前 N 個查詢

下列範例會傳回以平均 CPU 時間排名的前五個查詢的相關資訊。 此範例會根據查詢雜湊來彙總查詢，使在邏輯上等同的查詢依其累積資源耗用量進行分組。

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>監視封鎖的查詢

速度慢或長時間執行的查詢會造成過度的資源耗用，並且會導致封鎖查詢的後果。 導致封鎖的原因可能是不佳的應用程式設計、不良的查詢計畫、缺乏有用的索引等等。 您可以使用 sys.dm_tran_locks 檢視，取得在您的 Azure SQL Database 中目前正鎖定之活動的相關資訊。 如需範例程式碼，請參閱《SQL Server 線上叢書》中的 [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx)。

### <a name="monitoring-query-plans"></a>監視查詢計畫

效率不佳的查詢計畫也可能會增加 CPU 耗用量。 下列範例使用 [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) 檢視來判斷哪一個查詢使用最多的累計 CPU。

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>請參閱

[SQL Database 簡介](sql-database-technical-overview.md)
