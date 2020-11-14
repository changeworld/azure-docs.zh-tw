---
title: 從備份還原資料庫
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 瞭解時間點還原，這可讓您在 Azure SQL 中的 Azure SQL Database 或實例中復原資料庫，受控執行個體最多35天。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 415c9fdcbf0e8bfecaa48b8199702d4159bc32d9
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629184"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>使用自動資料庫備份復原-Azure SQL Database & SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

下列選項可供使用 [自動資料庫備份](automated-backups-overview.md)來進行資料庫復原。 您可以：

- 在同一部伺服器上建立新的資料庫，並復原到保留期限內的指定時間點。
- 在同一部伺服器上建立資料庫，並復原到已刪除資料庫的刪除時間。
- 在相同區域中的任何伺服器上建立新的資料庫，並復原到最新的備份點。
- 在任何其他區域中的任何伺服器上建立新的資料庫，並復原到最新複寫的備份點。

如果您已設定 [備份長期保留](long-term-retention-overview.md)，也可以從任何伺服器上的任何長期保留備份建立新的資料庫。

> [!IMPORTANT]
> 還原期間無法覆寫現有的資料庫。

當您使用標準或高階服務層級時，您的資料庫還原可能會產生額外的儲存體成本。 當還原的資料庫大小上限大於目標資料庫的服務層級和效能層級所包含的儲存空間時，就會產生額外的成本。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際使用的空間量小於所含的儲存空間量，您可以將資料庫大小上限設定為包含的數量，以避免此額外成本。

## <a name="recovery-time"></a>復原時間

使用自動資料庫備份還原資料庫的復原時間會受到幾個因素影響：

- 資料庫的大小。
- 資料庫的計算大小。
- 涉及的交易記錄檔數目。
- 需要重新執行才能復原至還原點的活動量。
- 如果還原至不同的區域，則會使用網路頻寬。
- 要在目標區域中處理的並行還原要求數目。

如果是大型或非常活躍的資料庫，還原可能需要數小時的時間。 如果區域中的停機時間過長，可能會起始大量的異地還原要求，以進行嚴重損壞修復。 當有許多要求時，個別資料庫的復原時間可能會增加。 大部分的資料庫還原會在12小時內完成。

單一訂用帳戶的並行還原要求數目有一些限制。 這些限制適用于任何時間點還原、異地還原和長期保留備份的還原組合。

| **部署選項** | **正在處理的並行要求的最大數目** | **正在提交的並行要求的最大數目** |
| :--- | --: | --: |
|**單一資料庫 (每個訂閱)**|10|60|
|**彈性集區 (每個集區)**|4|200|


沒有內建方法可還原整部伺服器。 如需如何完成這項工作的範例，請參閱 [Azure SQL Database：完整伺服器](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)復原。

> [!IMPORTANT]
> 若要使用自動備份進行復原，您必須是 SQL Server 參與者角色或 SQL 受控執行個體參與者角色的成員 (視訂用帳戶中的復原目的地) 而定，或者您必須是訂用帳戶擁有者。 如需詳細資訊，請參閱[RBAC：內建角色](../../role-based-access-control/built-in-roles.md)。 您可以使用 Azure 入口網站、PowerShell 或 REST API 來復原。 您無法使用 Transact-sql。

## <a name="point-in-time-restore"></a>時間點還原

您可以使用 Azure 入口網站、 [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)或 [REST API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.)，將獨立、集區或實例資料庫還原至較早的時間點。 要求可以指定還原資料庫的任何服務層級或計算大小。 確定您在要還原資料庫的伺服器上有足夠的資源。 

完成時，還原會在與原始資料庫相同的伺服器上建立新的資料庫。 還原的資料庫會根據其服務層級和計算大小，以一般費率計費。 在資料庫還原完成之前，您不會產生費用。

基於復原目的，您通常會將資料庫還原到較早的時間點。 您可以將還原的資料庫視為原始資料庫的取代，或使用它作為資料來源來更新原始資料庫。

- **資料庫取代**

  如果您想要讓還原的資料庫成為原始資料庫的取代，您應該指定原始資料庫的計算大小和服務層級。 然後，您可以重新命名原始資料庫，並使用 T-sql 中的 [ALTER database](/sql/t-sql/statements/alter-database-azure-sql-database) 命令，將原始名稱提供給還原的資料庫。

- **資料復原**

  如果您打算從還原的資料庫取出資料，以便從使用者或應用程式錯誤中復原，您必須撰寫並執行資料修復腳本，以從還原的資料庫中解壓縮資料，並套用至原始資料庫。 雖然還原作業可能要花很長的時間才能完成，但還原中的資料庫在整個還原過程中都會顯示在資料庫清單上。 如果您在還原期間刪除該資料庫，還原作業將會取消，而不會針對未完成還原的資料庫向您收費。
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>使用 Azure 入口網站的時間點還原

您可以從您想要在 Azure 入口網站中還原之資料庫的總覽分頁，將單一或實例資料庫復原至某個時間點。

