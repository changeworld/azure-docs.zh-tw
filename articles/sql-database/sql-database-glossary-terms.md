---
title: 名詞解釋
description: Azure SQL Database 術語詞彙
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: 733901d38703e02ab7dbe811b0f80a1dfedf03d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705780"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database 術語詞彙

|Context|詞彙|詳細資訊|
|:---|:---|:---|
|Azure 服務|Azure SQL Database 或 SQL Database|[Azure SQL Database 服務](sql-database-technical-overview.md)|
|購買模型|以 DTU 為基礎的購買模型|[基於 DTU 的採購模型](sql-database-service-tiers-dtu.md)|
||以虛擬核心為基礎的購買模型|[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)|
|部署選項 |單一資料庫|[單一資料庫](sql-database-single-database.md)|
||彈性集區|[彈性池](sql-database-elastic-pool.md)|
||受控執行個體|[託管實例](sql-database-managed-instance.md)|
|服務層|基本、標準、高級、通用、超大規模、業務關鍵|有關 vCore 模型中的服務層，請參閱[單個資料庫和彈性池](sql-database-service-tiers-vcore.md#service-tiers)以及[託管實例](sql-database-managed-instance.md#managed-instance-service-tiers)。 有關 DTU 模型中的服務層，請參閱[DTU 模型](sql-database-service-tiers-dtu.md#compare-the-dtu-based-service-tiers)。|
|計算層|無伺服器計算|[無伺服器計算](sql-database-service-tiers-vcore.md#compute-tiers)
||佈建計算|[佈建計算](sql-database-service-tiers-vcore.md#compute-tiers)
|計算世代|第5代，M系列，Fsv2系列|[硬體代數](sql-database-service-tiers-vcore.md#hardware-generations)
|伺服器實體|SQL Database 伺服器或資料庫伺服器|[資料庫伺服器](sql-database-servers.md)|
||SQL Database 受控執行個體伺服器、受控執行個體伺服器或執行個體伺服器|[託管實例](sql-database-managed-instance.md)|
|資源類型|vCore|提供給單個資料庫、彈性池或託管實例的計算資源的 CPU 內核。|
||計算大小和存儲量|計算大小是可用於單個資料庫、彈性池或託管實例的最大 CPU、記憶體和其他非存儲相關資源量。  存儲大小是單個資料庫、彈性池或託管實例可用於的最大存儲量。  有關 vcore 模型中大小調整選項，請參閱[vCore 單個資料庫](sql-database-vcore-resource-limits-single-databases.md)[、vCore 彈性池](sql-database-vcore-resource-limits-elastic-pools.md)和[託管實例](sql-database-managed-instance-resource-limits.md)。  有關 DTU 模型中的大小調整選項，請參閱[DTU 單個資料庫](sql-database-dtu-resource-limits-single-databases.md)和[DTU 彈性池](sql-database-dtu-resource-limits-elastic-pools.md)。
