---
title: 複製資料庫
description: 在同個伺服器或不同伺服器上，建立現有 Azure SQL 資料庫的交易一致性複本。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473739"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>複製 Azure SQL 資料庫的交易一致性複本

Azure SQL 資料庫提供了幾種方法，用於在同一伺服器或其他伺服器上創建現有 Azure SQL 資料庫（[單個資料庫](sql-database-single-database.md)）的事務一致性副本。 若要複製 SQL Database，您可使用 Azure 入口網站、PowerShell 或 T-SQL。

## <a name="overview"></a>總覽

資料庫複本是發生複製要求時的來源資料庫快照集。 您可以選擇同一台伺服器或其他伺服器。 此外，您可以選擇保留其服務層和計算大小，或在同一服務層（版本）中使用不同的計算大小。 複製完成之後，複本會變成功能完整的獨立資料庫。 此時，您可以將它升級或降級成任何版本。 可以個別管理登入、使用者和權限。 複製使用異地複製技術創建，種子設定完成後，異地複製連結將自動終止。 使用異地複製的所有要求都適用于資料庫複製操作。 有關詳細資訊，請參閱[活動異地複製概述](sql-database-active-geo-replication.md)。

> [!NOTE]
> 創建資料庫副本時，將使用[自動資料庫備份](sql-database-automated-backups.md)。

## <a name="logins-in-the-database-copy"></a>資料庫複本中的登入

當您將資料庫複製到相同的 SQL Database 伺服器時，可以在這兩個資料庫上使用相同的登入。 您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者。 

將資料庫複製到其他 SQL 資料庫伺服器時，在目標伺服器上啟動複製操作的安全主體將成為新資料庫的擁有者。 

無論目標伺服器是什麼，所有資料庫使用者、其許可權及其安全識別碼 （SID） 都將複製到資料庫副本。 使用[包含的資料庫使用者](sql-database-manage-logins.md)進行資料訪問可確保複製的資料庫具有相同的使用者憑據，以便在副本完成後，您可以立即使用相同的憑據訪問它。

