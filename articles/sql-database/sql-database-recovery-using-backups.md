---
title: 從備份還原資料庫
description: 瞭解時間點還原，這使您能夠回滾 Azure SQL 資料庫長達 35 天。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268739"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>使用自動資料庫備份恢復 Azure SQL 資料庫

預設情況下，Azure SQL 資料庫備份存儲在異地複製的 Blob 存儲（RA-GRS 存儲類型） 中。 以下選項可用於使用[自動資料庫備份](sql-database-automated-backups.md)進行資料庫恢復。 您可以：

- 在同一 SQL 資料庫伺服器上創建新資料庫，在保留期內恢復到指定的時間點。
- 在同一 SQL 資料庫伺服器上創建資料庫，恢復到已刪除資料庫的刪除時間。
- 在同一區域中的任何 SQL 資料庫伺服器上創建新資料庫，恢復到最新備份的點。
- 在任何其他區域的任何 SQL 資料庫伺服器上創建新資料庫，恢復到最近複製的備份點。

如果配置[了備份長期保留](sql-database-long-term-retention.md)，還可以從任何 SQL 資料庫伺服器上的任何長期保留備份創建新資料庫。

> [!IMPORTANT]
> 在還原期間無法覆蓋現有資料庫。

當您使用標準或高級服務層時，資料庫還原可能會產生額外的存儲成本。 當還原資料庫的最大大小大於目標資料庫的服務層和性能級別中包含的存儲量時，會產生額外的成本。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際使用空間量小於包括的存儲量，則可以通過將最大資料庫大小設置為包含的量來避免此額外費用。

## <a name="recovery-time"></a>復原時間

使用自動資料庫備份還原資料庫的恢復時間受以下幾個因素影響：

- 資料庫的大小。
- 資料庫的計算大小。
- 所涉及的事務日誌數。
- 需要重播以恢復到還原點的活動量。
- 如果還原是到其他區域，則網路頻寬。
- 要在目標區域中處理的並行還原要求數目。

對於大型或非常活躍的資料庫，還原可能需要幾個小時。 如果區域長時間中斷，則可能會啟動大量異地恢復請求以進行災害復原。 當有許多請求時，各個資料庫的恢復時間可能會增加。 大多數資料庫還原都會在 12 小時內完成。

對於單個訂閱，併發還原請求的數量有限制。 這些限制適用于時間點還原、異地還原和長期保留備份還原的任意組合。

| | **正在處理的並行要求的最大數目** | **正在提交的並行要求的最大數目** |
| :--- | --: | --: |
|單一資料庫 (每個訂閱)|10|60|
|彈性集區 (每個集區)|4|200|
||||

沒有用於還原整個伺服器的內置方法。 有關如何完成此任務的示例，請參閱[Azure SQL 資料庫：完整伺服器恢復](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)。

> [!IMPORTANT]
> 要使用自動備份進行恢復，您必須是訂閱中的 SQL Server 參與者角色的成員，或者是訂閱擁有者。 有關詳細資訊，請參閱[RBAC：內置角色](../role-based-access-control/built-in-roles.md)。 可以使用 Azure 門戶、PowerShell 或 REST API 進行恢復。 不能使用 Transact-SQL。

## <a name="point-in-time-restore"></a>時間點還原

