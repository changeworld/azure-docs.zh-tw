---
title: 購買模型
description: 瞭解適用于 Azure SQL Database 的購買模型。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/01/2020
ms.openlocfilehash: 20c93d214195f8fe389f4982e1d8b10998c7057d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192382"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>在 vCore 和 DTU 購買模型之間做選擇

Azure SQL Database 可讓您輕鬆購買完全受控的平臺即服務（PaaS）資料庫引擎，以符合您的效能和成本需求。 視您為 Azure SQL Database 選擇的部署模型而定，您可以選取適合您的購買模型：

- 以[虛擬核心（vCore）為基礎的購買模型](sql-database-service-tiers-vcore.md)（建議）。 此購買模型可讓您選擇布建的計算層和無伺服器計算層。 在布建的計算層中，您可以選擇一律針對您的工作負載布建的確切計算資源量。 透過無伺服器計算層，您可以透過可設定的計算範圍來指定計算資源的自動調整。 使用此計算層時，您也可以根據工作負載活動自動暫停和繼續資料庫。 在布建的計算層中，每個時間單位的 vCore 單位價格低於無伺服器計算層級。
- 以[資料庫交易單位（DTU）為基礎的購買模型](sql-database-service-tiers-dtu.md)。 此購買模型提供針對一般工作負載平衡的配套計算和儲存體套件。

不同的購買模型適用于不同的 Azure SQL Database 部署模型：

