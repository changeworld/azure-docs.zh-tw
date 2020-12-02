---
title: 還原現有的專用 SQL 集區
description: 在 Azure Synapse Analytics 中還原現有專用 SQL 集區的操作指南。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d3c84f7d951db715d26042f524f385826197e076
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450013"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>將現有的專用 SQL 集區還原 (先前的 SQL DW) 

在本文中，您將瞭解如何使用 Azure 入口網站和 PowerShell，將現有的專用 SQL 集區 (先前的 SQL DW) 還原。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個集區都是由 [邏輯 SQL server](../../azure-sql/database/logical-servers.md) 所主控 (例如，具有預設 DTU 配額的 myserver.database.windows.net) 。 確認伺服器有足夠的剩餘 DTU 配額來還原資料庫。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="before-you-begin"></a>開始之前

1. 請務必 [安裝 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 具有您想要從中還原的現有還原點。 如果您想要建立新的還原，請參閱教學課程， [以建立新的使用者定義還原點](sql-data-warehouse-restore-points.md)。

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>透過 PowerShell 將現有的專用 SQL 集區 (先前的 SQL DW) 還原

若要從還原點還原現有的專用 SQL 集區 (先前的 SQL DW) ，請使用 [>set-azsqldatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell Cmdlet。

1. 開啟 PowerShell。

2. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。

3. 選取包含要還原之資料庫的訂用帳戶。

4. 列出專用 SQL 集區 (先前的 SQL DW) 的還原點。

5. 使用 RestorePointCreationDate 挑選出想要的還原點。

6. 使用 [>set-azsqldatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell Cmdlet 將專用 sql 集區 (先前的 sql DW) 還原至所需的還原點。

    1. 若要將專用的 SQL 集區 (先前的 SQL DW) 還原至不同的伺服器，請務必指定其他伺服器名稱。  此伺服器也可以位於不同的資源群組和區域中。
    2. 若要還原至不同的訂用帳戶，請使用 [移動] 按鈕將伺服器移至另一個訂用帳戶。

7. 確認先前 SQL DW)  (已還原的專用 SQL 集區已上線。

8. 還原完成之後，您可以在復原 [之後設定資料庫](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)，以將已復原的專用 SQL 集區 (先前的 sql DW) 。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>透過 Azure 入口網站還原現有的專用 SQL 集區 (先前的 SQL DW) 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 流覽至您想要從中還原的專用。
3. 在 [概觀] 刀鋒視窗頂端，選取 [還原]。

    ![ 還原概觀](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. 選取 [自動還原點] 或 [使用者定義的還原點]。 如果專用的 SQL 集區 (先前的 SQL DW) 沒有任何自動還原點，請等候幾個小時，或在還原之前先建立使用者定義的還原點。 針對 User-Defined 還原點，請選取現有的還原點，或建立一個新的。 針對 **伺服器**，您可以選擇不同資源群組和區域中的伺服器，或建立一個新的伺服器。 提供所有參數之後，請按一下 [ **審核 + 還原**]。

    ![自動還原點](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>後續步驟

- [將已刪除的專用 SQL 集區還原 (先前的 SQL DW) ](sql-data-warehouse-restore-deleted-dw.md)
- [從異地備份專用 SQL 集區還原 (先前為 SQL DW) ](sql-data-warehouse-restore-from-geo-backup.md)
