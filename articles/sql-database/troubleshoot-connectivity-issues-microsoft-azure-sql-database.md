---
title: 解決 SQL 資料庫的常見連接問題
description: 提供解決 Azure SQL 資料庫連接問題和解決其他 SQL 資料庫特定問題的步驟
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: 578d076dfc2fc383051c0c3a8528cdbb0fbcdb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208789"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>對 Microsoft Azure SQL Database 的連線問題和其他問題進行疑難排解

Azure SQL Database 的連線失敗時，您會收到錯誤訊息。 這些連接問題可能由 Azure SQL 資料庫重新配置、防火牆設置、連接逾時、不正確的登錄資訊或[應用程式設計](sql-database-develop-overview.md)過程中未能應用最佳實踐和設計指南引起。 此外，如果達到某些 Azure SQL 資料庫資源的最大限制，則無法連接到 Azure SQL 資料庫。

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>瞬態故障錯誤訊息 （40197， 40613 等）

Azure 基礎結構能夠在 SQL Database 服務出現繁重的工作負載時動態重新設定伺服器。  此動態行為可能會導致您的用戶端程式遺失其 SQL Database 連接。 此類錯誤情況稱為「暫時性錯誤」 **。 資料庫重新設定事件由於規劃的事件 (例如，軟體升級) 或未規劃的事件 (例如，處理序損毀或負載平衡) 而發生。 大部分的重新設定事件通常只是短期的，至多應在不到 60 秒的時間完成。 不過，這些事件可能偶爾會需要更長時間才能完成，例如當大型交易導致長時間執行的復原時。 下表列出了應用程式在連接到 SQL 資料庫時可以收到的各種瞬態錯誤

### <a name="list-of-transient-fault-error-codes"></a>瞬態故障錯誤代碼清單


