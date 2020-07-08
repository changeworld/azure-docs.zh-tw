---
title: SQL Server & Azure SQL 受控執行個體之間的 t-sql 差異
description: 本文討論 Azure SQL 受控執行個體與 SQL Server 之間的 Transact-sql （T-sql）差異。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 06/02/2020
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 229a74fe760386b59bc83373cc7b1429bd826929
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85298442"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>SQL Server & Azure SQL 受控執行個體之間的 t-sql 差異
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文摘要說明並解釋 Azure SQL 受控執行個體與 SQL Server 之間的語法和行為差異。 


SQL 受控執行個體提供與 SQL Server 資料庫引擎的高度相容性，而且 SQL 受控執行個體中支援大部分的功能。

![移轉](./media/transact-sql-tsql-differences-sql-server/migration.png)

SQL 受控執行個體中引進了一些 PaaS 限制，相較于 SQL Server，還有一些行為變更。 差異分成下列類別：<a name="Differences"></a>

- [可用性](#availability)包括[Always On 可用性群組](#always-on-availability-groups)和[備份](#backup)的差異。
- [安全性](#security)包括[審核](#auditing)、[憑證](#credential)、認證[、](#certificates)[密碼編譯提供者](#cryptographic-providers)、登入[和使用者](#logins-and-users)，以及[服務金鑰和服務主要金鑰](#service-key-and-service-master-key)的差異。
- [Configuration](#configuration)設定包括[緩衝集區延伸](#buffer-pool-extension)、定[序](#collation)、[相容性層級](#compatibility-levels)、[資料庫鏡像](#database-mirroring)、[資料庫選項](#database-options)、 [SQL Server Agent](#sql-server-agent)和[資料表選項](#tables)的差異。
- [功能](#functionalities)包括[BULK INSERT/OPENROWSET](#bulk-insert--openrowset)、 [CLR](#clr)、 [DBCC](#dbcc)、[分散式交易](#distributed-transactions)、[擴充的事件](#extended-events)、[外部程式庫](#external-libraries)、 [filestream 和 FileTable](#filestream-and-filetable)、[全文檢索語義搜尋](#full-text-semantic-search)、[連結的伺服器](#linked-servers)、 [PolyBase](#polybase) [、複寫](#replication)、[還原](#restore-statement)、 [Service Broker](#service-broker)、[預存程式、函數和觸發](#stored-procedures-functions-and-triggers)程式。
- [環境設定](#Environment)，例如 vnet 和子網設定。

大部分的功能都是架構條件約束，並代表服務功能。

在 SQL 受控執行個體中探索到的暫時性已知問題，未來將會在[版本資訊頁面](../database/doc-changes-updates-release-notes.md)中加以解決。

## <a name="availability"></a>可用性

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Always On 可用性群組

SQL 受控執行個體內建[高可用性](../database/high-availability-sla.md)，而且不能由使用者控制。 不支援下列語句：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [建立可用性群組](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)語句的[SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr)子句

### <a name="backup"></a>備份

SQL 受控執行個體具有自動備份，因此使用者可以建立完整資料庫 `COPY_ONLY` 備份。 不支援差異、記錄和檔案快照集備份。

- 使用 SQL 受控執行個體，您只能將實例資料庫備份至 Azure Blob 儲存體帳戶：
  - 只支援 `BACKUP TO URL`。
  - `FILE``TAPE`不支援、和備份裝置。
- 支援大部分的一般 `WITH` 選項。
  - `COPY_ONLY`是必要的。
  - 不支援 `FILE_SNAPSHOT`。
  - 磁帶選項： `REWIND` `NOREWIND` `UNLOAD` 不支援、、和 `NOUNLOAD` 。
  - 記錄檔特有的選項 `NORECOVERY` ： `STANDBY` `NO_TRUNCATE` 不支援、和。

限制： 

- 使用 SQL 受控執行個體，您可以將實例資料庫備份至最多32個等量的備份，這對於在使用備份壓縮的情況下，最多可達 4 TB 的資料庫。
- 您無法 `BACKUP DATABASE ... WITH COPY_ONLY` 在使用服務管理的透明資料加密（TDE）加密的資料庫上執行。 服務管理的 TDE 會強制使用內部 TDE 金鑰來加密備份。 無法匯出金鑰，因此您無法還原備份。 使用自動備份和時間點還原，或改為使用[客戶管理的（BYOK） TDE](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) 。 您也可以在資料庫上停用加密。
- 使用 SQL 受控執行個體中的命令，備份等量大小上限 `BACKUP` 是 195 GB，這是最大 blob 大小。 在備份命令中增加等量磁碟區的數目，以減少個別的等量磁碟區大小並維持在這項限制內。

    > [!TIP]
    > 若要解決這項限制，當您從內部部署環境或虛擬機器中的 SQL Server 備份資料庫時，您可以：
    >
    > - 備份至， `DISK` 而不是備份至 `URL` 。
    > - 將備份檔案上傳至 Blob 儲存體。
    > - 還原至 SQL 受控執行個體。
    >
    > `Restore`SQL 受控執行個體中的命令在備份檔案中支援較大的 blob 大小，因為用來儲存已上傳備份檔案的不同 blob 類型。

如需使用 T-SQL 進行備份的相關資訊，請參閱[備份](/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>稽核

Microsoft Azure SQL 和 SQL Server 中的審核之間的主要差異如下：

- 有了 SQL 受控執行個體，就可以在伺服器層級進行審核。 `.xel`記錄檔會儲存在 Azure Blob 儲存體中。
- 有了 Azure SQL Database，審核功能就會在資料庫層級運作。 `.xel`記錄檔會儲存在 Azure Blob 儲存體中。
- 使用內部部署或虛擬機器中的 SQL Server，審核功能會在伺服器層級運作。 事件會儲存在檔案系統或 Windows 事件記錄檔上。
 
SQL 受控執行個體中的 XEvent 審核支援 Azure Blob 儲存體目標。 不支援檔案和 Windows 記錄檔。

向 Azure Blob 儲存體進行稽核的 `CREATE AUDIT` 語法有一個主要差異：

- 提供新的語法 `TO URL` ，您可以使用來指定放置檔案之 Azure Blob 儲存體容器的 URL `.xel` 。
- `TO FILE`因為 SQL 受控執行個體無法存取 Windows 檔案共用，所以不支援此語法。

如需詳細資訊，請參閱： 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [稽核](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>憑證

SQL 受控執行個體無法存取檔案共用和 Windows 資料夾，因此適用下列條件約束：

- 檔案 `CREATE FROM` / `BACKUP TO` 不支援憑證。
- `CREATE` / `BACKUP` 不支援來自的憑證 `FILE` / `ASSEMBLY` 。 無法使用私密金鑰檔案。 

請參閱 [CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql)。 
 
因應**措施：您**可以改[為取得憑證二進位內容和私密金鑰、將它儲存為 .sql 檔案，並從二進位檔建立](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)，而不是建立憑證的備份及還原備份：

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
- 支援使用[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)語法建立的 Azure Active Directory （Azure AD）伺服器主體（登入），或[從 Login [Azure AD 登入]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)語法建立使用者。 這些登入是在伺服器層級建立。

    SQL 受控執行個體支援使用語法 Azure AD 資料庫主體 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 。 這項功能也稱為 Azure AD 自主資料庫使用者。

- 不支援使用語法建立的 Windows 登入 `CREATE LOGIN ... FROM WINDOWS` 。 使用 Azure Active Directory 登入和使用者。
- 建立實例的 Azure AD 使用者具有不[受限制的系統管理員許可權](../database/logins-create-manage.md)。
- 您可以使用語法來建立非系統管理員 Azure AD 資料庫層級的使用者 `CREATE USER ... FROM EXTERNAL PROVIDER` 。 請參閱[建立使用者 .。。來自外部提供者](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。
- Azure AD 伺服器主體（登入）僅支援一個 SQL 受控執行個體中的 SQL 功能。 需要跨實例互動的功能，不論它們是否位於相同的 Azure AD 租使用者或不同的 tenant 中，Azure AD 的使用者都不支援。 這類功能的範例如下：

  - SQL 異動複寫。
  - 連結伺服器。

- 不支援將對應至 Azure AD 群組的 Azure AD 登入設定為資料庫擁有者。
- 支援使用其他 Azure AD 主體來模擬 Azure AD 伺服器層級主體，例如[EXECUTE as](/sql/t-sql/statements/execute-as-transact-sql)子句。 執行身分限制包括：

  - 當名稱不同于登入名稱時，不支援 Azure AD 使用者執行 AS 使用者。 例如，透過從登入 [] 的 CREATE USER [myAadUser] 語法建立使用者 john@contoso.com ，並透過 EXEC 以 user = _myAadUser_嘗試模擬。 當您從 Azure AD 伺服器主體（登入）建立**使用者**時，請將 user_name 指定為**登**入的相同 login_name。
  - 只有屬於角色一部分的 SQL Server 層級主體（登入） `sysadmin` 可以執行下列以 Azure AD 主體為目標的作業：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- 使用[SSMS v 18.4 或更新版本](/sql/ssms/download-sql-server-management-studio-ssms)，或[SQLPackage.exe](/sql/tools/sqlpackage-download)的 SQL 受控執行個體中的 Azure AD 使用者，都支援使用 bacpac 檔案進行資料庫匯出/匯入。
  - 使用資料庫 bacpac 檔案可支援下列設定： 
    - 在相同 Azure AD 網域內的不同管理實例之間匯出/匯入資料庫。
    - 從 SQL 受控執行個體匯出資料庫，並匯入至相同 Azure AD 網域內的 SQL Database。 
    - 從 SQL Database 匯出資料庫，然後匯入至相同 Azure AD 網域內的 SQL 受控執行個體。
    - 從 SQL 受控執行個體匯出資料庫，並匯入至 SQL Server （版本2012或更新版本）。
      - 在此設定中，所有 Azure AD 使用者都會建立為 SQL Server 的資料庫主體（使用者），而不需要登入。 使用者的類型會列為 `SQL` ，並顯示為 `SQL_USER` database_principals）。 其許可權和角色會保留在 SQL Server 資料庫中繼資料中，並且可用於模擬。 不過，它們不能用來存取和使用其認證來登入 SQL Server。

- 只有 SQL 受控執行個體布建程式所建立的伺服器層級主體登入、伺服器角色的成員（例如 `securityadmin` 或），或是在 `sysadmin` 伺服器層級具有 ALTER ANY login 許可權的其他登入，才能在 SQL 受控執行個體的 master 資料庫中建立 Azure AD 伺服器主體（登入）。
- 如果登入是 SQL 主體，只有屬於 `sysadmin` 角色一部分的登入可以使用 create 命令來為 Azure AD 帳戶建立登入。
- Azure AD 登入必須是用於 Azure SQL 受控執行個體的相同目錄中 Azure AD 的成員。
- 從 SQL Server Management Studio 18.0 preview 5 開始，物件總管可以看到 Azure AD 伺服器主體（登入）。
- 可允許 Azure AD 伺服器主體 (登入) 與 Azure AD 系統管理員帳戶重疊。 當您解析主體並將許可權套用至 SQL 受控執行個體時，Azure AD 伺服器主體（登入）會優先于 Azure AD 管理員。
- 在驗證期間，會套用下列順序來解析驗證主體：

    1. 如果 Azure AD 帳戶以直接對應至 Azure AD 伺服器主體（登入）的形式存在，這會出現在 sys.databases 中，server_principals 為類型 "E"、授與存取權，並套用 Azure AD 伺服器主體（登入）的許可權。
    2. 如果 Azure AD 帳戶是對應至 Azure AD 伺服器主體（登入）的 Azure AD 群組成員，而該群組存在於 sys. server_principals 中，其類型為 "X"，則授與存取權並套用 Azure AD 群組登入的許可權。
    3. 如果 Azure AD 帳戶是特殊的入口網站設定 Azure AD admin for SQL 受控執行個體（不存在於 SQL 受控執行個體系統檢視中），請套用適用于 SQL Azure AD （舊版模式）之受控執行個體系統管理員的特殊固定許可權。
    4. 如果 Azure AD 帳戶是直接對應到資料庫中的 Azure AD 使用者（在 sys. database_principals 為類型 "E"），則會授與存取權，並套用 Azure AD 資料庫使用者的許可權。
    5. 如果 Azure AD 帳戶是對應至資料庫中 Azure AD 使用者的 Azure AD 群組成員（在 database_principals sys.databases 中會顯示為 "X" 類型），則會授與存取權，並套用 Azure AD 群組登入的許可權。
    6. 如果有 Azure AD 登入對應到 Azure AD 使用者帳戶或 Azure AD 群組帳戶（解析為進行驗證的使用者），則會套用此 Azure AD 登入的擁有權限。

### <a name="service-key-and-service-master-key"></a>服務金鑰和服務主要金鑰

- 不支援[主要金鑰備份](/sql/t-sql/statements/backup-master-key-transact-sql)（由 SQL Database 服務管理）。
- 不支援[主要金鑰還原](/sql/t-sql/statements/restore-master-key-transact-sql)（由 SQL Database 服務管理）。
- 不支援[服務主要金鑰備份](/sql/t-sql/statements/backup-service-master-key-transact-sql)（由 SQL Database 服務管理）。
- 不支援[服務主要金鑰還原](/sql/t-sql/statements/restore-service-master-key-transact-sql)（由 SQL Database 服務管理）。

## <a name="configuration"></a>組態

### <a name="buffer-pool-extension"></a>緩衝集區延伸

- 不支援[緩衝集區延伸](/sql/database-engine/configure-windows/buffer-pool-extension)模組。
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
- 每個一般用途實例都有280個檔案的限制，這表示每個資料庫最多可有280個檔案。 一般用途層中的資料和記錄檔都會計入此限制。 [業務關鍵層支援每個資料庫32767個](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)檔案。
- 資料庫不能包含包含 filestream 資料的檔案群組。 如果 .bak 包含資料，還原將會失敗 `FILESTREAM` 。 
- 每個檔案都位於 Azure Blob 儲存體中。 每個檔案的 IO 和輸送量均取決於每個個別檔案的大小。

#### <a name="create-database-statement"></a>CREATE DATABASE 陳述式

下列限制適用于 `CREATE DATABASE` ：

- 無法定義檔案和檔案群組。 
- `CONTAINMENT`不支援此選項。 
- `WITH`不支援選項。 
   > [!TIP]
   > 因應措施是使用 `ALTER DATABASE` after `CREATE DATABASE` 設定資料庫選項來新增檔案或設定內含專案。 

- `FOR ATTACH`不支援此選項。
- `AS SNAPSHOT OF`不支援此選項。

如需詳細資訊，請參閱 [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 陳述式

有些檔案屬性無法設定或變更：

- 不能在 T-sql 語句中指定檔案路徑 `ALTER DATABASE ADD FILE (FILENAME='path')` 。 `FILENAME`從腳本中移除，因為 SQL 受控執行個體會自動放置檔案。 
- 無法使用語句變更檔案名 `ALTER DATABASE` 。

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

- SQL 受控執行個體目前不支援啟用和停用 SQL Server Agent。 SQL 代理程式一律會處於正在執行的狀態。
- SQL Server Agent 設定是唯讀的。 `sp_set_agent_properties`SQL 受控執行個體中不支援此程式。 
- 工作
  - 支援 T-SQL 作業步驟。
  - 支援下列複寫作業：
    - 交易記錄讀取器
    - 快照式
    - 散發者
  - 支援 SSIS 作業步驟。
  - 目前不支援其他類型的作業步驟：
    - 不支援合併式複寫作業步驟。 
    - 不支援佇列讀取器。 
    - 尚未支援命令 shell。
  - SQL 受控執行個體無法存取外部資源（例如，透過 robocopy 的網路共用）。 
  - 不支援 SQL Server Analysis Services。
- 部分支援通知。
- 雖然您需要設定 Database Mail 設定檔，但仍可支援電子郵件通知。 SQL Server Agent 只能使用一個 Database Mail 設定檔，而且必須呼叫它 `AzureManagedInstance_dbmail_profile` 。 
  - 不支援呼叫器。
  - 不支援 NetSend。
  - 尚不支援警示。
  - 不支援 proxy。
- 不支援 EventLog。

目前不支援下列 SQL 代理程式功能：

- Proxy
- 在閒置 CPU 上排程工作
- 啟用或停用代理程式
- 警示

如需有關 SQL Server Agent 的資訊，請參閱 [SQL Server Agent](/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>資料表

不支援下列資料表類型：

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [外部資料表](/sql/t-sql/statements/create-external-table-transact-sql)（Polybase）
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) （只在一般用途層中不支援）

如需有關如何建立和改變數據表的詳細資訊，請參閱[CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql)和[alter TABLE](/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>Bulk insert/OPENROWSET

SQL 受控執行個體無法存取檔案共用和 Windows 資料夾，因此必須從 Azure Blob 儲存體匯入檔案：

- `DATASOURCE``BULK INSERT`當您從 Azure Blob 儲存體匯入檔案時，命令中需要。 請參閱[大量插入](/sql/t-sql/statements/bulk-insert-transact-sql)。
- `DATASOURCE``OPENROWSET`當您從 Azure Blob 儲存體讀取檔案的內容時，函數中需要。 請參閱 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENROWSET`可以用來從 Azure SQL Database、Azure SQL 受控執行個體或 SQL Server 實例讀取資料。 不支援 Oracle 資料庫或 Excel 檔案之類的其他來源。

### <a name="clr"></a>CLR

SQL 受控執行個體無法存取檔案共用和 Windows 資料夾，因此適用下列條件約束：

- 只支援 `CREATE ASSEMBLY FROM BINARY`。 請參閱[CREATE ASSEMBLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支援 `CREATE ASSEMBLY FROM FILE`。 請參閱[CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 無法參考檔案。 請參閱[ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="database-mail-db_mail"></a>Database Mail （db_mail）
 - `sp_send_dbmail`無法使用參數傳送附件 @file_attachments 。 本機檔案系統和外部共用或 Azure Blob 儲存體無法從這個程式存取。
 - 查看與參數和驗證相關的已知問題 `@query` 。
 
### <a name="dbcc"></a>DBCC

SQL 受控執行個體不支援在 SQL Server 中啟用的未記載 DBCC 語句。

- 僅支援有限數目的全域追蹤旗標。 不支援工作階段層級 `Trace flags` 。 請參閱[追蹤旗標](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)和[dbcc TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)會使用有限數目的全域追蹤旗標。
- 無法使用選項 REPAIR_ALLOW_DATA_LOSS、REPAIR_FAST 和 REPAIR_REBUILD 的[DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) ，因為無法在模式中設定資料庫， `SINGLE_USER` 請參閱[ALTER database 差異](#alter-database-statement)。 Azure 支援小組會處理潛在的資料庫損毀。 若有任何資料庫損毀的跡象，請聯絡 Azure 支援。

### <a name="distributed-transactions"></a>分散式交易

SQL 受控執行個體中目前不支援 MSDTC 和[彈性交易](../database/elastic-transactions-overview.md)。

### <a name="extended-events"></a>擴充的事件

不支援擴充事件（XEvents）的某些 Windows 特定目標：

- `etw_classic_sync`不支援目標。 將檔案儲存 `.xel` 在 Azure Blob 儲存體中。 請參閱 [etw_classic_sync 目標](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- `event_file`不支援目標。 將檔案儲存 `.xel` 在 Azure Blob 儲存體中。 請參閱 [event_file 目標](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部程式庫

資料庫內 R 和 Python 尚未支援外部程式庫。 請參閱 [SQL Server Machine Learning 服務](/sql/advanced-analytics/r/sql-server-r-services)。

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

SQL 受控執行個體中的連結伺服器支援的目標數目有限：

- 支援的目標為 SQL 受控執行個體、SQL Database、Azure Synapse SQL 和 SQL Server 實例。 
- 連結的伺服器不支援分散式可寫入交易（MS DTC）。
- 不支援的目標為檔案、Analysis Services 和其他 RDBMS。 請嘗試使用或中的原生 CSV 匯入 Azure Blob 儲存體，做為檔案匯 `BULK INSERT` `OPENROWSET` 入的替代方法。

作業： 

- 不支援跨執行個體寫入交易。
- 支援使用 `sp_dropserver` 卸除連結的伺服器。 請參閱 [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET`函數只能用來在 SQL Server 實例上執行查詢。 它們可以是受控、內部部署或在虛擬機器中。 請參閱 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE`函數只能用來在 SQL Server 實例上執行查詢。 它們可以是受控、內部部署或在虛擬機器中。 僅 `SQLNCLI` 支援、 `SQLNCLI11` 和 `SQLOLEDB` 值做為提供者。 例如 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 請參閱 [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)。
- 連結的伺服器無法用來從網路共用讀取檔案（Excel、CSV）。 嘗試使用從 Azure Blob 儲存體讀取 CSV 檔案的[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 。 在[SQL 受控執行個體意見反應專案](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)上追蹤此要求|

### <a name="polybase"></a>PolyBase

不支援參考 HDFS 或 Azure Blob 儲存體中檔案的外部資料表。 如需有關 PolyBase 的詳細資訊，請參閱[polybase](/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>複寫

- 支援快照和雙向複寫類型。 不支援合併式複寫、點對點複寫和可更新的訂閱。
- [異動複寫](replication-transactional-overview.md)在 SQL 受控執行個體上可供公開預覽，但有一些條件約束：
    - 所有類型的複寫參與者（發行者、散發者、提取訂閱者和發送訂閱者）都可以放在 SQL 受控執行個體，但發行者和散發者必須同時位於雲端或內部部署兩者。
    - SQL 受控執行個體可以與最新版本的 SQL Server 通訊。 如需詳細資訊，請參閱[支援的版本矩陣](replication-transactional-overview.md#supportability-matrix)。
    - 異動複寫有一些[額外的網路需求](replication-transactional-overview.md#requirements)。

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
  - `FROM URL`（Azure Blob 儲存體）是唯一支援的選項。
  - 不支援 `FROM DISK`/`TAPE`/備份裝置。
  - 不支援備份組。
- `WITH`不支援選項。 包含 like、、等的還原嘗試 `WITH` `DIFFERENTIAL` `STATS` `REPLACE` 將會失敗。
- `ASYNC RESTORE`：即使用戶端連接中斷，還是會繼續還原。 如果您的連接已中斷，您可以 `sys.dm_operation_status` 查看還原作業的狀態，以及建立和卸載資料庫的狀態。 請參閱 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

系統會設定或覆寫下列資料庫選項，而且之後無法變更： 

- `NEW_BROKER`如果未在 .bak 檔案中啟用訊息代理程式，則為。 
- `ENABLE_BROKER`如果未在 .bak 檔案中啟用訊息代理程式，則為。 
- `AUTO_CLOSE=OFF`如果 .bak 檔案中的資料庫具有 `AUTO_CLOSE=ON` 。 
- `RECOVERY FULL`如果 .bak 檔案中的資料庫具有 `SIMPLE` 或 `BULK_LOGGED` 復原模式，則為。
- 系統會新增記憶體優化的檔案群組，如果它不在來源 .bak 檔案中，則會呼叫 XTP。 
- 任何現有的記憶體優化檔案群組都會重新命名為 XTP。 
- `SINGLE_USER`和 `RESTRICTED_USER` 選項會轉換成 `MULTI_USER` 。

限制： 

- 損毀資料庫的備份可能會根據損毀的類型還原，但在修正損毀之前，將不會採取自動備份。 請確定您是 `DBCC CHECKDB` 在來源 SQL 受控執行個體上執行並使用備份， `WITH CHECKSUM` 以避免發生此問題。
- `.BAK`無法在 SQL 受控執行個體上還原包含本檔中所述任何限制的資料庫檔案（例如 `FILESTREAM` 或 `FILETABLE` 物件）。
- `.BAK`無法還原包含多個備份組的檔案。 
- `.BAK`無法還原包含多個記錄檔的檔案。
- 包含大於 8 TB、作用中記憶體內部 OLTP 物件的資料庫，或每個實例超過280個檔案的備份，都無法在一般用途的實例上還原。 
- 包含大於 4 TB 的資料庫或記憶體內部 OLTP 物件的備份，其大小總計大於[資源限制](resource-limits.md)中所述的大小，因此無法在業務關鍵實例上還原。
如需 restore 語句的詳細資訊，請參閱[restore 語句](/sql/t-sql/statements/restore-statements-transact-sql)。

 > [!IMPORTANT]
 > 相同的限制適用于內建的時間點還原作業。 例如，無法在業務關鍵實例上還原超過 4 TB 的一般目的資料庫。 具有記憶體內部 OLTP 檔案或超過280個檔案的商務關鍵資料庫，無法在一般用途實例上還原。

### <a name="service-broker"></a>Service Broker

不支援跨執行個體的服務訊息代理程式：

- `sys.routes`：先決條件是，您必須從 sys.databases 選取位址。 位址在每個路由上都必須是 LOCAL。 請參閱 [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`：您無法使用 `CREATE ROUTE` 與 `ADDRESS` 以外的 `LOCAL` 。 請參閱 [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`：您無法使用 `ALTER ROUTE` 與 `ADDRESS` 以外的 `LOCAL` 。 請參閱 [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>預存程式、函數和觸發程式

- `NATIVE_COMPILATION`一般用途層中不支援。
- 不支援下列 [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 選項： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- 不支援 `sp_execute_external_scripts`。 請參閱 [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支援 `xp_cmdshell`。 請參閱 [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- `Extended stored procedures`不支援，其中包括 `sp_addextendedproc`   和 `sp_dropextendedproc` 。 請參閱[擴充預存程式](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支援 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 請參閱 [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

### <a name="system-functions-and-variables"></a>系統函數和變數

下列變數、函式和檢視會傳回不同的結果：

- `SERVERPROPERTY('EngineEdition')`傳回值8。 此屬性可唯一識別 SQL 受控執行個體。 請參閱 [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')`會傳回 Null，因為實例存在於 SQL Server 的概念不適用於 SQL 受控執行個體。 請參閱 [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME`傳回完整的 DNS 「可連線」名稱，例如 my-managed-instance.wcus17662feb9ce98.database.windows.net。 請[參閱 @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS`傳回完整的 DNS 「可連線」名稱，例如 `myinstance.domain.database.windows.net` 屬性「名稱」和「data_source」。 請參閱 [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME`會傳回 Null，因為存在於 SQL Server 的服務概念不適用於 SQL 受控執行個體。 請[參閱 @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql)。
- 支援 `SUSER_ID`。 如果 Azure AD 登入不在 sys.sys登入時，它會傳回 Null。 請參閱 [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支援 `SUSER_SID`。 傳回錯誤的資料，這是暫時性的已知問題。 請參閱 [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="environment-constraints"></a><a name="Environment"></a>環境條件約束

### <a name="subnet"></a>子網路
-  您無法將任何其他資源（例如虛擬機器）放在已部署 SQL 受控執行個體的子網中。 使用不同的子網來部署這些資源。
- 子網必須有足夠的可用[IP 位址](connectivity-architecture-overview.md#network-requirements)數目。 最小值為16，而建議在子網中至少要有32個 IP 位址。
- [服務端點無法與 SQL 受控執行個體的子網建立關聯](connectivity-architecture-overview.md#network-requirements)。 當您建立虛擬網路時，請確定已停用 [服務端點] 選項。
- 您可以在區域中部署的虛擬核心數目和實例類型有一些[條件約束和限制](resource-limits.md#regional-resource-limitations)。
- 有一些[必須在子網上套用的安全性規則](connectivity-architecture-overview.md#network-requirements)。

### <a name="vnet"></a>VNET
- VNet 可以使用資源模型來部署-不支援 VNet 的傳統模型。
- 建立 SQL 受控執行個體之後，不支援將 SQL 受控執行個體或 VNet 移至另一個資源群組或訂用帳戶。
- 某些服務（例如 App Service 環境、邏輯應用程式和 SQL 受控執行個體（用於異地複寫、異動複寫或透過連結的伺服器）無法存取不同區域中的 SQL 受控執行個體（如果其 Vnet 是使用[全域對等互連](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)進行連線）。 您可以透過 ExpressRoute 或透過 VNet 閘道的 VNet 對 VNet 來連接到這些資源。

### <a name="failover-groups"></a>容錯移轉群組
系統資料庫不會複寫到容錯移轉群組中的次要實例。 因此，相依于系統資料庫中之物件的案例，將無法在次要實例上進行，除非物件是在次要複本上手動建立的。

### <a name="failover-groups"></a>容錯移轉群組
系統資料庫不會複寫到容錯移轉群組中的次要實例。 因此，相依于系統資料庫中之物件的案例，將無法在次要實例上進行，除非物件是在次要複本上手動建立的。

### <a name="tempdb"></a>TEMPDB

`tempdb`一般用途層上的檔案大小上限不能大於每個核心 24 GB。 `tempdb`業務關鍵層的大小上限受到 SQL 受控執行個體儲存體大小的限制。 `Tempdb`一般用途層上的記錄檔大小限制為 120 GB。 如果某些查詢在中的每個核心需要超過 24 GB， `tempdb` 或產生超過 120 gb 的記錄資料，則可能會傳回錯誤。

### <a name="msdb"></a>MSDB

SQL 受控執行個體中的下列 MSDB 架構必須由其各自預先定義的角色所擁有：

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
> 變更客戶的預先定義角色名稱、架構名稱和架構擁有者，將會影響服務的正常運作。 對這些進行的任何變更都將會在偵測到，或在最新的下一個服務更新時還原回預先定義的值，以確保正常的服務作業。

### <a name="error-logs"></a>錯誤記錄

SQL 受控執行個體會在錯誤記錄檔中放入詳細資訊。 錯誤記錄檔中有許多內部系統事件會記錄下來。 使用自訂程式來讀取篩選掉一些不相關專案的錯誤記錄檔。 如需詳細資訊，請參閱適用于 Azure Data Studio 的[sql 受控執行個體– sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)或[sql 受控執行個體延伸模組（預覽）](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) 。

## <a name="next-steps"></a>後續步驟

- 如需 SQL 受控執行個體的詳細資訊，請參閱[什麼是 sql 受控執行個體？](sql-managed-instance-paas-overview.md)
- 如需功能和比較清單，請參閱[AZURE SQL 受控執行個體功能比較](../database/features-comparison.md)。
- 如需發行更新和已知問題的狀態，請參閱[SQL 受控執行個體版本](../database/doc-changes-updates-release-notes.md)資訊
- 如需說明如何建立新 SQL 受控執行個體的快速入門，請參閱[建立 sql 受控執行個體](instance-create-quickstart.md)。
