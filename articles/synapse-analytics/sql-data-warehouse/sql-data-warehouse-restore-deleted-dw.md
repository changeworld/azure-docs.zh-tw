---
title: '將已刪除的專用 SQL 集區還原 (先前的 SQL DW) '
description: 在 Azure Synapse Analytics 中還原已刪除的專用 SQL 集區的操作指南。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7264791654bf1b646338f0d429930b63f0cc3a06
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449919"
---
# <a name="restore-a-deleted-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>將已刪除的專用 SQL 集區 (先前的 SQL DW) 還原 Azure Synapse Analytics

在本文中，您將瞭解如何使用 Azure 入口網站或 PowerShell，將專用的 SQL 集區 (先前的 SQL DW) 來還原。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個專用的 SQL 集區 (先前的 SQL DW) 是由 [邏輯 SQL server](../../azure-sql/database/logical-servers.md) 所裝載 (例如，具有預設 DTU 配額的 myserver.database.windows.net) 。  確認伺服器有足夠的剩餘 DTU 配額來還原資料庫。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>透過 PowerShell 還原已刪除的資料倉儲

若要將已刪除的專用 SQL 集區還原 (先前的 SQL DW) ，請使用 [>set-azsqldatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Cmdlet。 如果對應的伺服器也已刪除，您就無法還原該資料倉儲。

1. 開始之前，請務必 [安裝 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 開啟 PowerShell。
3. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
4. 選取包含已刪除專用 SQL 集區的訂用帳戶， (先前要還原的 SQL DW) 。
5. 取得特定已刪除的資料倉儲。
6.  (先前的 SQL DW 還原已刪除的專用 SQL 集區) 
    1. 若要將已刪除的專用 SQL 集區 (先前的 SQL DW) 還原至不同的伺服器，請務必指定其他伺服器名稱。  此伺服器也可以位於不同的資源群組和區域中。
    1. 若要還原至不同的訂用帳戶，請使用 [ [移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) ] 按鈕將伺服器移至另一個訂用帳戶。
7. 確認還原的資料倉儲已上線。
8. 還原完成之後，您可以在復原 [之後設定資料庫](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)，以設定復原的資料倉儲。

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>使用 Azure 入口網站還原已刪除的資料庫

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 流覽至您已刪除的資料倉儲所裝載的伺服器。
3. 選取目錄中的 [ **已刪除的資料庫** ] 圖示。

    ![已刪除的資料庫](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 選取您要還原的已刪除 Azure Synapse Analytics。

    ![選取 [已刪除的資料庫]](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 指定新的 **資料庫名稱** ，然後按一下 **[確定]**

    ![指定資料庫名稱](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>後續步驟

- [將現有的專用 SQL 集區還原 (先前的 SQL DW) ](sql-data-warehouse-restore-active-paused-dw.md)
- [從異地備份專用 SQL 集區還原 (先前為 SQL DW) ](sql-data-warehouse-restore-from-geo-backup.md)
