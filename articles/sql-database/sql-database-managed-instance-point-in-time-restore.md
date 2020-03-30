---
title: 託管實例 - 時間點還原 （PITR）
description: 將託管實例中的 SQL 資料庫還原到以前的時間點。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268804"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>將託管實例中的 SQL 資料庫還原到上一個時間點

使用時間點還原 （PITR） 創建資料庫，作為過去某個時候另一個資料庫的副本。 本文介紹如何在 Azure SQL 資料庫託管實例中對資料庫進行時間點還原。

時間點還原在恢復方案中非常有用，例如由錯誤引起的事件、不正確的載入資料或刪除關鍵資料。 您也可以將其用於測試或審核。 備份檔案將保留 7 到 35 天，具體取決於資料庫設置。

時間點還原可以還原資料庫：

- 從現有資料庫。
- 從已刪除的資料庫。
- 到同一託管實例，或另一個託管實例。 

## <a name="limitations"></a>限制

還原到託管實例的時間點具有以下限制：

- 當您從一個託管實例還原到另一個實例時，兩個實例必須位於同一訂閱和區域中。 當前不支援跨區域和跨訂閱還原。
- 無法對整個託管實例進行時間點還原。 本文僅說明可能實現的內容：託管實例上託管的資料庫的時間點還原。

> [!WARNING]
> 請注意託管實例的存儲大小。 根據要還原的資料的大小，實例存儲可能耗盡。 如果還原的資料沒有足夠的空間，請使用其他方法。

下表顯示了託管實例的時間點還原方案：

|           |將現有資料庫還原到同一託管實例| 將現有資料庫還原到另一個託管實例|將丟棄的資料庫還原到同一託管實例|將丟棄的資料庫還原到另一個託管實例|
|:----------|:----------|:----------|:----------|:----------|
|**Azure 門戶**| 是|否 |是|否|
|**Azure CLI**|是 |是 |否|否|
|**電源外殼**| 是|是 |是|是|

## <a name="restore-an-existing-database"></a>還原現有資料庫

使用 Azure 門戶、PowerShell 或 Azure CLI 將現有資料庫還原到同一實例。 要將資料庫還原到另一個實例，請使用 PowerShell 或 Azure CLI，以便指定目標託管實例和資源組的屬性。 如果不指定這些參數，預設情況下資料庫將還原到現有實例。 Azure 門戶當前不支援還原到另一個實例。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 登錄到 Azure[門戶](https://portal.azure.com)。 
2. 轉到託管實例並選擇要還原的資料庫。
3. 在資料庫頁面上選擇 **"還原**"：

    ![使用 Azure 門戶還原資料庫](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. 在 **"還原"** 頁上，選擇要還原資料庫的日期和時間的點。
5. 選擇 **"確認**"以還原資料庫。 此操作啟動還原過程，該進程創建新資料庫，並在指定的時間點使用原始資料庫的資料填充它。 有關恢復過程的詳細資訊，請參閱[恢復時間](sql-database-recovery-using-backups.md#recovery-time)。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

如果尚未安裝 Azure PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

要使用 PowerShell 還原資料庫，請在以下命令中指定參數的值。 然後，運行命令：

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

要將資料庫還原到另一個託管實例，請指定目標資源組和目標託管實例的名稱：  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

有關詳細資訊，請參閱[還原-AzSqlinstance 資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果尚未安裝 Azure CLI，請參閱安裝 Azure [CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

要使用 Azure CLI 還原資料庫，請在以下命令中指定參數的值。 然後，運行命令：

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

要將資料庫還原到另一個託管實例，請指定目標資源組和託管實例的名稱：  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

有關可用參數的詳細說明，請參閱[用於在託管實例中還原資料庫的 CLI 文檔](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)。

---

## <a name="restore-a-deleted-database"></a>還原已刪除的資料庫

還原已刪除的資料庫可以通過使用 PowerShell 或 Azure 門戶來完成。 要將已刪除的資料庫還原到同一實例，請使用 Azure 門戶或 PowerShell。 要將已刪除的資料庫還原到另一個實例，請使用 PowerShell。 

### <a name="portal"></a>入口網站 


要使用 Azure 門戶恢復託管資料庫，請打開託管實例概覽頁，然後選擇 **"已刪除的資料庫**"。 選擇要還原的已刪除資料庫，然後鍵入將使用從備份還原的資料創建的新資料庫的名稱。

  ![已還原已刪除的 Azure SQL 實例資料庫的螢幕截圖](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

要將資料庫還原到同一實例，請更新參數值，然後運行以下 PowerShell 命令： 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

要將資料庫還原到另一個託管實例，請指定目標資源組和目標託管實例的名稱：

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>覆蓋現有資料庫

要覆蓋現有資料庫，必須：

1. 刪除要覆蓋的現有資料庫。
2. 將時間點還原的資料庫重命名為丟棄的資料庫的名稱。

### <a name="drop-the-original-database"></a>刪除原始資料庫

可以使用 Azure 門戶、PowerShell 或 Azure CLI 刪除資料庫。

還可以通過直接連接到託管實例、啟動 SQL 伺服器管理工作室 （SSMS）以及運行以下 Transact-SQL （T-SQL） 命令來刪除資料庫：

```sql
DROP DATABASE WorldWideImporters;
```

使用以下方法之一連接到託管實例中的資料庫：

- [通過 Azure 虛擬機器的 SSMS/Azure 資料工作室](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [點對點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 門戶中，從託管實例中選擇資料庫，然後選擇 **"刪除**"。

   ![使用 Azure 門戶刪除資料庫](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

使用以下 PowerShell 命令從託管實例中刪除現有資料庫：

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下 Azure CLI 命令從託管實例中刪除現有資料庫：

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>更改新資料庫名稱以匹配原始資料庫名稱

直接連接到託管實例並啟動 SQL 伺服器管理工作室。 然後，運行以下 Transact SQL （T-SQL） 查詢。 查詢將還原的資料庫的名稱更改為要覆蓋的已刪除資料庫的名稱。

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

使用以下方法之一連接到託管實例中的資料庫：

- [Azure 虛擬機器](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [點對點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>後續步驟

瞭解[自動備份](sql-database-automated-backups.md)。
