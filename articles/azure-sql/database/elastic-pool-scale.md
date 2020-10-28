---
title: 縮放彈性集區資源
description: 此頁面將針對 Azure SQL Database 中的彈性集區說明調整資源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 947d842860452425f8b30fbdaf9558c2a94a89a2
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781204"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>在 Azure SQL Database 中調整彈性集區
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文說明如何在 Azure SQL Database 中調整彈性集區和集區資料庫可用的計算和儲存資源。

## <a name="change-compute-resources-vcores-or-dtus"></a> (虛擬核心或 Dtu) 變更計算資源

一開始挑選虛擬核心或 Edtu 的數目之後，您就可以根據使用 [Azure 入口網站](elastic-pool-manage.md#azure-portal)、 [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、 [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)或 [REST API](/rest/api/sql/elasticpools/update)的實際體驗，動態地相應增加或減少彈性集區。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層級或重新調整計算大小的影響

變更彈性集區的服務層級或計算大小，會遵循與單一資料庫類似的模式，而且主要牽涉到服務執行下列步驟：

1. 建立彈性集區的新計算實例  

    彈性集區的新計算實例是以要求的服務層級和計算大小所建立。 針對某些服務層級和計算大小變更的組合，必須在新的計算實例中建立每個資料庫的複本，其中牽涉到複製資料，而且可能會對整體延遲造成強烈影響。 無論是哪種情況，在此步驟期間，資料庫會保持上線狀態，並繼續將連接導向至原始計算實例中的資料庫。

2. 將連接的路由切換至新的計算實例

    原始計算實例中資料庫的現有連接會遭到卸載。 新的計算實例中的資料庫會建立任何新的連接。 針對某些服務層級和計算大小變更的組合，系統會在切換期間卸離和重新附加資料庫檔案。  無論如何，在資料庫無法使用的情況下，通常不到30秒，而且通常只需要幾秒鐘的時間，切換可能會導致短暫的服務中斷。 如果在中斷連接時執行長時間執行的交易，則此步驟可能需要較長的時間才能復原中止的交易。 [加速資料庫](../accelerated-database-recovery.md) 復原可以降低中止長時間執行之交易的影響。

> [!IMPORTANT]
> 工作流程中的任何步驟都不會遺失任何資料。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層級或重新調整計算大小的延遲

變更服務層級的估計延遲、調整單一資料庫或彈性集區的計算大小、將資料庫移入/移出彈性集區，或在彈性集區之間移動資料庫的參數化方式如下：

|服務層級|基本單一資料庫，</br>標準 (S0-S1) |基本彈性集區，</br>標準 (S2-S12) 、 </br>一般用途單一資料庫或彈性集區|Premium 或 Business Critical 單一資料庫或彈性集區|超大規模資料庫
|:---|:---|:---|:---|:---|
|**基本單一資料庫， </br> 標準 (S0-S1)**|&bull;&nbsp;固定時間延遲，與使用的空間無關</br>&bull;&nbsp;通常不到5分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|
|**基本彈性集區、 </br> 標準 (S2-S12) 、 </br> 一般用途單一資料庫或彈性集區**|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;針對單一資料庫，固定時間延遲與使用的空間無關</br>&bull;&nbsp;通常不到5分鐘的單一資料庫</br>&bull;&nbsp;針對彈性集區，與資料庫數目成正比|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|
|**Premium 或 Business Critical 單一資料庫或彈性集區**|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|&bull;&nbsp;由於資料複製而使用資料庫空間的延遲比例</br>&bull;&nbsp;通常每 GB 使用的空間不到1分鐘|
|**超大規模資料庫**|N/A|N/A|N/A|&bull;&nbsp;固定時間延遲，與使用的空間無關</br>&bull;&nbsp;通常不到2分鐘|

> [!NOTE]
>
> - 在變更服務層級或重新調整彈性集區計算的情況下，應該使用在集區中的所有資料庫所使用的空間總和來計算預估值。
> - 在彈性集區之間移動資料庫的情況下，只有資料庫使用的空間會影響延遲，而不會影響彈性集區所使用的空間。
> - 針對標準和一般目的彈性集區，如果彈性集區使用 Premium 檔案共用 ([PFS](../../storage/files/storage-files-introduction.md)) 儲存體，則將資料庫移入/移出彈性集區或在彈性集區之間移動的延遲會與資料庫大小成正比。 若要判斷集區是否使用 PFS 儲存體，請在集區中任何資料庫的內容中執行下列查詢。 如果 AccountType 資料行中的值為 `PremiumFileStorage` 或 `PremiumFileStorage-ZRS` ，則集區會使用 PFS 儲存體。

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

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>變更服務層級或重新調整計算大小時的其他考慮

- 縮減彈性集區的虛擬核心或 Edtu 時，集區使用的空間必須小於目標服務層級和集區 Edtu 允許的大小上限。
- 重新調整彈性集區的 eDTU 時，如果發生下列情況，就會產生額外的儲存體成本：(1) 目標集區支援集區的儲存體大小上限，而且 (2) 儲存體大小上限超過目標集區內含的儲存體數量。 例如，如果大小上限為 100 GB 的 100 eDTU 標準集區縮減為 50 eDTU 標準集區，則會產生額外的儲存體成本，因為目標集區可支援 100 GB 的大小上限，而且其內含儲存體數量只有 50 GB。 因此，額外的儲存體數量為 100 GB – 50 GB = 50 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。

### <a name="billing-during-rescaling"></a>重新調整期間的計費

您需要支付使用最高服務層級資料庫存在的時數 + 在該小時適用的計算大小，不論使用狀況或資料庫是否在作用中少於一小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。

## <a name="change-elastic-pool-storage-size"></a>變更彈性集區儲存體大小

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱 [管理 Azure SQL Database 中](file-space-manage.md)的檔案空間。

### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

- 可以將儲存體佈建到大小上限：

  - 針對標準或一般用途服務層級中的儲存體，增加或減少大小 (以 10 GB 為增量單位)
  - 針對進階或商務關鍵性服務層級中的儲存體，增加或減少大小 (以 250 GB 為增量單位)
- 增加或減少其大小上限，即可佈建彈性集區的儲存體。
- 彈性集區儲存體的價格為儲存體數量乘以服務層的儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱 [管理 Azure SQL Database 中](file-space-manage.md)的檔案空間。

### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

- 彈性集區的 eDTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量和大小上限，請參閱[彈性集區：儲存體大小與計算大小](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)。
- 可藉由使用 [Azure 入口網站](elastic-pool-manage.md#azure-portal)、[PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 或 [REST API](/rest/api/sql/elasticpools/update) 增加其大小上限，以佈建彈性集區的額外儲存體。
- 彈性集區之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱 [管理 Azure SQL Database 中](file-space-manage.md)的檔案空間。

## <a name="next-steps"></a>後續步驟

如需整體資源限制，請參閱[SQL Database 以虛擬核心為基礎的資源限制 - 彈性集區](resource-limits-vcore-elastic-pools.md)和[SQL Database 以 DTU 為基礎的資源限制 - 彈性集區](resource-limits-dtu-elastic-pools.md)。