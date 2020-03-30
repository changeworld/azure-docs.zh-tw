---
title: 託管實例 T-SQL 差異
description: 本文將討論 Azure SQL Database 中的受控執行個體與 SQL Server之間的 T-SQL 差異
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365486"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>託管實例 T-SQL 差異和限制

本文總結並解釋了 Azure SQL 資料庫託管實例和本地 SQL Server 資料庫引擎之間的語法和行為差異。 受控執行個體的部署選項提供對內部部署 SQL Server 資料庫引擎的高度相容性。 受控執行個體支援大部分的 SQL Server 資料庫引擎功能。

![移轉](./media/sql-database-managed-instance/migration.png)

與 SQL Server 相比，託管實例中引入了一些 PaaS 限制，並且發生了一些行為更改。 差異分為以下幾類：<a name="Differences"></a>

- [可用性](#availability)包括["始終打開可用性"組](#always-on-availability-groups)和[備份](#backup)中的差異。
- [安全性](#security)包括[審核](#auditing)、[證書](#certificates)、[憑據](#credential)、[加密提供程式](#cryptographic-providers)、[登錄名和使用者](#logins-and-users)以及[服務金鑰和服務主金鑰](#service-key-and-service-master-key)的差異。
- [配置](#configuration)包括[緩衝集區擴展](#buffer-pool-extension)、[排序規則](#collation)、[相容性級別](#compatibility-levels)、[資料庫鏡像](#database-mirroring)、[資料庫選項](#database-options)[、SQL Server 代理](#sql-server-agent)和[表選項](#tables)的差異。
- [功能](#functionalities)包括[BULK INSERT/OPENROWSET、CLR、DBCC、](#bulk-insert--openrowset)[分散式交易](#distributed-transactions)、[擴展事件](#extended-events)、[外部庫](#external-libraries)、[檔流和檔表](#filestream-and-filetable)、[全文語義搜索](#full-text-semantic-search)、[連結伺服器](#linked-servers)[、PolyBase、](#polybase)[複製](#replication)[、RESTORE、](#restore-statement)[服務代理](#service-broker)[、預存程序、函數和觸發器](#stored-procedures-functions-and-triggers)。 [CLR](#clr) [DBCC](#dbcc)
- [環境設置](#Environment)，如 VNet 和子網配置。

這些功能大多數是體系結構約束，表示服務功能。

在託管實例中發現的將來將解決的臨時已知問題將在[版本資訊頁](sql-database-release-notes.md)中描述。

## <a name="availability"></a>可用性

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>AlwaysOn 可用性群組

[高可用性](sql-database-high-availability.md)內置於託管實例中，使用者無法控制。 不支援以下語句：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [創建可用性組](/sql/t-sql/statements/create-availability-group-transact-sql)
- [更改可用性組](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER 資料庫](/sql/t-sql/statements/alter-database-transact-sql)語句的[SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr)子句

### <a name="backup"></a>Backup 

託管實例具有自動備份，因此使用者可以創建完整的資料庫`COPY_ONLY`備份。 不支援差異、日誌和檔快照備份。

- 使用託管實例，只能將實例資料庫備份到 Azure Blob 存儲帳戶：
  - 只支援 `BACKUP TO URL`。
  - `FILE`不支援`TAPE`和 備份裝置。
- 大多數常規`WITH`選項都支援。
  - `COPY_ONLY`是強制性的。
  - 不支援 `FILE_SNAPSHOT`。
  - 磁帶選項： `REWIND` `NOREWIND`、`UNLOAD``NOUNLOAD`和 不受支援。
  - 特定于日誌的選項： `NORECOVERY` `STANDBY` `NO_TRUNCATE` ， 不支援 和 。

限制： 

- 使用託管實例，可以將實例資料庫備份到具有最多 32 個條帶的備份，如果使用備份壓縮，則足夠資料庫最多 4 TB。
- 不能在使用服務管理`BACKUP DATABASE ... WITH COPY_ONLY`透明資料加密 （TDE） 加密的資料庫上執行。 服務管理的 TDE 強制使用內部 TDE 金鑰加密備份。 無法匯出金鑰，因此無法還原備份。 使用自動備份和時間點還原，或者改用[客戶管理 （BYOK） TDE。](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) 您還可以禁用資料庫上的加密。
- 在託管實例中使用`BACKUP`命令的最大備份條帶大小為 195 GB，即最大 blob 大小。 在備份命令中增加等量磁碟區的數目，以減少個別的等量磁碟區大小並維持在這項限制內。

    > [!TIP]
    > 要解決此限制，當您在本地環境中或虛擬機器中從 SQL Server 備份資料庫時，可以：
    >
    > - 備份到`DISK`而不是備份到`URL`。
    > - 將備份檔案上載到 Blob 存儲。
    > - 還原到託管實例。
    >
    > 託管`Restore`實例中的命令支援備份檔案中更大的 Blob 大小，因為不同的 blob 類型用於存儲上載的備份檔案。

如需使用 T-SQL 進行備份的相關資訊，請參閱[備份](/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>稽核

Azure SQL Database 中的資料庫和 SQL Server 中的資料庫兩者之間的主要稽核差異在於：

- 使用 Azure SQL 資料庫中的託管實例部署選項，審核在伺服器級別工作。 日誌檔`.xel`存儲在 Azure Blob 存儲中。
- 在使用 Azure SQL Database 中的單一資料庫和彈性集區部署選項時，稽核作業則會在資料庫層級運作。
- 在 SQL 伺服器本地或虛擬機器中，審核在伺服器級別工作。 事件存儲在檔案系統或 Windows 事件日誌上。
 
受控執行個體中的 XEvent 稽核支援 Azure Blob 儲存體目標。 不支援檔和 Windows 日誌。

向 Azure Blob 儲存體進行稽核的 `CREATE AUDIT` 語法有一個主要差異：

- 提供了一個新的`TO URL`語法，可用於指定放置檔的 Azure Blob 存儲容器的`.xel`URL。
- 不支援該`TO FILE`語法，因為託管實例無法訪問 Windows 檔共用。

如需詳細資訊，請參閱 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [審計](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>憑證

託管實例無法訪問檔共用和 Windows 資料夾，因此應用以下約束：

- 證書`CREATE FROM`/`BACKUP TO`不支援該檔。
- / `BACKUP` `FILE`不支援/的`ASSEMBLY`證書。 `CREATE` 無法使用私密金鑰檔案。 

請參閱 [CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql)。 
 
**解決方法**：而不是創建證書備份和還原備份，[獲取證書二進位內容和私密金鑰，將其存儲為 .sql 檔，並從二進位創建](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)：

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>認證

僅支援Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 身分識別。 不支援 Windows 使用者。

請參閱 [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql)。

### <a name="cryptographic-providers"></a>密碼編譯提供者

託管實例無法訪問檔，因此無法創建加密提供程式：

- 不支援 `CREATE CRYPTOGRAPHIC PROVIDER`。 請參閱 [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支援 `ALTER CRYPTOGRAPHIC PROVIDER`。 請參閱 [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins-and-users"></a>登入和使用者

- 支援使用 .`FROM CERTIFICATE``FROM ASYMMETRIC KEY``FROM SID`和 創建的 SQL 登錄名。 請參閱 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql)。
- 支援使用[創建登錄](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)語法或從[登錄創建使用者 [Azure AD 登錄]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)語法創建的 Azure 活動目錄 （Azure AD） 伺服器主體（登錄）。 這些登錄是在伺服器級別創建的。

    託管實例支援具有語法`CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`的 Azure AD 資料庫主體。 此功能也稱為 Azure AD 包含的資料庫使用者。

- 不支援使用`CREATE LOGIN ... FROM WINDOWS`語法創建的 Windows 登錄。 使用 Azure Active Directory 登入和使用者。
- 創建實例的 Azure AD 使用者具有[不受限制的管理許可權](sql-database-manage-logins.md)。
- 可以使用`CREATE USER ... FROM EXTERNAL PROVIDER`語法創建非管理員 Azure AD 資料庫級使用者。 請參閱[創建使用者...來自外部提供程式](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。
- Azure AD 伺服器主體（登錄名）僅支援一個託管實例中的 SQL 功能。 Azure AD 使用者不支援需要跨實例交互的功能，無論它們屬於同一 Azure AD 租戶還是不同的租戶。 這類功能的範例如下：

  - SQL 異動複寫。
  - 連結伺服器。

- 不支援將對應至 Azure AD 群組的 Azure AD 登入設定為資料庫擁有者。
- 支援使用其他 Azure AD 主體類比 Azure AD 伺服器級主體，例如[EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql)子句。 執行作為限制包括：

  - 當名稱與登錄名不同時，Azure AD 使用者不支援執行"作為使用者"。 例如，當使用者通過語法 CREATE USER [myAdUser] 從登錄 *john@contoso.com創建 ， 並通過 EXEC AS USER = _myAdUser_嘗試類比. 當您從 Azure AD 伺服器主體（登錄）創建**USER**時，請指定 user_name與**從 LOGIN**中的相同login_name。
  - 只有作為角色一部分的`sysadmin`SQL Server 級主體（登錄名）才能執行針對 Azure AD 主體的以下操作：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- 使用[SSMS V18.4 或更高版本](/sql/ssms/download-sql-server-management-studio-ssms)的 Azure AD 使用者支援使用 bacpac 檔的資料庫匯出/導入，或[SQLPackage.exe](/sql/tools/sqlpackage-download)。
  - 支援使用資料庫 bacpac 檔進行以下配置： 
    - 在同一 Azure AD 域中的不同管理實例之間匯出/導入資料庫。
    - 從託管實例匯出資料庫，並將導入到同一 Azure AD 域中的 SQL 資料庫。 
    - 從 SQL 資料庫匯出資料庫，並將導入到同一 Azure AD 域中的託管實例。
    - 從託管實例匯出資料庫並導入 SQL Server（版本 2012 或更高版本）。
      - 在此配置中，所有 Azure AD 使用者都創建為 SQL 資料庫主體（使用者），無需登錄。 使用者類型列為 SQL（在 database_principalsdatabase_principals 中SQL_USER可見）。 它們的許可權和角色保留在 SQL Server 資料庫中繼資料中，可用於類比。 但是，它們不能用於使用其憑據訪問和登錄到 SQL Server。

- 只有伺服器級主體登錄（由託管實例預配過程創建、伺服器角色的成員（如`securityadmin`或`sysadmin`）或其他具有伺服器級別 ALTER 任何登入權利的登錄，才能在託管實例的主資料庫中創建 Azure AD 伺服器主體（登錄）。
- 如果登錄是 SQL 主體，則只有`sysadmin`作為角色一部分的登錄名才能使用創建命令為 Azure AD 帳戶創建登錄名。
- Azure AD 登錄名必須是用於 Azure SQL 資料庫託管實例的同一目錄中的 Azure AD 的成員。
- Azure AD 伺服器主體（登錄名）在物件資源管理器中可見，從 SQL 伺服器管理工作室 18.0 預覽 5 開始。
- 可允許 Azure AD 伺服器主體 (登入) 與 Azure AD 系統管理員帳戶重疊。 解析主體並將許可權應用於託管實例時，Azure AD 伺服器主體（登錄名）優先于 Azure AD 管理員。
- 在身份驗證期間，將應用以下順序來解決身份驗證主體：

    1. 如果 Azure AD 帳戶以直接映射到 Azure AD 伺服器主體（登錄名）的形式存在，則該帳戶在 sys.server_principals 中以類型"E"的形式存在，請授予 Azure AD 伺服器主體（登錄）的許可權並應用許可權。
    2. 如果 Azure AD 帳戶是映射到 Azure AD 伺服器主體（登錄）的 Azure AD 組的成員，該組位於 sys.server_principals 中為類型為"X"，則授予 Azure AD 組登錄的存取權限並應用許可權。
    3. 如果 Azure AD 帳戶是託管實例的特殊門戶配置的 Azure AD 管理員（託管實例系統檢視中不存在），請為託管實例（舊模式）應用 Azure AD 管理員的特殊固定許可權。
    4. 如果 Azure AD 帳戶以直接映射到資料庫中的 Azure AD 使用者（位於 database_principals系統中為類型為"E"）存在，則授予 Azure AD 資料庫使用者的許可權並應用許可權。
    5. 如果 Azure AD 帳戶是映射到資料庫中的 Azure AD 使用者（database_principals作為類型"X"存在於的 Azure AD 組的成員，則授予 Azure AD 組登錄的存取權限並應用許可權。
    6. 如果存在映射到 Azure AD 使用者帳戶或 Azure AD 群組帳戶（解析為正在進行身份驗證的使用者）的 Azure AD 登錄名，則將應用此 Azure AD 登錄名中的擁有權限。

### <a name="service-key-and-service-master-key"></a>服務金鑰和服務主要金鑰

- 不支援[主金鑰備份](/sql/t-sql/statements/backup-master-key-transact-sql)（由 SQL 資料庫服務管理）。
- 不支援[主金鑰還原](/sql/t-sql/statements/restore-master-key-transact-sql)（由 SQL 資料庫服務管理）。
- [不支援服務主金鑰備份](/sql/t-sql/statements/backup-service-master-key-transact-sql)（由 SQL 資料庫服務管理）。
- [不支援服務主金鑰還原](/sql/t-sql/statements/restore-service-master-key-transact-sql)（由 SQL 資料庫服務管理）。

## <a name="configuration"></a>組態

### <a name="buffer-pool-extension"></a>緩衝集區延伸

- 不支援[緩衝集區擴展](/sql/database-engine/configure-windows/buffer-pool-extension)。
- 不支援 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 請參閱 [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>定序

預設執行個體定序為 `SQL_Latin1_General_CP1_CI_AS`，而且可指定為建立參數。 請參閱[定序](/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>相容性層級

- 支援的相容性級別為 100、110、120、130、140 和 150。
- 不支援低於 100 的相容性層級。
- 新資料庫的預設相容性層級為 140。 對於還原的資料庫，相容性級別保持不變（如果為 100 和以上）。

請參閱 [ALTER DATABASE 相容性層級](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>資料庫鏡像

不支援資料庫鏡像。

- 不支援 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 選項。
- 不支援 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

如需詳細資訊，請參閱 [ALTER DATABASE SET PARTNER and SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 和 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>資料庫選項

- 不支援多個記錄檔。
- 「一般用途」服務層級中不支援記憶體內部物件。 
- 每個通用實例有 280 個檔的限制，這意味著每個資料庫最多有 280 個檔。 "通用"層中的資料和日誌檔都計入此限制。 [業務關鍵層支援每個資料庫 32，767 個檔](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。
- 資料庫不能包含包含檔流資料的檔組。 如果 .bak 包含資料`FILESTREAM`，則還原將失敗。 
- 每個檔案都位於 Azure Blob 儲存體中。 每個檔案的 IO 和輸送量均取決於每個個別檔案的大小。

#### <a name="create-database-statement"></a>CREATE DATABASE 陳述式

以下限制適用于`CREATE DATABASE`：

- 無法定義檔案和檔案群組。 
- 不支援`CONTAINMENT`該選項。 
- `WITH`不支援選項。 
   > [!TIP]
   > 作為解決方法，使用`ALTER DATABASE`後`CREATE DATABASE`設置資料庫選項以添加檔或設置包含。 

- 不支援`FOR ATTACH`該選項。
- 不支援`AS SNAPSHOT OF`該選項。

如需詳細資訊，請參閱 [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 陳述式

有些檔案屬性無法設定或變更：

- 無法在`ALTER DATABASE ADD FILE (FILENAME='path')`T-SQL 語句中指定檔路徑。 從指令碼中移除 `FILENAME`，因為受控執行個體會自動放置這些檔案。 
- 無法使用 語句更改`ALTER DATABASE`檔案名。

以下為預設選項且無法變更：

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

下列選項無法修改：

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

如需詳細資訊，請參閱 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)。

### <a name="sql-server-agent"></a>SQL Server Agent

- 託管實例中當前不支援啟用和禁用 SQL Server 代理。 SQL 代理程式一律會處於正在執行的狀態。
- SQL 伺服器代理設置是唯讀的。 託管實例`sp_set_agent_properties`不支援該過程。 
- 工作
  - 支援 T-SQL 作業步驟。
  - 支援下列複寫作業：
    - 交易記錄讀取器
    - 快照式
    - 散發者
  - 支援 SSIS 作業步驟。
  - 當前不支援其他類型的作業步驟：
    - 不支援合併複製作業步驟。 
    - 不支援佇列讀取器。 
    - 命令外殼尚不受支援。
  - 託管實例無法訪問外部資源，例如，通過網路複製網路共用。 
  - 不支援 SQL 伺服器分析服務。
- 部分支援通知。
- 電子郵件通知受支援，儘管它要求您設定資料庫郵件設定檔。 SQL 伺服器代理只能使用一個資料庫郵件設定檔，並且必須調用`AzureManagedInstance_dbmail_profile`它。 
  - 不支援呼叫器。
  - 不支援 NetSend。
  - 尚未支援警報。
  - 不支援代理。
- 不支援事件日誌。

當前不支援以下 SQL 代理功能：

- Proxy
- 在空閒 CPU 上安排作業
- 啟用或禁用代理
- 警示

如需有關 SQL Server Agent 的資訊，請參閱 [SQL Server Agent](/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>資料表

不支援以下表類型：

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [檔表](/sql/relational-databases/blob/filetables-sql-server)
- [外部表](/sql/t-sql/statements/create-external-table-transact-sql)（底座）
- [MEMORY_OPTIMIZED（](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)僅在通用層中支援）

有關如何創建和更改表的資訊，請參閱[創建表](/sql/t-sql/statements/create-table-transact-sql)和 ALTER [TABLE](/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>散裝插入 / 開羅設置

託管實例無法訪問檔共用和 Windows 資料夾，因此必須從 Azure Blob 存儲導入檔：

- `DATASOURCE`從 Azure `BULK INSERT` Blob 存儲導入檔時，命令中需要。 請參閱[大量插入](/sql/t-sql/statements/bulk-insert-transact-sql)。
- `DATASOURCE`從 Azure `OPENROWSET` Blob 存儲讀取檔內容時，函數中需要。 請參閱 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENROWSET`可用於從其他 Azure SQL 單個資料庫、託管實例或 SQL Server 實例讀取資料。 不支援其他源，如 Oracle 資料庫或 Excel 檔。

### <a name="clr"></a>CLR

託管實例無法訪問檔共用和 Windows 資料夾，因此應用以下約束：

- 只支援 `CREATE ASSEMBLY FROM BINARY`。 請參閱[從二進位創建 ASSEM BLY](/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支援 `CREATE ASSEMBLY FROM FILE`。 請參閱[從檔創建程式集](/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 無法參考檔案。 請參閱[ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="database-mail-db_mail"></a>資料庫郵件 （db_mail）
 - `sp_send_dbmail`無法使用@file_attachments參數發送附件。 此過程無法訪問本地檔案系統和外部共用或 Azure Blob 存儲。
 - 請參閱與參數和身份驗證相關的`@query`已知問題。
 
### <a name="dbcc"></a>DBCC

在託管實例中不支援在 SQL Server 中啟用的已記錄的 DBCC 語句。

- 僅支援有限數量的全域跟蹤標誌。 不支援會話級別`Trace flags`。 請參閱[跟蹤標誌](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)和[DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)使用數量有限的全域跟蹤標誌。
- 由於無法在模式下設置資料庫，因此無法使用具有REPAIR_ALLOW_DATA_LOSS、REPAIR_FAST和REPAIR_REBUILD選項的`SINGLE_USER` [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) - 參見 ALTER[資料庫差異](#alter-database-statement)。 潛在的資料庫損壞由 Azure 支援小組處理。 如果發現應修復的資料庫損壞，請與 Azure 支援部門聯繫。

### <a name="distributed-transactions"></a>分散式交易

MSDTC 和[彈性事務](sql-database-elastic-transactions-overview.md)當前在託管實例中不受支援。

### <a name="extended-events"></a>擴充事件

不支援擴展事件 （XEvents） 的某些特定于 Windows 的目標：

- 不支援`etw_classic_sync`目標。 將`.xel`檔存儲在 Azure Blob 存儲中。 請參閱 [etw_classic_sync 目標](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- 不支援`event_file`目標。 將`.xel`檔存儲在 Azure Blob 存儲中。 請參閱 [event_file 目標](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部程式庫

資料庫內 R 和 Python 中，尚不支援外部庫。 請參閱 [SQL Server Machine Learning 服務](/sql/advanced-analytics/r/sql-server-r-services)。

### <a name="filestream-and-filetable"></a>Filestream 與 FileTable

- 不支援檔流資料。
- 資料庫不能包含包含`FILESTREAM`資料的檔組。
- 不支援 `FILETABLE`。
- 資料表不能有 `FILESTREAM` 類型
- 以下是不支援的函式：
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

如需詳細資訊，請參閱 [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) 和 [Filetable](/sql/relational-databases/blob/filetables-sql-server)。

### <a name="full-text-semantic-search"></a>全文檢索的語意搜尋

不支援[語意搜尋](/sql/relational-databases/search/semantic-search-sql-server)。

### <a name="linked-servers"></a>連結的伺服器

在受控執行個體中，連結伺服器支援的目標數有限：

- 支援的目標是託管實例、單個資料庫和 SQL Server 實例。 
- 連結的伺服器不支援分散式可寫事務 （MS DTC）。
- 不支援的目標包括檔、分析服務和其他 RDBMS。 嘗試使用或`BULK INSERT``OPENROWSET`作為檔導入的替代方法，使用 Azure Blob 存儲導入本機 CSV 導入。

作業

- 不支援跨執行個體寫入交易。
- 支援使用 `sp_dropserver` 卸除連結的伺服器。 請參閱 [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- 該`OPENROWSET`函數僅用於在 SQL Server 實例上執行查詢。 它們可以在本地或虛擬機器中進行管理。 請參閱 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- 該`OPENDATASOURCE`函數僅用於在 SQL Server 實例上執行查詢。 它們可以在本地或虛擬機器中進行管理。 僅`SQLNCLI`作為`SQLNCLI11`提供程式支援`SQLOLEDB`的 和 值。 例如 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 請參閱 [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)。
- 連結的伺服器不能用於從網路共用讀取檔（Excel、CSV）。 嘗試使用從 Azure Blob 存儲讀取 CSV 檔的[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET。](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 在[託管實例回饋項](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)上跟蹤此請求|

### <a name="polybase"></a>PolyBase

不支援引用 HDFS 或 Azure Blob 存儲中檔的外部表。 有關 PolyBase 的資訊，請參閱[PolyBase](/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>複寫

- 支援快照和雙向複製類型。 不支援合併複製、對等複製和可恢復訂閱。
- [異動複寫](sql-database-managed-instance-transactional-replication.md)可用於託管實例上的公共預覽，具有一些約束：
    - 所有類型的複製參與者（發佈伺服器、分發伺服器、拉取訂閱伺服器和發送訂閱伺服器）都可以放置在託管實例上，但發行者和分發伺服器必須同時位於雲中或同時位於本地。
    - 託管實例可以與最新版本的 SQL Server 進行通信。 有關詳細資訊，請參閱[支援的版本矩陣](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)。
    - 異動複寫有一些[額外的網路要求](sql-database-managed-instance-transactional-replication.md#requirements)。

有關配置異動複寫的詳細資訊，請參閱以下教程：
- [MI 發行者和訂閱者之間的複製](replication-with-sql-database-managed-instance.md)
- [MI 發行者、MI 分發伺服器和 SQL Server 訂閱者之間的複製](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>RESTORE 陳述式 

- 支援的語法：
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 不支援的語法：
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 來源： 
  - `FROM URL`（Azure Blob 存儲）是唯一受支援的選項。
  - 不支援 `FROM DISK`/`TAPE`/備份裝置。
  - 不支援備份組。
- `WITH`不支援選項，例如否`DIFFERENTIAL`或`STATS`。
- `ASYNC RESTORE`：即使用戶端連接中斷，恢復也會繼續。 如果連接已斷開，則可以檢查`sys.dm_operation_status`視圖的還原操作的狀態以及創建和 DROP 資料庫。 請參閱 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

設置或重寫以下資料庫選項，以後無法更改： 

- `NEW_BROKER`如果 .bak 檔中未啟用代理。 
- `ENABLE_BROKER`如果 .bak 檔中未啟用代理。 
- `AUTO_CLOSE=OFF`如果 .bak 檔中的資料庫具有`AUTO_CLOSE=ON`。 
- `RECOVERY FULL`如果 .bak 檔中的資料庫具有`SIMPLE`或`BULK_LOGGED`復原模式。
- 如果檔組不在源 .bak 檔中，則添加記憶體優化的檔組並稱為 XTP。 
- 任何現有的記憶體優化檔組都重命名為 XTP。 
- `SINGLE_USER`和`RESTRICTED_USER`選項轉換為`MULTI_USER`。

限制： 

- 根據損壞的類型，可能會還原損壞的資料庫的備份，但在修復損壞之前不會執行自動備份。 請確保`DBCC CHECKDB`在源實例上運行並使用備份`WITH CHECKSUM`來防止此問題。
- 無法在`.BAK`託管實例上還原包含本文檔中描述的任何限制（例如或`FILESTREAM``FILETABLE`物件）的資料庫檔案的還原。
- `.BAK`無法還原包含多個備份組的檔。 
- `.BAK`無法還原包含多個日誌檔的檔。
- 在通用實例上無法還原包含大於 8 TB 的資料庫、活動記憶體中的 OLTP 物件或每個實例超過 280 個檔的檔數。 
- 在業務關鍵型實例上無法還原包含大於 4 TB 的資料庫或總大小大於[資源限制](sql-database-managed-instance-resource-limits.md)中描述的大小的記憶體中 OLTP 物件的備份。
有關還原語句的資訊，請參閱[RESTORE 語句](/sql/t-sql/statements/restore-statements-transact-sql)。

 > [!IMPORTANT]
 > 同樣的限制也適用于內置的時間點還原操作。 例如，無法在"業務關鍵"實例上還原大於 4 TB 的通用資料庫。 在通用實例上無法還原具有記憶體中 OLTP 檔或超過 280 個檔的"業務關鍵型資料庫"。

### <a name="service-broker"></a>Service Broker

不支援跨執行個體的服務訊息代理程式：

- `sys.routes`作為先決條件，您必須從 sys.路由中選擇位址。 每個路線上的位址必須為 LOCAL。 請參閱 [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`： 不能將 以外的`CREATE ROUTE``ADDRESS`使用`LOCAL`與 。 請參閱 [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`： 不能將 以外的`ALTER ROUTE``ADDRESS`使用`LOCAL`與 。 請參閱 [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>預存程序、函數和觸發器

- `NATIVE_COMPILATION`"通用"層不支援。
- 不支援下列 [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 選項： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- 不支援 `sp_execute_external_scripts`。 請參閱 [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支援 `xp_cmdshell`。 請參閱 [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- `Extended stored procedures`不受支援，其中包括`sp_addextendedproc` 和`sp_dropextendedproc`。 請參閱[擴充預存程序](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支援 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 請參閱 [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

### <a name="system-functions-and-variables"></a>系統函數和變數

下列變數、函式和檢視會傳回不同的結果：

- `SERVERPROPERTY('EngineEdition')`傳回值 8。 此屬性只會識別出受控執行個體。 請參閱 [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')`返回 Null，因為 SQL Server 存在的實例概念不適用於託管實例。 請參閱 [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME`返回完整的 DNS"可連接"名稱，例如，my-managed-instance.wcus17662feb9ce98.database.windows.net。 請參閱[@SERVERNAME*](/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS`返回完整的 DNS"可連接"名稱，例如`myinstance.domain.database.windows.net`屬性"名稱"和"data_source"。 請參閱 [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME`返回 Null，因為 SQL Server 存在的服務概念不適用於託管實例。 請參閱[@SERVICENAME*](/sql/t-sql/functions/servicename-transact-sql)。
- 支援 `SUSER_ID`。 如果 Azure AD 登錄名不在 sys.syslogins 中，則返回 Null。 請參閱 [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支援 `SUSER_SID`。 返回錯誤的資料，這是一個臨時的已知問題。 請參閱 [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="environment-constraints"></a><a name="Environment"></a>環境限制

### <a name="subnet"></a>子網路
-  不能將任何其他資源（例如虛擬機器）放置在已部署託管實例的子網中。 使用其他子網部署這些資源。
- 子網必須有足夠的可用 IP[位址](sql-database-managed-instance-connectivity-architecture.md#network-requirements)數。 最小值為 16，而建議在子網中至少有 32 個 IP 位址。
- [服務終結點不能與託管實例的子網相關聯](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 創建虛擬網路時，請確保禁用服務終結點選項。
- 可以在區域中部署的 vCore 和實例類型有一些[限制和限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)。
- 子[網上必須應用一些安全規則](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。

### <a name="vnet"></a>VNET
- 可以使用資源模型部署 VNet - 不支援 VNet 的經典模型。
- 創建託管實例後，不支援將託管實例或 VNet 移動到其他資源組或訂閱。
- 某些服務（如應用服務環境、邏輯應用和託管實例（用於異地複製、異動複寫或通過連結伺服器）無法訪問不同區域中的託管實例，如果 VNet 使用[全域對等互連](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)連接。 您可以通過 ExpressRoute 或 VNet 到 VNet 閘道連接到這些資源。

### <a name="tempdb"></a>Tempdb

在通用層上，`tempdb`每個內核的最大檔案大小不能大於 24 GB。 業務關鍵`tempdb`型層上的最大大小受實例存儲大小的限制。 `Tempdb`日誌檔案大小在通用層上限制為 120 GB。 如果某些查詢需要每個內核超過 24 `tempdb` GB，或者它們生成超過 120 GB 的日誌資料，則可能會返回錯誤。

### <a name="msdb"></a>MSDB

託管實例中的以下 MSDB 架構必須由其各自的預定義角色擁有：

- 常規角色
  - 目標伺服器角色
- [固定資料庫角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [資料庫郵件角色](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile)：
  - 資料庫郵件使用者角色
- [整合服務角色](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)：
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> 客戶更改預定義的角色名稱、架構名稱和架構擁有者將影響服務的正常運行。 對這些所做的任何更改將在檢測到後立即還原回預定義值，或最遲在下次服務更新時還原，以確保正常服務操作。

### <a name="error-logs"></a>錯誤記錄

託管實例在錯誤日誌中放置詳細資訊。 在錯誤日誌中記錄了許多內部系統事件。 使用自訂過程讀取篩選出一些不相關的條目的錯誤日誌。 有關詳細資訊，請參閱 Azure 資料工作室的[託管實例sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)或[託管實例擴展（預覽）。](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs)

## <a name="next-steps"></a>後續步驟

- 有關託管實例的詳細資訊，請參閱[什麼是託管實例？](sql-database-managed-instance.md)
- 有關功能和比較清單，請參閱[Azure SQL 資料庫功能比較](sql-database-features.md)。
- 有關發佈更新和已知問題狀態，請參閱[SQL 資料庫版本資訊](sql-database-release-notes.md)
- 有關演示如何創建新託管實例的快速入門，請參閱[創建託管實例](sql-database-managed-instance-get-started.md)。
