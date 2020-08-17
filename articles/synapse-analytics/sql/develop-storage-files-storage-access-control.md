---
title: 控制 SQL 隨選 (預覽版) 的儲存體帳戶存取
description: 說明 SQL 隨選 (預覽版) 如何存取 Azure 儲存體，以及您如何在 Azure Synapse Analytics 中控制 SQL 隨選的儲存體存取。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: fd4cc4cfa7b7be9085ac404cab7fc7447b6d66a7
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987132"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>控制 SQL 隨選 (預覽版) 的儲存體帳戶存取

SQL 隨選查詢會直接從 Azure 儲存體讀取檔案。 存取 Azure 儲存體上檔案的權限會由兩個層級來控制：
- **儲存體層級** - 使用者應該具有存取基礎儲存體檔案的權限。 您的儲存體管理員應允許 Azure AD 主體讀取/寫入檔案，或產生將用來存取儲存體的 SAS 金鑰。
- **SQL 服務層級** - 使用者應該具有從[外部資料表](develop-tables-external-tables.md)讀取資料的 `SELECT` 權限，或執行 `OPENROWSET` 的 `ADMINISTER BULK ADMIN` 權限，以及使用存取儲存體所需認證的權限。

本文將說明您可以使用的認證類型，以及如何為 SQL 和 Azure AD 使用者制訂認證查閱。

## <a name="supported-storage-authorization-types"></a>支援的儲存體授權類型

已登入 SQL 隨選資源的使用者必須獲得授權，才能存取及查詢 Azure 儲存體中未公開使用的檔案。 您可以使用三種授權類型來存取非公用儲存體：[使用者身分識別](?tabs=user-identity)、[共用存取簽章](?tabs=shared-access-signature)以及[受控識別](?tabs=managed-identity)。

> [!NOTE]
> 當您建立工作區時，**Azure AD 傳遞**是預設的行為。

### <a name="user-identity"></a>[使用者身分識別](#tab/user-identity)

**使用者身分識別** (也稱為「Azure AD 傳遞」) 是一種授權類型，其使用登入 SQL 隨選的 Azure AD 使用者身分識別來授與資料存取權。 存取資料之前，Azure 儲存體管理員必須將權限授與 Azure AD 使用者。 如下表所示，SQL 使用者類型不支援此方式。

> [!IMPORTANT]
> 您必須具有儲存體 Blob 資料擁有者/參與者/讀者角色，才能使用您的身分識別來存取資料。
> 即使您是儲存體帳戶的擁有者，仍然需要將自己新增至其中一個儲存體 Blob 資料角色。
>
> 若要深入了解 Azure Data Lake Store Gen2 中的存取控制，請參閱 [Azure Data Lake Storage Gen2 文章中的存取控制](../../storage/blobs/data-lake-storage-access-control.md)一文。
>

### <a name="shared-access-signature"></a>[共用存取簽章](#tab/shared-access-signature)

**共用存取簽章 (SAS)** 可提供您儲存體帳戶中資源的委派存取權。 透過 SAS，客戶可以對用戶端授與儲存體帳戶中資源的存取權，而不必共用帳戶金鑰。 針對具有 SAS 的用戶端，SAS 可讓您精準控制這些用戶端應獲得的存取類型，包括有效性間隔、授與的權限、可接受的 IP 位址範圍，以及可接受的通訊協定 (HTTPs/HTTP)。

若要取得 SAS 權杖，您可以瀏覽至 **Azure 入口網站 -> 儲存體帳戶 -> 共用存取簽章 -> 設定權限 -> 產生 SAS 和連接字串。**

> [!IMPORTANT]
> 產生 SAS 權杖時，權杖的開頭會包含問號 ('?')。 若要在 SQL 隨選中使用權杖，您必須在建立認證時移除問號 ('?')。 例如：
>
> SAS 權杖：?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

您必須建立資料庫範圍或伺服器範圍的認證，才能使用 SAS 權杖來啟用存取。

### <a name="managed-identity"></a>[受控身分識別](#tab/managed-identity)

**受控識別**也稱為 MSI。 這是 Azure Active Directory (Azure AD) 的功能，可為 SQL 隨選提供 Azure 服務。 此外，也會在 Azure AD 中部署自動受控識別。 在 Azure 儲存體中，此身分識別可用來授與資料存取要求的權限。

存取資料之前，Azure 儲存體管理員必須將權限授與受控識別，才能存取資料。 將權限授與受控識別的方式，與對任何其他 Azure AD 使用者授與權限的方式一樣。

