---
title: 分析工作負載
description: 在 Azure 同步分析中分析工作負荷的查詢優先順序的技術。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a382ef2d93f10e69569ecbbed1399f256a7afbb3
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351209"
---
# <a name="analyze-your-workload-in-azure-synapse-analytics"></a>在 Azure 突觸分析中分析工作負荷

在 Azure 同步分析中分析 SQL 分析工作負荷的技術。

## <a name="resource-classes"></a>資源課程

SQL 分析提供資源類，用於將系統資源配置給查詢。  有關資源類的詳細資訊，請參閱[資源類&工作負載管理](resource-classes-for-workload-management.md)。  如果分配給查詢的資源類需要的資源比當前可用資源多，則查詢將等待。

## <a name="queued-query-detection-and-other-dmvs"></a>已排入佇列的查詢偵測和其他 DMV

您可以使用 `sys.dm_pdw_exec_requests` DMV，來識別正在並行存取佇列中等候的查詢。 正在等待並行存取插槽的查詢狀態為**暫止**。

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

工作負載管理角色可以使用 `sys.database_principals`來檢視。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

下列查詢會顯示每位使用者指派給哪些角色。

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL 分析具有以下等待類型：

* **LocalQueriesConcurrencyResourceType**：位於並行存取插槽架構外部的查詢。 DMV 查詢及 `SELECT @@VERSION` 這類的系統函數是本機查詢的範例。
* **UserConcurrencyResourceType**：位於並行存取插槽架構內部的查詢。 針對使用者資料表的查詢代表會使用此資源類型的範例。
* **DmsConcurrencyResourceType**：資料移動作業所產生的等候。
* **BackupConcurrencyResourceType**：此等候指出正在備份資料庫。 此資源類型的最大值為 1。 如果在同一時間要求多個備份，其他備份會排入佇列。 通常，我們建議在連續快照之間至少 10 分鐘。 

`sys.dm_pdw_waits` DMV 可用來查看要求正在等待哪些資源。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV 顯示給定查詢的等待資訊。 資源等待時間測量等待提供資源的時間。 信號等待時間是基礎 SQL 伺服器將查詢安排到 CPU 所需的時間。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

您也可以使用 `sys.dm_pdw_resource_waits` DMV 來計算已授與的並行位置數量。

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV 可用於針對等候項目進行歷史趨勢分析。

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>後續步驟

有關管理資料庫使用者和安全的詳細資訊，請參閱 SQL [Analytics 中保護資料庫](sql-data-warehouse-overview-manage-security.md)。 如需較大資源類別如何改善叢集資料行存放區索引品質的詳細資訊，請參閱 [重建索引以提升區段品質](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)。
