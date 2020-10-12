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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986636"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>一般用途服務層級-Azure SQL Database 和 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 以 vCore 為基礎的購買模型中的一般用途服務層級，在以 DTU 為基礎的購買模型中稱為標準服務層級。 如需以 DTU 為基礎的購買模型比較 vCore 為基礎的購買模型，請參閱 [購買模型和資源](purchasing-models.md)。

Azure SQL Database 和 Azure SQL 受控執行個體是以針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保即使在基礎結構失敗的情況下，仍可確保99.99% 的可用性。 

Azure SQL Database 和 SQL 受控執行個體使用兩個服務層級： 

- 一般用途
- 業務關鍵

Azure SQL Database 也有第三個服務層級，目前無法用於 Azure SQL 受控執行個體：

- 超大規模資料庫

一般用途服務層的架構模型是以計算和儲存體的分隔為基礎。 此架構模型仰賴 Azure Blob 儲存體的高可用性和可靠性，可以透明的方式複寫資料庫檔案，並保證當基礎結構發生失敗時也不會遺失資料。

下圖顯示標準架構模型中具有分隔計算和儲存體層的四個節點。

![分隔計算與儲存體](./media/service-tier-general-purpose/general-purpose-service-tier.png)

在一般用途服務層級的架構模型中，有兩個層級：

- 無狀態計算層，執行 `sqlservr.exe` 流程並且僅包含暫時性和快取資料 (例如計畫快取、緩衝集區、列儲存集區)。 此無狀態節點是由 Azure Service Fabric 操作，可將進程初始化、控制節點的健全狀況，並在必要時執行容錯移轉至另一個位置。
- 具狀態資料層，包含儲存在 Azure Blob 儲存體中的資料庫檔案 (.mdf/.ldf)。 Azure Blob 可確保任何資料庫檔案中放置的任何記錄都不會遺失資料。 Azure 儲存體具有內建的資料可用性/冗余，可確保即使進程損毀，也會保留資料檔案中記錄檔或頁面中的每筆記錄。

每次升級資料庫引擎或作業系統時，基礎結構的某些部分會失敗，或者如果在此程式中偵測到某個重大問題 `sqlservr.exe` ，Azure Service Fabric 會將無狀態程式移至另一個無狀態計算節點。 主要節點發生容錯移轉時，會有一組備用節點等候執行新的計算服務，以便將容錯移轉的時間縮到最短。 Azure 儲存體層中的資料不會受到影響，而且資料/記錄檔會附加到新初始化的進程。 此程式可保證99.99% 的可用性，但由於轉換時間和新節點以冷快取啟動，因此可能會對正在執行的繁重工作負載產生一些效能影響。

## <a name="when-to-choose-this-service-tier"></a>選擇此服務層級的時機

一般用途服務層級是 Azure SQL Database 的預設服務層級，而 Azure SQL 受控執行個體是針對大部分的一般工作負載所設計。 如果您需要完全受控的資料庫引擎（具有 99.99% SLA），且在大部分情況下符合 Azure 虛擬機器上 SQL Server 的5到10毫秒之間的儲存體延遲，一般用途層就是您可以選擇的選項。

## <a name="next-steps"></a>接下來的步驟

- 尋找資源特性 ([SQL 受控執行個體](../managed-instance/resource-limits.md#service-tier-characteristics)中一般用途/標準層的核心、i/o、記憶體) 、 [vCore 模型](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) 中的單一資料庫或 [dtu 模型](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)，或 [vCore 模型](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) 和 [dtu 模型](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits)中的彈性集區。
- 了解[業務關鍵](service-tier-business-critical.md)和[超大規模資料庫](service-tier-hyperscale.md)層。
- 瞭解 [Service Fabric](../../service-fabric/service-fabric-overview.md)。
- 如需高可用性和嚴重損壞修復的更多選項，請參閱 [商務持續性](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
