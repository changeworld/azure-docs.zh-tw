---
title: 設定 & 管理內容參考
titleSuffix: Azure SQL Managed Instance
description: 說明如何設定和管理 Azure SQL 受控執行個體內容的參考指南。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 23ce3272d99401340d784f88dd537baa55774a63
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708819"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Azure SQL 受控執行個體內容參考
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

在本文中，您可以找到各種指南、腳本和說明的內容參考，以協助您管理和設定 Azure SQL 受控執行個體。

## <a name="load-data"></a>載入資料

- [遷移至 AZURE sql 受控執行個體](migrate-to-instance-from-sql-server.md)：瞭解建議的遷移程式和工具，以遷移至 azure sql 受控執行個體。
- [將 TDE 憑證遷移至 AZURE SQL 受控執行個體](tde-certificate-migrate.md)：如果您的 SQL Server 資料庫受到透明資料加密（TDE）保護，您將需要遷移 SQL 受控執行個體可用來解密您要在 Azure 中還原之備份的憑證。
- [從 BACPAC 匯入 DB](../database/database-import.md)
- [從 BACPAC 匯出 DB](../database/database-export.md)
- [使用 BCP 載入資料](../load-from-csv-with-bcp.md)
- [使用 Azure Data Factory 載入資料](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>網路組態

- [判斷子網大小](vnet-subnet-determine-size.md)：由於部署 SQL 受控執行個體之後無法調整子網，因此您必須計算打算部署到子網的受控實例數目和類型所需的位址 IP 範圍。 
- [建立新的 VNet 和子網](virtual-network-subnet-create-arm-template.md)：根據[網路需求](connectivity-architecture-overview.md#network-requirements)設定虛擬網路和子網。 
- [設定現有的 VNet 和子網](vnet-existing-add-subnet.md)：確認網路需求，並設定現有的虛擬網路和子網以部署 SQL 受控執行個體。 
- [設定自訂 dns](custom-dns-configure.md)：設定自訂 dns，以透過 db 郵件設定檔的連結伺服器，將外部資源存取權授與 SQL 受控執行個體的自訂網域。 
- [同步網路](azure-app-sync-network-configuration.md)設定：如果您在[將應用程式與 Azure 虛擬網路整合](../../app-service/web-sites-integrate-with-vnet.md)之後無法建立連線，請重新整理網路設定計劃。
- [尋找管理端點 IP 位址](management-endpoint-find-ip-address.md)：判斷 SQL 受控執行個體用來進行管理的公用端點。 
- [確認內建防火牆保護](management-endpoint-verify-built-in-firewall.md)：確認 SQL 受控執行個體只允許必要端口上的流量，以及其他內建的防火牆規則。 
- [連接應用程式](connect-application-instance.md)：瞭解將應用程式連接到 SQL 受控執行個體的不同模式。

## <a name="feature-configuration"></a>功能組態

- [設定 Azure AD 驗證](../database/authentication-aad-configure.md)
- [設定條件式存取](../database/conditional-access-configure.md)
- [多重要素 Azure AD 驗證](../database/authentication-mfa-ssms-overview.md)
- [設定多因素驗證](../database/authentication-mfa-ssms-configure.md)
- [設定時態性保留原則](../database/temporal-tables-retention-policy.md)
- [使用 BYOK 設定 TDE](../database/transparent-data-encryption-byok-configure.md)
- [旋轉 TDE BYOK 金鑰](../database/transparent-data-encryption-byok-key-rotation.md)
- [移除 TDE 保護裝置](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [設定記憶體內部 OLTP](../in-memory-oltp-configure.md)
- [設定 Azure 自動化](../database/automation-manage.md)
- [異動複寫](replication-between-two-instances-configure-tutorial.md)可讓您在受控實例之間複寫資料，或從內部部署 SQL Server 複寫至 SQL 受控執行個體，反之亦然。
- [設定威脅偵測](threat-detection-configure.md)-[威脅偵測](../database/threat-detection-overview.md)是內建的 Azure SQL 受控執行個體功能，可偵測各種潛在的攻擊，例如 SQL 插入式攻擊或來自可疑位置的存取。 
- [建立警示](alerts-create.md)可讓您針對受監視的計量（例如 CPU 使用率、儲存空間耗用量、IOPS 和其他 SQL 受控執行個體）設定警示。 

## <a name="monitoring-and-tuning"></a>監視和微調

- [手動微調](../database/performance-guidance.md)
- [使用 DMV 監視效能](../database/monitoring-with-dmvs.md)
- [使用查詢存放區來監視效能](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [使用 Intelligent Insights 針對效能問題進行疑難排解](../database/intelligent-insights-troubleshoot-performance.md)
- [使用 Intelligent Insights 診斷記錄](../database/intelligent-insights-use-diagnostics-log.md)
- [監視記憶體內部 OLTP 空間](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>擴充事件

- [擴充事件](../database/xevent-db-diff-from-svr.md)
- [將擴充事件儲存到事件檔案中](../database/xevent-code-event-file.md)
- [將擴充事件儲存到信號緩衝區](../database/xevent-code-ring-buffer.md)

## <a name="develop-applications"></a>開發應用程式

- [連線能力](../database/connect-query-content-reference-guide.md#libraries)
- [使用 Spark 連接器](../../cosmos-db/spark-connector.md)
- [驗證應用程式](../database/application-authentication-get-client-id-keys.md)
- [使用批次處理改善效能](../performance-improve-use-batching.md)
- [連線指導方針](../database/troubleshoot-common-connectivity-issues.md)
- [DNS 別名](../database/dns-alias-overview.md)
- [使用 PowerShell 設定 DNS 別名](../database/dns-alias-powershell-create.md)
- [連接埠 - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C 和 C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>設計應用程式

- [為災害復原而設計](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [為彈性集區而設計](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [為應用程式升級而設計](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>設計多租用戶 SaaS 應用程式

- [SaaS 設計模式](../database/saas-tenancy-app-design-patterns.md)
- [SaaS 影片索引子](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 應用程式安全性](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)



## <a name="next-steps"></a>後續步驟

從[部署 SQL 受控執行個體](instance-create-quickstart.md)開始。
