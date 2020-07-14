---
title: 在 Synapse SQL 中使用 SQL 隨選 (預覽) 存取儲存體上的檔案
description: 說明如何在 Synapse SQL 中使用 SQL 隨選 (預覽) 資源來查詢儲存體檔案。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: c251b70d1988be82821f1e133151dae1ac6d1bc9
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921299"
---
# <a name="accessing-external-storage-in-synapse-sql-on-demand"></a>存取 Synapse SQL 中的外部儲存體 (隨選)

本文件說明使用者如何在 Synapse SQL (隨選) 中讀取儲存在 Azure 儲存體中的檔案資料。 使用者可以使用下列選項來存取儲存體：

- [OPENROWSET](develop-openrowset.md) 函式，可對 Azure 儲存體中的檔案進行特定查詢。
- [外部資料表](develop-tables-external-tables.md)，這是建置於外部檔案集之上的預先定義資料結構。

使用者可以使用[不同的驗證方法](develop-storage-files-storage-access-control.md)，例如 Azure AD 傳遞驗證 (Azure AD 主體的預設值) 和 SAS 驗證 (SQL 主體的預設值)。

## <a name="openrowset"></a>OPENROWSET

[OPENROWSET](develop-openrowset.md) 函式可讓使用者從 Azure 儲存體讀取檔案。

### <a name="query-files-using-openrowset"></a>使用 OPENROWSET 查詢檔案

OPENROWSET 可讓使用者在 Azure 儲存體上查詢外部檔案 (如果他們具有儲存體的存取權)。 連線到 Synapse SQL 隨選端點的使用者應該使用下列查詢來讀取 Azure 儲存體上的檔案內容：

```sql
SELECT * FROM
 OPENROWSET(BULK 'http://storage...com/container/file/path/*.csv', format= 'parquet') as rows
```

使用者可以使用下列存取規則來存取儲存體：

- Azure AD 使用者 - OPENROWSET 會使用呼叫者的 Azure AD 身分識別存取 Azure 儲存體，或以匿名存取方式存取儲存體。
- SQL 使用者 – OPENROWSET 以匿名存取的方式存取儲存體。

