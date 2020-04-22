---
title: 託管實例:長期備份保留(PowerShell)
description: 瞭解如何使用 PowerShell 在單獨的 Azure Blob 儲存容器上儲存和還原自動備份。
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
ms.date: 04/19/2020
ms.openlocfilehash: 24eacb555704593fe44bc2d949de44de163345bc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677110"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>管理 Azure SQL 資料庫託管實例長期備份保留 (PowerShell)

在 Azure SQL 資料庫託管實例中,可以將[長期備份保留](sql-database-long-term-retention.md#managed-instance-support)策略 (LTR) 配置為有限的公共預覽功能。 這允許您自動將資料庫備份保留在單獨的 Azure Blob 儲存容器中長達 10 年。 然後,您可以使用這些備份與 PowerShell 一起恢復資料庫。

   > [!IMPORTANT]
   > 託管實例的 LTR 當前處於有限的預覽狀態,可按案例針對 EA 和 CSP 訂閱使用。 要請求註冊,請在支援主題 **「備份、還原和業務連續性/長期備份保留**」下創建[Azure 支援票證](https://azure.microsoft.com/support/create-ticket/)。 


下列各節說明如何使用 PowerShell 來設定長期備份保留、檢視 Azure SQL 儲存體中的備份，以及從 Azure SQL 儲存體中的備份還原。

## <a name="rbac-roles-to-manage-long-term-retention"></a>管理長期保留的 RBAC 角色

對於**取得 AzSqlInstance 資料庫長期保留備份**和**還原-AzSqlInstance 資料庫**,您需要具有以下角色之一:

- 訂閱擁有者角色或
- 託管實例參與者角色或
- 具有以下權限的自訂角色:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

對於**刪除 AzSqlInstance 資料庫長期保留備份**,您需要具有以下角色之一:

- 訂閱擁有者角色或
- 具有以下權限的自訂角色:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> 託管實例參與者角色無權刪除 LTR 備份。

可以在*訂閱*或*資源組*作用域中授予 RBAC 許可權。 但是,要造訪屬於丟棄實例的 LTR 備份,必須在該實例的*訂閱*範圍內授予許可權。

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>建立 LTR 原則

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>檢視 LTR 原則

此範例展示如何在實體中列出 LTR 政策

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>清除 LTR 原則

此範例示範如何清除資料庫中的 LTR 原則

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>檢視 LTR 備份

此示例演示如何在實例中列出 LTR 備份。

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>刪除 LTR 備份

此範例示範如何刪除備份清單中的 LTR 備份。

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> 刪除 LTR 備份，便無法回復。 要在實體被刪除後刪除 LTR 備份,您必須具有訂閱範圍許可權。 您可以通過篩選操作「刪除長期保留備份」來設置有關 Azure 監視器中每個刪除的通知。 活動記錄包含提出要求的人員和時間資訊。 如需詳細指示，請參閱[建立活動記錄警示](../azure-monitor/platform/alerts-activity-log.md)。

## <a name="restore-from-ltr-backups"></a>從 LTR 備份還原

此範例示範如何從 LTR 備份還原。 請注意，這個介面並未變更，但是資源識別碼參數現在需要 LTR 備份資源識別碼。

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> 要在刪除實例後從 LTR 備份進行還原,您必須具有實例訂閱的許可權範圍,並且該訂閱必須處於活動狀態。 您還必須省略可選的 -資源組名稱參數。

> [!NOTE]
> 從這裡開始，您可以使用 SQL Server Management Studio 連線到已還原的資料庫來執行所需的工作，例如從還原的資料庫擷取一堆資料來複製到現有的資料庫，或刪除現有的資料庫，並將還原的資料庫重新命名為現有的資料庫名稱。 請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解服務產生的自動備份，請參閱[自動備份](sql-database-automated-backups.md)
- 若要深入了解長期備份保留，請參閱[長期備份保留](sql-database-long-term-retention.md)
