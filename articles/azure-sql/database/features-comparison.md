---
title: 比較 SQL Database 與 SQL 受控執行個體的 database engine 功能
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 本文將比較 Azure SQL Database 的 database engine 功能和 Azure SQL 受控執行個體
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein, danil
ms.date: 12/25/2020
ms.openlocfilehash: 7bdde57c1d33118fd7d3c8e04a2507d8997c36d0
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809508"
---
# <a name="features-comparison-azure-sql-database-and-azure-sql-managed-instance"></a>功能比較： Azure SQL Database 和 Azure SQL 受控執行個體

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 和 SQL 受控執行個體共用通用的程式碼基底 SQL Server 的最新穩定版本。 大部分的標準 SQL 語言、查詢處理和資料庫管理功能都相同。 SQL Server 與 SQL Database 或 SQL 受控執行個體之間常見的功能如下：

- 語言功能-[控制流程語言關鍵字](/sql/t-sql/language-elements/control-of-flow)、資料[指標](/sql/t-sql/language-elements/cursors-transact-sql)、[資料類型](/sql/t-sql/data-types/data-types-transact-sql)、 [DML 語句](/sql/t-sql/queries/queries)、 [](/sql/t-sql/queries/predicates)述詞、[序號](/sql/relational-databases/sequence-numbers/sequence-numbers)、[預存程式](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)和[變數](/sql/t-sql/language-elements/variables-transact-sql)。
- 資料庫功能- [自動調整 (計畫強制) ](/sql/relational-databases/automatic-tuning/automatic-tuning)、 [變更追蹤](/sql/relational-databases/track-changes/about-change-tracking-sql-server)、 [資料庫定序](/sql/relational-databases/collations/set-or-change-the-database-collation)、自主 [資料庫](/sql/relational-databases/databases/contained-databases)、 [包含的使用者](/sql/relational-databases/security/contained-database-users-making-your-database-portable)、 [資料壓縮](/sql/relational-databases/data-compression/data-compression)、 [資料庫設定](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)、 [線上索引作業](/sql/relational-databases/indexes/perform-index-operations-online)、資料 [分割](/sql/relational-databases/partitions/partitioned-tables-and-indexes)和 [時態表](/sql/relational-databases/tables/temporal-tables) ([請參閱快速入門手冊](../temporal-tables.md)) 。
- 安全性功能- [應用程式角色](/sql/relational-databases/security/authentication-access/application-roles)、 [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking) ([請參閱快速入門手冊](dynamic-data-masking-overview.md)) 、 [資料列層級安全性](/sql/relational-databases/security/row-level-security)和威脅偵測-請參閱 [SQL Database](threat-detection-configure.md) 和 [SQL 受控執行個體](../managed-instance/threat-detection-configure.md)的快速入門手冊。
- 多模型功能- [圖形處理](/sql/relational-databases/graphs/sql-graph-overview)、 [JSON 資料](/sql/relational-databases/json/json-data-sql-server) ([請參閱快速入門手冊](json-features.md)) 、 [OPENXML](/sql/t-sql/functions/openxml-transact-sql)、 [空間](/sql/relational-databases/spatial/spatial-data-sql-server)、 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql)和 [XML 索引](/sql/t-sql/statements/create-xml-index-transact-sql)。

Azure 會管理您的資料庫，並保證其高可用性。 某些可能會影響高可用性或不適用於 PaaS 世界的功能，在 SQL Database 和 SQL 受控執行個體中的功能有限。 下表將說明這些功能。 如果您需要更多有關差異的詳細資料，您可以在 [Azure SQL Database](../managed-instance/transact-sql-tsql-differences-sql-server.md) 或 [Azure SQL 受控執行個體](../managed-instance/transact-sql-tsql-differences-sql-server.md)的個別頁面中找到這些差異。

## <a name="features-of-sql-database-and-sql-managed-instance"></a>SQL Database 和 SQL 受控執行個體的功能

