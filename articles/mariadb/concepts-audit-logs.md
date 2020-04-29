---
title: Audit logs-適用於 MariaDB 的 Azure 資料庫
description: 描述適用於 MariaDB 的 Azure 資料庫中可用的 audit 記錄，以及可用來啟用記錄層級的參數。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: e8d5abd81feb86ba48fc442ee95615cb52230a24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063821"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的 Audit 記錄

在適用於 MariaDB 的 Azure 資料庫中，審核記錄可供使用者使用。 您可以使用 audit 記錄來追蹤資料庫層級的活動，而且通常會用於合規性。

> [!IMPORTANT]
> Audit log 功能目前為預覽狀態。

## <a name="configure-audit-logging"></a>設定審核記錄

根據預設，會停用 audit 記錄檔。 若要啟用它， `audit_log_enabled`請將設定為 ON。

您可以調整的其他參數包含：

- `audit_log_events`：控制要記錄的事件。 請參閱下表以取得特定的 audit 事件。
- `audit_log_include_users`：適用于 mariadb 要包含在記錄中的使用者。 這個參數的預設值是空的，這將包含所有用於記錄的使用者。 其優先順序高於`audit_log_exclude_users`。 參數的最大長度為512個字元。
> [!Note]
> `audit_log_include_users`的優先順序高於`audit_log_exclude_users`。 例如`audit_log_include_users`  =  `demouser` ，如果`audit_log_exclude_users`  = 和`demouser`，則會將使用者包含在 audit 記錄中，因為`audit_log_include_users`具有較高的優先順序。
- `audit_log_exclude_users`：適用于 mariadb 要排除在記錄之外的使用者。 最多允許四個使用者。 參數的最大長度為256個字元。

| **發生** | **說明** |
|---|---|
| `CONNECTION` | -連接起始（成功或失敗） <br> -在會話期間以不同的使用者/密碼重新驗證使用者 <br> -連接終止 |
| `DML_SELECT`| SELECT 查詢 |
| `DML_NONSELECT` | 插入/刪除/更新查詢 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | 「卸載資料庫」之類的查詢 |
| `DCL` | 「授與許可權」之類的查詢 |
| `ADMIN` | 「顯示狀態」之類的查詢 |
| `GENERAL` | 全部 DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN |

## <a name="access-audit-logs"></a>存取稽核記錄

Audit 記錄會與 Azure 監視器診斷記錄整合。 一旦您已在適用于 mariadb 伺服器上啟用 audit 記錄檔，您就可以將它們發出至 Azure 監視器記錄、事件中樞或 Azure 儲存體。 若要深入瞭解如何在 Azure 入口網站中啟用診斷記錄，請參閱[audit log portal 一文](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)。

## <a name="diagnostic-logs-schemas"></a>診斷記錄架構

下列各節根據事件種類來適用于 mariadb audit 記錄，以描述輸出內容。 視輸出方法而定，包含的欄位及其出現的順序可能有所不同。

### <a name="connection"></a>Connection

| **屬性** | **說明** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | 適用于 mariadb 產生的唯一連接識別碼 |
| `host_s` | Blank |
| `ip_s` | 連接到適用于 mariadb 的用戶端 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `db_s` | 連接的資料庫名稱 |
| `\_ResourceId` | 資源 URI |

### <a name="general"></a>一般

以下架構適用于 GENERAL、DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN 事件種類。

| **屬性** | **說明** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | 伺服器的名稱 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | UNIX 時間戳記中的查詢開始秒數 |
| `error_code_d` | 如果查詢失敗，則為錯誤碼。 `0`表示沒有錯誤 |
| `thread_id_d` | 執行查詢的執行緒識別碼 |
| `host_s` | Blank |
| `ip_s` | 連接到適用于 mariadb 的用戶端 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `sql_text_s` | 完整查詢文字 |
| `\_ResourceId` | 資源 URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>分析 Azure 監視器記錄中的記錄

當您的審核記錄透過診斷記錄傳送至 Azure 監視器記錄後，您就可以進一步分析已審核的事件。 以下是一些可協助您開始使用的範例查詢。 請務必以您的伺服器名稱更新下列各節。

- 列出特定伺服器上的一般事件

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

- 摘要特定伺服器上的已審核事件

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- 在特定伺服器上建立 audit 事件種類散發的圖表

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- 列出所有適用于 mariadb 伺服器上已啟用 audit 記錄之診斷記錄的已審核事件

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>後續步驟

- [如何設定 Azure 入口網站中的 audit 記錄](howto-configure-audit-logs-portal.md)