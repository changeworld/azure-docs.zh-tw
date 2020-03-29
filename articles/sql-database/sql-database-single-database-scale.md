---
title: 調整單一資料庫資源
description: 本文說明如何在 Azure SQL Database 中調整單一資料庫可用的計算和儲存資源。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 84846e642fa102045b89eb12dbc85b0995867a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061587"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>在 Azure SQL Database 中調整單一資料庫資源

本文介紹如何縮放預配計算層中可用於 Azure SQL 資料庫的計算和存儲資源。 或者，[無伺服器計算層](sql-database-serverless.md)提供計算自動縮放和每秒帳單，用於計算。

在最初選取 vCore 或 DTUs 的數量後，可以使用[Azure 門戶](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)[、Transact-SQL、PowerShell、Azure](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [CLI](/cli/azure/sql/db#az-sql-db-update)或 REST [PowerShell](/powershell/module/az.sql/set-azsqldatabase) [API](https://docs.microsoft.com/rest/api/sql/databases/update)根據實際經驗動態擴展或縮減單個資料庫。

下列影片示範如何動態變更服務層級與計算大小，以提高單一資料庫的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 有關詳細資訊，請參閱在[Azure SQL 資料庫中管理檔空間](sql-database-file-space-management.md)。

## <a name="impact"></a>影響

更改服務層或計算大小的主要涉及執行以下步驟的服務：

1. 為資料庫創建新的計算實例  

    使用請求的服務層和計算大小創建新的計算實例。 對於服務層和計算大小更改的某些組合，必須在涉及複製資料的新計算實例中創建資料庫副本，並可以強烈地影響總體延遲。 無論如何，在此步驟中資料庫保持線上狀態，並且連接將繼續定向到原始計算實例中的資料庫。

2. 將連接路由切換到新的計算實例

    將刪除與原始計算實例中資料庫的現有連接。 在新計算實例中建立到資料庫的任何新連接。 對於服務層和計算大小更改的某些組合，資料庫檔案在切換期間分離並重新連接。  無論如何，當資料庫通常不可用少於 30 秒且通常只有幾秒鐘時，交換器可能會導致短暫的服務中斷。 如果在斷開連接時運行了很長時間的事務，則此步驟的持續時間可能會更長，以便恢復中止的事務。 [加速資料庫恢復](sql-database-accelerated-database-recovery.md)可以減少中止長時間運行的事務的影響。

> [!IMPORTANT]
> 在工作流中的任何步驟中不會丟失任何資料。 請確保在更改服務層時使用 Azure SQL 資料庫的應用程式和元件中實現了一些[重試邏輯](sql-database-connectivity-issues.md)。

## <a name="latency"></a>Latency 

更改服務層或重新縮放單個資料庫或彈性池的計算大小的估計延遲參數化如下：

|服務層|基本單一資料庫，</br>標準 （S0-S1）|基本彈性池，</br>標準 （S2-S12）， </br>超大規模， </br>通用單資料庫或彈性池|高級或業務關鍵型單一資料庫或彈性池|
|:---|:---|:---|:---|
|**基本單一資料庫</br>，標準 （S0-S1）**|&bull;&nbsp;恒定時間延遲，與使用的空間無關</br>&bull;&nbsp;通常，少於 5 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|
|**基本彈性池，</br>標準 （S2-S12）、</br>超量</br>程、通用單資料庫或彈性池**|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|&bull;&nbsp;恒定時間延遲，與使用的空間無關</br>&bull;&nbsp;通常，少於 5 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|
|**高級或業務關鍵型單一資料庫或彈性池**|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|&bull;&nbsp;與資料複製導致使用的資料庫空間成比例的延遲</br>&bull;&nbsp;通常，每 GB 使用空間少於 1 分鐘|

> [!TIP]
> 要監視正在進行的操作，請參閱：使用[SQL REST API 管理操作](https://docs.microsoft.com/rest/api/sql/operations/list)，[使用 CLI 管理操作](/cli/azure/sql/db/op)，[使用 T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)監視操作，並監視這兩個 PowerShell 命令：[獲取 AzSql 資料庫活動](/powershell/module/az.sql/get-azsqldatabaseactivity)和[停止-AzSql資料庫活動](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

## <a name="cancelling-changes"></a>取消更改

可以取消服務層更改或計算重新縮放操作。

#### <a name="azure-portal"></a>Azure 入口網站

在資料庫概述邊欄選項卡中，導航到 **"通知"** 並按一下磁貼，指示正在進行操作：

![持續運營](media/sql-database-single-database-scale/ongoing-operations.png)

接下來，按一下標記為 **"取消此操作"的**按鈕。

![取消正在進行的操作](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

從 PowerShell 命令提示符中`$resourceGroupName`，`$serverName`設置`$databaseName`、 和 ， 然後運行以下命令：

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

- 如果您要升級到較高服務層級或計算大小，除非明確指定較大的大小 (大小上限)，否則資料庫大小上限不會增加。
- 若要將資料庫降級，資料庫已用的空間必須小於目標服務層級和計算大小允許的大小上限。
- 從**進階**降級至**標準**層時，如果發生下列情況，將會產生額外的儲存體成本：(1) 以目標計算大小支援資料庫的大小上限，而且 (2) 大小上限超過目標計算大小的內含儲存體數量。 例如，如果最大大小為 500 GB 的 P1 資料庫縮小為 S3，則需要額外的存儲成本，因為 S3 支援最大大小為 1 TB，並且其包含的存儲量僅為 250 GB。 因此，額外的儲存體數量為 500 GB – 250 GB = 250 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。
- 升級資料庫時，若已啟用[異地複寫](sql-database-geo-replication-portal.md)，您必須先將其次要資料庫升級為所需的服務層級與計算大小，然後再升級主要資料庫 (最佳效能的一般指引)。 升級到其他版本時，首先升級次要資料庫是必需的。
- 當您在啟用[異地複寫](sql-database-geo-replication-portal.md)的情況下將資料庫降級時，必須先將其主要資料庫降級為所需的服務層級與計算大小，然後再將次要資料庫降級 (最佳效能的一般指引)。 降級為其他版本時，首先降級主資料庫。
- 還原服務會針對各種服務層級提供不同的供應項目。 如果降級至**基本**層，會有較短的備份保留期。 請參閱 [Azure SQL Database 備份](sql-database-automated-backups.md)。
- 完成變更之前，不會將新屬性套用至資料庫。

## <a name="billing"></a>計費 

您需要支付使用最高服務層級資料庫存在的時數 + 在該小時適用的計算大小，不論使用狀況或資料庫是否在作用中少於一小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。

## <a name="change-storage-size"></a>變更儲存體大小

### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

- 可以使用 1 GB 的增量將存儲預配到資料存儲最大大小限制。 最小可配置資料存儲為 1 GB。 有關每個服務目標中的資料存儲最大大小限制，請參閱[單個資料庫](sql-database-vcore-resource-limits-single-databases.md)和[彈性池](sql-database-vcore-resource-limits-elastic-pools.md)的資源限制文檔頁。
- 單個資料庫的資料存儲可以通過使用[Azure 門戶](https://portal.azure.com)[、Transact-SQL、PowerShell、Azure](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [CLI](/cli/azure/sql/db#az-sql-db-update)或 REST [PowerShell](/powershell/module/az.sql/set-azsqldatabase) [API](https://docs.microsoft.com/rest/api/sql/databases/update)增加或減小其最大大小來預配。 如果以位元組為單位指定最大大小值，則必須為 1 GB 的倍數（1073741824 位元組）。
- 可存儲在資料庫資料檔案中的資料量受配置的資料存儲最大大小的限制。 除了該存儲之外，SQL 資料庫還自動分配 30% 以上的存儲用於事務日誌。
- SQL 資料庫自動為每個 vCore 為`tempdb`資料庫分配 32 GB。 `tempdb`位於所有服務層中的本地 SSD 存儲上。
- 單個資料庫或彈性池的存儲價格是資料存儲和事務日誌存儲量乘以服務層的存儲單價的總和。 費用`tempdb`包含在價格中。 有關存儲價格的詳細資訊，請參閱[SQL 資料庫定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 有關詳細資訊，請參閱在[Azure SQL 資料庫中管理檔空間](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

- 單一資料庫的 DTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量與大小上限，請參閱[單一資料庫：儲存體大小與計算大小](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 可以使用 Azure 門戶[、Transact-SQL、PowerShell、Azure](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [CLI](/cli/azure/sql/db#az-sql-db-update)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)[PowerShell](/powershell/module/az.sql/set-azsqldatabase)增加其最大大小來預配單個資料庫的額外存儲。
- 單一資料庫之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 有關詳細資訊，請參閱在[Azure SQL 資料庫中管理檔空間](sql-database-file-space-management.md)。

### <a name="geo-replicated-database"></a>地理複製資料庫

要更改複製的次要資料庫的資料庫大小，更改主資料庫的大小。 然後，此更改也將複製並在次要資料庫上實現。 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>最大大小大於 1 TB 時 P11 和 P15 約束

除中國東部、華北、德國中部、德國東北部、美國中西部、美國 DoD 地區和美國政府中心外，高級級別中目前所有區域都提供超過 1 TB 的存儲。 在這些區域中，進階層中的儲存空間上限為 1 TB。 下列考量與限制適用於大小上限大於 1 TB 的 P11 和 P15 資料庫：

- 如果 P11 或 P15 資料庫的最大大小設置為大於 1 TB 的值，則只能將其還原或複製到 P11 或 P15 資料庫。  隨後，如果重新縮放操作時分配的空間量不超過新計算大小的最大大小限制，則可以將資料庫重新縮放為不同的計算大小。
- 若是作用中異地複寫案例：
  - 設定異地複寫關聯性：如果 P11 或 P15 為主要資料庫，則次要資料庫也必須是 P11 或 P15；系統會拒絕使用較低的計算大小作為次要資料庫，因為這些資料庫無法支援 1 TB 以上的大小。
  - 在異地複寫關聯性中升級主要資料庫：將主要資料庫的大小上限變更為 1 TB 以上會在次要資料庫中觸發相同的變更。 這兩種升級方式都必須成功，在主要資料庫中的變更才會生效。 適用於大於 1-TB 選項的區域限制。 如果次要資料庫位於不支援大於 1 TB 的區域，就無法升級主要資料庫。
- 不支援使用匯入/匯出服務載入具有 1 TB 以上之 P11/P15 資料庫的功能。 使用 SqlPackage.exe 來[匯入](sql-database-import.md)和[匯出](sql-database-export.md)資料。

## <a name="next-steps"></a>後續步驟

如需整體資源限制，請參閱[SQL Database 以虛擬核心為基礎的資源限制 - 單一資料庫](sql-database-vcore-resource-limits-single-databases.md)和[SQL Database 以 DTU 為基礎的資源限制 - 彈性集區](sql-database-dtu-resource-limits-single-databases.md)。
