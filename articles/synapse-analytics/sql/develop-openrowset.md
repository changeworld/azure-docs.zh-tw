---
title: 如何使用 SQL 隨選 (預覽) 中的 OPENROWSET
description: 本文將說明 SQL 隨選 (預覽) 中的 OPENROWSET 語法，並說明如何使用引數。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680499"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>如何搭配 SQL 隨選使用 (預覽) OPENROWSET

OPENROWSET(BULK...) 函式可讓您存取 Azure 儲存體中的檔案。 在 SQL 隨選 (預覽) 資源中，OPENROWSET BULK 資料列集提供者可透過呼叫 OPENROWSET 函數及指定 BULK 選項加以存取。  

您可以依照參考資料表名稱 OPENROWSET 的相同方式，在查詢的 FROM 子句中參考 OPENROWSET 函式。 其也支援透過內建 BULK 提供者執行大量作業，可讓檔案資料被讀取，並且當做資料列集傳回。

SQL 集區目前不支援 OPENROWSET。

## <a name="syntax"></a>語法

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>引數

您有兩個輸入檔選擇，其中皆包含要查詢的目標資料。 有效值為：

- 'CSV' - 包含具有資料列/資料行分隔符號的任何分隔文字檔。 任何字元都可以作為欄位分隔符號使用，例如 TSV：FIELDTERMINATOR = tab。

- 'PARQUET' - Parquet 格式的二進位檔案 

**'unstructured_data_path'**

建立資料路徑的 unstructured_data_path 結構如下：  
'\<prefix>://\<storage_account_path>/\<storage_path>'
 
 
 您可以在下面找到將連結至特定外部資料源的相關儲存體帳戶路徑。 

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

指定要使用的資料列結束字元。 預設的資料列結束字元是新行字元，例如 \r\n。

ESCAPE_CHAR = 'char'

指定檔案中用來將本身和所有分隔符號值逸出的字元。 如果逸出字元後面接著本身或任何分隔符號值以外的值，讀取值時就會捨棄逸出字元。 

無論 FIELDQUOTE 已啟用或未啟用，都會套用 ESCAPE_CHAR 參數。 其不會用來逸出引號字元。 為配合 Excel CSV 的行為，引號字元會以雙引號來逸出。

FIRSTROW = 'first_row' 

指定要載入之第一個資料列的號碼。 預設值是 1。 這表示指定之資料檔中的第一個資料列。 資料列號碼是由計算資料列結束字元所決定。 FIRSTROW 是以 1 為基底。

FIELDQUOTE = 'field_quote' 

指定將用來當作 CSV 檔案中引號字元的字元。 如果未指定，則會使用引號字元 (")。 

## <a name="examples"></a>範例

下列範例只會從 population*.csv 檔案 中傳回序號為 1 和 4 的兩個資料行。 由於檔案中沒有標頭資料列，因此會從第一行開始讀取：

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

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
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>後續步驟

如需更多範例，請移至[快速入門](query-data-storage.md)，或使用 [CETAS](develop-tables-cetas.md) 將查詢的結果儲存至 Azure 儲存體。