- [Azure SQL Database](sql-database-single-databases-manage.md) 中的[單一資料庫](sql-database-elastic-pool.md)與[彈性集區](sql-database-technical-overview.md)部署選項會提供[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。
- Azure SQL Database 中的[受控實例](sql-database-managed-instance.md)部署選項只提供[vCore 為基礎的購買模型](sql-database-service-tiers-vcore.md)。
- [超大規模資料庫服務層級](sql-database-service-tier-hyperscale.md)適用于使用[vCore 為基礎的購買模型](sql-database-service-tiers-vcore.md)的單一資料庫。

下表和圖表會比較和對比以 vCore 為基礎和以 DTU 為基礎的購買模型：

|**購買模型**|**說明**|**適用對象**|
|---|---|---|
|以 DTU 為基礎的模型|此模型是以計算、儲存體和 i/o 資源的配套量值為基礎。 計算大小會以 Dtu 為單一資料庫和彈性資料庫交易單位（Edtu）來表示。 如需 Dtu 和 Edtu 的詳細資訊，請參閱[什麼是 dtu 和 edtu？](sql-database-purchase-models.md#dtu-based-purchasing-model)。|適用于需要簡單、預先設定之資源選項的客戶。|
|以虛擬核心為基礎的模型|此模型可讓您獨立地選擇計算和儲存體資源。 以虛擬核心為基礎的購買模型也可讓您使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以節省成本。|適合重視彈性、控制力和透明度的客戶。|
||||  

![計價模式比較](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>計算成本

### <a name="provisioned-compute-costs"></a>已布建的計算成本

在布建的計算層中，計算成本會反映為應用程式布建的計算容量總計。

在業務關鍵服務層級中，我們會自動配置至少 3 個複本。 為了反映這項額外的計算資源配置，以 vCore 為基礎的購買模型中的價格比一般用途服務層級更高的商務關鍵服務層級中的 2.7 x 以上。 同樣地，商務關鍵服務層級中的每 GB 儲存體價格也會反映較高的 IO 限制和較低的 SSD 儲存體延遲。

對於業務關鍵服務層級和一般用途服務層級而言，備份儲存體的成本相同，因為這兩層都使用標準儲存體來進行備份。

### <a name="serverless-compute-costs"></a>無伺服器計算成本

如需如何定義計算容量和計算無伺服器計算層成本的說明，請參閱[SQL Database 無伺服器](sql-database-serverless.md)。

## <a name="storage-costs"></a>儲存成本

不同類型的儲存體會以不同方式計費。 對於資料儲存體，會根據您選取的資料庫或集區大小上限來支付已布建儲存體的費用。 除非您減少或增加該上限，否則成本不會變更。 備份儲存體與您執行個體的自動備份相關聯，而且是動態配置的。 增加您的備份保留期限，會增加您的實例所耗用的備份儲存體。

根據預設，系統會將您資料庫的7天自動備份複製到讀取權限異地多餘儲存體（RA-GRS）標準 Blob 儲存體帳戶。 每週完整備份、每日差異備份，以及每5分鐘複製一次的交易記錄備份都使用此儲存體。 交易記錄檔的大小取決於資料庫的變更率。 最小儲存體金額等於100% 的資料庫大小，免費提供。 備份儲存體的額外使用量，會按月以 GB 為單位收費。

如需儲存體價格的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/sql-database/single/)頁面。

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

虛擬核心（vCore）代表邏輯 CPU，並可讓您選擇硬體的世代和硬體的實體特性（例如核心數目、記憶體和儲存體大小）。 VCore 為基礎的購買模型可讓您彈性、控制、個別資源耗用量的透明度，以及直接將內部部署工作負載需求轉譯到雲端的方式。 此模型可讓您根據您的工作負載需求，選擇計算、記憶體和儲存體資源。

在 vCore 為基礎的購買模型中，您可以選擇[單一資料庫](sql-database-single-database-scale.md)、[彈性](sql-database-elastic-pool.md)集區和[受控實例](sql-database-managed-instance.md)的[一般用途](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)和[商務關鍵](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)服務層級。 針對單一資料庫，您也可以選擇[超大規模資料庫服務層級](sql-database-service-tier-hyperscale.md)。

VCore 為基礎的購買模型可讓您獨立選擇計算和儲存體資源、符合內部部署效能，並將價格優化。 在 vCore 為基礎的購買模型中，您需支付下列費用：

- 計算資源（服務層級 + 虛擬核心數目和記憶體數量 + 硬體世代）。
- 資料和記錄儲存體的類型和數量。
- 備份儲存體（RA-GRS）。

> [!IMPORTANT]
> 計算資源、i/o 和資料和記錄儲存體會依資料庫或彈性集區收費。 備份儲存體是依每個資料庫收費。 如需受控執行個體費用的詳細資訊，請參閱[受控執行個體](sql-database-managed-instance.md)。
> **區域限制：** 如需目前支援的區域清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 若要在目前不支援的區域中建立受控實例，請透過[Azure 入口網站傳送支援要求](quota-increase-request.md)。

如果您的單一資料庫或彈性集區耗用300個以上的 Dtu，轉換成以 vCore 為基礎的購買模型可能會降低成本。 您可以使用您選擇的 API 或使用 Azure 入口網站，而不需要停機來進行轉換。 不過，不需要轉換，也不會自動完成。 如果以 DTU 為基礎的購買模型符合您的效能和商務需求，請繼續使用即可。

若要從以 DTU 為基礎的購買模型轉換成 vCore 為基礎的購買模型，請使用下列經驗法則選取計算大小：

- 標準層中的每個 100 Dtu 在一般用途服務層級中至少需要1個 vCore。
- 進階層中的每個 125 Dtu 在業務關鍵服務層中至少需要1個 vCore。

## <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

資料庫交易單位（DTU）代表 CPU、記憶體、讀取和寫入的混合量值。 以 DTU 為基礎的購買模型會提供一組預先設定好的計算資源組合和所包含的儲存體，以期達成不同的應用程式效能等級。 如果您偏好預先設定的配套和每月固定款項的簡單性，以 DTU 為基礎的模型可能更適合您的需求。

在以 DTU 為基礎的購買模型中，您可以為[單一資料庫](sql-database-single-database-scale.md)和[彈性](sql-database-elastic-pool.md)集區選擇基本、標準和 premium 服務層。 以 DTU 為基礎的購買模型不適用於[受控實例](sql-database-managed-instance.md)。

### <a name="database-transaction-units-dtus"></a>資料庫交易單位 (DTU)

針對[服務層](sql-database-single-database-scale.md)中特定計算大小的單一資料庫，Microsoft 保證該資料庫的特定資源層級（與 Azure 雲端中的任何其他資料庫無關）。 這項保證可提供可預測的效能層級。 配置給資料庫的資源數量是以 Dtu 數計算，而且是計算、儲存體和 i/o 資源的配套量值。

這些資源的比率，原本是由專為實際 OLTP 工作負載一般的[線上交易處理（OLTP）基準測試工作負載](sql-database-benchmark-overview.md)所決定。 當您的工作負載超過其中任何一項資源的數量時，您的輸送量會進行節流處理，因而導致效能和時間更慢。

您的工作負載所使用的資源，不會影響 Azure 雲端中其他 SQL 資料庫可用的資源。 同樣地，其他工作負載所使用的資源也不會影響您的 SQL database 可用的資源。

![週框方塊](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu 最適合用於瞭解針對不同計算大小和服務層級的 Azure SQL 資料庫所配置的相對資源。 例如，

- 藉由增加資料庫的計算大小來使 Dtu 加倍，等於讓該資料庫可用的資源集合加倍。
- 具有 1750 Dtu 的高階服務層級 P11 資料庫，比具有5個 Dtu 的基本服務層資料庫，提供350x 更多 DTU 計算能力。  

若要深入瞭解您工作負載的資源（DTU）耗用量，請使用[查詢效能深入](sql-database-query-performance.md)解析來執行下列動作：

- 依 CPU/持續時間/執行計數來識別最前面的查詢，可能會針對改善的效能進行調整。 例如，需要大量 i/o 的查詢可受益于[記憶體中的優化技術](sql-database-in-memory.md)，以便更充分利用特定服務層級和計算大小的可用記憶體。
- 向下切入查詢的詳細資料，以查看其文字及其資源使用方式的歷程記錄。
- 存取效能微調建議，以顯示[SQL Database Advisor](sql-database-advisor.md)所採取的動作。

### <a name="elastic-database-transaction-units-edtus"></a>彈性資料庫交易單位（Edtu）

對於永遠可用的 SQL 資料庫，而不是提供可能不一定需要的專屬資源集（Dtu），您可以將這些資料庫放入[彈性](sql-database-elastic-pool.md)集區中。 彈性集區中的資料庫位於單一 Azure SQL Database 伺服器上，並共用資源集區。

彈性集區中的共用資源是以彈性資料庫交易單位（Edtu）來測量。 彈性集區提供簡單、符合成本效益的解決方案，以管理多個資料庫的效能目標，這些資料庫的使用模式有很大的不同且無法預測。 彈性集區可確保集區中的一個資料庫無法取用所有資源，同時確保集區中的每個資料庫一律具有所需的最低資源數量。

集區以固定價格提供固定數目的 eDTU。 在彈性集區中，個別資料庫可以在設定的界限內自動調整。 較繁重的負載下的資料庫會耗用更多 Edtu 以滿足需求。 較輕量負載下的資料庫會耗用較少的 Edtu。 沒有負載的資料庫不會取用 eDTU。 因為資源是針對整個集區所布建，而不是針對每個資料庫，所以彈性集區會簡化您的管理工作，並為集區提供可預測的預算。

您可以將其他 Edtu 新增至現有的集區，而不會造成資料庫停機，而且不會影響集區中的資料庫。 同樣地，如果您不再需要額外的 Edtu，請隨時從現有的集區中移除它們。 您也可以隨時在集區中新增或減少資料庫。 若要保留其他資料庫的 Edtu，請限制資料庫在負載過重時可以使用的 Edtu 數目。 如果資料庫一致地 underuses 資源，請將它移出集區，並將它設定為具有可預測的必要資源量的單一資料庫。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>判斷工作負載所需的 DTU 數目

如果您想要將現有的內部部署或 SQL Server 的虛擬機器工作負載遷移至 Azure SQL Database，請使用[dtu 計算機](https://dtucalculator.azurewebsites.net/)來估計所需的 dtu 數目。 針對現有的 Azure SQL Database 工作負載，請使用[查詢效能深入](sql-database-query-performance.md)解析來瞭解您的資料庫資源耗用量（dtu），並取得優化工作負載的更深入見解。 [Dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)動態管理檢視（DMV）可讓您查看過去一小時的資源耗用量。 [ [Resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)目錄] 視圖會顯示過去14天的資源耗用量，但以較低的精確度為五分鐘的平均值。

### <a name="determine-dtu-utilization"></a>判斷 DTU 使用率

若要判斷 DTU/eDTU 使用率相對於資料庫或彈性集區之 DTU/eDTU 限制的平均百分比，請使用下列公式：

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

此公式的輸入值可以從[sys.databases 取得 dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、 [sys.databases resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)和[sys.databases。 elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) dmv。 換句話說，若要針對資料庫或彈性集區的 DTU/eDTU 限制，判斷 DTU/eDTU 使用率的百分比，請從下列位置挑選最大的百分比值： `avg_cpu_percent`、`avg_data_io_percent`和 `avg_log_write_percent` 在指定的時間點。

> [!NOTE]
> 資料庫的 DTU 限制是由資料庫可用的 CPU、讀取、寫入和記憶體所決定。 不過，由於 SQL Server 資料庫引擎通常會使用其資料快取的所有可用記憶體來改善效能，因此不論目前的資料庫負載為何，`avg_memory_usage_percent` 值通常會接近100%。 因此，即使記憶體間接影響 DTU 限制，也不會用於 DTU 使用率公式中。
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>能受益於彈性資源集區的工作負載

集區非常適用于資源使用率較低的資料庫，而且使用量尖峰較少。 如需詳細資訊，請參閱[何時應該考慮 SQL Database 彈性集區？](sql-database-elastic-pool.md)。

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型中的硬體世代

在以 DTU 為基礎的購買模型中，客戶無法選擇用於其資料庫的硬體世代。 雖然指定的資料庫在一段很長的時間（通常是多個月）仍會持續出現在特定的硬體上，但有某些事件可能會導致資料庫移至另一個硬體世代。

例如，如果資料庫相應增加或減少至不同的服務目標，或是資料中心內的目前基礎結構已接近其容量限制，或目前使用的硬體正在進行中，則可以將它移至不同的硬體世代。因其生命週期結束而解除委任。

如果將資料庫移至不同的硬體，工作負載效能可能會變更。 DTU 模型保證[dtu 基準測試](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark)工作負載的輸送量和回應時間，在資料庫移至不同的硬體世代時，會維持明顯相同，只要其服務目標（dtu 數目）維持不變即可。 

不過，在 Azure SQL Database 中執行的大量客戶工作負載中，對相同服務目標使用不同硬體的影響可能更明顯。 不同的工作負載將受益于不同的硬體設定和功能。 因此，對於 DTU 基準測試以外的工作負載，如果資料庫從一個硬體世代移到另一個，則可能會看到效能差異。

例如，對於網路延遲敏感的應用程式，在第5代硬體與第4代上的效能可能會因為使用第5代中的加速網路而看到較佳的效能，但使用密集讀取 IO 的應用程式可能會在第4代硬體與第5代上看到較佳的效能，因為第4代上每個核心比例的記憶體越高。

若客戶的工作負載與硬體變更相關，或想要控制其資料庫的硬體世代選擇的客戶，則可以使用[vCore](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore)模型，在資料庫建立和調整期間選擇其慣用的硬體世代。 在 vCore 模型中，會針對[單一資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)和[彈性](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)集區記錄每個硬體產生的每個服務目標的資源限制。 如需 vCore 模型中硬體世代的詳細資訊，請參閱[硬體](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations)世代。

## <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>我是否需要讓應用程式離線，才能從以 DTU 為基礎的服務層級轉換成以 vCore 為基礎的服務層級？

No。 您不需要讓應用程式離線。 新的服務層級提供簡單的線上轉換方法，類似于將資料庫從標準升級至高階服務層的現有程式，還有另一種方式。 您可以使用 [Azure 入口網站]、[PowerShell]、[Azure CLI]、[T-sql] 或 REST API 來開始這項轉換。 請參閱[管理單一資料庫](sql-database-single-database-scale.md)和[管理彈性集區](sql-database-elastic-pool.md)。

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>是否可以將資料庫從 vCore 為基礎的購買模型中的服務層級，轉換為以 DTU 為基礎的購買模型中的服務層級？

是，您可以使用 Azure 入口網站、PowerShell、Azure CLI、T-sql 或 REST API，輕鬆地將資料庫轉換為任何支援的效能目標。 請參閱[管理單一資料庫](sql-database-single-database-scale.md)和[管理彈性集區](sql-database-elastic-pool.md)。

## <a name="next-steps"></a>後續步驟

- 如需 vCore 為基礎之購買模型的詳細資訊，請參閱[vCore 為基礎的購買模型](sql-database-service-tiers-vcore.md)。
- 如需以 DTU 為基礎的購買模型的詳細資訊，請參閱以[dtu 為基礎的購買模型](sql-database-service-tiers-dtu.md)。
