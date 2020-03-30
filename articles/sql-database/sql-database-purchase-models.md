---
title: 購買模型
description: 瞭解可用於 Azure SQL 資料庫的採購模型。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255986"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>在 vCore 和 DTU 採購模式之間進行選擇

Azure SQL 資料庫可讓您輕鬆購買完全託管的平臺作為服務 （PaaS） 資料庫引擎，以滿足您的性能和成本需求。 根據為 Azure SQL 資料庫選擇的部署模型，您可以選擇適合您購買的採購模型：

- [基於虛擬內核 （vCore） 的採購模型](sql-database-service-tiers-vcore.md)（推薦）。 此購買模型提供了預配計算層和無伺服器計算層之間的選擇。 使用預配計算層，您可以選擇始終為工作負荷預配的計算資源的確切數量。 使用無伺服器計算層，可以在可配置的計算範圍內指定計算資源的自動縮放。 使用此計算層，您還可以根據工作負載活動自動暫停和恢復資料庫。 在預配計算層中，vCore 單位時間單價低於無伺服器計算層。
- [資料庫事務單元 （DTU） 基於的採購模型](sql-database-service-tiers-dtu.md)。 此採購模型提供針對常見工作負載平衡的捆綁計算和存儲包。

不同的 Azure SQL 資料庫部署模型提供了不同的採購模型：