可以使用 Azure 門戶[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)或[REST API](https://docs.microsoft.com/rest/api/sql/databases)將獨立、池或實例資料庫還原到較早的時間點。 請求可以為還原的資料庫指定任何服務層或計算大小。 確保還原資料庫的伺服器上有足夠的資源。 完成後，還原將創建與原始資料庫相同的伺服器上的新資料庫。 還原的資料庫根據其服務層和計算大小按正常費率收費。 在資料庫還原完成之前，不會產生費用。

基於復原目的，您通常會將資料庫還原到較早的時間點。 您可以將還原的資料庫視為原始資料庫的替換，也可以將其用作資料來源來更新原始資料庫。

- **資料庫取代**

  如果希望還原的資料庫作為原始資料庫的替換，則應指定原始資料庫的計算大小和服務層。 然後，您可以重命名原始資料庫，並使用 T-SQL 中的[ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database)命令為還原的資料庫提供原始名稱。

- **資料恢復**

  如果計畫從還原的資料庫檢索資料以從使用者或應用程式錯誤中恢復，則需要編寫並執行資料恢復腳本，該腳本從還原的資料庫中提取資料並應用於原始資料庫。 雖然還原作業可能要花很長的時間才能完成，但還原中的資料庫在整個還原過程中都會顯示在資料庫清單上。 如果在還原期間刪除資料庫，還原操作將被取消，並且不會為未完成還原的資料庫向您收費。
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>使用 Azure 門戶進行時間點還原

可以從要在 Azure 門戶中還原的資料庫的概述邊欄選項卡將單個 SQL 資料庫或實例資料庫恢復到某個時間點。

#### <a name="single-azure-sql-database"></a>單個 Azure SQL 資料庫

要使用 Azure 門戶將單個或池資料庫恢復到時間點，請打開資料庫概覽頁，然後選擇工具列上的 **"還原**"。 選擇備份源，然後選擇從中創建新資料庫的時間點備份點。 

  ![資料庫還原選項的螢幕截圖](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>託管實例資料庫

要使用 Azure 門戶將託管實例資料庫恢復到某個時間點，請打開資料庫概覽頁，然後選擇工具列上的 **"還原**"。 選擇從中創建新資料庫的時間點備份點。 

  ![資料庫還原選項的螢幕截圖](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> 要從備份以程式設計方式還原資料庫，請參閱[使用自動備份以程式設計方式執行恢復](sql-database-recovery-using-backups.md)。

## <a name="deleted-database-restore"></a>還原已刪除的資料庫

您可以將已刪除的資料庫還原到同一 SQL 資料庫伺服器或同一託管實例上的刪除時間或較早的時間點。 您可以通過 Azure 門戶[、PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)或[REST（建立模式+還原）](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)來實現此目的。 通過從備份創建新資料庫來還原已刪除的資料庫。

> [!IMPORTANT]
> 如果刪除 Azure SQL 資料庫伺服器或託管實例，則其所有資料庫也將被刪除，並且無法恢復。 無法還原已刪除的伺服器或託管實例。

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>使用 Azure 門戶刪除的資料庫還原

從伺服器和實例資源從 Azure 門戶還原已刪除的資料庫。

#### <a name="single-azure-sql-database"></a>單個 Azure SQL 資料庫

要使用 Azure 門戶將單個或池化刪除資料庫恢復到刪除時間，請打開伺服器概覽頁，然後選擇 **"已刪除的資料庫**"。 選擇要還原的已刪除資料庫，然後鍵入將使用從備份還原的資料創建的新資料庫的名稱。

  ![已還原已刪除的 Azure SQL 資料庫的螢幕截圖](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>託管實例資料庫

要使用 Azure 門戶恢復託管資料庫，請打開託管實例概覽頁，然後選擇 **"已刪除的資料庫**"。 選擇要還原的已刪除資料庫，然後鍵入將使用從備份還原的資料創建的新資料庫的名稱。

  ![已還原已刪除的 Azure SQL 實例資料庫的螢幕截圖](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>使用 PowerShell 刪除的資料庫還原

使用以下示例腳本使用 PowerShell 還原 Azure SQL 資料庫和託管實例的已刪除資料庫。

#### <a name="single-azure-sql-database"></a>單個 Azure SQL 資料庫

有關演示如何還原已刪除的 Azure SQL 資料庫的示例 PowerShell 腳本，請參閱[使用 PowerShell 還原 SQL 資料庫](scripts/sql-database-restore-database-powershell.md)。

#### <a name="managed-instance-database"></a>託管實例資料庫

有關演示如何還原已刪除實例資料庫的示例 PowerShell 腳本，請參閱使用[PowerShell 在託管實例上還原已刪除的資料庫](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> 要以程式設計方式還原已刪除的資料庫，請參閱[使用自動備份以程式設計方式執行恢復](sql-database-recovery-using-backups.md)。

## <a name="geo-restore"></a>異地還原

您可以從最新的異地複寫備份，在任何 Azure 區域中的任何伺服器上還原 SQL Database。 地理還原使用異地複本備份作為其源。 即使資料庫或資料中心由於中斷而無法訪問，您也可以請求異地還原。

當資料庫由於託管區域中的事件而無法接通，地理還原是預設恢復選項。 您可以將資料庫還原至任何其他區域中的伺服器。 在建立備份時以及對它進行異地複寫到不同區域中的 Azure Blob 之間會有延遲。 因此，還原的資料庫最多比原始資料庫晚一小時。 下圖顯示了從另一個區域中的最後一個可用備份還原的資料庫。

![地理恢復圖形](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>使用 Azure 門戶進行地理還原

從 Azure 門戶創建新的單個或託管實例資料庫，並選擇可用的地理還原備份。 新創建的資料庫包含異地還原的備份資料。

#### <a name="single-azure-sql-database"></a>單個 Azure SQL 資料庫

要從您選擇的區域和伺服器中的 Azure 門戶進行地理還原單個 SQL 資料庫，請按照以下步驟操作：

1. 從**儀表板**中，選擇 **"添加** > **創建 SQL 資料庫**"。 在"**基礎知識"** 選項卡上，輸入所需的資訊。
2. 選取 [其他設定]****。
3. 要**使用現有資料**，請選擇 **"備份**"。
4. 對於**備份**，從可用異地還原備份清單中選擇備份。

    ![創建 SQL 資料庫選項的螢幕截圖](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

完成從備份創建新資料庫的過程。 創建單個 Azure SQL 資料庫時，它包含還原的異地還原備份。

#### <a name="managed-instance-database"></a>託管實例資料庫

要將託管實例資料庫從 Azure 門戶地理還原到您選擇的區域中的現有託管實例，請選擇要還原資料庫的託管實例。 請遵循下列步驟：

1. 選擇 **"新建資料庫**"。
2. 鍵入所需的資料庫名稱。
3. 在 **"使用現有資料**"下，選擇 **"備份**"。
4. 從可用異地還原備份清單中選擇備份。

    ![新資料庫選項的螢幕截圖](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

完成創建新資料庫的過程。 創建實例資料庫時，它包含還原的異地還原備份。

### <a name="geo-restore-by-using-powershell"></a>使用 PowerShell 進行地理恢復

#### <a name="single-azure-sql-database"></a>單個 Azure SQL 資料庫

對於演示如何對單個 SQL 資料庫執行地理還原的 PowerShell 腳本，請參閱[使用 PowerShell 將 Azure SQL 單個資料庫還原到較早的時間點](scripts/sql-database-restore-database-powershell.md)。

#### <a name="managed-instance-database"></a>託管實例資料庫

對於演示如何對託管實例資料庫執行地理還原的 PowerShell 腳本，請參閱[使用 PowerShell 將託管實例資料庫還原到另一個地理區域](scripts/sql-managed-instance-restore-geo-backup.md)。

### <a name="geo-restore-considerations"></a>地理還原注意事項

不能在地理次要資料庫上執行時間點還原。 只能在主資料庫上執行此操作。 有關使用異地還原從中斷中恢復的詳細資訊，請參閱[從中斷中恢復](sql-database-disaster-recovery.md)。

> [!IMPORTANT]
> 地理恢復是 SQL 資料庫中提供的最基本的災害復原解決方案。 它依賴于自動創建的異地複本備份，復原點目標 （RPO） 等於 1 小時，估計恢復時間長達 12 小時。 它不能保證目的地區域在區域中斷後能夠還原資料庫，因為需求可能會急劇增加。 如果應用程式使用相對較小的資料庫，並且對業務不重要，則異地還原是適當的災害復原解決方案。 對於需要大型資料庫且必須確保業務連續性的業務關鍵型應用程式，請使用[自動容錯移轉組](sql-database-auto-failover-group.md)。 它提供了更低的 RPO 和恢復時間目標，並且始終保證容量。 如需商務持續性選項的詳細資訊，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>使用自動備份以程式設計方式執行恢復

您還可以使用 Azure PowerShell 或 REST API 進行恢復。 下表描述可用的命令集。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組，但所有後續開發都針對 Az.Sql 模組。 有關這些 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數在很大程度上是相同的。

#### <a name="single-azure-sql-database"></a>單個 Azure SQL 資料庫

要還原獨立資料庫或池資料庫，請參閱[還原-AzSql資料庫](/powershell/module/az.sql/restore-azsqldatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |取得一或多個資料庫。 |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 取得可還原的已刪除資料庫。 |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |取得資料庫的異地備援備份。 |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |還原 SQL Database。 |

  > [!TIP]
  > 有關演示如何執行資料庫的時間點還原的示例 PowerShell 腳本，請參閱[使用 PowerShell 還原 SQL 資料庫](scripts/sql-database-restore-database-powershell.md)。

#### <a name="managed-instance-database"></a>託管實例資料庫

要還原託管實例資料庫，請參閱[還原-AzSqlInstance 資料庫](/powershell/module/az.sql/restore-azsqlinstancedatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |獲取一個或多個託管實例。 |
  | [獲取-AzSql實例資料庫](/powershell/module/az.sql/get-azsqlinstancedatabase) | 獲取實例資料庫。 |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |還原實例資料庫。 |

### <a name="rest-api"></a>REST API

要使用 REST API 還原單個或池資料庫，請：

| API | 描述 |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |還原資料庫。 |
| [取得建立或更新資料庫狀態](https://docs.microsoft.com/rest/api/sql/operations) |在還原操作期間返回狀態。 |

### <a name="azure-cli"></a>Azure CLI

#### <a name="single-azure-sql-database"></a>單個 Azure SQL 資料庫

要使用 Azure CLI 還原單個或池資料庫，請參閱[az sql db 還原](/cli/azure/sql/db#az-sql-db-restore)。

#### <a name="managed-instance-database"></a>託管實例資料庫

要使用 Azure CLI 還原託管實例資料庫，請參閱[az sql midb 還原](/cli/azure/sql/midb#az-sql-midb-restore)。

## <a name="summary"></a>總結

自動備份可在發生使用者和應用程式錯誤、意外刪除資料庫和長時間中斷時保護您的資料庫。 所有服務層級和計算大小都可以取得此內建功能。

## <a name="next-steps"></a>後續步驟

- [商務持續性概觀](sql-database-business-continuity.md)
- [SQL Database 自動備份](sql-database-automated-backups.md)
- [長期保留](sql-database-long-term-retention.md)
- 若要了解更快速的復原選項，請參閱[主動式異地複寫](sql-database-active-geo-replication.md)或[自動容錯移轉群組](sql-database-auto-failover-group.md)。
