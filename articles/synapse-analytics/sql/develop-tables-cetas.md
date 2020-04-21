---
title: Synapse SQL 中的 CETAS
description: 搭配 Synapse SQL 使用 CETAS
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 71bc20680467d270436e28190bb49db5b9313ca0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420042"
---
# <a name="cetas-with-synapse-sql"></a>CETAS 搭配 Synapse SQL

在 SQL 集區或 SQL 隨選 (預覽) 中，您可以使用 CREATE EXTERNAL TABLE AS SELECT (CETAS) 來完成下列工作：  

- 建立外部資料表
- 將 Transact-SQL SELECT 陳述式的結果以平行方式匯出至

  - Hadoop
  - Azure 儲存體 Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>SQL 集區中的 CETAS

如需 SQL 集區的 CETAS 用法和語法，請查看 [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 一文。 此外，如需使用 SQL 集區進行 CTAS 的指引，請參閱 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 一文。

## <a name="cetas-in-sql-on-demand"></a>SQL 隨選中的 CETAS

使用 SQL 隨選資源時，CETAS 會用來建立外部資料表，並將查詢結果匯出至 Azure 儲存體 Blob 或 Azure Data Lake Storage Gen2。

## <a name="syntax"></a>語法

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>引數

*[ [ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

要建立之資料表名稱的第一到第三部分。 針對外部資料表，SQL 隨選只會儲存資料表中繼資料。 不會在 SQL 隨選中移動或儲存任何實際資料。

LOCATION = *'path_to_folder'*

指定要將 SELECT 陳述式的結果寫入至外部資料來源上的哪個位置。 根資料夾是在外部資料來源中指定的資料位置。 LOCATION 必須指向資料夾，並以 / 結尾。 範例：aggregated_data/

DATA_SOURCE = *external_data_source_name*

指定包含外部資料儲存位置的外部資料來源名稱。 若要建立外部資料來源，請使用 [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source)。

FILE_FORMAT = *external_file_format_name*

指定包含外部資料檔案格式之外部檔案格式物件的名稱。 若要建立外部檔案格式，請使用 [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format)。 目前只支援格式 FORMAT='PARQUET' 的外部檔案格式。

WITH *<common_table_expression>*

指定稱為通用資料表運算式 (CTE) 的暫存具名結果集。 如需詳細資訊，請參閱 [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

SELECT <select_criteria>

將 SELECT 陳述式產生的結果填入新資料表。 *select_criteria* 是 SELECT 陳述式的主體，可決定要複製到新資料表的資料。 如需 SELECT 陳述式的相關資訊，請參閱 [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="permissions"></a>權限

您必須擁有列出資料夾內容和寫入 LOCATION 資料夾的權限，CETAS 才能正常執行。

## <a name="examples"></a>範例

這些範例會使用 CETAS，將依據年和州彙總的總人口數儲存至位於 population_ds 資料來源中的 aggregated_data 資料夾。

此範例會依賴先前建立的認證、資料來源和外部檔案格式。 請參閱[外部資料表](develop-tables-external-tables.md)文件。 若要將查詢結果儲存到相同資料來源中的不同資料夾，請變更 LOCATION 引數。 若要將結果儲存到不同的儲存體帳戶，請為 DATA_SOURCE 引數建立並使用不同的資料來源。

> [!NOTE]
> 接下來的範例會使用公用 Azure 開放資料儲存體帳戶。 此範例僅供讀取。 若要執行這些查詢，您需要提供您具有寫入權限的資料來源。

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

下列範例使用外部資料表作為 CETAS 的來源。 此範例會依賴先前建立的認證、資料來源、外部檔案格式，以及外部資料表。 請參閱[外部資料表](develop-tables-external-tables.md)文件。

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>支援的資料類型

CETAS 可以用來儲存屬於下列 SQL 資料類型的結果集：

- BINARY
- varbinary
- char
- varchar
- date
- time
- datetime2
- decimal
- NUMERIC
- FLOAT
- real
- BIGINT
- int
- SMALLINT
- TINYINT
- bit

LOB 無法與 CETAS 搭配使用。

下列資料類型不能用在 CETAS 的 SELECT 部分中：

- NCHAR
- NVARCHAR
- Datetime
- smalldatetime
- datetimeoffset
- money
- SMALLMONEY
- UNIQUEIDENTIFIER

## <a name="next-steps"></a>後續步驟

您可以嘗試查詢 [Spark 資料表](develop-storage-files-spark-tables.md)。
