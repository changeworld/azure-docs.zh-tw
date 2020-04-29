---
title: 受控實例-時間點還原（PITR）
description: 將受控實例中的 SQL database 還原至先前的時間點。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268804"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>將受控實例中的 SQL database 還原至先前的時間點

使用時間點還原（PITR），從過去的某個時間，將資料庫建立為另一個資料庫的複本。 本文說明如何在 Azure SQL Database 受控實例中執行資料庫的時間點還原。

時間點還原在復原案例中很有用，例如錯誤造成的事件、不正確地載入資料，或刪除重要的資料。 您也可以將它用於測試或審核。 備份檔案會保留7到35天，視您的資料庫設定而定。

還原時間點可以還原資料庫：

- 從現有的資料庫。
- 從已刪除的資料庫。
- 指向相同的受控實例或另一個受控實例。 

## <a name="limitations"></a>限制

將時間點還原至受控實例有下列限制：

- 當您從某個受控實例還原到另一個時，這兩個實例必須位於相同的訂用帳戶和區域中。 目前不支援跨區域和跨訂用帳戶還原。
- 不可能進行整個受控實例的時間點還原。 本文僅說明可能的情況：裝載在受控實例上之資料庫的時間點還原。

> [!WARNING]
> 請留意受控實例的儲存體大小。 視要還原的資料大小而定，您可能會用盡實例儲存體。 如果沒有足夠的空間可用於還原的資料，請使用不同的方法。

下表顯示受控實例的時間點還原案例：

|           |將現有的資料庫還原到相同的受控實例| 將現有的資料庫還原到另一個受控實例|將已卸載的資料庫還原到相同的受控實例|將已卸載的資料庫還原到另一個受控實例|
|:----------|:----------|:----------|:----------|:----------|
|**Azure 入口網站**| 是|否 |是|否|
|**Azure CLI**|是 |是 |否|否|
|**PowerShell**| 是|是 |是|是|

## <a name="restore-an-existing-database"></a>還原現有的資料庫

使用 Azure 入口網站、PowerShell 或 Azure CLI，將現有的資料庫還原至相同的實例。 若要將資料庫還原到另一個實例，請使用 PowerShell 或 Azure CLI，讓您可以指定目標受控實例和資源群組的屬性。 如果您未指定這些參數，則預設會將資料庫還原至現有的實例。 Azure 入口網站目前不支援還原至另一個實例。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 移至您的受控實例，然後選取您想要還原的資料庫。
3. 在 [資料庫] 頁面上選取 [**還原**]：

    ![使用 Azure 入口網站還原資料庫](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. 在 [**還原**] 頁面上，選取您想要還原資料庫的日期和時間點。
5. 選取 [**確認**] 以還原您的資料庫。 此動作會啟動還原程式，此程式會建立新的資料庫，並在指定的時間點，將原始資料庫的資料填入其中。 如需復原程式的詳細資訊，請參閱復原[時間](sql-database-recovery-using-backups.md#recovery-time)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

如果您尚未安裝 Azure PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要使用 PowerShell 來還原資料庫，請在下列命令中指定參數的值。 然後，執行命令：

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

若要將資料庫還原到另一個受控實例，請同時指定目標資源群組和目標受控實例的名稱：  

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

如需詳細資訊，請參閱[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果您尚未安裝 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要使用 Azure CLI 來還原資料庫，請在下列命令中指定參數的值。 然後，執行命令：

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

若要將資料庫還原到另一個受控實例，請同時指定目標資源群組和受控實例的名稱：  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

如需可用參數的詳細說明，請參閱[CLI 檔，以瞭解如何在受控實例中還原資料庫](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)。

---

## <a name="restore-a-deleted-database"></a>還原已刪除的資料庫

您可以使用 PowerShell 或 Azure 入口網站來還原已刪除的資料庫。 若要將已刪除的資料庫還原到相同的實例，請使用 Azure 入口網站或 PowerShell。 若要將已刪除的資料庫還原到另一個實例，請使用 PowerShell。 

### <a name="portal"></a>入口網站 


若要使用 Azure 入口網站復原受管理的資料庫，請開啟 [受控實例總覽] 頁面，然後選取 [**已刪除的資料庫**]。 選擇您想要還原的已刪除資料庫，然後輸入將使用從備份還原之資料所建立的新資料庫名稱。

  ![還原已刪除的 Azure SQL 實例資料庫的螢幕擷取畫面](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

若要將資料庫還原到相同的實例，請更新參數值，然後執行下列 PowerShell 命令： 

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

若要將資料庫還原到另一個受控實例，請同時指定目標資源群組和目標受控實例的名稱：

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

## <a name="overwrite-an-existing-database"></a>覆寫現有的資料庫

若要覆寫現有的資料庫，您必須：

1. 卸載您想要覆寫的現有資料庫。
2. 將還原時間點的資料庫重新命名為您卸載之資料庫的名稱。

### <a name="drop-the-original-database"></a>卸載原始資料庫

您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來卸載資料庫。

您也可以直接連接到受控實例來卸載資料庫，從 SQL Server Management Studio （SSMS）開始，然後執行下列 Transact-sql （T-sql）命令：

```sql
DROP DATABASE WorldWideImporters;
```

使用下列其中一種方法來連線到受控實例中的資料庫：

- [透過 Azure 虛擬機器的 SSMS/Azure Data Studio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [點對站](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 [Azure 入口網站中，選取受控實例中的資料庫，然後選取 [**刪除**]。

   ![使用 Azure 入口網站刪除資料庫](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用下列 PowerShell 命令，從受控實例中卸載現有的資料庫：

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用下列 Azure CLI 命令，從受控實例中卸載現有的資料庫：

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>改變新的資料庫名稱，使其符合原始資料庫名稱

直接連接到受控實例並啟動 SQL Server Management Studio。 然後，執行下列 Transact-sql （T-sql）查詢。 查詢會將還原的資料庫名稱變更為您想要覆寫之已卸載資料庫的名稱。

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

使用下列其中一種方法來連線到受控實例中的資料庫：

- [Azure 虛擬機器](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [點對站](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>後續步驟

瞭解[自動備份](sql-database-automated-backups.md)。
