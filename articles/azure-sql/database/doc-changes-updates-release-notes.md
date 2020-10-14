---
title: 新功能
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 瞭解 Azure SQL Database & SQL 受控執行個體的新功能和檔增強功能。
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: sstein
ms.openlocfilehash: ebbdd103350e1de36d45ecf84acf15d477fa34db
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058126"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Azure SQL Database & SQL 受控執行個體有哪些新功能？
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文列出目前處於公開預覽狀態 Azure SQL Database 和 Azure SQL 受控執行個體功能。 如 SQL Database 和 SQL 受控執行個體更新和改進，請參閱 [SQL Database & SQL 受控執行個體服務更新](https://azure.microsoft.com/updates/?product=sql-database)。 如需其他 Azure 服務的更新和增強功能，請參閱 [服務更新](https://azure.microsoft.com/updates)。

## <a name="whats-new"></a>新功能

Azure SQL Database 和 Azure SQL 受控執行個體的檔已分割成不同的區段。 我們也已更新從 *Azure SQL Database 受控實例* 將受控實例參考至 *Azure SQL 受控執行個體*的方式。

我們已完成這項工作，因為單一資料庫和受控實例之間的部分特性和功能會有很大的差異，並在個別的共用文章中說明 Azure SQL Database 和 Azure SQL 受控執行個體之間的複雜細微差異。

這項有關不同 Azure SQL 產品的說明應該簡化並簡化在 Azure 中使用 SQL Server database engine 的程式，無論是 Azure SQL Database 中的單一受控資料庫、Azure SQL 受控執行個體中裝載多個資料庫的完整受控實例，或是裝載在 Azure 虛擬機器上的熟悉內部部署 SQL Server 產品。

請考慮這是進行中的工作，但尚未更新所有文章。 例如，Transact-sql (T-sql) 語句、預存程式以及 Azure SQL Database 與 Azure SQL 受控執行個體之間共用的許多功能的檔尚未完成，因此我們感謝您耐心等候，因為我們會持續澄清內容。 

下表提供術語變更的快速比較： 


|**新詞彙**  | **上一個詞彙**  |**說明** |
|---------|---------|---------|
|**Azure SQL 受控執行個體** | Azure SQL Database *受控實例*| Azure SQL 受控執行個體是其在 Azure SQL 系列內專屬的產品，而不只是 Azure SQL Database 內的部署選項。 | 
|**Azure SQL Database**|Azure SQL Database *單一資料庫*| 除非明確指定，否則產品名稱 Azure SQL Database 包含部署到彈性集區的單一資料庫和資料庫。 |
|**Azure SQL Database**|Azure SQL Database*彈性集*區| 除非明確指定，否則產品名稱 Azure SQL Database 包含部署到彈性集區的單一資料庫和資料庫。  |
|**Azure SQL Database** |Azure SQL Database | 雖然此詞彙保持不變，但現在僅適用于單一資料庫和彈性集區部署，且不包含受控實例。 |
| **Azure SQL**| N/A | 這指的是 Azure 中可用的 SQL Server 資料庫引擎產品系列： Azure SQL Database、Azure SQL 受控執行個體，以及 Azure Vm 上的 SQL Server。 | 

## <a name="features-in-public-preview"></a>公開預覽中的功能

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| 功能 | 詳細資料 |
| ---| --- |
| 使用單一資料庫和彈性集區加速資料庫復原 | 如需詳細資訊，請參閱 [加速資料庫](../accelerated-database-recovery.md)復原。|
| 資料探索與分類  |如需詳細資訊，請參閱 [Azure SQL Database 和 Azure Synapse Analytics 資料探索 & 分類](data-discovery-and-classification-overview.md)。|
| 彈性資料庫工作 (預覽)  | 如需詳細資訊，請參閱 [建立、設定和管理彈性作業](elastic-jobs-overview.md)。 |
| 彈性查詢 | 如需詳細資訊，請參閱 [彈性查詢總覽](elastic-query-overview.md)。 |
| 彈性交易 | [跨雲端資料庫的分散式交易](elastic-transactions-overview.md)。 |
| Azure 入口網站中的查詢編輯器 |如需詳細資訊，請參閱 [使用 Azure 入口網站的 SQL 查詢編輯器連接和查詢資料](connect-query-portal.md)。|
| 具有單一資料庫和彈性集區的 R services/機器學習服務 |如需詳細資訊，請參閱 [Azure SQL Database 中的 Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)。|
|SQL Analytics|如需詳細資訊，請參閱 [Azure SQL 分析](../../azure-monitor/insights/azure-sql.md)。|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Azure SQL 受控執行個體](#tab/managed-instance)

| 功能 | 詳細資料 |
| ---| --- |
| <a href="/azure/azure-sql/database/elastic-transactions-overview">分散式交易</a> | 受控實例之間的分散式交易。 |
| <a href="/azure/sql-database/sql-database-instance-pools">執行個體集區</a> | 方便且符合成本效益的方式，將較小的 SQL 實例遷移至雲端。 |
| <a href="https://aka.ms/managed-instance-aadlogins">實例層級 Azure AD 伺服器主體 (登入) </a> | 使用 <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> 語句來建立實例層級的登入。 |
| [異動複寫](../managed-instance/replication-transactional-overview.md) | 將資料表中的變更複寫到 SQL 受控執行個體、SQL Database 或 SQL Server 中的其他資料庫。 或在 SQL 受控執行個體或 SQL Server 的其他實例中變更部分資料列時，更新您的資料表。 如需詳細資訊，請參閱 [在 AZURE SQL 受控執行個體中設定](../managed-instance/replication-between-two-instances-configure-tutorial.md)複寫。 |
| 威脅偵測 |如需詳細資訊，請參閱 [在 AZURE SQL 受控執行個體中設定威脅偵測](../managed-instance/threat-detection-configure.md)。|
| 長期備份保留期 | 如需詳細資訊，請參閱在 [AZURE SQL 受控執行個體中設定長期備份保留期](../managed-instance/long-term-backup-retention-configure.md)，其目前處於有限的公開預覽狀態。 | 

---

## <a name="new-features"></a>新功能

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL 受控執行個體 H2 2019 更新

- [服務輔助子網](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) 設定是管理子網設定的安全且方便的方式，可讓您在 SQL 受控執行個體確保管理流量不中斷的流程時，控制資料流量。
- [透明資料加密 (TDE) 與攜帶您自己的金鑰 (BYOK) ](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) 可為待用資料保護提供攜帶您自己的金鑰 (BYOK) 案例，並可讓組織將金鑰和資料的管理責任分開。
- [自動容錯移轉群組](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) 可讓您將所有資料庫從主要實例複寫到另一個區域中的次要實例。
- [全域追蹤旗標](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) 可讓您設定 SQL 受控執行個體行為。

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL 受控執行個體 H1 2019 更新

下列功能已在 H1 2019 的 SQL 受控執行個體部署模型中啟用：
  - 支援訂用帳戶， <a href="https://aka.ms/sql-mi-visual-studio-subscribers"> Visual Studio 訂閱者的每月點數 </a> ，以及更多的 [區域限制](../managed-instance/resource-limits.md#regional-resource-limitations)。
  - 支援 <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> sharepoint 2016 和 sharepoint 2019 </a> 與 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central。 </a>
  - 建立具有 <a href="https://aka.ms/managed-instance-collation">實例層級定序</a> 和您所選擇 <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">時區</a> 的受控實例。
  - 受控實例現在會使用 [內建防火牆](../managed-instance/management-endpoint-verify-built-in-firewall.md)來保護。
  - 將 SQL 受控執行個體設定為使用 [公用端點](../managed-instance/public-endpoint-configure.md)、 [Proxy 覆寫](connectivity-architecture.md#connection-policy) 連線以取得較佳的網路效能、 <a href="https://aka.ms/four-cores-sql-mi-update"> 4 虛擬核心第5代硬體世代</a> 或設定 <a href="https://aka.ms/managed-instance-configurable-backup-retention">備份保留最多35天</a> 以進行時間點還原。 [長期備份保留](long-term-retention-overview.md#sql-managed-instance-support) (最多10年的) 目前處於有限的公開預覽狀態。  
  - 新功能可讓您使用 PowerShell、[重新命名資料庫](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[刪除虛擬叢集](../managed-instance/virtual-cluster-delete.md)，將<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">資料庫異地還原至另一個資料中心</a>。
  - 新的內建 [實例參與者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) 可將責任 (SoD) 合規性與安全性原則和符合企業標準的合規性分開。
  - 您可以在下列 Azure Government 區域中取得 SQL 受控執行個體，以 GA (US Gov 德克薩斯州、US Gov 亞利桑那州) 以及中國北部2和中國東部2。 也可在下列公用區域中使用：澳大利亞中部、澳大利亞中部2、巴西南部、法國南部、阿拉伯聯合大公國中部、阿拉伯聯合大公國北部、南非北部、南非西部。

## <a name="known-issues"></a>已知問題

|問題  |探索日期  |狀態  |解決日期  |
|---------|---------|---------|---------|
|[從伺服器信任群組移除受控執行個體之後，可以執行分散式交易](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Sep 2020|有因應措施||
|[受控執行個體調整作業之後，無法執行分散式交易](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Sep 2020|有因應措施||
|[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)在 azure SQL 中， `BACKUP` / `RESTORE` 受控執行個體中的語句無法使用 Azure AD 管理身分識別來向 Azure 儲存體進行驗證|Sep 2020|有因應措施||
|[服務主體無法存取 Azure AD 和 AKV](#service-principal-cannot-access-azure-ad-and-akv)|2020年8月|有因應措施||
|[還原沒有總和檢查碼的手動備份可能會失敗](#restoring-manual-backup-without-checksum-might-fail)|2020 年 5 月|已解決|2020 年 6 月|
|[在修改、停用或啟用現有的作業時，代理程式變成沒有回應](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020 年 5 月|已解決|2020 年 6 月|
|[資源群組的許可權未套用至 SQL 受控執行個體](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020年2月|有因應措施||
|[透過入口網站進行容錯移轉群組的手動容錯移轉限制](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|有因應措施||
|[SQL Agent 角色需要非系統管理員 (sysadmin) 登入的明確 EXECUTE 許可權](#in-memory-oltp-memory-limits-are-not-applied)|12月2019|有因應措施||
|[代理程式進程重新開機可能會中斷 SQL Agent 作業](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|12月2019|已解決|三月2020|
|[SSDT 中不支援 Azure AD 登入和使用者](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|11月2019|沒有解決方法||
|[記憶體內部 OLTP 記憶體限制不適用](#in-memory-oltp-memory-limits-are-not-applied)|10月2019|有因應措施||
|[嘗試移除非空白的檔案時傳回錯誤的錯誤](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|10月2019|有因應措施||
|[進行中的資料庫還原封鎖了變更服務層級和建立實例作業](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|有因應措施||
|[可能需要在容錯移轉後重新設定商務關鍵服務層級上的 Resource Governor](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|有因應措施||
|[服務層升級之後，必須重新初始化跨資料庫的 Service Broker 對話方塊](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019年8月|有因應措施||
|[不支援模擬 Azure AD 的登入類型](#impersonation-of-azure-ad-login-types-is-not-supported)|2019年7月|沒有解決方法||
|[@query sp_send_db_mail 中不支援參數](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|沒有解決方法||
|[在異地容錯移轉之後，必須重新設定異動複寫](#transactional-replication-must-be-reconfigured-after-geo-failover)|三月2019|沒有解決方法||
|[在還原作業期間使用暫存資料庫](#temporary-database-is-used-during-restore-operation)||有因應措施||
|[TEMPDB 結構和內容已重新建立](#tempdb-structure-and-content-is-re-created)||沒有解決方法||
|[小型資料庫檔案造成儲存空間超出限制](#exceeding-storage-space-with-small-database-files)||有因應措施||
|[顯示的 GUID 值，而不是資料庫名稱](#guid-values-shown-instead-of-database-names)||有因應措施||
|[錯誤記錄不會保存](#error-logs-arent-persisted)||沒有解決方法||
|[不支援相同實例內兩個資料庫的交易範圍](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||有因應措施|三月2020|
|[CLR 模組與連結的伺服器有時候無法參考本機 IP 位址](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||有因應措施||
|從 Azure Blob 儲存體還原資料庫之後，未使用 DBCC CHECKDB 驗證資料庫一致性。||已解決|11月2019|
|如果源資料庫包含記憶體內部 OLTP 物件，則從商務關鍵層還原至一般用途層的時間點資料庫還原將不會成功。||已解決|10月2019|
|具有外部 (非 Azure) mail 伺服器使用安全連線的 Database mail 功能||已解決|10月2019|
|SQL 受控執行個體不支援包含的資料庫||已解決|2019年8月|

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>從伺服器信任群組移除受控執行個體之後，可以執行分散式交易

[伺服器信任群組](https://docs.microsoft.com/azure/azure-sql/managed-instance/server-trust-group-overview) 是用來在受管理的實例（執行 [分散式交易](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview)的必要條件）之間建立信任。 從伺服器信任群組移除受控執行個體或刪除群組之後，您仍然可以執行分散式交易。 您可以套用此因應措施，以確保分散式交易已停用，且在受控執行個體上是 [使用者起始的手動容錯移轉](https://docs.microsoft.com/azure/azure-sql/managed-instance/user-initiated-failover) 。

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>受控執行個體調整作業之後，無法執行分散式交易

受控執行個體包含變更服務層級或虛擬核心數目的調整作業，會在後端重設伺服器信任群組設定，並停用執行中的 [分散式交易](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview)。 若要解決此問題，請在 Azure 入口網站上刪除並建立新的 [伺服器信任群組](https://docs.microsoft.com/azure/azure-sql/managed-instance/server-trust-group-overview) 。

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT 和備份/還原語句無法使用受控識別來存取 Azure 儲存體

Bulk insert 語句無法使用 `DATABASE SCOPED CREDENTIAL` With 受控識別來向 Azure 儲存體進行驗證。 若要解決此問題，請切換到共用存取簽章驗證。 下列範例將無法在 Azure SQL (資料庫和受控執行個體) 上運作：

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

因應**措施：使用**[共用存取簽章來對儲存體進行驗證](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver15#f-importing-data-from-a-file-in-azure-blob-storage)。

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>服務主體無法存取 Azure AD 和 AKV

在某些情況下，用來存取 Azure AD 和 Azure Key Vault (AKV) 服務的服務主體可能會有問題。 因此，此問題會影響 Azure AD authentication 和透明資料庫加密 (TDE) 與 SQL 受控執行個體的使用方式。 這可能是間歇性的連線問題，或是無法執行語句，例如從外部提供者建立登入/使用者，或是以登入/使用者身分執行。 在新的 Azure SQL 受控執行個體上使用客戶管理的金鑰來設定 TDE，在某些情況下可能也無法運作。

因應**措施：若**要在執行任何 update 命令之前防止 SQL 受控執行個體發生此問題，或在更新命令之後遇到此問題，請移至 Azure 入口網站，存取 SQL 受控執行個體 Active Directory 系統[管理](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure?tabs=azure-powershell#azure-portal)分頁。 確認您是否可以看到錯誤訊息「受控執行個體需要服務主體才能存取 Azure Active Directory。 按一下這裡以建立服務主體」。 如果您遇到這個錯誤訊息，請按一下該訊息，然後依照所提供的逐步指示進行，直到解決此錯誤為止。

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>還原沒有總和檢查碼的手動備份可能會失敗

在特定情況下，手動備份在受控實例上建立的資料庫（不含總和檢查碼）可能無法還原。 在這種情況下，請重試還原備份，直到成功為止。

因應**措施：在**已啟用總和檢查碼的受控實例上手動備份資料庫。

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>在修改、停用或啟用現有的作業時，代理程式變成沒有回應

在某些情況下，修改、停用或啟用現有的作業可能會導致代理程式無法回應。 此問題會在偵測時自動緩和，導致代理程式重新開機。

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>資源群組的許可權未套用至 SQL 受控執行個體

將 SQL 受控執行個體參與者 Azure 角色套用至 (RG) 的資源群組時，不會套用至 SQL 受控執行個體且沒有任何作用。

因應**措施：為**訂用帳戶層級的使用者設定 SQL 受控執行個體參與者角色。

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>透過入口網站進行容錯移轉群組的手動容錯移轉限制

如果容錯移轉群組跨越不同 Azure 訂用帳戶或資源群組中的實例，則無法從容錯移轉群組中的主要實例起始手動容錯移轉。

因應**措施：透過**入口網站從異地次要實例起始容錯移轉。

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL Agent 角色需要非系統管理員 (sysadmin) 登入的明確 EXECUTE 許可權

如果將非系統管理員（sysadmin）登入加入至任何 [SQL Agent 固定資料庫角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)，則有一個問題，就是必須將明確的 EXECUTE 許可權授與主要預存程式，這些登入才能運作。 如果遇到這個問題，將會顯示錯誤訊息「物件上的 EXECUTE 許可權被拒 <object_name> (Microsoft SQL Server，將會顯示錯誤： 229) 」。

因應**措施：將**登入加入至 SQL Agent 固定資料庫角色 (SQLAgentUserRole、SQLAgentReaderRole 或 SQLAgentOperatorRole) 中，針對每個新增至這些角色的登入，執行下列 t-sql 腳本，以明確地將執行許可權授與所列出的預存程式。

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>代理程式進程重新開機可能會中斷 SQL Agent 作業

** (于2020年3月解決) ** SQL Agent 會在每次啟動作業時建立新的會話，並逐漸增加記憶體耗用量。 為了避免達到內部記憶體限制（這會封鎖排程工作的執行），Agent 進程會在其記憶體耗用量達到閾值時重新開機。 這可能會導致在重新開機時中斷執行作業。

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>記憶體內部 OLTP 記憶體限制不適用

在某些情況下，商務關鍵服務層級不會正確地套用 [記憶體優化物件的最大記憶體限制](../managed-instance/resource-limits.md#in-memory-oltp-available-space) 。 SQL 受控執行個體可能會讓工作負載針對記憶體內部 OLTP 作業使用更多記憶體，這可能會影響實例的可用性和穩定性。 達到限制的記憶體內部 OLTP 查詢可能不會立即失敗。 此問題即將修正。 使用更多記憶體內部 OLTP 記憶體的查詢如果達到 [限制](../managed-instance/resource-limits.md#in-memory-oltp-available-space)，將會更快失敗。

因應**措施：使用** [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) [監視記憶體內部 OLTP 儲存體使用量](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-monitor-space)，以確保工作負載不會使用超過可用的記憶體。 提高相依于虛擬核心數目的記憶體限制，或將您的工作負載優化以使用較少的記憶體。
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>嘗試移除非空白的檔案時傳回錯誤的錯誤

SQL Server 和 SQL 受控執行個體 [不允許使用者捨棄非空白](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)的檔案。 如果您嘗試使用語句來移除非空白的資料檔案 `ALTER DATABASE REMOVE FILE` ，則 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 不會立即傳回錯誤。 SQL 受控執行個體將繼續嘗試卸載檔案，而且在30分鐘之後，作業將會失敗 `Internal server error` 。

因應**措施：使用**命令移除檔案的內容 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 。 如果這是檔案群組中唯一的檔案，則在壓縮檔案之前，您必須先刪除與這個檔案群組相關聯之資料表或資料分割中的資料，並選擇性地將此資料載入另一個資料表/分割區。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>進行中的資料庫還原封鎖了變更服務層級和建立實例作業

進行 `RESTORE` 中的語句、資料移轉服務遷移程式和內建的時間點還原，將會封鎖更新服務層級或調整現有實例的大小，以及建立新的實例，直到還原程式完成為止。 

還原程式將會在執行還原程式的相同子網中，封鎖受管理的實例和實例集區上的這些作業。 實例集區中的實例不會受到影響。 建立或變更服務層級作業將不會失敗或超時。當還原程式完成或取消時，它們就會繼續進行。

因應**措施：等候**還原程式完成，如果建立或更新服務層作業的優先順序較高，則取消還原程式。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>可能需要在容錯移轉後重新設定商務關鍵服務層級上的 Resource Governor

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor)的功能，可讓您限制指派給使用者工作負載的資源，可能會在容錯移轉之後或使用者起始的服務層級變更時，不正確地將某些使用者工作負載分類 (例如，最大 vCore 或最大實例儲存體大小) 的變更。

因應**措施：** `ALTER RESOURCE GOVERNOR RECONFIGURE` 如果您使用[Resource Governor](/sql/relational-databases/resource-governor/resource-governor)，請定期執行，或做為執行 SQL 工作的 SQL Agent 作業的一部分。

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>服務層升級之後，必須重新初始化跨資料庫的 Service Broker 對話方塊

在變更服務層級作業之後，跨資料庫的 Service Broker 對話方塊將會停止將訊息傳遞至其他資料庫中的服務。 訊息不會 *遺失*，而且可以在寄件者佇列中找到。 SQL 受控執行個體中虛擬核心或實例儲存體大小的任何變更，將會 `service_broke_guid` 針對所有資料庫變更 [sys. 資料庫](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 視圖中的值。 任何 `DIALOG` 使用參考其他資料庫中的服務代理程式之 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 語句所建立的，將會停止將訊息傳遞至目標服務。

因應**措施：在**更新服務層之前，請先停止使用跨資料庫 Service Broker 對話交談的任何活動，然後再重新初始化。 如果有剩餘的訊息在服務層變更後未傳遞，請從來源佇列中讀取訊息，並將它們重新傳送至目標佇列。

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>不支援模擬 Azure AD 的登入類型

`EXECUTE AS USER` `EXECUTE AS LOGIN` 不支援使用下列 Azure Active Directory (Azure AD) 主體的模擬：
-   Azure AD 使用者的別名。 在此情況下，會傳回下列錯誤： `15517` 。
- Azure AD 以 Azure AD 應用程式或服務主體為基礎的登入和使用者。 在此情況下，會傳回下列錯誤： `15517` 和 `15406` 。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query sp_send_db_mail 中不支援參數

`@query` [Sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql)程式中的參數無法運作。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>在異地容錯移轉之後，必須重新設定異動複寫

如果在自動容錯移轉群組中的資料庫上啟用異動複寫，SQL 受控執行個體系統管理員必須清除舊主要複本上的所有發行集，並在容錯移轉至另一個區域之後，在新的主資料庫上重新設定它們。 如需詳細資訊， [請參閱複寫](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)。

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 中不支援 Azure AD 登入和使用者

SQL Server Data Tools 不完全支援 Azure AD 登入和使用者。

### <a name="temporary-database-is-used-during-restore-operation"></a>在還原作業期間使用暫存資料庫

當資料庫在 SQL 受控執行個體中還原時，還原服務會先建立具有所需名稱的空白資料庫，以便在實例上配置名稱。 經過一段時間之後，這個資料庫就會被捨棄，而實際資料庫的還原將會啟動。 

處於正在 *還原* 狀態的資料庫會暫時擁有隨機 GUID 值，而不是名稱。 一旦還原程式完成，暫存名稱就會變更為在語句中指定的所需名稱 `RESTORE` 。 

在初始階段中，使用者可以存取空的資料庫，甚至是在此資料庫中建立資料表或載入資料。 當還原服務啟動第二個階段時，就會卸載這個暫存資料庫。

因應**措施：在**您看到還原完成之前，請不要存取您要還原的資料庫。

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB 結構和內容已重新建立

`tempdb`資料庫一律會分割成12個資料檔案，而且無法變更檔案結構。 無法變更每個檔案的大小上限，也無法將新檔案新增至 `tempdb` 。 `Tempdb` 當實例啟動或容錯移轉時，一律會重新建立為空的資料庫，而且不會保留在中進行的任何變更 `tempdb` 。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型資料庫檔案造成儲存空間超出限制

`CREATE DATABASE`、 `ALTER DATABASE ADD FILE` 和 `RESTORE DATABASE` 語句可能會失敗，因為實例可以達到 Azure 儲存體限制。

SQL 受控執行個體的每個一般用途實例最多可為 Azure Premium 磁碟空間保留 35 TB 的儲存體。 每個資料庫檔案都會放在個別的實體磁片上。 磁碟大小可以是 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁片上未使用的空間不會收取費用，但 Azure Premium 磁片大小的總和不能超過 35 TB。 在某些情況下，由於內部片段的緣故，不需要 8 TB 的受控實例可能會超過 35 TB 的儲存體大小限制。

例如，SQL 受控執行個體的一般目的實例可能有一個大小為 1.2 TB 的大型檔案放在 4 TB 磁片上。 它也可能會有248檔案，每個檔案都是 1 GB，並放在不同的 128-GB 磁片上。 在此範例中：

- 配置的磁碟儲存體大小總計為 1 x 4 TB + 248 x 128 GB = 35 TB。
- 為執行個體上的資料庫保留的大小總計為 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

此範例說明，在特定情況下，由於檔案的特定散發，SQL 受控執行個體的實例可能會達到您可能不會預期時，為連結的 Azure Premium 磁片保留的 35-TB 限制。

在此範例中，現有資料庫會繼續運作，而且只要不新增檔案，就不會有任何問題的成長。 因為新的磁片磁碟機沒有足夠的空間，所以無法建立或還原新資料庫，即使所有資料庫的大小總計未達到實例大小限制也一樣。 在該情況下傳回的錯誤並不清楚。

您可以使用 [系統檢視] 來 [識別剩餘的檔案數目](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) 。 如果達到此限制，請嘗試 [使用 DBCC SHRINKFILE 語句來清空和刪除部分較小](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) 的檔案，或切換至 [沒有此限制的業務關鍵層](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="guid-values-shown-instead-of-database-names"></a>顯示的 GUID 值，而不是資料庫名稱

數個系統檢視、效能計數器、錯誤訊息、XEvents 與錯誤記錄檔項目都會顯示 GUID 資料庫識別碼，而非實際資料庫名稱。 請勿依賴這些 GUID 識別碼，因為未來會將它們取代為實際的資料庫名稱。

因應**措施：使用**sys. 資料庫檢視來解析實體資料庫名稱中的實際資料庫名稱，以 GUID 資料庫識別碼的形式指定：

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>錯誤記錄不會保存

SQL 受控執行個體中可用的錯誤記錄不會保存，而且其大小不會包含在最大儲存空間限制內。 如果發生容錯移轉，可能會自動清除錯誤記錄檔。 因為在數部虛擬機器上移動了數次 SQL 受控執行個體，所以錯誤記錄檔記錄中可能會有間距。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>不支援相同實例內兩個資料庫的交易範圍

** (于2020年3月解決) **`TransactionScope`如果兩個查詢都傳送至相同交易範圍內相同實例內的兩個資料庫，.net 中的類別將無法運作：

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

因應措施** (在2020年3月) 之後不需要**：使用[SqlConnection. >sqlconnection.changedatabase (字串) ](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)在連接內容中使用另一個資料庫，而不是使用兩個連接。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模組與連結的伺服器有時候無法參考本機 IP 位址

SQL 受控執行個體中的 CLR 模組與參考目前實例的連結伺服器或分散式查詢有時無法解析本機實例的 IP。 此錯誤為暫時性問題。

因應措施：如有可能，請在 CLR 模組中**使用內容連接**。

## <a name="updates"></a>更新

如需 SQL Database 更新和增強功能的清單，請參閱 [SQL Database 服務更新](https://azure.microsoft.com/updates/?product=sql-database)。

如需所有 Azure 服務的更新和增強功能，請參閱 [服務更新](https://azure.microsoft.com/updates)。

## <a name="contribute-to-content"></a>參與內容

若要參與 Azure SQL 檔，請參閱檔 [參與者指南](https://docs.microsoft.com/contribute/)。
