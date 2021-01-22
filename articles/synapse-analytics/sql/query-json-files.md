---
title: 使用無伺服器 SQL 集區查詢 JSON 檔案
description: 本節說明如何在 Azure Synapse Analytics 中使用無伺服器 SQL 集區讀取 JSON 檔案。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8dc07a3aa954a74ba594eb99da1ea3ee59610c9b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678316"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的無伺服器 SQL 集區來查詢 JSON 檔案

在本文中，您將瞭解如何使用 Azure Synapse Analytics 中的無伺服器 SQL 集區來撰寫查詢。 查詢的目標是要使用 [OPENROWSET](develop-openrowset.md)來讀取 JSON 檔案。 
- 將多個 JSON 檔儲存為 JSON 陣列的標準 JSON 檔案。
- 以行分隔的 JSON 檔案，其中 JSON 檔會以分行符號分隔。 這些檔案類型的一般延伸模組為 `jsonl` 、 `ldjson` 和 `ndjson` 。

## <a name="read-json-documents"></a>讀取 JSON 檔

若要查看 JSON 檔案的內容，最簡單的方式就是提供函數的檔案 URL `OPENROWSET` 、指定 csv `FORMAT` ，以及設定和的值 `0x0b` `fieldterminator` `fieldquote` 。 如果您需要讀取以行分隔的 JSON 檔案，則這就夠了。 如果您有傳統 JSON 檔案，則需要設定 `0x0b` 的值 `rowterminator` 。 `OPENROWSET` 函數會剖析 JSON，並以下列格式傳回每個檔：

| doc |
| --- |
|{"date_rep"： "2020-07-24"，"day"：24，"month"：7，"year"：2020，"案例"：3，"裝死"：0，"geo_id"： "AF"}|
|{"date_rep"： "2020-07-25"，"day"：25，"month"：7，"year"：2020，"案例"：7，"裝死"：0，"geo_id"： "AF"}|
|{"date_rep"： "2020-07-26"，"day"：26，"month"：7，"year"：2020，"案例"：4，"裝死"：0，"geo_id"： "AF"}|
|{"date_rep"： "2020-07-27"，"day"：27，"month"：7，"year"：2020，"案例"：8，"裝死"：0，"geo_id"： "AF"}|

如果檔案可公開取得，或您的 Azure AD 識別可以存取此檔案，您應該會看到檔案的內容，如下列範例所示的查詢所示。

### <a name="read-json-files"></a>讀取 JSON 檔案

下列範例查詢會讀取 JSON 和以行分隔的 JSON 檔案，並以個別的資料列形式傳回每份檔。

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

上述範例查詢中的 JSON 檔包含物件的陣列。 此查詢會傳回每個物件，做為結果集中的個別資料列。 請確定您可以存取此檔案。 如果您的檔案受到 SAS 金鑰或自訂身分識別的保護，您必須設定 [sql 登入的伺服器層級認證](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)。 

### <a name="data-source-usage"></a>資料來源使用方式

上一個範例使用檔案的完整路徑。 或者，您可以使用指向儲存體根資料夾的位置來建立外部資料源，然後使用該資料來源以及函式中檔案的相對路徑 `OPENROWSET` ：

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

如果使用 SAS 金鑰或自訂身分識別來保護資料來源，您可以 [使用資料庫範圍認證來設定資料來源](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)。

在下列各節中，您可以瞭解如何查詢各種類型的 JSON 檔案。

## <a name="parse-json-documents"></a>剖析 JSON 檔

上述範例中的查詢會將每個 JSON 檔以單一字串的形式傳回結果集的個別資料列中。 您可以使用函式 `JSON_VALUE` 和 `OPENJSON` 來剖析 JSON 檔中的值，並以關聯式值形式傳回它們，如下列範例所示：

| 日期 \_ 代表 | 案例 | 地理 \_ 識別碼 |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>範例 JSON 檔

查詢範例會讀取 *json* 檔案，其中包含具有下列結構的檔：

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> 如果這些檔儲存為以行分隔的 JSON，您必須將 `FIELDTERMINATOR` 和設定 `FIELDQUOTE` 為0x0b。 如果您有標準 JSON 格式，您需要將設定 `ROWTERMINATOR` 為0x0b。

### <a name="query-json-files-using-json_value"></a>使用 JSON_VALUE 查詢 JSON 檔案

下列查詢示範如何使用 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ，從 JSON 檔 (標題、發行者) 取得純量值：

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

### <a name="query-json-files-using-openjson"></a>使用 OPENJSON 查詢 JSON 檔案

下列查詢使用 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。 它會取出在塞爾維亞中回報的 COVID-19 統計資料：

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```

## <a name="next-steps"></a>後續步驟

本系列的下一篇文章將示範如何：

- [查詢資料夾和多個檔案](query-folders-multiple-csv-files.md)
- [建立和使用檢視](create-use-views.md)
