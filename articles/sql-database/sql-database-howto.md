---
title: 設定和管理
description: 了解如何設定與管理 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 11/14/2019
ms.openlocfilehash: c3f7b33e4b42b08334cfb687024985c878dc3713
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209436"
---
# <a name="how-to-use-azure-sql-database"></a>如何使用 Azure SQL Database

在本節中，您可以找到各種指南、指令碼，以及可協助您管理和設定您 Azure SQL Database 的說明。 您也可以尋找[單一資料庫](sql-database-howto-single-database.md)與[受控執行個體](sql-database-howto-managed-instance.md)的操作指南。

## <a name="load-data"></a>載入資料

- [在 Azure 內複製單一資料庫或集區資料庫](sql-database-copy.md)
- [從 BACPAC 匯入 DB](sql-database-import.md)
- [將 DB 匯出到 BACPAC](sql-database-export.md)
- [使用 BCP 載入資料](sql-database-load-from-csv-with-bcp.md)
- [使用 ADF 載入資料](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>資料同步

- [SQL 資料同步](sql-database-sync-data.md)
- [Data Sync Agent](sql-database-data-sync-agent.md)
- [複寫結構描述變更](sql-database-update-sync-schema.md)
- [透過 OMS 進行監視](sql-database-sync-monitor-oms.md)
- [資料同步最佳做法](sql-database-best-practices-data-sync.md)
- [資料同步疑難排解](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>監視和微調

- [手動微調](sql-database-performance-guidance.md)
- [使用 DMV 監視效能](sql-database-monitoring-with-dmvs.md)
- [使用查詢存放區監視效能](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [使用 Intelligent Insights 針對效能問題進行疑難排解](sql-database-intelligent-insights-troubleshoot-performance.md)
- [使用 Intelligent Insights 診斷記錄](sql-database-intelligent-insights-use-diagnostics-log.md)
- [監視記憶體內部 OLTP 空間](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>擴充事件

- [擴充事件](sql-database-xevent-db-diff-from-svr.md)
- [將擴充的事件儲存至事件檔案](sql-database-xevent-code-event-file.md)
- [將擴充的事件儲存至信號緩衝區](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>設定功能

- [設定 Azure AD 驗證](sql-database-aad-authentication-configure.md)
- [設定條件式存取](sql-database-conditional-access.md)
- [多重要素 AAD 驗證](sql-database-ssms-mfa-authentication.md)
- [設定多因素驗證](sql-database-ssms-mfa-authentication-configure.md)
- [設定時態性保留原則](sql-database-temporal-tables-retention-policy.md)
- [使用 BYOK 設定 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
- [旋轉 TDE BYOK 金鑰](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [移除 TDE 保護裝置](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [設定記憶體內部 OLTP](sql-database-in-memory-oltp-migration.md)
- [設定 Azure 自動化](sql-database-manage-automation.md)

## <a name="develop-applications"></a>開發應用程式

- [連線能力](sql-database-libraries.md)
- [使用 Spark 連接器](sql-database-spark-connector.md)
- [驗證應用程式](sql-database-client-id-keys.md)
- [使用批次處理改善效能](sql-database-use-batching-to-improve-performance.md)
- [連線能力指導](sql-database-connectivity-issues.md)
- [DNS 別名](dns-alias-overview.md)
- [DNS 別名 PowerShell](dns-alias-powershell.md)
- [連接埠 - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C 和 C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>設計應用程式

- [為災害復原而設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [為彈性集區而設計](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [為應用程式升級而設計](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>設計多租用戶 SaaS 應用程式

- [SaaS 設計模式](saas-tenancy-app-design-patterns.md)
- [SaaS 影片索引子](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 應用程式安全性](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>後續步驟

- 深入了解[受控執行個體的操作指南](sql-database-howto-managed-instance.md)。
- 深入了解[單一資料庫的操作指南](sql-database-howto-single-database.md)。
