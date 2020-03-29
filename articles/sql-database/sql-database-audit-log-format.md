---
title: 稽核記錄檔格式
description: 瞭解 SQL 資料庫稽核記錄的結構。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722079"
---
# <a name="sql-database-audit-log-format"></a>SQL 資料庫稽核記錄格式

[Azure SQL 資料庫審核](sql-database-auditing.md)跟蹤資料庫事件並將其寫入 Azure 存儲帳戶中的稽核記錄，或將它們發送到事件中心或日誌分析，以便進行下游處理和分析。

## <a name="naming-conventions"></a>命名規範

### <a name="blob-audit"></a>Blob 審核

存儲在 Blob 存儲中的稽核記錄存儲在 Azure`sqldbauditlogs`存儲帳戶中指定的容器中。 容器中的目錄層次結構是 表單`<ServerName>/<DatabaseName>/<AuditName>/<Date>/`。 Blob 檔案名格式為`<CreationTime>_<FileNumberInSession>.xel`，`CreationTime`其中以`hh_mm_ss_ms`UTC 格式`FileNumberInSession`，是會話日誌跨越多個 Blob 檔時運行的索引。

例如，對於以下`Database1`資料庫`Server1`，可能是一個可能的有效路徑：

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[唯讀副本](sql-database-read-scale-out.md)稽核記錄存儲在同一容器中。 容器中的目錄層次結構是 表單`<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`。 Blob 檔案名共用相同的格式。 唯讀副本的稽核記錄存儲在同一容器中。


### <a name="event-hub"></a>事件中樞

