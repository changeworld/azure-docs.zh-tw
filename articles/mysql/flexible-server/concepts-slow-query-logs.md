---
title: 慢速查詢記錄-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 描述適用於 MySQL 的 Azure 資料庫彈性伺服器中可用的慢速查詢記錄。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 512a103f5ac116f1f4eb18631cdc8e4a8b9380c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399205"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽) 的查詢記錄緩慢

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

在適用於 MySQL 的 Azure 資料庫彈性的伺服器中，慢速查詢記錄檔可供使用者設定和存取。 預設會停用慢速查詢記錄，並可加以啟用，以協助找出疑難排解期間的效能瓶頸。

如需 MySQL 慢速查詢記錄的詳細資訊，請參閱 MySQL 引擎檔中的 [慢速查詢記錄一節](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) 。

## <a name="configure-slow-query-logging"></a>設定慢速查詢記錄 
預設會停用慢速查詢記錄。 若要啟用記錄，請將 `slow_query_log` server 參數設定為 *ON*。 您可以使用 Azure 入口網站或 Azure CLI 來設定此設定。 <!-- add link to server parameter-->. 

您可以調整以控制慢速查詢記錄行為的其他參數包括：

- **long_query_time**：如果查詢所需的時間超過 `long_query_time` (（以秒為單位），) 完成記錄。 預設值是 10 秒。
- **log_slow_admin_statements**：判斷系統管理語句 (例如 `ALTER_TABLE``ANALYZE_TABLE`會記錄) 。
- **log_queries_not_using_indexes**：決定是否記錄未使用索引的查詢。
- **log_throttle_queries_not_using_indexes**：限制可寫入至慢速查詢記錄的非索引查詢數目。 當 `log_queries_not_using_indexes` 設定為*ON*時，此參數會生效

> [!IMPORTANT]
> 如果您的資料表未編制索引，將 `log_queries_not_using_indexes` 和 `log_throttle_queries_not_using_indexes` 參數設定為 [ **開啟** ] 可能會影響 MySQL 效能，因為針對這些非索引資料表執行的所有查詢都會寫入慢速查詢記錄檔。

如需慢速查詢記錄參數的完整描述，請參閱 MySQL [慢速查詢記錄文件](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="access-slow-query-logs"></a>存取慢速查詢記錄

慢速查詢記錄會與 Azure 監視器診斷設定整合。 在 MySQL 彈性伺服器上啟用慢速查詢記錄之後，您可以將它們發出至 Azure 監視器記錄、事件中樞或 Azure 儲存體。 若要深入瞭解診斷設定，請參閱 [診斷記錄檔](../../azure-monitor/platform/platform-logs-overview.md)。 若要深入瞭解如何啟用 Azure 入口網站中的診斷設定，請參閱 [慢速查詢記錄入口網站文章](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics)。

下表描述慢速查詢記錄的輸出。 視輸出方法而定，包含的欄位及其出現的順序可能有所不同。

| **屬性** | **說明** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 伺服器的名稱 |
| `start_time_t` UTC | 查詢開始時間 |
| `query_time_s` | 執行查詢所花費的總時間（秒） |
| `lock_time_s` | 鎖定查詢的總時間（秒） |
| `user_host_s` | 使用者名稱 |
| `rows_sent_s` | 傳送的資料列數目 |
| `rows_examined_s` | 檢查的資料列數目 |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | 插入識別碼 |
| `sql_text_s` | 完整查詢 |
| `server_id_s` | 伺服器的識別碼 |
| `thread_id_s` | 執行緒識別碼 |
| `\_ResourceId` | 資源 URI |

> [!Note]
> `sql_text_s`若為，則如果記錄超過2048個字元，則會予以截斷。

## <a name="analyze-logs-in-azure-monitor-logs"></a>在 Azure 監視器記錄中分析記錄

當您透過診斷記錄將慢速查詢記錄輸送至 Azure 監視器記錄之後，您就可以進一步分析慢速查詢。 以下是一些可協助您開始使用的範例查詢。 請務必以您的伺服器名稱更新下列。

- 特定伺服器上超過10秒的查詢

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- 列出特定伺服器上5個最長的查詢

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- 根據特定伺服器上的最小值、最大值、平均和標準差查詢時間來摘要查詢速度緩慢的查詢

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 在特定伺服器上繪製慢速查詢分布圖

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 在啟用診斷記錄的所有 MySQL 伺服器上顯示超過10秒的查詢

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>後續步驟
- 深入瞭解 [audit 記錄](concepts-audit-logs.md)
- 從[Azure 入口網站](how-to-configure-slow-query-logs-portal.md)設定慢速查詢記錄
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->