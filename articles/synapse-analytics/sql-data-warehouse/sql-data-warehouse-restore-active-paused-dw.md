---
title: 還原現有的資料倉儲
description: 還原現有 SQL 集區的操作指南。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f8dc7be63506e586b90d1e389d26ac591dce9c3b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075882"
---
# <a name="restore-an-existing-sql-pool"></a>還原現有的 SQL 集區

在本文中，您將瞭解如何使用 Azure 入口網站和 PowerShell，在 Azure Synapse 分析中還原現有的 SQL 集區。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個集區都是由具有預設 DTU 配額的[邏輯 SQL 伺服器](../../azure-sql/database/logical-servers.md)（例如，myserver.database.windows.net）所主控。 確認伺服器有足夠的剩餘 DTU 配額可供要還原的資料庫。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="before-you-begin"></a>開始之前

1. 請務必[安裝 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 擁有您想要從中還原的現有還原點。 如果您想要建立新的還原，請參閱教學課程，[以建立新的使用者定義還原點](sql-data-warehouse-restore-points.md)。

## <a name="restore-an-existing-sql-pool-through-powershell"></a>透過 PowerShell 還原現有的 SQL 集區

若要從還原點還原現有的 SQL 集區，請使用[Set-azsqldatabase 搭配](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)PowerShell Cmdlet。

1. 開啟 PowerShell。

2. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。

3. 選取包含要還原之資料庫的訂用帳戶。

4. 列出 SQL 集區的還原點。

5. 使用 RestorePointCreationDate 挑選出想要的還原點。

6. 使用[Set-azsqldatabase 搭配](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)PowerShell Cmdlet，將 SQL 集區還原到所需的還原點。

    1. 若要將 SQL 集區還原到不同的伺服器，請務必指定其他伺服器名稱。  此伺服器也可以位於不同的資源群組和區域中。
    2. 若要還原至不同的訂用帳戶，請使用 [移動] 按鈕，將伺服器移至另一個訂用帳戶。

7. 確認還原的 SQL 集區已上線。

8. 還原完成之後，您可以遵循在復原[之後設定資料庫](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)來設定已復原的 SQL 集區。

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
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>透過 Azure 入口網站還原現有的 SQL 集區

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 流覽至您想要從中還原的 SQL 集區。
3. 在 [概觀] 刀鋒視窗頂端，選取 [還原]****。

    ![ 還原概觀](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. 選取 [自動還原點]**** 或 [使用者定義的還原點]****。 如果 SQL 集區沒有任何自動還原點，請等待幾個小時，或先建立使用者定義的還原點，再進行還原。 若為使用者定義的還原點，請選取現有的還原點，或建立一個新的。 針對 [**伺服器**]，您可以在不同的資源群組和區域中挑選伺服器，或建立一個新的。 提供所有參數之後，請按一下 [**審核 + 還原**]。

    ![自動還原點](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>後續步驟

- [還原已刪除的 SQL 集區](sql-data-warehouse-restore-deleted-dw.md)
- [從異地備份 SQL 集區還原](sql-data-warehouse-restore-from-geo-backup.md)
