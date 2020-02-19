---
title: 縮放彈性集區資源
description: 此頁面將針對 Azure SQL Database 中的彈性集區說明調整資源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462593"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>在 Azure SQL Database 中調整彈性集區

本文說明如何在 Azure SQL Database 中調整彈性集區和集區資料庫可用的計算和儲存資源。

## <a name="change-compute-resources-vcores-or-dtus"></a>變更計算資源（虛擬核心或 Dtu）

一開始挑選虛擬核心或 Edtu 數目之後，您可以根據使用[Azure 入口網站](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、 [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、 [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)或[REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)的實際體驗，以動態方式相應增加或相應減少彈性集區。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層級或重新調整計算大小的影響

變更彈性集區的服務層級或計算大小，會遵循與單一資料庫類似的模式，而且主要涉及執行下列步驟的服務：

1. 為彈性集區建立新的計算實例  

    會使用要求的服務層級和計算大小來建立彈性集區的新計算實例。 針對某些服務層級和計算大小變更的組合，必須在新的計算實例中建立每個資料庫的複本，這牽涉到複製資料，而且可能會強烈影響整體延遲。 不論是哪一種情況，在此步驟期間，資料庫都會維持線上狀態，而且連接會繼續導向至原始計算實例中的資料庫。

2. 將連接的路由切換至新的計算實例

    原始計算實例中的資料庫現有連接會被捨棄。 新的計算實例中的資料庫會建立任何新的連接。 針對某些服務層級和計算大小變更的組合，資料庫檔案會在切換期間卸離並重新附加。  無論如何，當資料庫無法使用的時間通常不到30秒，而且通常只需要幾秒鐘，切換就會導致短暫的服務中斷。 當連接中斷時，如果有長時間執行的交易在執行中，則此步驟可能需要較長的時間才能復原中止的交易。 [加速資料庫](sql-database-accelerated-database-recovery.md)復原可以降低中止長時間執行之交易的影響。

> [!IMPORTANT]
> 在工作流程中的任何步驟期間，都不會遺失任何資料。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層級或重新調整計算大小的延遲

變更服務層級或重新調整單一資料庫或彈性集區之計算大小的預估延遲會參數化，如下所示：

|服務層|基本單一資料庫、</br>標準（S0-S1）|基本彈性集區，</br>標準（S2-S12）、 </br>超大規模資料庫 </br>一般用途單一資料庫或彈性集區|Premium 或 Business Critical 單一資料庫或彈性集區|
|:---|:---|:---|:---|
|**基本單一資料庫，</br> 標準（S0-S1）**|&bull; &nbsp;與所用空間無關的固定時間延遲</br>&bull; &nbsp;通常不到5分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|
|**基本彈性集區、</br>標準（S2-S12）、</br>超大規模資料庫、</br>一般用途單一資料庫或彈性集區**|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|&bull; &nbsp;與所用空間無關的固定時間延遲</br>&bull; &nbsp;通常不到5分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|
|**Premium 或 Business Critical 單一資料庫或彈性集區**|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|

> [!NOTE]
>
> - 如果變更服務層級或重新調整彈性集區的計算，就應該使用集區中所有資料庫所使用的空間總和來計算估計值。
> - 如果將資料庫移入/移出彈性集區，只有資料庫所使用的空間會影響延遲，而非彈性集區所使用的空間。
>
> [!TIP]
> 若要監視進行中的作業，請參閱：[使用 SQL REST API 管理作業](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理](/cli/azure/sql/db/op)作業、[使用 t-sql 監視作業](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)和這兩個 PowerShell 命令： [get-azsqldatabaseactivity](/powershell/module/az.sql/get-azsqldatabaseactivity)和[get-azsqldatabaseactivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>變更服務層級或重新調整計算大小時的其他考慮

- 縮減彈性集區的虛擬核心或 Edtu 時，集區使用的空間必須小於目標服務層級和集區 Edtu 允許的大小上限。
- 重新調整彈性集區的 eDTU 時，如果發生下列情況，就會產生額外的儲存體成本：(1) 目標集區支援集區的儲存體大小上限，而且 (2) 儲存體大小上限超過目標集區內含的儲存體數量。 例如，如果大小上限為 100 GB 的 100 eDTU 標準集區縮減為 50 eDTU 標準集區，則會產生額外的儲存體成本，因為目標集區可支援 100 GB 的大小上限，而且其內含儲存體數量只有 50 GB。 因此，額外的儲存體數量為 100 GB – 50 GB = 50 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。

### <a name="billing-during-rescaling"></a>重新調整期間的計費

您需要支付使用最高服務層級資料庫存在的時數 + 在該小時適用的計算大小，不論使用狀況或資料庫是否在作用中少於一小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。

## <a name="change-elastic-pool-storage-size"></a>變更彈性集區儲存體大小

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

- 可以將儲存體佈建到大小上限：

  - 針對標準或一般用途服務層級中的儲存體，增加或減少大小 (以 10 GB 為增量單位)
  - 針對進階或商務關鍵性服務層級中的儲存體，增加或減少大小 (以 250 GB 為增量單位)
- 增加或減少其大小上限，即可佈建彈性集區的儲存體。
- 彈性集區儲存體的價格為儲存體數量乘以服務層的儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

- 彈性集區的 eDTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量和大小上限，請參閱[彈性集區：儲存體大小與計算大小](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)。
- 可藉由使用 [Azure 入口網站](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update) 增加其大小上限，以佈建彈性集區的額外儲存體。
- 彈性集區之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="next-steps"></a>後續步驟

如需整體資源限制，請參閱[SQL Database 以虛擬核心為基礎的資源限制 - 彈性集區](sql-database-vcore-resource-limits-elastic-pools.md)和[SQL Database 以 DTU 為基礎的資源限制 - 彈性集區](sql-database-dtu-resource-limits-elastic-pools.md)。
