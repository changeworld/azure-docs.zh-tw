---
title: 慢速查詢記錄-適用於 MySQL 的 Azure 資料庫
description: 描述適用於 MySQL 的 Azure 資料庫中可用的慢速查詢記錄，以及啟用不同記錄層級的可用參數。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: 1e967c77bc41f0f91674fe55bc622adaf5046f6d
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358995"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫中的慢速查詢記錄
在適用於 MySQL 的 Azure 資料庫中，使用者可以使用慢速查詢記錄。 不支援存取交易記錄。 慢速查詢記錄檔可以用來找出效能瓶頸，以進行疑難排解。

如需 MySQL 慢速查詢記錄的詳細資訊，請參閱 MySQL 參考手冊的[慢速查詢記錄章節](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

在您的伺服器上啟用 [查詢存放區](concepts-query-store.md) 時，您可能會看到 " `CALL mysql.az_procedure_collect_wait_stats (900, 30);` " 記錄在您的慢速查詢記錄中的查詢。 這是預期的行為，因為查詢存放區功能會收集查詢的相關統計資料。 

## <a name="configure-slow-query-logging"></a>設定慢速查詢記錄 
預設會停用慢速查詢記錄。 若要啟用它，請將設定 `slow_query_log` 為 ON。 您可以使用 Azure 入口網站或 Azure CLI 來啟用此功能。 

您可以調整的其他參數包含：

- **long_query_time** ：如果查詢所需的時間比記錄該查詢的 long_query_time (秒) 還要久。 預設值是 10 秒。
- **log_slow_admin_statements** ：如果 ON 在寫入至 slow_query_log 的陳述式中包含 ALTER_TABLE 和 ANALYZE_TABLE 這類管理陳述式。
- **log_queries_not_using_indexes** ：決定是否將未使用索引的查詢記錄至 slow_query_log
- **log_throttle_queries_not_using_indexes** ：這個參數會限制可寫入至慢速查詢記錄的非索引查詢次數。 log_queries_not_using_indexes 設為 ON 時，這個參數會生效。
- **log_output** ：如果是 "File"，則會允許將慢速查詢記錄寫入本機伺服器儲存體和 Azure 監視器診斷記錄。 如果為「無」，則慢速查詢記錄只會寫入 Azure 監視器診斷記錄。 

> [!IMPORTANT]
> 如果您的資料表未編制索引，將 `log_queries_not_using_indexes` 和 `log_throttle_queries_not_using_indexes` 參數設定為 [開啟] 可能會影響 MySQL 效能，因為針對這些非索引資料表執行的所有查詢都會寫入慢速查詢記錄檔。<br><br>
> 如果您打算記錄長時間的查詢速度緩慢的查詢，建議您將設定為「 `log_output` 無」。 如果設定為 "File"，這些記錄會寫入本機伺服器儲存體，而且可能會影響 MySQL 效能。 

如需慢速查詢記錄參數的完整描述，請參閱 MySQL [慢速查詢記錄文件](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="access-slow-query-logs"></a>存取慢速查詢記錄
有兩個選項可用於存取適用於 MySQL 的 Azure 資料庫中的慢速查詢記錄：本機伺服器儲存體或 Azure 監視器診斷記錄。 這是使用參數進行設定 `log_output` 。

若為本機伺服器儲存，您可以使用 Azure 入口網站或 Azure CLI 來列出和下載慢速查詢記錄。 在 Azure 入口網站中，流覽至您在 Azure 入口網站中的伺服器。 在 [監視] 標題下方，選取 [伺服器記錄] 頁面。 如需 Azure CLI 的詳細資訊，請參閱 [使用 Azure CLI 設定和存取慢速查詢記錄](howto-configure-server-logs-in-cli.md)。 

Azure 監視器診斷記錄可讓您透過管線將慢速查詢記錄傳送至 Log Analytics) 、Azure 儲存體或事件中樞 (的 Azure 監視器記錄。 如需詳細資訊，請參閱 [下文](concepts-server-logs.md#diagnostic-logs) 。

## <a name="local-server-storage-log-retention"></a>本機伺服器儲存體記錄保留
當記錄至伺服器的本機儲存體時，記錄最多可從其建立的七天內取得。 如果可用記錄的大小總計超過 7 GB，則除非有空間可用，否則會刪除最舊檔案。

記錄會每隔 24 小時或 7 GB 旋轉一次，先到者先用。

> [!Note]
> 上述記錄保留不會套用至使用 Azure 監視器診斷記錄輸送的記錄檔。 您可以將發出的資料接收的保留期限變更為 (例如。 Azure 儲存體) 。

## <a name="diagnostic-logs"></a>診斷記錄
適用於 MySQL 的 Azure 資料庫會與 Azure 監視器診斷記錄整合。 在 MySQL 伺服器上啟用慢速查詢記錄之後，您可以選擇讓它們發出 Azure 監視器記錄、事件中樞或 Azure 儲存體。 若要深入了解如何啟用診斷記錄，請參閱[診斷記錄文件](../azure-monitor/platform/platform-logs-overview.md)的操作說明一節。

下表描述每個記錄的內容。 視輸出方法而定，包含的欄位及其出現的順序可能有所不同。

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
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | 插入識別碼 |
| `sql_text_s` | 完整查詢 |
| `server_id_s` | 伺服器的識別碼 |
| `thread_id_s` | 執行緒識別碼 |
| `\_ResourceId` | 資源 URI |

> [!Note]
> `sql_text`若為，則如果記錄超過2048個字元，則會予以截斷。

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
- [如何從 Azure 入口網站設定慢速查詢記錄](howto-configure-server-logs-in-portal.md)
- [如何從 Azure CLI 設定慢速查詢記錄](howto-configure-server-logs-in-cli.md)