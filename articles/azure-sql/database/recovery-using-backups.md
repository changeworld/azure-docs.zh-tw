---
title: 從備份還原資料庫
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 瞭解時間點還原，這可讓您在 Azure SQL Database 或 Azure SQL 受控執行個體中的實例中復原資料庫，最多35天。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: e12d5d7e9cfc6cfa80de1032e3d4d5659c44c0a7
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86075875"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>使用自動資料庫備份進行復原-Azure SQL Database & SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

根據預設，Azure SQL Database 和 Azure SQL 受控執行個體備份會儲存在異地複寫的 blob 儲存體（GRS 儲存體類型）中。 下列選項適用于使用[自動資料庫備份](automated-backups-overview.md)進行資料庫復原。 您可以：

- 在同一部伺服器上建立新的資料庫，並復原到保留期限內的指定時間點。
- 在同一部伺服器上建立資料庫，並復原到已刪除資料庫的刪除時間。
- 在相同區域中的任何伺服器上建立新的資料庫，並復原到最新的備份點。
- 在任何其他區域中的任何伺服器上建立新的資料庫，並復原到最新複寫的備份點。

如果您已設定[備份長期保留](long-term-retention-overview.md)，您也可以從任何伺服器上的任何長期保留備份來建立新的資料庫。

> [!IMPORTANT]
> 在還原期間，您無法覆寫現有的資料庫。

當您使用 Standard 或 Premium 服務層級時，您的資料庫還原可能會產生額外的儲存成本。 當還原資料庫的大小上限大於目標資料庫的服務層級和效能等級所包含的儲存量時，就會產生額外的成本。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的已使用空間量小於內含的儲存體數量，您可以將資料庫大小上限設定為包含的數量，以避免此額外成本。

## <a name="recovery-time"></a>復原時間

使用自動資料庫備份來還原資料庫的復原時間會受到幾個因素的影響：

- 資料庫的大小。
- 資料庫的計算大小。
- 涉及的交易記錄數目。
- 需要重新執行才能復原到還原點的活動量。
- 還原至不同區域時的網路頻寬。
- 要在目標區域中處理的並行還原要求數目。

對於大型或非常活躍的資料庫，還原可能需要數小時的時間。 如果區域發生長時間中斷，可能會起始大量的異地還原要求以進行嚴重損壞修復。 當有多個要求時，個別資料庫的復原時間也會增加。 大部分的資料庫還原會在12小時內完成。

對於單一訂用帳戶，並行還原要求數目有一些限制。 這些限制適用于時間點還原、異地還原和從長期保留備份還原的任何組合。

|| **正在處理的並行要求的最大數目** | **正在提交的並行要求的最大數目** |
| :--- | --: | --: |
|**單一資料庫 (每個訂閱)**|10|60|
|**彈性集區 (每個集區)**|4|200|


沒有內建方法可還原整部伺服器。 如需如何完成這項工作的範例，請參閱[Azure SQL Database：完整伺服器](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)復原。

> [!IMPORTANT]
> 若要使用自動備份進行復原，您必須是訂用帳戶中「SQL Server 參與者」角色或「SQL 受控執行個體參與者」角色的成員（視復原目的地而定），或者您必須是訂用帳戶擁有者。 如需詳細資訊，請參閱[RBAC：內建角色](../../role-based-access-control/built-in-roles.md)。 您可以使用 Azure 入口網站、PowerShell 或 REST API 來進行復原。 您無法使用 Transact-sql。

## <a name="point-in-time-restore"></a>時間點還原

您可以使用 Azure 入口網站、 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.)，將獨立、集區或實例資料庫還原到較早的時間點。 要求可以針對還原的資料庫指定任何服務層級或計算大小。 確定您要還原資料庫的伺服器上有足夠的資源。 

完成時，還原會在與原始資料庫相同的伺服器上建立新的資料庫。 還原的資料庫會根據其服務層級和計算大小，以標準費率計費。 在資料庫還原完成之前，您不會產生任何費用。

基於復原目的，您通常會將資料庫還原到較早的時間點。 您可以將還原的資料庫視為原始資料庫的取代，或使用它做為資料來源來更新原始資料庫。

- **資料庫取代**

  如果您想要將還原的資料庫取代為原始資料庫，您應該指定原始資料庫的計算大小和服務層級。 接著，您可以使用 T-sql 中的[ALTER database](/sql/t-sql/statements/alter-database-azure-sql-database)命令，將原始資料庫重新命名，並提供原始名稱給還原的資料庫。

- **資料復原**

  如果您打算從還原的資料庫抓取資料，以從使用者或應用程式錯誤中復原，您必須撰寫並執行資料復原腳本，以從還原的資料庫中解壓縮資料，並將其套用至原始資料庫。 雖然還原作業可能要花很長的時間才能完成，但還原中的資料庫在整個還原過程中都會顯示在資料庫清單上。 如果您在還原期間刪除資料庫，還原作業將會取消，且不會向您收取未完成還原的資料庫費用。
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>使用 Azure 入口網站的時間點還原