SQL 主體也可以使用 OPENROWSET 來直接查詢以 SAS 權杖或工作區受控識別保護的檔案。 如果 SQL 使用者執行此函式，具有 ALTER ANY CREDENTIAL 權限的進階使用者就必須建立符合函式中 URL 的伺服器範圍認證 (使用儲存體名稱和容器)，並將此認證的 REFERENCES 授權授與 OPENROWSET 函式的呼叫者：

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [http://storage.dfs.com/container]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[http://storage.dfs.com/container] TO sqluser
```

如果沒有符合 URL 的伺服器層級認證，或 SQL 使用者沒有此認證的參考權限，則會傳回錯誤。 SQL 主體無法使用某些 Azure AD 身分識別進行模擬。

> [!NOTE]
> 此版 OPENROWSET 的設計訴求是使用預設驗證快速且輕鬆進行資料探索。 若要利用模擬或受控識別，請使用 OPENROWSET 搭配 DATASOURCE，如下一節所述。

### <a name="querying-data-sources-using-openrowset"></a>使用 OPENROWSET 查詢資料來源

OPENROWSET 可讓使用者查詢放在某些外部資料來源上的檔案：

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

具有 CONTROL DATABASE 權限的進階使用者必須建立用來存取儲存區的 DATABASE SCOPED CREDENTIAL，以及建立 EXTERNAL DATA SOURCE 以指定應使用的資料來源 URL 和認證：

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

DATABASE SCOPED CREDENTIAL 會指定如何存取參考資料來源 (目前為 SAS 和受控識別) 上的檔案。

呼叫者必須具有下列其中一個權限，才能執行 OPENROWSET 函式：

- 執行 OPENROWSET 的其中一個權限：
  - ADMINISTER BULK OPERATION 可讓登入執行 OPENROWSET 函式。
  - ADMINISTER DATABASE BULK OPERATION 可讓資料庫範圍的使用者執行 OPENROWSET 函式。
- EXTERNAL DATA SOURCE 中所參考認證的 REFERENCES DATABASE SCOPED CREDENTIAL

#### <a name="accessing-anonymous-data-sources"></a>存取匿名資料來源

使用者可以在沒有 CREDENTIAL 可參考公用存取儲存體或使用 Azure AD 傳遞驗證的情況下，建立 EXTERNAL DATA SOURCE：

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3') ;
```

## <a name="external-table"></a>EXTERNAL TABLE

具有讀取資料表權限的使用者可以使用 EXTERNAL TABLE 存取外部檔案，該資料表會建立在 Azure 儲存體資料夾和檔案集合的上方。

具有[建立外部資料表權限](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions)的使用者 (例如 CREATE TABLE 和 ALTER ANY CREDENTIAL 或 REFERENCES DATABASE SCOPED CREDENTIAL)，可以使用下列指令碼，在 Azure 儲存體資料來源上方建立資料表：

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

具有 CONTROL DATABASE 權限的使用者必須建立用來存取儲存區的 DATABASE SCOPED CREDENTIAL，以及建立 EXTERNAL DATA SOURCE 以指定應使用的資料來源 URL 和認證：

```sql
CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://samples.blob.core.windows.net/products' ,
 CREDENTIAL = cred
 ) ;
```

DATABASE SCOPED CREDENTIAL 會指定如何存取參考資料來源上的檔案。

### <a name="reading-external-files-with-external-table"></a>使用 EXTERNAL TABLE 讀取外部檔案

EXTERNAL TABLE 可讓您使用標準 SQL SELECT 陳述式，從透過資料來源參考的檔案中讀取資料：

```sql
SELECT *
FROM dbo.DimProductsExternal
```

呼叫者必須具有下列權限才能讀取資料：
- 外部資料表上的 `SELECT` 權限
- `REFERENCES DATABASE SCOPED CREDENTIAL` 權限 (如果 `DATA SOURCE` 具有 `CREDENTIAL`)

## <a name="permissions"></a>權限

下表列出以上所述作業的必要權限。

| 查詢 | 所需的權限|
| --- | --- |
| 沒有資料來源的 OPENROWSET(BULK) | `ADMINISTER BULK ADMIN`、`ADMINISTER DATABASE BULK ADMIN` 或 SQL 登入必須具有 REFERENCES CREDENTIAL::\<URL>，才能存取 SAS 保護的儲存體 |
| 有資料來源但沒有認證的 OPENROWSET(BULK) | `ADMINISTER BULK ADMIN` 或 `ADMINISTER DATABASE BULK ADMIN` |
| 有資料來源和認證的 OPENROWSET(BULK) | `ADMINISTER BULK ADMIN`、`ADMINISTER DATABASE BULK ADMIN` 或 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` 和 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY EXTERNAL FILE FORMAT` 和 `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` 和 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | 若要建立資料表 - `CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY DATA SOURCE` 和 `ALTER ANY EXTERNAL FILE FORMAT`。 若要讀取資料：查詢中每個資料表/檢視/函式的 `ADMIN BULK OPERATIONS` 或 `REFERENCES CREDENTIAL` 或 `SELECT TABLE` + 儲存體上的 R/W 權限 |

## <a name="next-steps"></a>後續步驟

您現在已準備好繼續進行下列操作說明文章：

- [查詢儲存體上的資料](query-data-storage.md)

- [查詢 CSV 檔案](query-single-csv-file.md)

- [查詢資料夾和多個檔案](query-folders-multiple-csv-files.md)

- [查詢特定檔案](query-specific-files.md)

- [查詢 Parquet 檔案](query-parquet-files.md)

- [查詢巢狀型別](query-parquet-nested-types.md)

- [查詢 JSON 檔案](query-json-files.md)

- [建立和使用檢視](create-use-views.md)
