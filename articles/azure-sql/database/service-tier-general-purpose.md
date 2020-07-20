---
title: 一般用途服務層級
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 瞭解 Azure SQL Database 和 Azure SQL 受控執行個體的一般用途服務層級。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986636"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>一般用途服務層級-Azure SQL Database 和 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> VCore 為基礎的購買模型中的一般用途服務層級，在以 DTU 為基礎的購買模型中稱為「標準」服務層級。 如需 vCore 為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱[購買模型和資源](purchasing-models.md)。

Azure SQL Database 和 Azure SQL 受控執行個體是以適用于雲端環境的 SQL Server 資料庫引擎架構為基礎，以確保99.99% 的可用性，即使在基礎結構失敗的情況下也是如此。 

Azure SQL Database 和 SQL 受控執行個體使用兩個服務層級： 

- 一般用途
- 業務關鍵

Azure SQL Database 也有第三個服務層級，目前無法用於 Azure SQL 受控執行個體：

- 超大規模資料庫

一般用途服務層級的架構模型是以計算和儲存體的分隔為基礎。 此架構模型仰賴 Azure Blob 儲存體的高可用性和可靠性，可以透明的方式複寫資料庫檔案，並保證當基礎結構發生失敗時也不會遺失資料。

下圖顯示標準架構模型中具有分隔計算和儲存體層的四個節點。

![分隔計算與儲存體](./media/service-tier-general-purpose/general-purpose-service-tier.png)

在一般用途服務層級的架構模型中，有兩個層級：

- 無狀態計算層，執行 `sqlservr.exe` 流程並且僅包含暫時性和快取資料 (例如計畫快取、緩衝集區、列儲存集區)。 此無狀態節點由 Azure Service Fabric 操作，會初始化程式、控制節點的健全狀況，並在必要時執行容錯移轉至另一個位置。
- 具狀態資料層，包含儲存在 Azure Blob 儲存體中的資料庫檔案 (.mdf/.ldf)。 Azure Blob 可確保任何資料庫檔案中放置的任何記錄都不會遺失資料。 Azure 儲存體具有內建的資料可用性/冗余，可確保資料檔案中記錄檔或頁面中的每筆記錄都會保留，即使進程損毀也一樣。

每當資料庫引擎或作業系統升級時，基礎結構的某些部分就會失敗，或如果在程式中偵測到某個重大問題， `sqlservr.exe` Azure Service Fabric 會將無狀態程式移至另一個無狀態計算節點。 主要節點發生容錯移轉時，會有一組備用節點等候執行新的計算服務，以便將容錯移轉的時間縮到最短。 Azure 儲存體層中的資料不會受到影響，且資料/記錄檔會附加到新初始化的進程。 此程式保證99.99% 的可用性，但可能會對因為轉換時間而執行的繁重工作負載產生一些效能影響，而新節點則是以冷快取開始。

## <a name="when-to-choose-this-service-tier"></a>選擇此服務層級的時機

一般用途服務層級是 Azure SQL Database 和 Azure SQL 受控執行個體中的預設服務層級，專為大部分的一般工作負載所設計。 如果您需要具有 99.99% SLA 的完全受控資料庫引擎，且在大部分情況下符合 Azure 虛擬機器上 SQL Server 的儲存體延遲為5到10毫秒，則一般用途層是您選擇的選項。

## <a name="next-steps"></a>後續步驟

- 在[SQL 受控執行個體](../managed-instance/resource-limits.md#service-tier-characteristics)中，尋找 [一般用途]/[標準] 層的資源特性（核心數目、i/o、記憶體）、 [vCore 模型](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4)中的單一資料庫或[dtu 模型](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)，或[vCore 模型](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4)和[dtu 模型](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits)中的彈性集區。
- 了解[業務關鍵](service-tier-business-critical.md)和[超大規模資料庫](service-tier-hyperscale.md)層。
- 深入瞭解[Service Fabric](../../service-fabric/service-fabric-overview.md)。
- 如需高可用性和嚴重損壞修復的更多選項，請參閱[商務持續性](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