您可以在 [Azure 入口網站] 中，從您想要還原之資料庫的 [總覽] 分頁，將單一或實例資料庫復原至某個時間點。

#### <a name="sql-database"></a>SQL Database

若要使用 Azure 入口網站將資料庫復原到某個時間點，請開啟 [資料庫總覽] 頁面，然後選取工具列上的 [**還原**]。 選擇 [備份來源]，然後選取將建立新資料庫的時間點備份點。

  ![資料庫還原選項的螢幕擷取畫面](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要使用 Azure 入口網站將受控實例資料庫復原到某個時間點，請開啟 [資料庫總覽] 頁面，然後選取工具列上的 [**還原**]。 選擇將建立新資料庫的時間點備份點。

  ![資料庫還原選項的螢幕擷取畫面](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> 若要以程式設計方式從備份還原資料庫，請參閱[使用自動備份以程式設計方式執行](recovery-using-backups.md)復原。

## <a name="deleted-database-restore"></a>還原已刪除的資料庫

您可以將已刪除的資料庫還原到相同伺服器或相同受控實例上的刪除時間或較早的時間點。 您可以透過 Azure 入口網站、 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)或[REST （createMode = Restore）](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)來完成此動作。 您可以從備份建立新的資料庫，以還原已刪除的資料庫。

> [!IMPORTANT]
> 如果您刪除伺服器或受控實例，其所有資料庫也會一併刪除且無法復原。 您無法還原已刪除的伺服器或受控實例。

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>使用 Azure 入口網站刪除的資料庫還原

您會從伺服器或受控實例資源的 Azure 入口網站還原已刪除的資料庫。

#### <a name="sql-database"></a>SQL Database

若要使用 Azure 入口網站將已刪除的資料庫復原到刪除時間，請開啟 [伺服器總覽] 頁面，然後選取 [**已刪除的資料庫**]。 選取您想要還原的已刪除資料庫，然後輸入將使用從備份還原之資料所建立的新資料庫名稱。

  ![還原已刪除資料庫的螢幕擷取畫面](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要使用 Azure 入口網站復原受控資料庫，請開啟 [受控實例總覽] 頁面，然後選取 [**已刪除的資料庫**]。 選取您想要還原的已刪除資料庫，然後輸入將使用從備份還原之資料所建立的新資料庫名稱。

  ![還原已刪除的 Azure SQL 受控執行個體資料庫的螢幕擷取畫面](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>使用 PowerShell 刪除資料庫還原

使用下列範例腳本，使用 PowerShell 還原 SQL Database 或 SQL 受控執行個體的已刪除資料庫。

#### <a name="sql-database"></a>SQL Database

如需示範如何在 Azure SQL Database 中還原已刪除資料庫的 PowerShell 腳本範例，請參閱[使用 PowerShell 還原資料庫](scripts/restore-database-powershell.md)。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

如需示範如何還原已刪除之實例資料庫的 PowerShell 腳本範例，請參閱[使用 PowerShell 還原已刪除的實例資料庫](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> 若要以程式設計方式還原已刪除的資料庫，請參閱[使用自動備份以程式設計方式執行](recovery-using-backups.md)復原

## <a name="geo-restore"></a>異地還原

您可以從最近的異地複寫備份，在任何 Azure 區域中的任何受管理的實例上，還原任何 SQL Database 伺服器或實例資料庫上的資料庫。 異地還原使用異地複寫備份作為其來源。 即使因為中斷而無法存取資料庫或資料中心，也可以要求異地還原。

當您的資料庫因為裝載區域中的事件而無法使用時，異地還原就是預設的復原選項。 您可以將資料庫還原至任何其他區域中的伺服器。 在建立備份時以及對它進行異地複寫到不同區域中的 Azure Blob 之間會有延遲。 因此，還原後的資料庫最多可達一小時的原始資料庫。 下圖顯示從另一個區域中的最後一個可用備份來還原資料庫。

![異地還原的圖形](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>使用 Azure 入口網站進行異地還原

在 Azure 入口網站中，您可以建立新的單一或受控實例資料庫，並選取可用的異地還原備份。 新建立的資料庫包含異地還原的備份資料。

#### <a name="sql-database"></a>SQL Database

若要從您選擇的區域和伺服器中的 Azure 入口網站異地還原單一資料庫，請遵循下列步驟：

1. 從 [**儀表板**] 選取 [**新增**] [  >  **建立 SQL Database**]。 在 [**基本**] 索引標籤上，輸入所需的資訊。
2. 選取 [其他設定]****。
3. 針對 [**使用現有的資料**]，選取 [**備份**]。
4. 針對 [**備份**]，從可用的異地還原備份清單中選取備份。

    ![建立 SQL Database 選項的螢幕擷取畫面](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

完成從備份建立新資料庫的程式。 當您在 Azure SQL Database 中建立資料庫時，它會包含還原的異地還原備份。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要從 Azure 入口網站將受控實例資料庫異地還原至您所選區域中的現有受控實例，請選取您要在其上還原資料庫的受控實例。 請遵循下列步驟：

1. 選取 [**新增資料庫**]。
2. 輸入想要的資料庫名稱。
3. 在 [**使用現有資料**] 底下，選取 [**備份**]。
4. 從可用的異地還原備份清單中選取備份。

    ![新資料庫選項的螢幕擷取畫面](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

完成建立新資料庫的程式。 當您建立實例資料庫時，它會包含還原的異地還原備份。

### <a name="geo-restore-by-using-powershell"></a>使用 PowerShell 進行異地還原

#### <a name="sql-database"></a>SQL Database

如需示範如何針對單一資料庫執行異地還原的 PowerShell 腳本，請參閱[使用 powershell 將單一資料庫還原至較早的時間點](scripts/restore-database-powershell.md)。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

如需示範如何針對受控實例資料庫執行異地還原的 PowerShell 腳本，請參閱[使用 powershell 將受控實例資料庫還原到另一個地理區域](../managed-instance/scripts/restore-geo-backup.md)。

### <a name="geo-restore-considerations"></a>異地還原考慮

您無法在異地次要資料庫上執行還原時間點。 您只可以在主資料庫上這麼做。 如需使用異地還原從中斷復原的詳細資訊，請參閱[從中斷復原](../../key-vault/general/disaster-recovery-guidance.md)。

> [!IMPORTANT]
> 異地還原是 SQL Database 和 SQL 受控執行個體中最基本的嚴重損壞修復解決方案。 它依賴自動建立的異地複寫備份，其復原點目標（RPO）等於1小時，且預估復原時間最多可達12小時。 它不保證在區域中斷之後，目的地區域將會擁有還原資料庫的容量，因為需求可能會明顯增加。 如果您的應用程式使用相對較小的資料庫，而且對企業而言並不重要，則異地還原是適當的嚴重損壞修復解決方案。 
>
> 對於需要大型資料庫且必須確保商務持續性的商務關鍵應用程式，請使用[自動容錯移轉群組](auto-failover-group-overview.md)。 它提供較低的 RPO 和復原時間目標，而且一律保證容量。 
>
> 如需商務持續性選項的詳細資訊，請參閱[商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)。

## <a name="programmatic-recovery-using-automated-backups"></a>使用自動備份進行程式設計復原

您也可以使用 Azure PowerShell 或 REST API 進行復原。 下表描述可用的命令集。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> SQL Database 和 SQL 受控執行個體仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 Azure Resource Manager 模組中命令的引數，相當於相同的範圍。

#### <a name="sql-database"></a>SQL Database

若要還原獨立或集區資料庫，請參閱[restore-set-azsqldatabase 搭配](/powershell/module/az.sql/restore-azsqldatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |取得一或多個資料庫。 |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 取得可還原的已刪除資料庫。 |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |取得資料庫的異地備援備份。 |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |還原資料庫。 |

  > [!TIP]
  > 如需示範如何執行資料庫還原時間點的範例 PowerShell 腳本，請參閱[使用 PowerShell 還原資料庫](scripts/restore-database-powershell.md)。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要還原受控實例資料庫，請參閱[AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |取得一或多個受控實例。 |
  | [AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | 取得實例資料庫。 |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |還原實例資料庫。 |

### <a name="rest-api"></a>REST API

若要使用 REST API 來還原資料庫：

| API | 說明 |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |還原資料庫。 |
| [取得建立或更新資料庫狀態](https://docs.microsoft.com/rest/api/sql/operations) |傳回還原作業期間的狀態。 |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

若要使用 Azure CLI 來還原資料庫，請參閱[az sql db restore](/cli/azure/sql/db#az-sql-db-restore)。

#### <a name="sql-managed-instance"></a>SQL 受控執行個體

若要使用 Azure CLI 來還原受控實例資料庫，請參閱[az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)。

## <a name="summary"></a>摘要

自動備份可在發生使用者和應用程式錯誤、意外刪除資料庫和長時間中斷時保護您的資料庫。 所有服務層級和計算大小都可以取得此內建功能。

## <a name="next-steps"></a>後續步驟

- [商務持續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database 自動備份](automated-backups-overview.md)
- [長期保留](long-term-retention-overview.md)
- 若要了解更快速的復原選項，請參閱[主動式異地複寫](active-geo-replication-overview.md)或[自動容錯移轉群組](auto-failover-group-overview.md)。
