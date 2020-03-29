---
title: 稽核記錄 - MySQL 的 Azure 資料庫
description: 描述 MySQL Azure 資料庫中可用的稽核記錄，以及用於啟用日誌記錄級別的可用參數。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: b42f0d7a8146f7f2b313959273abd22303c89a60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062552"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>MySQL Azure 資料庫中的稽核記錄

在 MySQL 的 Azure 資料庫中，稽核記錄可供使用者使用。 稽核記錄可用於跟蹤資料庫級活動，通常用於合規性。

> [!IMPORTANT]
> 稽核記錄功能當前處於預覽狀態。

## <a name="configure-audit-logging"></a>配置稽核記錄記錄

預設情況下，稽核記錄已禁用。 要啟用它，應`audit_log_enabled`設置為"打開"。

您可以調整的其他參數包含：

- `audit_log_events`：控制要記錄的事件。 有關特定審核事件，請參閱下表。
- `audit_log_include_users`：要包括用於日誌記錄的 MySQL 使用者。 此參數的預設值為空，這將包括用於日誌記錄的所有使用者。 這具有更高的優先順序。 `audit_log_exclude_users` 參數的最大長度為 512 個字元。
> [!Note]
> `audit_log_include_users`具有高於`audit_log_exclude_users`的優先順序。 例如`audit_log_include_users` = `demouser`，如果 和`audit_log_exclude_users` = `demouser`，使用者將包含在稽核記錄中，因為`audit_log_include_users`優先順序較高。
- `audit_log_exclude_users`：mySQL 使用者將從日誌記錄中排除。 參數的最大長度為 512 個字元。

> [!Note]
> 對於`sql_text`，如果日誌超過 2048 個字元，日誌將被截斷。

| **事件** | **描述** |
|---|---|
| `CONNECTION` | - 連接啟動（成功或不成功） <br> - 會話期間使用不同的使用者/密碼重新認證使用者 <br> - 連接終止 |
| `DML_SELECT`| SELECT 查詢 |
| `DML_NONSELECT` | 插入/刪除/更新查詢 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | 查詢，如"DROP 資料庫" |
| `DCL` | 查詢，如"授權" |
| `ADMIN` | 查詢，如"顯示狀態" |
| `GENERAL` | 全部DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN |
| `TABLE_ACCESS` | - 僅適用于 MySQL 5.7 <br> - 表讀取語句，如選擇或插入 ...選擇 <br> - 表刪除語句，如刪除或截圖表 <br> - 表插入語句，如插入或替換 <br> - 表更新語句，如更新 |

## <a name="access-audit-logs"></a>存取稽核記錄

稽核記錄與 Azure 監視器診斷日誌集成。 在 MySQL 伺服器上啟用稽核記錄後，可以將日誌發射到 Azure 監視器日誌、事件中心或 Azure 存儲。 要瞭解有關如何在 Azure 門戶中啟用診斷日誌的更多資訊，請參閱[稽核記錄門戶一文](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)。

## <a name="diagnostic-logs-schemas"></a>診斷日誌架構

以下各節介紹 MySQL 稽核記錄基於事件種類輸出的內容。 視輸出方法而定，包含的欄位及其出現的順序可能有所不同。

### <a name="connection"></a>Connection

| **屬性** | **描述** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 伺服器的名稱 |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT``DISCONNECT`，（`CHANGE USER`僅適用于 MySQL 5.7） |
| `connection_id_d` | MySQL 生成的唯一連接 ID |
| `host_s` | Blank |
| `ip_s` | 連接到 MySQL 的用戶端的 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `db_s` | 連接到的資料庫名稱 |
| `\_ResourceId` | 資源 URI |

### <a name="general"></a>一般

以下架構適用于 GENERAL、DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN 事件種類。

| **屬性** | **描述** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 伺服器的名稱 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG``ERROR`，（`RESULT`僅適用于 MySQL 5.6） |
| `event_time` | 以 UTC 時間戳記表示查詢開始時間 |
| `error_code_d` | 如果查詢失敗，則錯誤代碼。 `0`表示沒有錯誤 |
| `thread_id_d` | 執行查詢的執行緒的 ID |
| `host_s` | Blank |
| `ip_s` | 連接到 MySQL 的用戶端的 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `sql_text_s` | 完整查詢文本 |
| `\_ResourceId` | 資源 URI |

### <a name="table-access"></a>資料表存取

> [!NOTE]
> 僅為 MySQL 5.7 輸出表訪問日誌。

| **屬性** | **描述** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 伺服器的名稱 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`、`INSERT`、`UPDATE` 或 `DELETE` |
| `connection_id_d` | MySQL 生成的唯一連接 ID |
| `db_s` | 訪問的資料庫名稱 |
| `table_s` | 訪問的表的名稱 |
| `sql_text_s` | 完整查詢文本 |
| `\_ResourceId` | 資源 URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>分析 Azure 監視器日誌中的日誌

稽核記錄通過診斷記錄傳送到 Azure 監視器日誌後，可以對審核的事件執行進一步分析。 下面是一些依例查詢，以説明您入門。 請確保使用伺服器名稱更新以下內容。

- 列出特定伺服器上的 GENERAL 事件

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- 列出特定伺服器上的連接事件

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- 匯總特定伺服器上的已審核事件

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- 繪製特定伺服器上的審核事件種類分布圖

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- 列出所有 MySQL 伺服器的審核事件，並啟用診斷日誌以進行稽核記錄

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>後續步驟

- [如何在 Azure 門戶中配置稽核記錄](howto-configure-audit-logs-portal.md)