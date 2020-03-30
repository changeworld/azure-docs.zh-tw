---
title: 還原已刪除的 SQL 池
description: 如何指導還原已刪除的 SQL 池。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5f0432cafee07dbed071d24aa8c24ee9b2176967
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350183"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>使用 Azure 同步分析還原已刪除的 SQL 池

在本文中，您將學習使用 Azure 門戶或 PowerShell 還原 SQL。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個 SQL 池都由具有預設 DTU 配額的 SQL 伺服器（例如myserver.database.windows.net）託管。  驗證 SQL 伺服器是否有足夠的剩餘 DTU 配額來還原資料庫。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>通過 PowerShell 還原已刪除的資料倉儲

要還原已刪除的 SQL 池，請使用[還原-AzSql 資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)Cmdlet。 如果相應的邏輯伺服器也被刪除，則無法還原該資料倉儲。

1. 開始之前，請確保安裝 Azure [PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 開啟 PowerShell。
3. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
4. 選擇包含要還原的已刪除資料倉儲的訂閱。
5. 獲取特定已刪除的資料倉儲。
6. 還原已刪除的資料倉儲
    1. 要將已刪除的 SQL 資料倉儲還原到其他邏輯伺服器，請確保指定其他邏輯伺服器名稱。  此邏輯伺服器也可以位於不同的資源組和地區中。
    1. 要還原到其他訂閱，請使用["移動"](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal)按鈕將邏輯伺服器移動到其他訂閱。
1. 驗證還原的資料倉儲是否連線。
1. 還原完成後，您可以通過在[恢復後按照設定資料庫來配置](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)恢復的資料倉儲。

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

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 導航到已刪除的資料倉儲託管的 SQL 伺服器。
3. 在目錄中選擇 **"已刪除的資料庫**"圖示。

    ![已刪除的資料庫](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 選擇要還原的已刪除 SQL 資料倉儲。

    ![選取已刪除的資料庫](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 指定新的**資料庫名稱**，然後按一下 **"確定"**

    ![指定資料庫名稱](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>後續步驟
- [還原現有 SQL 池](sql-data-warehouse-restore-active-paused-dw.md)
- [從異地備份 SQL 池還原](sql-data-warehouse-restore-from-geo-backup.md)