---
title: 調整單一資料庫資源
description: 本文說明如何在 Azure SQL Database 中調整單一資料庫可用的計算和儲存資源。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/16/2020
ms.openlocfilehash: d24143a440c0d30c3abcd6eb5efd454033b8f71c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791472"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>在 Azure SQL Database 中調整單一資料庫資源

本文說明如何在布建的計算層級調整可供 Azure SQL Database 的計算和儲存體資源。 或者， [無伺服器計算層](serverless-tier-overview.md) 會針對使用的計算，提供每秒的計算自動調整和帳單。

一開始挑選虛擬核心或 Dtu 數目之後，您可以根據實際經驗，使用 [Azure 入口網站](single-database-manage.md#the-azure-portal)、 [transact-sql](/sql/t-sql/statements/alter-database-transact-sql#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或 [REST API](/rest/api/sql/databases/update)，動態地相應增加或相應減少單一資料庫。

下列影片示範如何動態變更服務層級與計算大小，以提高單一資料庫的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱 [管理 Azure SQL Database 中](file-space-manage.md)的檔案空間。

## <a name="impact"></a>影響

變更服務層級或計算大小主要牽涉到服務執行下列步驟：

1. 為資料庫建立新的計算實例。 

    使用要求的服務層級和計算大小建立新的計算實例。 針對某些服務層級和計算大小變更的組合，必須在新的計算實例中建立資料庫的複本，其中牽涉到複製資料，而且可能會對整體延遲造成強烈影響。 無論是哪種情況，資料庫會在此步驟期間維持線上狀態，並繼續將連接導向至原始計算實例中的資料庫。

2. 將連接的路由切換至新的計算實例。

    原始計算實例中資料庫的現有連接會遭到卸載。 系統會在新的計算實例中建立任何新的連接至資料庫。 針對某些服務層級和計算大小變更的組合，系統會在切換期間卸離和重新附加資料庫檔案。  無論如何，在資料庫無法使用的情況下，通常只需要幾秒鐘的時間，切換就會導致短暫的服務中斷。 如果有長時間執行的交易在中斷連接時執行，則此步驟可能需要較長的時間才能復原中止的交易。 [加速資料庫](../accelerated-database-recovery.md) 復原可以降低中止長時間執行之交易的影響。

> [!IMPORTANT]
> 工作流程中的任何步驟都不會遺失任何資料。 當服務層級變更時，請確定您已在使用 Azure SQL Database 的應用程式和元件中執行了一些 [重試邏輯](troubleshoot-common-connectivity-issues.md) 。

## <a name="latency"></a>Latency

變更服務層級的估計延遲、調整單一資料庫或彈性集區的計算大小、將資料庫移入/移出彈性集區，或在彈性集區之間移動資料庫的參數化方式如下：

|服務層級|基本單一資料庫，</br>標準 (S0-S1) |基本彈性集區，</br>標準 (S2-S12) 、 </br>一般用途單一資料庫或彈性集區|Premium 或 Business Critical 單一資料庫或彈性集區|超大規模資料庫
|:---|:---|:---|:---|:---|
|**基本單一資料庫， </br> 標準 (S0-S1)**|&bull;&nbsp;固定時間延遲，與使用的空間無關</br>&bull;&nbsp;通常不到5分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|
|**基本彈性集區、 </br> 標準 (S2-S12) 、 </br> 一般用途單一資料庫或彈性集區**|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;針對單一資料庫，固定時間延遲與使用的空間無關</br>&bull;&nbsp;通常不到5分鐘的單一資料庫</br>&bull;&nbsp;針對彈性集區，與資料庫數目成正比|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|
|**Premium 或 Business Critical 單一資料庫或彈性集區**|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|
|**超大規模資料庫**|N/A|N/A|N/A|&bull;&nbsp;固定時間延遲，與使用的空間無關</br>&bull;&nbsp;通常不到2分鐘|

> [!NOTE]
> 此外，如果是標準 (S2 S12) 和一般用途的資料庫，在彈性集區中移入/移出，或在彈性集區之間移動資料庫的延遲會與資料庫大小成正比，如果資料庫使用 Premium 檔案共用 ([PFS](../../storage/files/storage-files-introduction.md)) 儲存體。
>
> 若要判斷資料庫是否使用 PFS 儲存體，請在資料庫的內容中執行下列查詢。 如果 AccountType 資料行中的值為 `PremiumFileStorage` 或 `PremiumFileStorage-ZRS` ，則資料庫會使用 PFS 儲存體。
 
```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> 若要監視進行中的作業，請參閱：使用 [SQL REST API 管理作業](/rest/api/sql/operations/list)、 [使用 CLI 管理作業](/cli/azure/sql/db/op)、 [使用 t-sql 監視作業](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) ，以及這兩個 PowerShell 命令： [>get-azsqldatabaseactivity](/powershell/module/az.sql/get-azsqldatabaseactivity) 和 [>get-azsqldatabaseactivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

## <a name="cancelling-changes"></a>取消變更

服務層級變更或計算重新調整作業可以取消。

### <a name="the-azure-portal"></a>Azure 入口網站

在 [資料庫總覽] 分頁中，流覽至 [ **通知** ]，然後按一下表示有進行中作業的磚：

![進行中的操作](./media/single-database-scale/ongoing-operations.png)

接著，按一下標示為 [ **取消此操作** ] 的按鈕。

![取消進行中的操作](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

在 PowerShell 命令提示字元中，設定 `$resourceGroupName` 、 `$serverName` 和 `$databaseName` ，然後執行下列命令：

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>其他考量

- 如果您要升級至較高的服務層級或計算大小，除非您明確指定較大的大小 (maxsize) ，否則資料庫大小上限不會增加。
- 若要將資料庫降級，資料庫已用的空間必須小於目標服務層級和計算大小允許的大小上限。
- 從 **進階** 降級至 **標準** 層時，如果發生下列情況，將會產生額外的儲存體成本：(1) 以目標計算大小支援資料庫的大小上限，而且 (2) 大小上限超過目標計算大小的內含儲存體數量。 例如，如果大小上限為 500 GB 的 P1 資料庫縮減至 S3，則會套用額外的儲存體成本，因為 S3 支援的大小上限為 1 TB，且其內含儲存體數量只有 250 GB。 因此，額外的儲存體數量為 500 GB – 250 GB = 250 GB。 如需額外儲存體的價格，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。
- 升級資料庫時，若已啟用[異地複寫](active-geo-replication-configure-portal.md)，您必須先將其次要資料庫升級為所需的服務層級與計算大小，然後再升級主要資料庫 (最佳效能的一般指引)。 升級至不同的版本時，需要先升級次要資料庫。
- 當您在啟用[異地複寫](active-geo-replication-configure-portal.md)的情況下將資料庫降級時，必須先將其主要資料庫降級為所需的服務層級與計算大小，然後再將次要資料庫降級 (最佳效能的一般指引)。 降級至不同的版本時，必須先降級主資料庫。
- 還原服務會針對各種服務層級提供不同的供應項目。 如果您要降級至 **基本** 層，則會有較低的備份保留期限。 請參閱 [Azure SQL Database 備份](automated-backups-overview.md)。
- 在變更完成之前，不會套用資料庫的新屬性。

## <a name="billing"></a>計費

您需支付使用最高服務層級的資料庫存在的每個小時，以及該小時內所套用的計算大小，不論使用方式或資料庫是否使用不到一小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。

## <a name="change-storage-size"></a>變更儲存體大小

### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

- 儲存體最多可以使用 1 GB 的增量布建至資料儲存體大小上限。 可設定的最小資料儲存體為 1 GB。 針對每個服務目標中的資料儲存體大小上限，請參閱 [單一資料庫](resource-limits-vcore-single-databases.md) 和 [彈性](resource-limits-vcore-elastic-pools.md) 集區的資源限制檔頁面。
- 您可以使用 [Azure 入口網站](https://portal.azure.com)、 [transact-sql](/sql/t-sql/statements/alter-database-transact-sql#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或 [REST API](/rest/api/sql/databases/update)來增加或減少其大小上限，以布建單一資料庫的資料儲存體。 如果最大大小值是以位元組為單位來指定，則必須是 1 GB (1073741824 個位元組) 的倍數。
- 可以儲存在資料庫資料檔案中的資料量，會受限於設定的資料儲存體大小上限。 除了該儲存體以外，Azure SQL Database 會自動設定30% 以上的儲存空間，以供交易記錄使用。
- Azure SQL Database 會為資料庫自動設定 32 GB 的每個 vCore `tempdb` 。 `tempdb` 位於所有服務層級中的本機 SSD 儲存體上。
- 單一資料庫或彈性集區的儲存體價格是資料儲存和交易記錄儲存體數量的總和乘以服務層的儲存體單價。 `tempdb`價格中包含的成本。 如需儲存體價格的詳細資訊，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱 [管理 Azure SQL Database 中](file-space-manage.md)的檔案空間。

### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

- 單一資料庫的 DTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量與大小上限，請參閱[單一資料庫：儲存體大小與計算大小](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 您可以使用 Azure 入口網站、 [transact-sql](/sql/t-sql/statements/alter-database-transact-sql#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或 [REST API](/rest/api/sql/databases/update)來增加其大小上限，以布建單一資料庫的額外儲存空間。
- 單一資料庫之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需額外儲存體價格的詳細資訊，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱 [管理 Azure SQL Database 中](file-space-manage.md)的檔案空間。

### <a name="geo-replicated-database"></a>異地複寫資料庫

若要變更複寫次要資料庫的資料庫大小，請變更主資料庫的大小。 然後，這種變更也會在次要資料庫上進行複寫和執行。

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>當大小上限大於 1 TB 時的 P11 和 P15 條件約束

所有區域目前均可使用進階層中超過 1 TB 的儲存體，但：中國東部、中國北部、德國中部和德國東北部除外。 在這些區域中，進階層中的儲存空間上限為 1 TB。 下列考量與限制適用於大小上限大於 1 TB 的 P11 和 P15 資料庫：

- 如果 P11 或 P15 資料庫的大小上限設定為大於 1 TB 的值，則只能將其還原或複製到 P11 或 P15 資料庫。  接著，如果在重新調整作業時配置的空間量不超過新計算大小的大小上限，則可將資料庫重新調整至不同的計算大小。
- 若是作用中異地複寫案例：
  - 設定異地複寫關聯性：如果主資料庫是 P11 或 P15，則) 的次要 (也必須是 P11 或 P15。 較低的計算大小會遭到拒絕，因為它們不能支援超過 1 TB。
  - 在異地複寫關聯性中升級主要資料庫：將主要資料庫的大小上限變更為 1 TB 以上會在次要資料庫中觸發相同的變更。 這兩種升級方式都必須成功，在主要資料庫中的變更才會生效。 適用於大於 1-TB 選項的區域限制。 如果次要資料庫所在的區域不支援超過 1 TB，則不會升級主要複本。
- 不支援使用匯入/匯出服務來載入 P11/P15 資料庫（超過 1 TB）。 使用 SqlPackage.exe 來[匯入](database-import.md)和[匯出](database-export.md)資料。

## <a name="next-steps"></a>後續步驟

如需整體資源限制，請參閱 [Azure SQL Database 以 vCore 為基礎的資源限制-單一資料庫](resource-limits-vcore-single-databases.md) 和 [Azure SQL Database 以 DTU 為基礎的資源限制-單一資料庫](resource-limits-dtu-single-databases.md)。
