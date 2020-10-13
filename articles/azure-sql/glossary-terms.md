---
title: 名詞解釋
titleSuffix: Azure SQL
description: 在 Azure VM 上使用 Azure SQL Database、Azure SQL 受控執行個體和 SQL 的詞彙。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: 18ff2c9690de1708cd8382d83a0c01662a8e6fb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619775"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database 術語詞彙
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|Context|詞彙|詳細資訊|
|:---|:---|:---|
|Azure 服務|Azure SQL Database 或 SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|購買模型|以 DTU 為基礎的購買模型|[以 DTU 為基礎的購買模型](database/service-tiers-dtu.md)|
||以虛擬核心為基礎的購買模型|[以虛擬核心為基礎的購買模型](database/service-tiers-vcore.md)|
|部署選項 |單一資料庫|[單一資料庫](database/single-database-overview.md)|
||彈性集區|[彈性集區](database/elastic-pool-overview.md)|
|服務層級|Basic、Standard、Premium、一般用途、超大規模、業務關鍵|針對 vCore 模型中的服務層，請參閱 [SQL Database 服務層級](database/service-tiers-vcore.md#service-tiers)。 針對 DTU 模型中的服務層，請參閱 [dtu 模型](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)。|
|計算層|無伺服器計算|[無伺服器計算](database/service-tiers-vcore.md#compute-tiers)
||佈建計算|[佈建計算](database/service-tiers-vcore.md#compute-tiers)
|計算世代|第5代，M 系列，Fsv2 系列|[硬體世代](database/service-tiers-vcore.md#hardware-generations)
|伺服器實體| 伺服器 |[邏輯 SQL 伺服器](database/logical-servers.md)|
|資源類型|vCore|提供給單一資料庫、彈性集區之計算資源的 CPU 核心。 |
||計算大小和儲存體數量|計算大小是適用于單一資料庫或彈性集區的 CPU、記憶體和其他非儲存體相關資源的最大數量。  儲存體大小是單一資料庫或彈性集區可用的最大儲存體數量。 如需 vcore 模型中的調整大小選項，請參閱 [vcore 單一資料庫](database/resource-limits-vcore-single-databases.md)和 [vcore 彈性](database/resource-limits-vcore-elastic-pools.md)集區。   (。/managed-instance/resource-limits.md) 。  如需 DTU 模型中的調整大小選項，請參閱 [dtu 單一資料庫](database/resource-limits-dtu-single-databases.md) 和 [dtu 彈性](database/resource-limits-dtu-elastic-pools.md)集區。

## <a name="azure-sql-managed-instance"></a>Azure SQL 受控執行個體

|Context|詞彙|詳細資訊|
|:---|:---|:---|
|Azure 服務|Azure SQL 受控執行個體|[SQL 受控執行個體](managed-instance/sql-managed-instance-paas-overview.md)|
|購買模型|以虛擬核心為基礎的購買模型|[以虛擬核心為基礎的購買模型](database/service-tiers-vcore.md)|
|部署選項 |單一執行個體|[單一實例](managed-instance/sql-managed-instance-paas-overview.md)|
|| (預覽) 的實例集區|[執行個體集區](managed-instance/instance-pools-overview.md)|
|服務層級|一般用途，業務關鍵|[SQL 受控執行個體服務層級](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|計算層|佈建計算|[佈建計算](database/service-tiers-vcore.md#compute-tiers)|
|計算世代|Gen5|[硬體世代](database/service-tiers-vcore.md#hardware-generations)
|伺服器實體|受控實例或實例| N/A 作為 SQL 受控執行個體本身就是伺服器 |
|資源類型|vCore|提供給 SQL 受控執行個體計算資源的 CPU 核心。|
||計算大小和儲存體數量|計算大小是 SQL 受控執行個體的 CPU、記憶體和其他非儲存體相關資源的最大數量。  儲存體大小是適用于 SQL 受控執行個體的最大儲存體數量。  適用于調整大小選項、 [SQL 受控實例](managed-instance/resource-limits.md)。 |

## <a name="sql-on-azure-vm"></a>Azure VM 上的 SQL

在這裡需要更多內容
