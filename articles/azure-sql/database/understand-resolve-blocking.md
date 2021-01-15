---
title: 瞭解並解決 Azure SQL 封鎖問題
titleSuffix: Azure SQL Database
description: 概述有關封鎖和疑難排解的 Azure SQL database 特定主題。
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 1/14/2020
ms.openlocfilehash: d3bd63566daaf6e1d3e3343b5956d8a8d5fc8ea5
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224451"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>瞭解和解決 Azure SQL Database 封鎖問題
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>目標

本文說明 Azure SQL database 中的封鎖，並示範如何進行疑難排解並解決封鎖問題。 

在本文中，「連接」一詞是指資料庫的單一登入會話。 在許多 Dmv 中，每個連線會顯示為會話識別碼 (SPID) 或 session_id。 這些 Spid 通常稱為「處理常式」（process），雖然它不是一般意義上的個別進程內容。 相反地，每個 SPID 都是由伺服器資源和資料結構所組成，這是從指定用戶端對單一連接提出要求所需的服務。 單一用戶端應用程式可能會有一或多個連接。 從 Azure SQL Database 的觀點來看，單一用戶端電腦上的單一用戶端應用程式與多個用戶端應用程式或多部用戶端電腦之間的多個連線之間沒有任何差異;它們是不可部分完成的。 無論來源用戶端為何，一個連接都可以封鎖另一個連線。

> [!NOTE]
> **這是 Azure SQL Database 特有的內容。** Azure SQL Database 是以 Microsoft SQL Server Database engine 的最新穩定版本為基礎，因此大部分的內容都很類似，但疑難排解選項和工具可能會有所不同。 如需 SQL Server 封鎖的詳細資訊，請參閱 [瞭解並解決 SQL Server 封鎖問題](/troubleshoot/sql/performance/understand-resolve-blocking)。

## <a name="understand-blocking"></a>瞭解封鎖 
 
封鎖是任何關係資料庫管理系統的無法避免和設計特性， (RDBMS) 與鎖定型平行存取。 如先前所述，在 SQL Server 中，當某個會話持有特定資源的鎖定，而第二個 SPID 嘗試在相同資源上取得衝突的鎖定類型時，就會發生封鎖。 一般來說，第一個 SPID 用來鎖定資源的時間範圍很小。 當擁有的會話釋放鎖定時，第二個連接就可以自由取得自己的資源鎖定，並繼續處理。 這是正常行為，而且可能會在一天內發生許多次，而且對系統效能沒有明顯的影響。

查詢的持續時間和交易內容會決定鎖定的保留時間長度，進而影響對其他查詢的影響。 如果查詢不是在交易 (內執行，而且沒有使用任何鎖定提示) ，SELECT 語句的鎖定將只會保留在實際讀取時的資源上，而不會保留在查詢期間。 針對 INSERT、UPDATE 和 DELETE 子句，查詢期間會保留鎖定，以用於資料一致性，並允許在必要時回復查詢。

針對在交易內執行的查詢，保留鎖定的持續時間取決於查詢類型、交易隔離等級，以及查詢中是否使用鎖定提示。 如需鎖定、鎖定提示和交易隔離等級的描述，請參閱下列文章：

