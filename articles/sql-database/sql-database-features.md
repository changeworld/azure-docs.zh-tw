---
title: 功能比較
description: 本文會比較不同類型之 Azure SQL Database 中可用的 SQL Server 功能。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
ms.date: 05/10/2019
ms.openlocfilehash: 33ecef4dde3787546afd28e5f5b31e8dd535fc7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646359"
---
# <a name="azure-sql-database-features"></a>Azure SQL 資料庫功能

Azure SQL 資料庫服務與最新的穩定版本的 SQL Server 共用一個通用代碼庫。 大多數標準 SQL 語言、查詢處理和資料庫管理功能在 SQL Server 和 Azure SQL 資料庫中是相同的。 SQL Server 中常見的功能和 Azure SQL 資料庫的所有風格包括：
  - 語言功能 -[控制流語言關鍵字](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow)、[游標](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql)、[資料類型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql)[、DML 語句](https://docs.microsoft.com/sql/t-sql/queries/queries)、[謂詞](https://docs.microsoft.com/sql/t-sql/queries/predicates)、[序號](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers)、[預存程序](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)和[變數](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql)。
  - 資料庫功能 -[自動調優（計畫強制），](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)[更改跟蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)，[資料庫排序規則](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation)，[包含資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)，[包含使用者](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)，[資料壓縮](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression)，[資料庫配置設置](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)，[線上索引操作](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online)，[分區](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes)，和[時程表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)（[請參閱入門指南](sql-database-temporal-tables.md)）。
  - 安全功能 -[應用程式角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles)，[動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)（[請參閱入門指南](sql-database-dynamic-data-masking-get-started.md)），[行級安全和](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)威脅檢測 - 請參閱[單個資料庫和彈性池和](sql-database-threat-detection.md)[託管實例](sql-database-managed-instance-threat-detection.md)入門指南。
  - 多模型功能 -[圖形處理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)[，JSON資料](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)（[見入門指南](sql-database-json-features.md)[），OPENXML，](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)[空間](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)[，OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)和[XML索引](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql)。

Azure SQL 資料庫管理資料庫並保證其高可用性。 某些可能影響高可用性或無法在 PaaS 世界中使用的功能在 Azure SQL 資料庫中的功能有限。 此外，某些資料庫功能取決於所創建的 Azure SQL 資料庫的類型。 下表描述了這些功能。 使用 Azure SQL Database 時，您可以將資料庫建立為[受控執行個體](sql-database-managed-instance.md)的一部分、作為單一資料庫，或為彈性集區的一部分。 如果需要有關差異的更多詳細資訊，可以在[單個資料庫和彈性池](sql-database-transact-sql-information.md)或[託管實例](sql-database-managed-instance-transact-sql-information.md)的單獨頁面中找到它們。

## <a name="sql-features"></a>SQL 功能

下表列出了 SQL Server 的主要功能，並提供有關該功能在託管實例或單個資料庫和彈性池中是部分支援還是完全支援的資訊，並提供有關該功能的詳細資訊的連結。

| **SQL 功能** | **單一資料庫和彈性集區** | **託管實例和實例池** |
| --- | --- | --- |
| [始終加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 請參閱[憑證存放區](sql-database-always-encrypted.md)和[金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md) | 是 - 請參閱[憑證存放區](sql-database-always-encrypted.md)和[金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md) |
| [AlwaysOn 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每個資料庫都保證[99.99-99.995% 的可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 | [99.99.% 的可用性](sql-database-high-availability.md)保證每個資料庫，[並且不能由使用者管理](sql-database-managed-instance-transact-sql-information.md#availability)。 災害復原在[Azure SQL 資料庫的業務連續性概述](sql-database-business-continuity.md)中討論。 使用[自動容錯移轉組](sql-database-auto-failover-group.md)在另一個區域中配置輔助"始終位於託管實例"。。 其他 SQL Server 實例和單個資料庫不能用作託管實例的次要資料庫。 |
| [附加資料庫](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 否 | 否 |
| [審計](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [是](sql-database-auditing.md)| [是的](sql-database-managed-instance-auditing.md)，有些[不同](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [Azure Active Directory (AAD) 驗證](sql-database-aad-authentication.md) | 是。 僅限 AAD 使用者。 | 是。 包括伺服器級 AAD 登錄名。 |
| [BACKUP 命令](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 否，僅限系統起始的自動備份 - 請參閱[自動備份](sql-database-automated-backups.md) | 是，使用者啟動的僅複本備份到 Azure Blob 存儲（使用者無法啟動自動系統備份） - 請參閱[備份差異](sql-database-managed-instance-transact-sql-information.md#backup) |
| [內置功能](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) | 
| [BULK 插入語句](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 是，但只是從 Azure Blob 存儲作為源。 | 是的，但只是從 Azure Blob 存儲作為源 - 看到[差異](sql-database-managed-instance-transact-sql-information.md#bulk-insert--openrowset)。 |
| [憑證與非對稱金鑰](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 是，無法訪問檔案系統和`BACKUP``CREATE`操作。 | 是的，無法訪問檔案系統`BACKUP`和`CREATE`操作 - 請參閱[證書差異](sql-database-managed-instance-transact-sql-information.md#certificates)。 | 
| [更改資料捕獲 - CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 | 是 |
| [定序 - 伺服器/執行個體](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | 否，始終使用預設邏輯伺服器`SQL_Latin1_General_CP1_CI_AS`排序規則。 | 是，可以在[創建實例](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)時設置，以後無法更新。 |
| [列存儲索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 是 -[高級層、標準層 - S3 及以上、通用層、業務關鍵型層和超規模層](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |是 |
| [通用語言運行時 - CLR](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 | 是，但無法訪問語句中的`CREATE ASSEMBLY`檔案系統 - 請參閱[CLR 差異](sql-database-managed-instance-transact-sql-information.md#clr) |
| [認證](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | 是，但僅[資料庫作用域憑據](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)。 | 是，但僅 Azure**金鑰保存庫**且`SHARED ACCESS SIGNATURE`受支援，請參閱[詳細資訊](sql-database-managed-instance-transact-sql-information.md#credential) |
| [跨資料庫/三部分名稱查詢](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 請參閱[彈性查詢](sql-database-elastic-query-overview.md) | 是，再加上[彈性查詢](sql-database-elastic-query-overview.md) |
| [跨資料庫交易](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 | 是，在實例中。 有關跨實例查詢，請參閱[連結伺服器差異](sql-database-managed-instance-transact-sql-information.md#linked-servers)。 |
| [資料庫郵件 - DbMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 否 | 是 |
| [資料庫鏡像](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 | [否](sql-database-managed-instance-transact-sql-information.md#database-mirroring) |
| [資料庫快照](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 否 | 否 |
| [DBCC 陳述式](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [DBCC 差異](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL 語句](https://docs.microsoft.com/sql/t-sql/statements/statements) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [DDL 觸發器](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 僅限資料庫 |  是 |
| [分散式分割區檢視](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 否 | 是 |
| [分散式交易 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 請參閱[彈性交易](sql-database-elastic-transactions-overview.md) |  否 - 請參閱[連結伺服器差異](sql-database-managed-instance-transact-sql-information.md#linked-servers)。 嘗試在遷移期間將多個分散式 SQL Server 實例中的資料庫合併到一個託管實例中。 |
| [DML 觸發器](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 大部分 - 請參閱個別陳述式 |  是 |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 大部分 - 請參閱個別 DMV |  是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [事件通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 否 - 請參閱[警示](sql-database-insights-alerts-portal.md) | 否 |
| [運算式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |是 | 是 |
| [擴展事件（XEvent）](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分請參閱 [SQL Database 中的擴充事件](sql-database-xevent-db-diff-from-svr.md) | 是 - 請參閱[擴充事件差異](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [擴展的預存程序](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 | 否 |
| [檔案和檔案群組](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 僅限主要檔案群組 | 是。 檔路徑將自動分配，並且無法在`ALTER DATABASE ADD FILE`[語句](sql-database-managed-instance-transact-sql-information.md#alter-database-statement)中指定檔位置。  |
| [檔流](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 否 | [否](sql-database-managed-instance-transact-sql-information.md#filestream-and-filetable) |
| [全文檢索搜尋 （FTS）](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  是，但不支援協力廠商斷字元 | 是，但[不支援協力廠商斷字元](sql-database-managed-instance-transact-sql-information.md#full-text-semantic-search) |
| [函式](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [記憶體內部最佳化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 是 -[高級層和業務關鍵型層僅](sql-database-in-memory.md)對非持久性記憶體內物件（如表類型）的支援有限 | 是 - [僅限業務關鍵層](sql-database-managed-instance.md) |
| [語言元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大部分 - 請參閱個別元素 |  是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [連結伺服器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 請參閱[彈性查詢](sql-database-elastic-query-horizontal-partitioning.md) | 是。 僅到[SQL 伺服器和 SQL 資料庫](sql-database-managed-instance-transact-sql-information.md#linked-servers)，無需分散式交易。 |
| 從檔讀取[的連結伺服器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine)（CSV，Excel）| 否。 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)作為 CSV 格式的替代方法。 | 否。 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)作為 CSV 格式的替代方法。 在[託管實例回饋項](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)上跟蹤此請求|
| [日誌發貨](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每個資料庫都包含[高可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 | 本機內置作為 DMS 遷移過程的一部分。 不能作為高可用性解決方案提供，因為每個資料庫都包含其他[高可用性](sql-database-high-availability.md)方法，並且不建議將記錄傳送用作 HA 替代方法。 災害復原在[Azure SQL 資料庫的業務連續性概述](sql-database-business-continuity.md)中討論。 資料庫之間的複製機制不可用 - 在[業務關鍵型層](sql-database-service-tier-business-critical.md)、[自動容錯移轉組](sql-database-auto-failover-group.md)或[異動複寫](sql-database-managed-instance-transactional-replication.md)上使用輔助副本作為替代方法。 |
| [登入和使用者](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | 是，但`CREATE`和`ALTER`登錄語句不提供所有選項（沒有 Windows 和伺服器級 Azure 活動目錄登錄）。 `EXECUTE AS LOGIN`不受支援 -`EXECUTE AS USER`改用。  | 是的，有一些[區別](sql-database-managed-instance-transact-sql-information.md#logins-and-users)。 不支援 Windows 登錄，應用 Azure 活動目錄登錄替換它們。 |
| [最低記錄大量匯入](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否，僅支援完全恢復模型。 | 否，僅支援完全恢復模型。 |
| [修改系統資料](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 否 | 是 |
| [OLE Automation](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 否 | 否 |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|否|是，僅適用于其他 Azure SQL 資料庫和 SQL 伺服器。 請參閱[T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|否|是，僅適用于其他 Azure SQL 資料庫和 SQL 伺服器。 請參閱[T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)|
| [打開的ROW集](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|是，只能從 Azure Blob 存儲導入。 |是，僅對其他 Azure SQL 資料庫和 SQL 伺服器，並從 Azure Blob 存儲導入。 請參閱[T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)|
| [運算子](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大部分 - 請參閱個別運算子 |是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [聚基](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 否。 您可以使用`OPENROWSET`函數查詢放置在 Azure Blob 存儲上的檔中的資料。 | 否。 您可以使用`OPENROWSET`函數查詢放置在 Azure Blob 存儲上的檔中的資料。 |
| [查詢通知](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | 否 | 是 |
| [機器學習服務](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)（_以前的研發服務_）| 是，現已在[公開預覽版](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)中推出  | 否 |
| [恢復模型](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | 僅支援保證高可用性的完整恢復。 簡單和批量記錄恢復模型不可用。 | 僅支援保證高可用性的完整恢復。 簡單和批量記錄恢復模型不可用。 | 
| [資源調控器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 否 | 是 |
| [RESTORE 陳述式](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 | 是，在`FROM URL`Azure Blob 存儲上放置備份檔案的必填選項。 請參閱[還原差異](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [從備份還原資料庫](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 僅限從自動備份 - 請參閱[SQL Database 復原](sql-database-recovery-using-backups.md) | 從自動備份 - 請參閱[SQL 資料庫恢復](sql-database-recovery-using-backups.md)和在 Azure Blob 存儲上放置的完整備份 - 請參閱[備份差異](sql-database-managed-instance-transact-sql-information.md#backup) |
| [將資料庫還原到 SQL 伺服器](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 否。 使用 BACPAC 或 BCP 而不是本機還原。 | 否，因為託管實例中使用的 SQL Server 資料庫引擎的版本高於本地使用的任何 SQL Server 版本的 RTM 版本。 改用 BACPAC、BCP 或異動複寫。 |
| [語義搜索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 否 | 否 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 否 | 是，但僅在實例內。 如果使用遠端服務代理路由，請嘗試在遷移期間將多個分散式 SQL Server 實例中的資料庫合併到一個託管實例中，並且僅使用本地路由。 請參閱[服務代理差異](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [伺服器組態設定](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [設置語句](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)|
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 否 - 請參閱[彈性工作](elastic-jobs-overview.md) | 是 - 請參閱 [SQL Server Agent 差異](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server 稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 請參閱 [SQL Database 稽核](sql-database-auditing.md) | 是 - 請參閱[稽核差異](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [系統預存函式](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [系統預存程序](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 請參閱個別預存程序 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [系統資料表](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 請參閱個別資料表 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [系統目錄檢視](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 請參閱個別檢視 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [Tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database) | 是。 [每個資料庫每個內核 32GB 大小](sql-database-vcore-resource-limits-single-databases.md)。 | 是。 [整個 GP 層每個 vCore 的 24GB 大小，受 BC 層上的實例大小限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)  |
| [臨時表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 本機和資料庫範圍的全域暫存資料表 | 本機和執行個體範圍的全域暫存資料表 |
| 時區選擇 | 否 | [是](sql-database-managed-instance-timezone.md)，必須在創建託管實例時配置它。 |
| [跟蹤標誌](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 否 | 是，但僅限於有限的全域跟蹤標誌集。 查看[DBCC 差異](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [異動複寫](sql-database-managed-instance-transactional-replication.md) | 是，[僅事務和快照複製訂閱伺服器](sql-database-single-database-migrate.md) | 是，[在公共預覽版](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)中。 在此處[查看約束。](sql-database-managed-instance-transact-sql-information.md#replication) |
| [透明資料加密 （TDE）](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 是 - 僅限一般用途與商務關鍵服務層級| [是](transparent-data-encryption-azure-sql.md) |
| Windows 驗證 | 否 | 否 |
| [Windows Server 容錯移轉叢集](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 否。 每個資料庫都包含提供[高可用性](sql-database-high-availability.md)的其他技術。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 | 否。 每個資料庫都包含提供[高可用性](sql-database-high-availability.md)的其他技術。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 |

## <a name="platform-capabilities"></a>平台功能

Azure 平臺提供了許多 PaaS 功能，這些功能作為附加值添加到標準資料庫功能中。 有許多外部服務可用於 Azure SQL 資料庫服務。 

| **平臺功能** | **單一資料庫和彈性集區** | **託管實例和實例池** |
| --- | --- | --- |
| [活動異地複製](sql-database-active-geo-replication.md) | 是 - 超大規模以外的所有服務層 | 否，請參閱[自動容錯移轉組](sql-database-auto-failover-group.md)作為替代方法 |
| [自動容錯移轉組](sql-database-auto-failover-group.md) | 是 - 超大規模以外的所有服務層 | 是，請參閱[自動容錯移轉組](sql-database-auto-failover-group.md)|
| 自動調整規模 | 是，但僅在[無伺服器模型中](sql-database-serverless.md)。 在非伺服器模型中，服務層（vCore、存儲或 DTU 的更改）快速且線上。 服務層更改需要最少或沒有停機時間。 | 否，您需要選擇保留的計算和存儲。 服務層（vCore 或最大存儲）的更改處於線上狀態，需要最少或沒有停機時間。 |
| [自動備份](sql-database-automated-backups.md) | 是。 每 7 天進行一次完整備份，差 12 小時進行，每 5-10 分鐘進行一次記錄備份。 | 是。 每 7 天進行一次完整備份，差 12 小時進行，每 5-10 分鐘進行一次記錄備份。 |
| [自動調整 (索引)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) \(機器翻譯\)| [是](sql-database-automatic-tuning.md)| 否 |
| [可用性區域](/azure/availability-zones/az-overview) | 是 | 否 |
| [Azure 資源健康狀態](/azure/service-health/resource-health-overview) | 是 | 否 |
| 備份保留期 | 是。 7 天預設值，最多 35 天。 | 是。 7 天預設值，最多 35 天。 |
| [資料移轉服務 (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | 是 | 是 |
| 檔案系統存取 | 否。 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage)可從 Azure Blob 存儲訪問和載入資料作為替代方法。 | 否。 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage)可從 Azure Blob 存儲訪問和載入資料作為替代方法。 |
| [地理恢復](sql-database-recovery-using-backups.md#geo-restore) | 是 - 超大規模以外的所有服務層 | 是 - 超大規模以外的所有服務層 |
| [超大規模架構](sql-database-service-tier-hyperscale.md) | 是 | 否 |
| [長期備份保留 - LTR](sql-database-long-term-retention.md) | 是，將自動備份保留長達 10 年。 | 尚未提供。 使用`COPY_ONLY`[手動備份](sql-database-managed-instance-transact-sql-information.md#backup)作為臨時解決方法。 |
| 暫停/恢復 | 是，在[無伺服器模型中](sql-database-serverless.md) | 否 | 
| [基於策略的管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 | 否 |
| 公用 IP 位址 | 是。 可以使用防火牆或服務終結點限制訪問。  | 是。 需要顯式啟用埠 3342 必須在 NSG 規則中啟用。 如果需要，可以禁用公共 IP。 有關詳細資訊，請參閱[公共終結點](sql-database-managed-instance-public-endpoint-securely.md)。 | 
| [資料庫還原時間點](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 是 - 超大規模以外的所有服務層 - 請參閱[SQL 資料庫恢復](sql-database-recovery-using-backups.md#point-in-time-restore) | 是 - 請參閱 [SQL Database 復原](sql-database-recovery-using-backups.md#point-in-time-restore) |
| 資源集區 | 是，作為[彈性池](sql-database-elastic-pool.md) | 是。 單個託管實例可以有多個共用同一資源池的資料庫。 此外，您還可以在[實例池（預覽）](sql-database-instance-pools.md)中部署多個可共用資源的託管實例。 |
| 向上或向下擴展（線上） | 可以，您可以更改 DTU 或保留 vCore 或最大存儲，但停機時間最少。 | 可以，您可以以最少的停機時間更改預留的 vCore 或最大存儲。 |
| [SQL 別名](https://docs.microsoft.com/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) | 否，使用[DNS 別名](dns-alias-overview.md) | 否，使用[Clicongf](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/Lesson-Learned-33-How-to-make-quot-cliconfg-quot-to-work-with/ba-p/369022)在用戶端電腦上設置別名。 |
| [SQL 分析](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | 是 | 是 |
| [SQL 資料同步](sql-database-get-started-sql-data-sync.md) | 是 | 否 |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 否[，Azure 分析服務](https://azure.microsoft.com/services/analysis-services/)是單獨的 Azure 雲服務。 | 否[，Azure 分析服務](https://azure.microsoft.com/services/analysis-services/)是單獨的 Azure 雲服務。 |
| [SQL 伺服器整合服務 （SSIS）](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 是，使用 Azure Data Factory (ADF) 環境中的受控 SSIS，其中的套件會儲存於 Azure SQL Database 所裝載的 SSISDB 中，並於 Azure SSIS Integration Runtime (IR) 上執行，請參閱[在 ADF 中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 <br/><br/>要比較 SQL 資料庫伺服器和託管實例中的 SSIS 功能，請參閱[比較 Azure SQL 資料庫單個資料庫、彈性池和託管實例](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)。 | 是，使用 Azure Data Factory (ADF) 環境中的受控 SSIS，其中的套件會儲存於受控執行個體所裝載的 SSISDB 中，並於 Azure SSIS Integration Runtime (IR) 上執行，請參閱[在 ADF 中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 <br/><br/>要比較 SQL 資料庫和託管實例中的 SSIS 功能，請參閱[比較 Azure SQL 資料庫單個資料庫、彈性池和託管實例](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)。 |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 - 請參閱 [Power BI](https://docs.microsoft.com/power-bi/) | 否 - 請參閱 [Power BI](https://docs.microsoft.com/power-bi/) |
| [查詢性能見解 （QPI）](sql-database-query-performance.md) | 是 | 否。 在 SQL 伺服器管理工作室和 Azure 資料工作室中使用內置報表。 |
| [VNet](../virtual-network/virtual-networks-overview.md) | 部分，它允許使用[VNet 終結點](sql-database-vnet-service-endpoint-rule-overview.md)進行受限訪問 | 是，託管實例將注入客戶的 VNet 中。 查看[子網](sql-database-managed-instance-transact-sql-information.md#subnet)和[VNet](sql-database-managed-instance-transact-sql-information.md#vnet) |
| VNet 服務終結點 | [是](sql-database-vnet-service-endpoint-rule-overview.md) | 否 |
| VNet 全球對等互連 | 是，使用[專用 IP 和服務終結點](sql-database-vnet-service-endpoint-rule-overview.md) | 否，由於[VNet 全域對等互連中的負載等化器約束](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)，[不支援託管實例](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。

## <a name="tools"></a>工具
Azure SQL 資料庫支援各種資料工具，可説明您管理資料。

| **工具** | **單一資料庫和彈性集區** | **託管實例和實例池** |
| --- | --- | --- |
| Azure 入口網站 | 是 | 是 |
| Azure CLI | 是 | 是|
| [Azure 資料工作室](https://docs.microsoft.com/sql/azure-data-studio/what-is) | 是 | 是 |
| Azure Powershell | 是 | 是 |
| [BACPAC 檔案 (匯出)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 請參閱 [SQL Database 匯出](sql-database-export.md) | 是 - 請參閱 [SQL Database 匯出](sql-database-export.md) |
| [BACPAC 檔案 (匯入)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 請參閱 [SQL Database 匯入](sql-database-import.md) | 是 - 請參閱 [SQL Database 匯入](sql-database-import.md) |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 否 | 否 |
| [主資料服務 （MDS）](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 否 | 否 |
| [Smo](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [是](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 是[版本 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SQL 伺服器資料工具 （SSDT）](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | 是 | 是 |
| [SQL 伺服器管理工作室 （SSMS）](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | 是 | 是[版本 18.0 及更高版本](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 是 | 是 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 請參閱[擴充事件](sql-database-xevent-db-diff-from-svr.md) | 是 |
| [系統中心運營經理 （SCOM）](https://docs.microsoft.com/system-center/scom/welcome) | [是](https://www.microsoft.com/download/details.aspx?id=38829) | 是[，預覽版](https://www.microsoft.com/download/details.aspx?id=100306) |

## <a name="migration-methods"></a>移轉方法

您可以使用不同的遷移方法在 SQL Server、單一資料庫和託管實例資料庫之間移動資料。 某些方法是**連線**的，並拾取在運行遷移時在源上所做的所有更改，而在**離線**方法中，您需要停止在遷移進行時修改源上的資料的工作負載。

| **來源** | **單個資料庫和彈性池** | **託管實例和實例池** |
| --- | --- | --- |
| SQL 伺服器（上部、AzureVM、亞馬遜 RDS） | **線上：**[資料移轉服務 （DMS）](https://docs.microsoft.com/sql/dma/dma-overview)，[異動複寫](sql-database-managed-instance-transactional-replication.md) <br/> **離線**[：BACPAC 檔（導入）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)， BCP | **線上：**[資料移轉服務 （DMS）](https://docs.microsoft.com/sql/dma/dma-overview)，[異動複寫](sql-database-managed-instance-transactional-replication.md) <br/> **離線：** 本機備份/還原[、BACPAC 檔（導入](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)）、BCP、[快照複製](sql-database-managed-instance-transactional-replication.md) |
| 單一資料庫 | **離線**[：BACPAC 檔（導入）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)， BCP | **離線**[：BACPAC 檔（導入）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)， BCP |
| 受控執行個體 | **線上：**[異動複寫](sql-database-managed-instance-transactional-replication.md) <br/> **離線**[：BACPAC 檔（導入）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[快照複製](sql-database-managed-instance-transactional-replication.md) | **線上：**[異動複寫](sql-database-managed-instance-transactional-replication.md) <br/> **離線：** 跨實例點時間還原[（Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase?#examples)或[Azure CLI），](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Cross-instance-point-in-time-restore-in-Azure-SQL-Database/ba-p/386208)[本機備份/還原](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)[、BACPAC 檔（導入](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)）、BCP、[快照複製](sql-database-managed-instance-transactional-replication.md) |

## <a name="next-steps"></a>後續步驟

Microsoft 會持續為 Azure SQL Database 新增功能。 請使用下列篩選來瀏覽 Azure 的「服務更新」網頁，以尋找最新更新：

- 篩選出 [SQL Database 服務](https://azure.microsoft.com/updates/?service=sql-database)。
- 篩選出 SQL Database 功能的「正式運作 [(GA)」公告](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 。

有關 Azure SQL 資料庫風格的詳細資訊，請參閱：
- [什麼是 SQL Database？](sql-database-technical-overview.md)
- [什麼是受控執行個體？](sql-database-managed-instance.md)
- [什麼是託管實例池？](sql-database-instance-pools.md)
