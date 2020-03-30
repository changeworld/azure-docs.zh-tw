---
title: 版本資訊
description: 瞭解 Azure SQL 資料庫服務以及 Azure SQL 資料庫文檔中的新功能和改進
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: 2df83e3b62994381895315b2ef100299e40b745e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366503"
---
# <a name="sql-database-release-notes"></a>SQL 資料庫版本資訊

本文列出了當前處於公共預覽中的 SQL 資料庫功能。 有關 SQL 資料庫更新和改進，請參閱[SQL 資料庫服務更新](https://azure.microsoft.com/updates/?product=sql-database)。 有關其他 Azure 服務的更新和改進，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="features-in-public-preview"></a>公共預覽中的功能

### <a name="single-database"></a>[單一資料庫](#tab/single-database)

| 功能 | 詳細資料 |
| ---| --- |
| 全新 Fsv2 系列和 M 系列硬體系列| 有關詳細資訊，請參閱[硬體代](sql-database-service-tiers-vcore.md#hardware-generations)。|
| [Azure 專用連結](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| 專用連結通過保留 Azure 網路上的資料來簡化網路架構並保護 Azure 中的終結點之間的連接，從而消除了對 Internet 的暴露。 專用連結還使您能夠在 Azure 上創建和呈現自己的服務。 |
| 使用單個資料庫和彈性池加快資料庫恢復 | 有關詳細資訊，請參閱[加速資料庫恢復](sql-database-accelerated-database-recovery.md)。|
|近似的相異計數|有關詳細資訊，請參閱[近似計數唯一](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)。|
|行存儲上的批次處理模式（相容性級別 150 下）|有關詳細資訊，請參閱[行存儲上的批次處理模式](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)。|
| 資料探索與分類  |有關詳細資訊，請參閱[Azure SQL 資料庫和 SQL 資料倉儲資料發現&分類](sql-database-data-discovery-and-classification.md)。|
| 彈性資料庫工作 | 有關詳細資訊，請參閱[創建、配置和管理彈性作業](elastic-jobs-overview.md)。 |
| 彈性查詢 | 有關詳細資訊，請參閱[彈性查詢概述](sql-database-elastic-query-overview.md)。 |
| 彈性交易 | [跨雲資料庫的分散式交易](sql-database-elastic-transactions-overview.md)。 |
|記憶體授予回饋（行模式）（相容性級別 150 下）|有關詳細資訊，請參閱[記憶體授予回饋（行模式）。](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)|
| Azure 門戶中的查詢編輯器 |有關詳細資訊，請參閱[使用 Azure 門戶的 SQL 查詢編輯器來連接和查詢資料](sql-database-connect-query-portal.md)。|
| 具有單個資料庫和彈性池的 R 服務/機器學習 |有關詳細資訊，請參閱[Azure SQL 資料庫中的機器學習服務](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)。|
|SQL Analytics|有關詳細資訊，請參閱[Azure SQL 分析](../azure-monitor/insights/azure-sql.md)。|
|表變數延遲編譯（相容性級別 150 下）|有關詳細資訊，請參閱[表變數延遲編譯](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)。|
| &nbsp; |

### <a name="managed-instance"></a>[託管實例](#tab/managed-instance)

| 功能 | 詳細資料 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">執行個體集區</a> | 將較小的 SQL 實例遷移到雲的便捷且經濟高效的方法。 |
| <a href="https://aka.ms/managed-instance-aadlogins">實例級 Azure AD 伺服器主體（登錄名）</a> | 使用<a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">"從外部提供程式"創建登錄，創建</a>伺服器級登錄。 |
| [異動複寫](sql-database-managed-instance-transactional-replication.md) | 將表的更改複製到放置在託管實例、單個資料庫或 SQL Server 實例上的其他資料庫，或者在其他託管實例或 SQL Server 實例中更改某些行時更新表。 有關詳細資訊，請參閱[在 Azure SQL 資料庫託管實例資料庫中配置複製](replication-with-sql-database-managed-instance.md)。 |
| 威脅偵測 |有關詳細資訊，請參閱[在 Azure SQL 資料庫託管實例中配置威脅檢測](sql-database-managed-instance-threat-detection.md)。|

---

## <a name="managed-instance---new-features-and-known-issues"></a>託管實例 - 新功能和已知問題

### <a name="managed-instance-h2-2019-updates"></a>託管實例 H2 2019 更新

- [服務輔助子網配置](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/)管理子網配置的安全便捷方法，在託管實例控制資料流量時確保管理流量的不間斷流動
- [透明資料加密 （TDE） 帶自帶金鑰 （BYOK）](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/)支援自帶金鑰 （BYOK） 方案，以進行靜態資料保護，並允許組織為金鑰和資料分開管理職責。
- [自動容錯移轉組](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/)使您能夠將所有資料庫從主實例複製到另一個區域中的輔助實例。
- 使用[全域跟蹤標誌](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)配置託管實例行為。

### <a name="managed-instance-h1-2019-updates"></a>託管實例 H1 2019 更新

在 H1 2019 中的託管實例部署模型中啟用了以下功能：
  - 支援使用<a href="https://aka.ms/sql-mi-visual-studio-subscribers">Azure 每月信用額度訂閱 Visual Studio 訂閱者</a>，並提高了[區域限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)。
  - 對 <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 和 SharePoint 2019 </a> 以及 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a> 的支援
  - 創建具有<a href="https://aka.ms/managed-instance-collation">伺服器級排序規則</a>和您選擇的<a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">時區</a>的實例。
  - 託管實例現在受到<a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">內置防火牆</a>的保護。
  - 將實例配置為使用[公共終結點](sql-database-managed-instance-public-endpoint-configure.md)、[代理覆蓋](sql-database-connectivity-architecture.md#connection-policy)連接以獲得更好的網路性能、<a href="https://aka.ms/four-cores-sql-mi-update">在 Gen5 硬體生成上生成 4 個 vCore</a>或為時間點還原<a href="https://aka.ms/managed-instance-configurable-backup-retention">配置備份保留長達 35 天</a>。 長期備份保留（最多 10 年）仍未啟用，因此您可以使用<a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">僅複本備份</a>作為替代方法。
  - 新功能使您能夠使用 PowerShell、[重命名資料庫](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[刪除虛擬叢集](sql-database-managed-instance-delete-virtual-cluster.md)將<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">資料庫異地還原到另一個資料中心</a>。
  - 新的內置[實例參與者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor)允許職責分離 （SoD） 符合安全原則並符合企業標準。
  - 託管實例在以下 Azure 政府區域中可用於 GA（美國德克薩斯州州長、亞利桑那州州長）以及中國北 2 和中國東部 2。 它還可用於以下公共區域：澳大利亞中部、澳大利亞中部 2、巴西南部、法國南部、阿聯酋中部、阿聯酋北部、南非北部、南非西部。

### <a name="known-issues"></a>已知問題

|問題  |發現日期  |狀態  |解決日期  |
|---------|---------|---------|---------|
|[通過門戶限制容錯移轉組](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020 年 1 月|具有解決方法||
|[SQL 代理角色需要非系統管理員登錄的顯式 EXECUTE 許可權](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 12 月|具有解決方法||
|[SQL 代理作業可能會因代理進程重新開機而中斷](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019 年 12 月|無解決方法|2020 年 3 月|
|[SSDT 不支援 AAD 登錄和使用者](#aad-logins-and-users-are-not-supported-in-ssdt)|2019 年 11 月|無解決方法||
|[未應用記憶體中 OLTP 記憶體限制](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 10 月|具有解決方法||
|[嘗試刪除不為空的檔時返回的錯誤錯誤](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019 年 10 月|具有解決方法||
|[更改服務層和創建實例操作被正在進行的資料庫還原阻止](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019 年 9 月|具有解決方法||
|[容錯移轉後可能需要重新配置業務關鍵型服務層上的資源調控器](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019 年 9 月|具有解決方法||
|[跨資料庫服務代理對話方塊必須在服務層升級後重新初始化](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|2019 年 8 月|具有解決方法||
|[不支援 Azure AD 登錄類型的化](#impersonification-of-azure-ad-login-types-is-not-supported)|2019 年 7 月|無解決方法||
|[@querysp_send_db_mail不支援參數](#-parameter-not-supported-in-sp_send_db_mail)|2019 年 4 月|無解決方法||
|[在異地容錯移轉後，必須重新配置異動複寫](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019 年 3 月|無解決方法||
|[在恢復操作期間使用臨時資料庫](#temporary-database-is-used-during-restore-operation)||具有解決方法||
|[重新創建 TEMPDB 結構和內容](#tempdb-structure-and-content-is-re-created)||無解決方法||
|[小型資料庫檔案造成儲存空間超出限制](#exceeding-storage-space-with-small-database-files)||具有解決方法||
|[顯示的 GUID 值而不是資料庫名稱](#guid-values-shown-instead-of-database-names)||具有解決方法||
|[錯誤日誌未保留](#error-logs-arent-persisted)||無解決方法||
|[不支援同一實例中的兩個資料庫上的事務作用域](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||具有解決方法||
|[CLR 模組和連結伺服器有時無法引用本地 IP 位址](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||具有解決方法||
|從 Azure Blob 存儲還原資料庫後，未使用 DBCC CHECKDB 驗證資料庫一致性。||已解決|2019 年 11 月|
|如果源資料庫包含記憶體中 OLTP 物件，則從業務關鍵型層還原到通用層的時間點資料庫將不會成功。||已解決|2019 年 10 月|
|使用安全連線的外部（非 Azure）郵件伺服器的資料庫郵件功能||已解決|2019 年 10 月|
|託管實例中不支援的包含資料庫||已解決|2019 年 8 月|

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>通過門戶限制容錯移轉組

如果容錯移轉組跨越不同 Azure 訂閱或資源組中的實例，則無法從容錯移轉組中的主實例啟動手動容錯移轉。

**解決方法**：通過來自地理輔助實例的門戶啟動容錯移轉。

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL 代理角色需要非系統管理員登錄的顯式 EXECUTE 許可權

如果將非系統管理員登錄添加到任何 SQL Agent[固定資料庫角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)中，則存在一個問題，即需要向主預存程序授予顯式 EXECUTE 許可權才能將這些登錄正常工作。 如果遇到此問題，將顯示錯誤訊息"物件上的 EXECUTE 許可權被拒絕<object_name>（Microsoft SQL 伺服器，錯誤：229）"。

**解決方法**：將登錄添加到 SQL 代理固定資料庫角色之一：SQLAgentUserRole、SQLAgentReaderRole 或 SQLAgent仲裁者Role後，對於添加到這些角色的每個登錄者，將執行以下 T-SQL 腳本，以顯式授予列出的預存程序的 EXECUTE 許可權。

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL 代理作業可能會因代理進程重新開機而中斷

SQL 代理在每次開始作業時都會創建新的會話，從而逐漸增加記憶體消耗。 為了避免達到內部記憶體限制，這將阻止計畫作業的執行，一旦代理的記憶體消耗達到閾值，它將重新開機代理進程。 它可能導致在重新開機時中斷運行的作業的執行。

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>未應用記憶體中 OLTP 記憶體限制

在某些情況下，業務關鍵型服務層無法[正確應用記憶體優化物件的最大記憶體限制](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)。 託管實例可能使工作負載能夠為記憶體中的 OLTP 操作使用更多記憶體，這可能會影響實例的可用性和穩定性。 記憶體中正在達到限制的 OLTP 查詢可能不會立即失敗。 此問題將很快得到解決。 如果查詢達到[限制](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)，則使用更多記憶體中 OLTP 記憶體的查詢將更早失敗。

**解決方法：** 使用[SQL Server 管理工作室](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring)[監視記憶體中 OLTP 存儲使用方式](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)，以確保工作負載使用不超過可用記憶體。 增加取決於 vCore 數量的記憶體限制，或優化工作負載以減少記憶體。

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>嘗試刪除不為空的檔時返回的錯誤錯誤

SQL 伺服器/託管實例[不允許使用者刪除不為空的檔](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)。 如果嘗試使用`ALTER DATABASE REMOVE FILE`語句刪除非空資料檔案，則不會立即返回該錯誤`Msg 5042 – The file '<file_name>' cannot be removed because it is not empty`。 託管實例將繼續嘗試刪除檔，操作將在 30 分鐘後失敗`Internal server error`。

**解決方法**：使用`DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)`命令刪除檔的內容。 如果這是檔組中的唯一檔，則需要在縮小檔之前從與此檔組關聯的表或分區中刪除資料，並可以選擇將此資料載入到另一個表/分區中。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>更改服務層和創建實例操作被正在進行的資料庫還原阻止

正在進行的`RESTORE`語句、資料移轉服務遷移過程和內置的點時間還原將阻止更新服務層或調整現有實例的大小，並創建新實例，直到還原過程完成。 還原進程將在運行還原過程的同一子網中的"託管實例"和實例池上阻止這些操作。 實例池中的實例不受影響。 創建或更改服務層操作不會失敗或超時 - 一旦還原過程完成或取消，它們將繼續。

**解決方法**：等待還原過程完成，或者如果創建或更新服務層操作具有更高的優先順序，則取消還原過程。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>容錯移轉後可能需要重新配置業務關鍵型服務層上的資源調控器

[資源調速器](/sql/relational-databases/resource-governor/resource-governor)功能使您能夠限制分配給使用者工作負荷的資源，在容錯移轉或使用者發起的服務層更改（例如，最大 vCore 或最大實例存儲大小的更改）後，可能會錯誤地對某些使用者工作負荷進行分類。

**解決方法**：定期運行`ALTER RESOURCE GOVERNOR RECONFIGURE`或作為 SQL 代理作業的一部分運行，該作業在實例啟動時執行 SQL 任務，如果使用[資源調控器](/sql/relational-databases/resource-governor/resource-governor)。

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>跨資料庫服務代理對話方塊必須在服務層升級後重新初始化

跨資料庫服務代理對話方塊將在更改服務層操作後停止將消息傳遞到其他資料庫中的服務。 郵件**不會丟失**，可以在寄件者佇列中找到它們。 託管實例中 vCore 或實例存儲大小的任何更改都將導致`service_broke_guid`更改[sys.資料庫](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)視圖中的值，以便更改所有資料庫。 使用`DIALOG` [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql)語句創建的任何引用其他資料庫中的服務代理都將停止向目標服務傳遞消息。

**解決方法：** 在更新服務層並在更新服務層之前停止任何使用跨資料庫服務代理對話方塊的對話的活動，並在更新後重新初始化它們。 如果服務層更改後仍有未傳遞的消息，請從源佇列中讀取消息並將其重新發送到目標佇列。

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>不支援 Azure AD 登錄類型的化

不支援使用`EXECUTE AS USER`或`EXECUTE AS LOGIN`以下 AAD 主體進行類比：
-   別名 AAD 使用者。 在這種情況下`15517`，將返回以下錯誤。
- AAD 登錄和基於 AAD 應用程式或服務主體的使用者。 在這種情況下`15517`，將返回以下錯誤， 和`15406`。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@querysp_send_db_mail不支援參數

sp_send_db_mail`@query`過程中的參數[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql)不起作用。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>在異地容錯移轉後，必須重新配置異動複寫

如果在自動容錯移轉組中的資料庫上啟用異動複寫，則託管實例管理員必須清理舊主伺服器上的所有發佈，並在容錯移轉到其他區域後在新主伺服器上重新配置它們。 有關詳細資訊，請參閱[複製](sql-database-managed-instance-transact-sql-information.md#replication)。

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 不支援 AAD 登錄和使用者

SQL 伺服器資料工具不完全支援 Azure 活動目錄登錄和使用者。

### <a name="temporary-database-is-used-during-restore-operation"></a>在恢復操作期間使用臨時資料庫

當資料庫在託管實例上還原時，還原服務將首先創建一個具有所需名稱的空資料庫，以在實例上分配名稱。 一段時間後，此資料庫將被刪除，並啟動實際資料庫的還原。 處於*還原*狀態的資料庫將暫時具有隨機 GUID 值，而不是名稱。 還原過程完成後，臨時名稱將更改為語句中`RESTORE`指定的所需名稱。 在初始階段，使用者可以訪問空資料庫，甚至在此資料庫中創建表或載入資料。 還原服務啟動第二階段時，將刪除此臨時資料庫。

**解決方法**：在看到還原完成之前，不要訪問要還原的資料庫。

### <a name="tempdb-structure-and-content-is-re-created"></a>重新創建 TEMPDB 結構和內容

資料庫`tempdb`始終拆分為 12 個資料檔案，並且無法更改檔結構。 無法更改每個檔的最大大小，並且無法將新檔添加到`tempdb`。 `Tempdb`當實例啟動或失敗時，始終重新創建為空資料庫，並且不會在 中`tempdb`所做的任何更改將保留。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型資料庫檔案造成儲存空間超出限制

`CREATE DATABASE``ALTER DATABASE ADD FILE`和`RESTORE DATABASE`語句可能會失敗，因為實例可以達到 Azure 存儲限制。

每個通用託管實例最多為 Azure 高級磁碟空間保留 35 TB 的存儲空間。 每個資料庫檔案都放置在單獨的物理磁片上。 磁碟大小可以是 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁片上的未使用空間不收費，但 Azure 高級磁片大小的總和不能超過 35 TB。 在某些情況下，由於內部碎片，總共不需要 8 TB 的託管實例可能會超過存儲大小的 35 TB Azure 限制。

例如，通用託管實例可能有一個大型檔，大小為 1.2 TB，放置在 4 TB 磁片上。 它也可能有 248 個大小為 1 GB 的檔，每個檔都放置在單獨的 128 GB 磁片上。 在此範例中：

- 配置的磁碟儲存體大小總計為 1 x 4 TB + 248 x 128 GB = 35 TB。
- 為執行個體上的資料庫保留的大小總計為 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

此示例說明，在某些情況下，由於檔的特定分佈，託管實例可能會達到 35 TB 的限制，該限制在您可能不希望時保留給附加的 Azure 高級磁片。

在此示例中，只要不添加新檔，現有資料庫就會繼續工作，並且可以毫無問題地增長。 無法創建新資料庫，因為沒有足夠的空間用於新磁片磁碟機，即使所有資料庫的總大小未達到實例大小限制也是如此。 在這種情況下返回的錯誤不清楚。

您可以使用系統檢視[標識剩餘檔的數量](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)。 如果達到此限制，請嘗試使用[DBCC SHRINKFILE 語句清空並刪除一些較小的檔](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)，或切換到[業務關鍵型層，該層沒有此限制](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="guid-values-shown-instead-of-database-names"></a>顯示的 GUID 值而不是資料庫名稱

數個系統檢視、效能計數器、錯誤訊息、XEvents 與錯誤記錄檔項目都會顯示 GUID 資料庫識別碼，而非實際資料庫名稱。 不要依賴這些 GUID 識別碼，因為它們將來會替換為實際的資料庫名稱。

**解決方法**： 使用 sys.database 視圖從物理資料庫名稱解析實際資料庫名稱，以 GUID 資料庫識別碼的形式指定

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>錯誤日誌未保留

託管實例中可用的錯誤日誌不會持久化，並且其大小不包括在最大存儲限制中。 如果發生容錯移轉，錯誤日誌可能會自動擦除。 錯誤日誌歷史記錄中可能存在漏洞，因為託管實例在幾個虛擬機器上多次移動。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>不支援同一實例中的兩個資料庫上的事務作用域

如果`TransactionScope`兩個查詢發送到同一事務作用域下同一實例中的兩個資料庫，則 .NET 中的類不起作用：

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

儘管此代碼適用于同一實例中的資料，但它需要 MSDTC。

**解決方法：** 使用[SqlConnection.更改資料庫（字串）](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)在連接上下文中使用另一個資料庫，而不是使用兩個連接。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模組和連結伺服器有時無法引用本地 IP 位址

放置在託管實例和連結伺服器或引用當前實例的分散式查詢中的 CLR 模組有時無法解決本地實例的 IP。 此錯誤為暫時性問題。

**解決方法：** 如果可能，在 CLR 模組中使用上下文連接。

## <a name="updates"></a>更新

有關 SQL 資料庫更新和改進的清單，請參閱[SQL 資料庫服務更新](https://azure.microsoft.com/updates/?product=sql-database)。

有關所有 Azure 服務的更新和改進，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="contribute-to-content"></a>參與內容

要向 Azure SQL 資料庫文檔提供，請參閱[文檔參與者指南](https://docs.microsoft.com/contribute/)。