### <a name="anonymous-access"></a>[匿名存取](#tab/public-access)

您可以存取放在 Azure 儲存體帳戶上的公用檔案，而且這些檔案[允許匿名存取](/azure/storage/blobs/storage-manage-access-to-resources)。

---

### <a name="supported-authorization-types-for-databases-users"></a>資料庫使用者可用的授權類型

在下表中，您可以找到可用的授權類型：

| 授權類型                    | SQL 使用者    | Azure AD 使用者     |
| ------------------------------------- | ------------- | -----------    |
| [使用者身分識別](?tabs=user-identity#supported-storage-authorization-types)       | 不支援 | 支援      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | 支援     | 支援      |
| [受控身分識別](?tabs=managed-identity#supported-storage-authorization-types) | 不支援 | 支援      |

### <a name="supported-storages-and-authorization-types"></a>支援的儲存體和授權類型

您可以使用下列的授權和 Azure 儲存體類型組合：

| 授權類型  | Blob 儲存體   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| SAS    | 支援\*      | 不支援   | 支援\*     |
| [受控身分識別](?tabs=managed-identity#supported-storage-authorization-types) | 支援      | 支援        | 支援     |
| [使用者身分識別](?tabs=user-identity#supported-storage-authorization-types)    | 支援\*      | 支援\*        | 支援\*     |

\* SAS 權杖和 Azure AD 身分識別可用來存取未受防火牆保護的儲存體。

> [!IMPORTANT]
> 存取受防火牆保護的儲存體時，只能使用受控識別。 您必須[允許受信任的 Microsoft 服務... 設定](../../storage/common/storage-network-security.md#trusted-microsoft-services)，並針對該資源執行個體明確[指派 Azure 角色](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)給[系統指派的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。 在此情況下，執行個體的存取範圍會對應至指派給受控識別的 Azure 角色。
>

## <a name="credentials"></a>認證

若要查詢 Azure 儲存體中的檔案，您的 SQL 隨選端點需要其中包含驗證資訊的認證。 您可以使用兩種類型的認證：
- 伺服器層級的認證適用於使用 `OPENROWSET` 函式執行的特定查詢。 認證名稱必須符合儲存體 URL。
- DATABASE SCOPED CREDENTIAL 適用於外部資料表。 外部資料表會參考具有存取儲存體所需認證的 `DATA SOURCE`。

若要允許使用者建立或卸載認證，系統管理員可以對使用者執行 GRANT/DENY ALTER ANY CREDENTIAL 權限：

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

存取外部儲存體的資料庫使用者必須擁有使用認證的權限。

### <a name="grant-permissions-to-use-credential"></a>授與使用認證的權限

若要使用認證，使用者必須具有特定認證的 `REFERENCES` 權限。 若要在 specific_user 的 storage_credential 上授與 `REFERENCES` 權限，請執行：

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

為確保 Azure AD 傳遞體驗能順利進行，根據預設，所有使用者都有權使用 `UserIdentity` 認證。

## <a name="server-scoped-credential"></a>伺服器範圍的認證

當 SQL 登入呼叫沒有 `DATA_SOURCE` 的 `OPENROWSET` 函式來讀取某些儲存體帳戶上的檔案時，就會用到伺服器範圍的認證。 伺服器範圍認證的名稱**必須**符合 Azure 儲存體的 URL。 您可以執行 [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)來新增認證。 您必須提供 CREDENTIAL NAME 引數。 其必須是儲存體中資料的部分路徑或完整路徑 (如下所示)。

> [!NOTE]
> 不支援 `FOR CRYPTOGRAPHIC PROVIDER` 引數。

伺服器層級的 CREDENTIAL 名稱必須符合儲存體帳戶 (和選擇性容器) 的完整路徑，其格式如下：`<prefix>://<storage_account_path>/<storage_path>`。 下表說明儲存體帳戶路徑：

| 外部資料來源       | 前置詞 | 儲存體帳戶路徑                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob 儲存體         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

伺服器範圍的認證可讓您使用下列驗證類型來存取 Azure 儲存體：

### <a name="user-identity"></a>[使用者身分識別](#tab/user-identity)

Azure AD 使用者若具有 `Storage Blob Data Owner`、`Storage Blob Data Contributor` 或 `Storage Blob Data Reader` 角色，即可存取 Azure 儲存體上的任何檔案。 Azure AD 使用者不需要認證即可存取儲存體。 

SQL 使用者無法使用 Azure AD 驗證來存取儲存體。

### <a name="shared-access-signature"></a>[共用存取簽章](#tab/shared-access-signature)

下列指令碼會建立伺服器層級的認證，讓 `OPENROWSET` 函式可以使用 SAS 權杖來存取 Azure 儲存體上的任何檔案。 建立此認證可讓 SQL 主體執行 `OPENROWSET` 函式，在符合認證名稱中 URL 的 Azure 儲存體上讀取以 SAS 金鑰保護的檔案。

請將 <*mystorageaccountname*> 替換為實際的儲存體帳戶名稱，以及將 <*mystorageaccountcontainername*> 替換為實際的容器名稱：

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[受控身分識別](#tab/managed-identity)

下列指令碼會建立伺服器層級的認證，讓 `OPENROWSET` 函式可以使用工作區受控識別來存取 Azure 儲存體上的任何檔案。

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[公用存取權](#tab/public-access)

存取公用檔案不需要資料庫範圍認證。 建立[不含資料庫範圍認證的資料來源](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source)，以存取 Azure 儲存體上的公用檔案。

---

## <a name="database-scoped-credential"></a>資料庫範圍認證

當任何主體呼叫具有 `DATA_SOURCE` 的 `OPENROWSET` 函式，或從未存取公用檔案的[外部資料表](develop-tables-external-tables.md)選取資料時，就會使用資料庫範圍的認證。 資料庫範圍認證不需要符合儲存體帳戶的名稱，因為其會在定義儲存體位置的 DATA SOURCE 中明確地使用。

資料庫範圍的認證可讓您使用下列驗證類型來存取 Azure 儲存體：

### <a name="azure-ad-identity"></a>[Azure AD 身分識別](#tab/user-identity)

Azure AD 使用者若至少具有 `Storage Blob Data Owner`、`Storage Blob Data Contributor` 或 `Storage Blob Data Reader` 角色，即可存取 Azure 儲存體上的任何檔案。 Azure AD 使用者不需要認證即可存取儲存體。

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

SQL 使用者無法使用 Azure AD 驗證來存取儲存體。

### <a name="shared-access-signature"></a>[共用存取簽章](#tab/shared-access-signature)

下列指令碼會建立認證，以使用認證中指定的 SAS 權杖來存取儲存體上的檔案。 此指令碼會建立使用此 SAS 權杖來存取儲存體的範例外部資料來源。

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[受控身分識別](#tab/managed-identity)

下列指令碼會建立資料庫範圍認證，以用來將目前的 Azure AD 使用者模擬為服務的受控識別。 此指令碼會建立使用工作區身分識別來存取儲存體的範例外部資料來源。

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

資料庫範圍認證不需要符合儲存體帳戶的名稱，因為其會在定義儲存體位置的 DATA SOURCE 中明確地使用。

### <a name="public-access"></a>[公用存取權](#tab/public-access)

存取公用檔案不需要資料庫範圍認證。 建立[不含資料庫範圍認證的資料來源](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source)，以存取 Azure 儲存體上的公用檔案。

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

資料庫範圍認證會用於外部資料源中，其用途是指定將用來存取此儲存體的驗證方法：

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>範例

### <a name="access-a-publicly-available-data-source"></a>**存取公用的資料來源**

使用下列指令碼建立可存取可公用資料來源的資料表。

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

資料庫使用者可以使用參考資料源的外部資料表或 [OPENROWSET](develop-openrowset.md) 函式，從資料來源讀取檔案內容：

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = [mysample],
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**使用認證來存取資料來源**

修改下列指令碼來建立外部資料表，使其可使用 SAS 權杖、使用者的 Azure AD 身分識別或工作區的受控識別來存取 Azure 儲存體。

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

資料庫使用者可以使用參考資料源的[外部資料表](develop-tables-external-tables.md)或 [OPENROWSET](develop-openrowset.md) 函式，從資料來源讀取檔案內容：

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>後續步驟

下列文章將協助您了解查詢不同資料夾類型、檔案類型，以及建立和使用檢視的方式：

- [查詢單一 CSV 檔案](query-single-csv-file.md)
- [查詢資料夾和多個 CSV 檔案](query-folders-multiple-csv-files.md)
- [查詢特定檔案](query-specific-files.md)
- [查詢 Parquet 檔案](query-parquet-files.md)
- [建立和使用檢視](create-use-views.md)
- [查詢 JSON 檔案](query-json-files.md)
- [查詢 Parquet 巢狀型別](query-parquet-nested-types.md)
