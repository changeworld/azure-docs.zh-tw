---
title: SQL Server & Azure SQL 受控執行個體之間的 t-sql 差異
description: 本文討論 Transact-sql (T-sql) Azure SQL 受控執行個體和 SQL Server 之間的差異。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 06/02/2020
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 63aed3ce47a5499154041f067c292c656adcde6c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323170"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>SQL Server & Azure SQL 受控執行個體之間的 t-sql 差異
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文將摘要說明 Azure SQL 受控執行個體與 SQL Server 之間的語法和行為差異。 


SQL 受控執行個體提供與 SQL Server database engine 的高度相容性，而且 SQL 受控執行個體中支援大部分的功能。

![從 SQL Server 輕鬆遷移](./media/transact-sql-tsql-differences-sql-server/migration.png)

在 SQL 受控執行個體中引進了一些 PaaS 限制，而相較于 SQL Server，有一些行為變更。 差異分為下列幾個類別： <a name="Differences"></a>

- [可用性](#availability) 包含 [Always On 可用性群組](#always-on-availability-groups) 和 [備份](#backup)的差異。
- [安全性](#security)包括[審核](#auditing)、[憑證](#credential)、認證[、](#certificates)[密碼編譯提供者](#cryptographic-providers)、登入[和使用者](#logins-and-users)，以及[服務金鑰和服務主要金鑰](#service-key-and-service-master-key)的差異。
- [Configuration](#configuration)設定包括[緩衝集區延伸](#buffer-pool-extension)、定[序](#collation)、[相容性層級](#compatibility-levels)、[資料庫鏡像](#database-mirroring)、[資料庫選項](#database-options)、 [SQL Server Agent](#sql-server-agent)和[資料表選項](#tables)的差異。
- [功能](#functionalities)包括[BULK INSERT/OPENROWSET](#bulk-insert--openrowset)、 [CLR](#clr)、 [DBCC](#dbcc)、[分散式交易](#distributed-transactions)、[擴充事件](#extended-events)、[外部程式庫](#external-libraries)、 [filestream 和 FileTable](#filestream-and-filetable)、[全文檢索語義搜尋](#full-text-semantic-search)、[連結的伺服器](#linked-servers)、 [PolyBase](#polybase)、[複寫、](#replication)[還原](#restore-statement)、 [Service Broker](#service-broker)、[預存程式、函數和觸發](#stored-procedures-functions-and-triggers)程式。
- [環境設定](#Environment) ，例如 vnet 和子網設定。

這些功能大多都是架構條件約束，代表服務功能。

在 SQL 受控執行個體中探索到的暫時性已知問題，將于未來解決，請參閱 [版本資訊頁面](../database/doc-changes-updates-release-notes.md)。

## <a name="availability"></a>可用性

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>AlwaysOn 可用性群組

SQL 受控執行個體內建[高可用性](../database/high-availability-sla.md)，而且無法由使用者控制。 不支援下列語句：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)語句的[SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr)子句

### <a name="backup"></a>Backup

SQL 受控執行個體具有自動備份，因此使用者可以建立完整的資料庫 `COPY_ONLY` 備份。 不支援差異、記錄和檔案快照集備份。

- 有了 SQL 受控執行個體，您就可以將實例資料庫只備份到 Azure Blob 儲存體帳戶：
  - 只支援 `BACKUP TO URL`。
  - `FILE`、 `TAPE` 和備份裝置不受支援。
- 支援大部分的一般 `WITH` 選項。
  - `COPY_ONLY` 是必要的。
  - 不支援 `FILE_SNAPSHOT`。
  - 磁帶選項： `REWIND` 、 `NOREWIND` 、 `UNLOAD` 和 `NOUNLOAD` 不受支援。
  - 記錄檔專用選項： `NORECOVERY` 、 `STANDBY` 和 `NO_TRUNCATE` 不受支援。

限制： 

- 有了 SQL 受控執行個體，您就可以將實例資料庫備份至最多有32個等量的備份，而如果使用備份壓縮的話，就足以容納最多 4 TB 的資料庫。
- 您無法 `BACKUP DATABASE ... WITH COPY_ONLY` 在以服務管理的透明資料加密 (TDE) 加密的資料庫上執行。 服務管理的 TDE 會強制使用內部 TDE 金鑰來加密備份。 無法匯出金鑰，因此您無法還原備份。 使用自動備份和時間點還原，或改用 [客戶管理的 (BYOK) TDE](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) 。 您也可以停用資料庫的加密。
- 在 SQL 受控執行個體中使用命令的最大備份等量大小 `BACKUP` 是 195 GB，這是最大的 blob 大小。 在備份命令中增加等量磁碟區的數目，以減少個別的等量磁碟區大小並維持在這項限制內。

    > [!TIP]
    > 若要解決這項限制，當您從內部部署環境或虛擬機器中的 SQL Server 備份資料庫時，您可以：
    >
    > - 備份至， `DISK` 而不是備份至 `URL` 。
    > - 將備份檔案上傳至 Blob 儲存體。
    > - 還原至 SQL 受控執行個體。
    >
    > `Restore`SQL 受控執行個體中的命令在備份檔案中支援較大的 blob 大小，因為已上傳備份檔案的儲存體會使用不同的 blob 類型。

如需使用 T-SQL 進行備份的相關資訊，請參閱[備份](/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>稽核

Microsoft Azure SQL 和 SQL Server 中的之間的主要差異在於：

- 有了 SQL 受控執行個體，就可以在伺服器層級進行審核。 `.xel`記錄檔會儲存在 Azure Blob 儲存體中。
- 使用 Azure SQL Database，可在資料庫層級進行審核。 `.xel`記錄檔會儲存在 Azure Blob 儲存體中。
- 在 SQL Server、內部部署或虛擬機器中，會在伺服器層級進行審核。 事件會儲存在檔案系統或 Windows 事件記錄檔中。
 
SQL 受控執行個體中的 XEvent 審核支援 Azure Blob 儲存體目標。 不支援檔案和 Windows 記錄檔。

向 Azure Blob 儲存體進行稽核的 `CREATE AUDIT` 語法有一個主要差異：

- `TO URL`您可以使用新的語法，來指定放置檔案的 Azure Blob 儲存體容器的 URL `.xel` 。
- `TO FILE`因為 SQL 受控執行個體無法存取 Windows 檔案共用，所以不支援語法。

如需詳細資訊，請參閱 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [稽核](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>憑證

SQL 受控執行個體無法存取檔案共用及 Windows 資料夾，因此適用下列限制：

- `CREATE FROM` / `BACKUP TO` 憑證不支援此檔案。
- `CREATE` / `BACKUP` 不支援來自的憑證 `FILE` / `ASSEMBLY` 。 無法使用私密金鑰檔案。 

請參閱 [CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql)。 
 
因應**措施：不**是建立憑證的備份並還原備份、[取得憑證二進位內容和私密金鑰、將其儲存為 .sql 檔案，以及從二進位檔建立](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)：

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>認證

僅支援Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 身分識別。 不支援 Windows 使用者。

請參閱 [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql)。

### <a name="cryptographic-providers"></a>密碼編譯提供者

SQL 受控執行個體無法存取檔案，所以無法建立密碼編譯提供者：

- 不支援 `CREATE CRYPTOGRAPHIC PROVIDER`。 請參閱 [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支援 `ALTER CRYPTOGRAPHIC PROVIDER`。 請參閱 [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins-and-users"></a>登入和使用者

- `FROM CERTIFICATE`支援使用、 `FROM ASYMMETRIC KEY` 和所建立的 SQL 登入 `FROM SID` 。 請參閱 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql)。
- Azure Active Directory (Azure AD) 伺服器主體 (使用 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 語法建立的登入，或 [從登入建立使用者 [) login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 語法。 這些登入是在伺服器層級建立。

    SQL 受控執行個體支援使用語法 Azure AD 資料庫主體 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 。 這項功能也稱為 Azure AD 自主資料庫使用者。

- 不支援使用語法建立的 Windows 登入 `CREATE LOGIN ... FROM WINDOWS` 。 使用 Azure Active Directory 登入和使用者。
- 建立實例的 Azure AD 使用者具有不 [受限制的系統管理員許可權](../database/logins-create-manage.md)。
- 您可以使用語法來建立非系統管理員 Azure AD 資料庫層級的使用者 `CREATE USER ... FROM EXTERNAL PROVIDER` 。 請參閱 [建立使用者 .。。從外部提供者](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。
- Azure AD 伺服器主體 (登入) 只支援一個 SQL 受控執行個體內的 SQL 功能。 需要跨實例互動的功能，不論它們是否位於相同的 Azure AD 租使用者或不同的租使用者中，都不支援 Azure AD 的使用者。 這類功能的範例如下：

  - SQL 異動複寫。
  - 連結伺服器。

- 不支援將對應至 Azure AD 群組的 Azure AD 登入設定為資料庫擁有者。
- 支援使用其他 Azure AD 主體模擬 Azure AD 伺服器層級主體，例如 [EXECUTE as](/sql/t-sql/statements/execute-as-transact-sql) 子句。 EXECUTE AS 限制如下：

  - 當名稱與登入名稱不同時，不支援 Azure AD 使用者的 EXECUTE AS USER。 例如，當使用者從 LOGIN [] 的 CREATE USER [myAadUser] 語法建立時，就會 john@contoso.com 透過 EXEC （user = _myAadUser_）嘗試模擬。 當您從 Azure AD 伺服器主體 (登入) 建立 **使用者** 時，請將 user_name 指定為與 **登**入相同的 login_name。
  - 只有屬於角色的 SQL Server 層級主體 (登入) `sysadmin` 可以執行以 Azure AD 主體為目標的下列作業：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- 使用 [SSMS 18.4 或更新版本](/sql/ssms/download-sql-server-management-studio-ssms)或 [SQLPackage.exe](/sql/tools/sqlpackage-download)的 SQL 受控執行個體中的 Azure AD 使用者，支援使用 bacpac 檔案進行資料庫匯出/匯入。
  - 下列是使用資料庫 bacpac 檔案支援的設定： 
    - 在相同 Azure AD 網域內的不同管理實例之間匯出/匯入資料庫。
    - 從 SQL 受控執行個體匯出資料庫，並匯入至相同 Azure AD 網域內的 SQL Database。 
    - 從 SQL Database 匯出資料庫，並匯入至相同 Azure AD 網域內的 SQL 受控執行個體。
    - 從 SQL 受控執行個體匯出資料庫，並匯入至 SQL Server (2012 版或更新版本) 。
      - 在此設定中，所有 Azure AD 使用者都會建立為 SQL Server 資料庫主體 (使用者) 沒有登入。 使用者的類型會列為 `SQL` ，而且 `SQL_USER` 在 sys.database_principals) 中會顯示為。 其許可權和角色會保留在 SQL Server 資料庫中繼資料中，並且可用於模擬。 不過，它們無法用來存取並使用其認證登入 SQL Server。

- 只有 SQL 受控執行個體布建程式所建立的伺服器層級主體登入、伺服器角色的成員（例如 `securityadmin` 或），或在 `sysadmin` 伺服器層級具有 ALTER ANY login 許可權的其他登入，才能在 SQL 受控執行個體的 master 資料庫中，建立 Azure AD 的伺服器主體)  (登入。
- 如果登入是 SQL 主體，只有屬於 `sysadmin` 角色一部分的登入可以使用 create 命令來為 Azure AD 帳戶建立登入。
- Azure AD 的登入必須是用於 Azure SQL 受控執行個體的相同目錄中 Azure AD 的成員。
- 從 SQL Server Management Studio 18.0 preview 5 開始，物件總管會顯示 Azure AD 伺服器主體 (登入) 。
- 可允許 Azure AD 伺服器主體 (登入) 與 Azure AD 系統管理員帳戶重疊。 當您解析主體並將許可權套用至 SQL 受控執行個體時，Azure AD 伺服器主體 (登入) 優先于 Azure AD 管理員。
- 在驗證期間，會套用下列順序來解析驗證主體：

    1. 如果 Azure AD 帳戶以直接對應至 Azure AD 伺服器主體 (登入) （存在於 sys.server_principals 中的類型 "E"），請授與存取權，並將 Azure AD 伺服器主體的許可權授與 (登入) 。
    2. 如果 Azure AD 帳戶是對應至 Azure AD 伺服器主體的 Azure AD 群組成員 (登入) （存在於 sys.server_principals 中的類型 "X"），請授與存取權，並套用 Azure AD 群組登入的許可權。
    3. 如果 Azure AD 帳戶是特殊的入口網站設定 Azure AD admin for SQL 受控執行個體，但 SQL 受控執行個體系統檢視中並不存在，請套用 Azure AD 管理員 for SQL 受控執行個體 (舊版模式) 的特殊固定許可權。
    4. 如果 Azure AD 帳戶以直接對應至資料庫中的 Azure AD 使用者（存在於 sys.database_principals 中）為 "E" 類型的資料庫，則授與存取權並套用 Azure AD 資料庫使用者的許可權。
    5. 如果 Azure AD 帳戶是 Azure AD 群組的成員，而該群組是對應至資料庫中 Azure AD 使用者（存在於 sys.database_principals 中的類型 "X"），請授與存取權，並套用 Azure AD 群組登入的許可權。
    6. 如果有 Azure AD 的登入對應至 Azure AD 使用者帳戶或 Azure AD 群組帳戶，而此帳戶解析為驗證的使用者，則會套用此 Azure AD 登入的擁有權限。

### <a name="service-key-and-service-master-key"></a>服務金鑰和服務主要金鑰

- SQL Database 服務) 所管理的 (不支援[主要金鑰備份](/sql/t-sql/statements/backup-master-key-transact-sql)。
- SQL Database 服務) 管理的 (不支援[主要金鑰還原](/sql/t-sql/statements/restore-master-key-transact-sql)。
- SQL Database 服務) 所管理的 (不支援[服務主要金鑰備份](/sql/t-sql/statements/backup-service-master-key-transact-sql)。
- SQL Database 服務) 管理的 (不支援[服務主要金鑰還原](/sql/t-sql/statements/restore-service-master-key-transact-sql)。

## <a name="configuration"></a>設定

### <a name="buffer-pool-extension"></a>緩衝集區延伸

- 不支援[緩衝集區延伸](/sql/database-engine/configure-windows/buffer-pool-extension)。
- 不支援 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 請參閱 [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>定序

預設執行個體定序為 `SQL_Latin1_General_CP1_CI_AS`，而且可指定為建立參數。 請參閱[定序](/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>相容性層級

- 支援的相容性層級為100、110、120、130、140和150。
- 不支援低於 100 的相容性層級。
- 新資料庫的預設相容性層級為 140。 若為已還原的資料庫，相容性層級會維持不變（如果它是100和更新版本）。

請參閱 [ALTER DATABASE 相容性層級](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>資料庫鏡像

不支援資料庫鏡像。

- 不支援 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 選項。
- 不支援 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

如需詳細資訊，請參閱 [ALTER DATABASE SET PARTNER and SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 和 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>資料庫選項

- 不支援多個記錄檔。
- 「一般用途」服務層級中不支援記憶體內部物件。 
- 每個一般用途實例都有280個檔案的限制，這表示每個資料庫最多可有280個檔案。 一般用途層中的資料和記錄檔都會計入這項限制。 [商務關鍵性層支援每個資料庫32767個](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)檔案。
- 資料庫不能包含包含 filestream 資料的檔案群組。 如果 .bak 包含資料，Restore 會失敗 `FILESTREAM` 。 
- 每個檔案都位於 Azure Blob 儲存體中。 每個檔案的 IO 和輸送量均取決於每個個別檔案的大小。

#### <a name="create-database-statement"></a>CREATE DATABASE 陳述式

下列限制適用于 `CREATE DATABASE` ：

- 無法定義檔案和檔案群組。 
- `CONTAINMENT`不支援此選項。 
- `WITH` 不支援選項。 
   > [!TIP]
   > 因應措施是使用 `ALTER DATABASE` after `CREATE DATABASE` 來設定資料庫選項，以新增檔案或設定內含專案。 

- `FOR ATTACH`不支援此選項。
- `AS SNAPSHOT OF`不支援此選項。

如需詳細資訊，請參閱 [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 陳述式

有些檔案屬性無法設定或變更：

- T-sql 語句中無法指定檔案路徑 `ALTER DATABASE ADD FILE (FILENAME='path')` 。 `FILENAME`因為 SQL 受控執行個體自動放置檔案，所以請從腳本中移除。 
- 無法使用語句來變更檔案名 `ALTER DATABASE` 。

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

- SQL 受控執行個體中目前不支援啟用和停用 SQL Server Agent。 SQL 代理程式一律會處於正在執行的狀態。
- SQL Server Agent 設定是唯讀的。 `sp_set_agent_properties`SQL 受控執行個體不支援此程式。 
- 工作
  - 支援 T-SQL 作業步驟。
  - 支援下列複寫作業：
    - 交易記錄讀取器
    - 快照式
    - 散發者
  - 支援 SSIS 作業步驟。
  - 目前不支援其他類型的作業步驟：
    - 不支援合併複寫作業步驟。 
    - 不支援佇列讀取器。 
    - 尚未支援命令 shell。
  - SQL 受控執行個體無法存取外部資源，例如透過 robocopy 的網路共用。 
  - 不支援 SQL Server Analysis Services。
- 部分支援通知。
- 雖然需要您設定 Database Mail 設定檔，但仍支援電子郵件通知。 SQL Server Agent 只能使用一個 Database Mail 設定檔，而且必須加以呼叫 `AzureManagedInstance_dbmail_profile` 。 
  - 不支援呼叫器。
  - 不支援 NetSend。
  - 尚不支援警示。
  - 不支援 proxy。
- 不支援 EventLog。

目前不支援下列 SQL Agent 功能：

- Proxy
- 排程閒置 CPU 上的工作
- 啟用或停用代理程式
- 警示

如需有關 SQL Server Agent 的資訊，請參閱 [SQL Server Agent](/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>資料表

不支援下列資料表類型：

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
-  (Polybase) 的[外部資料表](/sql/t-sql/statements/create-external-table-transact-sql)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (在一般用途層中不受支援) 

如需有關如何建立和改變數據表的詳細資訊，請參閱 [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) 和 [alter TABLE](/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>Bulk insert/OPENROWSET

SQL 受控執行個體無法存取檔案共用及 Windows 資料夾，因此必須從 Azure Blob 儲存體匯入檔案：

- `DATASOURCE``BULK INSERT`當您從 Azure Blob 儲存體匯入檔案時，命令中必須要有。 請參閱[大量插入](/sql/t-sql/statements/bulk-insert-transact-sql)。
- `DATASOURCE``OPENROWSET`當您從 Azure Blob 儲存體讀取檔案的內容時，此函式中需要有。 請參閱 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENROWSET` 可以用來從 Azure SQL Database、Azure SQL 受控執行個體或 SQL Server 實例讀取資料。 其他來源（例如 Oracle 資料庫或 Excel 檔案）則不受支援。

### <a name="clr"></a>CLR

SQL 受控執行個體無法存取檔案共用及 Windows 資料夾，因此適用下列限制：

- 只支援 `CREATE ASSEMBLY FROM BINARY`。 請參閱 [從二進位檔建立元件](/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支援 `CREATE ASSEMBLY FROM FILE`。 請參閱 [從檔案建立元件](/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 無法參考檔案。 請參閱 [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="database-mail-db_mail"></a>Database Mail (db_mail) 
 - `sp_send_dbmail` 無法使用參數傳送附件 @file_attachments 。 此程式無法存取本機檔案系統和外部共用或 Azure Blob 儲存體。
 - 查看與參數和驗證相關的已知問題 `@query` 。
 
### <a name="dbcc"></a>DBCC

在 SQL Server 中啟用的未記載 DBCC 語句，在 SQL 受控執行個體中不受支援。

- 僅支援有限數目的全域追蹤旗標。 不支援工作階段層級 `Trace flags` 。 請參閱 [追蹤旗標](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) 和 [dbcc TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) 使用有限數目的全域追蹤旗標。
- 無法使用具有選項 REPAIR_ALLOW_DATA_LOSS、REPAIR_FAST 和 REPAIR_REBUILD 的[DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) ，因為無法在模式中設定資料庫， `SINGLE_USER` 請參閱[ALTER database 差異](#alter-database-statement)。 潛在的資料庫損毀是由 Azure 支援團隊處理。 如果有任何資料庫損毀的指示，請 Azure 支援聯絡。

### <a name="distributed-transactions"></a>分散式交易

SQL 受控執行個體目前不支援 MSDTC 和 [彈性交易](../database/elastic-transactions-overview.md) 。

### <a name="extended-events"></a>擴充事件

 (XEvents) 的擴充事件的某些 Windows 特定目標不受支援：

- `etw_classic_sync`不支援目標。 將檔案儲存 `.xel` 在 Azure Blob 儲存體中。 請參閱 [etw_classic_sync 目標](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- `event_file`不支援目標。 將檔案儲存 `.xel` 在 Azure Blob 儲存體中。 請參閱 [event_file 目標](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部程式庫

有限的公開預覽支援資料庫內 R 和 Python 外部程式庫。 請參閱 [AZURE SQL 受控執行個體 (preview) Machine Learning 服務 ](machine-learning-services-overview.md)。

### <a name="filestream-and-filetable"></a>Filestream 與 FileTable

- 不支援 Filestream 資料。
- 資料庫不能包含具有資料的檔案群組 `FILESTREAM` 。
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

SQL 受控執行個體中連結的伺服器支援數量有限的目標：

- 支援的目標為 SQL 受控執行個體、SQL Database、Azure Synapse SQL 和 SQL Server 實例。 
- 連結的伺服器不支援 (MS DTC) 的分散式可寫入交易。
- 不支援的目標為檔案、Analysis Services 和其他 RDBMS。 請嘗試使用 Azure Blob 儲存體的原生 CSV 匯入，或做為檔案匯 `BULK INSERT` `OPENROWSET` 入的替代方法。

作業： 

- 不支援跨執行個體寫入交易。
- 支援使用 `sp_dropserver` 卸除連結的伺服器。 請參閱 [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET`函數只能用來在 SQL Server 實例上執行查詢。 它們可以是受控、內部部署或虛擬機器。 請參閱 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE`函數只能用來在 SQL Server 實例上執行查詢。 它們可以是受控、內部部署或虛擬機器。 僅 `SQLNCLI` 支援、 `SQLNCLI11` 和 `SQLOLEDB` 值做為提供者。 例如 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 請參閱 [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)。
- 連結的伺服器不能用來從網路共用讀取 (Excel、CSV) 的檔案。 請嘗試使用 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) ，從 Azure Blob 儲存體讀取 CSV 檔案。 在[SQL 受控執行個體意見反應專案](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)上追蹤此要求|

### <a name="polybase"></a>PolyBase

不支援參考 HDFS 或 Azure Blob 儲存體中檔案的外部資料表。 如需 PolyBase 的相關資訊，請參閱 [polybase](/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>複寫

- 支援快照式和雙向複寫類型。 不支援合併式複寫、點對點複寫，以及可更新的訂閱。
- [異動複寫](replication-transactional-overview.md) 適用于 SQL 受控執行個體上的公開預覽，但有一些限制：
    - 所有類型的複寫參與者 (「發行者」、「散發者」、「提取訂閱者」和「發送訂閱者」) 都可以放在 SQL 受控執行個體，但是「發行者」和「散發者」必須在雲端或內部部署中。
    - SQL 受控執行個體可以與 SQL Server 的最新版本進行通訊。 如需詳細資訊，請參閱 [支援的版本矩陣](replication-transactional-overview.md#supportability-matrix) 。
    - 異動複寫有一些 [額外的網路需求](replication-transactional-overview.md#requirements)。

如需有關設定異動複寫的詳細資訊，請參閱下列教學課程：
- [SQL MI 發行者與 SQL MI 訂閱者之間的複寫](replication-between-two-instances-configure-tutorial.md)
- [SQL MI 發行者、SQL MI 散發者和 SQL Server 訂閱者之間的複寫](replication-two-instances-and-sql-server-configure-tutorial.md)

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
  - `FROM URL` (Azure Blob 儲存體) 是唯一支援的選項。
  - 不支援 `FROM DISK`/`TAPE`/備份裝置。
  - 不支援備份組。
- `WITH` 不支援選項。 還原的嘗試（包括 `WITH` like `DIFFERENTIAL` 、 `STATS` 、等 `REPLACE` ）將會失敗。
- `ASYNC RESTORE`：即使用戶端連接中斷，還是會繼續還原。 如果中斷連接，您可以查看還原作業的 `sys.dm_operation_status` 狀態，以及建立和卸載資料庫的狀態。 請參閱 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

您可以設定或覆寫下列資料庫選項，之後就無法再變更： 

- `NEW_BROKER` 如果 .bak 檔案中未啟用 broker，則為。 
- `ENABLE_BROKER` 如果 .bak 檔案中未啟用 broker，則為。 
- `AUTO_CLOSE=OFF` 如果 .bak 檔案中的資料庫具有 `AUTO_CLOSE=ON` 。 
- `RECOVERY FULL` 如果 .bak 檔案中的資料庫具有 `SIMPLE` 或 `BULK_LOGGED` 復原模式。
- 如果記憶體優化的檔案群組不在來源 .bak 檔案中，則會新增並呼叫 XTP。 
- 任何現有的記憶體優化檔案群組都會重新命名為 XTP。 
- `SINGLE_USER` 和 `RESTRICTED_USER` 選項會轉換成 `MULTI_USER` 。

限制： 

- 損毀資料庫的備份可能會根據損毀的類型還原，但在修正損毀之前，將不會執行自動備份。 請確定您在 `DBCC CHECKDB` 來源 SQL 受控執行個體上執行，並使用備份 `WITH CHECKSUM` 來避免此問題。
- 還原 `.BAK` 資料庫的檔案，其中包含本檔中所述的任何限制 (例如， `FILESTREAM` 或是 `FILETABLE` 無法在 SQL 受控執行個體上還原的物件) 。
- `.BAK` 無法還原包含多個備份組的檔案。 
- `.BAK` 無法還原包含多個記錄檔的檔案。
- 如果備份包含大於 8 TB 的資料庫、作用中的記憶體內部 OLTP 物件，或每個實例超過280個檔案的檔案數目，就無法在一般用途的實例上還原。 
- 如果備份包含大於 4 TB 的資料庫或記憶體內部 OLTP 物件，且大小總計大於 [資源限制](resource-limits.md) 中所述的大小，則無法在商務關鍵實例上還原。
如需 restore 語句的詳細資訊，請參閱 [restore 語句](/sql/t-sql/statements/restore-statements-transact-sql)。

 > [!IMPORTANT]
 > 相同的限制適用于內建的時間點還原作業。 例如，無法在商務關鍵實例上還原超過 4 TB 的一般目的資料庫。 具有記憶體內部 OLTP 檔案或超過280檔案的商務關鍵資料庫，無法在一般用途實例上還原。

### <a name="service-broker"></a>Service Broker

不支援跨執行個體的服務訊息代理程式：

- `sys.routes`：必要條件是，您必須從 sys. 路由選取位址。 位址在每個路由上必須是本機的。 請參閱 [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`：您無法使用以外 `CREATE ROUTE` 的 `ADDRESS` `LOCAL` 。 請參閱 [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`：您無法使用以外 `ALTER ROUTE` 的 `ADDRESS` `LOCAL` 。 請參閱 [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>預存程式、函數和觸發程式

- `NATIVE_COMPILATION` 一般用途層中不支援。
- 不支援下列 [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 選項： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- 不支援 `sp_execute_external_scripts`。 請參閱 [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支援 `xp_cmdshell`。 請參閱 [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- `Extended stored procedures`不受支援，其中包括 `sp_addextendedproc`   和 `sp_dropextendedproc` 。 請參閱 [擴充預存程式](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支援 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 請參閱 [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

### <a name="system-functions-and-variables"></a>系統函數和變數

下列變數、函式和檢視會傳回不同的結果：

- `SERVERPROPERTY('EngineEdition')` 傳回值8。 這個屬性會唯一識別 SQL 受控執行個體。 請參閱 [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 會傳回 Null，因為實例存在於 SQL Server 的概念不適用於 SQL 受控執行個體。 請參閱 [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 傳回完整的 DNS 「可連接」名稱，例如，my-managed-instance.wcus17662feb9ce98.database.windows.net。 請[參閱 @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS` 傳回完整的 DNS 「可連接」名稱，例如 " `myinstance.domain.database.windows.net` name" 和 "data_source" 屬性。 請參閱 [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 會傳回 Null，因為存在於 SQL Server 的服務概念不適用於 SQL 受控執行個體。 請[參閱 @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql)。
- 支援 `SUSER_ID`。 如果 Azure AD 登入不是 sys.sys登入，則會傳回 Null。 請參閱 [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支援 `SUSER_SID`。 傳回錯誤的資料，這是暫時性的已知問題。 請參閱 [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="environment-constraints"></a><a name="Environment"></a>環境條件約束

### <a name="subnet"></a>子網路
-  您無法將任何其他資源放在已部署 SQL 受控執行個體的子網中， (例如虛擬機器) 。 使用不同的子網部署這些資源。
- 子網必須有足夠的可用 [IP 位址](connectivity-architecture-overview.md#network-requirements)數目。 最小值為16，而建議在子網中至少有32個 IP 位址。
- [服務端點無法與 SQL 受控執行個體的子網相關聯](connectivity-architecture-overview.md#network-requirements)。 當您建立虛擬網路時，請確定已停用 [服務端點] 選項。
- 您可以在區域中部署的虛擬核心和實例類型數目有一些 [限制和限制](resource-limits.md#regional-resource-limitations)。
- [子網上必須套用一些安全性規則](connectivity-architecture-overview.md#network-requirements)。

### <a name="vnet"></a>VNET
- 您可以使用資源模型部署 VNet-不支援 VNet 的傳統模型。
- 建立 SQL 受控執行個體之後，不支援將 SQL 受控執行個體或 VNet 移至另一個資源群組或訂用帳戶。
- 某些服務（例如 App Service 環境、邏輯應用程式和 SQL 受控執行個體 (用於異地複寫、異動複寫或透過連結的伺服器）) 無法存取不同區域中的 SQL 受控執行個體（如果其 Vnet 是使用 [全域對等互連](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)）。 您可以透過 ExpressRoute 或透過 vnet 閘道的 VNet 對 VNet 連接到這些資源。

### <a name="failover-groups"></a>容錯移轉群組
系統資料庫不會複寫至容錯移轉群組中的次要實例。 因此，除非在次要資料庫上以手動方式建立物件，否則不可能在次要實例上相依于系統資料庫中物件的案例。

### <a name="failover-groups"></a>容錯移轉群組
系統資料庫不會複寫至容錯移轉群組中的次要實例。 因此，除非在次要資料庫上以手動方式建立物件，否則不可能在次要實例上相依于系統資料庫中物件的案例。

### <a name="tempdb"></a>Tempdb

一般用途層上的最大檔案大小 `tempdb` 不可超過每個核心 24 GB。 `tempdb`商務關鍵層的最大大小受限於 SQL 受控執行個體儲存體大小。 `Tempdb` 一般用途層的記錄檔大小限制為 120 GB。 如果查詢的每個核心需要 24 GB 以上， `tempdb` 或產生超過 120 GB 的記錄資料，則可能會傳回錯誤。

### <a name="msdb"></a>MSDB

下列 SQL 受控執行個體中的 MSDB 架構必須由其各自預先定義的角色所擁有：

- 一般角色
  - TargetServersRole
- [固定資料庫角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail 角色](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile)：
  - DatabaseMailUserRole
- [Integration services 角色](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)：
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> 依客戶變更預先定義的角色名稱、架構名稱和架構擁有者將會影響服務的正常運作。 對這些變更所做的任何變更，都會在偵測到時立即還原為預先定義的值，或在最新的服務更新時還原回預先定義的值，以確保正常的服務作業。

### <a name="error-logs"></a>錯誤記錄

SQL 受控執行個體將詳細資訊放在錯誤記錄檔中。 錯誤記錄檔中會記錄許多內部系統事件。 您可以使用自訂程式來讀取錯誤記錄檔，以篩選出一些不相關的專案。 如需詳細資訊，請參閱適用于) 的 [sql 受控執行個體-sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) 或 [sql 受控執行個體延伸模組 (preview Azure Data Studio ](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) 。

## <a name="next-steps"></a>後續步驟

- 如需 SQL 受控執行個體的詳細資訊，請參閱 [何謂 sql 受控執行個體？](sql-managed-instance-paas-overview.md)
- 如需功能和比較清單，請參閱 [AZURE SQL 受控執行個體功能比較](../database/features-comparison.md)。
- 如需發行更新和已知問題狀態，請參閱[SQL 受控執行個體版本](../database/doc-changes-updates-release-notes.md)資訊
- 如需示範如何建立新 SQL 受控執行個體的快速入門，請參閱 [建立 sql 受控執行個體](instance-create-quickstart.md)。