* [資料庫引擎中的鎖定](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [自訂鎖定及資料列版本設定](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [鎖定模式](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [鎖定相容性](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [交易](/sql/t-sql/language-elements/transactions-transact-sql)

當鎖定和封鎖持續存在於對系統效能造成不利影響的點時，則是因為下列其中一個原因所造成：

* SPID 在釋出一組資源之前，會保留一段長時間的鎖定。 這種類型的封鎖會在一段時間內自行解決，但可能會導致效能降低。

* SPID 持有一組資源的鎖定，而且永遠不會釋出。 這種類型的封鎖無法自行解決，並可防止無限期地存取受影響的資源。

在第一個案例中，這種情況可能會非常流暢，因為不同的 Spid 會在一段時間內導致不同的資源封鎖，從而建立移動的目標。 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 將問題縮小為個別查詢時，這些情況很難進行疑難排解。 相反地，第二種情況會產生一致的狀態，以便更容易進行診斷。

## <a name="applications-and-blocking"></a>應用程式和封鎖

在面對封鎖問題時，您可能會想要專注于伺服器端的微調和平臺問題。 不過，只針對資料庫付費的問題可能無法解決問題，而且可以在檢查用戶端應用程式和它所提交的查詢時，更妥善地吸收時間和能源。 無論應用程式公開什麼層級的資料庫呼叫相關資訊，封鎖問題通常都需要檢查應用程式所提交的確切 SQL 語句，以及應用程式對於查詢取消、連接管理、提取所有結果資料列等的確切行為。 如果開發工具不允許明確控制連接管理、查詢取消、查詢超時、結果提取等，可能無法解析封鎖問題。 在針對 Azure SQL Database 選取應用程式開發工具之前，應該仔細檢查這種可能性，特別是針對效能敏感性 OLTP 環境。 

在資料庫和應用程式的設計和結構階段中，請注意資料庫效能。 特別是，您應該針對每個查詢評估資源耗用量、隔離等級和交易路徑長度。 每個查詢和交易應該盡可能輕量。 如果有良好的連線管理專業領域，就不需要這樣做，應用程式可能會在較少的使用者上看得到可接受的效能，但是當使用者數目相應增加時，效能可能會大幅降低。 

透過適當的應用程式和查詢設計，Azure SQL Database 可以在單一伺服器上支援數千個並行使用者，但幾乎不會封鎖。

> [!Note]
> 如需更多應用程式開發指引，請參閱 [使用 Azure SQL Database 和 AZURE SQL 受控執行個體](troubleshoot-common-errors-issues.md) 和 [暫時性錯誤處理](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)來疑難排解連線問題和其他錯誤。

## <a name="troubleshoot-blocking"></a>疑難排解封鎖

無論我們在哪一個封鎖的情況下，針對鎖定進行疑難排解的方法都一樣。 這些邏輯分隔將會決定本文的其餘部分。 概念是找出前端封鎖程式，並找出該查詢的作用以及其封鎖原因。 一旦識別出有問題的查詢， (也就是在長時間內持有鎖定) 的情況下，下一步是分析並判斷封鎖發生的原因。 在瞭解原因之後，我們可以重新設計查詢和交易來進行變更。

疑難排解的步驟：

1. 找出 (前端封鎖程式的主要封鎖會話) 

2. 找出造成封鎖的查詢和交易 (長時間的保留鎖定) 

3. 分析/瞭解發生長時間封鎖的原因

4. 藉由重新設計查詢和交易來解決封鎖問題

現在讓我們深入探討如何使用適當的資料捕獲來找出主要的封鎖會話。

## <a name="gather-blocking-information"></a>收集封鎖資訊

為了抵禦封鎖問題疑難排解的困難，資料庫管理員可以使用 SQL 腳本，在 Azure SQL 資料庫中持續監視鎖定和封鎖的狀態。 若要收集此資料，基本上有兩種方法。 

第一個是查詢動態管理物件 (DMOs) 並儲存結果，以便於一段時間內進行比較。 本文中參考的部分物件是 (Dmv) 的動態管理檢視，有些是動態管理功能 (Dmf) 。 第二種方法是使用 XEvents 來捕捉正在執行的內容。 


## <a name="gather-information-from-dmvs"></a>從 Dmv 收集資訊

參考 Dmv 以進行封鎖疑難排解的目標是在封鎖鏈和 SQL 語句的開頭找出 SPID (會話識別碼) 。 尋找被封鎖的犧牲者 Spid。 如果有其他 SPID 封鎖任何 SPID，則請調查擁有資源 (封鎖的 SPID) 的 SPID。 是否也會封鎖擁有者 SPID？ 您可以引導鏈找出前端封鎖程式，然後調查其鎖定的原因。

請記得在目標 Azure SQL database 中執行每個腳本。

* Sp_who 和 sp_who2 命令是用來顯示所有目前會話的較舊命令。 DMV sys.dm_exec_sessions 會在結果集中傳回更多資料，更容易查詢和篩選。 您將可在其他查詢的核心找到 sys.dm_exec_sessions。 

* 如果您已經識別出特定的會話，您可以使用 `DBCC INPUTBUFFER(<session_id>)` 來尋找會話所提交的最後一個語句。 您可以使用 sys.dm_exec_input_buffer 動態管理函式來傳回類似的結果， (DMF) 、在查詢和篩選較容易的結果集中，以及提供 session_id 和 request_id。 例如，若要傳回 session_id 66 和 request_id 0 所提交的最新查詢：

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* 請參閱 sys.dm_exec_requests，並參考 blocking_session_id 的資料行。 當 blocking_session_id = 0 時，會話不會遭到封鎖。 雖然 sys.dm_exec_requests 只會列出目前正在執行的要求，但任何 (作用中或不) 的連接都會列在 sys.dm_exec_sessions 中。 在下一個查詢中，sys.dm_exec_requests 和 sys.dm_exec_sessions 之間的一般聯結建立。

* 使用 [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) 或 [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) dmv，執行此範例查詢來尋找主動執行的查詢和其目前的 SQL 批次文字或輸入緩衝區文字。 如果 sys.dm_exec_sql_text 的欄位所傳回的資料 `text` 為 Null，則查詢目前不在執行中。 在這種情況下， `event_info` sys.dm_exec_input_buffer 的欄位將包含傳遞至 SQL 引擎的最後一個命令字串。 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* 若要攔截長時間執行或未認可的交易，請使用另一組 Dmv 來查看目前的開啟交易，包括 [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)、 [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)、 [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)和 sys.dm_exec_sql_text。 有幾個 Dmv 與追蹤交易相關聯，請參閱此處的交易更多 [dmv](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) 。 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* 參考 [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 位於 SQL 的執行緒/工作層。 這會傳回要求目前所遇到的 SQL 等候類型相關資訊。 如同 sys.dm_exec_requests，sys.dm_os_waiting_tasks 只會傳回使用中的要求。 

> [!Note]
> 如需等候類型的詳細資訊（包括一段時間的匯總等候統計資料），請參閱 DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)。 這個 DMV 只會傳回目前資料庫的匯總等候統計資料。

* 使用 [Sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV，取得查詢所放置之鎖定的更細微資訊。 此 DMV 可能會在生產環境 SQL Server 上傳回大量資料，並且有助於診斷目前保留的鎖定。 

由於 sys.dm_os_waiting_tasks 的內部聯結，下列查詢會將 sys.dm_tran_locks 的輸出限制為目前封鎖的要求、其等候狀態和其鎖定：

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* 使用 Dmv 時，儲存查詢結果一段時間將會提供資料點，讓您在指定的時間間隔內審核封鎖，以找出持續性的封鎖或趨勢。 

## <a name="gather-information-from-extended-events"></a>從擴充事件收集資訊

除了上述資訊以外，通常還需要捕捉伺服器上的活動追蹤，以徹底調查 Azure SQL Database 上的封鎖問題。 例如，如果會話在交易內執行多個語句，則只會表示最後提交的最後一個語句。 不過，其中一個較早的語句可能是仍然保留鎖定的原因。 追蹤可讓您查看目前交易內的會話所執行的所有命令。

有兩種方式可以在 SQL Server 中捕捉追蹤;擴充的事件 (XEvents) 和 Profiler 追蹤。 不過， [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) 是 Azure SQL Database 不支援的已淘汰追蹤技術。 [擴充事件](/sql/relational-databases/extended-events/extended-events) 是較新的追蹤技術，可讓觀察到的系統擁有更多功能且較不受影響，而且其介面已整合至 SQL SERVER MANAGEMENT STUDIO (SSMS) 。 

請參閱檔，以瞭解如何使用 SSMS 中的「 [擴充事件」新會話 Wizard](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) 。 不過，在 Azure SQL 資料庫中，SSMS 會在物件總管中的每個資料庫下提供「擴充事件」子資料夾。 使用「擴充事件」會話嚮導來捕獲這些有用的事件： 

-   類別錯誤：
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   類別警告： 
    -   Missing_join_predicate

-   類別執行：
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   鎖定
    -   Lock_deadlock

-   工作階段
    -   Existing_connection
    -   登入
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>識別及解決常見的封鎖案例

藉由檢查上述資訊，您可以判斷大部分封鎖問題的原因。 本文的其餘部分將討論如何使用這項資訊來識別及解決一些常見的封鎖案例。 本文假設您已使用 (稍) 早所參考的封鎖腳本來捕捉封鎖 Spid 的資訊，並使用 XEvent 會話來捕捉應用程式活動。

## <a name="analyze-blocking-data"></a>分析封鎖資料 

* 檢查 Dmv sys.dm_exec_requests 的輸出，並 sys.dm_exec_sessions 使用 blocking_these 和 session_id 判斷封鎖鏈的標頭。 這會最清楚地找出哪些要求被封鎖，哪些要求正在封鎖中。 深入瞭解封鎖和封鎖的會話。 封鎖鏈是否有共同的或根？ 他們很可能共用通用資料表，以及封鎖鏈中涉及的一或多個會話正在執行寫入作業。 

* 檢查 Dmv sys.dm_exec_requests 的輸出，並 sys.dm_exec_sessions，以取得有關在封鎖鏈的開頭之 Spid 的詳細資訊。 尋找下欄欄位： 

    -    `sys.dm_exec_requests.status`  
    此欄顯示特定要求的狀態。 睡眠狀態通常表示 SPID 已完成執行，而且正在等候應用程式提交另一個查詢或批次。 可執行或正在執行的狀態表示 SPID 目前正在處理查詢。 下表提供各種狀態值的簡短說明。

    | 狀態 | 意義 |
    |:-|:-|
    | 背景 | SPID 正在執行背景工作，例如鎖死偵測、記錄寫入器或檢查點。 |
    | 休眠中 | SPID 目前未執行。 這通常表示 SPID 正在等候應用程式中的命令。 |
    | 執行中 | SPID 目前正在排程器上執行。 |
    | 可執行的 | SPID 位於排程器的可執行佇列中，並等候取得排程器時間。 |
    | 暫止 | SPID 正在等候資源，例如鎖定或閂鎖。 | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    此欄位會告訴您此會話中的開啟交易數目。 如果這個值大於0，則 SPID 位於開啟的交易中，而且可能持有交易內任何語句所取得的鎖定。

    -    `sys.dm_exec_requests.open_transaction_count`  
    同樣地，這個欄位會告訴您此要求中的開啟交易數目。 如果這個值大於0，則 SPID 位於開啟的交易中，而且可能持有交易內任何語句所取得的鎖定。

    -   `sys.dm_exec_requests.wait_type`、`wait_time` 和 `last_wait_type`  
    如果  `sys.dm_exec_requests.wait_type`   是 Null，則要求目前不會等候任何事物，而  `last_wait_type`   值會指出要求最後遇到的最後一個  `wait_type`   。 如需  `sys.dm_os_wait_stats` 最常見等候類型的詳細資訊和描述，請參閱 [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)。 您  `wait_time`   可以使用此值來判斷要求是否正在進行中。 當針對 sys.dm_exec_requests 資料表的查詢傳回的資料  `wait_time`   行值小於  `wait_time`   之前的 sys.dm_exec_requests 查詢中的值時，這表示已取得和釋放先前的鎖定，而且現在正在等候新的鎖定 (假設非零的 `wait_time`) 。 這可以藉由比較 `wait_resource`   sys.dm_exec_requests 輸出來驗證，這會顯示要求正在等候的資源。

    -   `sys.dm_exec_requests.wait_resource` 此欄位表示封鎖的要求正在等候的資源。 下表列出常見  `wait_resource`   的格式及其意義：

    | 資源 | [格式] | 範例 | 說明 | 
    |:-|:-|:-|:-|
    | Table | DatabaseID： ObjectID： IndexID | TAB：5:261575970:1 | 在此情況下，資料庫識別碼5是 pubs 範例資料庫，而物件識別碼261575970是標題資料表，1是叢集索引。 |
    | 頁面 | DatabaseID： FileID： PageID | 頁面：5:1:104 | 在此情況下，資料庫識別碼5為 pubs，檔案識別碼1是主要資料檔案，而頁面104是屬於 titles 資料表的頁面。 若要識別頁面所屬的 object_id，請使用動態管理函式 [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql)，傳入 DatabaseID、FileId、PageId 中的 `wait_resource` 。 | 
    | 答案 | DatabaseID： Hobt_id 索引鍵的 (雜湊值)  | 機碼： 5:72057594044284928 (3300a4f361aa)  | 在此情況下，資料庫識別碼5為 Pubs，Hobt_ID 72057594044284928 對應至 object_id 261575970 (標題資料表) 的 index_id 2。 使用 sys. 分割目錄檢視，將 hobt_id 與特定 index_id 和 object_id。 沒有任何方法可將索引鍵雜湊取消至特定的索引鍵值。 |
    | 資料列 | DatabaseID： FileID： PageID：插槽 (資料列)  | RID：5:1:104:3 | 在此情況下，資料庫識別碼5為 pubs，檔案識別碼1是主要資料檔案，頁面104是屬於標題資料表的頁面，而插槽3表示資料列在頁面上的位置。 |
    | 編譯  | DatabaseID： FileID： PageID：插槽 (資料列)  | RID：5:1:104:3 | 在此情況下，資料庫識別碼5為 pubs，檔案識別碼1是主要資料檔案，頁面104是屬於標題資料表的頁面，而插槽3表示資料列在頁面上的位置。 |

    -    `sys.dm_tran_active_transactions`[Sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV 包含可聯結至其他 dmv 的開啟交易相關資料，以完整瞭解等候認可或回復的交易。 使用下列查詢，以傳回已加入其他 Dmv （包括 [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)）的開啟交易資訊。 請考慮交易的目前狀態、 `transaction_begin_time` 和其他環境資料，以評估它是否可能是封鎖的來源。

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   其他資料行

        [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql)和[sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)的其餘資料行也可以提供問題根源的深入解析。 它們的實用性會根據問題的情況而有所不同。 例如，您可以判斷此問題是否只發生于特定的用戶端 (主機名稱) 、特定網路程式庫上的特定網路程式庫 (net_library) 、當 SPID 提交的最後一個批次是 `last_request_start_time` 在 sys.dm_exec_sessions 中、要求 `start_time` 在 sys.dm_exec_requests 中執行的時間長度等等。


## <a name="common-blocking-scenarios"></a>常見的封鎖案例

下表將常見的徵兆對應至可能的原因。  

`wait_type`、 `open_transaction_count` 和資料 `status` 行指的是[sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)所傳回的資訊， [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql)可能會傳回其他資料行。 「解析？」 資料行指出封鎖是否會自行解決，或是否應該透過命令來終止會話 `KILL` 。 如需詳細資訊，請參閱 [KILL (transact-sql) ](/sql/t-sql/language-elements/kill-transact-sql)。

| 案例 | Waittype | Open_Tran | 狀態 | 解決？ | 其他徵兆 |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | 可執行的 | 是，查詢完成時。 | 在 sys.dm_exec_sessions 中， **讀取**、 **cpu_time** 和/或 **memory_usage** 的資料行會隨著時間增加。 完成時，查詢的持續時間會很高。 |
| 2 | NULL | \>0 | 睡眠中 | 否，但可以終止 SPID。 | 這個 SPID 的擴充事件會話可能會出現注意信號，表示已發生查詢超時或取消。 |
| 3 | NULL | \>= 0 | 可執行的 | 不會。 在用戶端提取所有資料列或關閉連接之前，將無法解析。 SPID 可以被終止，但最多可能需要30秒的時間。 | 如果 open_transaction_count = 0，而 SPID 持有鎖定，但交易隔離等級為預設 (READ COMMMITTED) ，則這可能是原因。 |  
| 4 | 不定 | \>= 0 | 可執行的 | 不會。 在用戶端取消查詢或關閉連接之前，將無法解析。 Spid 可以被終止，但最多可能需要30秒的時間。 | 在封鎖鏈的標頭中，sys.dm_exec_sessions 的 **hostname** 資料行，將會與它所封鎖的其中一個 spid 相同。 |  
| 5 | NULL | \>0 | 復原 | 是。 | 這個 SPID 的「擴充事件」會話可能會出現注意信號，表示已發生查詢超時或取消，或只是已發出 rollback 語句。 |  
| 6 | NULL | \>0 | 睡眠中 | 最終。 當 Windows NT 判斷會話已不再處於使用中狀態時，Azure SQL Database 連線將會中斷。 | `last_request_start_time`Sys.dm_exec_sessions 中的值比目前時間更早。 |

下列案例將擴充這些案例。 

1.  執行長時間執行的查詢所造成的封鎖

    **解決方法**：這種封鎖問題的解決方法是尋找優化查詢的方式。 實際上，這個類別的封鎖問題可能只是效能問題，而且需要您採用這種方式。 如需針對特定執行緩慢查詢進行疑難排解的詳細資訊，請參閱 [如何針對 SQL Server 上的執行緩慢查詢進行疑難排解](/troubleshoot/sql/performance/troubleshoot-slow-running-queries)。 如需詳細資訊，請參閱[監視及調整效能](/sql/relational-databases/performance/monitor-and-tune-for-performance)。 

    SSMS 中來自 [查詢存放區](/sql/relational-databases/performance/best-practice-with-the-query-store) 的報告也是強烈建議且有用的工具，可用於識別成本最高的查詢、最佳的執行計畫。 此外，請參閱 Azure SQL database Azure 入口網站的 [智慧型效能](intelligent-insights-overview.md) 區段，包括 [查詢效能深入解析](query-performance-insight-use.md)。

    如果您有長時間執行的查詢會封鎖其他使用者且無法優化，請考慮將它從 OLTP 環境移至專用的報告系統，也就是 [資料庫的同步唯讀複本](read-scale-out.md)。

1.  具有未認可之交易的睡眠 SPID 造成的封鎖

    這種類型的封鎖通常可以藉由正在睡眠或等候命令的 SPID 來識別，但其交易嵌套層級 (`@@TRANCOUNT` ， `open_transaction_count` 從 sys.dm_exec_requests) 大於零。 如果應用程式遇到查詢超時，或在未發出必要的 ROLLBACK 和/或 COMMIT 語句數目的情況下發出取消，就會發生這種情況。 當 SPID 收到查詢超時或取消時，它會終止目前的查詢和批次，但不會自動回復或認可交易。 應用程式會負責這項工作，因為 Azure SQL Database 無法假設整個交易都必須回復，因為系統會取消單一查詢。 在擴充的事件會話中，查詢超時或取消將會顯示為 SPID 的注意訊號事件。

    若要示範未認可的明確交易，請發出下列查詢：

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    然後，在同一個視窗中執行此查詢：
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    第二個查詢的輸出指出交易嵌套層級為一。 在交易認可或回復之前，仍會保留在交易中取得的所有鎖定。 如果應用程式明確開啟並認可交易，則通訊或其他錯誤可能會讓會話及其交易處於開啟狀態。 

    根據 sys.dm_tran_active_transactions 使用上述腳本來識別目前未認可的交易。

    **解決方法**：

     -   此外，這個類別的封鎖問題也可能是效能問題，而且需要您採用這種方式。 如果查詢執行時間可能會降低，則不會發生查詢超時或取消。 應用程式必須能夠處理所發生的超時或取消案例，但您也可以從檢查查詢效能中獲益。 
     
     -    應用程式必須適當地管理交易的嵌套層級，否則可能會導致在以這種方式取消查詢之後發生封鎖問題。 請考慮下列事項：  

            *    在用戶端應用程式的錯誤處理常式中，執行 `IF @@TRANCOUNT > 0 ROLLBACK TRAN` 下列任何錯誤，即使用戶端應用程式不認為交易已開啟。 檢查是否有開啟的交易是必要的，因為在批次期間呼叫的預存程式可能會在沒有用戶端應用程式知識的情況下啟動交易。 某些情況（例如取消查詢）會讓程式無法執行目前的語句，所以即使程式具有檢查 `IF @@ERROR <> 0` 和中止交易的邏輯，也不會在這種情況下執行此復原程式碼。  
            *    如果在開啟連接的應用程式中使用連接共用，並在釋出連接回集區之前執行少量的查詢（例如 Web 應用程式），則暫時停用連接共用可能有助於減輕問題，直到用戶端應用程式修改為適當地處理錯誤為止。 藉由停用連接共用，釋放連接將會造成 Azure SQL Database 連接的實體中斷連線，而導致伺服器回復任何開啟的交易。  
            *    用於 `SET XACT_ABORT ON` 連接，或在任何開始交易的預存程式中使用，且不會在發生錯誤時清除。 當執行階段錯誤發生時，此設定會中止所有開啟的交易，並將控制權傳回給用戶端。 如需詳細資訊，請參閱 [XACT_ABORT (transact-sql) ](/sql/t-sql/statements/set-xact-abort-transact-sql)。
    > [!NOTE]
    > 連接不會重設，直到從連接集區重複使用為止，因此使用者可以開啟交易，然後釋放連接集區的連接，但它可能不會重複使用幾秒鐘，在這段期間，交易仍會保持開啟。 如果未重複使用連接，則會在連接逾時並從連接集區中移除時，中止交易。 因此，用戶端應用程式可以在其錯誤處理常式中中止交易，或使用 `SET XACT_ABORT ON` 來避免這種潛在的延遲，這是最理想的做法。

    > [!CAUTION]
    > 之後 `SET XACT_ABORT ON` ，將不會執行導致錯誤之語句後面的 t-sql 語句。 這可能會影響現有程式碼的預期流程。

1.  因對應的用戶端應用程式未提取所有結果資料列完成的 SPID 所造成的封鎖

    將查詢傳送至伺服器之後，所有的應用程式都必須立即將所有結果資料列提取到完成。 如果應用程式不會提取所有結果資料列，則鎖定可以保留在資料表上，封鎖其他使用者。 如果您使用的應用程式會以透明方式將 SQL 語句提交至伺服器，則應用程式必須提取所有結果資料列。 如果未 (，且無法設定為) ，您可能無法解決封鎖問題。 若要避免這個問題，您可以將效能不佳的應用程式限制在報告或決策支援資料庫中。
    
    > [!NOTE]
    > 請參閱連接到 Azure SQL Database 之應用程式的 [重試邏輯指引](/azure/azure-sql/database/troubleshoot-common-connectivity-issues#retry-logic-for-transient-errors) 。 
    
    **解決** 方式：必須重新撰寫應用程式，才能將結果的所有資料列提取到完成。 這並不會排除在查詢的 [ORDER BY 子句中使用 OFFSET 和 FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) 來執行伺服器端分頁的規則。

1.  處於復原狀態的 SPID 造成的封鎖

    在使用者自訂交易以外終止或取消的資料修改查詢，將會回復。 這也可能是因為用戶端網路會話中斷連線，或選取要求作為鎖死犧牲者的副作用。 這通常可以藉由觀察 sys.dm_exec_requests 的輸出來識別，這可能表示 ROLLBACK **命令**，而 percent_complete 資料 **行** 可能會顯示進度。 

    由於2019中引進的 [加速資料庫復原功能](../accelerated-database-recovery.md) ，冗長的復原應該很罕見。
    
    **解決方法**：等候 SPID 完成回復所做的變更。 

    若要避免這種情況，請不要在 OLTP 系統上執行大型批次寫入作業，或在忙碌時間內執行索引建立或維護作業。 可能的話，請在活動較少的期間執行這類作業。

1.  孤立連接所造成的封鎖

    如果用戶端應用程式會攔截錯誤或重新開機用戶端工作站，則在某些情況下可能不會立即取消伺服器的網路會話。 從 Azure SQL Database 的觀點來看，用戶端仍然存在，而且可能仍會保留任何取得的鎖定。 如需詳細資訊，請參閱 [如何疑難排解 SQL Server 中的孤立連接](/sql/t-sql/language-elements/kill-transact-sql#remarks)。 

    **解決方案**：如果用戶端應用程式已中斷連線，而未適當地清除其資源，您可以使用命令來終止 SPID `KILL` 。 此 `KILL` 命令會採用 SPID 值做為輸入。 例如，若要終止 SPID 99，請發出下列命令：

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>請參閱

* [Azure SQL Database 和 Azure SQL 受控執行個體監視和效能微調](/monitor-tune-overview.md)
* [使用查詢存放區來監視效能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [交易鎖定與資料列版本設定指南](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [SET TRANSACTION ISOLATION LEVEL](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [快速入門：SQL Server 中的延伸事件](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Intelligent Insights 使用 AI 來針對資料庫的效能進行監視和疑難排解](intelligent-insights-overview.md)

## <a name="learn-more"></a>深入了解

* [Azure SQL Database：使用自動調整改善效能調整](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [使用自動調整來改善 Azure SQL Database 效能](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [透過 Azure SQL 提供一致的效能](/learn/modules/azure-sql-performance/)
* [針對 Azure SQL Database 和 Azure SQL 受控執行個體的連線能力問題和其他錯誤進行疑難排解](troubleshoot-common-errors-issues.md)
* [暫時性錯誤處理](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
