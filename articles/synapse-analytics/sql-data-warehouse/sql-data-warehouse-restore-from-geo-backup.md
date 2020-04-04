---
title: 從異地備份還原資料倉庫
description: 地理還原 SQL 池的指南。
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
ms.openlocfilehash: 68d53d2a33b7ab705dfa88f03618a5d5a3d1bced
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633034"
---
# <a name="geo-restore-for-sql-pool"></a>SQL 池的地理還原

在本文中,您將瞭解如何通過 Azure 門戶和 PowerShell 從地理備份還原 SQL 池。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個 SQL 池都由具有預設 DTU 配額的 SQL 伺服器(例如myserver.database.windows.net)託管。 驗證 SQL 伺服器是否有足夠的剩餘 DTU 配額來還原資料庫。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>以 PowerShell 從 Azure 地理區域還原

要從異地備份進行還原,請使用[獲取-AzSql 資料庫地理備份](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup)和[還原-AzSql 資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)cmdlet。

> [!NOTE]
> 您可以執行異地還原來還原至 Gen2！ 若要這麼做，請指定 Gen2 ServiceObjectiveName (例如 DW1000**c**) 作為選擇性參數。
>

1. 開始之前,請確保安裝 Azure [PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 開啟 PowerShell。
3. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
4. 選擇包含要還原的數據倉庫的訂閱。
5. 獲取要恢復的數據倉庫。
6. 為數據倉庫創建恢復請求。
7. 驗證異地還原數據倉庫的狀態。
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

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>通過 Azure 門戶從 Azure 地理區域還原

按照下面概述的步驟從異地備份還原 SQL 池:

1. 登錄到 Azure[門戶](https://portal.azure.com/)帳戶。
2. 按一下 [+ 建立資源]****。

   ![新 DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. 按下 **「資料庫**」,然後單擊 [Azure 突觸分析(以前的 SQL DW) *。

   ![新 DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. 填寫基礎**知識選項**卡中請求的資訊,然後單擊**下一步:其他設置**"。

   ![基本概念](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. 對於**使用現有資料**參數,請選擇 **「備份」** 並從向下滾動選項中選擇相應的備份。 按下 **「審閱 + 創建**」。

   ![備份 (backup)](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. 還原數據倉庫后,檢查**狀態**是否為連線狀態。

## <a name="next-steps"></a>後續步驟

- [回復的 SQL 池](sql-data-warehouse-restore-active-paused-dw.md)
- [回復已移除的 SQL 池](sql-data-warehouse-restore-deleted-dw.md)
