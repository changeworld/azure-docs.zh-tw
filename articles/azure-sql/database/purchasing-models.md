---
title: 購買模型
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 瞭解適用于 Azure SQL Database 和 Azure SQL 受控執行個體的購買模型。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: a5760d3daaa13a5ed16230e1ffb7fe3691455e09
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427033"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>選擇 vCore 和 DTU 購買模型-Azure SQL Database 和 SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 和 Azure SQL 受控執行個體可讓您輕鬆地購買完全受控的平臺即服務 (PaaS) Database engine，以符合您的效能和成本需求。 根據您為 Azure SQL Database 選擇的部署模型，您可以選取適合您的購買模型：

- [虛擬核心 (vCore) 為基礎的購買模型](service-tiers-vcore.md) (建議的) 。 此購買模型可讓您選擇布建的計算層和無伺服器計算層。 在布建的計算層中，您可以選擇一律針對您的工作負載布建的確切計算資源量。 使用無伺服器計算層級，您可以透過可設定的計算範圍來指定計算資源的自動調整。 使用此計算層，您也可以根據工作負載活動自動暫停和繼續資料庫。 布建的計算層級中，每個時間單位的 vCore 單位價格低於無伺服器計算層級。
- [資料庫交易單位 (DTU) 為基礎的購買模型](service-tiers-dtu.md)。 此購買模型提供適用于一般工作負載的配套計算和儲存體套件。

有兩種購買模型：

- [vCore 為基礎的購買模型](service-tiers-vcore.md) 適用于 [AZURE SQL DATABASE](sql-database-paas-overview.md) 和 [Azure SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)。 [超大規模服務層級](service-tier-hyperscale.md)適用于使用以[vCore 為基礎的購買模型](service-tiers-vcore.md)的單一資料庫。
- 以[DTU 為基礎的購買模型](service-tiers-dtu.md)適用于[Azure SQL Database](single-database-manage.md)。

下表和圖表比較和對比 vCore 型和以 DTU 為基礎的購買模型：

|**購買模型**|**說明**|**適用對象**|
|---|---|---|
|以 DTU 為基礎|此模型是以計算、儲存體和 i/o 資源的配套量值為基礎。 DTU 的計算大小會以資料庫交易單位 (DTU) 表示，而彈性集區的計算大小則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 與 eDTU 的相關資訊，請參閱 [什麼是 DTU 與 eDTU？](purchasing-models.md#dtu-based-purchasing-model)。|需要簡單、預先設定之資源選項的客戶|
|以 vCore 為基礎|此模型可讓您獨立地選擇計算和儲存體資源。 以虛擬核心為基礎的購買模型也可讓您使用適用於 SQL Server 的 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以節省成本。|彈性、控制和透明度的價值客戶|
||||  

![定價模型比較](./media/purchasing-models/pricing-model.png)

想要最佳化並節省您的雲端費用嗎？

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>計算成本

### <a name="provisioned-compute-costs"></a>布建的計算成本

在布建的計算層中，計算成本會反映為應用程式布建的計算容量總計。

在業務關鍵服務層級中，我們會自動設定至少三個複本。 為了反映這項額外的計算資源配置，以 vCore 為基礎的購買模型中的價格大約比一般用途服務層級的商務關鍵服務層級中的2.7 倍高。 同樣地，商務關鍵服務層級中的每 GB 儲存體價格越高，也會反映出更高的 IO 限制和 SSD 儲存體的較低延遲。

對於業務關鍵服務層級和一般用途服務層級，備份儲存體的成本相同，因為這兩個層級會使用標準儲存體進行備份。

### <a name="serverless-compute-costs"></a>無伺服器計算成本

如需計算容量的定義方式，以及如何計算無伺服器計算層費用的說明，請參閱 [SQL Database 無伺服器層](serverless-tier-overview.md)。

## <a name="storage-costs"></a>儲存體成本

不同類型的儲存體會以不同方式計費。 對於資料儲存體，您必須根據所選的資料庫或集區大小上限，為布建的儲存體支付費用。 除非您減少或增加該上限，否則成本不會變更。 備份儲存體與您執行個體的自動備份相關聯，而且是動態配置的。 增加您的備份保留期限，可增加實例所耗用的備份儲存體。

根據預設，系統會將您資料庫的7天自動備份複製到讀取權限異地冗余儲存體， (GRS) 標準 Blob 儲存體帳戶。 每週完整備份、每日差異備份，以及每隔五分鐘複製一次的交易記錄備份都使用此儲存體。 交易記錄檔的大小取決於資料庫的變動率。 提供的最小儲存體數量等於資料庫大小的100%，不需額外費用。 備份儲存體的額外使用量，會按月以 GB 為單位收費。

如需儲存體價格的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/sql-database/single/)頁面。

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

