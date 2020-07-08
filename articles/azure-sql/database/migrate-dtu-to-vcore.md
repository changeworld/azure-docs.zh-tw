---
title: 從 DTU 遷移至虛擬核心
description: 將 Azure SQL Database 中的資料庫從 DTU 模型遷移到 vCore 模型。 遷移至 vCore 類似于 standard 和 premium 層之間的升級或降級。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 05/28/2020
ms.openlocfilehash: 0193e7f7001fb8f63794a379c4d2b8e28abd5c0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85297863"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>將 Azure SQL Database 從以 DTU 為基礎的模型遷移到 vCore 為基礎的模型
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文說明如何將您的資料庫從以[DTU 為基礎的購買模型](service-tiers-dtu.md)Azure SQL Database 遷移到[vCore 為基礎的購買模型](service-tiers-vcore.md)。 

## <a name="migrate-a-database"></a>移轉資料庫

將資料庫從以 DTU 為基礎的購買模型遷移到 vCore 為基礎的購買模型，類似于在「基本」、「標準」和「高階」服務層級的服務目標之間進行調整，其[持續時間](single-database-scale.md#latency)和在遷移程式結束時的[停機時間最短](scale-resources.md)。 遷移至以 vCore 為基礎的購買模型的資料庫，可以隨時以相同的方式遷移回以 DTU 為基礎的購買模型，但將資料庫移轉至[超大規模資料庫](service-tier-hyperscale.md)服務層級並不例外。 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>選擇 vCore 服務層級和服務目標

針對大部分 DTU 來 vCore 遷移案例，基本和標準服務層級中的資料庫和彈性集區會對應至[一般用途](service-tier-general-purpose.md)服務層級。 Premium 服務層中的資料庫和彈性集區會對應到[商務關鍵性](service-tier-business-critical.md)服務層。 視應用程式案例和需求而定，[超大規模資料庫](service-tier-hyperscale.md)服務層級通常可作為所有 DTU 服務層級中單一資料庫的遷移目標。

若要針對 vCore 模型中遷移的資料庫選擇服務目標或計算大小，您可以使用簡單但近似的經驗法則：基本或標準層中的每個 100 Dtu*至少*需要 1 vCore，而進階層中的每個 125 dtu*至少需要 1* vCore。 

> [!TIP]
> 此規則是近似的，因為它不會考慮用於 DTU 資料庫或彈性集區的硬體世代。 

在 DTU 模型中，所有可用的[硬體世代](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model)都可以用於您的資料庫或彈性集區。 此外，您只能透過選擇較高或較低的 DTU 或 eDTU 值，間接控制虛擬核心（邏輯 Cpu）的數目。 

使用 vCore 模型時，客戶必須明確選擇硬體世代和虛擬核心（邏輯 Cpu）數目。 DTU 模型並不提供這些選擇，不過，硬體世代和用於每個資料庫和彈性集區的邏輯 Cpu 數目，都是透過動態管理檢視來公開。 這讓您能夠更精確地判斷相符的 vCore 服務目標。 

下列方法會使用這項資訊，以類似的資源配置來判斷 vCore 服務目標，以在遷移至 vCore 模型之後取得類似的效能層級。

### <a name="dtu-to-vcore-mapping"></a>要 vCore 的 DTU 對應

下面的 T-SQL 查詢在要遷移的 DTU 資料庫內容中執行時，會在 vCore 模型中的每個硬體世代中傳回相符（可能是分數）的虛擬核心數目。 藉由將此數位四捨五入到 vCore 模型中每個硬體世代中可用於[資料庫](resource-limits-vcore-single-databases.md)和[彈性](resource-limits-vcore-elastic-pools.md)集區的最接近虛擬核心數目，客戶可以選擇最符合其 DTU 資料庫或彈性集區的 vCore 服務目標。 

[範例](#dtu-to-vcore-migration-examples)一節會說明使用這種方法的範例遷移案例。

在要遷移的資料庫內容中執行此查詢，而不是在資料庫中執行 `master` 。 遷移彈性集區時，請在集區中任何資料庫的內容中執行查詢。

```SQL
WITH dtu_vcore_map AS
(
SELECT TOP (1) rg.slo_name,
               CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
                    WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
               END AS dtu_hardware_gen,
               s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
               CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>其他因素

除了虛擬核心（邏輯 Cpu）數目和硬體世代以外，還有其他幾個因素可能會影響 vCore 服務目標的選擇：

- 對應 T-SQL 查詢符合其 CPU 容量的 DTU 和 vCore 服務目標，因此，針對 CPU 系結的工作負載，其結果會更精確。
- 針對相同的硬體產生和相同數目的虛擬核心，vCore 資料庫的 IOPS 和交易記錄輸送量資源限制通常會高於 DTU 資料庫。 對於 IO 系結的工作負載，可能會降低 vCore 模型中的虛擬核心數目，以達到相同的效能層級。 以絕對值表示的 DTU 和 vCore 資料庫的資源限制會在[sys.databases dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)視圖中公開。 比較要遷移的 DTU 資料庫與使用大約相符服務目標的 vCore 資料庫之間的這些值，將可協助您更精確地選取 vCore 服務目標。
- 對應查詢也會針對要遷移的 DTU 資料庫或彈性集區，以及 vCore 模型中的每個硬體產生，傳回每個核心的記憶體數量。 在遷移至 vCore 之後，若要確保記憶體的總計，對於需要大型記憶體資料快取以達到足夠效能的工作負載，或需要大型記憶體授與查詢處理的工作負載而言，是很重要的。 針對這類工作負載，視實際效能而定，可能需要增加虛擬核心的數目，以取得足夠的總記憶體。
- 選擇 vCore 服務目標時，應該考慮 DTU 資料庫的歷程[記錄資源使用量](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)。 具有一致使用量過低之 CPU 資源的 DTU 資料庫，可能需要比對應查詢所傳回的數目少虛擬核心。 相反地，如果 DTU 資料庫的 CPU 使用率過高導致工作負載效能不足，可能需要比查詢傳回的虛擬核心更多。
- 如果遷移具有間歇或無法預測之使用模式的資料庫，請考慮使用[無伺服器](serverless-tier-overview.md)計算層。  請注意，無伺服器中並行背景工作角色（要求）的最大數目為75% 已布建的計算中已設定的最大虛擬核心數目限制。  此外，無伺服器可用的記憶體上限是已設定之虛擬核心數目上限的 3 GB 倍;例如，當設定 40 max 虛擬核心時，最大記憶體是 120 GB。   
- 在 vCore 模型中，支援的資料庫大小上限可能會隨著硬體產生而有所不同。 針對大型資料庫，請檢查[單一資料庫](resource-limits-vcore-single-databases.md)和[彈性](resource-limits-vcore-elastic-pools.md)集區的 vCore 模型中支援的大小上限。
- 針對彈性集區， [DTU](resource-limits-dtu-elastic-pools.md)和[vCore](resource-limits-vcore-elastic-pools.md)模型在每個集區支援的資料庫數目上限各有差異。 遷移具有許多資料庫的彈性集區時，應該考慮這一點。
- 某些硬體世代可能無法在每個區域中使用。 檢查硬體層[代](service-tiers-vcore.md#hardware-generations)下的可用性。

> [!IMPORTANT]
> 系統會提供上述 vCore 大小調整指導方針的 DTU，以協助進行目標資料庫服務目標的初始估計。
>
> 目標資料庫的最佳設定與工作負載相關。 因此，在遷移後達到最佳的價格/效能比例可能需要利用 vCore 模型的彈性來調整虛擬核心數目、[硬體世代](service-tiers-vcore.md#hardware-generations)、[服務](service-tiers-vcore.md#service-tiers)和[計算](service-tiers-vcore.md#compute-tiers)層，以及微調其他資料庫設定參數，例如平行處理原則[的最大程度](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)。
> 

### <a name="dtu-to-vcore-migration-examples"></a>VCore 遷移的 DTU 範例

> [!NOTE]
> 以下範例中的值僅供說明之用。 在描述的案例中傳回的實際值可能會不同。
> 

**遷移標準 S9 資料庫**

對應查詢會傳回下列結果（為了簡潔起見，有些資料行未顯示）：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24.00|Gen5|5.40|16.800|7|24.000|5.05|

我們看到 DTU 資料庫有24個邏輯 Cpu （虛擬核心），每個 vCore 5.4 GB 的記憶體，且使用第5代硬體。 這是在第5代硬體上的一般用途 24 vCore 資料庫，也就是**GP_Gen5_24** vCore 服務目標的直接符合。

**遷移標準 S0 資料庫**

對應查詢會傳回下列結果（為了簡潔起見，有些資料行未顯示）：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0.25|Gen4|0.42|0.250|7|0.425|5.05|

我們看到 DTU 資料庫具有相當於0.25 邏輯 Cpu （虛擬核心），每個 vCore 有 0.42 GB 的記憶體，並使用第4代硬體。 第4代和第5代硬體世代中最小的 vCore 服務目標、 **GP_Gen4_1**和**GP_Gen5_2**提供比標準 S0 資料庫更多的計算資源，因此不可能直接比對。 由於第4代硬體已[解除](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)委任，因此偏好**GP_Gen5_2**選項。 此外，如果工作負載適用于[無伺服器](serverless-tier-overview.md)計算層級，則**GP_S_Gen5_1**會比較接近。

**遷移 Premium P15 資料庫**

對應查詢會傳回下列結果（為了簡潔起見，有些資料行未顯示）：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42.00|Gen5|4.86|29.400|7|42.000|5.05|

我們看到 DTU 資料庫具有42個邏輯 Cpu （虛擬核心），每個 vCore 有 4.86 GB 的記憶體，且使用第5代硬體。 雖然沒有42核心的 vCore 服務目標，但**BC_Gen5_40**的服務目標在 CPU 和記憶體容量方面非常接近，而且是一項很好的比對。

**遷移基本 200 eDTU 彈性集區**

對應查詢會傳回下列結果（為了簡潔起見，有些資料行未顯示）：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4.00|Gen5|5.40|2.800|7|4.000|5.05|

我們看到 DTU 彈性集區有4個邏輯 Cpu （虛擬核心），每個 vCore 5.4 GB 的記憶體，並使用第5代硬體。 VCore 模型中的直接相符是**GP_Gen5_4**彈性集區。 不過，此服務目標支援每個集區最多200個資料庫，而基本 200 eDTU 彈性集區支援最多500個資料庫。 如果要遷移的彈性集區具有超過200個資料庫，則必須**GP_Gen5_6**相符的 vCore 服務目標，以支援最多500個資料庫。

## <a name="migrate-geo-replicated-databases"></a>遷移異地複寫的資料庫

從以 DTU 為基礎的模型遷移到 vCore 為基礎的購買模型，類似于在 standard 和 premium 服務層級中的資料庫之間升級或降級異地複寫關聯性。 在遷移期間，您不需要停止異地複寫，但必須遵循下列排序規則：

- 升級時，您必須先升級次要資料庫，然後再升級主要資料庫。
- 降級時，順序相反︰您必須先降級主要資料庫，然後再降級次要資料庫。

當您在兩個彈性集區之間使用異地複寫時，建議您將一個集區指定為主要複本，並將另一個做為次要資料庫。 在此情況下，當您要遷移彈性集區時，您應該使用相同的排序指引。 不過，如果您有同時包含主要和次要資料庫的彈性集區，請將具有較高使用率的集區視為主要複本，並據以遵循順序規則。  

下表提供特定遷移案例的指引：

|目前的服務層級|目標服務層級|遷移類型|使用者動作|
|---|---|---|---|
|標準|一般用途|橫向|可以依任何順序遷移，但必須確保適當的 vCore 大小，如上所述|
|Premium|業務關鍵|橫向|可以依任何順序遷移，但必須確保適當的 vCore 大小，如上所述|
|標準|業務關鍵|升級|必須先遷移次要|
|業務關鍵|標準|降級|必須先遷移主要|
|Premium|一般用途|降級|必須先遷移主要|
|一般用途|Premium|升級|必須先遷移次要|
|業務關鍵|一般用途|降級|必須先遷移主要|
|一般用途|業務關鍵|升級|必須先遷移次要|
||||

## <a name="migrate-failover-groups"></a>遷移容錯移轉群組

在遷移具有多個資料庫的容錯移轉群組時，必須個別遷移主要和次要資料庫。 過程中適用相同的考量和排序規則。 將資料庫轉換成 vCore 為基礎的購買模型之後，容錯移轉群組將會使用相同的原則設定繼續作用。

### <a name="create-a-geo-replication-secondary-database"></a>建立異地複寫次要資料庫

您只能使用與主資料庫相同的服務層級，來建立異地複寫次要資料庫（地理位置）。 對於記錄產生率較高的資料庫，我們建議使用與主要複本相同的計算大小來建立異地次要資料庫。

如果您要在彈性集區中為單一主資料庫建立異地次要資料庫，請確定集區的 `maxVCore` 設定符合主資料庫的計算大小。 如果您要為另一個彈性集區中的主要複本建立異地次要資料庫，建議您讓集區具有相同的 `maxVCore` 設定。

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>使用資料庫複製從 DTU 遷移至 vCore

您可以將任何資料庫 (具有以 DTU 為基礎的計算大小) 複製到資料庫 (具有以虛擬核心為基礎的計算大小)，而不會有任何限制或特殊排序，但前提是目標計算大小支援來源資料庫的最大資料庫大小。 資料庫複製會在複製作業開始時建立資料的快照集，而不會同步處理來源與目標之間的資料。

## <a name="next-steps"></a>後續步驟

- 如需單一資料庫可用的特定計算大小和儲存體大小選項，請參閱[SQL Database 單一資料庫的 vCore 為基礎的資源限制](resource-limits-vcore-single-databases.md)。
- 如需彈性集區可用的特定計算大小和儲存體大小選項，請參閱彈性集區[SQL Database vCore 為基礎的資源限制](resource-limits-vcore-elastic-pools.md)。
