---
title: 如何使用 SQL 隨選 (預覽) 中的 OPENROWSET
description: 本文將說明 SQL 隨選 (預覽) 中的 OPENROWSET 語法，並說明如何使用引數。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4ec6e18aa4fa741ba784e68ccf9b5f87ad654eba
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591415"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>如何搭配 SQL 隨選使用 (預覽) OPENROWSET

`OPENROWSET(BULK...)` 函式可讓您存取 Azure 儲存體中的檔案。 `OPENROWSET` 函式會讀取遠端資料來源 (例如檔案) 的內容，並以一組資料列傳回內容。 在 SQL 隨選 (預覽) 資源中，OPENROWSET BULK 資料列集提供者可透過呼叫 OPENROWSET 函數及指定 BULK 選項加以存取。  

您可以依照參考資料表名稱 `OPENROWSET` 的相同方式，在查詢的 `FROM` 子句中參考 `OPENROWSET` 函式。 其也支援透過內建 BULK 提供者執行大量作業，可讓檔案資料被讀取，並且當做資料列集傳回。

## <a name="data-source"></a>資料來源

Synapse SQL 中的 OPENROWSET 函式會從資料來源讀取檔案的內容。 資料來源是 Azure 儲存體帳戶，其可以在 `OPENROWSET` 函式中明確參考，也可以從您想要讀取的檔案 URL 進行動態推斷。
`OPENROWSET` 函式可以選擇性地包含 `DATA_SOURCE` 參數，以指定包含檔案的資料來源。
- 沒有 `DATA_SOURCE` 的 `OPENROWSET` 可以用來直接從指定為 `BULK` 選項的 URL 位置讀取檔案內容：

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://storage..../container/folder/*.parquet',
                    TYPE = 'PARQUET') AS file
    ```

在沒有預先設定的情況下，這是可快速且簡便讀取檔案內容的方式。 此選項可讓您使用基本驗證選項來存取儲存體 (針對 Azure AD 登入使用 Azure AD 傳遞，針對 SQL 登入使用 SAS 權杖)。 

- 具有 `DATA_SOURCE` 的 `OPENROWSET` 可用來存取指定儲存體帳戶上的檔案：

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    TYPE = 'PARQUET') AS file
    ```

    此選項可讓您在資料來源中設定儲存體帳戶的位置，並指定應該用來存取儲存體的驗證方法。 
    
    > [!IMPORTANT]
    > 沒有 `DATA_SOURCE`的 `OPENROWSET` 會提供快速且輕鬆的方式來存取儲存體檔案，但其提供的驗證選項有限。 例如，Azure AD 主體只能使用其 [Azure AD 身分識別](develop-storage-files-storage-access-control.md?tabs=user-identity#force-azure-ad-pass-through)來存取檔案，而且無法存取公用檔案。 如果您需要更強大的驗證選項，請使用 `DATA_SOURCE` 選項，並定義您要用來存取儲存體的認證。

## <a name="security"></a>安全性

資料庫使用者必須擁有 `ADMINISTER BULK OPERATIONS` 權限，才能使用 `OPENROWSET` 函式。

儲存體管理員也必須藉由提供有效的 SAS 權杖或啟用 Azure AD 主體來存取儲存體檔案，才能讓使用者存取檔案。 若要深入了解儲存體的存取控制，請參閱[本文](develop-storage-files-storage-access-control.md)。

`OPENROWSET` 會使用下列規則來決定如何向儲存體進行驗證：
- 在具有 `DATA_SOURCE` 的 `OPENROWSET` 中，驗證機制會視呼叫者類型而定。
  - 如果 Azure 儲存體允許 Azure AD 使用者存取基礎檔案 (例如，如果呼叫者具有儲存體的儲存體讀者權限)，以及如果您在Synapse SQL 服務上[啟用 Azure AD 傳遞驗證](develop-storage-files-storage-access-control.md#force-azure-ad-pass-through)，則 AAD 登入只能使用自己的 [Azure AD 身分識別](develop-storage-files-storage-access-control.md?tabs=user-identity#force-azure-ad-pass-through)來存取檔案。
  - SQL 登入也可以使用沒有 `DATA_SOURCE` 的 `OPENROWSET` 來存取公用檔案、使用 SAS 權杖保護的檔案或 Synapse 工作區的受控識別。 您需要[建立伺服器範圍的認證](develop-storage-files-storage-access-control.md#examples)，以允許存取儲存體檔案。 
- 在具有 `DATA_SOURCE` 的 `OPENROWSET` 中，驗證機制會在指派給參考資料源的資料庫範圍認證中定義。 此選項可讓您存取公用儲存體，或使用 SAS 權杖、工作區的受控識別或[呼叫者的 Azure AD 身分識別](develop-storage-files-storage-access-control.md?tabs=user-identity#) (如果呼叫者是 Azure AD 主體) 來存取儲存體。 如果 `DATA_SOURCE` 參照非公用的 Azure 儲存體，您就必須[建立資料庫範圍的認證](develop-storage-files-storage-access-control.md#examples)，並在 `DATA SOURCE` 中加以參考，才能允許存取儲存體檔案。

呼叫者必須具有認證的 `REFERENCES` 權限，才能使用該認證向儲存體進行驗證。

## <a name="syntax"></a>語法

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>引數

您有兩個輸入檔選擇，其中皆包含要查詢的目標資料。 有效值為：

- 'CSV' - 包含具有資料列/資料行分隔符號的任何分隔文字檔。 任何字元都可以作為欄位分隔符號使用，例如 TSV：FIELDTERMINATOR = tab。

- 'PARQUET' - Parquet 格式的二進位檔案 

**'unstructured_data_path'**

建立資料路徑的 unstructured_data_path 可能是絕對或相對路徑：
- 格式為 '\<prefix>://\<storage_account_path>/\<storage_path>' 的絕對路徑可讓使用者直接讀取檔案。
- 格式為 '<storage_path>' 的相對路徑必須與 `DATA_SOURCE` 參數搭配使用，並在 `EXTERNAL DATA SOURCE` 中定義的 <storage_account_path> 位置內描述檔案模式。 

 您可以在下面找到將連結至特定外部資料源的相關 <storage account path> 值。 

| 外部資料來源       | 前置詞 | 儲存體帳戶路徑                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob 儲存體         | https  | \<storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>.dfs.core.windows.net              |
||||

'\<storage_path>'

 指定儲存體內的路徑，其指向您想要讀取的資料夾或檔案。 如果路徑指向容器或資料夾，則會從該特定容器或資料夾讀取所有檔案。 子資料夾中的檔案不會包含在內。 

 您可以使用萬用字元將目標設為多個檔案或資料夾。 允許使用多個不連續的萬用字元。
以下範例表示會從所有開頭為 */csv/population* 的資料夾中，讀取所有開頭為 *population*的 *csv* 檔案：  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

如果您將 unstructured_data_path 指定為資料夾，SQL 隨選查詢將會從該資料夾擷取檔案。 

> [!NOTE]
> 與 Hadoop 和 PolyBase 不同的是，SQL 隨選不會傳回子資料夾。 此外，與 Hadoop 和 PloyBase 不同的是，SQL 隨選也不會傳回檔案名稱開頭為底線 (_) 或英文句號 (.) 的檔案。

在下列範例中，如果 unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`，SQL 隨選查詢將會傳回 mydata.txt 和 _hidden.txt 中的資料列。 其不會傳回 mydata2.txt 和 mydata3.txt，因為其位於子資料夾中。

![外部資料表的遞迴資料](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

WITH 子句可讓您指定要從檔案讀取的資料行。

- 針對 CSV 資料檔案，應提供資料行名稱及其資料類型，才能讀取所有資料行。 如果您想要讀取一小組資料行，請使用序數從原始資料檔案中依序挑選資料行。 資料行將會以加上順序的指派來繫結。 

    > [!IMPORTANT]
    > 對於 CSV 檔案而言，WITH 子句是必要的。
    >
    
- 針對 Parquet 資料檔案，請提供與原始資料檔案中資料行名稱相符的資料行名稱。 資料行將會依名稱來繫結。 如果省略 WITH 子句，則會傳回 Parquet 檔案中的所有資料行。

column_name = 輸出資料行的名稱。 如果有提供，此名稱會覆寫來源檔案中的資料行名稱。

column_type = 輸出資料行的資料類型。 隱含的資料類型轉換將會在此進行。

column_ordinal = 來源檔案中資料行的序數。 Parquet 檔案會忽略這個引數，因為繫結是根據名稱來進行。 下列範例只會從 CSV 檔案傳回第二個資料行：

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

指定要使用的欄位結束字元。 預設的欄位結束字元為逗號 (" **,** ")。

ROWTERMINATOR ='row_terminator'`

指定要使用的資料列結束字元。 如果未指定資料列結束字元，則會使用其中一個預設的結束字元。 PARSER_VERSION = '1.0' 的預設結束字元是 \r\n、\n 和 \r。 PARSER_VERSION = '2.0' 的預設結束字元是 \r\n 和 \n。

ESCAPE_CHAR = 'char'

指定檔案中用來將本身和所有分隔符號值逸出的字元。 如果逸出字元後面接著本身或任何分隔符號值以外的值，讀取值時就會捨棄逸出字元。 

無論 FIELDQUOTE 已啟用或未啟用，都會套用 ESCAPE_CHAR 參數。 其不會用來逸出引號字元。 為配合 Excel CSV 的行為，引號字元會以雙引號來逸出。

FIRSTROW = 'first_row' 

指定要載入之第一個資料列的號碼。 預設值是 1。 這表示指定之資料檔中的第一個資料列。 資料列號碼是由計算資料列結束字元所決定。 FIRSTROW 是以 1 為基底。

FIELDQUOTE = 'field_quote' 

指定將用來當作 CSV 檔案中引號字元的字元。 如果未指定，則會使用引號字元 (")。 

DATA_COMPRESSION = 'data_compression_method'

指定壓縮方法。 以下是支援的壓縮方法：

- org.apache.hadoop.io.compress.GzipCodec

PARSER_VERSION = 'parser_version'

指定讀取檔案時要使用的剖析器版本。 目前支援的 CSV 剖析器版本為 1.0 和 2.0

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

CSV 剖析器 1.0 版是預設項目且功能豐富，而 2.0 是為提高效能而建立的，並不支援所有選項和編碼。 

CSV 剖析器 2.0 版的詳細資訊：

- 未支援所有資料類型。
- 資料列大小的上限為 8 MB。
- 未支援下列選項：DATA_COMPRESSION。
- 以引號括住的空字串 ("") 會被視為空字串。

## <a name="examples"></a>範例

下列範例只會從 population*.csv 檔案 中傳回序號為 1 和 4 的兩個資料行。 由於檔案中沒有標頭資料列，因此會從第一行開始讀取：

```sql
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

下列範例會在不指定資料行名稱和資料類型的情況下，從 Parquet 格式的人口普查資料集中，傳回第一個資料列的所有資料行： 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

如果您收到無法列出檔案的錯誤訊息，您需要在 Synapse SQL 隨選中啟用公用儲存體的存取權：
- 如果您使用 SQL 登入，您需要[建立伺服器範圍的認證，以允許存取公用儲存體](develop-storage-files-storage-access-control.md#examples)。
- 如果您使用 Azure AD 主體來存取公用儲存體，則必須[建立伺服器範圍的認證，以允許存取公用儲存體](develop-storage-files-storage-access-control.md#examples)，並停用 [Azure AD 傳遞驗證](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)。

## <a name="next-steps"></a>後續步驟

如需更多範例，請參閱[查詢資料儲存體快速入門](query-data-storage.md)，以了解如何使用 `OPENROWSET 來讀取 [CSV](query-single-csv-file.md)、[PARQUET](query-parquet-files.md) 和 [JSON](query-json-files.md) 檔案格式。 您也可以了解如何使用 [CETAS](develop-tables-cetas.md)，將查詢結果儲存到 Azure 儲存體。
