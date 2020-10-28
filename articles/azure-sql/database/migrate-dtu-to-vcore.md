---
title: 從 DTU 遷移至虛擬核心
description: 將 Azure SQL Database 中的資料庫從 DTU 模型遷移至 vCore 模型。 遷移至 vCore 類似于在 standard 和 premium 層之間進行升級或降級。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 05/28/2020
ms.openlocfilehash: aa236ecaaa9c38c68e66d1813280cd98b85b9463
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790384"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>將 Azure SQL Database 從以 DTU 為基礎的模型遷移到以 vCore 為基礎的模型
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文說明如何從以 [DTU 為基礎的購買模型](service-tiers-dtu.md) ，將 Azure SQL Database 中的資料庫移轉到以 [vCore 為基礎的購買模型](service-tiers-vcore.md)。 

## <a name="migrate-a-database"></a>移轉資料庫

將資料庫從以 DTU 為基礎的購買模型遷移到以 vCore 為基礎的購買模型，類似于在「基本」、「標準」和「高階」服務層級中的服務目標之間進行調整，並具有類似的 [持續時間](single-database-scale.md#latency) 和在遷移程式結束時的 [最短停機](scale-resources.md) 時間。 遷移至以 vCore 為基礎的購買模型的資料庫可隨時以同樣的方式遷移回以 DTU 為基礎的購買模型，但遷移至 [超大規模](service-tier-hyperscale.md) 服務層的資料庫除外。 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>選擇 vCore 服務層級和服務目標

針對大部分的 DTU 來 vCore 遷移案例，基本和標準服務層級中的資料庫和彈性集區會對應至 [一般用途](service-tier-general-purpose.md) 服務層級。 Premium 服務層中的資料庫和彈性集區會對應至 [商務關鍵](service-tier-business-critical.md) 服務層級。 視應用程式案例和需求而定， [超大規模](service-tier-hyperscale.md) 服務層級通常可以作為所有 DTU 服務層級中單一資料庫的遷移目標。

若要針對 vCore 模型中已遷移的資料庫選擇服務目標或計算大小，您可以使用簡單但近似的經驗法則：基本或標準層中的每 100 Dtu *至少* 需要 1 vCore，而進階層中的每 125 dtu *至少需要 1* 個 vCore。 

> [!TIP]
> 這是大約的規則，因為它不會考慮 DTU 資料庫或彈性集區所使用的硬體世代。 

在 DTU 模型中，任何可用的 [硬體世代](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) 都可用於您的資料庫或彈性集區。 此外，您只需選擇較高或較低的 DTU 或 eDTU 值，就可以直接對虛擬核心 (邏輯 Cpu 的數目進行間接控制) 。 

使用 vCore 模型時，客戶必須明確地選擇硬體世代以及虛擬核心 (邏輯) Cpu 數目。 DTU 模型不提供這些選擇，不過硬體世代以及用於每個資料庫和彈性集區的邏輯 Cpu 數目，都會透過動態管理檢視來公開。 這讓您能夠更精確地判斷相符的 vCore 服務目標。 

下列方法會使用這項資訊來判斷具有類似資源配置的 vCore 服務目標，以在遷移至 vCore 模型之後取得類似的效能層級。

### <a name="dtu-to-vcore-mapping"></a>DTU 與 vCore 的對應

下面的 T-SQL 查詢，在要遷移的 DTU 資料庫內容中執行時，將會傳回相符的 (可能會在 vCore 模型中的每個硬體產生中) 數目的虛擬核心。 藉由將此數位四捨五入到 vCore 模型中每個硬體產生之 [資料庫](resource-limits-vcore-single-databases.md) 和 [彈性](resource-limits-vcore-elastic-pools.md) 集區的最虛擬核心可用數量，客戶可以選擇最符合其 DTU 資料庫或彈性集區的 vCore 服務目標。 

[範例](#dtu-to-vcore-migration-examples)一節將說明使用這種方法的範例遷移案例。

在要遷移之資料庫的內容中執行此查詢，而不是在資料庫中執行 `master` 。 在遷移彈性集區時，請在集區中任何資料庫的內容中執行查詢。

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

除了虛擬核心數目 (邏輯 Cpu) 和硬體世代之外，其他數個因素可能會影響 vCore 服務目標的選擇：

- 對應 T-SQL 查詢符合 DTU 和 vCore 服務目標的 CPU 容量，因此對於 CPU 系結的工作負載而言，結果會更準確。
- 針對相同的硬體世代和相同數量的虛擬核心，vCore 資料庫的 IOPS 和交易記錄輸送量資源限制通常會高於 DTU 資料庫。 對於 IO 系結的工作負載，可能會降低 vCore 模型中的虛擬核心數目，以達到相同層級的效能。 以絕對值表示的 DTU 和 vCore 資料庫的資源限制會在 [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 視圖中公開。 比較要遷移的 DTU 資料庫與使用大約相符服務目標的 vCore 資料庫之間的這些值，可協助您更精確地選取 vCore 服務目標。
- 對應查詢也會針對要遷移的 DTU 資料庫或彈性集區，以及 vCore 模型中的每個硬體產生，傳回每個核心的記憶體數量。 在遷移至 vCore 之後，如果需要大型記憶體資料快取以達到足夠效能的工作負載，或需要大型記憶體授與進行查詢處理的工作負載，請務必確定記憶體總計或更高的記憶體。 針對這類工作負載，視實際效能而定，可能需要增加虛擬核心數目，以取得足夠的總記憶體。
- 選擇 vCore 服務目標時，應考慮 DTU 資料庫的歷程 [記錄資源使用量](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 。 具有一致使用量過低之 CPU 資源的 DTU 資料庫，可能需要比對應查詢所傳回的數目少虛擬核心。 相反地，如果 DTU 資料庫的 CPU 使用率過高，可能會需要比查詢所傳回的更多虛擬核心。
- 如果使用間歇或無法預測的使用模式來遷移資料庫，請考慮使用 [無伺服器](serverless-tier-overview.md) 計算層級。  請注意，在無伺服器中)  (要求的並行工作者數目上限為75% 布建計算中已設定的最大虛擬核心數目的限制。  此外，無伺服器中可用的最大記憶體是已設定的最大虛擬核心數目的 3 GB 倍;例如，在設定 40 max 虛擬核心時，最大記憶體是 120 GB。   
- 在 vCore 模型中，支援的資料庫大小上限可能會因硬體產生而有所不同。 針對大型資料庫，請檢查 vCore 模型中針對 [單一資料庫](resource-limits-vcore-single-databases.md) 和 [彈性](resource-limits-vcore-elastic-pools.md)集區所支援的最大大小。
- 針對彈性集區， [DTU](resource-limits-dtu-elastic-pools.md) 和 [vCore](resource-limits-vcore-elastic-pools.md) 模型會有每個集區支援的最大資料庫數目差異。 當您遷移具有多個資料庫的彈性集區時，應考慮這一點。
- 某些硬體世代可能無法在每個區域中使用。 檢查 [硬體](service-tiers-vcore.md#hardware-generations)世代下的可用性。

> [!IMPORTANT]
> 系統會提供上述的 DTU 來 vCore 大小調整指導方針，以協助進行目標資料庫服務目標的初始估計。
>
> 目標資料庫的最佳設定取決於工作負載。 因此，在遷移後達到最佳的價格/效能比率，可能需要利用 vCore 模型的彈性來調整虛擬核心數目、 [硬體世代](service-tiers-vcore.md#hardware-generations)、 [服務](service-tiers-vcore.md#service-tiers) 和 [計算](service-tiers-vcore.md#compute-tiers) 層，以及調整其他資料庫設定參數，例如平行處理原則的 [最大程度](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)。
> 

### <a name="dtu-to-vcore-migration-examples"></a>VCore 遷移範例的 DTU

> [!NOTE]
> 下列範例中的值僅供說明之用。 在描述案例中傳回的實際值可能會不同。
> 

**遷移標準 S9 資料庫**

對應查詢會傳回下列結果 (沒有為了簡潔起見而顯示的部分資料行) ：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24.00|Gen5|5.40|16.800|7|24.000|5.05|

我們看到 DTU 資料庫有24個邏輯 Cpu (虛擬核心) ，每個 vCore 5.4 GB 的記憶體，而且正在使用第5代硬體。 直接符合的是第5代硬體上的一般用途 24 vCore 資料庫，亦即 **GP_Gen5_24** 的 vCore 服務目標。

**遷移標準 S0 資料庫**

對應查詢會傳回下列結果 (沒有為了簡潔起見而顯示的部分資料行) ：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0.25|Gen4|0.42|0.250|7|0.425|5.05|

我們看到 DTU 資料庫具有相當於0.25 的邏輯 Cpu (虛擬核心) 、每個 vCore 0.42 GB 的記憶體，以及使用第4代硬體。 第4代和第5代硬體世代中最小的 vCore 服務目標 **GP_Gen4_1** 和 **GP_Gen5_2** ，提供比標準 S0 資料庫更多的計算資源，因此不可能直接符合。 由於第4代硬體已 [解除](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)委任，因此偏好 **GP_Gen5_2** 選項。 此外，如果工作負載適用于 [無伺服器](serverless-tier-overview.md) 計算層級，則 **GP_S_Gen5_1** 會是更接近的相符項。

**遷移 Premium P15 資料庫**

對應查詢會傳回下列結果 (沒有為了簡潔起見而顯示的部分資料行) ：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42.00|Gen5|4.86|29.400|7|42.000|5.05|

我們看到 DTU 資料庫具有42個邏輯 Cpu (虛擬核心) ，每個 vCore 4.86 GB 的記憶體，而且正在使用第5代硬體。 雖然沒有42核心的 vCore 服務目標，但 **BC_Gen5_40** 的服務目標在 CPU 和記憶體容量方面都非常接近，而且是非常相符的。

**遷移基本的 200 eDTU 彈性集區**

對應查詢會傳回下列結果 (沒有為了簡潔起見而顯示的部分資料行) ：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4.00|Gen5|5.40|2.800|7|4.000|5.05|

我們看到 DTU 彈性集區有4個邏輯 Cpu (虛擬核心) （每個 vCore 5.4 GB 記憶體），以及使用第5代硬體。 VCore 模型中的直接相符是 **GP_Gen5_4** 彈性集區。 不過，此服務目標支援每個集區最多200個資料庫，而基本 200 eDTU 彈性集區則支援最多500個資料庫。 如果要遷移的彈性集區具有200以上的資料庫，則必須 **GP_Gen5_6** 相符的 vCore 服務目標，以支援最多500個資料庫。

## <a name="migrate-geo-replicated-databases"></a>遷移異地複寫資料庫

從以 DTU 為基礎的模型遷移到以 vCore 為基礎的購買模型，類似于在 standard 和 premium 服務層中的資料庫之間升級或降級異地複寫關聯性。 在遷移期間，您不需要停止異地複寫，但必須遵循下列排序規則：

- 升級時，您必須先升級次要資料庫，然後再升級主要資料庫。
- 降級時，順序相反︰您必須先降級主要資料庫，然後再降級次要資料庫。

當您在兩個彈性集區之間使用異地複寫時，建議您將一個集區指定為主要集區，並將另一個集區指定為次要集區。 在此情況下，當您要遷移彈性集區時，您應該使用相同的排序指引。 但是，如果您有包含主要和次要資料庫的彈性集區，請將集區的使用率視為主要和次要資料庫，並據以依照排序規則進行。  

下表提供特定遷移案例的指引：

|目前的服務層級|目標服務層級|遷移類型|使用者動作|
|---|---|---|---|
|標準|一般用途|橫向|可以依任何順序遷移，但必須確保適當的 vCore 大小調整，如上所述|
|Premium|業務關鍵|橫向|可以依任何順序遷移，但必須確保適當的 vCore 大小調整，如上所述|
|標準|業務關鍵|升級|必須先遷移次要|
|業務關鍵|標準|降級|必須先遷移主要|
|Premium|一般用途|降級|必須先遷移主要|
|一般用途|Premium|升級|必須先遷移次要|
|業務關鍵|一般用途|降級|必須先遷移主要|
|一般用途|業務關鍵|升級|必須先遷移次要|
||||

## <a name="migrate-failover-groups"></a>遷移容錯移轉群組

在遷移具有多個資料庫的容錯移轉群組時，必須個別遷移主要和次要資料庫。 過程中適用相同的考量和排序規則。 將資料庫轉換成以 vCore 為基礎的購買模型之後，容錯移轉群組將會以相同的原則設定保持有效。

### <a name="create-a-geo-replication-secondary-database"></a>建立異地複寫次要資料庫

您只能使用主資料庫所用的相同服務層級，來建立異地複寫次要資料庫， (異地複寫的次要資料庫) 。 對於記錄產生率較高的資料庫，我們建議使用與主資料庫相同的計算大小來建立異地次要資料庫。

如果您要在彈性集區中為單一主資料庫建立異地次要資料庫，請確定集區的 `maxVCore` 設定符合主資料庫的計算大小。 如果您要為另一個彈性集區中的主資料庫建立異地次要資料庫，建議您讓集區具有相同的 `maxVCore` 設定。

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>使用資料庫複製從 DTU 遷移至 vCore

您可以將任何資料庫 (具有以 DTU 為基礎的計算大小) 複製到資料庫 (具有以虛擬核心為基礎的計算大小)，而不會有任何限制或特殊排序，但前提是目標計算大小支援來源資料庫的最大資料庫大小。 資料庫複本會在複製作業開始時建立資料的快照集，而且不會在來源與目標之間同步處理資料。

## <a name="next-steps"></a>後續步驟

- 如需適用于單一資料庫的特定計算大小和儲存體大小選項，請參閱 [SQL Database 單一資料庫的 vCore 為基礎的資源限制](resource-limits-vcore-single-databases.md)。
- 如需特定計算大小和彈性集區可用的儲存體大小選項，請參閱 SQL Database 彈性集區的 [vCore 為基礎的資源限制](resource-limits-vcore-elastic-pools.md)。