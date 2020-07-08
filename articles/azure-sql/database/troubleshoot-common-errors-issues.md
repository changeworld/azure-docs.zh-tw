---
title: 針對 Azure SQL Database 常見的連線問題進行疑難排解
description: 提供針對 Azure SQL Database 連線問題進行疑難排解，以及解決其他 Azure SQL Database 或 Azure SQL 受控執行個體特定問題的步驟
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: e1a018b06b7ee7230612d2ee6a582214a817547b
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985219"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>針對 Azure SQL Database 和 Azure SQL 受控執行個體的連線問題和其他錯誤進行疑難排解
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

當 Azure SQL Database 或 Azure SQL 受控執行個體的連接失敗時，您會收到錯誤訊息。 這些連線問題可能是因為重新設定、防火牆設定、連接逾時、不正確的登入資訊，或在[應用程式設計](develop-overview.md)過程中套用最佳作法和設計指導方針所造成。 此外，如果已達到部分 Azure SQL Database 或 SQL 受控執行個體資源的上限，您就無法再連接。

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>暫時性錯誤訊息（40197、40613及其他）

Azure 基礎結構能夠在 SQL Database 服務出現繁重的工作負載時動態重新設定伺服器。  此動態行為可能會導致您的用戶端程式失去其與資料庫或實例的連接。 此類錯誤情況稱為「暫時性錯誤」 **。 資料庫重新設定事件由於規劃的事件 (例如，軟體升級) 或未規劃的事件 (例如，處理序損毀或負載平衡) 而發生。 大部分的重新設定事件通常只是短期的，至多應在不到 60 秒的時間完成。 不過，這些事件可能偶爾會需要更長時間才能完成，例如當大型交易導致長時間執行的復原時。 下表列出當連接到時，應用程式可以接收的各種暫時性錯誤 SQL Database

### <a name="list-of-transient-fault-error-codes"></a>暫時性錯誤錯誤碼清單