#### <a name="sql-database"></a>SQL Database

若要使用 Azure 入口網站將資料庫復原到某個時間點，請開啟 [資料庫總覽] 頁面，然後選取工具列上的 [ **還原** ]。 選擇備份來源，然後選取將建立新資料庫的時間點備份點。

  ![SQL Database 的資料庫還原選項螢幕擷取畫面。](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要使用 Azure 入口網站將受控實例資料庫復原至某個時間點，請開啟 [資料庫總覽] 頁面，然後選取工具列上的 [ **還原** ]。 選擇將建立新資料庫的時間點備份點。

  ![SQL 受控實例的資料庫還原選項螢幕擷取畫面。](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> 若要以程式設計方式從備份還原資料庫，請參閱 [使用自動備份以程式設計方式執行](recovery-using-backups.md)復原。

## <a name="deleted-database-restore"></a>還原已刪除的資料庫

您可以將已刪除的資料庫還原到相同伺服器或相同受控實例上的刪除時間或較早的時間點。 您可以透過 Azure 入口網站、 [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)或 [REST (createMode = Restore) ](/rest/api/sql/databases/createorupdate)來完成此動作。 您可以從備份建立新的資料庫來還原已刪除的資料庫。

> [!IMPORTANT]
> 如果您刪除伺服器或受控實例，其所有資料庫也會一併刪除且無法復原。 您無法還原已刪除的伺服器或受控實例。

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>使用 Azure 入口網站刪除的資料庫還原

您可以從伺服器或受控實例資源的 Azure 入口網站還原已刪除的資料庫。

> [!TIP]
> 最近刪除的資料庫可能需要幾分鐘的時間才會出現在 Azure 入口網站的 [ **已刪除的資料庫** ] 頁面上，或以程式設計 [方式](#programmatic-recovery-using-automated-backups)顯示已刪除的資料庫時。

#### <a name="sql-database"></a>SQL Database

若要使用 Azure 入口網站將已刪除的資料庫復原到刪除時間，請開啟 [伺服器總覽] 頁面，然後選取 [ **已刪除的資料庫** ]。 選取您要還原的已刪除資料庫，然後輸入新資料庫的名稱，該資料庫將會使用從備份還原的資料來建立。

  ![還原已刪除資料庫的螢幕擷取畫面](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要使用 Azure 入口網站來復原受管理的資料庫，請開啟 [受管理的實例] 總覽頁面，然後選取 [ **已刪除的資料庫** ]。 選取您要還原的已刪除資料庫，然後輸入新資料庫的名稱，該資料庫將會使用從備份還原的資料來建立。

  ![還原已刪除的 Azure SQL 受控執行個體資料庫的螢幕擷取畫面](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>使用 PowerShell 刪除的資料庫還原

您可以使用下列範例腳本，使用 PowerShell 來還原 SQL Database 或 SQL 受控執行個體的已刪除資料庫。

#### <a name="sql-database"></a>SQL Database

如需示範如何在 Azure SQL Database 中還原已刪除之資料庫的 PowerShell 腳本範例，請參閱 [使用 PowerShell 來還原資料庫](scripts/restore-database-powershell.md)。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

如需示範如何還原已刪除實例資料庫的 PowerShell 腳本範例，請參閱[使用 PowerShell 還原已刪除的實例資料庫](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)。

> [!TIP]
> 若要以程式設計方式還原已刪除的資料庫，請參閱 [使用自動備份以程式設計方式執行](recovery-using-backups.md)復原

## <a name="geo-restore"></a>異地復原

> [!IMPORTANT]
> 異地還原僅適用于使用地理位置冗余 [備份儲存體](automated-backups-overview.md#backup-storage-redundancy)設定的 SQL 資料庫或受控實例。

您可以從最新的異地複寫備份，在任何 Azure 區域中的任何受控實例上，還原任何 SQL Database 伺服器或實例資料庫上的資料庫。 異地還原使用異地複寫的備份作為其來源。 即使因為中斷而無法存取資料庫或資料中心，也可以要求異地還原。

當您的資料庫因為裝載區域中的事件而無法使用時，異地還原就是預設的復原選項。 您可以將資料庫還原至任何其他區域中的伺服器。 在建立備份時以及對它進行異地複寫到不同區域中的 Azure Blob 之間會有延遲。 如此一來，還原的資料庫最多可能會在原始資料庫後的一小時內完成。 下圖顯示從另一個區域中的最後一個可用備份進行資料庫還原。

![異地還原圖形](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>使用 Azure 入口網站的異地還原

在 Azure 入口網站中，您可以建立新的單一或受控實例資料庫，並選取可用的異地還原備份。 新建立的資料庫包含異地還原的備份資料。

#### <a name="sql-database"></a>SQL Database

若要從您選擇的區域和伺服器中的 Azure 入口網站異地還原單一資料庫，請遵循下列步驟：

1. 從 **儀表板** 選取 [ **新增**  >  **建立 SQL Database** ]。 在 [ **基本** ] 索引標籤上，輸入必要的資訊。
2. 選取 [其他設定]。
3. 若要 **使用現有的資料** ，請選取 [ **備份** ]。
4. 針對 [ **備份** ]，請從可用的異地還原備份清單中選取備份。

    ![建立 SQL Database 選項的螢幕擷取畫面](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

完成從備份建立新資料庫的流程。 當您在 Azure SQL Database 中建立資料庫時，它會包含還原的異地還原備份。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要從 Azure 入口網站將受控實例資料庫異地還原至您選擇的區域中現有的受控實例，請選取您想要還原資料庫的受控實例。 請遵循這些步驟：

1. 選取 [ **新增資料庫** ]。
2. 輸入所需的資料庫名稱。
3. 在 [ **使用現有資料** ] 下，選取 [ **備份** ]。
4. 從可用的異地還原備份清單中選取備份。

    ![新資料庫選項的螢幕擷取畫面](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

完成建立新資料庫的流程。 當您建立實例資料庫時，它會包含還原的異地還原備份。

### <a name="geo-restore-by-using-powershell"></a>使用 PowerShell 進行異地還原

#### <a name="sql-database"></a>SQL Database

如需顯示如何針對單一資料庫執行異地還原的 PowerShell 腳本，請參閱 [使用 PowerShell 將單一資料庫還原至較早的時間點](scripts/restore-database-powershell.md)。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

如需說明如何針對受控實例資料庫執行異地還原的 PowerShell 腳本，請參閱 [使用 PowerShell 將受控實例資料庫還原至另一個地理區域](../managed-instance/scripts/restore-geo-backup.md)。

### <a name="geo-restore-considerations"></a>異地還原考慮

您無法在異地次要資料庫上執行還原時間點。 您只能在主資料庫上這麼做。 如需使用異地還原從中斷復原的詳細資訊，請參閱 [從中斷復原](../../key-vault/general/disaster-recovery-guidance.md)。

> [!IMPORTANT]
> 異地還原是 SQL Database 和 SQL 受控執行個體中最基本的災難復原解決方案。 它依賴自動建立的異地複寫備份，其復原點目標 (RPO) 最多1小時，而估計的復原時間最多可達12小時。 這並不保證目的地區域在區域性中斷之後可還原您的資料庫，因為可能會有明顯增加的需求。 如果您的應用程式使用相對較小的資料庫，而且對企業而言並不重要，則異地還原就是適當的災難復原解決方案。 
>
> 對於需要大型資料庫且必須確保商務持續性的商務關鍵應用程式，請使用 [自動容錯移轉群組](auto-failover-group-overview.md)。 它提供了更低的 RPO 和復原時間目標，並一律保證容量。 
>
> 如需商務持續性選項的詳細資訊，請參閱 [商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)。

## <a name="programmatic-recovery-using-automated-backups"></a>使用自動備份進行程式設計復原

您也可以使用 Azure PowerShell 或 REST API 來進行復原。 下表描述可用的命令集。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> SQL Database 和 SQL 受控執行個體仍支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az. Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 Azure Resource Manager 模組中之命令的引數，是相當大的範圍。

#### <a name="sql-database"></a>SQL Database

若要還原獨立或集區資料庫，請參閱 [restore->set-azsqldatabase](/powershell/module/az.sql/restore-azsqldatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |取得一或多個資料庫。 |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 取得可還原的已刪除資料庫。 |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |取得資料庫的異地備援備份。 |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |還原資料庫。 |

  > [!TIP]
  > 如需示範如何執行資料庫之時間點還原的 PowerShell 腳本範例，請參閱 [使用 PowerShell 來還原資料庫](scripts/restore-database-powershell.md)。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要還原受控實例資料庫，請參閱 [restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |取得一或多個受控實例。 |
  | [AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | 取得實例資料庫。 |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |還原實例資料庫。 |

### <a name="rest-api"></a>REST API

若要使用 REST API 來還原資料庫：

| API | 描述 |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |還原資料庫。 |
| [取得建立或更新資料庫狀態](/rest/api/sql/operations) |在還原作業期間傳回狀態。 |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

若要使用 Azure CLI 來還原資料庫，請參閱 [az sql db restore](/cli/azure/sql/db#az-sql-db-restore)。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要使用 Azure CLI 來還原受控實例資料庫，請參閱 [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)。

## <a name="summary"></a>摘要

自動備份可在發生使用者和應用程式錯誤、意外刪除資料庫和長時間中斷時保護您的資料庫。 所有服務層級和計算大小都可以取得此內建功能。

## <a name="next-steps"></a>後續步驟

- [商務持續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database 自動備份](automated-backups-overview.md)
- [長期保留](long-term-retention-overview.md)
- 若要了解更快速的復原選項，請參閱[主動式異地複寫](active-geo-replication-overview.md)或[自動容錯移轉群組](auto-failover-group-overview.md)。