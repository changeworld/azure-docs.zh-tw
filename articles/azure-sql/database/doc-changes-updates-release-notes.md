---
title: 新功能
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 瞭解 Azure SQL Database & SQL 受控執行個體的新功能和檔改進。
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: sstein
ms.openlocfilehash: af19b72846c78ef80ba170b6d6e0cec97fa2b96e
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533354"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Azure SQL Database & SQL 受控執行個體有哪些新功能？
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文列出目前處於公開預覽狀態 Azure SQL Database 和 Azure SQL 受控執行個體功能。 如 SQL Database 和 SQL 受控執行個體更新與改善，請參閱[SQL Database & SQL 受控執行個體服務更新](https://azure.microsoft.com/updates/?product=sql-database)。 如需其他 Azure 服務的更新和改進，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="whats-new"></a>新功能

Azure SQL Database 和 Azure SQL 受控執行個體的檔已分割成不同的區段。 我們也已更新從*Azure SQL Database 受控實例*到*Azure SQL 受控執行個體*參考受控實例的方式。

我們已完成這項作業，因為單一資料庫和受控實例之間的某些特性和功能會有很大的差異，而且在個別共用的發行項中，說明 Azure SQL Database 和 Azure SQL 受控執行個體之間的複雜細微性變得越來越困難。

不同 Azure SQL 產品之間的這項說明應該簡化並簡化在 Azure 中使用 SQL Server 資料庫引擎的程式，無論是 Azure SQL Database 中的單一受控資料庫、裝載 Azure SQL 受控執行個體中的多個資料庫的完整受控實例，或是在 Azure 虛擬機器上託管的熟悉內部部署 SQL Server 產品。

請考慮這是進行中的工作，而不是每篇文章都已更新。 例如，Transact-sql （T-sql）語句、預存程式和 Azure SQL Database 與 Azure SQL 受控執行個體之間共用的許多功能的檔尚未完成，因此我們會感謝您的耐心等候，我們會繼續說明內容。 

下表提供術語變更的快速比較： 


|**新詞彙**  | **上一個詞彙**  |**說明** |
|---------|---------|---------|
|**Azure SQL 受控執行個體** | Azure SQL Database*受控實例*| Azure SQL 受控執行個體是 Azure SQL 系列中自己的產品，而不只是 Azure SQL Database 內的部署選項。 | 
|**Azure SQL Database**|Azure SQL Database*單一資料庫*| 除非另有明確指定，否則產品名稱 Azure SQL Database 包括部署到彈性集區的單一資料庫和資料庫。 |
|**Azure SQL Database**|Azure SQL Database*彈性集*區| 除非另有明確指定，否則產品名稱 Azure SQL Database 包括部署到彈性集區的單一資料庫和資料庫。  |
|**Azure SQL Database** |Azure SQL Database | 雖然這一詞維持不變，但現在僅適用于單一資料庫和彈性集區部署，而且不包含受控實例。 |
| **Azure SQL**| N/A | 這是指 Azure 中可用的 SQL Server 資料庫引擎產品系列： Azure SQL Database、Azure SQL 受控執行個體，以及 Azure Vm 上的 SQL Server。 | 

## <a name="features-in-public-preview"></a>公開預覽中的功能

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| 功能 | 詳細資料 |
| ---| --- |
| 使用單一資料庫和彈性集區加速資料庫復原 | 如需相關資訊，請參閱[加速資料庫](../accelerated-database-recovery.md)復原。|
| 資料探索與分類  |如需詳細資訊，請參閱[Azure SQL Database 和 Azure Synapse 分析資料探索 & 分類](data-discovery-and-classification-overview.md)。|
| 彈性資料庫工作 | 如需相關資訊，請參閱[建立、設定和管理彈性作業](elastic-jobs-overview.md)。 |
| 彈性查詢 | 如需相關資訊，請參閱[彈性查詢總覽](elastic-query-overview.md)。 |
| 彈性交易 | [跨雲端資料庫的分散式交易](elastic-transactions-overview.md)。 |
| Azure 入口網站中的查詢編輯器 |如需相關資訊，請參閱[使用 Azure 入口網站的 SQL 查詢編輯器來連接及查詢資料](connect-query-portal.md)。|
| 具有單一資料庫和彈性集區的 R services/機器學習服務 |如需詳細資訊，請參閱[Azure SQL Database 中的 Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)。|
|SQL Analytics|如需相關資訊，請參閱[Azure SQL 分析](../../azure-monitor/insights/azure-sql.md)。|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Azure SQL 受控執行個體](#tab/managed-instance)

| 功能 | 詳細資料 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">執行個體集區</a> | 方便且符合成本效益的方式，將較小的 SQL 實例遷移至雲端。 |
| <a href="https://aka.ms/managed-instance-aadlogins">實例層級 Azure AD 伺服器主體（登入）</a> | 使用<a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a>語句來建立實例層級的登入。 |
| [異動複寫](../managed-instance/replication-transactional-overview.md) | 將資料表中的變更複寫到 SQL 受控執行個體、SQL Database 或 SQL Server 中的其他資料庫。 當某些資料列在 SQL 受控執行個體或 SQL Server 的其他實例中變更時，或更新您的資料表。 如需相關資訊，請參閱[在 AZURE SQL 受控執行個體中設定](../managed-instance/replication-between-two-instances-configure-tutorial.md)複寫。 |
| 威脅偵測 |如需相關資訊，請參閱[在 AZURE SQL 受控執行個體中設定威脅偵測](../managed-instance/threat-detection-configure.md)。|
| 長期備份保留期 | 如需相關資訊，請參閱在[AZURE SQL 受控執行個體中設定長期備份保留期](../managed-instance/long-term-backup-retention-configure.md)，此功能目前為有限的公開預覽。 | 

---

## <a name="sql-managed-instance-new-features-and-known-issues"></a>SQL 受控執行個體新功能和已知問題

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL 受控執行個體 H2 2019 更新

- [服務輔助子網](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/)設定是一種安全且方便的方式，可管理您控制資料流量的子網設定，而 SQL 受控執行個體可確保管理流量不中斷。
- [透明資料加密（TDE）與攜帶您自己的金鑰（BYOK）](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/)可讓您自備金鑰（BYOK）案例進行待用資料保護，並可讓組織將金鑰和資料的管理責任分開。
- [自動容錯移轉群組](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/)可讓您將主要實例的所有資料庫複寫至另一個區域中的次要實例。
- [全域追蹤旗標](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)可讓您設定 SQL 受控執行個體行為。

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL 受控執行個體 H1 2019 更新

下列功能已在 H1 2019 的 SQL 受控執行個體部署模型中啟用：
  - 支援訂用帳戶的訂用帳戶， <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio 訂閱者的每月點數</a>，以及增加的[地區限制](../managed-instance/resource-limits.md#regional-resource-limitations)。
  - 支援<a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">sharepoint 2016 和 sharepoint 2019</a>和<a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central。</a>
  - 建立具有<a href="https://aka.ms/managed-instance-collation">實例層級定序</a>的受控實例，以及您選擇的<a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">時區</a>。
  - 受控實例現在會受到[內建防火牆](../managed-instance/management-endpoint-verify-built-in-firewall.md)的保護。
  - 設定 SQL 受控執行個體以使用[公用端點](../managed-instance/public-endpoint-configure.md)、 [Proxy 覆寫](connectivity-architecture.md#connection-policy)連線以取得更佳的網路效能、 <a href="https://aka.ms/four-cores-sql-mi-update">4 虛擬核心在第5代硬體產生</a>，或<a href="https://aka.ms/managed-instance-configurable-backup-retention">將備份保留設定為35天</a>以進行時間點還原。 [長期備份保留期](long-term-retention-overview.md#sql-managed-instance-support)（最多10年）目前僅限公開預覽。  
  - 新功能可讓您<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">使用 PowerShell 將資料庫異地還原至另一個資料中心</a>、[重新命名資料庫](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[刪除虛擬叢集](../managed-instance/virtual-cluster-delete.md)。
  - 新的內建[實例參與者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor)可讓責任區隔（SoD）符合安全性原則，並遵循企業標準。
  - SQL 受控執行個體適用于 GA （US Gov 德克薩斯州、US Gov 亞利桑那州）的下列 Azure Government 區域，以及中國北部2和中國東部2。 這也適用于下列公用區域：澳大利亞中部、澳大利亞中部2、巴西南部、法國南部、阿拉伯聯合大公國中部、阿拉伯聯合大公國北部、南非北部、南非西部。

### <a name="known-issues"></a>已知問題

|問題  |探索日期  |狀態  |解決日期  |
|---------|---------|---------|---------|
|[還原不含總和檢查碼的手動備份可能會失敗](#restoring-manual-backup-without-checksum-might-fail)|2020 年 5 月|已解決|2020 年 6 月|
|[代理程式在修改、停用或啟用現有作業時變得沒有回應](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020 年 5 月|已解決|2020 年 6 月|
|[資源群組的許可權未套用至 SQL 受控執行個體](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020年2月|有因應措施||
|[透過入口網站針對容錯移轉群組進行手動容錯移轉的限制](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|有因應措施||
|[SQL Agent 角色需要非系統管理員 (sysadmin) 登入的明確 EXECUTE 許可權](#in-memory-oltp-memory-limits-are-not-applied)|12月2019|有因應措施||
|[代理程式進程重新開機可能會中斷 SQL 代理程式作業](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|12月2019|已解決|Mar 2020|
|[SSDT 中不支援 Azure AD 登入和使用者](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|2019年11月|無因應措施||
|[記憶體內部 OLTP 記憶體限制不適用](#in-memory-oltp-memory-limits-are-not-applied)|2019年10月|有因應措施||
|[嘗試移除不是空的檔案時傳回錯誤的錯誤](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019年10月|有因應措施||
|[進行中的資料庫還原會封鎖變更服務層級和建立實例作業](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|有因應措施||
|[業務關鍵服務層級上的 Resource Governor 可能需要在容錯移轉之後重新設定](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|有因應措施||
|[在服務層升級之後，必須重新初始化跨資料庫 Service Broker 對話方塊](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019年8月|有因應措施||
|[不支援 Azure AD 登入類型的模擬](#impersonation-of-azure-ad-login-types-is-not-supported)|2019年7月|無因應措施||
|[@querysp_send_db_mail 中不支援參數](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|無因應措施||
|[必須在異地容錯移轉之後重新設定異動複寫](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|無因應措施||
|[還原作業期間會使用暫存資料庫](#temporary-database-is-used-during-restore-operation)||有因應措施||
|[已重新建立 TEMPDB 結構和內容](#tempdb-structure-and-content-is-re-created)||無因應措施||
|[小型資料庫檔案造成儲存空間超出限制](#exceeding-storage-space-with-small-database-files)||有因應措施||
|[顯示的 GUID 值，而不是資料庫名稱](#guid-values-shown-instead-of-database-names)||有因應措施||
|[錯誤記錄檔不會保存](#error-logs-arent-persisted)||無因應措施||
|[不支援相同實例內兩個資料庫的交易範圍](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||有因應措施|Mar 2020|
|[CLR 模組與連結的伺服器有時候無法參考本機 IP 位址](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||有因應措施||
|從 Azure Blob 儲存體還原資料庫之後，未使用 DBCC CHECKDB 確認資料庫一致性。||已解決|2019年11月|
|如果源資料庫包含記憶體內部 OLTP 物件，則從業務關鍵層到一般用途層的時間點資料庫還原將不會成功。||已解決|2019年10月|
|使用安全連線之外部（非 Azure）郵件伺服器的 Database mail 功能||已解決|2019年10月|
|SQL 受控執行個體中不支援自主資料庫||已解決|2019年8月|


### <a name="restoring-manual-backup-without-checksum-might-fail"></a>還原不含總和檢查碼的手動備份可能會失敗

在某些情況下，手動備份在不含總和檢查碼的受控實例上進行的資料庫可能無法還原。 在這種情況下，請重試還原備份，直到成功為止。

因應**措施：在**啟用總和檢查碼的受控實例上，手動備份資料庫。

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>代理程式在修改、停用或啟用現有作業時變得沒有回應

在某些情況下，修改、停用或啟用現有的作業可能會導致代理程式變成沒有回應。 此問題會在偵測時自動降低，導致代理程式進程重新開機。

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>資源群組的許可權未套用至 SQL 受控執行個體

當 SQL 受控執行個體參與者 Azure 角色套用至資源群組（RG）時，它不會套用至 SQL 受控執行個體且不會有任何作用。

因應**措施：為**訂用帳戶層級的使用者設定 SQL 受控執行個體參與者角色。

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>透過入口網站針對容錯移轉群組進行手動容錯移轉的限制

如果容錯移轉群組橫跨不同 Azure 訂用帳戶或資源群組中的實例，則無法從容錯移轉群組中的主要實例起始手動容錯移轉。

因應**措施：透過**入口網站，從地理位置次要實例起始容錯移轉。

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL Agent 角色需要非系統管理員 (sysadmin) 登入的明確 EXECUTE 許可權

如果將非系統管理員（sysadmin）登入加入任何[SQL Agent 固定資料庫角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)中，就會有一個問題，即必須授與主要預存程式的明確執行許可權，才能讓這些登入工作。 如果發生此問題，將會顯示錯誤訊息「物件 <上的執行許可權被拒絕 object_name> （Microsoft SQL Server，錯誤：229）」。

因應**措施：當**您將登入加入至 SQL Agent 固定資料庫角色（SQLAgentUserRole、SQLAgentReaderRole 或 SQLAgentOperatorRole）之後，針對加入這些角色的每個登入，請執行下列 t-sql 腳本，將執行許可權明確授與所列的預存程式。

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>代理程式進程重新開機可能會中斷 SQL 代理程式作業

**（2020年3月解決）** SQL 代理程式會在每次作業啟動時建立新的會話，逐漸增加記憶體耗用量。 為了避免達到內部記憶體限制（這會封鎖執行排程工作），Agent 進程會在其記憶體耗用量達到閾值後重新開機。 這可能會導致在重新開機時中斷執行的作業。

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>記憶體內部 OLTP 記憶體限制不適用

在某些情況下，商務關鍵服務層級不會正確地套用[記憶體優化物件的最大記憶體限制](../managed-instance/resource-limits.md#in-memory-oltp-available-space)。 SQL 受控執行個體可以讓工作負載針對記憶體內部 OLTP 作業使用更多記憶體，這可能會影響實例的可用性和穩定性。 達到限制的記憶體內部 OLTP 查詢可能不會立即失敗。 這個問題很快就會修正。 使用更多記憶體內部 OLTP 記憶體的查詢，如果達到[限制](../managed-instance/resource-limits.md#in-memory-oltp-available-space)，將會很快失敗。

因應**措施：使用** [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) [監視記憶體內部 OLTP 儲存體使用量](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-monitor-space)，以確保工作負載不會使用超過可用的記憶體。 增加相依于虛擬核心數目的記憶體限制，或優化您的工作負載以使用較少的記憶體。
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>嘗試移除不是空的檔案時傳回錯誤的錯誤

SQL Server 和 SQL 受控執行個體[不允許使用者捨棄不是空的](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)檔案。 如果您嘗試使用語句移除非空的資料檔案 `ALTER DATABASE REMOVE FILE` ， `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 將不會立即傳回此錯誤。 SQL 受控執行個體會繼續嘗試卸載檔案，且作業將會在30分鐘後失敗 `Internal server error` 。

因應**措施：使用**命令移除檔案的內容 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 。 如果這是檔案群組中的唯一檔案，您必須先刪除與這個檔案群組相關聯的資料表或資料分割中的資料，然後再壓縮檔案，並選擇性地將此資料載入另一個資料表/資料分割。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>進行中的資料庫還原會封鎖變更服務層級和建立實例作業

持續 `RESTORE` 的語句、資料移轉服務遷移程式和內建的還原時間點，將會封鎖更新服務層級或調整現有實例的大小，並建立新的實例，直到還原程式完成為止。 

還原程式會在執行還原進程的相同子網中，封鎖受控實例和實例集區上的這些作業。 實例集區中的實例不受影響。 建立或變更服務層級作業不會失敗或超時。一旦還原程式完成或取消，就會繼續進行。

因應**措施：等到**還原程式完成，或在建立或更新服務層級作業的優先順序較高時取消還原程式。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>業務關鍵服務層級上的 Resource Governor 可能需要在容錯移轉之後重新設定

[ [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) ] 功能可讓您限制指派給使用者工作負載的資源，可能會在容錯移轉或使用者起始的服務層級變更（例如，最大 vCore 或最大實例儲存體大小的變更）時，不正確地分類某些使用者工作負載。

因應**措施：定期執行，** `ALTER RESOURCE GOVERNOR RECONFIGURE` 或當做 sql 代理程式作業的一部分，當實例啟動時，如果您使用[Resource Governor](/sql/relational-databases/resource-governor/resource-governor)，就會執行 sql 工作。

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>在服務層升級之後，必須重新初始化跨資料庫 Service Broker 對話方塊

跨資料庫 Service Broker 對話方塊會在變更服務層級作業之後，停止將訊息傳遞至其他資料庫中的服務。 訊息不會*遺失*，而且可以在寄件者佇列中找到。 在 SQL 受控執行個體中，任何虛擬核心或實例儲存體大小的變更，都會導致 `service_broke_guid` [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) view 中的值針對所有資料庫進行變更。 任何 `DIALOG` 使用[BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql)語句所建立的會參考其他資料庫中的服務代理程式，都會停止將訊息傳遞至目標服務。

因應**措施：在**更新服務層之前，停止使用跨資料庫 Service Broker 對話交談的任何活動，然後在之後重新初始化。 如果有剩餘的訊息在服務層級變更後無法傳遞，請從來源佇列讀取訊息，然後將它們重新傳送至目標佇列。

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>不支援 Azure AD 登入類型的模擬

`EXECUTE AS USER` `EXECUTE AS LOGIN` 不支援使用或下列 Azure Active Directory （Azure AD）主體的模擬：
-   別名 Azure AD 使用者。 在此情況下，會傳回下列錯誤： `15517` 。
- 根據 Azure AD 的應用程式或服務主體，Azure AD 登入和使用者。 在此情況下，會傳回下列錯誤： `15517` 和 `15406` 。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@querysp_send_db_mail 中不支援參數

`@query` [Sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql)程式中的參數無法使用。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>必須在異地容錯移轉之後重新設定異動複寫

如果已在自動容錯移轉群組中的資料庫上啟用異動複寫，SQL 受控執行個體系統管理員必須清除舊主要複本上的所有發行集，並在容錯移轉至另一個區域之後，在新的主要上重新設定它們。 如需詳細資訊，請參閱[Replication](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)。

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 中不支援 Azure AD 登入和使用者

SQL Server Data Tools 不完全支援 Azure AD 登入和使用者。

### <a name="temporary-database-is-used-during-restore-operation"></a>還原作業期間會使用暫存資料庫

在 SQL 受控執行個體中還原資料庫時，還原服務會先建立具有所需名稱的空資料庫，以在實例上配置名稱。 經過一段時間之後，就會卸載此資料庫，並啟動實際資料庫的還原。 

處於*還原*狀態的資料庫會暫時有隨機的 GUID 值，而不是名稱。 一旦還原程式完成，暫存名稱就會變更為語句中所指定的名稱 `RESTORE` 。 

在初始階段中，使用者可以存取空的資料庫，甚至在此資料庫中建立資料表或載入資料。 當還原服務啟動第二個階段時，將會卸載此暫存資料庫。

因應**措施：除非**您看到還原已完成，否則請不要存取您要還原的資料庫。

### <a name="tempdb-structure-and-content-is-re-created"></a>已重新建立 TEMPDB 結構和內容

`tempdb`資料庫一律會分割成12個資料檔案，而且無法變更檔案結構。 無法變更每個檔案的大小上限，而且無法將新檔案新增至 `tempdb` 。 `Tempdb`當實例開始或故障時，一律會重新建立為空的資料庫，而且不會保留在中進行的任何變更 `tempdb` 。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型資料庫檔案造成儲存空間超出限制

`CREATE DATABASE`、 `ALTER DATABASE ADD FILE` 和 `RESTORE DATABASE` 語句可能會失敗，因為實例可以達到 Azure 儲存體的限制。

SQL 受控執行個體的每個一般用途實例最多可保留 35 TB 的儲存體給 Azure Premium 磁碟空間。 每個資料庫檔案都會放在個別的實體磁片上。 磁碟大小可以是 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁片上未使用的空間不會收費，但 Azure Premium 磁片大小的總計總和不能超過 35 TB。 在某些情況下，不需要 8 TB 的受控實例總計可能會超過 35 TB 的 Azure 儲存體大小限制，因為內部分散。

例如，SQL 受控執行個體的一般用途實例可能有一個大小為 1.2 TB 的大型檔案，放在 4 TB 的磁片上。 它也可能有248個檔案，每個檔案都是 1 GB，而且放在不同的 128 GB 磁片上。 在此範例中：

- 配置的磁碟儲存體大小總計為 1 x 4 TB + 248 x 128 GB = 35 TB。
- 為執行個體上的資料庫保留的大小總計為 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

這個範例說明，在某些情況下，由於檔案的特定散發，因此 SQL 受控執行個體的實例可能會達到 35 TB 的限制，而當您可能不會預期時，會保留給附加的 Azure Premium 磁片。

在此範例中，只要未新增新檔案，現有的資料庫就會繼續正常執行，而且不會有任何問題就可以成長。 無法建立或還原新的資料庫，因為新磁片磁碟機的空間不足，即使所有資料庫的大小總計未達到實例大小限制也一樣。 在此情況下傳回的錯誤並不清楚。

您可以使用系統檢視來[識別剩餘](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)的檔案數目。 如果您達到此限制，請嘗試[使用 DBCC SHRINKFILE 語句來清空和刪除一些較小](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)的檔案，或切換至[不具有此限制的業務關鍵層](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="guid-values-shown-instead-of-database-names"></a>顯示的 GUID 值，而不是資料庫名稱

數個系統檢視、效能計數器、錯誤訊息、XEvents 與錯誤記錄檔項目都會顯示 GUID 資料庫識別碼，而非實際資料庫名稱。 不要依賴這些 GUID 識別碼，因為未來會以實際的資料庫名稱來取代它們。

因應**措施：使用**sys.databases view 來解析實體資料庫名稱中的實際資料庫名稱（以 GUID 資料庫識別碼的形式指定）：

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>錯誤記錄檔不會保存

SQL 受控執行個體中提供的錯誤記錄檔不會保存下來，而且其大小不會包含在最大儲存體限制中。 如果發生容錯移轉，可能會自動清除錯誤記錄。 錯誤記錄檔歷程記錄中可能有間距，因為 SQL 受控執行個體已在數部虛擬機器上移動數次。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>不支援相同實例內兩個資料庫的交易範圍

**（2020年3月解決）**`TransactionScope`如果兩個查詢傳送至相同交易範圍下相同實例中的兩個資料庫，.net 中的類別將無法使用：

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

因應措施 **（自2020年3月起不需要）**：使用[sqlconnection.changedatabase （字串）](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)來使用連接內容中的另一個資料庫，而不是使用兩個連接。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模組與連結的伺服器有時候無法參考本機 IP 位址

SQL 受控執行個體中的 CLR 模組與參考目前實例的連結伺服器或分散式查詢有時無法解析本機實例的 IP。 此錯誤為暫時性問題。

因應措施：可能的話，在 CLR 模組中**使用內容連接**。

## <a name="updates"></a>更新

如需 SQL Database 更新和增強功能的清單，請參閱[SQL Database 服務更新](https://azure.microsoft.com/updates/?product=sql-database)。

如需所有 Azure 服務的更新和改善，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="contribute-to-content"></a>參與內容

若要參與 Azure SQL 檔，請參閱檔[參與者指南](https://docs.microsoft.com/contribute/)。
