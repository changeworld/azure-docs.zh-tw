---
title: Azure SQL 受控執行個體：長期備份保留（PowerShell）
description: 瞭解如何使用 PowerShell 在 Azure SQL 受控執行個體的個別 Azure Blob 儲存體容器上儲存和還原自動備份。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/29/2020
ms.openlocfilehash: b628ca7f676c3eab80e11da124f4d6aa7ebd52a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708785"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>管理 Azure SQL 受控執行個體長期備份保留（PowerShell）
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

在 Azure SQL 受控執行個體中，您可以設定[長期備份保留](../database/long-term-retention-overview.md#sql-managed-instance-support)原則（LTR）做為有限的公開預覽功能。 這可讓您自動將資料庫備份保留在不同的 Azure Blob 儲存體容器中，最多可達10年。 然後，您可以使用這些備份搭配 PowerShell 來復原資料庫。

   > [!IMPORTANT]
   > 針對受控實例，LTR 目前處於有限預覽狀態，並以案例為依據，適用于 EA 和 CSP 訂用帳戶。 若要要求註冊，請建立[Azure 支援票證](https://azure.microsoft.com/support/create-ticket/)。 針對 [問題類型]，選取 [技術問題]，針對 [服務] 選擇 [SQL Database 受控執行個體]，並針對 [問題類型] 選取 [**備份、還原和商務持續性/長期備份保留**]。 在您的要求中，請確定您想要在受管理的實例之 LTR 公開預覽中註冊。

下列各節說明如何使用 PowerShell 來設定長期備份保留、檢視 Azure SQL 儲存體中的備份，以及從 Azure SQL 儲存體中的備份還原。

## <a name="rbac-roles-to-manage-long-term-retention"></a>管理長期保留的 RBAC 角色

針對**AzSqlInstanceDatabaseLongTermRetentionBackup**和**還原 AzSqlInstanceDatabase**，您必須具有下列其中一個角色：

- 訂用帳戶擁有者角色或
- 受控執行個體參與者角色或
- 具有下列許可權的自訂角色：
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

針對**AzSqlInstanceDatabaseLongTermRetentionBackup**，您必須具有下列其中一個角色：

- 訂用帳戶擁有者角色或
- 具有下列許可權的自訂角色：
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> 受控執行個體參與者角色沒有刪除 LTR 備份的許可權。

可以在*訂*用帳戶或*資源群組*範圍中授與 RBAC 許可權。 不過，若要存取屬於已卸載實例的 LTR 備份，則必須在該實例的*訂*用帳戶範圍中授與許可權。

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

這個範例示範如何列出實例內的 LTR 原則

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

這個範例會示範如何列出實例內的 LTR 備份。

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
> 刪除 LTR 備份，便無法回復。 若要在刪除實例之後刪除 LTR 備份，您必須擁有「訂用帳戶範圍」許可權。 您可以藉由篩選「刪除長期保留備份」作業，設定 Azure 監視器中每個刪除的通知。 活動記錄包含提出要求的人員和時間資訊。 如需詳細指示，請參閱[建立活動記錄警示](../../azure-monitor/platform/alerts-activity-log.md)。

## <a name="restore-from-ltr-backups"></a>從 LTR 備份還原

此範例示範如何從 LTR 備份還原。 請注意，這個介面並未變更，但是資源識別碼參數現在需要 LTR 備份資源識別碼。

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> 若要在刪除實例之後從 LTR 備份還原，您必須擁有該實例之訂閱的許可權，而且該訂閱必須是作用中。 您也必須省略選擇性的-ResourceGroupName 參數。

> [!NOTE]
> 從這裡開始，您可以使用 SQL Server Management Studio 連線到已還原的資料庫來執行所需的工作，例如從還原的資料庫擷取一堆資料來複製到現有的資料庫，或刪除現有的資料庫，並將還原的資料庫重新命名為現有的資料庫名稱。 請參閱[還原時間點](../database/recovery-using-backups.md#point-in-time-restore)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解服務產生的自動備份，請參閱[自動備份](../database/automated-backups-overview.md)
- 若要深入了解長期備份保留，請參閱[長期備份保留](../database/long-term-retention-overview.md)
