---
title: 從異地備份還原資料倉儲
description: 異地還原 SQL 集區的操作指南。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e0980a9142dc966916d5a4df898ea53b0ddeae5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745072"
---
# <a name="geo-restore-for-sql-pool"></a>SQL 集區的異地還原

在本文中，您會瞭解如何透過 Azure 入口網站和 PowerShell 從異地備份還原 SQL 集區。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個 SQL 集區都是由具有預設 DTU 配額的 SQL server （例如 myserver.database.windows.net）所主控。 確認 SQL server 有足夠的剩餘 DTU 配額可供要還原的資料庫之用。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>透過 PowerShell 從 Azure 地理區域還原

若要從異地備份還原，請使用[AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)和[restore-set-azsqldatabase 搭配](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)Cmdlet。

> [!NOTE]
> 您可以執行異地還原來還原至 Gen2！ 若要這麼做，請指定 Gen2 ServiceObjectiveName (例如 DW1000**c**) 作為選擇性參數。
>

1. 開始之前，請務必[安裝 Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
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
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
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

請遵循以下所述的步驟，從異地備份還原 SQL 集區：

1. 登入您的[Azure 入口網站](https://portal.azure.com/)帳戶。
2. 按一下 [+ 建立資源]****。

   ![新的 DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. 依序按一下 [**資料庫**] 和 [Azure Synapse 分析] （先前為 SQL DW） * *。

   ![新 DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. 在 [**基本**] 索引標籤中填寫要求的資訊，然後按 **[下一步：其他設定**]。

   ![基本概念](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. 針對 [**使用現有的資料**] 參數，選取 [**備份**]，然後從 [向下] 選項中選取適當的備份。 按一下 [**檢查 + 建立**]。

   ![備份 (backup)](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. 還原資料倉儲之後，請檢查**狀態**是否為 [線上]。

## <a name="next-steps"></a>後續步驟

- [還原現有的 SQL 集區](sql-data-warehouse-restore-active-paused-dw.md)
- [還原已刪除的 SQL 集區](sql-data-warehouse-restore-deleted-dw.md)