虛擬核心 (vCore) 代表邏輯 CPU，並可讓您選擇硬體世代和 (硬體的實體特性，例如，核心數目、記憶體和儲存體大小) 。 VCore 為基礎的購買模型為您提供彈性、控制、透明的個別資源耗用量，以及將內部部署工作負載需求轉譯至雲端的簡單方式。 此模型可讓您根據您的工作負載需求，選擇計算、記憶體和儲存體資源。

在以 vCore 為基礎的購買模型中，您可以選擇 SQL Database 和 SQL 受控執行個體的 [一般用途](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 和 [業務關鍵](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) 服務層級。  若為單一資料庫，您也可以選擇 [超大規模服務層級](service-tier-hyperscale.md)。

VCore 為基礎的購買模型可讓您獨立地選擇計算和儲存體資源、符合內部部署效能，並將價格優化。 在以 vCore 為基礎的購買模型中，您需要支付：

- 計算資源 (服務層級 + 虛擬核心數目和記憶體數量 + 硬體世代)。
- 資料和記錄儲存體的類型和數量。
- 備份儲存體 (RA-GRS) 。

> [!IMPORTANT]
> 計算資源、i/o 和資料和記錄儲存體都會依資料庫或彈性集區收費。 備份儲存體是依每個資料庫計費。 如需 SQL 受控執行個體費用的詳細資訊，請參閱 [sql 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)。
> **區域限制：** 如需目前支援的區域清單，請參閱 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 若要在目前不支援的區域中建立受控實例，請透過 [Azure 入口網站傳送支援要求](quota-increase-request.md)。

如果您的資料庫耗用超過 300 Dtu，則轉換成 vCore 為基礎的購買模型可能會降低您的成本。 您可以使用您選擇的 API 或使用 Azure 入口網站來進行轉換，而不需要停機。 不過，轉換並不是必要的，而且不會自動完成。 如果以 DTU 為基礎的購買模型符合您的效能和商務需求，請繼續使用即可。

若要從以 DTU 為基礎的購買模型轉換為 vCore 為基礎的購買模型，請參閱 [從 Dtu 遷移至 vCore](migrate-dtu-to-vcore.md)。

## <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

資料庫交易單位 (DTU) 代表 CPU、記憶體、讀取和寫入的混合量值。 以 DTU 為基礎的購買模型會提供一組預先設定好的計算資源組合和所包含的儲存體，以期達成不同的應用程式效能等級。 如果您偏好預先設定的組合和每個月的固定付款，以 DTU 為基礎的模型可能更適合您的需求。

在以 DTU 為基礎的購買模型中，您可以選擇 Azure SQL Database 的基本、標準和 premium 服務層。 以 DTU 為基礎的購買模型不適用於 Azure SQL 受控執行個體。

### <a name="database-transaction-units-dtus"></a>資料庫交易單位 (DTU)

針對 [服務層級](single-database-scale.md)中特定計算大小的單一資料庫，azure 保證該資料庫的特定資源層級 (與 Azure 雲端) 中的其他任何資料庫無關。 這項保證可提供可預測的效能等級。 針對資料庫所配置的資源數量會計算為 Dtu 數目，並且是計算、儲存體和 i/o 資源的配套量值。

這些資源的比率，原本是由 [線上交易處理所決定， (OLTP) 效能評定工作負載](service-tiers-dtu.md) ，其設計目的是真實世界的 oltp 工作負載。 當您的工作負載超過上述任何資源的數量時，您的輸送量會受到節流處理，導致效能變慢和超時時間。

您的工作負載所使用的資源，不會影響 Azure 雲端中其他資料庫可用的資源。 同樣地，其他工作負載所使用的資源也不會影響您資料庫可用的資源。

![週框方塊](./media/purchasing-models/bounding-box.png)

Dtu 最適合用來瞭解針對不同計算大小和服務層級的資料庫所配置的相對資源。 例如：

- 藉由增加資料庫的計算大小來使 Dtu 加倍，等於讓該資料庫可用的資源集合加倍。
- 具有 1750 Dtu 的高階服務層 P11 資料庫提供350倍以上的 DTU 計算能力，而非具有5個 Dtu 的基本服務層資料庫。  

若要深入瞭解資源 (DTU) 取用工作負載，請使用 [查詢效能深入](query-performance-insight-use.md) 解析來：

- 依據 CPU/持續時間/執行計數，找出可調整以改善效能的排名最前面的查詢。 例如，需要大量 i/o 的查詢可能會受益于 [記憶體中的優化技術](../in-memory-oltp-overview.md) ，以便在特定服務層級和計算大小更妥善地使用可用的記憶體。
- 向下切入查詢的詳細資料，以查看其文字和其資源使用量歷程記錄。
- 存取效能微調建議，以顯示 [SQL Database Advisor](database-advisor-implement-performance-recommendations.md)所採取的動作。

### <a name="elastic-database-transaction-units-edtus"></a>彈性資料庫交易單位 (Edtu) 

針對永遠可用的資料庫，而不是提供一組專用的資源 (Dtu) 可能不一定需要，您可以將這些資料庫放在 [彈性集](elastic-pool-overview.md)區中。 彈性集區中的資料庫位於單一伺服器上，並共用資源集區。

彈性集區中的共用資源，是以彈性資料庫交易單位 (Edtu) 來測量。 彈性集區提供簡單且符合成本效益的解決方案，以管理多個資料庫的效能目標，而這些資料庫具有廣泛變化且無法預測的使用模式。 彈性集區可確保集區中的一個資料庫無法取用所有資源，同時確保集區中的每個資料庫一律具有最少量的必要資源可用。

集區以固定價格提供固定數目的 eDTU。 在彈性集區中，個別資料庫可以在設定的界限內自動調整。 較繁重負載下的資料庫將會耗用更多 Edtu 來滿足需求。 較輕量負載下的資料庫會耗用較少的 Edtu。 沒有負載的資料庫不會取用 eDTU。 由於系統會針對整個集區布建資源，而不是針對每個資料庫布建，因此彈性集區會簡化您的管理工作，並提供可預測的集區預算。

您可以將其他 Edtu 新增至現有集區，而不會造成資料庫停機，且不會影響集區中的資料庫。 同樣地，如果您不再需要額外的 Edtu，請隨時從現有的集區中移除它們。 您也可以隨時在集區中新增或減少資料庫。 若要保留其他資料庫的 Edtu，請限制資料庫在繁重負載下可使用的 Edtu 數目。 如果資料庫一致地 underuses 資源，請將其移出集區，並將它設定為具有可預測的必要資源量的單一資料庫。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>判斷工作負載所需的 DTU 數目

如果您想要將現有的內部部署或 SQL Server 的虛擬機器工作負載遷移到 SQL Database，請使用 [dtu 計算機](https://dtucalculator.azurewebsites.net/) 來估計所需的 dtu 數目。 針對現有的 SQL Database 工作負載，請使用 [查詢效能深入](query-performance-insight-use.md) 解析來瞭解您的資料庫資源耗用量 (dtu) 並取得更深入的見解，以優化您的工作負載。 [Sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)動態管理檢視 (DMV) 可讓您查看過去一小時的資源耗用量。 [ [Sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 目錄] 視圖會顯示過去14天的資源耗用量，但以較低的精確度表示五分鐘的平均值。

### <a name="determine-dtu-utilization"></a>判斷 DTU 使用量

若要判斷 DTU/eDTU 使用量的平均百分比（相對於資料庫或彈性集區的 DTU/eDTU 限制），請使用下列公式：

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

您可以從 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、 [Sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)和 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) dmv 取得此公式的輸入值。 換句話說，若要判斷 DTU/eDTU 使用量對資料庫或彈性集區的 DTU/eDTU 限制的百分比，請從下列位置挑選最大的百分比值： `avg_cpu_percent` 、 `avg_data_io_percent` 以及 `avg_log_write_percent` 在指定的時間點。

> [!NOTE]
> 資料庫的 DTU 限制取決於資料庫可用的 CPU、讀取、寫入和記憶體。 不過，因為 SQL Database 引擎通常會使用所有可用的記憶體來進行資料快取，以改善效能，所以 `avg_memory_usage_percent` 不論目前的資料庫負載為何，此值通常會接近100%。 因此，即使記憶體間接影響 DTU 限制，也不會在 DTU 使用率公式中使用。

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>能受益於彈性資源集區的工作負載

集區非常適合具有低資源使用率平均和相對不頻繁使用率尖峰的資料庫。 如需詳細資訊，請參閱 [何時應該考慮 SQL Database 彈性集區？](elastic-pool-overview.md)。

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型中的硬體世代

在以 DTU 為基礎的購買模型中，客戶無法選擇用於其資料庫的硬體世代。 雖然指定的資料庫通常會維持在特定硬體世代上， (通常會有數個月的時間) ，但有些事件可能會導致資料庫移至另一個硬體世代。

例如，如果資料庫相應增加或減少至不同的服務目標，或是資料中心內目前的基礎結構已接近其容量限制，或目前使用的硬體因為生命週期結束而解除委任，則可以將資料庫移至不同的硬體世代。

如果資料庫移至不同的硬體，工作負載效能可能會變更。 DTU 模型可保證 [dtu](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) 效能評定工作負載的輸送量和回應時間，在資料庫移至不同的硬體世代時，會保持完全相同，只要其服務目標 (dtu 數目) 維持不變。

不過，在 Azure SQL Database 中執行的廣泛客戶工作負載中，針對相同的服務目標使用不同硬體的影響可能更明顯。 不同的工作負載將受益于不同的硬體設定和功能。 因此，針對 DTU 基準測試以外的工作負載，如果資料庫從一個硬體世代移至另一個硬體，就可能會看到效能差異。

例如，對網路延遲敏感的應用程式可能會在第5代硬體與第4代上看到更佳的效能，因為在第5代中使用加速網路，但使用大量讀取 IO 的應用程式在第4代硬體與第5代上可能會看到較佳的效能，因為第4代上的每個核心比例會有較高的記憶體。

如果客戶的工作負載受到硬體變更的影響，或想要控制其資料庫之硬體世代選擇的客戶，則可以使用 [vCore](service-tiers-vcore.md) 模型，在資料庫建立和調整期間選擇其慣用的硬體世代。 在 vCore 模型中，會針對 [單一資料庫](resource-limits-vcore-single-databases.md) 和 [彈性](resource-limits-vcore-elastic-pools.md)集區記錄每個硬體世代上每個服務目標的資源限制。 如需 vCore 模型中硬體世代的詳細資訊，請參閱 [硬體](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations)世代。

## <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>我是否需要讓我的應用程式離線，才能從以 DTU 為基礎的服務層級轉換為以 vCore 為基礎的服務層級？

否。 您不需要讓應用程式離線。 新的服務層級提供簡單的線上轉換方法，類似于將資料庫從標準升級至 premium 服務層的現有程式，以及其他方式。 您可以使用 Azure 入口網站、PowerShell、Azure CLI、T-sql 或 REST API 來開始這項轉換。 請參閱 [管理單一資料庫](single-database-scale.md) 和 [管理彈性](elastic-pool-overview.md)集區。

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>是否可以將資料庫從 vCore 為基礎的購買模型中的服務層，轉換成以 DTU 為基礎的購買模型中的服務層級？

是的，您可以使用 Azure 入口網站、PowerShell、Azure CLI、T-sql 或 REST API，輕鬆地將資料庫轉換為任何支援的效能目標。 請參閱 [管理單一資料庫](single-database-scale.md) 和 [管理彈性](elastic-pool-overview.md)集區。

## <a name="next-steps"></a>後續步驟

- 如需 vCore 為基礎的購買模型的詳細資訊，請參閱以 [vCore 為基礎的購買模型](service-tiers-vcore.md)。
- 如需以 DTU 為基礎的購買模型的詳細資訊，請參閱以 [dtu 為基礎的購買模型](service-tiers-dtu.md)。
