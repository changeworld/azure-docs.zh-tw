---
title: 還原已刪除的 SQL 集區
description: 還原已刪除之 SQL 集區的操作指南。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 34851203432b7e2daf44e840e45275de76bc3b3a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196589"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>使用 Azure Synapse 分析還原已刪除的 SQL 集區

在本文中，您將瞭解如何使用 Azure 入口網站或 PowerShell 來還原 SQL。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個 SQL 集區都是由具有預設 DTU 配額的 SQL server （例如 myserver.database.windows.net）所主控。  確認 SQL server 有足夠的剩餘 DTU 配額可供要還原的資料庫之用。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>透過 PowerShell 還原已刪除的資料倉儲

若要還原已刪除的 SQL 集區，請使用[set-azsqldatabase 搭配](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)Cmdlet。 如果對應的邏輯伺服器也已刪除，您就無法還原該資料倉儲。

1. 開始之前，請務必[安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 開啟 PowerShell。
3. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
4. 選取包含要還原之已刪除資料倉儲的訂用帳戶。
5. 取得特定已刪除的資料倉儲。
6. 還原已刪除的資料倉儲
    1. 若要將已刪除的 SQL 資料倉儲還原到不同的邏輯伺服器，請務必指定其他邏輯伺服器名稱。  此邏輯伺服器也可以位於不同的資源群組和區域中。
    1. 若要還原至不同的訂用帳戶，請使用 [[移動](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal)] 按鈕將邏輯伺服器移至另一個訂用帳戶。
1. 確認還原的資料倉儲已上線。
1. 還原完成之後，您可以遵循在復原[之後設定資料庫](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)來設定已復原的資料倉儲。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
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

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>使用 Azure 入口網站還原已刪除的資料庫

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 流覽至您已刪除資料倉儲的主控 SQL server。
3. 在目錄中選取 [**已刪除的資料庫**] 圖示。

    ![已刪除的資料庫](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 選取您想要還原的已刪除 SQL 資料倉儲。

    ![選取已刪除的資料庫](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 指定新的**資料庫名稱**，然後按一下 **[確定]**

    ![指定資料庫名稱](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>後續步驟
- [還原現有的 SQL 集區](sql-data-warehouse-restore-active-paused-dw.md)
- [從異地備份 SQL 集區還原](sql-data-warehouse-restore-from-geo-backup.md)