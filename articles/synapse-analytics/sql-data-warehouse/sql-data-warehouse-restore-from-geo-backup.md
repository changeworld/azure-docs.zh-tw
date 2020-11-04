---
title: 從異地備份還原專用的 SQL 集區
description: 在 Azure Synapse Analytics 中異地還原專用 SQL 集區的操作指南
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7496cedd127182482bccf97909cc0a0a4a78253f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313410"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中異地還原專用的 SQL 集區

在本文中，您將瞭解如何透過 Azure 入口網站和 PowerShell，從異地備份還原專用的 SQL 集區。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個專用的 SQL 集區都是由 [邏輯 sql server](../../azure-sql/database/logical-servers.md) 所主控 (例如，具有預設 DTU 配額的 myserver.database.windows.net) 。 確認 SQL server 有足夠的剩餘 DTU 配額來還原資料庫。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>透過 PowerShell 從 Azure 地理區域還原

若要從異地備份還原，請使用 [AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 和 [restore->set-azsqldatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Cmdlet。

> [!NOTE]
> 您可以執行異地還原來還原至 Gen2！ 若要這麼做，請指定 Gen2 ServiceObjectiveName (例如 DW1000 **c** ) 作為選擇性參數。
>

1. 開始之前，請務必 [安裝 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 開啟 PowerShell。
3. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
4. 選取包含要還原之資料倉儲的訂用帳戶。
5. 取得您想要復原的資料倉儲。
6. 建立資料倉儲的復原要求。
7. 確認異地還原資料倉儲的狀態。
8. 若要在還原完成之後設定資料倉儲，請參閱[在復原之後設定資料庫]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

如果來源資料庫是啟用 TDE，則復原的資料庫將是啟用 TDE。

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>透過 Azure 入口網站從 Azure 地理區域還原

請依照下面所述的步驟，從異地備份還原專用的 SQL 集區：

1. 登入您的 [Azure 入口網站](https://portal.azure.com/) 帳戶。
2. 按一下 [+ 建立資源]。

   ![新 DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. 按一下 [ **資料庫** ]，然後 **Azure Synapse Analytics (先前的 SQL DW)** 。

   ![新 DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. 填寫 [ **基本** ] 索引標籤中所要求的資訊，然後按 **[下一步：其他設定** ]。

   ![基本動作](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. 若要 **使用現有的資料** 參數，請選取 [ **備份** ]，然後從下拉選項中選取適當的備份。 按一下 [檢閱 + 建立]。

   ![備份 (backup)](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. 還原資料倉儲之後，請檢查 **狀態** 是否為「線上」。

## <a name="next-steps"></a>後續步驟

- [還原現有的專用 SQL 集區](sql-data-warehouse-restore-active-paused-dw.md)
- [還原已刪除的專用 SQL 集區](sql-data-warehouse-restore-deleted-dw.md)
