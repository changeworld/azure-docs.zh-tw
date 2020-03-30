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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462593"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>在 Azure SQL Database 中調整彈性集區

本文說明如何在 Azure SQL Database 中調整彈性集區和集區資料庫可用的計算和儲存資源。

## <a name="change-compute-resources-vcores-or-dtus"></a>更改計算資源（vCore 或 DT）

在最初選取 vCore 或 eDTUs 的數量後，可以使用[Azure 門戶](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)[、PowerShell、Azure](/powershell/module/az.sql/Get-AzSqlElasticPool) [CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)或[REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)根據實際體驗動態擴展或縮小彈性池。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>更改服務層或重新縮放計算大小的影響

更改彈性池的服務層或計算大小遵循與單個資料庫類似的模式，主要涉及執行以下步驟的服務：

1. 為彈性池創建新的計算實例  

    使用請求的服務層和計算大小創建彈性池的新計算實例。 對於服務層和計算大小更改的某些組合，必須在涉及複製資料的新計算實例中創建每個資料庫的副本，並且可以強烈地影響總體延遲。 無論如何，在此步驟中資料庫保持線上狀態，並且連接將繼續定向到原始計算實例中的資料庫。

2. 將連接路由切換到新的計算實例

    將刪除與原始計算實例中資料庫的現有連接。 任何新連接都建立到新計算實例中的資料庫。 對於服務層和計算大小更改的某些組合，資料庫檔案在切換期間分離並重新連接。  無論如何，當資料庫通常不可用少於 30 秒且通常只有幾秒鐘時，交換器可能會導致短暫的服務中斷。 如果在斷開連接時運行了很長時間的事務，則此步驟的持續時間可能會更長，以便恢復中止的事務。 [加速資料庫恢復](sql-database-accelerated-database-recovery.md)可以減少中止長時間運行的事務的影響。

> [!IMPORTANT]
> 在工作流中的任何步驟中不會丟失任何資料。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>更改服務層或重新縮放計算大小的延遲

更改服務層或重新縮放單個資料庫或彈性池的計算大小的估計延遲參數化如下：

|服務層|基本單一資料庫，</br>標準 （S0-S1）|基本彈性池，</br>標準 （S2-S12）， </br>超大規模， </br>通用單資料庫或彈性池|高級或業務關鍵型單一資料庫或彈性池|
|:---|:---|:---|:---|
|**基本單一資料庫</br>，標準 （S0-S1）**|&bull;&nbsp;恒定時間延遲，與使用的空間無關</br>&bull;&nbsp;通常，少於 5 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|
|**基本彈性池，</br>標準 （S2-S12）、</br>超量</br>程、通用單資料庫或彈性池**|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|&bull;&nbsp;恒定時間延遲，與使用的空間無關</br>&bull;&nbsp;通常，少於 5 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|
|**高級或業務關鍵型單一資料庫或彈性池**|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|

> [!NOTE]
>
> - 在更改彈性池的服務層或重新縮放計算的情況下，應使用池中所有資料庫使用的空間的總和來計算估計值。
> - 在將資料庫移動到/移離彈性池的情況下，只有資料庫使用的空間會影響延遲，而影響彈性池使用的空間。
>
> [!TIP]
> 要監視正在進行的操作，請參閱：使用[SQL REST API 管理操作](https://docs.microsoft.com/rest/api/sql/operations/list)，[使用 CLI 管理操作](/cli/azure/sql/db/op)，[使用 T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)監視操作，並監視這兩個 PowerShell 命令：[獲取 AzSql 資料庫活動](/powershell/module/az.sql/get-azsqldatabaseactivity)和[停止-AzSql資料庫活動](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>更改服務層或重新縮放計算大小時的其他注意事項

- 為彈性池縮小 vCore 或 eDTD 時，池使用的空間必須小於目標服務層和池 eDTUs 的最大允許大小。
- 重新調整彈性集區的 eDTU 時，如果發生下列情況，就會產生額外的儲存體成本：(1) 目標集區支援集區的儲存體大小上限，而且 (2) 儲存體大小上限超過目標集區內含的儲存體數量。 例如，如果大小上限為 100 GB 的 100 eDTU 標準集區縮減為 50 eDTU 標準集區，則會產生額外的儲存體成本，因為目標集區可支援 100 GB 的大小上限，而且其內含儲存體數量只有 50 GB。 因此，額外的儲存體數量為 100 GB – 50 GB = 50 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。

### <a name="billing-during-rescaling"></a>重新縮放期間的計費

您需要支付使用最高服務層級資料庫存在的時數 + 在該小時適用的計算大小，不論使用狀況或資料庫是否在作用中少於一小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。

## <a name="change-elastic-pool-storage-size"></a>變更彈性集區儲存體大小

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 有關詳細資訊，請參閱在[Azure SQL 資料庫中管理檔空間](sql-database-file-space-management.md)。

### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

- 可以將儲存體佈建到大小上限：

  - 針對標準或一般用途服務層級中的儲存體，增加或減少大小 (以 10 GB 為增量單位)
  - 針對進階或商務關鍵性服務層級中的儲存體，增加或減少大小 (以 250 GB 為增量單位)
- 增加或減少其大小上限，即可佈建彈性集區的儲存體。
- 彈性集區儲存體的價格為儲存體數量乘以服務層的儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 有關詳細資訊，請參閱在[Azure SQL 資料庫中管理檔空間](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

- 彈性集區的 eDTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量和大小上限，請參閱[彈性集區：儲存體大小與計算大小](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)。
- 可藉由使用 [Azure 入口網站](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update) 增加其大小上限，以佈建彈性集區的額外儲存體。
- 彈性集區之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 有關詳細資訊，請參閱在[Azure SQL 資料庫中管理檔空間](sql-database-file-space-management.md)。

## <a name="next-steps"></a>後續步驟

如需整體資源限制，請參閱[SQL Database 以虛擬核心為基礎的資源限制 - 彈性集區](sql-database-vcore-resource-limits-elastic-pools.md)和[SQL Database 以 DTU 為基礎的資源限制 - 彈性集區](sql-database-dtu-resource-limits-elastic-pools.md)。
