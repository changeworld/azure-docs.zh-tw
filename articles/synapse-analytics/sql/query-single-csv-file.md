---
title: 使用 SQL 隨選 (預覽) 查詢 CSV 檔案
description: 在本文中，您將了解如何使用 SQL 隨選 (預覽) 來查詢具有不同檔案格式的單一 CSV 檔案。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: d2f8a30503e14e647cbc9151ebcea7efa000ca07
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288285"
---
# <a name="query-csv-files"></a>查詢 CSV 檔案

在本文中，您將了解如何在 Azure Synapse Analytics 使用 SQL 隨選 (預覽) 來查詢單一 CSV 檔案。 CSV 檔案可能會有不同的格式： 

- 包含和不包含標頭資料列
- 逗號和定位字元分隔值
- Windows 和 UNIX 樣式行尾結束符號
- 未加上引號和加上引號的值，以及逸出字元

以下將涵蓋上述所有變化。

## <a name="quickstart-example"></a>快速入門範例

`OPENROWSET` 函數可讓您藉由提供檔案的 URL 來讀取 CSV 檔案的內容。

### <a name="read-a-csv-file"></a>讀取 csv 檔案

若要查看檔案的內容，最簡單的方式 `CSV` 就是提供檔案 URL 來 `OPENROWSET` 運作、指定 csv `FORMAT` 和 2.0 `PARSER_VERSION` 。 如果檔案可公開取得，或您的 Azure AD 識別可以存取此檔案，您應該能夠使用如下列範例所示的查詢來查看檔案的內容：

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

選項 `firstrow` 可用來略過 CSV 檔案中代表標頭的第一個資料列（在此案例中）。 請確定您可以存取此檔案。 如果您的檔案受到 SAS 金鑰或自訂身分識別的保護，您將需要設定 [sql 登入的伺服器層級認證](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)。

### <a name="data-source-usage"></a>資料來源使用量

上一個範例使用檔案的完整路徑。 或者，您可以使用指向儲存體根資料夾的位置來建立外部資料源：

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

建立資料來源之後，您可以使用該資料來源以及函式中檔案的相對路徑 `OPENROWSET` ：

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

如果使用 SAS 金鑰或自訂身分識別來保護資料來源，您可以 [使用資料庫範圍認證來設定資料來源](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)。

### <a name="explicitly-specify-schema"></a>明確指定架構

`OPENROWSET` 可讓您明確地指定要使用子句從檔案讀取的資料行 `WITH` ：

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

在子句中的資料類型後面的數位 `WITH` 代表 CSV 檔案中的資料行索引。

在下列各節中，您可以瞭解如何查詢各種類型的 CSV 檔案。

## <a name="prerequisites"></a>Prerequisites

第一個步驟是**建立將在其中建立資料表的資料庫**。 然後藉由在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)來初始化物件。 此安裝指令碼會建立資料來源、資料庫範圍認證，以及用於這些範例中的外部檔案格式。

## <a name="windows-style-new-line"></a>Windows 樣式新行

下列查詢顯示如何讀取一個 CSV 檔案，其不包含標頭資料列、採用 Windows 樣式新行，且以逗號分隔資料行。

檔案預覽：

![CSV 檔案中的前 10 個資料列沒有標頭，採用 Windows 樣式新行。](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>UNIX 樣式新行

下列查詢顯示如何讀取一個檔案，其不包含標頭資料列、採用 UNIX 樣式新行，且以逗號分隔資料行。 請注意，與其他範例相比，檔案的位置不同。

檔案預覽：

![不包含標頭資料列且採用 UNIX 樣式新行的 CSV 檔案中，其前 10 個資料列。](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>標頭資料列

下列查詢顯示如何讀取一個檔案，其包含標頭資料列、採用 UNIX 樣式新行，且以逗號分隔資料行。 請注意，與其他範例相比，檔案的位置不同。

檔案預覽：

![包含標頭資料列且採用 UNIX 樣式新行的 CSV 檔案中，其前 10 個資料列。](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>自訂引號字元

下列查詢顯示如何讀取一個檔案，其包含標頭資料列、採用 UNIX 樣式新行、以逗號分隔資料行，以及其具有加上引號的值。 請注意，與其他範例相比，檔案的位置不同。

檔案預覽：

![包含標頭資料列及採用 UNIX 樣式新行且具有加上引號值的 CSV 檔案中，其前 10 個資料列。](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> 如果省略 FIELDQUOTE 參數，則此查詢會傳回相同的結果，因為 FIELDQUOTE 的預設值是雙引號。

## <a name="escape-characters"></a>逸出字元

下列查詢顯示如何讀取一個檔案，其包含標頭資料列、採用 UNIX 樣式新行、以逗號分隔資料行，且針對值內的欄位分隔符號 (逗號) 使用逸出字元。 請注意，與其他範例相比，檔案的位置不同。

檔案預覽：

![包含標頭資料列及採用 UNIX 樣式新行且針對欄位分隔符號使用逸出字元的 CSV 檔案中，其前 10 個資料列。](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> 如果未指定 ESCAPECHAR，此查詢將會失敗，因為在 "Slov,enia" 中的逗號會視為欄位分隔符號，而不是國家/地區名稱的一部分。 "Slov,enia" 會視為兩個資料行。 因此，特定資料列會比其他資料列多出一個的資料行，且比您在 WITH 子句中的定義多一個資料行。

### <a name="escape-quoting-characters"></a>Escape 引號字元

下列查詢會示範如何讀取含有標頭資料列的檔案，其中包含 Unix 樣式的新行、逗點分隔的資料行，以及值內的有引號的雙引號字元。 請注意，與其他範例相比，檔案的位置不同。

檔案預覽：

![下列查詢會示範如何讀取含有標頭資料列的檔案，其中包含 Unix 樣式的新行、逗點分隔的資料行，以及值內的有引號的雙引號字元。](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> 引號字元必須以另一個引號字元來逸出。 只有在以引號字元封住值時，引號字元才能在資料行值中出現。

## <a name="tab-delimited-files"></a>Tab 字元分隔檔案

下列查詢顯示如何讀取一個檔案，其包含標頭資料列、採用 UNIX 樣式新行，且以 Tab 字元分隔資料行。 請注意，與其他範例相比，檔案的位置不同。

檔案預覽：

![包含標頭資料列且採用 UNIX 樣式新行和 Tab 分隔符號的 CSV 檔案中，其前 10 個資料列。](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>傳回資料行的子集

到目前為止，您已使用 WITH 來指定 CSV 檔案結構描述，並列出所有資料行。 在查詢中，您只能針對每個必要資料行使用序數來指定實際需要的資料行。 您也會省略不感興趣的資料行。

下列查詢會傳回檔案中不同國家/地區名稱的數目，僅指定所需的資料行：

> [!NOTE]
> 請查看下方查詢中的 WITH 子句，並請注意，在您定義 *[country_name]* 資料行的資料列結尾有 "2" (不含引號)。 這表示 *[country_name]* 資料行是檔案中的第二個資料行。 查詢會忽略檔案中的所有資料行，但第二個除外。

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>後續步驟

接下來的文章將會示範如何：

- [查詢 Parquet 檔案](query-parquet-files.md)
- [查詢資料夾和多個檔案](query-folders-multiple-csv-files.md)
