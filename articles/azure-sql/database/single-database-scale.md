---
title: 調整單一資料庫資源
description: 本文說明如何在 Azure SQL Database 中調整單一資料庫可用的計算和儲存資源。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/30/2020
ms.openlocfilehash: 84e9593884f40fce8affce628b7817c528b3c31d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84343280"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>在 Azure SQL Database 中調整單一資料庫資源

本文說明如何在布建的計算層級調整 Azure SQL Database 的計算和儲存體資源。 或者，[無伺服器計算層](serverless-tier-overview.md)會為使用的計算提供計算自動調整和每秒計費。

一開始挑選虛擬核心或 Dtu 數之後，您可以根據使用[Azure 入口網站](single-database-manage.md#the-azure-portal)、 [transact-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)的實際經驗，以動態方式相應增加或相應減少單一資料庫。

下列影片示範如何動態變更服務層級與計算大小，以提高單一資料庫的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](file-space-manage.md)。

## <a name="impact"></a>影響

變更服務層級或計算大小主要涉及服務執行下列步驟：

1. 為資料庫建立新的計算實例。 

    使用要求的服務層級和計算大小建立新的計算實例。 針對某些服務層級和計算大小變更的組合，必須在新的計算實例中建立資料庫的複本，這牽涉到複製資料，而且可能會強烈影響整體延遲。 不論是哪一種情況，在此步驟期間，資料庫都會保持上線，而且連接會繼續導向至原始計算實例中的資料庫。

2. 將連接的路由切換至新的計算實例。

    原始計算實例中的資料庫現有連接會被捨棄。 系統會在新的計算實例中，為資料庫建立任何新的連接。 針對某些服務層級和計算大小變更的組合，資料庫檔案會在切換期間卸離並重新附加。  無論如何，在資料庫無法使用時，此參數可能會導致短暫的服務中斷，通常只有幾秒鐘的時間。 當連接中斷時，如果有長時間執行的交易正在執行，則此步驟的持續時間可能需要較長的時間才能復原中止的交易。 [加速資料庫](../accelerated-database-recovery.md)復原可以降低中止長時間執行之交易的影響。

> [!IMPORTANT]
> 在工作流程中的任何步驟期間，都不會遺失任何資料。 請確定您已在服務層級變更時，使用 Azure SQL Database 的應用程式和元件中，執行了一些[重試邏輯](troubleshoot-common-connectivity-issues.md)。

## <a name="latency"></a>Latency

變更服務層的預估延遲、調整單一資料庫或彈性集區的計算大小、將資料庫移入/移出彈性集區，或在彈性集區之間移動資料庫的參數如下：

|服務層|基本單一資料庫、</br>標準（S0-S1）|基本彈性集區，</br>標準（S2-S12）、 </br>超大規模資料庫 </br>一般用途單一資料庫或彈性集區|Premium 或 Business Critical 單一資料庫或彈性集區|
|:---|:---|:---|:---|
|**基本單一資料庫， </br> 標準（S0-S1）**|&bull;與 &nbsp; 使用的空間無關的固定時間延遲</br>&bull;&nbsp;通常不到5分鐘|&bull;&nbsp;因數據複製而使用的資料庫空間的延遲比例</br>&bull;&nbsp;通常，每 GB 使用的空間少於1分鐘|&bull;&nbsp;因數據複製而使用的資料庫空間的延遲比例</br>&bull;&nbsp;通常，每 GB 使用的空間少於1分鐘|
|**基本彈性集區， </br> 標準（S2-S12）， </br> 超大規模資料庫， </br> 一般用途單一資料庫或彈性集區**|&bull;&nbsp;因數據複製而使用的資料庫空間的延遲比例</br>&bull;&nbsp;通常，每 GB 使用的空間少於1分鐘|&bull;與 &nbsp; 使用的空間無關的固定時間延遲</br>&bull;&nbsp;通常不到5分鐘|&bull;&nbsp;因數據複製而使用的資料庫空間的延遲比例</br>&bull;&nbsp;通常，每 GB 使用的空間少於1分鐘|
|**Premium 或 Business Critical 單一資料庫或彈性集區**|&bull;&nbsp;因數據複製而使用的資料庫空間的延遲比例</br>&bull;&nbsp;通常，每 GB 使用的空間少於1分鐘|&bull;&nbsp;因數據複製而使用的資料庫空間的延遲比例</br>&bull;&nbsp;通常，每 GB 使用的空間少於1分鐘|&bull;&nbsp;因數據複製而使用的資料庫空間的延遲比例</br>&bull;&nbsp;通常，每 GB 使用的空間少於1分鐘|

