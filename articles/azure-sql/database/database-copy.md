---
title: 複製資料庫
description: 在相同伺服器或不同伺服器上的 Azure SQL Database 中，建立現有資料庫的交易一致性複本。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 07/29/2020
ms.openlocfilehash: 968fe5a0f4999c25486189384186c23d1fcd5509
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430253"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>在 Azure SQL Database 中複製資料庫的交易一致性複本

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 提供數種方法，可在相同伺服器或不同的伺服器上建立現有[資料庫](single-database-overview.md)的複本。 您可以使用 Azure 入口網站、PowerShell、Azure CLI 或 T-sql 來複製資料庫。

## <a name="overview"></a>概觀

資料庫複本是源資料庫的交易一致性快照集，從起始複製要求之後的時間點開始。 您可以為複本選取相同的伺服器或不同的伺服器。 此外，您也可以選擇保留源資料庫的服務層級和計算大小，或在相同或不同的服務層級中使用不同的計算大小。 複製完成之後，複本會變成功能完整的獨立資料庫。 複製的資料庫中的登入、使用者和許可權，會與源資料庫分開管理。 此複本是使用異地複寫技術所建立。 一旦完成複本植入，異地複寫連結就會自動終止。 使用異地複寫的所有需求都適用于資料庫複製作業。 如需詳細資訊，請參閱[主動式異地複寫總覽](active-geo-replication-overview.md)。

## <a name="logins-in-the-database-copy"></a>資料庫複本中的登入

當您將資料庫複製到相同的伺服器時，可以在這兩個資料庫上使用相同的登入。 您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者。

當您將資料庫複製到不同的伺服器時，在目標伺服器上啟動複製作業的安全性主體會變成新資料庫的擁有者。

無論目標伺服器為何，所有資料庫使用者、其許可權及其安全識別碼（Sid）都會複製到資料庫複本。 使用自主[資料庫使用者](logins-create-manage.md)進行資料存取，可確保複製的資料庫具有相同的使用者認證，因此在複製完成之後，您可以使用相同的認證立即存取它。