- [Azure SQL Database](sql-database-technical-overview.md) 中的[單一資料庫](sql-database-single-databases-manage.md)與[彈性集區](sql-database-elastic-pool.md)部署選項會提供[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。
- Azure SQL 資料庫中的[託管實例](sql-database-managed-instance.md)部署選項僅提供[基於 vCore 的採購模型](sql-database-service-tiers-vcore.md)。
- [Hyperscale 服務層](sql-database-service-tier-hyperscale.md)可用於使用[基於 vCore 的採購模型](sql-database-service-tiers-vcore.md)的單個資料庫。

下表和圖表比較和對比了基於 vCore 和基於 DTU 的採購模型：

|**購買模型**|**描述**|**適用對象**|
|---|---|---|
|以 DTU 為基礎的模型|此模型基於計算、存儲和 I/O 資源的捆綁度量。 計算大小以單個資料庫的 DT 和彈性池的彈性資料庫事務單元 （eDT） 表示。 有關 DT 和 eDTUS 的詳細資訊，請參閱[什麼是 DT 和 eDT？](sql-database-purchase-models.md#dtu-based-purchasing-model)|最適合需要簡單、預配置的資源選項的客戶。|
|以虛擬核心為基礎的模型|此模型可讓您獨立地選擇計算和儲存體資源。 以虛擬核心為基礎的購買模型也可讓您使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以節省成本。|適合重視彈性、控制力和透明度的客戶。|
||||  

![定價模型比較](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>計算成本

### <a name="provisioned-compute-costs"></a>預配計算成本

在預配計算層中，計算成本反映為應用程式預配的總計算容量。

在業務關鍵服務層級中，我們會自動配置至少 3 個複本。 為了反映計算資源的這種額外分配，基於 vCore 的採購模型中的價格在關鍵業務服務層中比泛型服務層的價格高出大約 2.7 倍。 同樣，業務關鍵型服務層中每 GB 的存儲價格較高，反映了 SSD 存儲更高的 IO 限制和更低的延遲。

對於業務關鍵型服務層和泛型服務層，備份存儲成本相同，因為兩個層都使用標準存儲進行備份。

### <a name="serverless-compute-costs"></a>無伺服器計算成本

有關如何定義計算容量和計算無伺服器計算層的成本的說明，請參閱[SQL 資料庫無伺服器](sql-database-serverless.md)。

## <a name="storage-costs"></a>儲存成本

不同類型的儲存體會以不同方式計費。 對於資料存儲，將根據您選擇的最大資料庫或池大小為預配存儲付費。 除非降低或增加最大值，否則成本不會更改。 備份儲存體與您執行個體的自動備份相關聯，而且是動態配置的。 增加備份保留期會增加實例使用的備份存儲。

預設情況下，資料庫的 7 天自動備份將複製到讀取存取異地冗余存儲 （RA-GRS） 標準 Blob 存儲帳戶。 此存儲由每週完整備份、每日差異備份和事務記錄備份使用，每 5 分鐘複製一次。 事務日誌的大小取決於資料庫的更改速率。 提供的最低存儲量，等於資料庫大小的 100%，無需支付額外費用。 備份儲存體的額外使用量，會按月以 GB 為單位收費。

如需儲存體價格的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/sql-database/single/)頁面。

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

虛擬內核 （vCore） 表示邏輯 CPU，並為您提供了在一代硬體和硬體的物理特性（例如，內核數量、記憶體和存儲大小）之間進行選擇的選項。 基於 vCore 的採購模型為您提供了靈活性、控制性、單個資源消耗的透明度，以及將本地工作負載要求轉換為雲的直觀方法。 此模型允許您根據工作負載需求選擇計算、記憶體和存儲資源。

在基於 vCore 的採購模型中，您可以選擇[單個資料庫](sql-database-single-database-scale.md)、[彈性池](sql-database-elastic-pool.md)和[託管實例](sql-database-managed-instance.md)的[通用](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)服務和[業務關鍵型](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)服務層。 對於單個資料庫，您還可以選擇[超大規模服務層](sql-database-service-tier-hyperscale.md)。

基於 vCore 的採購模型允許您獨立選擇計算和存儲資源、匹配本地性能並優化價格。 在基於 vCore 的採購模型中，您需要支付以下費用：

- 計算資源（服務層 + vCore 的數量和記憶體量 + 硬體的生成）。
- 資料和日誌存儲的類型和數量。
- 備份存儲 （RA-GRS）。

> [!IMPORTANT]
> 計算資源、I/O 以及資料和日誌存儲按資料庫或彈性池收費。 備份存儲按每個資料庫收費。 如需受控執行個體費用的詳細資訊，請參閱[受控執行個體](sql-database-managed-instance.md)。
> **區域限制：** 有關當前受支援區域的清單，請參閱[按區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 要在當前不受支援的區域中創建託管實例，[請通過 Azure 門戶發送支援請求](quota-increase-request.md)。

如果單個資料庫或彈性池消耗超過 300 個 DT，則轉換為基於 vCore 的採購模型可能會降低您的成本。 您可以使用您選擇的 API 或使用 Azure 門戶進行轉換，無需停機。 但是，轉換不是必需的，也不會自動完成。 如果以 DTU 為基礎的購買模型符合您的效能和商務需求，請繼續使用即可。

要從基於 DTU 的採購模型轉換為基於 vCore 的採購模型，請使用以下經驗法則選擇計算大小：

- 標準層中每 100 個 DTUS 在泛型服務層中至少需要 1 個 vCore。
- 高級層中每 125 個 DTUS 在業務關鍵型服務層中至少需要 1 個 vCore。

> [!NOTE]
> DTU vCore 大小調整指南是近似值的，並且提供有助於對目標資料庫服務目標進行初始估計。 目標資料庫的最佳配置取決於工作負載。 
> 
> 實現最佳性價比可能需要利用 vCore 模型的靈活性來調整 vCore 的數量、[硬體生成](sql-database-service-tiers-vcore.md#hardware-generations)、[服務和](sql-database-service-tiers-vcore.md#service-tiers)[計算](sql-database-service-tiers-vcore.md#compute-tiers)層，以及調整其他資料庫配置參數，如[最大並行性程度](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)。

## <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

資料庫事務單元 （DTU） 表示 CPU、記憶體、讀取和寫入的混合度量。 以 DTU 為基礎的購買模型會提供一組預先設定好的計算資源組合和所包含的儲存體，以期達成不同的應用程式效能等級。 如果您更喜歡每月預配置的捆綁包和固定付款的簡單性，則基於 DTU 的模型可能更適合您的需要。

在基於 DTU 的採購模型中，您可以選擇[單個資料庫](sql-database-single-database-scale.md)和[彈性池](sql-database-elastic-pool.md)的基本、標準和高級服務層。 基於 DTU 的採購模型不適用於[託管實例](sql-database-managed-instance.md)。

### <a name="database-transaction-units-dtus"></a>資料庫交易單位 (DTU)

對於[服務層](sql-database-single-database-scale.md)中具有特定計算大小的單個資料庫，Microsoft 保證該資料庫的一定級別的資源（獨立于 Azure 雲中的任何其他資料庫）。 此保證提供了可預測的性能級別。 為資料庫分配的資源量計算為多個 DT，是計算、存儲和 I/O 資源的捆綁度量。

這些資源之間的比率最初由[線上交易處理 （OLTP） 基準工作負載](sql-database-benchmark-overview.md)決定，該工作負載設計為真實 OLTP 工作負載的典型特徵。 當工作負荷超過這些資源的任何量時，輸送量將受到限制，從而導致性能降低和超時。

工作負荷使用的資源不會影響 Azure 雲中其他 SQL 資料庫可用的資源。 同樣，其他工作負荷使用的資源不會影響 SQL 資料庫可用的資源。

![週框方塊](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTUS 對於瞭解為 Azure SQL 資料庫分配的不同計算大小和服務層的相對資源最有用。 例如：

- 通過增加資料庫的計算大小使 DT 增加一倍，相當於將資料庫可用的資源集加倍。
- 具有 1750 DTU 的優質服務層 P11 資料庫提供比具有 5 個 DTU 的基本服務層資料庫的 350 倍的 DTU 計算能力。  

要深入瞭解工作負載的資源 （DTU） 使用方式，請使用[查詢性能見解](sql-database-query-performance.md)：

- 按 CPU/持續時間/執行計數確定可能針對提高性能進行優化的頂級查詢。 例如，I/O 密集型查詢可能受益于[記憶體優化技術](sql-database-in-memory.md)，以更好地利用特定服務層和計算大小的可用記憶體。
- 向下切入查詢的詳細資訊以查看其文本和資源使用歷史記錄。
- 訪問顯示[SQL 資料庫顧問](sql-database-advisor.md)執行的操作的性能調整建議。

### <a name="elastic-database-transaction-units-edtus"></a>彈性資料庫事務單元（eDT）

對於始終可用的 SQL 資料庫，而不是提供可能並不總是需要的專用資源集 （DTUs），您可以將這些資料庫放在[彈性池](sql-database-elastic-pool.md)中。 彈性池中的資料庫位於單個 Azure SQL 資料庫伺服器上並共用資源池。

彈性池中的共用資源由彈性資料庫事務單位 （eDTUs） 測量。 彈性池提供了一個簡單、經濟高效的解決方案，用於管理具有廣泛變化和不可預知使用模式的多個資料庫的性能目標。 彈性池可確保池中的一個資料庫不能使用所有資源，同時確保池中的每個資料庫始終具有最少的可用資源量。

集區以固定價格提供固定數目的 eDTU。 在彈性池中，各個資料庫可以在配置的邊界內自動縮放。 負載較重的資料庫將消耗更多的 eDTUS 來滿足需求。 負載較輕的資料庫消耗的 eDTUS 更少。 沒有負載的資料庫不會取用 eDTU。 由於資源是為整個池（而不是每個資料庫）預配的，因此彈性池簡化了管理工作，並為池提供了可預測的預算。

您可以將其他 eDTUS 添加到現有池，而沒有資料庫停機時間，並且不會影響池中的資料庫。 同樣，如果您不再需要額外的 eDT，請隨時從現有池中刪除它們。 您還可以隨時向池中添加資料庫或從池中減去資料庫。 要為其他資料庫保留 eDT，請使用大量負載限制資料庫可以使用的 eDT。 如果資料庫始終不足資源，請將其移出池，並將其配置為具有可預測所需資源的單個資料庫。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>判斷工作負載所需的 DTU 數目

如果要將現有的本地或 SQL Server 虛擬機器工作負荷遷移到 Azure SQL 資料庫，請使用[DTU 計算機](https://dtucalculator.azurewebsites.net/)來近似所需的 DTU 數量。 對於現有的 Azure SQL 資料庫工作負荷，請使用[查詢性能見解](sql-database-query-performance.md)來瞭解資料庫資源消耗 （DTUs）， 並獲得更深入的見解以優化工作負荷。 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)動態管理檢視 （DMV） 允許您查看過去一小時的資源消耗量。 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)目錄檢視顯示過去 14 天的資源消耗，但逼真度較低，為 5 分鐘平均值。

### <a name="determine-dtu-utilization"></a>確定 DTU 利用率

要確定 DTU/eDTU 利用率相對於資料庫或彈性池的 DTU/eDTU 限制的平均百分比，請使用以下公式：

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

此公式的輸入值可以從[sys.dm_db_resource_stats、sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)和[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV 獲得。 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 換句話說，要確定 DTU/eDTU 利用率對資料庫或彈性池的 DTU/eDTU 限制的百分比，請從以下點選擇最大百分比值`avg_cpu_percent`：`avg_data_io_percent`和`avg_log_write_percent`給定的時間點。

> [!NOTE]
> 資料庫的 DTU 限制由 CPU、讀取、寫入和資料庫可用的記憶體決定。 但是，由於 SQL Server 資料庫引擎通常使用其所有可用記憶體進行資料緩存以提高性能，因此`avg_memory_usage_percent`無論當前資料庫負載如何，該值通常接近 100%。 因此，即使記憶體間接影響 DTU 限制，也不會在 DTU 利用率公式中使用。
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>能受益於彈性資源集區的工作負載

池非常適合資源利用率平均值低且利用率峰值相對較少的資料庫。 有關詳細資訊，請參閱[何時應考慮 SQL 資料庫彈性池？](sql-database-elastic-pool.md)

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>基於 DTU 的採購模型中的硬體代

在基於 DTU 的採購模型中，客戶無法選擇用於其資料庫的硬體生成。 雖然給定資料庫通常在特定硬體生成上長時間（通常持續數月），但某些事件可能會導致資料庫移動到另一個硬體生成。

例如，如果資料庫被向上擴展或縮減為不同的服務目標，或者資料中心中的當前基礎結構接近其容量限制，或者當前使用的硬體正在退役，由於其壽命的結束。

如果將資料庫移動到不同的硬體，工作負載性能可能會更改。 DTU 模型可確保[DTU 基準](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark)工作負載的輸送量和回應時間將基本相同，因為資料庫將移動到不同的硬體生成，只要其服務目標（DTU 的數量）保持不變。 

但是，在 Azure SQL 資料庫中運行的各種客戶工作負載中，對同一服務目標使用不同的硬體的影響可能更為明顯。 不同的工作負載將受益于不同的硬體設定和功能。 因此，對於 DTU 基準以外的工作負載，如果資料庫從一個硬體生成移動到另一個硬體生成，則可以看到性能差異。

例如，由於在 Gen5 中使用加速網路，對網路延遲敏感的應用程式在 Gen5 硬體上可以看到更好的性能，但使用密集讀取 IO 的應用程式在 Gen4 硬體上可以看到更好的性能，因為 Gen4 硬體與 Gen5 的性能更高第 4 代上每個內核比的記憶體更高。

具有對硬體更改敏感的工作負載的客戶，或者希望控制資料庫硬體生成選擇的客戶，可以使用[vCore](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore)模型在資料庫創建和縮放期間選擇首選硬體生成。 在 vCore 模型中，記錄每個硬體生成上每個服務目標的資源限制，包括[單個資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)和[彈性池](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)。 有關 vCore 模型中硬體生成的詳細資訊，請參閱[硬體生成](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations)。

## <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>我是否需要使應用程式離線才能從基於 DTU 的服務層轉換為基於 vCore 的服務層？

否。 您無需使應用程式離線。 新的服務層提供了一個簡單的線上轉換方法，類似于將資料庫從標準級別升級到高級服務層的現有過程，相反。 可以使用 Azure 門戶、PowerShell、Azure CLI、T-SQL 或 REST API 開始此轉換。 請參閱[管理單個資料庫](sql-database-single-database-scale.md)[和管理彈性池](sql-database-elastic-pool.md)。

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>是否可以將基於 vCore 的採購模型中的服務層中的資料庫轉換為基於 DTU 的採購模型中的服務層？

可以，通過使用 Azure 門戶、PowerShell、Azure CLI、T-SQL 或 REST API，可以輕鬆地將資料庫轉換為任何受支援的性能目標。 請參閱[管理單個資料庫](sql-database-single-database-scale.md)[和管理彈性池](sql-database-elastic-pool.md)。

## <a name="next-steps"></a>後續步驟

- 有關基於 vCore 的採購模型的詳細資訊，請參閱基於[vCore 的採購模型](sql-database-service-tiers-vcore.md)。
- 有關基於 DTU 的採購模型的詳細資訊，請參閱基於[DTU 的採購模型](sql-database-service-tiers-dtu.md)。
