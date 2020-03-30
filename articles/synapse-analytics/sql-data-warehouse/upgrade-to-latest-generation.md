---
title: 升級到最新一代
description: 將 Azure Synapse 分析 SQL 池升級到最新一代的 Azure 硬體和存儲體系結構。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 637e377e469eeb1a82b6c0ad3a845d94ac09c7db
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351203"
---
# <a name="optimize-performance-by-upgrading-azure-synapse-analytics-sql-pool"></a>通過升級 Azure 突觸分析 SQL 池來優化性能

將 SQL 池升級到最新一代的 Azure 硬體和存儲體系結構。

## <a name="why-upgrade"></a>為何要升級？

現在，您可以無縫升級到 Azure 門戶中[支援區域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)的 SQL 池計算優化第 2 代層。 如果您所在的區域不支援自我升級，您可以升級至支援的區域，或靜候您的區域支援自我升級。 請立即升級，以利用最新一代的 Azure 硬體和增強的儲存體架構，包括更快的效能、更高的延展性，以及無限制的單欄式儲存體。 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>適用於

此升級適用于[受支援區域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)中的計算優化第 1 代 SQL 池。

## <a name="before-you-begin"></a>開始之前

1. 確認您所在的[區域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)是否支援從 GEN1 移轉至 GEN2。 請注意自動移轉日期。 若要避免與自動化程序發生衝突，請規劃在自動化程序開始日期之前進行手動移轉。
2. 如果您所在的區域尚不提供支援，請繼續檢查您所要新增的區域，或[使用還原升級](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal)至支援的區域。
3. 如果您所在的區域已提供支援，則請[透過 Azure 入口網站來升級](#upgrade-in-a-supported-region-using-the-azure-portal)
4. 使用以下映射，根據計算優化第 1 代層上的當前性能級別選擇 SQL 池**的建議性能級別**：

   | 計算最佳化 Gen1 層 | 計算最佳化 Gen2 層 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> 建議的效能層級並非直接轉換。 例如，我們建議從 DW600 轉換至 DW500c。

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>使用 Azure 入口網站在支援的區域中進行升級

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> 通過 Azure 門戶從 Gen 1 遷移到第 2 代是永久性的。 沒有返回第 1 代的過程。  

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登錄到 Azure[門戶](https://portal.azure.com/)。

1. 如果要升級的計算優化第 1 層 SQL 池已暫停，[請繼續 SQL 池](pause-and-resume-compute-portal.md)。

   > [!NOTE]
   > SQL 池必須運行才能遷移到 Gen2。

2. 請做好停機幾分鐘的準備。 

3. 找出計算最佳化 Gen1 效能層級的任何程式碼參考，並修改其對等計算最佳化 Gen2 效能層級的程式碼參考。 以下是您應該在升級前更新程式碼參考的兩個範例：

   原始 Gen1 PowerShell 命令：

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   修改為：

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" 已變更為 - RequestedServiceObjectiveName "DW300**c**"
   >

   原始 Gen1 T-SQL 命令：

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   修改為：

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = 'DW300' 已變更為 SERVICE_OBJECTIVE = 'DW300**c**'

## <a name="start-the-upgrade"></a>開始升級

1. 轉到 Azure 門戶中的計算優化第 1 代 SQL 池。 如果要升級的計算優化第 1 層 SQL 池已暫停，[請繼續 SQL 池](pause-and-resume-compute-portal.md)。 
2. 在"任務"選項卡下選擇 **"升級到 Gen2"** 卡：Upgrade_1 ![](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)
    
    > [!NOTE]
    > 如果您未在 [工作] 索引標籤下看見 [升級至 Gen2]**** 卡片，您的訂用帳戶類型受限於目前的區域。
    > [提交支援票證](sql-data-warehouse-get-started-create-support-ticket.md)，將您的訂用帳戶列入允許清單中。

3. 在升級之前，請確定您的工作負載已完成執行並停止。 在 SQL 池作為計算優化 Gen2 層 SQL 池重新連線之前，您將遇到幾分鐘的停機時間。 **選取升級**：

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. 檢查 Azure 入口網站中的狀態來**監視您的升級**：

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   升級程序的第一個步驟會進行調整規模作業 (「升級 - 離線」)，期間所有工作階段都會終止，且連線將會予以捨棄。 

   升級程序的第二個步驟是資料移轉 (「升級 - 上線」)。 資料移轉是在線上慢慢進行的背景程序。 此程序會將單欄式資料慢慢地從舊的儲存體架構移至使用本機 SSD 快取的新儲存體架構。 在此期間，您的 SQL 池將連線以進行查詢和載入。 您的資料無論已遷移與否，都將可供查詢。 資料移轉會依據您的資料大小、效能層級和資料行存放區的區段數目，而以不同的速率進行。 

5. **可選建議：** 縮放操作完成後，可以加快資料移轉後臺進程。 您可以用較大的 SLO 和資源類別，在您會查詢的所有主要資料行存放區資料表上執行 [Alter Index rebuild](sql-data-warehouse-tables-index.md)，以強制移動資料。 相較於會慢慢進行的背景程序，這項作業會**離線**進行，因此可能需要數小時才能完成 (視資料表的數量和大小而定)。 不過一旦完成，資料移轉速度就會大幅提升，因為增強後的新儲存體架構具有高品質的資料列群組。
 
> [!NOTE]
> Alter Index rebuild 是一種離線作業，資料表在重建完成後才可使用。

下列查詢會產生必要的 Alter Index Rebuild 命令，以加速資料移轉：

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>透過 Azure 入口網站使用還原功能從 Azure 地理區域升級

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>使用 Azure 入口網站建立使用者定義的還原點

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

2. 導航到要為其創建還原點的 SQL 池。

3. 在 [概觀] 區段頂端，選取 [+ 新增還原點]****。

    ![新增還原點](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. 指定還原點的名稱。

    ![還原點名稱](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>使用 Azure 入口網站還原作用中或已暫停的資料庫

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 導航到要還原的 SQL 池。
3. 在 [概觀] 區段頂端，選取 [還原]****。

    ![ 還原概觀](./media/upgrade-to-latest-generation/restoring_0.png)

4. 選擇**自動還原點**或**使用者定義的還原點**。 對於使用者定義的還原點，**請選擇使用者定義的還原點**或**創建新的使用者定義的還原點**。 對於伺服器，選擇 **"新建"** 並選擇 Gen2 支援的地理區域中的伺服器。 

    ![自動還原點](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>使用 PowerShell 從 Azure 地理區域還原

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

要恢復資料庫，請使用[還原-AzSql 資料庫](/powershell/module/az.sql/restore-azsqldatabase)Cmdlet。

> [!NOTE]
> 您可以執行異地還原來還原至 Gen2！ 若要這麼做，請指定 Gen2 ServiceObjectiveName (例如 DW1000**c**) 作為選擇性參數。

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
3. 選取包含要還原之資料庫的訂用帳戶。
4. 取得您想要復原的資料庫。
5. 建立資料庫的復原要求，指定 Gen2 ServiceObjectiveName。
6. 確認異地還原資料庫的狀態。

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> 若要在還原完成之後設定資料庫，請參閱 [在復原之後設定資料庫](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)。

如果來源資料庫是啟用 TDE，則復原的資料庫將是啟用 TDE。


如果 SQL 池遇到任何問題，請創建[支援請求](sql-data-warehouse-get-started-create-support-ticket.md)，並將"Gen2 升級"作為可能的原因。

## <a name="next-steps"></a>後續步驟

升級後的 SQL 池處於線上狀態。 若要充分利用增強的架構，請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)。
