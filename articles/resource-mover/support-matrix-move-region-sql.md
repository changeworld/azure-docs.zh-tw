---
title: 支援使用 Azure 資源移動移動區來移動區域之間的 Azure SQL 資源。
description: 請參閱使用 Azure 資源移動器在區域之間移動 Azure SQL 資源的支援。
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4925f6ffd2383c21f8ff9b0e3196d44fc15bb657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652804"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>支援在 Azure 區域之間移動 Azure SQL 資源

本文摘要說明使用 azure 資源移動器在 Azure 區域之間移動 Azure SQL 資源的支援和必要條件。

## <a name="requirements"></a>需求

下表摘要說明需求。

**功能** | **支援/不支援** | **詳細資料**
--- | --- | ---
**Azure SQL Database Hyperscale** | 不支援 | 無法使用資源移動器移動 Azure SQL 超大規模服務層中的資料庫。
**區域備援** | 支援 |  支援的移動選項：<br/><br/> -支援區域冗余的區域之間。<br/><br/> -不支援區域冗余的區域之間。<br/><br/> -支援區域冗余的區域與不支援區域冗余的區域之間的差異。<br/><br/> -不支援區域冗余的區域與支援區域冗余的區域之間的差異。 
**資料同步** | 中樞/同步資料庫：不支援<br/><br/> 同步成員：支援。 | 如果已移動同步成員，您必須設定新目標資料庫的資料同步。
**現有的異地複寫** | 支援 | 現有的地理複本會重新對應到目的地區域中的新主要複本。<br/><br/> 您必須在移動之後初始化植入。 [深入了解](/azure/sql-database/sql-database-active-geo-replication-portal)
**透明資料加密 (TDE) 攜帶您自己的金鑰 (BYOK) ** | 支援 | [深入瞭解](../key-vault/general/move-region.md) 跨區域移動金鑰保存庫。
**TDE 與服務管理的金鑰** | 支援。 |  [深入瞭解](../key-vault/general/move-region.md) 跨區域移動金鑰保存庫。
**動態資料遮罩規則** | 支援。 | 在移動過程中，規則會自動複製到目的地區域。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started-portal)。
**進階資料安全性** | 不支援。 | 因應措施：在目的地區域的 SQL Server 層級進行設定。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)。
**防火牆規則** | 不支援。 | 因應措施：設定目的地區域中 SQL Server 的防火牆規則。 資料庫層級防火牆規則會從源伺服器複製到目標伺服器。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-server-level-firewall-rule)。
**稽核原則** | 不支援。 | 移動之後，原則將重設為預設值。 [瞭解](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) 如何重設。
**備份保留** | 支援。 | 來源資料庫的備份保留原則會一併傳送到目標資料庫。 [瞭解](/azure/sql-database/sql-database-long-term-backup-retention-configure) 如何在移動之後修改設定。
**自動調整** | 不支援。 | 因應措施：在移動之後設定自動調整設定。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning-enable)。
**資料庫警示** | 不支援。 | 因應措施：在移動之後設定警示。 [深入了解](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal)。
**Azure SQL Server stretch database** | 不支援 | 無法移動具有資源移動器的 SQL server stretch database。
**Azure Synapse Analytics** | 不支援 | 無法使用資源移動器將 Synapse Analytics (先前的 Azure SQL 資料倉儲) 。
## <a name="next-steps"></a>後續步驟

使用資源移動器嘗試使用 [AZURE SQL 資源](tutorial-move-region-sql.md) 至另一個區域。