如果您使用伺服器層級登入來進行資料存取，並將資料庫複製到不同的伺服器，登入型存取可能會無法使用。 這可能是因為登入不存在於目標伺服器上，或因為其密碼和安全識別碼（Sid）不同而發生。 若要瞭解如何在將資料庫複製到不同的伺服器時管理登入，請參閱[如何在嚴重損壞修復後管理 Azure SQL Database 安全性](active-geo-replication-security-configure.md)。 在另一部伺服器的複製作業成功之後，而且在重新對應其他使用者之前，只有與資料庫擁有者或伺服器管理員相關聯的登入可以登入已複製的資料庫。 若要在複製作業完成之後解析登入並建立資料存取，請參閱[解析](#resolve-logins)登入。

## <a name="copy-using-the-azure-portal"></a>使用 Azure 入口網站複製

若要使用 Azure 入口網站來複製資料庫，請開啟資料庫頁面，然後按一下 [複製]****。

   ![資料庫複本](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>使用 PowerShell 或 Azure CLI 進行複製

若要複製資料庫，請使用下列範例。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

針對 PowerShell，請使用[AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) Cmdlet。

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager （RM）模組，但所有未來的開發都是針對 Az .Sql 模組。 AzureRM 模組會繼續收到錯誤修正，直到2020年12月為止。  Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 如需其相容性的詳細資訊，請參閱[新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

資料庫複本是非同步作業，但是在接受要求之後，就會立即建立目標資料庫。 如果您需要在仍在進行時取消複製作業，請使用[set-azsqldatabase 搭配 Cmdlet 卸載](/powershell/module/az.sql/new-azsqldatabase)目標資料庫。

如需完整的 PowerShell 腳本範例，請參閱[將資料庫複製到新的伺服器](scripts/copy-database-to-new-server-powershell.md)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

資料庫複本是非同步作業，但是在接受要求之後，就會立即建立目標資料庫。 如果您需要在仍在進行時取消複製作業，請使用[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)命令來卸載目標資料庫。

* * *

## <a name="copy-using-transact-sql"></a>使用 Transact-sql 複製

使用伺服器管理員登入或建立您要複製之資料庫的登入，登入 master 資料庫。 若要讓資料庫複製成功，不是伺服器管理員的登入必須是角色的成員 `dbmanager` 。 如需登入與連接到伺服器的詳細資訊，請參閱 [管理登入](logins-create-manage.md)。

開始複製源資料庫與[建立資料庫 .。。當做語句的複本](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database)。 T-sql 語句會繼續執行，直到資料庫複製作業完成為止。

> [!NOTE]
> 終止 T-sql 語句並不會終止資料庫複製作業。 若要終止作業，請卸載目標資料庫。
>

### <a name="copy-to-the-same-server"></a>複製到相同的伺服器

使用伺服器管理員登入或建立您要複製之資料庫的登入，登入 master 資料庫。 若要讓資料庫複製成功，不是伺服器管理員的登入必須是角色的成員 `dbmanager` 。

此命令會將 Database1 複製到相同伺服器上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-a-different-server"></a>複製到不同的伺服器

登入要在其中建立新資料庫之目標伺服器的 master 資料庫。 使用與來源伺服器上源資料庫的資料庫擁有者具有相同名稱和密碼的登入。 目標伺服器上的登入也必須是角色的成員 `dbmanager` ，或者是伺服器管理員登入。

此命令會將 server1 上的 Database1 複製到 server2 上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> 這兩部伺服器的防火牆必須設定為允許來自發出 T-sql 建立資料庫之用戶端 IP 的輸入連線 .。。作為命令的複本。

### <a name="copy-to-a-different-subscription"></a>複製到不同的訂用帳戶

您可以使用將[SQL Database 複製到不同的伺服器](#copy-to-a-different-server)一節中的步驟，將您的資料庫複製到使用 t-sql 的不同訂用帳戶中的伺服器。 請確定您使用的登入與源資料庫的資料庫擁有者具有相同的名稱和密碼。 此外，登入必須是 `dbmanager` 來源和目標伺服器上的角色或伺服器管理員的成員。

> [!NOTE]
> [Azure 入口網站](https://portal.azure.com)、PowerShell 和 Azure CLI 不支援將資料庫複製到不同的訂用帳戶。

> [!TIP]
> 使用 T-sql 的資料庫複製支援從不同的 Azure 租使用者中的訂用帳戶複製資料庫。

## <a name="monitor-the-progress-of-the-copying-operation"></a>監視複製作業的進度

藉由查詢[sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)、 [sys.databases dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)和[sys.databases dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)視圖來監視複製程式。 當複製正在進行時，新資料庫的 sys.databases 視圖的 [ **state_desc** ] 資料行會設定為 [**複製**]。

* 如果複製失敗，新資料庫的 sys.databases 視圖的 [ **state_desc** ] 資料行會設定為 [**可疑**]。 在新的資料庫上執行 DROP 陳述式，稍後再試一次。
* 如果複製成功，新資料庫的 sys.databases 視圖的 [ **state_desc** ] 資料行會設定為 [**線上**]。 複製已完成且新資料庫是一般資料庫，能夠與來源資料庫分開進行變更。

> [!NOTE]
> 如果您決定在進行複製時予以取消，請在新資料庫上執行 [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) 陳述式。

> [!IMPORTANT]
> 如果您需要建立的複本與來源的服務目標明顯較小，則目標資料庫可能沒有足夠的資源來完成植入程式，而且可能會導致複製 operaion 失敗。 在此案例中，請使用異地還原要求，在不同的伺服器和/或不同的區域中建立複本。 如需詳細資訊，請參閱[使用資料庫備份來復原 Azure SQL Database](recovery-using-backups.md#geo-restore) 。

## <a name="rbac-roles-to-manage-database-copy"></a>用來管理資料庫複製的 RBAC 角色

若要建立資料庫複本，您必須在下列角色中

* 訂用帳戶擁有者，或是
* SQL Server 參與者角色或
* 來源和目標資料庫上具有下列許可權的自訂角色：

   Microsoft .Sql/servers/資料庫/讀取 Microsoft .Sql/伺服器/資料庫/寫入

若要取消資料庫複本，您必須在下列角色中

* 訂用帳戶擁有者，或是
* SQL Server 參與者角色或
* 來源和目標資料庫上具有下列許可權的自訂角色：

   Microsoft .Sql/servers/資料庫/讀取 Microsoft .Sql/伺服器/資料庫/寫入

若要使用 Azure 入口網站來管理資料庫複製，您也需要下列許可權：

   Microsoft .Resources/訂用帳戶/資源/讀取 Microsoft .resources/訂用帳戶/資源/撰寫 Microsoft .Resources/部署/讀取 microsoft .resources/部署/撰寫 microsoft .resources/部署/operationstatuses/讀取

如果您想要查看入口網站上資源群組中的 [部署] 底下的作業，跨多個資源提供者的作業（包括 SQL 作業），您將需要這些額外的 RBAC 角色：

   Microsoft .Resources/訂用帳戶/resourcegroups/部署/作業/讀取 Microsoft .Resources/訂用帳戶/resourcegroups/部署/operationstatuses/讀取

## <a name="resolve-logins"></a>解析登入

在目標伺服器上的新資料庫上線之後，請使用[ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current)語句，將新資料庫中的使用者重新對應至目標伺服器上的登入。 若要解析被遺棄的使用者，請參閱 [被遺棄使用者疑難排解](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)。 另請參閱[如何管理嚴重損壞修復後的 Azure SQL Database 安全性](active-geo-replication-security-configure.md)。

新資料庫中的所有使用者都保有其在來源資料庫中原有的權限。 起始資料庫複本的使用者會成為新資料庫的資料庫擁有者。 複製成功之後，以及重新對應其他使用者之前，只有資料庫擁有者可以登入新的資料庫。

若要瞭解如何在將資料庫複製到不同的伺服器時管理使用者和登入，請參閱[如何在嚴重損壞修復後管理 Azure SQL Database 安全性](active-geo-replication-security-configure.md)。

## <a name="database-copy-errors"></a>資料庫複製錯誤

在 Azure SQL Database 中複製資料庫時，可能會發生下列錯誤。 如需詳細資訊，請參閱 [複製 Azure SQL Database](database-copy.md)。

| 錯誤碼 | 嚴重性 | 描述 |
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

* 如需登入的相關資訊，請參閱[管理](logins-create-manage.md)登入和[如何管理嚴重損壞修復後的 Azure SQL Database 安全性](active-geo-replication-security-configure.md)。
* 若要匯出資料庫，請參閱將[資料庫匯出至 BACPAC](database-export.md)。