審核事件寫入審核配置期間定義的命名空間和事件中心，並在[Apache Avro](https://avro.apache.org/)事件正文中捕獲並使用使用具有 UTF-8 編碼的 JSON 格式進行存儲。 若要讀取稽核記錄，您可以使用 [Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) 或類似工具來處理這種格式。

### <a name="log-analytics"></a>Log Analytics

審核事件寫入在審核配置期間定義的日誌分析工作區，寫入類別 的`AzureDiagnostics`表`SQLSecurityAuditEvents`。 如需 Log Analytics 搜尋語言和命令的其他實用資訊，請參閱 [Log Analytics 搜尋參考](../log-analytics/log-analytics-log-search.md)。

## <a name="audit-log-fields"></a><a id="subheading-1"></a>稽核記錄欄位

| 名稱（Blob） | 名稱（事件中心/日誌分析） | 描述 | Blob 類型 | 事件中心/日誌分析類型 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 動作的識別碼 | varchar(4) | 字串 |
| action_name | action_name_s | 操作的名稱 | N/A | 字串 |
| additional_information | additional_information_s | 有關事件的任何其他資訊，存儲為 XML | nvarchar(4000) | 字串 |
| affected_rows | affected_rows_d | 受查詢影響的行數 | BIGINT | int |
| application_name | application_name_s| 用戶端應用程式的名稱 | nvarchar(128) | 字串 |
| audit_schema_version | audit_schema_version_d | 始終為 1 | int | int |
| class_type | class_type_s | 審核發生的可審核實體的類型 | varchar(2) | 字串 |
| class_type_desc | class_type_description_s | 審核發生的可審核實體的說明 | N/A | 字串 |
| client_ip | client_ip_s | 用戶端應用程式的源 IP | nvarchar(128) | 字串 |
| connection_id | N/A | 伺服器中連接的 ID | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | 根據資料庫中的分類列，經審核的查詢返回的資訊類型和敏感度標籤。 瞭解有關[Azure SQL 資料庫資料發現和分類](sql-database-data-discovery-and-classification.md)的詳細資訊 | nvarchar(4000) | 字串 |
| database_name | database_name_s | 發生操作的資料庫上下文 | sysname | 字串 |
| database_principal_id | database_principal_id_d | 在 中執行操作的資料庫使用者上下文的 ID | int | int |
| database_principal_name | database_principal_name_s | 執行操作的資料庫使用者上下文的名稱 | sysname | 字串 |
| duration_milliseconds | duration_milliseconds_d | 查詢執行持續時間（以毫秒為單位） | BIGINT | int |
| event_time | event_time_t | 觸發可審核操作的日期和時間 | datetime2 | Datetime |
| host_name | N/A | 用戶端主機名稱 | 字串 | N/A |
| is_column_permission | is_column_permission_s | 指出這是否為資料行層級權限的旗標。 1 = 真，0 = 假 | bit | 字串 |
| N/A | is_server_level_audit_s | 指示此審核是否位於伺服器級別的標誌 | N/A | 字串 |
| object_ id | object_id_d | 稽核發生所在之實體的識別碼。 這包括 ： 伺服器物件、資料庫、資料庫物件和架構物件。 0 如果實體是伺服器本身，或者審核未在物件級別執行 | int | int |
| object_name | object_name_s | 稽核發生所在之實體的名稱。 這包括 ： 伺服器物件、資料庫、資料庫物件和架構物件。 0 如果實體是伺服器本身，或者審核未在物件級別執行 | sysname | 字串 |
| permission_bitmask | permission_bitmask_s | 當適用時，顯示已授與、拒絕或撤銷的權限 | varbinary(16) | 字串 |
| response_rows | response_rows_d | 在結果集中返回的行數 | BIGINT | int |
| schema_name | schema_name_s | 動作發生所在的結構描述環境。 對於在架構之外發生的審核的 Null | sysname | 字串 |
| N/A | securable_class_type_s | 映射到要審核class_type的可安全物件 | N/A | 字串 |
| sequence_group_id | sequence_group_id_g | 唯一識別碼 | varbinary | GUID |
| sequence_number | sequence_number_d | 跟蹤單個審核記錄中的記錄序列，這些記錄太大，無法放入用於審核的寫入緩衝區中 | int | int |
| server_instance_name | server_instance_name_s | 發生審核的伺服器實例的名稱 | sysname | 字串 |
| server_principal_id | server_principal_id_d | 執行操作的登錄上下文的 ID | int | int |
| server_principal_name | server_principal_name_s | 當前登錄 | sysname | 字串 |
| server_principal_sid | server_principal_sid_s | 當前登錄 SID | varbinary | 字串 |
| session_id | session_id_d | 事件發生的會話的 ID | SMALLINT | int |
| session_server_principal_name | session_server_principal_name_s | 會話的伺服器主體 | sysname | 字串 |
| 陳述式 | statement_s | 已執行的 T-SQL 語句（如果有） | nvarchar(4000) | 字串 |
| succeeded | succeeded_s | 指示觸發此事件的動作是否成功。 對於登錄和批次處理以外的事件，這僅報告許可權檢查是否成功或失敗，而不是操作。 1 = 成功，0 = 失敗 | bit | 字串 |
| target_database_principal_id | target_database_principal_id_d | GRANT/DENY/REVOKE 作業執行所在的資料庫主體。 0，如果不適用 | int | int |
| target_database_principal_name | target_database_principal_name_s | 動作的目標使用者。 無效（如果不適用） | 字串 | 字串 |
| target_server_principal_id | target_server_principal_id_d | GRANT/DENY/REVOKE 作業執行所在的伺服器主體。 返回 0（如果不適用） | int | int |
| target_server_principal_name | target_server_principal_name_s | 動作的目標登入。 無效（如果不適用） | sysname | 字串 |
| target_server_principal_sid | target_server_principal_sid_s | 目標登入的 SID。 無效（如果不適用） | varbinary | 字串 |
| transaction_id | transaction_id_d | 僅限 SQL 伺服器（從 2016 年開始） - Azure SQL DB 的 0 | BIGINT | int |
| user_defined_event_id | user_defined_event_id_d | 使用者定義的事件 ID 作為參數傳遞給sp_audit_write。 系統事件（預設）為 Null，使用者定義的事件為非零。 有關詳細資訊，請參閱[sp_audit_write（轉算-SQL）](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | SMALLINT | int |
| user_defined_information | user_defined_information_s | 使用者定義的資訊作為參數傳遞給sp_audit_write。 系統事件（預設）為 Null，使用者定義的事件為非零。 有關詳細資訊，請參閱[sp_audit_write（轉算-SQL）](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | 字串 |

## <a name="next-steps"></a>後續步驟

瞭解有關[Azure SQL 資料庫審核](sql-database-auditing.md)的詳細資訊。
