---
title: 設定 & 管理內容參考
description: 尋找說明如何設定和管理 Azure SQL Database 的內容參考。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791761"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>設定和管理內容參考-Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在本文中，您可以找到各種指南、腳本和說明的內容參考，可協助您管理和設定您的 Azure SQL Database。 

## <a name="load-data"></a>載入資料

- [移轉至 SQL Database](migrate-to-database-from-sql-server.md)
- 瞭解如何 [在遷移之後管理 SQL Database](manage-data-after-migrating-to-database.md)。
- [複製資料庫](database-copy.md)
- [從 BACPAC 匯入 DB](database-import.md)
- [從 BACPAC 匯出 DB](database-export.md)
- [使用 BCP 載入資料](../load-from-csv-with-bcp.md)
- [使用 ADF 載入資料](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>設定功能

- [設定 Azure Active Directory (Azure AD) auth](authentication-aad-configure.md)
- [設定條件式存取](conditional-access-configure.md)
- [多重要素 Azure AD 驗證](authentication-mfa-ssms-overview.md)
- [設定 Multi-Factor Authentication](authentication-mfa-ssms-configure.md)
- [設定時態性保留原則](temporal-tables-retention-policy.md)
- [使用 BYOK 設定 TDE](transparent-data-encryption-byok-configure.md)
- [旋轉 TDE BYOK 金鑰](transparent-data-encryption-byok-key-rotation.md)
- [移除 TDE 保護裝置](transparent-data-encryption-byok-remove-tde-protector.md)
- [設定記憶體內部 OLTP](../in-memory-oltp-configure.md)
- [設定 Azure 自動化](automation-manage.md)
- [設定異動複寫](replication-to-sql-database.md)可在資料庫之間複寫日期。
- [設定威脅偵測](threat-detection-configure.md)可讓 Azure SQL Database 識別可疑的活動，例如 SQL 插入式攻擊或是從可疑位置進行的存取。
- [設定動態資料遮罩](dynamic-data-masking-configure-portal.md)來保護您的敏感性資料。
- [設定資料庫的備份保留期](long-term-backup-retention-configure.md)將備份保留在 Azure Blob 儲存體上。 
- [設定異地複寫](active-geo-replication-overview.md)將您資料庫的複本保持在另一個區域中。
- [設定異地複本的安全性](active-geo-replication-security-configure.md)。

## <a name="monitor-and-tune-your-database"></a>監視與微調資料庫

- [手動微調](performance-guidance.md)
- [使用 DMV 監視效能](monitoring-with-dmvs.md)
- [使用查詢存放區監視效能](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [啟用自動調整](automatic-tuning-enable.md)讓 Azure SQL Database 使工作負載的效能達到最佳化。
- [啟用自動調整的電子郵件通知](automatic-tuning-email-notifications-configure.md)以取得調整建議的相關資訊。
- [套用效能建議](database-advisor-find-recommendations-portal.md)將資料庫最佳化。
- [建立警示](alerts-insights-configure-portal.md)以從 Azure SQL Database 取得通知。
- 如果發現應用程式與資料庫之間有一些連線問題，請[疑難排解連線](troubleshoot-common-errors-issues.md)。 您也可以使用[資源健康狀態了解連線問題](resource-health-to-troubleshoot-connectivity.md)。
- [使用 Intelligent Insights 針對效能問題進行疑難排解](intelligent-insights-troubleshoot-performance.md)
- [管理檔案空間](file-space-manage.md)來監視您資料庫中的儲存體使用量。
- [使用 Intelligent Insights 診斷記錄](intelligent-insights-use-diagnostics-log.md)
- [監視記憶體內部 OLTP 空間](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>擴充事件

- [擴充事件](xevent-db-diff-from-svr.md)
- [將擴充的事件儲存至事件檔案](xevent-code-event-file.md)
- [將擴充的事件儲存至信號緩衝區](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>查詢分散式資料

- [查詢多個資料庫之間垂直分割的資料](elastic-query-getting-started-vertical.md)。
- [跨相應放大的資料層報告](elastic-query-horizontal-partitioning.md)。
- [跨具有不同架構的資料表進行查詢](elastic-query-vertical-partitioning.md)。

### <a name="data-sync"></a>資料同步

- [SQL 資料同步](sql-data-sync-data-sql-server-sql-database.md)
- [Data Sync Agent](sql-data-sync-agent-overview.md)
- [複寫結構描述變更](sql-data-sync-update-sync-schema.md)
- [透過 OMS 進行監視](./monitor-tune-overview.md)
- [資料同步最佳做法](sql-data-sync-best-practices.md)
- [資料同步疑難排解](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>彈性資料庫工作

- [建立與管理](elastic-jobs-powershell-create.md) 使用 PowerShell 的彈性資料庫作業。
- 使用 Transact-SQL [建立及管理](elastic-jobs-tsql-create-manage.md)彈性資料庫作業。
- [移轉 (從舊的彈性工作)](elastic-jobs-migrate.md)。

## <a name="database-sharding"></a>資料庫分區

- [升級彈性資料庫用戶端程式庫](elastic-scale-upgrade-client-library.md)。
- [建立分區化應用程式](elastic-scale-get-started.md)。
- [查詢水平分區的資料](elastic-query-getting-started.md)。
- 執行[多分區查詢](elastic-scale-multishard-querying.md)。
- [移動分區化資料](elastic-scale-configure-deploy-split-and-merge.md)。
- [設定資料庫分區中的安全性](elastic-scale-split-merge-security-configuration.md)。
- [新增分區](elastic-scale-add-a-shard.md)至目前的 od 資料庫分區集。
- [修正分區對應問題](elastic-database-recovery-manager.md)。
- [遷移分區化 DB](elastic-convert-to-use-elastic-tools.md)。
- [建立計數器](elastic-database-perf-counters.md)。
- [使用實體架構](elastic-scale-use-entity-framework-applications-visual-studio.md)查詢分區化資料。
- [使用 Dapper 架構](elastic-scale-working-with-dapper.md)查詢分區化資料。

## <a name="develop-applications"></a>開發應用程式

- [連線能力](connect-query-content-reference-guide.md#libraries)
- [使用 Spark 連接器](spark-connector.md)
- [驗證應用程式](application-authentication-get-client-id-keys.md)
- [使用批次處理改善效能](../performance-improve-use-batching.md)
- [連線指導方針](troubleshoot-common-connectivity-issues.md)
- [DNS 別名](dns-alias-overview.md)
- [DNS 別名 PowerShell](dns-alias-powershell-create.md)
- [連接埠 - ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C 和 C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>設計應用程式

- [為災害復原而設計](designing-cloud-solutions-for-disaster-recovery.md)
- [為彈性集區而設計](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [為應用程式升級而設計](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a> (SaaS) 應用程式中設計多租使用者軟體即服務

- [SaaS 設計模式](saas-tenancy-app-design-patterns.md)
- [SaaS 影片索引子](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 應用程式安全性](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [AZURE SQL 受控執行個體的操作指南](../managed-instance/how-to-content-reference-guide.md)