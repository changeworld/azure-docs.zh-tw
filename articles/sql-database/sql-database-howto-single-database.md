---
title: 如何配置單個資料庫
description: 了解如何設定與管理 Azure SQL Database - 單一資料庫
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 4283e1a2c92b7fe738fb57a8103cea1deb0015a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76027711"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>如何在 Azure SQL Database 中使用單一資料庫

本節會提供各種指南、指令碼和講解說明，協助您管理和設定 Azure SQL Database 中的單一資料庫

## <a name="migrate"></a>遷移

- [移轉至 SQL Database](sql-database-single-database-migrate.md) – 了解移轉至受控執行個體的建議移轉程序與工具。
- 了解如何在[移轉後管理 SQL 資料庫](sql-database-manage-after-migration.md)。

## <a name="configure-features"></a>設定功能

- [設定異動複寫](replication-to-sql-database.md)可在資料庫之間複寫日期。
- [設定威脅偵測](sql-database-threat-detection.md)可讓 Azure SQL Database 識別可疑的活動，例如 SQL 插入式攻擊或是從可疑位置進行的存取。
- [設定動態資料遮罩](sql-database-dynamic-data-masking-get-started-portal.md)來保護您的敏感性資料。
- [設定資料庫的備份保留期](sql-database-long-term-backup-retention-configure.md)將備份保留在 Azure Blob 儲存體上。 替代方法是[使用 Azure 保存庫設定備份保留期 (已取代)](sql-database-long-term-backup-retention-configure-vault.md)。
- [設定異地複寫](sql-database-geo-replication-portal.md)將您資料庫的複本保持在另一個區域中。
- [配置地理副本的安全性](sql-database-geo-replication-security-config.md)。

## <a name="monitor-and-tune-your-database"></a>監視與微調資料庫

- [啟用自動調整](sql-database-automatic-tuning-enable.md)讓 Azure SQL Database 使工作負載的效能達到最佳化。
- [啟用自動調整的電子郵件通知](sql-database-automatic-tuning-email-notifications.md)以取得調整建議的相關資訊。
- [套用效能建議](sql-database-advisor-portal.md)將資料庫最佳化。
- [建立警示](sql-database-insights-alerts-portal.md)以從 Azure SQL Database 取得通知。
- 如果發現應用程式與資料庫之間有一些連線問題，請[疑難排解連線](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)。 您也可以使用[資源健康狀態了解連線問題](sql-database-resource-health.md)。
- [管理檔案空間](sql-database-file-space-management.md)來監視您資料庫中的儲存體使用量。

## <a name="query-distributed-data"></a>查詢分散式資料

- [查詢多個資料庫之間垂直分割的資料](sql-database-elastic-query-getting-started-vertical.md)。
- [跨橫向擴展的資料層進行報告](sql-database-elastic-query-horizontal-partitioning.md)。
- [跨具有不同架構的表進行查詢](sql-database-elastic-query-vertical-partitioning.md)。

## <a name="elastic-database-jobs"></a>彈性資料庫作業

- [創建和管理](elastic-jobs-powershell.md)使用 PowerShell 的彈性資料庫作業。
- 使用 Transact-SQL [建立及管理](elastic-jobs-tsql.md)彈性資料庫作業。
- [移轉 (從舊的彈性工作)](elastic-jobs-migrate.md)。

## <a name="database-sharding"></a>資料庫分區

- [升級彈性資料庫用戶端庫](sql-database-elastic-scale-upgrade-client-library.md)。
- [創建分片應用程式](sql-database-elastic-scale-get-started.md)。
- [查詢水平分區的資料](sql-database-elastic-query-getting-started.md)。
- 執行[多分區查詢](sql-database-elastic-scale-multishard-querying.md)。
- [移動分片資料](sql-database-elastic-scale-configure-deploy-split-and-merge.md)。
- [設定資料庫分區中的安全性](sql-database-elastic-scale-split-merge-security-configuration.md)。
- [新增分區](sql-database-elastic-scale-add-a-shard.md)至目前的 od 資料庫分區集。
- [修復分片映射問題](sql-database-elastic-database-recovery-manager.md)。
- [遷移分片資料庫](sql-database-elastic-convert-to-use-elastic-tools.md)。
- [創建計數器](sql-database-elastic-database-perf-counters.md)。
- [使用實體架構](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)查詢分區化資料。
- [使用 Dapper 架構](sql-database-elastic-scale-working-with-dapper.md)查詢分區化資料。

## <a name="next-steps"></a>後續步驟
- 深入了解[受控執行個體的操作指南](sql-database-howto-managed-instance.md)
