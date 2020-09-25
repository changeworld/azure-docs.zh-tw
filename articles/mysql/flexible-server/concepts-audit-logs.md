---
title: Audit logs-適用於 MySQL 的 Azure 資料庫-彈性伺服器
description: 描述適用於 MySQL 的 Azure 資料庫彈性伺服器中可用的 audit 記錄。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 00d0808e83a888a39f9268328feb209167c9b644
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295984"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>使用適用於 MySQL 的 Azure 資料庫彈性伺服器中的 Audit 記錄來追蹤資料庫活動

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫彈性伺服器目前處於公開預覽狀態

> [!NOTE]
> Azure 監視器診斷設定與存取記錄的整合正在進行部署，而且很快就會提供完整功能。

適用於 MySQL 的 Azure 資料庫彈性的伺服器可讓使用者設定 audit 記錄。 您可以使用 Audit 記錄來追蹤資料庫層級活動，包括連接、管理員、DDL 和 DML 事件。 這些記錄類型通常用於合規性用途。

## <a name="configure-audit-logging"></a>設定 audit 記錄

>[!IMPORTANT]
> 建議您只針對您的審核目的記錄所需的事件種類和使用者，以確保伺服器的效能不會受到嚴重的影響。

預設會停用 audit 記錄檔。 若要啟用它們，請將 `audit_log_enabled` server 參數設定為 *ON*。 您可以使用 Azure 入口網站或 Azure CLI 來設定此設定。 <!-- add link to server parameter-->. 

您可以調整以控制審核記錄行為的其他參數包括：

- `audit_log_events`：控制要記錄的事件。 請參閱下表以取得特定的 audit 事件。
- `audit_log_include_users`：要包含在記錄中的 MySQL 使用者。 這個參數的預設值是空的，它會包含所有用於記錄的使用者。 優先順序高於 `audit_log_exclude_users` 。 參數的最大長度為512個字元。
- `audit_log_exclude_users`：要排除在記錄之外的 MySQL 使用者。 參數的最大長度為512個字元。

> [!NOTE]
> `audit_log_include_users` 的優先順序高於 `audit_log_exclude_users` 。 例如，如果 `audit_log_include_users`  =  `demouser` 和 `audit_log_exclude_users`  =  `demouser` ，使用者將會包含在 audit 記錄中，因為 `audit_log_include_users` 優先順序較高。

| **事件** | **說明** |
|---|---|
| `CONNECTION` | -連接初始 (成功或失敗)  <br> -會話期間以不同的使用者/密碼重新驗證使用者 <br> -連接終止 |
| `DML_SELECT`| SELECT 查詢 |
| `DML_NONSELECT` | 插入/刪除/更新查詢 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | 「卸載資料庫」之類的查詢 |
| `DCL` | 像是「授與許可權」的查詢 |
| `ADMIN` | "SHOW STATUS" 之類的查詢 |
| `GENERAL` | All in DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN |
| `TABLE_ACCESS` | -僅適用于 MySQL 5。7 <br> -Table read 語句，例如 SELECT 或 INSERT INTO .。。選擇 <br> -資料表 delete 語句，例如 DELETE 或 TRUNCATE TABLE <br> -資料表 insert 語句，例如 INSERT 或 REPLACE <br> -資料表 update 語句，例如 UPDATE |

## <a name="access-audit-logs"></a>存取稽核記錄

> [!NOTE]
> Azure 監視器診斷設定與存取記錄的整合正在進行部署，而且很快就會提供完整功能。

Audit 記錄檔會與 Azure 監視器診斷設定整合。 當您在 MySQL 彈性伺服器上啟用 audit 記錄之後，您可以將它們發出至 Azure 監視器記錄、事件中樞或 Azure 儲存體。 若要深入瞭解診斷設定，請參閱 [診斷記錄檔](../../azure-monitor/platform/platform-logs-overview.md)。 若要深入瞭解如何啟用 Azure 入口網站中的診斷設定，請參閱 [audit log portal 文章](how-to-configure-audit-logs-portal.md#set-up-diagnostics)。

下列各節會根據事件種類來描述 MySQL audit 記錄的輸出。 視輸出方法而定，包含的欄位及其出現的順序可能有所不同。

### <a name="connection"></a>連線

| **屬性** | **說明** |
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
| `event_subclass_s` | `CONNECT`、 `DISCONNECT` `CHANGE USER` (僅適用于 MySQL 5.7)  |
| `connection_id_d` | MySQL 產生的唯一連接識別碼 |
| `host_s` | Blank |
| `ip_s` | 連線至 MySQL 之用戶端的 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `db_s` | 連接的資料庫名稱 |
| `\_ResourceId` | 資源 URI |

### <a name="general"></a>一般

下列架構適用于 GENERAL、DML_SELECT、DML_NONSELECT、DML、DDL、DCL 和 ADMIN 事件種類。

> [!NOTE]
> `sql_text_s`若為，則如果記錄超過2048個字元，則會予以截斷。

| **屬性** | **說明** |
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
| `event_subclass_s` | `LOG`、 `ERROR` `RESULT` (僅適用于 MySQL 5.6)  |
| `event_time` | UTC 時間戳記的查詢開始時間 |
| `error_code_d` | 如果查詢失敗，則為錯誤碼。 `0` 表示沒有錯誤 |
| `thread_id_d` | 執行查詢的執行緒識別碼 |
| `host_s` | Blank |
| `ip_s` | 連線至 MySQL 之用戶端的 IP 位址 |
| `user_s` | 執行查詢的使用者名稱 |
| `sql_text_s` | 完整查詢文字 |
| `\_ResourceId` | 資源 URI |

### <a name="table-access"></a>資料表存取

> [!NOTE]
> 資料表存取記錄只是 MySQL 5.7 的輸出。<br>`sql_text_s`若為，則如果記錄超過2048個字元，則會予以截斷。

| **屬性** | **說明** |
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
| `connection_id_d` | MySQL 產生的唯一連接識別碼 |
| `db_s` | 存取的資料庫名稱 |
| `table_s` | 存取的資料表名稱 |
| `sql_text_s` | 完整查詢文字 |
| `\_ResourceId` | 資源 URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>在 Azure 監視器記錄中分析記錄

一旦將您的 audit 記錄輸送至透過診斷記錄 Azure 監視器記錄之後，您就可以進一步分析已審核的事件。 以下是一些可協助您開始使用的範例查詢。 請務必以您的伺服器名稱更新下列。

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

- 圖表特定伺服器上的 audit 事件種類散發

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- 列出所有 MySQL 伺服器上已啟用審核記錄之診斷記錄的已審核事件

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>後續步驟
- 深入瞭解 [查詢記錄緩慢](concepts-slow-query-logs.md)
- 從[Azure 入口網站](how-to-configure-audit-logs-portal.md)設定 audit 查詢記錄
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->