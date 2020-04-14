---
title: 查詢紀錄緩慢 - MariaDB 的 Azure 資料庫
description: 描述適用於 MariaDB 的 Azure 資料庫中的可用記錄，以及啟用不同記錄層級的可用參數。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: ffd4ab463080001dbab5b0ed9ece69c4b5f91382
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272078"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>MariaDB Azure 資料庫中的查詢紀錄速度慢
在適用於 MariaDB 的 Azure 資料庫中，使用者可以使用慢速查詢記錄檔。 不支援存取交易記錄。 慢速查詢記錄檔可以用來找出效能瓶頸，以進行疑難排解。

如需慢速查詢記錄檔的詳細資訊，請參閱 MariaDB 文件的[慢速查詢記錄檔](https://mariadb.com/kb/en/library/slow-query-log-overview/) (英文)。

## <a name="configure-slow-query-logging"></a>設定慢速查詢紀錄記錄
預設會停用慢速查詢記錄。 要啟用它,應`slow_query_log`設置為"打開"。 這可以使用 Azure 門戶或 Azure CLI 啟用。 

您可以調整的其他參數包含：

- **long_query_time**：如果查詢所需的時間比記錄該查詢的 long_query_time (秒) 還要久。 預設值為 10 秒。
- **log_slow_admin_statements**：如果 ON 在寫入至 slow_query_log 的陳述式中包含 ALTER_TABLE 和 ANALYZE_TABLE 這類管理陳述式。
- **log_queries_not_using_indexes**：決定是否將未使用索引的查詢記錄至 slow_query_log
- **log_throttle_queries_not_using_indexes**：這個參數會限制可寫入至慢速查詢記錄的非索引查詢次數。 log_queries_not_using_indexes 設為 ON 時，這個參數會生效。
- **log_output**:如果為"檔",則允許將慢速查詢日誌寫入本地伺服器存儲和 Azure 監視器診斷日誌。 如果"無",則慢速查詢日誌將僅寫入 Azure 監視器診斷日誌。 

> [!IMPORTANT]
> 如果表未編製索引,則將`log_queries_not_using_indexes``log_throttle_queries_not_using_indexes`和 參數設置為 ON 可能會影響 MariaDB 性能,因為針對這些非索引表運行的所有查詢都將寫入慢速查詢日誌。<br><br>
> 如果您計劃在較長時間內記錄慢速查詢,建議將設置為`log_output`「無」。 如果設置為"檔",這些日誌將寫入本機伺服器存儲,並可能影響 MariaDB 的性能。 

如需慢速查詢記錄檔參數的完整描述，請參閱 MariaDB [慢速查詢記錄檔文件](https://mariadb.com/kb/en/library/slow-query-log-overview/)。

## <a name="access-slow-query-logs"></a>存取慢速查詢紀錄
在 MariaDB 的 Azure 資料庫中存取慢速查詢日誌有兩個選項:本地伺服器儲存或 Azure 監視器診斷日誌。 這是使用 參數`log_output`設置的。

對於本地伺服器存儲,可以使用 Azure 門戶或 Azure CLI 列出和下載慢速查詢日誌。 在 Azure 門戶中,在 Azure 門戶中導航到伺服器。 在 [監視]**** 標題下方，選取 [伺服器記錄]**** 頁面。 如需 Azure CLI 的詳細資訊，請參閱[使用 Azure CLI 設定和存取伺服器記錄](howto-configure-server-logs-cli.md)。 

Azure 監視器診斷日誌允許您將慢速查詢日誌管道到 Azure 監視器日誌(日誌分析)、Azure 儲存或事件中心。 有關詳細資訊,請參閱[下文](concepts-server-logs.md#diagnostic-logs)。

## <a name="local-server-storage-log-retention"></a>本地伺服器儲存紀錄保留
當登錄到伺服器的本地存儲時,日誌在創建後最多有 7 天可用。 如果可用記錄的大小總計超過 7 GB，則除非有空間可用，否則會刪除最舊檔案。

記錄會每隔 24 小時或 7 GB 旋轉一次，先到者先用。

> [!Note]
> 上述日誌保留不適用於使用 Azure 監視器診斷日誌進行管道的日誌。 您可以更改要發送到的資料接收器的保留期(例如。 Azure 存儲)。

## <a name="diagnostic-logs"></a>診斷記錄
MariaDB 的 Azure 資料庫與 Azure 監視器診斷日誌集成。 在 MariaDB 伺服器上啟用慢速查詢日誌後,可以選擇將它們發送到 Azure 監視器日誌、事件中心或 Azure 儲存。 若要深入了解如何啟用診斷記錄，請參閱[診斷記錄文件](../azure-monitor/platform/platform-logs-overview.md)的操作說明一節。

下表描述每個記錄的內容。 視輸出方法而定，包含的欄位及其出現的順序可能有所不同。

| **屬性** | **說明** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated`[UTC] | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 伺服器的名稱 |
| `start_time_t`[UTC] | 查詢開始時間 |
| `query_time_s` | 執行查詢所花費的總時間 |
| `lock_time_s` | 查詢遭到鎖定的總時間 |
| `user_host_s` | 使用者名稱 |
| `rows_sent_s` | 傳送的資料列數目 |
| `rows_examined_s` | 檢查的資料列數目 |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | 插入識別碼 |
| `sql_text_s` | 完整查詢 |
| `server_id_s` | 伺服器識別碼 |
| `thread_id_s` | 執行緒識別碼 |
| `\_ResourceId` | 資源 URI |

> [!Note]
> 對於`sql_text`,如果日誌超過 2048 個字元,日誌將被截斷。

## <a name="analyze-logs-in-azure-monitor-logs"></a>剖析 Azure 監視器紀錄中的紀錄

通過診斷日誌將慢速查詢日誌傳送到 Azure 監視器日誌後,可以對慢速查詢執行進一步分析。 下面是一些示例查詢,以説明您入門。 請確保使用伺服器名稱更新以下內容。

- 在特定伺服器上的查詢時間超過 10 秒

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- 列出特定伺服器上的 5 個最長查詢

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- 依特定伺服器上的最小、最大、平均和標準差查詢時間匯總慢速查詢

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 繪製特定伺服器上的慢速查詢分配圖

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 在所有啟用診斷紀錄的 MariaDB 伺服器上顯示超過 10 秒的查詢

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>後續步驟
- [如何從 Azure 門戶配置慢速查詢日誌](howto-configure-server-logs-portal.md)
- [如何設定來自 Azure CLI 的慢速查詢紀錄](howto-configure-server-logs-cli.md)
