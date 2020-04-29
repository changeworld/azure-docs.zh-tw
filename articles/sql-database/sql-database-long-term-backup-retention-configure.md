---
title: 單一資料庫：管理長期備份保留
description: 瞭解如何使用 Azure 入口網站和 PowerShell，在 Azure 儲存體中儲存和還原 Azure SQL Database 單一或集區資料庫的自動備份（最多10年）
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/14/2020
ms.openlocfilehash: 2564fd0ffd980dae4ca1835f4211fee0a0cf040c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380924"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>管理 Azure SQL Database 長期備份保留

在 Azure SQL Database 中，您可以使用[長期備份保留](sql-database-long-term-retention.md)原則（LTR）來設定單一或集區資料庫，以自動將資料庫備份保留在不同的 Azure Blob 儲存體容器中長達10年。 然後，您可以使用 Azure 入口網站或 PowerShell 來復原資料庫。

> [!IMPORTANT]
> [AZURE SQL database 受控實例](sql-database-managed-instance.md)目前不支援長期備份保留。

## <a name="using-azure-portal"></a>使用 Azure 入口網站

下列各節說明如何使用 Azure 入口網站來設定長期保留、檢視長期保留的備份，以及從長期保留還原備份。

### <a name="configure-long-term-retention-policies"></a>設定長期保留原則

您可以將 Azure SQL Database 設定為[保留自動備份](sql-database-long-term-retention.md)的期間比您服務層級的保留期限還要長。

1. 在 Azure 入口網站中，選取您的 SQL Server，然後按一下 [管理備份]****。 在 [設定原則]**** 索引標籤上，針對您要設定或修改長期備份保留原則的資料庫選取其核取方塊。 如果未選取資料庫旁的核取方塊，則原則的變更不會套用到該資料庫。  

   ![管理備份連結](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. 在 [設定原則]**** 窗格中，選取您想要保留每週、每月或每年備份並指定各項的保留期限。

   ![設定原則](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. 完成時，按一下 [套用]****。

> [!IMPORTANT]
> 當您啟用長期備份保留原則時，最多可能需要7天的時間，第一次備份才會變成可見且可供還原。 如需 LTR 備份 cadance 的詳細資訊，請參閱[長期備份保留](sql-database-long-term-retention.md)。

### <a name="view-backups-and-restore-from-a-backup"></a>從備份中查看備份和還原

檢視使用 LTR 原則針對特定資料庫保留的備份，然後從這些備份還原。

1. 在 Azure 入口網站中，選取您的 SQL Server，然後按一下 [管理備份]****。 在 [可用備份]**** 索引標籤上，選取您要查看可用備份的資料庫。

   ![選取資料庫](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

1. 在 [可用備份]**** 窗格中，檢閱可用的備份。

   ![檢視備份](./media/sql-database-long-term-retention/ltr-available-backups.png)

1. 選取您要還原的備份，然後指定新的資料庫名稱。

   ![還原](./media/sql-database-long-term-retention/ltr-restore.png)

1. 按一下 [確定]****，將您的資料庫從 Azure SQL 儲存體中的備份還原到新的資料庫。

1. 在工具列上，按一下 [通知] 圖示以檢視還原作業的狀態。

   ![還原作業進度](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

1. 完成還原作業之後，開啟 [SQL Database]**** 頁面，以檢視剛還原的資料庫。

> [!NOTE]
> 從這裡開始，您可以使用 SQL Server Management Studio 連接到已還原的資料庫來執行所需的工作，例如[從還原的資料庫擷取一堆資料來複製到現有的資料庫，或刪除現有的資料庫，並將還原的資料庫重新命名為現有的資料庫名稱](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="using-powershell"></a>使用 PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

下列各節說明如何使用 PowerShell 來設定長期備份保留、檢視 Azure SQL 儲存體中的備份，以及從 Azure SQL 儲存體中的備份還原。

### <a name="rbac-roles-to-manage-long-term-retention"></a>管理長期保留的 RBAC 角色

針對**AzSqlDatabaseLongTermRetentionBackup**和**還原 set-azsqldatabase 搭配**，您必須具有下列其中一個角色：

- 訂用帳戶擁有者角色或
- SQL Server 參與者角色或
- 具有下列許可權的自訂角色：

   Microsoft .Sql/位置/longTermRetentionBackups/讀取 Microsoft .Sql/位置/longTermRetentionServers/longTermRetentionBackups/讀取 Microsoft .sql/位置/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

針對**AzSqlDatabaseLongTermRetentionBackup**，您必須具有下列其中一個角色：

- 訂用帳戶擁有者角色或
- 具有下列許可權的自訂角色：

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> SQL Server 參與者角色沒有刪除 LTR 備份的許可權。

可以在*訂*用帳戶或*資源群組*範圍中授與 RBAC 許可權。 不過，若要存取屬於已卸載伺服器的 LTR 備份，則必須在該伺服器的*訂*用帳戶範圍中授與許可權。

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>建立 LTR 原則

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>檢視 LTR 原則

此範例示範如何列出伺服器內的 LTR 原則

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | `
    Get-AzSqlDatabaseLongTermRetentionPolicy -Current

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -Current
```

### <a name="clear-an-ltr-policy"></a>清除 LTR 原則

此範例示範如何清除資料庫中的 LTR 原則

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>檢視 LTR 備份

此範例示範如何列出伺服器內的 LTR 備份。

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>刪除 LTR 備份

此範例示範如何刪除備份清單中的 LTR 備份。

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> 刪除 LTR 備份，便無法回復。 若要在刪除伺服器後刪除 LTR 備份，您必須擁有訂用帳戶範圍許可權。 您可以藉由篩選「刪除長期保留備份」作業，設定 Azure 監視器中每個刪除的通知。 活動記錄包含提出要求的人員和時間資訊。 如需詳細指示，請參閱[建立活動記錄警示](../azure-monitor/platform/alerts-activity-log.md)。

### <a name="restore-from-ltr-backups"></a>從 LTR 備份還原

此範例示範如何從 LTR 備份還原。 請注意，這個介面並未變更，但是資源識別碼參數現在需要 LTR 備份資源識別碼。

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> 若要在刪除伺服器後從 LTR 備份還原，您必須擁有伺服器訂閱的範圍許可權，而且該訂閱必須處於使用中狀態。 您也必須省略選擇性的-ResourceGroupName 參數。

> [!NOTE]
> 從這裡開始，您可以使用 SQL Server Management Studio 連線到已還原的資料庫來執行所需的工作，例如從還原的資料庫擷取一堆資料來複製到現有的資料庫，或刪除現有的資料庫，並將還原的資料庫重新命名為現有的資料庫名稱。 請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解服務產生的自動備份，請參閱[自動備份](sql-database-automated-backups.md)
- 若要深入了解長期備份保留，請參閱[長期備份保留](sql-database-long-term-retention.md)