| 錯誤碼 | Severity | 描述 |
| ---:| ---:|:--- |
| 4060 |16 |無法開啟登入所要求的資料庫 "%.&#x2a;ls"。 登入失敗。 有關詳細資訊，請參閱錯誤[4000 到 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |服務處理您的要求時發生錯誤。 請再試一次。 錯誤代碼 %d。<br/><br/>當服務因為軟體或硬體升級、硬體故障或任何其他容錯移轉問題而關閉時，您會收到這個錯誤。 錯誤訊息 40197] 中嵌入的錯誤代碼 （%d） 提供有關發生的故障或容錯移轉類型的其他資訊。 錯誤代碼會內嵌在錯誤 40197 的訊息錯誤範例包括 40020、40143、40166 和 40540。<br/><br/>重新連線到您的 SQL Database 伺服器時，會自動連線至健康情況良好的資料庫複本。 您的應用程式必須攔截錯誤 40197、記錄訊息中內嵌的錯誤碼 (%d) 以進行疑難排解，並嘗試重新連接到 SQL Database 直到資源可供使用，並再次建立您的連線。 有關詳細資訊，請參閱[瞬態錯誤](sql-database-connectivity-issues.md#transient-errors-transient-faults)。|
| 40501 |20 |服務目前忙碌中。 在 10 秒後重試要求。 事件識別碼：%ls。 程式碼：%d。 如需詳細資訊，請參閱 <br/>&bull;&nbsp;[資料庫伺服器資源限制](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp;[基於 DTU 的單資料庫的限制](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp;[基於 DTU 的彈性池限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[單個資料庫基於 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp;[基於 vCore 的彈性池限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[託管實例資源限制](sql-database-managed-instance-resource-limits.md)。|
| 40613 |17 |資料庫 '%.&#x2a;ls' (在伺服器 '%.&#x2a;ls' 上) 目前無法使用。 請稍後重試連接。 如果問題持續發生，請連絡客戶支援服務，並提供工作階段追蹤識別碼 '%.&#x2a;ls'。<br/><br/> 如果已建立到資料庫的現有專用管理員連接 （DAC），則可能發生此錯誤。 有關詳細資訊，請參閱[瞬態錯誤](sql-database-connectivity-issues.md#transient-errors-transient-faults)。|
| 49918 |16 |無法處理要求。 資源不足，無法處理要求。<br/><br/>服務目前忙碌中。 請稍後再重試要求。 如需詳細資訊，請參閱 <br/>&bull;&nbsp;[資料庫伺服器資源限制](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp;[基於 DTU 的單資料庫的限制](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp;[基於 DTU 的彈性池限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[單個資料庫基於 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp;[基於 vCore 的彈性池限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[託管實例資源限制](sql-database-managed-instance-resource-limits.md)。 |
| 49919 |16 |無法處理建立或更新要求。 訂用帳戶 "%ld" 太多建立或更新作業進行中。<br/><br/>服務正忙於處理多個建立或更新您訂用帳戶或伺服器的要求。 要求目前已封鎖以求資源最佳化。 查詢 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以查看暫止的作業。 等待直到暫止的建立或更新要求完成，或刪除您的其中一個暫止要求，稍後再重試您的要求。 如需詳細資訊，請參閱 <br/>&bull;&nbsp;[資料庫伺服器資源限制](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp;[基於 DTU 的單資料庫的限制](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp;[基於 DTU 的彈性池限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[單個資料庫基於 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp;[基於 vCore 的彈性池限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[託管實例資源限制](sql-database-managed-instance-resource-limits.md)。 |
| 49920 |16 |無法處理要求。 訂用帳戶 "%ld" 太多作業進行中。<br/><br/>服務正忙於處理這個訂用帳戶的多個要求。 要求目前已封鎖以求資源最佳化。 查詢 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以查看作業狀態。 等候直到暫止的要求已完成，或刪除您其中一個暫止要求，稍後再重試您的要求。 如需詳細資訊，請參閱 <br/>&bull;&nbsp;[資料庫伺服器資源限制](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp;[基於 DTU 的單資料庫的限制](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp;[基於 DTU 的彈性池限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[單個資料庫基於 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp;[基於 vCore 的彈性池限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[託管實例資源限制](sql-database-managed-instance-resource-limits.md)。 |
| 4221 |16 |登入 read-secondary 失敗，因為 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING' 上的等候時間很長。 無法使用複本進行登入，因為在複本回收時執行中之交易的資料列版本已遺失。 將主要複本上的使用中交易復原或認可，就可以解決問題。 避免在主要伺服器上的冗長寫入交易，就可讓此條件發生率降至最低。 |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>解決暫時性連線問題的步驟

1. 檢查 [Microsoft Azure 服務儀表板](https://azure.microsoft.com/status) ，以取得應用程式報告錯誤期間發生的任何已知中斷。
2. 連接到雲端服務的應用程式 (例如 Azure SQL Database) 應該預期定期的重新設定事件，並實作應用程式重試邏輯來處理這些錯誤，而不是將這些錯誤當做應用程式錯誤呈現給使用者。 
3. 由於資料庫接近其資源限制，因此似乎是暫時性連線問題。 請參閱[資源限制](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached)。
4. 如果連線問題繼續發生，或如果您的應用程式發生錯誤的持續時間超過 60 秒，或如果您在一天當中，看到錯誤多次發生，請在 [Azure 支援](https://azure.microsoft.com/support/options)網站上選取 [取得支援]****，來提出 Azure 支援要求。

#### <a name="implementing-retry-logic"></a>實作重試邏輯
強烈建議您的用戶端程式具有重試邏輯，以便在給予暫時性錯誤一些時間自行修正後，可以嘗試重新建立連線。  我們建議您在您第一次重試前延遲 5 秒鐘。 在少於 5 秒的延遲後重試，雲端服務會有超過負荷的風險。 對於後續每次重試，延遲應以指數方式成長，最大值為 60 秒。

如需重試邏輯的程式碼範例，請參閱：
- [使用ADO.NET彈性連接到 SQL](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [使用 PHP 復原連線 SQL](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

有關在應用程式審閱中處理瞬態錯誤的其他資訊
* [排除 SQL 資料庫的瞬態連接錯誤](sql-database-connectivity-issues.md)

在 [SQL Server 連接集區 (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx) 中可找使用 ADO.NET 之用戶端的封鎖期間** 討論。

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>建立與 SQL 資料庫伺服器的連接時發生與網路相關或特定于實例的錯誤

如果應用程式無法連接到伺服器，則會出現此問題。

要解決此問題，請嘗試"步驟"中的步驟（按顯示的順序）[修復常見連接問題](#steps-to-fix-common-connection-issues)部分。

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>找不到或無法訪問伺服器/實例（錯誤 26、40、10053）

#### <a name="error-26-error-locating-server-specified"></a>錯誤 26：指定查找伺服器錯誤

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>錯誤 40：無法打開與伺服器的連接

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>錯誤 10053：從伺服器接收結果時發生傳輸級錯誤

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

如果應用程式無法連接到伺服器，則會出現這些問題。

要解決這些問題，請嘗試"步驟"中的步驟（按顯示的順序）[修復常見連接問題](#steps-to-fix-common-connection-issues)部分。

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>由於防火牆問題，無法連接到伺服器

### <a name="error-40615-cannot-connect-to--servername-"></a>錯誤 40615： 無法連接到<伺服器名稱>

要解決此問題，[請通過 Azure 門戶在 SQL 資料庫上配置防火牆設置。](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>錯誤 5：無法連接到<伺服器名稱>

要解決此問題，請確保埠 1433 對用戶端和 Internet 之間的所有防火牆上的出站連接開放。

有關詳細資訊，請參閱配置[Windows 防火牆以允許 SQL Server 訪問](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)。

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>無法登錄到伺服器（錯誤 18456，40531）

### <a name="login-failed-for-user--user-name-"></a>使用者"<使用者名>"的登錄失敗

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

要解決此問題，請與服務管理員聯繫，為您提供有效的 SQL Server 使用者名和密碼。

通常，服務管理員可以使用以下步驟添加登錄憑據：

1. 使用 SQL 伺服器管理工作室 （SSMS） 登錄到伺服器。
2. 運行以下 SQL 查詢以檢查登錄名是否已禁用：

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. 如果對應的名稱已停用，使用下列陳述式啟用它：

   ```sql
   Alter login <User name> enable
   ```

4. 如果 SQL 登錄使用者名不存在，請按照以下步驟創建它：

   1. 在 SSMS 中，按兩下**安全性**以展開它。
   2. 以滑鼠右鍵按一下 [登入]****，然後選取 [新增登入]****。
   3. 在具有預留位置的生成腳本中，編輯並運行以下 SQL 查詢：

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. 按兩下 [資料庫]****。
6. 選擇要授予使用者許可權的資料庫。
7. 按兩下 [安全性]****。
8. 按右鍵 **"使用者**"，然後選擇 **"新使用者**"。
9. 在具有預留位置的生成腳本中，編輯並運行以下 SQL 查詢：

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
   > 您還可以使用`sp_addrolemember`將特定使用者映射到特定的資料庫角色。

有關詳細資訊，請參閱在[Azure SQL 資料庫中管理資料庫和登錄](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)。

## <a name="connection-timeout-expired-errors"></a>連接逾時過期錯誤

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>系統.Data.SqlClient.Sqlexception （0x80131904）： 連接逾時已過期

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>系統.Data.SqlClient.Sqlexception （0x80131904）： 超時已過期

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>系統.資料.實體.核心.實體異常：基礎提供程式在打開時失敗

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>無法連接到<伺服器名稱>

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

這些異常可能由於連接或查詢問題而發生。 要確認此錯誤是由連接問題引起的，請參閱[確認錯誤是否由連接問題引起](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)。

由於應用程式無法連接到伺服器，因此會發生連接逾時。 要解決此問題，請嘗試"步驟"中的步驟（按顯示的順序）[修復常見連接問題](#steps-to-fix-common-connection-issues)部分。

## <a name="resource-governance-errors"></a>資源管理錯誤

### <a name="error-10928-resource-id-d"></a>錯誤 10928： 資源識別碼： %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

要解決此問題，請嘗試以下方法之一：

* 驗證是否存在長時間運行的查詢。

  > [!NOTE]
  > 這是一種極簡主義的方法，可能無法解決問題。

1. 運行以下 SQL 查詢以檢查[sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)視圖以查看任何阻止請求：

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. 確定頭塊塊器的**輸入緩衝區**。
3. 調整頭塊塊查詢。

   有關深入的故障排除過程，請參閱[我的查詢在雲中運行正常嗎？](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

如果資料庫始終達到其限制，儘管解決了阻塞和長時間運行的查詢，請考慮升級到具有更多資源[版本的版本](https://azure.microsoft.com/pricing/details/sql-database/)。

有關動態管理檢視的詳細資訊，請參閱[系統動態管理檢視](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)。

有關資料庫限制的詳細資訊，請參閱[Azure SQL 資料庫伺服器的 SQL 資料庫資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)。

### <a name="error-10929-resource-id-1"></a>錯誤 10929： 資源識別碼： 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>錯誤 40501：服務當前處於繁忙

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

這是一個引擎限制錯誤，表示正在超出資源限制。

有關資源限制的詳細資訊，請參閱[資料庫伺服器資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)。

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>錯誤 40544：資料庫已達到其大小配額

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

當資料庫達到其大小配額時，將發生此錯誤。

以下步驟可以説明您解決問題，也可以為您提供其他選項：

1. 使用 Azure 門戶中的儀表板檢查資料庫的當前大小。

   > [!NOTE]
   > 要確定哪些表佔用的空間最多，因此是清理的潛在候選項，請運行以下 SQL 查詢：

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. 如果當前大小不超過版本支援的最大大小，則可以使用 ALTER 資料庫來增加 MAXSIZE 設置。
3. 如果資料庫已超過版本支援的最大大小，請嘗試以下一個或多個步驟：

   * 執行正常的資料庫清理活動。 例如，使用截流/刪除來清理不需要的資料，或使用 SQL 伺服器整合服務 （SSIS） 或批量複製程式 （bcp） 實用程式將資料移出。
   * 資料分割或刪除資料、卸除索引，或參閱可能解決方式的文件。
   * 有關資料庫縮放，請參閱[縮放單個資料庫資源和](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale)[縮放彈性池資源](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)。

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>錯誤 40549： 會話已終止，因為您有一個長時間運行的事務

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

如果反復遇到此錯誤，請嘗試通過以下步驟解決此問題：

1. 檢查 sys.dm_exec_requests 視圖以查看對total_elapsed_time列具有較高值的任何打開會話。 通過運行以下 SQL 腳本來執行此檢查：

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. 確定長時間運行的查詢的輸入緩衝區。
3. 調整查詢。

還要考慮批次處理查詢。 有關批次處理的資訊，請參閱[如何使用批次處理來提高 SQL 資料庫應用程式性能](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)。

有關深入的故障排除過程，請參閱[我的查詢在雲中運行正常嗎？](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>錯誤 40551：由於 TEMPDB 使用過度，會話已終止

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

若要暫時解決此問題，請遵循下列步驟：

1. 更改查詢以減少臨時表空間的使用。
2. 在不再需要臨時物件後，將它們丟棄。
3. 截取表或刪除未使用的表。

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>錯誤 40552：由於事務日誌空間使用過多，會話已終止

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

若要解決此問題，請嘗試下列方法：

* 由於插入、更新或刪除操作，可能會出現此問題。
嘗試通過實現批次處理或拆分為多個較小的事務來減少立即操作的行數。
* 由於索引重建操作，可能會出現此問題。 要解決此問題，請確保表中受影響的行數 * （以位元組為單位更新的欄位的平均大小 = 80） < 2 GB）。

  > [!NOTE]
  > 對於索引重建，更新的欄位的平均大小應替換為平均索引大小。

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>錯誤 40553：由於記憶體使用過多，會話已終止

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

要解決此問題，請嘗試優化查詢。

有關深入的故障排除過程，請參閱[我的查詢在雲中運行正常嗎？](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

### <a name="table-of-additional-resource-governance-error-messages"></a>其他資源治理錯誤訊息表

| 錯誤碼 | Severity | 描述 |
| ---:| ---:|:--- |
| 10928 |20 |資源識別碼：%d。 資料庫的 %s 限制是 %d，且已達到。 如需詳細資訊，請參閱[單一和集區資料庫的 SQL Database 資源限制](sql-database-resource-limits-database-server.md)。<br/><br/>資源識別碼可指出已達到限制的資源。 對於背景工作執行緒，資源識別碼 = 1。 對於工作階段，資源識別碼 = 2。<br/><br/>如需有關此錯誤以及其解決方法的詳細資訊，請參閱： <br/>&bull;&nbsp;[資料庫伺服器資源限制](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp;[基於 DTU 的單資料庫的限制](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp;[基於 DTU 的彈性池限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[單個資料庫基於 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp;[基於 vCore 的彈性池限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[託管實例資源限制](sql-database-managed-instance-resource-limits.md)。 |
| 10929 |20 |資源識別碼：%d。 %s 最小保證是 %d，最大限制是 %d，而資料庫的目前使用量是 %d。 但伺服器目前太忙碌，無法針對此資料庫支援大於 %d 的要求。 資源識別碼可指出已達到限制的資源。 對於背景工作執行緒，資源識別碼 = 1。 對於工作階段，資源識別碼 = 2。 如需詳細資訊，請參閱 <br/>&bull;&nbsp;[資料庫伺服器資源限制](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp;[基於 DTU 的單資料庫的限制](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp;[基於 DTU 的彈性池限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[單個資料庫基於 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp;[基於 vCore 的彈性池限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[託管實例資源限制](sql-database-managed-instance-resource-limits.md)。 <br/>或者，請稍後再試一次。 |
| 40544 |20 |資料庫已達到大小配額。 資料分割或刪除資料、卸除索引，或參閱可能解決方式的文件。 有關資料庫縮放，請參閱[縮放單個資料庫資源和](sql-database-single-database-scale.md)[縮放彈性池資源](sql-database-elastic-pool-scale.md)。|
| 40549 |16 |工作階段已終止，因為您有長時間執行的交易。 請嘗試縮短您的交易。 有關批次處理的資訊，請參閱[如何使用批次處理來提高 SQL 資料庫應用程式性能](sql-database-use-batching-to-improve-performance.md)。|
| 40550 |16 |已終止工作階段，因為它取得太多鎖定。 請嘗試在單一交易中讀取或修改較少的資料列。 有關批次處理的資訊，請參閱[如何使用批次處理來提高 SQL 資料庫應用程式性能](sql-database-use-batching-to-improve-performance.md)。|
| 40551 |16 |已終止工作階段，因為它過度使用 `TEMPDB`。 請嘗試修改查詢，減少使用暫存資料表空間。<br/><br/>如果您是使用暫存物件，請在工作階段不再需要暫存物件時予以卸除，藉此節省 `TEMPDB` 資料庫的空間。 有關 SQL 資料庫中的 tempdb 使用方式的詳細資訊，請參閱[SQL 資料庫中的 Tempdb 資料庫](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。|
| 40552 |16 |已終止工作階段，因為它過度使用交易記錄檔空間。 請嘗試在單一交易中修改較少的資料列。 有關批次處理的資訊，請參閱[如何使用批次處理來提高 SQL 資料庫應用程式性能](sql-database-use-batching-to-improve-performance.md)。<br/><br/>如果您使用 `bcp.exe` 公用程式或 `System.Data.SqlClient.SqlBulkCopy` 類別執行大量插入，請嘗試使用 `-b batchsize` 或 `BatchSize` 選項來限制每一筆交易中要複製到伺服器的資料列數目。 如果您要使用 `ALTER INDEX` 陳述式重建索引，請嘗試使用 `REBUILD WITH ONLINE = ON` 選項。 有關 vCore 採購模型的交易記錄日誌大小的資訊，請參閱： <br/>&bull;&nbsp;[單個資料庫基於 vCore 的限制](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp;[基於 vCore 的彈性池限制](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[託管實例資源限制](sql-database-managed-instance-resource-limits.md)。|
| 40553 |16 |已終止工作階段，因為它過度使用記憶體。 請嘗試修改查詢以處理較少的資料列。<br/><br/>減少 Transact-SQL 程式碼中的 `ORDER BY` 和 `GROUP BY` 作業數目，從而減少查詢的記憶體需求。 有關資料庫縮放，請參閱[縮放單個資料庫資源和](sql-database-single-database-scale.md)[縮放彈性池資源](sql-database-elastic-pool-scale.md)。|

## <a name="elastic-pool-errors"></a>彈性集區錯誤

下列錯誤與建立及使用彈性集區有關：

| 錯誤碼 | Severity | 描述 | 更正措施 |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |彈性集區已達到其儲存體限制。 彈性集區的儲存體使用量不能超過 (%d) MB。 當彈性集區達到儲存體限制時，嘗試將資料寫入資料庫。 有關資源限制的資訊，請參閱： <br/>&bull;&nbsp;[基於 DTU 的彈性池限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[基於 vCore 的彈性池限制](sql-database-vcore-resource-limits-elastic-pools.md)。 <br/> |請考慮盡可能增加彈性集區的 DTU 及/或儲存體，以提高其儲存體限制、減少彈性集區中個別資料庫所使用的儲存體量，或是從彈性集區移除資料庫。 有關彈性池縮放，請參閱[縮放彈性池資源](sql-database-elastic-pool-scale.md)。|
| 10929 | 16 |%s 最小保證是 %d，最大限制是 %d，而資料庫的目前使用量是 %d。 但伺服器目前太忙碌，無法針對此資料庫支援大於 %d 的要求。 有關資源限制的資訊，請參閱： <br/>&bull;&nbsp;[基於 DTU 的彈性池限制](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp;[基於 vCore 的彈性池限制](sql-database-vcore-resource-limits-elastic-pools.md)。 <br/> 或者，請稍後再試一次。 每個資料庫的最小 DTU / vCore；每個資料庫的最大 DTU / vCore。 彈性集區中的所有資料庫並行背景工作 (要求) 總數試圖超過集區限制。 |請考慮盡可能增加彈性集區的 DTU 或 vCore，以提高其背景工作數的限制，或是從彈性集區移除資料庫。 |
| 40844 | 16 |伺服器 '%ls' 上的資料庫 '%ls' 是彈性集區中的 '%ls' 版資料庫，且無法有連續複製關聯性。  |N/A |
| 40857 | 16 |找不到伺服器 '%ls' 的彈性集區，彈性集區名稱: '%ls'。 指定的彈性集區不存在於指定的伺服器中。 | 請提供有效的彈性集區名稱。 |
| 40858 | 16 |彈性集區 '%ls' 已存在於伺服器 '%ls' 中。 指定的彈性集區已存在於指定的 SQL Database 伺服器中。 | 請提供新的彈性集區名稱。 |
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

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>無法打開登錄請求的資料庫"主"資料庫。 登錄失敗

出現此問題的原因是帳戶沒有訪問主資料庫的許可權。 但預設情況下，SQL 伺服器管理工作室 （SSMS） 嘗試連接到主資料庫。

若要解決此問題，請依照下列步驟執行︰

1. 在 SSMS 的登錄螢幕上，選擇**選項**，然後選擇**連接屬性**。
2. 在 **"連接到資料庫**"欄位中，將使用者的預設資料庫名稱輸入為預設登錄資料庫，然後選擇"**連接**"。

   ![連線內容](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>確認錯誤是否由連接問題引起

要確認錯誤是否由連接問題引起，請查看顯示調用以打開連接的幀的堆疊追蹤（請注意對**SqlConnection**類的引用）：

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

當異常由查詢問題觸發時，您會注意到類似于以下內容的呼叫堆疊（請注意對**SqlCommand**類的引用）。 在此情況下，[調整查詢](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)。

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

有關微調性能的其他指導，請參閱以下資源：

* [如何維護 Azure SQL 索引和統計資訊](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [在 Azure SQL Database 中手動微調查詢效能](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [使用動態管理檢視監視性能 Azure SQL 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [操作 Azure SQL Database 中的查詢存放區](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>修復常見連線問題的步驟

1. 確保在應用程式伺服器上將 TCP/IP 作為用戶端協定啟用。 有關詳細資訊，請參閱[配置用戶端協定](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols)。 在未安裝 SQL Server 工具的應用程式伺服器上，請通過運行**cliconfg.exe（SQL** Server 用戶端網路實用程式）驗證是否啟用了 TCP/IP。
2. 檢查應用程式的連接字串，以確保其配置正確。 例如，確保連接字串指定正確的埠 （1433） 和完全限定的伺服器名稱。
請參閱[獲取 SQL 伺服器連接資訊](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information)。
3. 嘗試增加連接逾時值。 我們建議使用至少 30 秒的連接逾時。
4. 使用[SQL Server 管理工作室 （SSMS）（UDL](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)檔、ping 或 telnet）測試應用程式伺服器和 Azure SQL 資料庫之間的連接。 有關詳細資訊，請參閱解決[SQL 伺服器連接問題的疑難排解](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)和[連接問題的診斷](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)。

   > [!NOTE]
   > 作為故障排除步驟，您還可以測試其他用戶端電腦上的連接。

5. 最佳做法是，請確保重試邏輯已到位。 有關重試邏輯的詳細資訊，請參閱[對 SQL 資料庫的暫時性故障和連接錯誤進行故障排除](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)。

如果這些步驟不能解決問題，請嘗試收集更多資料，然後聯繫支援人員。 如果應用程式是雲服務，請啟用日誌記錄。 此步驟返回失敗的 UTC 時間戳記。 此外，SQL Azure 返回跟蹤 ID。 [Microsoft 客戶支援服務](https://azure.microsoft.com/support/options/)可以使用此資訊。

有關如何啟用日誌記錄的詳細資訊，請參閱在 Azure[應用服務 中為應用啟用診斷日誌記錄](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)。

## <a name="next-steps"></a>後續步驟

* [Azure SQL 連接體系結構](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Azure SQL 資料庫和資料倉儲網路訪問控制項](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