下表列出 SQL Server 的主要功能，並提供 Azure SQL Database 和 Azure SQL 受控執行個體中部分或完整支援此功能的相關資訊，以及此功能的詳細資訊連結。

| **功能** | **Azure SQL Database** | **Azure SQL 受控執行個體** |
| --- | --- | --- |
| [一律加密](/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 請參閱[憑證存放區](always-encrypted-certificate-store-configure.md)和[金鑰保存庫](always-encrypted-azure-key-vault-configure.md) | 是 - 請參閱[憑證存放區](always-encrypted-certificate-store-configure.md)和[金鑰保存庫](always-encrypted-azure-key-vault-configure.md) |
| [AlwaysOn 可用性群組](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每個資料庫保證[99.99-達99.995% 的可用性](high-availability-sla.md)。 [Azure SQL Database 的業務連續性概觀](business-continuity-high-availability-disaster-recover-hadr-overview.md)會討論災害復原 | [99.99.](high-availability-sla.md) 保證每個資料庫都有可用性，而且 [無法由使用者管理](../managed-instance/transact-sql-tsql-differences-sql-server.md#availability)。 嚴重損壞修復將在 [使用 Azure SQL Database 的商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)中討論。 使用 [自動容錯移轉群組](auto-failover-group-overview.md) 來設定另一個區域中的次要 SQL 受控執行個體。 SQL Server 實例和 SQL Database 不能當做 SQL 受控執行個體的次要複本使用。 |
| [附加資料庫](/sql/relational-databases/databases/attach-a-database) | 否 | 否 |
| [稽核](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [是](auditing-overview.md)| [是](../managed-instance/auditing-configure.md)，但有一些 [差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing) |
| [Azure Active Directory (Azure AD) 驗證](authentication-aad-overview.md) | 是。 僅 Azure AD 使用者。 | 是。 包括伺服器層級的 Azure AD 登入。 |
| [BACKUP 命令](/sql/t-sql/statements/backup-transact-sql) | 否，僅限系統起始的自動備份 - 請參閱[自動備份](automated-backups-overview.md) | 是，使用者起始僅限複本備份至 Azure Blob 儲存體 (自動系統備份無法由使用者) -請參閱 [備份差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) |
| [內建函式](/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) |
| [BULK INSERT 陳述式](/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 是，但只是從 Azure Blob 儲存體作為來源。 | 是的，但只是從 Azure Blob 儲存體來源-請參閱 [差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)。 |
| [憑證與非對稱金鑰](/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 是，不需要存取和作業的檔案系統 `BACKUP` `CREATE` 。 | 是，沒有檔案系統 `BACKUP` 和 `CREATE` 作業的存取權-請參閱 [憑證差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#certificates)。 |
| [變更資料捕獲-CDC](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 | 是 |
| [定序 - 伺服器/執行個體](/sql/relational-databases/collations/set-or-change-the-server-collation) | 否，一律使用預設伺服器定序 `SQL_Latin1_General_CP1_CI_AS` 。 | 是，可以在 [建立實例](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md) 時設定，稍後無法更新。 |
| [資料行存放區索引](/sql/relational-databases/indexes/columnstore-indexes-overview) | 是- [Premium 層、標準層-S3 及更新版本、一般用途層、業務關鍵和超大規模層](/sql/relational-databases/indexes/columnstore-indexes-overview) |是 |
| [Common language runtime-CLR](/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 | 是，但不需要存取語句中的檔案系統 `CREATE ASSEMBLY` -請參閱 [CLR 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#clr) |
| [認證](/sql/relational-databases/security/authentication-access/credentials-database-engine) | 是，但僅限 [資料庫範圍認證](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)。 | 是，但僅支援 **Azure Key Vault** 和。 `SHARED ACCESS SIGNATURE` 請參閱 [詳細資料](../managed-instance/transact-sql-tsql-differences-sql-server.md#credential) |
| [跨資料庫/三部分名稱查詢](/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 請參閱[彈性查詢](elastic-query-overview.md) | 是，再加上[彈性查詢](elastic-query-overview.md) |
| [跨資料庫交易](/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 | 是，在實例內。 請參閱跨實例查詢的 [連結伺服器差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers) 。 |
| [Database mail-DbMail](/sql/relational-databases/database-mail/database-mail) | 否 | 是 |
| [資料庫鏡像](/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 | [否](../managed-instance/transact-sql-tsql-differences-sql-server.md#database-mirroring) |
| [資料庫快照集](/sql/relational-databases/databases/database-snapshots-sql-server) | 否 | 否 |
| [DBCC 陳述式](/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [DBCC 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc) |
| [DDL 語句](/sql/t-sql/statements/statements) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [T-SQL 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [DDL 觸發程式](/sql/relational-databases/triggers/ddl-triggers) | 僅限資料庫 |  是 |
| [分散式分割區檢視](/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 否 | 是 |
| [分散式交易 - MS DTC](/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 請參閱[彈性交易](elastic-transactions-overview.md) |  否-請參閱 [連結的伺服器差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)。 在遷移期間，嘗試將多個分散式 SQL Server 實例的資料庫合併成一個 SQL 受控執行個體。 |
| [DML 觸發程式](/sql/relational-databases/triggers/create-dml-triggers) | 大部分 - 請參閱個別陳述式 |  是 |
| [DMV](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 大部分 - 請參閱個別 DMV |  是 - 請參閱 [T-SQL 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| 公開預覽中的[彈性查詢](elastic-query-overview.md) ()  | 是，使用必要的 RDBMS 類型。 | 是，使用必要的 RDBMS 類型。 |
| [事件通知](/sql/relational-databases/service-broker/event-notifications) | 否 - 請參閱[警示](alerts-insights-configure-portal.md) | 否 |
| [運算式](/sql/t-sql/language-elements/expressions-transact-sql) |是 | 是 |
| [擴充的事件 (XEvent) ](/sql/relational-databases/extended-events/extended-events) | 部分請參閱 [SQL Database 中的擴充事件](xevent-db-diff-from-svr.md) | 是 - 請參閱[擴充事件差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#extended-events) |
| [擴充預存程序](/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 | 否 |
| [檔案和檔案群組](/sql/relational-databases/databases/database-files-and-filegroups) | 僅限主要檔案群組 | 是。 系統會自動指派檔案路徑，而且無法在語句中指定檔案位置 `ALTER DATABASE ADD FILE` [ ](../managed-instance/transact-sql-tsql-differences-sql-server.md#alter-database-statement)。  |
| [Filestream](/sql/relational-databases/blob/filestream-sql-server) | 否 | [否](../managed-instance/transact-sql-tsql-differences-sql-server.md#filestream-and-filetable) |
| [全文檢索搜尋 (FTS) ](/sql/relational-databases/search/full-text-search) |  是，但不支援協力廠商斷詞工具 | 是，但[不支援協力廠商斷詞](../managed-instance/transact-sql-tsql-differences-sql-server.md#full-text-semantic-search)工具 |
| [函式](/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) |
| [記憶體內部最佳化](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 是在 [Premium 和業務關鍵服務層級](../in-memory-oltp-overview.md)中。</br> 有限支援非持續性 In-Memory OLTP 物件，例如 [超大規模服務層](service-tier-hyperscale.md)級中的記憶體優化資料表變數。| 在[業務關鍵服務層級](../managed-instance/sql-managed-instance-paas-overview.md)中為 [是] |
| [語言元素](/sql/t-sql/language-elements/language-elements-transact-sql) | 大部分 - 請參閱個別元素 |  是 - 請參閱 [T-SQL 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [連結的伺服器](/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 請參閱[彈性查詢](elastic-query-horizontal-partitioning.md) | 是。 只有在沒有分散式交易的情況下 [SQL Server 和 SQL Database](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers) 。 |
| 從檔案讀取的[連結伺服器](/sql/relational-databases/linked-servers/linked-servers-database-engine) (CSV、Excel) | 否。 使用 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 作為 CSV 格式的替代方案。 | 否。 使用 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 作為 CSV 格式的替代方案。 在[SQL 受控執行個體意見反應專案](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)上追蹤這些要求|
| [記錄傳送](/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每個資料庫都包含[高可用性](high-availability-sla.md)。 嚴重損壞修復將在 [商務持續性的總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)中討論。 | 原生內建作為 Azure 資料移轉服務遷移程式的一部分。 無法當作高可用性解決方案使用，因為每個資料庫都包含其他 [高可用性](high-availability-sla.md) 方法，不建議使用記錄傳送作為 HA 替代方案。 嚴重損壞修復將在 [商務持續性的總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)中討論。 資料庫之間的複寫機制無法使用-在 [業務關鍵層](service-tier-business-critical.md)、 [自動容錯移轉群組](auto-failover-group-overview.md)或 [異動複寫](../managed-instance/replication-transactional-overview.md) 上，使用次要複本作為替代方案。 |
| [登入和使用者](/sql/relational-databases/security/authentication-access/principals-database-engine) | 是的， `CREATE` 但是 `ALTER` 登入語句不提供所有選項， (沒有任何 Windows 和伺服器層級的 Azure Active Directory 登入) 。 `EXECUTE AS LOGIN` 不支援-請改用 `EXECUTE AS USER` 。  | 是的，但有一些 [差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#logins-and-users)。 Windows 登入不受支援，而且應該以 Azure Active Directory 登入來取代。 |
| [最低記錄大量匯入](/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否，僅支援完整復原模式。 | 否，僅支援完整復原模式。 |
| [修改系統資料](/sql/relational-databases/databases/system-databases) | 否 | 是 |
| [OLE Automation](/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 否 | 否 |
| [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)|否|是，只有 SQL Database、SQL 受控執行個體和 SQL Server。 請參閱 [t-sql 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md)|
| [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql)|否|是，只有 SQL Database、SQL 受控執行個體和 SQL Server。 請參閱 [t-sql 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md)|
| [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)|是，只會從 Azure Blob 儲存體匯入。 |是，僅 SQL Database、SQL 受控執行個體和 SQL Server，以及從 Azure Blob 儲存體匯入。 請參閱 [t-sql 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md)|
| [運算子](/sql/t-sql/language-elements/operators-transact-sql) | 大部分 - 請參閱個別運算子 |是 - 請參閱 [T-SQL 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [Polybase](/sql/relational-databases/polybase/polybase-guide) | 否。 您可以使用函 `OPENROWSET` [式或在 Synapse Analytics 中使用參考無伺服器 SQL 集區的外部資料表，](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/)查詢放在 Azure Blob 儲存體檔案中的資料。 | 否。 您可以使用函式 `OPENROWSET` 、 [參考 Synapse analytics 中無伺服器 sql 集區的連結伺服器](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)，或在 Synapse 分析或 SQL Server 中參考 [無伺服器 sql 集](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) 區的公開預覽) 中的外部 (資料表，來查詢放置於 Azure Blob 儲存體檔案中的資料。 |
| [查詢通知](/sql/relational-databases/native-client/features/working-with-query-notifications) | 否 | 是 |
| [Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning) _先前 (R services_) | 是，現已在[公開預覽版](/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)中推出  | 否 |
| [復原模式](/sql/relational-databases/backup-restore/recovery-models-sql-server) | 只支援保證高可用性的完整復原。 無法使用簡單和大量記錄復原模式。 | 只支援保證高可用性的完整復原。 無法使用簡單和大量記錄復原模式。 |
| [資源管理員](/sql/relational-databases/resource-governor/resource-governor) | 否 | 是 |
| [RESTORE 陳述式](/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 | 是，有 `FROM URL` 備份檔案的必要選項會放在 Azure Blob 儲存體上。 請參閱 [還原差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#restore-statement) |
| [從備份還原資料庫](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 僅限從自動備份 - 請參閱[SQL Database 復原](recovery-using-backups.md) | 從自動備份-請參閱 Azure Blob 儲存體的 [SQL Database](recovery-using-backups.md) 復原和完整備份-請參閱 [備份差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) |
| [將資料庫還原至 SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 否。 使用 BACPAC 或 BCP，而不是原生還原。 | 否，因為 SQL 受控執行個體中所使用 SQL Server 資料庫引擎的版本高於內部部署使用之任何 RTM 版本的 SQL Server。 請改用 BACPAC、BCP 或異動複寫。 |
| [語義搜尋](/sql/relational-databases/search/semantic-search-sql-server) | 否 | 否 |
| [Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker) | 否 | 是，但只在實例內。 如果您使用遠端 Service Broker 路由，請在遷移期間嘗試將數個分散式 SQL Server 實例的資料庫合併成一個 SQL 受控執行個體，並只使用本機路由。 查看 [Service Broker 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker) |
| [伺服器組態設定](/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 | 是 - 請參閱 [T-SQL 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [Set 語句](/sql/t-sql/statements/set-statements-transact-sql) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [T-SQL 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md)|
| [SQL Server Agent](/sql/ssms/agent/sql-server-agent) | 否-查看 [彈性作業 (預覽) ](elastic-jobs-overview.md) | 是 - 請參閱 [SQL Server Agent 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent) |
| [SQL Server 稽核](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 請參閱 [SQL Database 稽核](auditing-overview.md) | 是 - 請參閱[稽核差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing) |
| [系統預存函式](/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) |
| [系統預存程序](/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 請參閱個別預存程序 | 是 - 請參閱[預存程序、函式、觸發程序差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers) |
| [系統資料表](/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 請參閱個別資料表 | 是 - 請參閱 [T-SQL 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [系統目錄檢視](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 請參閱個別檢視 | 是 - 請參閱 [T-SQL 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md) |
| [Tempdb](/sql/relational-databases/databases/tempdb-database) | 是。 [針對每個資料庫，每個核心 32 GB 的大小](resource-limits-vcore-single-databases.md)。 | 是。 [針對整個 GP 層，每個 vCore 24 GB 的大小，並受 BC 層上的實例大小限制](../managed-instance/resource-limits.md#service-tier-characteristics)  |
| [暫存資料表](/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 本機和資料庫範圍的全域暫存資料表 | 本機和執行個體範圍的全域暫存資料表 |
| 時區選擇 | 否 | [是](../managed-instance/timezones-overview.md)，而且必須在建立 SQL 受控執行個體時進行設定。 |
| [追蹤旗標](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) \(機器翻譯\) | 否 | 是，但只有有限的全域追蹤旗標集合。 請參閱 [DBCC 差異](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc) |
| [異動複寫](../managed-instance/replication-transactional-overview.md) | 是， [只有交易式和快照式複寫訂閱者](migrate-to-database-from-sql-server.md) | 是，處於 [公開預覽](/sql/relational-databases/replication/replication-with-sql-database-managed-instance)狀態。 請參閱 [這裡](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)的條件約束。 |
| [透明資料加密 (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 是 - 僅限一般用途與商務關鍵服務層級| [是](transparent-data-encryption-tde-overview.md) |
| Windows 驗證 | 否 | 否 |
| [Windows Server 容錯移轉叢集](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 否。 每個資料庫都包含提供 [高可用性](high-availability-sla.md) 的其他技術。 嚴重損壞修復將在 [使用 Azure SQL Database 的商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)中討論。 | 否。 每個資料庫都包含提供 [高可用性](high-availability-sla.md) 的其他技術。 嚴重損壞修復將在 [使用 Azure SQL Database 的商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)中討論。 |

## <a name="platform-capabilities"></a>平台功能

Azure 平臺提供許多 PaaS 功能，可新增為標準資料庫功能的額外價值。 有一些可搭配 Azure SQL Database 使用的外部服務。

| **平臺功能** | **Azure SQL Database** | **Azure SQL 受控執行個體** |
| --- | --- | --- |
| [作用中異地複寫](active-geo-replication-overview.md) | 是-超大規模以外的所有服務層級 | 否，請參閱 [自動容錯移轉群組](auto-failover-group-overview.md) 作為替代方案 |
| [自動容錯移轉群組](auto-failover-group-overview.md) | 是-超大規模以外的所有服務層級 | 是，請參閱 [自動容錯移轉群組](auto-failover-group-overview.md)|
| 自動調整規模 | 是，但只在 [無伺服器模型](serverless-tier-overview.md)中。 在非無伺服器模型中，服務層級的變更 (變更 vCore、儲存體或 DTU) 快速且上線。 服務層級變更需要最短或無停機時間。 | 否，您需要選擇保留的計算和儲存體。 服務層 (vCore 或儲存體) 上限的變更已上線，且需要最短或無停機時間。 |
| [自動備份](automated-backups-overview.md) | 是。 每隔7天、差異12小時和記錄備份每5-10 分鐘會執行一次完整備份。 | 是。 每隔7天、差異12小時和記錄備份每5-10 分鐘會執行一次完整備份。 |
| [自動調整 (索引)](/sql/relational-databases/automatic-tuning/automatic-tuning) \(機器翻譯\)| [是](automatic-tuning-overview.md)| 否 |
| [可用性區域](../../availability-zones/az-overview.md) | 是 | 否 |
| [Azure 資源健康狀態](../../service-health/resource-health-overview.md) | 是 | 否 |
| 備份保留 | 是。 預設值為7天，最大值為35天。 | 是。 預設值為7天，最大值為35天。 |
| [資料移轉服務 (DMS)](/sql/dma/dma-overview) | 是 | 是 |
| [彈性作業](elastic-jobs-overview.md) | 是-請參閱 [彈性作業 (預覽) ](elastic-jobs-overview.md) | 不可以使用 ([SQL Agent](../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent) 來) 。 |
| 檔案系統存取 | 否。 使用 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) ，以替代方式存取和載入 Azure Blob 儲存體的資料。 | 否。 使用 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) ，以替代方式存取和載入 Azure Blob 儲存體的資料。 |
| [異地復原](recovery-using-backups.md#geo-restore) | 是 | 是 |
| [超大規模架構](service-tier-hyperscale.md) | 是 | 否 |
| [長期備份保留-LTR](long-term-retention-overview.md) | 是，保持自動備份最多10年的時間。 | 目前還不行。 使用 `COPY_ONLY` [手動備份](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup) 作為暫時的因應措施。 |
| 暫停/繼續 | 是，在[無伺服器模型](serverless-tier-overview.md)中 | 否 |
| [以原則為基礎的管理](/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 | 否 |
| 公用 IP 位址 | 是。 存取權可以使用防火牆或服務端點來限制。  | 是。 需要明確啟用，而且必須在 NSG 規則中啟用埠3342。 如有需要，可以停用公用 IP。 如需詳細資訊，請參閱 [公用端點](../managed-instance/public-endpoint-overview.md) 。 |
| [資料庫還原時間點](/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 是-超大規模以外的所有服務層-請參閱[SQL Database](recovery-using-backups.md#point-in-time-restore)復原 | 是 - 請參閱 [SQL Database 復原](recovery-using-backups.md#point-in-time-restore) |
| 資源集區 | 是，作為[彈性](elastic-pool-overview.md)集區 | 是。 SQL 受控執行個體的單一實例可以有多個共用相同資源集區的資料庫。 此外，您可以在實例集區中部署多個 SQL 受控執行個體實例， [ (preview) ](../managed-instance/instance-pools-overview.md) 可以共用資源。 |
| 相應增加或減少 (線上)  | 是的，您可以在最短的停機時間內變更 DTU 或保留的虛擬核心或最大儲存體。 | 是的，您可以在最短的停機時間內變更保留的虛擬核心或最大儲存體。 |
| [SQL 別名](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) | 否，使用 [DNS 別名](dns-alias-overview.md) | 否，請使用 [Clicongf](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/Lesson-Learned-33-How-to-make-quot-cliconfg-quot-to-work-with/ba-p/369022) 來設定用戶端電腦上的別名。 |
| [SQL Analytics](../../azure-monitor/insights/azure-sql.md) | 是 | 是 |
| [SQL 資料同步](sql-data-sync-sql-server-configure.md) | 是 | 否 |
| [SQL Server Analysis Services (SSAS)](/sql/analysis-services/analysis-services) | 否， [azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 是個別的 azure 雲端服務。 | 否， [azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 是個別的 azure 雲端服務。 |
| [SQL Server Integration Services (SSIS)](/sql/integration-services/sql-server-integration-services) | 是，使用 Azure Data Factory (ADF) 環境中的受控 SSIS，其中的套件會儲存於 Azure SQL Database 所裝載的 SSISDB 中，並於 Azure SSIS Integration Runtime (IR) 上執行，請參閱[在 ADF 中建立 Azure-SSIS IR](../../data-factory/create-azure-ssis-integration-runtime.md)。 <br/><br/>若要比較 SQL Database 和 SQL 受控執行個體中的 SSIS 功能，請參閱 [比較 SQL Database 與 sql 受控執行個體](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。 | 是的，在 Azure Data Factory (ADF) 環境中使用受控 SSIS，其中的套件會儲存在 SQL 受控執行個體所裝載的 SSISDB 中，並在 Azure SSIS Integration Runtime (IR) 上執行，請參閱 [在 ADF 中建立 Azure-SSIS IR](../../data-factory/create-azure-ssis-integration-runtime.md)。 <br/><br/>若要比較 SQL Database 和 SQL 受控執行個體中的 SSIS 功能，請參閱 [比較 SQL Database 與 sql 受控執行個體](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。 |
| [SQL Server Reporting Services (SSRS)](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 - 請參閱 [Power BI](/power-bi/) | 否 - 改用 [Power BI 分頁報告](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi)或是在 Azure VM 上裝載 SSRS。 雖然 SQL 受控執行個體無法將 SSRS 當做服務執行，但是可以使用 SQL Server 驗證，為安裝在 Azure 虛擬機器上的報表伺服器裝載 [SSRS 目錄資料庫](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements)。 |
| [查詢效能深入解析 (QPI) ](query-performance-insight-use.md) | 是 | 否。 在 SQL Server Management Studio 和 Azure Data Studio 中使用內建報表。 |
| [VNet](../../virtual-network/virtual-networks-overview.md) | 部分，使用[VNet 端點](vnet-service-endpoint-rule-overview.md)啟用限制存取 | 是，SQL 受控執行個體會插入客戶的 VNet 中。 請參閱 [子網](../managed-instance/transact-sql-tsql-differences-sql-server.md#subnet) 和 [VNet](../managed-instance/transact-sql-tsql-differences-sql-server.md#vnet) |
| VNet 服務端點 | [是](vnet-service-endpoint-rule-overview.md) | 否 |
| VNet 全域對等互連 | 是，使用 [私人 IP 和服務端點](vnet-service-endpoint-rule-overview.md) | 是，使用 [虛擬網路對等互連](https://techcommunity.microsoft.com/t5/azure-sql/new-feature-global-vnet-peering-support-for-azure-sql-managed/ba-p/1746913)。 |

## <a name="tools"></a>工具

Azure SQL Database 和 Azure SQL 受控執行個體支援各種可協助您管理資料的資料工具。

| **工具** | **Azure SQL Database** | **Azure SQL 受控執行個體** |
| --- | --- | --- |
| Azure 入口網站 | 是 | 是 |
| Azure CLI | 是 | 是|
| [Azure Data Studio](/sql/azure-data-studio/what-is) | 是 | 是 |
| Azure Powershell | 是 | 是 |
| [BACPAC 檔案 (匯出)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 請參閱 [SQL Database 匯出](database-export.md) | 是-請參閱 [SQL 受控執行個體匯出](database-export.md) |
| [BACPAC 檔案 (匯入)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 請參閱 [SQL Database 匯入](database-import.md) | 是-請參閱 [SQL 受控執行個體匯入](database-import.md) |
| [Data Quality Services (DQS)](/sql/data-quality-services/data-quality-services) | 否 | 否 |
| [Master Data Services (MDS)](/sql/master-data-services/master-data-services-overview-mds) | 否 | 否 |
| [SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [是](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 是 [版本 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | 是 | 是 |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | 是 | 是 [18.0 版和更高版本](/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | 是 | 是 |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 請參閱[擴充事件](xevent-db-diff-from-svr.md) | 是 |
| [System Center Operations Manager (SCOM) ](/system-center/scom/welcome) | [是](https://www.microsoft.com/download/details.aspx?id=38829) | 是， [預覽中](https://www.microsoft.com/download/details.aspx?id=38829) |

## <a name="migration-methods"></a>移轉方法

您可以使用不同的遷移方法，在 SQL Server、Azure SQL Database 和 Azure SQL 受控執行個體之間移動資料。 當您執行遷移時，某些方法會在 **線上** ，並挑選對來源所做的所有變更，而在 **離線** 方法中，您必須在進行遷移時，停止正在修改來來源資料的工作負載。

| **來源** | **Azure SQL Database** | **Azure SQL 受控執行個體** |
| --- | --- | --- |
| SQL Server (內部部署、Add-azurevm、Amazon RDS)  | **線上：** [資料移轉服務 (DMS)](/sql/dma/dma-overview)， [異動複寫](../managed-instance/replication-transactional-overview.md) <br/> **離線：** [BACPAC 檔案 (匯入)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)，BCP | **線上：** [資料移轉服務 (DMS)](/sql/dma/dma-overview)， [異動複寫](../managed-instance/replication-transactional-overview.md) <br/> **離線：** 原生備份/還原、 [BACPAC 檔案 (匯入)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[快照](../managed-instance/replication-transactional-overview.md)式複寫 |
| 單一資料庫 | **離線：** [BACPAC 檔案 (匯入)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)，BCP | **離線：** [BACPAC 檔案 (匯入)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)，BCP |
| SQL 受控執行個體 | **線上：** [異動複寫](../managed-instance/replication-transactional-overview.md) <br/> **離線：** [BACPAC 檔案 (匯入)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[快照](../managed-instance/replication-transactional-overview.md)式複寫 | **線上：** [異動複寫](../managed-instance/replication-transactional-overview.md) <br/> **離線：** 跨實例時間點還原 ([Azure PowerShell](/powershell/module/az.sql/restore-azsqlinstancedatabase#examples)或 [Azure CLI](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Cross-instance-point-in-time-restore-in-Azure-SQL-Database/ba-p/386208)) 、[原生備份/還原](../managed-instance/restore-sample-database-quickstart.md)、 [BACPAC 檔案 (匯入)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[快照](../managed-instance/replication-transactional-overview.md)式複寫 |

## <a name="next-steps"></a>後續步驟

Microsoft 會持續為 Azure SQL Database 新增功能。 請使用下列篩選來瀏覽 Azure 的「服務更新」網頁，以尋找最新更新：

- 已篩選至 [Azure SQL Database](https://azure.microsoft.com/updates/?service=sql-database)。
- 已篩選至正式推出的 SQL Database 功能 [ \( GA \) 公告](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 。

如需 Azure SQL Database 和 Azure SQL 受控執行個體的詳細資訊，請參閱：

- [什麼是 Azure SQL Database？](sql-database-paas-overview.md)
- [什麼是 Azure SQL 受控執行個體？](../managed-instance/sql-managed-instance-paas-overview.md)
- [什麼是 Azure SQL 受控執行個體集區？](../managed-instance/instance-pools-overview.md)