> [!NOTE]
> 此外，針對標準（S2-S12）和一般用途資料庫，如果資料庫使用 Premium 檔案共用（[PFS](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)）儲存體，將資料庫移入/移出彈性集區的延遲，或在彈性集區之間移動時，將會與資料庫大小成正比。
>
> 若要判斷資料庫是否使用 PFS 儲存體，請在資料庫的內容中執行下列查詢。 如果 AccountType 資料行中的值是 `PremiumFileStorage` ，則資料庫會使用 PFS 儲存體。
 
```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> 若要監視進行中的作業，請參閱：[使用 SQL REST API 管理作業](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理](/cli/azure/sql/db/op)作業、[使用 t-sql 監視作業](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)和這兩個 PowerShell 命令： [get-azsqldatabaseactivity](/powershell/module/az.sql/get-azsqldatabaseactivity)和[get-azsqldatabaseactivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

## <a name="cancelling-changes"></a>取消變更

服務層級變更或計算重新調整作業可以取消。

### <a name="the-azure-portal"></a>Azure 入口網站

在 [資料庫總覽] 分頁中，流覽至 [**通知**]，然後按一下表示有進行中操作的磚：

![進行中的操作](./media/single-database-scale/ongoing-operations.png)

接下來，按一下標示為 [**取消此操作**] 的按鈕。

![取消進行中的操作](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

從 PowerShell 命令提示字元，設定 `$resourceGroupName` 、 `$serverName` 和，然後 `$databaseName` 執行下列命令：

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

- 如果您要升級至較高的服務層級或計算大小，除非您明確指定較大的大小（maxsize），否則資料庫大小上限不會增加。
- 若要將資料庫降級，資料庫已用的空間必須小於目標服務層級和計算大小允許的大小上限。
- 從**進階**降級至**標準**層時，如果發生下列情況，將會產生額外的儲存體成本：(1) 以目標計算大小支援資料庫的大小上限，而且 (2) 大小上限超過目標計算大小的內含儲存體數量。 例如，如果大小上限為 500 GB 的 P1 資料庫縮減為 S3，則會有額外的儲存成本，因為 S3 支援的大小上限為 1 TB，而其內含的儲存體數量只有 250 GB。 因此，額外的儲存體數量為 500 GB – 250 GB = 250 GB。 如需額外儲存體的價格，請參閱[Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。
- 升級資料庫時，若已啟用[異地複寫](active-geo-replication-configure-portal.md)，您必須先將其次要資料庫升級為所需的服務層級與計算大小，然後再升級主要資料庫 (最佳效能的一般指引)。 升級至不同的版本時，需要先升級次要資料庫。
- 當您在啟用[異地複寫](active-geo-replication-configure-portal.md)的情況下將資料庫降級時，必須先將其主要資料庫降級為所需的服務層級與計算大小，然後再將次要資料庫降級 (最佳效能的一般指引)。 當降級至不同的版本時，必須先降級主資料庫。
- 還原服務會針對各種服務層級提供不同的供應項目。 如果您要降級至**基本**層，則備份保留期限較低。 請參閱 [Azure SQL Database 備份](automated-backups-overview.md)。
- 在變更完成之前，不會套用資料庫的新屬性。

## <a name="billing"></a>計費

您會使用最高的服務層級 + 該小時內所套用的計算大小（不論使用量或資料庫是否使用時間不到一小時），向您收取資料庫存在的每個小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。

## <a name="change-storage-size"></a>變更儲存體大小

### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

- 您可以使用 1 GB 的增量，將儲存體布建到資料儲存體的最大大小限制。 可設定的最小資料儲存體為 1 GB。 如需每個服務目標中的資料儲存體最大大小限制，請參閱[單一資料庫](resource-limits-vcore-single-databases.md)的資源限制檔頁面和[彈性](resource-limits-vcore-elastic-pools.md)集區。
- 單一資料庫的資料儲存體可以藉由使用[Azure 入口網站](https://portal.azure.com)、 [transact-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)來增加或減少其大小上限來布建。 如果指定的大小上限值是以位元組為單位，它必須是 1 GB 的倍數（1073741824個位元組）。
- 可儲存在資料庫資料檔案中的資料量會受到設定的資料儲存體大小上限所限制。 除了該儲存體之外，Azure SQL Database 會自動為交易記錄檔配置30% 以上的儲存空間。
- Azure SQL Database 會針對資料庫自動設定每個 vCore 32 GB `tempdb` 。 `tempdb`位於所有服務層級中的本機 SSD 儲存體。
- 單一資料庫或彈性集區的儲存體價格是資料儲存體和交易記錄儲存體數量的總和，乘以服務層的儲存體單位價格。 的成本 `tempdb` 包含在價格中。 如需儲存體價格的詳細資訊，請參閱[Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](file-space-manage.md)。

### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

- 單一資料庫的 DTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量與大小上限，請參閱[單一資料庫：儲存體大小與計算大小](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 藉由使用 Azure 入口網站、 [transact-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)增加大小上限，即可布建單一資料庫的額外儲存空間。
- 單一資料庫之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需額外儲存體價格的詳細資訊，請參閱[Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](file-space-manage.md)。

### <a name="geo-replicated-database"></a>異地複寫的資料庫

若要變更已複寫之次要資料庫的資料庫大小，請變更主資料庫的大小。 這種變更也會在次要資料庫上複寫和執行。

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>當大小上限大於 1 TB 時的 P11 和 P15 條件約束

所有區域目前均可使用進階層中超過 1 TB 的儲存體，但下列地區除外：中國東部、中國北部、德國中部、德國東北部、美國中西部、美國 DoD 地區和美國政府中部。 在這些區域中，進階層中的儲存空間上限為 1 TB。 下列考量與限制適用於大小上限大於 1 TB 的 P11 和 P15 資料庫：

- 如果 P11 或 P15 資料庫的大小上限已設定為大於 1 TB 的值，則只能還原或複製到 P11 或 P15 資料庫。  之後，您可以將資料庫重新調整至不同的計算大小，但前提是在重新調整作業時所配置的空間量不會超過新計算大小的大小上限。
- 若是作用中異地複寫案例：
  - 設定異地複寫關聯性：如果主資料庫為 P11 或 P15，則次要（）也必須是 P11 或 P15。 較低的計算大小會被視為次要複本而遭到拒絕，因為它們無法支援 1 TB 以上的功能。
  - 在異地複寫關聯性中升級主要資料庫：將主要資料庫的大小上限變更為 1 TB 以上會在次要資料庫中觸發相同的變更。 這兩種升級方式都必須成功，在主要資料庫中的變更才會生效。 適用於大於 1-TB 選項的區域限制。 如果次要資料庫位於不支援超過 1 TB 的區域中，則不會升級主要複本。
- 不支援使用匯入/匯出服務來載入超過 1 TB 的 P11/P15 資料庫。 使用 SqlPackage.exe 來[匯入](database-import.md)和[匯出](database-export.md)資料。

## <a name="next-steps"></a>後續步驟

如需整體資源限制，請參閱[Azure SQL Database vCore 為基礎的資源限制-單一資料庫](resource-limits-vcore-single-databases.md)和[Azure SQL Database 以 DTU 為基礎的資源限制-單一資料庫](resource-limits-dtu-single-databases.md)。
 