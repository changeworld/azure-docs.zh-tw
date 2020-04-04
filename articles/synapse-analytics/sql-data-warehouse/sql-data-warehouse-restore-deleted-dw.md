---
title: 回復已移除的 SQL 池
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
ms.openlocfilehash: 251fdb83e848aaac3a5391320df23149ce1bce33
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633056"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>使用 Azure 同步分析回復已移除的 SQL 池

在本文中,您將學習使用 Azure 門戶或 PowerShell 還原 SQL。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個 SQL 池都由具有預設 DTU 配額的 SQL 伺服器(例如myserver.database.windows.net)託管。  驗證 SQL 伺服器是否有足夠的剩餘 DTU 配額來還原資料庫。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>以 PowerShell 回復的資料主目錄

要還原已刪除的 SQL 池,請使用[還原-AzSql 資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)cmdlet。 如果相應的邏輯伺服器也被刪除,則無法還原該數據倉庫。

1. 開始之前,請確保安裝 Azure [PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 開啟 PowerShell。
3. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
4. 選擇包含要還原的已刪除數據倉庫的訂閱。
5. 獲取特定已刪除的數據倉庫。
6. 回復已移除的資料主目錄
    1. 要將已刪除的 SQL 資料倉庫還原到其他邏輯伺服器,請確保指定其他邏輯伺服器名稱。  此邏輯伺服器也可以位於不同的資源組和地區中。
    1. 要還原到其他訂閱,請使用[「移動」](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal)按鈕將邏輯伺服器移動到其他訂閱。
7. 驗證還原的數據倉庫是否連線。
8. 還原完成後,您可以通過在[恢復后按照配置資料庫來配置](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)恢復的數據倉庫。

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
2. 導航到已刪除的數據倉庫託管的 SQL 伺服器。
3. 在目錄中選擇 **「已刪除的資料庫**」圖示。

    ![已刪除的資料庫](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 選擇要還原的已刪除 SQL 資料倉儲。

    ![選取已刪除的資料庫](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 指定新的**資料庫名稱**,然後單擊 **"確定"**

    ![指定資料庫名稱](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>後續步驟

- [回復的 SQL 池](sql-data-warehouse-restore-active-paused-dw.md)
- [從異地備份 SQL 池還原](sql-data-warehouse-restore-from-geo-backup.md)
