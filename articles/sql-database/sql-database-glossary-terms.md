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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705780"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database 術語詞彙

|Context|條款|詳細資訊|
|:---|:---|:---|
|Azure 服務|Azure SQL Database 或 SQL Database|[Azure SQL Database 服務](sql-database-technical-overview.md)|
|購買模型|以 DTU 為基礎的購買模型|[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)|
||以虛擬核心為基礎的購買模型|[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)|
|部署選項 |單一資料庫|[單一資料庫](sql-database-single-database.md)|
||彈性集區|[彈性集區](sql-database-elastic-pool.md)|
||受控執行個體|[受控執行個體](sql-database-managed-instance.md)|
|服務層級|基本，標準，Premium，一般用途，超大規模資料庫，業務關鍵|針對 vCore 模型中的服務層級，請參閱[單一資料庫和彈性集](sql-database-service-tiers-vcore.md#service-tiers)區與[受控實例](sql-database-managed-instance.md#managed-instance-service-tiers)。 如需 DTU 模型中的服務層級，請參閱[dtu 模型](sql-database-service-tiers-dtu.md#compare-the-dtu-based-service-tiers)。|
|計算層|無伺服器計算|[無伺服器計算](sql-database-service-tiers-vcore.md#compute-tiers)
||佈建的計算|[佈建計算](sql-database-service-tiers-vcore.md#compute-tiers)
|計算世代|第5代、M 系列、Fsv2 系列|[硬體世代](sql-database-service-tiers-vcore.md#hardware-generations)
|伺服器實體|SQL Database 伺服器或資料庫伺服器|[資料庫伺服器](sql-database-servers.md)|
||SQL Database 受控執行個體伺服器、受控執行個體伺服器或執行個體伺服器|[受控執行個體](sql-database-managed-instance.md)|
|資源類型|vCore|提供給單一資料庫、彈性集區或受控實例之計算資源的 CPU 核心。|
||計算大小和儲存體數量|計算大小是適用于單一資料庫、彈性集區或受控實例的 CPU、記憶體和其他非儲存體相關資源的最大數量。  儲存體大小是適用于單一資料庫、彈性集區或受控實例的最大儲存體數量。  如需 vcore 模型中的調整大小選項，請參閱[vcore 單一資料庫](sql-database-vcore-resource-limits-single-databases.md)、 [vcore 彈性](sql-database-vcore-resource-limits-elastic-pools.md)集區和[受控實例](sql-database-managed-instance-resource-limits.md)。  如需 DTU 模型中的調整大小選項，請參閱[dtu 單一資料庫](sql-database-dtu-resource-limits-single-databases.md)和[dtu 彈性](sql-database-dtu-resource-limits-elastic-pools.md)集區。
