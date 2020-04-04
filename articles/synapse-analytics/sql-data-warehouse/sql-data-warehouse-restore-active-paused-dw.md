---
title: 還原現有的資料倉儲
description: 還原現有 SQL 池的指南。
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
ms.openlocfilehash: e1d0c5cd850fa0dbacf104d3fcd72d83007df878
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632257"
---
# <a name="restore-an-existing-sql-pool"></a>回復的 SQL 池

在本文中,您將瞭解如何使用 Azure 門戶和 PowerShell 在 Azure 突觸分析中還原現有 SQL 池。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個池都由具有預設 DTU 配額的 SQL 伺服器(例如myserver.database.windows.net)託管。 驗證 SQL 伺服器有足夠的剩餘 DTU 配額來還原資料庫。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="before-you-begin"></a>開始之前

1. 請確定安裝[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 具有要還原的現有還原點。 如果要建立新還原,請參閱[教學以建立新的使用者定義的還原點](sql-data-warehouse-restore-points.md)。

## <a name="restore-an-existing-sql-pool-through-powershell"></a>透過 PowerShell 還原現有 SQL 池

要從還原點還原現有 SQL 池,請使用[還原-AzSql 資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)PowerShell cmdlet。

1. 開啟 PowerShell。

2. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。

3. 選取包含要還原之資料庫的訂用帳戶。

4. 列出 SQL 池的還原點。

5. 使用 RestorePointCreationDate 挑選出想要的還原點。

6. 使用[還原-AzSql資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)PowerShell cmdlet 將 SQL 池還原到所需的還原點。
        1. 要將 SQL 池還原到其他邏輯伺服器,請確保指定其他邏輯伺服器名稱。  此邏輯伺服器也可以位於不同的資源組和地區中。
        2. 要還原到其他訂閱,請使用「移動」按鈕將邏輯伺服器移動到其他訂閱。

7. 驗證還原的 SQL 池是否連線。

8. 還原完成後,可以通過在[恢復后按照配置資料庫來配置](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)恢復的 SQL 池。

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>通過 Azure 門戶還原現有 SQL 池

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 導航到要還原的 SQL 池。
3. 在 [概觀] 刀鋒視窗頂端，選取 [還原]****。

    ![ 還原概觀](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. 選取 [自動還原點]**** 或 [使用者定義的還原點]****。 如果 SQL 池沒有任何自動還原點,請等待幾個小時或創建使用者定義的還原點,然後再還原。 對於使用者定義的還原點,請選擇現有還原點或創建新還原點。 對於**伺服器**,您可以在不同的資源組和地區中選擇邏輯伺服器,也可以創建新的資源組和區域。 提供所有參數后,單擊「**審閱 + 還原**」。。

    ![自動還原點](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>後續步驟

- [回復已移除的 SQL 池](sql-data-warehouse-restore-deleted-dw.md)
- [從異地備份 SQL 池還原](sql-data-warehouse-restore-from-geo-backup.md)