如果使用伺服器級登錄進行資料訪問並將資料庫複製到其他伺服器，則基於登錄的訪問可能不起作用。 這可能是因為目標伺服器上不存在登錄名，或者因為它們的密碼和安全識別碼 （SID） 不同。 若要了解如何在將資料庫複製到不同的 SQL Database 伺服器時管理登入，請參閱[如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。 複製到其他伺服器後，在重新映射其他使用者之前，只有與資料庫擁有者關聯的登錄名，或者伺服器管理員才能登錄到複製的資料庫。 要在複製操作完成後解析登錄並建立資料訪問，請參閱[解析登錄](#resolve-logins)名。

## <a name="copy-a-database-by-using-the-azure-portal"></a>使用 Azure 入口網站來複製資料庫

若要使用 Azure 入口網站來複製資料庫，請開啟資料庫頁面，然後按一下 [複製]****。

   ![資料庫複本](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>使用 PowerShell 或 Azure CLI 複製資料庫

要複製資料庫，請使用以下示例。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

對於 PowerShell，請使用[新 AzSql 資料庫Copy](/powershell/module/az.sql/new-azsqldatabasecopy) Cmdlet。

> [!IMPORTANT]
> PowerShell Azure 資源管理器 （RM） 模組仍受 Azure SQL 資料庫支援，但所有後續開發都針對 Az.Sql 模組。 AzureRM 模組將繼續接收錯誤修復，至少直到 2020 年 12 月。  Az 模組和 AzureRm 模組中命令的參數基本相同。 有關其相容性的更多資訊，請參閱[介紹新的 Azure PowerShell Az 模組](/powershell/azure/new-azureps-module-az)。

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

資料庫副本是非同步作業，但在接受請求後立即創建目標資料庫。 如果需要在仍在進行中時取消複製操作，請使用["刪除-AzSql資料庫](/powershell/module/az.sql/new-azsqldatabase)"Cmdlet 刪除目標資料庫。

有關完整的 PowerShell 腳本示例，請參閱[將資料庫複製到新伺服器](scripts/sql-database-copy-database-to-new-server-powershell.md)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

資料庫副本是非同步作業，但在接受請求後立即創建目標資料庫。 如果需要在仍在進行中時取消複製操作，請使用[az sql db 刪除](/cli/azure/sql/db#az-sql-db-delete)命令刪除目標資料庫。

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC 角色來管理資料庫副本

要創建資料庫副本，您需要擔任以下角色

- 訂用帳戶擁有者，或是
- SQL 伺服器參與者角色或
- 具有以下許可權的源和目標資料庫上的自訂角色：

   微軟.Sql/伺服器/資料庫/讀取微軟.Sql/伺服器/資料庫/寫入

要取消資料庫副本，您需要擔任以下角色

- 訂用帳戶擁有者，或是
- SQL 伺服器參與者角色或
- 具有以下許可權的源和目標資料庫上的自訂角色：

   微軟.Sql/伺服器/資料庫/讀取微軟.Sql/伺服器/資料庫/寫入

要使用 Azure 門戶管理資料庫副本，還需要以下許可權：

   微軟.資源/訂閱/資源/讀取微軟.資源/訂閱/資源/寫入微軟.資源/部署/讀取微軟.資源/部署/寫入微軟.資源/部署/操作狀態/讀取

如果要查看門戶上資源組中的部署下的操作、跨多個資來源提供者（包括 SQL 操作）的操作，則需要以下其他 RBAC 角色：

   微軟.資源/訂閱/資源組/部署/操作/讀取微軟.資源/訂閱/資源組/部署/操作狀態/讀取

## <a name="copy-a-database-by-using-transact-sql"></a>使用 Transact-SQL 來複製資料庫

使用伺服器管理員登錄或創建要複製的資料庫的登錄登錄到主資料庫。 要使資料庫副本成功，不是伺服器管理員的登錄名必須是`dbmanager`角色的成員。 如需登入與連接到伺服器的詳細資訊，請參閱 [管理登入](sql-database-manage-logins.md)。

開始複製源資料庫與[創建資料庫...作為語句的副本](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database)。 T-SQL 語句繼續運行，直到資料庫複製操作完成。

> [!NOTE]
> 終止 T-SQL 語句不會終止資料庫複製操作。 要終止操作，刪除目標資料庫。
>

### <a name="copy-a-sql-database-to-the-same-server"></a>將 SQL Database 複製到相同伺服器

使用伺服器管理員登錄或創建要複製的資料庫的登錄登錄到主資料庫。 要成功進行資料庫複製，不是伺服器管理員的登錄名必須是`dbmanager`角色的成員。

此命令會將 Database1 複製到相同伺服器上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>將 SQL Database 複製到不同伺服器

登錄到要創建新資料庫的目標伺服器的主資料庫。 使用與源伺服器上的源資料庫的資料庫擁有者具有相同名稱和密碼的登錄名。 目標伺服器上的登錄也必須是`dbmanager`角色的成員，或者是伺服器管理員登錄。

此命令會將 server1 上的 Database1 複製到 server2 上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> 必須配置兩個伺服器的防火牆，以允許來自發出 T-SQL CREATE DATABASE 的用戶端的 IP 的入站連接...作為命令的副本。

### <a name="copy-a-sql-database-to-a-different-subscription"></a>將 SQL 資料庫複製到其他訂閱

您可以使用將[SQL 資料庫複製到其他伺服器](#copy-a-sql-database-to-a-different-server)部分中的步驟，使用 T-SQL 將資料庫複製到其他訂閱中的 SQL 資料庫伺服器。 請確保使用的登錄名與源資料庫的資料庫擁有者具有相同的名稱和密碼。 此外，登錄必須是源伺服器和目標伺服器上`dbmanager`的角色或伺服器管理員的成員。

> [!NOTE]
> [Azure 門戶](https://portal.azure.com)PowerShell 和 Azure CLI 不支援資料庫複製到其他訂閱。

### <a name="monitor-the-progress-of-the-copying-operation"></a>監視複製作業的進度

通過查詢[sys.資料庫](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)[、dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)和[sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)視圖來監視複製過程。 複製正在進行時，新資料庫的sys.資料庫檢視**的state_desc**列設置為**COPYING**。

* 如果複製失敗，新資料庫的state_desc資料庫檢視**的列將**設置為 **"可疑"。** 在新的資料庫上執行 DROP 陳述式，稍後再試一次。
* 如果複製成功，則新資料庫的 sys.database 視圖**的state_desc**列將設置為**ONLINE**。 複製已完成且新資料庫是一般資料庫，能夠與來源資料庫分開進行變更。

> [!NOTE]
> 如果您決定在進行複製時予以取消，請在新資料庫上執行 [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) 陳述式。

> [!IMPORTANT]
> 如果需要創建服務目標比源小得多的副本，目標資料庫可能沒有足夠的資源來完成種子設定過程，並且可能導致複製操作失敗。 在這種情況下，使用地理還原請求在不同的伺服器和/或其他區域中棄置站台。 有關更多資訊[，請參閱使用資料庫備份恢復 Azure SQL 資料庫](sql-database-recovery-using-backups.md#geo-restore)。

## <a name="resolve-logins"></a>解析登入

在目標伺服器上新資料庫連線後，使用[ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current)語句將使用者從新資料庫重新映射到目標伺服器上的登錄。 若要解析被遺棄的使用者，請參閱 [被遺棄使用者疑難排解](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)。 另請參閱 [如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。

新資料庫中的所有使用者都保有其在來源資料庫中原有的權限。 啟動資料庫副本的使用者將成為新資料庫的資料庫擁有者。 複製成功後，在重新映射其他使用者之前，只有資料庫擁有者才能登錄到新資料庫。

若要了解將資料庫複製到不同的邏輯 SQL Database 伺服器時如何管理使用者與登入，請參閱[如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。

## <a name="database-copy-errors"></a>資料庫複製錯誤

在 Azure SQL Database 中複製資料庫時，可能會發生下列錯誤。 如需詳細資訊，請參閱 [複製 Azure SQL Database](sql-database-copy.md)。

| 錯誤碼 | Severity | 描述 |
| ---:| ---:|:--- |
| 40635 |16 |IP 位址 '%.&#x2a;ls' 的用戶端已暫時停用。 |
| 40637 |16 |建立資料庫副本目前已停用。 |
| 40561 |16 |資料庫複製失敗。 來源或目標資料庫不存在。 |
| 40562 |16 |資料庫複製失敗。 已經卸除來源資料庫。 |
| 40563 |16 |資料庫複製失敗。 已經卸除目標資料庫。 |
| 40564 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並再試一次。 |
| 40565 |16 |資料庫複製失敗。 不允許從相同來源進行超過 1 個並行資料庫複製。 請卸除目標資料庫並稍後再試一次。 |
| 40566 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並再試一次。 |
| 40567 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並再試一次。 |
| 40568 |16 |資料庫複製失敗。 來源資料庫已變成無法使用。 請卸除目標資料庫並再試一次。 |
| 40569 |16 |資料庫複製失敗。 目標資料庫已變成無法使用。 請卸除目標資料庫並再試一次。 |
| 40570 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並稍後再試一次。 |
| 40571 |16 |資料庫複製因內部錯誤而失敗。 請卸除目標資料庫並稍後再試一次。 |

## <a name="next-steps"></a>後續步驟

- 如需登入相關資訊，請參閱[管理登入](sql-database-manage-logins.md)以及[如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。
- 要匯出資料庫，請參閱[將資料庫匯出到 BACPAC](sql-database-export.md)。