| 錯誤碼 | Severity | Description |
| ---:| ---:|:--- |
| 4060 |16 |無法開啟登入所要求的資料庫 "%.&#x2a;ls"。 登入失敗。 如需詳細資訊，請參閱[錯誤4000到 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |服務處理您的要求時發生錯誤。 請再試一次。 錯誤代碼 %d。<br/><br/>當服務因為軟體或硬體升級、硬體故障或任何其他容錯移轉問題而關閉時，您會收到這個錯誤。 內嵌在錯誤 40197 訊息中的錯誤代碼 (%d) 提供發生的失敗或容錯移轉種類的其他資訊。 錯誤代碼會內嵌在錯誤 40197 的訊息錯誤範例包括 40020、40143、40166 和 40540。<br/><br/>重新連接會自動將您連線到資料庫的狀況良好複本。 您的應用程式必須攔截錯誤 40197、記錄訊息中內嵌的錯誤碼 (%d) 以進行疑難排解，並嘗試重新連接到 SQL Database 直到資源可供使用，並再次建立您的連線。 如需詳細資訊，請參閱[暫時性錯誤](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults)。|
| 40501 |20 |服務目前忙碌中。 在 10 秒後重試要求。 事件識別碼：%ls。 程式碼：%d。 如需詳細資訊，請參閱： <br/>&bull;&nbsp;[邏輯 SQL server 資源限制](resource-limits-logical-server.md)<br/>&bull;&nbsp;[單一資料庫以 DTU 為基礎的限制](service-tiers-dtu.md)<br/>&bull;彈性集區 &nbsp; [的以 DTU 為基礎的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp;[單一資料庫以 vCore 為基礎的限制](resource-limits-vcore-single-databases.md)<br/>&bull;彈性集區 &nbsp; [的 vCore 為基礎的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL 受控執行個體資源限制](../managed-instance/resource-limits.md)。|
| 40613 |17 |資料庫 '%.&#x2a;ls' (在伺服器 '%.&#x2a;ls' 上) 目前無法使用。 請稍後重試連接。 如果問題持續發生，請連絡客戶支援服務，並提供工作階段追蹤識別碼 '%.&#x2a;ls'。<br/><br/> 如果已經為資料庫建立現有的專用管理員連接（DAC），就可能會發生這個錯誤。 如需詳細資訊，請參閱[暫時性錯誤](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults)。|
| 49918 |16 |無法處理要求。 資源不足，無法處理要求。<br/><br/>服務目前忙碌中。 請稍後再重試要求。 如需詳細資訊，請參閱： <br/>&bull;&nbsp;[邏輯 SQL server 資源限制](resource-limits-logical-server.md)<br/>&bull;&nbsp;[單一資料庫以 DTU 為基礎的限制](service-tiers-dtu.md)<br/>&bull;彈性集區 &nbsp; [的以 DTU 為基礎的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp;[單一資料庫以 vCore 為基礎的限制](resource-limits-vcore-single-databases.md)<br/>&bull;彈性集區 &nbsp; [的 vCore 為基礎的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL 受控執行個體資源限制](../managed-instance/resource-limits.md)。 |
| 49919 |16 |無法處理建立或更新要求。 訂用帳戶 "%ld" 太多建立或更新作業進行中。<br/><br/>服務正忙於處理多個建立或更新您訂用帳戶或伺服器的要求。 要求目前已封鎖以求資源最佳化。 查詢 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以查看暫止的作業。 等待直到暫止的建立或更新要求完成，或刪除您的其中一個暫止要求，稍後再重試您的要求。 如需詳細資訊，請參閱： <br/>&bull;&nbsp;[邏輯 SQL server 資源限制](resource-limits-logical-server.md)<br/>&bull;&nbsp;[單一資料庫以 DTU 為基礎的限制](service-tiers-dtu.md)<br/>&bull;彈性集區 &nbsp; [的以 DTU 為基礎的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp;[單一資料庫以 vCore 為基礎的限制](resource-limits-vcore-single-databases.md)<br/>&bull;彈性集區 &nbsp; [的 vCore 為基礎的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL 受控執行個體資源限制](../managed-instance/resource-limits.md)。 |
| 49920 |16 |無法處理要求。 訂用帳戶 "%ld" 太多作業進行中。<br/><br/>服務正忙於處理這個訂用帳戶的多個要求。 要求目前已封鎖以求資源最佳化。 查詢 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以查看作業狀態。 等候直到暫止的要求已完成，或刪除您其中一個暫止要求，稍後再重試您的要求。 如需詳細資訊，請參閱： <br/>&bull;&nbsp;[邏輯 SQL server 資源限制](resource-limits-logical-server.md)<br/>&bull;&nbsp;[單一資料庫以 DTU 為基礎的限制](service-tiers-dtu.md)<br/>&bull;彈性集區 &nbsp; [的以 DTU 為基礎的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp;[單一資料庫以 vCore 為基礎的限制](resource-limits-vcore-single-databases.md)<br/>&bull;彈性集區 &nbsp; [的 vCore 為基礎的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL 受控執行個體資源限制](../managed-instance/resource-limits.md)。 |
| 4221 |16 |登入 read-secondary 失敗，因為 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING' 上的等候時間很長。 無法使用複本進行登入，因為在複本回收時執行中之交易的資料列版本已遺失。 將主要複本上的使用中交易復原或認可，就可以解決問題。 避免在主要伺服器上的冗長寫入交易，就可讓此條件發生率降至最低。 |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解決暫時性連線問題的步驟

1. 檢查 [Microsoft Azure 服務儀表板](https://azure.microsoft.com/status) ，以取得應用程式報告錯誤期間發生的任何已知中斷。
2. 連接到雲端服務的應用程式 (例如 Azure SQL Database) 應該預期定期的重新設定事件，並實作應用程式重試邏輯來處理這些錯誤，而不是將這些錯誤當做應用程式錯誤呈現給使用者。
3. 由於資料庫接近其資源限制，因此似乎是暫時性連線問題。 請參閱[資源限制](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached)。
4. 如果連線問題繼續發生，或如果您的應用程式發生錯誤的持續時間超過 60 秒，或如果您在一天當中，看到錯誤多次發生，請在 [Azure 支援](https://azure.microsoft.com/support/options)網站上選取 [取得支援]****，來提出 Azure 支援要求。

#### <a name="implementing-retry-logic"></a>實作重試邏輯

強烈建議您的用戶端程式具有重試邏輯，以便在給予暫時性錯誤一些時間自行修正後，可以嘗試重新建立連線。  我們建議您在您第一次重試前延遲 5 秒鐘。 在少於 5 秒的延遲後重試，雲端服務會有超過負荷的風險。 對於後續每次重試，延遲應以指數方式成長，最大值為 60 秒。

如需重試邏輯的程式碼範例，請參閱：

- [使用 ADO.NET 彈性地連接到 SQL](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [使用 PHP 彈性地連接到 SQL](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

如需在應用程式中處理暫時性錯誤的詳細資訊，請參閱針對[暫時性連線錯誤進行疑難排解以 SQL Database](troubleshoot-common-connectivity-issues.md)

「連線共用」 [（ADO.NET）](https://msdn.microsoft.com/library/8xx3tyca.aspx)中提供了使用 ADO.NET 之用戶端的*封鎖期間*討論。

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>建立與伺服器之間的連線時發生網路相關或實例特定的錯誤

如果應用程式無法連接到伺服器，就會發生此問題。

若要解決此問題，請嘗試[修正常見](#steps-to-fix-common-connection-issues)的連線問題一節中的步驟（以顯示的順序）。

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>找不到或無法存取伺服器/實例（錯誤26、40、10053）

### <a name="error-26-error-locating-server-specified"></a>錯誤26：尋找指定的伺服器時發生錯誤

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>錯誤40：無法開啟與伺服器的連接

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>錯誤10053：從伺服器接收結果時發生傳輸層級錯誤

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

如果應用程式無法連接到伺服器，就會發生這些問題。

若要解決這些問題，請嘗試[修正常見](#steps-to-fix-common-connection-issues)的連線問題一節中的步驟（以顯示的順序）。

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>因為防火牆問題而無法連接到伺服器

### <a name="error-40615-cannot-connect-to--servername-"></a>錯誤40615：無法連接到 < servername >

若要解決此問題，請[透過 Azure 入口網站在 SQL Database 上設定防火牆設定](firewall-configure.md)。

### <a name="error-5-cannot-connect-to--servername-"></a>錯誤5：無法連接到 < servername >

若要解決此問題，請確定在用戶端與網際網路之間的所有防火牆上，埠1433已開放供輸出連線之用。

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>無法登入伺服器（錯誤18456、40531）

### <a name="login-failed-for-user--user-name-"></a>使用者 ' < 使用者名稱 > ' 的登入失敗

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

若要解決此問題，請洽詢您的服務系統管理員，為您提供有效的使用者名稱和密碼。

通常，服務系統管理員可以使用下列步驟來新增登入認證：

1. 使用 SQL Server Management Studio （SSMS）登入伺服器。
2. 執行下列 SQL 查詢，以檢查登入名稱是否已停用：

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. 如果對應的名稱已停用，使用下列陳述式啟用它：

   ```sql
   Alter login <User name> enable
   ```

4. 如果 SQL 登入使用者名稱不存在，請遵循下列步驟來建立：

   1. 在 SSMS 中，按兩下 [**安全性**] 將它展開。
   2. 以滑鼠右鍵按一下 [登入]****，然後選取 [新增登入]****。
   3. 在產生的腳本中加上預留位置，編輯並執行下列 SQL 查詢：

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. 按兩下 [資料庫]****。
6. 選取您想要授與使用者權限的資料庫。
7. 按兩下 [安全性]****。
8. 以滑鼠右鍵按一下 [**使用者**]，然後選取 [**新增使用者**]。
9. 在產生的腳本中加上預留位置，編輯並執行下列 SQL 查詢：

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > 您也可以使用 `sp_addrolemember` ，將特定使用者對應至特定的資料庫角色。

如需詳細資訊，請參閱[管理 Azure SQL Database 中的資料庫和登入](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)。

## <a name="connection-timeout-expired-errors"></a>連接逾時過期錯誤

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>SqlClient. SqlException （0x80131904）：連接逾時已過期

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>SqlClient. SqlException （0x80131904）： Timeout 已過期

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>EntityException：在開啟時，基礎提供者失敗

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>無法連接到 < 伺服器名稱 >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

這些例外狀況可能是因為連接或查詢問題所造成。 若要確認此錯誤是因連線問題所造成，請參閱[確認是否因連線問題而造成錯誤](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)。

由於應用程式無法連接到伺服器，因此會發生連接逾時。 若要解決此問題，請嘗試[修正常見](#steps-to-fix-common-connection-issues)的連線問題一節中的步驟（以顯示的順序）。

## <a name="resource-governance-errors"></a>資源管理錯誤

### <a name="error-10928-resource-id-d"></a>錯誤10928：資源識別碼：% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

若要解決此問題，請嘗試下列其中一種方法：

- 確認是否有長時間執行的查詢。

  > [!NOTE]
  > 這是可能無法解決問題的極簡方法。

1. 執行下列 SQL 查詢來檢查 [ [sys.databases] dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)查看是否有任何封鎖的要求：

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. 判斷 head 封鎖程式的**輸入緩衝區**。
3. 調整 [head 封鎖器] 查詢。

   如需深入的疑難排解程式，請參閱[我的查詢在雲端中是否正常運作？](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。

如果資料庫一直達到其限制，但卻要解決封鎖和長時間執行的查詢，請考慮升級至具有更多資源[版本](https://azure.microsoft.com/pricing/details/sql-database/)的版本）。

如需動態管理檢視的詳細資訊，請參閱[系統動態管理檢視](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)。

如需資料庫限制的詳細資訊，請參閱[伺服器的 SQL Database 資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)。

### <a name="error-10929-resource-id-1"></a>錯誤10929：資源識別碼：1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>錯誤40501：服務目前忙碌中

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

這是引擎節流錯誤，指出已超過資源限制。

如需資源限制的詳細資訊，請參閱[邏輯 SQL server 資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)。

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>錯誤40544：資料庫已達到其大小配額

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

當資料庫已達到其大小配額時，就會發生此錯誤。

下列步驟可協助您解決問題，或提供您其他選項：

1. 使用 [Azure 入口網站中的 [儀表板]，檢查資料庫的目前大小。

   > [!NOTE]
   > 若要找出哪些資料表耗用最多的空間，因此可能會有清除的候選項目，請執行下列 SQL 查詢：

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. 如果目前的大小不超過您的版本支援的大小上限，您可以使用 ALTER DATABASE 來增加 MAXSIZE 設定。
3. 如果資料庫已經超過您的版本支援的大小上限，請嘗試下列一或多個步驟：

   - 執行一般的資料庫清除活動。 例如，使用截斷/刪除來清除不必要的資料，或使用 SQL Server Integration Services （SSIS）或大量複製程式（bcp）公用程式將資料移出。
   - 資料分割或刪除資料、卸除索引，或參閱可能解決方式的文件。
   - 如需資料庫調整，請參閱[調整單一資料庫資源](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale)和[調整彈性集區資源](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)。

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>錯誤40549：會話已終止，因為您有長時間執行的交易

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

如果您重複遇到此錯誤，請嘗試依照下列步驟來解決此問題：

1. 檢查 [sys.databases dm_exec_requests] 視圖，查看是否有 [total_elapsed_time] 資料行具有較高值的任何已開啟會話。 執行下列 SQL 腳本來執行這項檢查：

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. 判斷長時間執行查詢的輸入緩衝區。
3. 調整查詢。

也請考慮批次處理您的查詢。 如需批次處理的詳細資訊，請參閱[如何使用批次處理來改善 SQL Database 應用程式效能](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)。

如需深入的疑難排解程式，請參閱[我的查詢在雲端中是否正常運作？](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>錯誤40551：已終止會話，因為過度使用 TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

若要暫時解決此問題，請遵循下列步驟：

1. 變更查詢以減少臨時表空間的使用方式。
2. 不再需要暫存物件之後，請將它卸載。
3. 截斷資料表或移除未使用的資料表。

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>錯誤40552：因為交易記錄檔空間使用量過高，所以會話已終止

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

若要解決此問題，請嘗試下列方法：

- 此問題可能是因為插入、更新或刪除作業所造成。
嘗試藉由執行批次處理或分割成多個較小的交易，以減少立即操作的資料列數目。
- 此問題可能是因為索引重建作業所造成。 若要解決此問題，請確定資料表中受影響的資料列數目 * （以位元組為單位更新的平均欄位大小 + 80） < 2 gb。

  > [!NOTE]
  > 對於索引重建，所更新欄位的平均大小應該由平均索引大小所取代。

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>錯誤40553：因為記憶體使用量過高，所以已終止會話

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

若要解決此問題，請嘗試優化查詢。

如需深入的疑難排解程式，請參閱[我的查詢在雲端中是否正常運作？](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。

### <a name="table-of-additional-resource-governance-error-messages"></a>其他資源治理錯誤訊息的資料表

| 錯誤碼 | Severity | Description |
| ---:| ---:|:--- |
| 10928 |20 |資源識別碼：%d。 資料庫的 %s 限制是 %d，且已達到。 如需詳細資訊，請參閱[單一和集區資料庫的 SQL Database 資源限制](resource-limits-logical-server.md)。<br/><br/>資源識別碼可指出已達到限制的資源。 對於背景工作執行緒，資源識別碼 = 1。 對於工作階段，資源識別碼 = 2。<br/><br/>如需有關此錯誤以及其解決方法的詳細資訊，請參閱： <br/>&bull;&nbsp;[邏輯 SQL server 資源限制](resource-limits-logical-server.md)<br/>&bull;&nbsp;[單一資料庫以 DTU 為基礎的限制](service-tiers-dtu.md)<br/>&bull;彈性集區 &nbsp; [的以 DTU 為基礎的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp;[單一資料庫以 vCore 為基礎的限制](resource-limits-vcore-single-databases.md)<br/>&bull;彈性集區 &nbsp; [的 vCore 為基礎的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL 受控執行個體資源限制](../managed-instance/resource-limits.md)。 |
| 10929 |20 |資源識別碼：%d。 %s 最小保證是 %d，最大限制是 %d，而資料庫的目前使用量是 %d。 但伺服器目前太忙碌，無法針對此資料庫支援大於 %d 的要求。 資源識別碼可指出已達到限制的資源。 對於背景工作執行緒，資源識別碼 = 1。 對於工作階段，資源識別碼 = 2。 如需詳細資訊，請參閱： <br/>&bull;&nbsp;[邏輯 SQL server 資源限制](resource-limits-logical-server.md)<br/>&bull;&nbsp;[單一資料庫以 DTU 為基礎的限制](service-tiers-dtu.md)<br/>&bull;彈性集區 &nbsp; [的以 DTU 為基礎的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp;[單一資料庫以 vCore 為基礎的限制](resource-limits-vcore-single-databases.md)<br/>&bull;彈性集區 &nbsp; [的 vCore 為基礎的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL 受控執行個體資源限制](../managed-instance/resource-limits.md)。 <br/>或者，請稍後再試一次。 |
| 40544 |20 |資料庫已達到大小配額。 資料分割或刪除資料、卸除索引，或參閱可能解決方式的文件。 如需資料庫調整，請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。|
| 40549 |16 |工作階段已終止，因為您有長時間執行的交易。 請嘗試縮短您的交易。 如需批次處理的詳細資訊，請參閱[如何使用批次處理來改善 SQL Database 應用程式效能](../performance-improve-use-batching.md)。|
| 40550 |16 |已終止工作階段，因為它取得太多鎖定。 請嘗試在單一交易中讀取或修改較少的資料列。 如需批次處理的詳細資訊，請參閱[如何使用批次處理來改善 SQL Database 應用程式效能](../performance-improve-use-batching.md)。|
| 40551 |16 |已終止工作階段，因為它過度使用 `TEMPDB`。 請嘗試修改查詢，減少使用暫存資料表空間。<br/><br/>如果您是使用暫存物件，請在工作階段不再需要暫存物件時予以卸除，藉此節省 `TEMPDB` 資料庫的空間。 如需 SQL Database 中 tempdb 使用方式的詳細資訊，請參閱[SQL Database 中的 tempdb 資料庫](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。|
| 40552 |16 |已終止工作階段，因為它過度使用交易記錄檔空間。 請嘗試在單一交易中修改較少的資料列。 如需批次處理的詳細資訊，請參閱[如何使用批次處理來改善 SQL Database 應用程式效能](../performance-improve-use-batching.md)。<br/><br/>如果您使用 `bcp.exe` 公用程式或 `System.Data.SqlClient.SqlBulkCopy` 類別執行大量插入，請嘗試使用 `-b batchsize` 或 `BatchSize` 選項來限制每一筆交易中要複製到伺服器的資料列數目。 如果您要使用 `ALTER INDEX` 陳述式重建索引，請嘗試使用 `REBUILD WITH ONLINE = ON` 選項。 如需 vCore 購買模型之交易記錄檔大小的詳細資訊，請參閱： <br/>&bull;&nbsp;[單一資料庫以 vCore 為基礎的限制](resource-limits-vcore-single-databases.md)<br/>&bull;彈性集區 &nbsp; [的 vCore 為基礎的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Azure SQL 受控執行個體資源限制](../managed-instance/resource-limits.md)。|
| 40553 |16 |已終止工作階段，因為它過度使用記憶體。 請嘗試修改查詢以處理較少的資料列。<br/><br/>減少 Transact-SQL 程式碼中的 `ORDER BY` 和 `GROUP BY` 作業數目，從而減少查詢的記憶體需求。 如需資料庫調整，請參閱[調整單一資料庫資源](single-database-scale.md)和[調整彈性集區資源](elastic-pool-scale.md)。|

## <a name="elastic-pool-errors"></a>彈性集區錯誤

下列錯誤與建立及使用彈性集區有關：

| 錯誤碼 | Severity | Description | 更正措施 |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |彈性集區已達到其儲存體限制。 彈性集區的儲存體使用量不能超過 (%d) MB。 當彈性集區達到儲存體限制時，嘗試將資料寫入資料庫。 如需資源限制的相關資訊，請參閱： <br/>&bull;彈性集區 &nbsp; [的以 DTU 為基礎的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull;彈性集區 &nbsp; [的 vCore 為基礎限制](resource-limits-vcore-elastic-pools.md)。 <br/> |請考慮盡可能增加彈性集區的 DTU 及/或儲存體，以提高其儲存體限制、減少彈性集區中個別資料庫所使用的儲存體量，或是從彈性集區移除資料庫。 如需彈性集區調整，請參閱[調整彈性集區資源](elastic-pool-scale.md)。|
| 10929 | 16 |%s 最小保證是 %d，最大限制是 %d，而資料庫的目前使用量是 %d。 但伺服器目前太忙碌，無法針對此資料庫支援大於 %d 的要求。 如需資源限制的相關資訊，請參閱： <br/>&bull;彈性集區 &nbsp; [的以 DTU 為基礎的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull;彈性集區 &nbsp; [的 vCore 為基礎限制](resource-limits-vcore-elastic-pools.md)。 <br/> 或者，請稍後再試一次。 每個資料庫的最小 DTU / vCore；每個資料庫的最大 DTU / vCore。 彈性集區中的所有資料庫並行背景工作 (要求) 總數試圖超過集區限制。 |請考慮盡可能增加彈性集區的 DTU 或 vCore，以提高其背景工作數的限制，或是從彈性集區移除資料庫。 |
| 40844 | 16 |伺服器 '%ls' 上的資料庫 '%ls' 是彈性集區中的 '%ls' 版資料庫，且無法有連續複製關聯性。  |N/A |
| 40857 | 16 |找不到伺服器 '%ls' 的彈性集區，彈性集區名稱: '%ls'。 指定的彈性集區不存在於指定的伺服器中。 | 請提供有效的彈性集區名稱。 |
| 40858 | 16 |彈性集區 '%ls' 已存在於伺服器 '%ls' 中。 指定的彈性集區已存在於指定的伺服器中。 | 請提供新的彈性集區名稱。 |
| 40859 | 16 |彈性集區不支援服務層級 '%ls'。 指定的服務層級不支援彈性集區佈建。 |請提供正確的版本，或者將服務層級保留空白，以使用預設的服務層級。 |
| 40860 | 16 |彈性集區 '%ls' 和服務目標 '%ls' 的組合無效。 如果資源類型指定為 'ElasticPool'，才能同時指定彈性集區和服務層級。 |請指定正確的彈性集區和服務層級組合。 |
| 40861 | 16 |資料庫版本 '%.ls' 不能和彈性集區服務層級 ('%.ls') 不同**。 資料庫版本和彈性集區服務層級不同。 |請勿指定與彈性集區服務層級不同的資料庫版本。  請注意，不需要指定資料庫版本。 |
| 40862 | 16 |指定彈性集區服務目標時，必須指定彈性集區名稱。 彈性集區服務目標不是彈性集區的唯一識別依據。 |使用彈性集區服務目標時，請指定彈性集區名稱。 |
| 40864 | 16 |服務層級 '%.*ls' 的彈性集區 DTU 數必須至少為 (%d) 個 DTU。 試圖將彈性集區的 DTU 數設為低於最小限制。 |請重試將彈性集區的 DTU 數至少設為等於最小限制。 |
| 40865 | 16 |服務層級 '%.*ls' 的彈性集區 DTU 數不可超過 (%d) 個 DTU。 試圖將彈性集區的 DTU 數設為高於最大限制。 |請重試將彈性集區的 DTU 數設為低於最大限制。 |
| 40867 | 16 |服務層級 '%.*ls' 的每個資料庫最大 DTU 必須至少為 (%d)。 試圖將每個資料庫的最大 DTU 設為低於支援的限制。 | 請考慮使用可支援所需設定的彈性集區服務層級。 |
| 40868 | 16 |服務層級 '%.*ls' 的每個資料庫最大 DTU 不可超過 (%d)。 試圖將每個資料庫的最大 DTU 設為超過支援的限制。 | 請考慮使用可支援所需設定的彈性集區服務層級。 |
| 40870 | 16 |服務層級 '%.*ls' 的每個資料庫最小 DTU 不可超過 (%d)。 試圖將每個資料庫的最小 DTU 設為超過支援的限制。 | 請考慮使用可支援所需設定的彈性集區服務層級。 |
| 40873 | 16 |資料庫數目 (%d) 和每個資料庫的最小 DTU (%d) 不能超過彈性集區的 DTU 數 (%d)。 試圖針對彈性集區中的資料庫指定最小 DTU 而超過彈性集區的 DTU 數。 | 請考慮增加彈性集區的 DTU 數，或減少每個資料庫的最小 DTU，或是減少彈性集區中的資料庫數目。 |
| 40877 | 16 |除非不包含任何資料庫，否則無法刪除彈性集區。 彈性集區包含一或多個資料庫，因此無法刪除。 |若要刪除彈性集區，請移除其中的資料庫。 |
| 40881 | 16 |彈性集區 '%.*ls' 已達到其資料庫計數上限。  如果彈性集區的 DTU 為 (%d) 個，則彈性集區的資料庫計數上限不能超過 (%d)。 當達到彈性集區的資料庫計數上限時，試圖建立資料庫，或將資料庫加入至彈性集區。 | 請考慮盡可能增加彈性集區的 DTU 數，以提高其資料庫限制，或是從彈性集區移除資料庫。 |
| 40889 | 16 |不能減少彈性集區 '%.*ls' 的 DTU 數或儲存體限制，因為這麼做會無法提供足夠的儲存空間給位於其中的資料庫。 試圖將彈性集區的儲存體限制減少為低於其儲存體使用量。 | 請考慮減少彈性集區中之個別資料庫的儲存體使用量，或從集區中移除資料庫，以便減少集區的 DTU 數或儲存體限制。 |
| 40891 | 16 |每個資料庫的最小 DTU (%d) 不能超過每個資料庫的最大 DTU (%d)。 試圖將每個資料庫的最小 DTU 設為超過每個資料庫的最大 DTU。 |請確定每個資料庫的最小 DTU 並未超過每個資料庫的最大 DTU。 |
| TBD | 16 |彈性集區中個別資料庫的儲存體大小，不能超過 '%.*ls' 服務層級彈性集區所允許的大小上限。 資料庫的大小上限超過彈性集區服務層級所允許的大小上限。 |請將資料庫的大小上限設定在彈性集區服務層級所允許的大小上限內。 |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>無法開啟登入所要求的資料庫「主要」。 登入失敗

此問題發生的原因是帳戶沒有存取 master 資料庫的許可權。 但是根據預設，SQL Server Management Studio （SSMS）會嘗試連接到 master 資料庫。

若要解決此問題，請依照下列步驟執行︰

1. 在 SSMS 的登入畫面上，選取 [**選項**]，然後選取 [**連接屬性**]。
2. 在 [**連接到資料庫]** 欄位中，輸入使用者的預設資料庫名稱做為預設登入資料庫，然後選取 **[連接]**。

   ![連線內容](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>確認錯誤是否因連線問題所造成

若要確認錯誤是否因連線問題所造成，請檢查顯示呼叫以開啟連接的畫面格的堆疊追蹤（請注意**SqlConnection**類別的參考）：

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

當查詢問題觸發例外狀況時，您會注意到類似下列的呼叫堆疊（請注意**SqlCommand**類別的參考）。 在此情況下，請[調整您的查詢](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

如需微調效能的其他指引，請參閱下列資源：

- [如何維護 Azure SQL 索引和統計資料](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [在 Azure SQL Database 中手動微調查詢效能](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [使用動態管理檢視來監視效能 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [操作 Azure SQL Database 中的查詢存放區](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>修復常見連線問題的步驟

1. 請確定已啟用 TCP/IP 做為應用程式伺服器上的用戶端通訊協定。 如需詳細資訊，請參閱[設定用戶端通訊協定](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols)。 在未安裝 SQL 工具的應用程式伺服器上，藉由執行**cliconfg.exe** （SQL Server 用戶端網路公用程式）來確認是否已啟用 tcp/ip。
2. 請檢查應用程式的連接字串，確定它已正確設定。 例如，請確定連接字串指定了正確的埠（1433）和完整的伺服器名稱。
請參閱[取得連接資訊](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information)。
3. 請嘗試增加連接逾時值。 我們建議使用至少30秒的連接逾時。
4. 使用[SQL Server Management Studio （SSMS）](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)、UDL 檔案、ping 或 telnet，測試應用程式伺服器與 Azure SQL Database 之間的連線能力。 如需詳細資訊，請參閱針對連線問題[進行疑難排解](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)和[連接問題診斷](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)。

   > [!NOTE]
   > 做為疑難排解步驟，您也可以在不同的用戶端電腦上測試連線能力。

5. 最佳做法是確保重試邏輯已就緒。 如需重試邏輯的詳細資訊，請參閱針對[暫時性錯誤和連接錯誤進行疑難排解以 SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)。

如果這些步驟無法解決您的問題，請嘗試收集更多資料，然後聯絡支援人員。 如果您的應用程式是雲端服務，請啟用記錄功能。 此步驟會傳回失敗的 UTC 時間戳記。 此外，SQL Database 會傳回追蹤識別碼。 [Microsoft 客戶支援服務](https://azure.microsoft.com/support/options/)可以使用此資訊。

如需有關如何啟用記錄的詳細資訊，請參閱[在 Azure App Service 中啟用應用程式的診斷記錄](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)。

## <a name="next-steps"></a>後續步驟

- [Azure SQL Database 連接架構](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
- [Azure SQL Database 和 Azure Synapse 分析網路存取控制](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
