---
title: 使用 SQL 隨選 (預覽) 查詢 JSON 檔案
description: 本節說明如何使用 Azure Synapse 分析中的隨選 SQL 來讀取 JSON 檔案。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 04b2d7842222426010b76a1a7ed4c72ee74e3d87
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489719"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用 SQL 隨選 (預覽) 的查詢 JSON 檔案

本文會說明如何在 Azure Synapse Analytics 中使用 SQL 隨選 (預覽) 來寫入查詢。 查詢的目標是要使用[OPENROWSET](develop-openrowset.md)來讀取 JSON 檔案。 
- 將多個 JSON 檔儲存為 JSON 陣列的標準 JSON 檔案。
- 以行分隔的 JSON 檔案，其中 JSON 檔會以新行字元分隔。 這些檔案類型的一般延伸模組為 `jsonl` 、 `ldjson` 和 `ndjson` 。

## <a name="read-json-documents"></a>讀取 JSON 檔

查看 JSON 檔案內容最簡單的方式，就是提供要函式的檔案 URL `OPENROWSET` 、指定 csv `FORMAT` ，以及設定 `0x0b` 和的值 `fieldterminator` `fieldquote` 。 如果您需要讀取以行分隔的 JSON 檔案，這就夠了。 如果您有傳統 JSON 檔案，則需要設定 `0x0b` 的值 `rowterminator` 。 `OPENROWSET`函式會剖析 JSON，並以下列格式傳回每份檔：

| doc |
| --- |
|{"date_rep"： "2020-07-24"，"day"：24，"month"：7，"year"：2020，"案例"：3，"裝死"：0，"geo_id"： "AF"}|
|{"date_rep"： "2020-07-25"，"day"：25，"month"：7，"year"：2020，"案例"：7，"裝死"：0，"geo_id"： "AF"}|
|{"date_rep"： "2020-07-26"，"day"：26，"month"：7，"year"：2020，"案例"：4，"裝死"：0，"geo_id"： "AF"}|
|{"date_rep"： "2020-07-27"，"day"：27，"month"：7，"year"：2020，"案例"：8，"裝死"：0，"geo_id"： "AF"}|

如果檔案可公開使用，或者您的 Azure AD 身分識別可以存取此檔案，則您應該能夠使用如下列範例所示的查詢來查看檔案的內容。

### <a name="read-json-files"></a>讀取 JSON 檔案

下列範例查詢會讀取 JSON 和以行分隔的 JSON 檔案，並將每個檔當做個別的資料列傳回。

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

此查詢會將每個 JSON 檔傳回為結果集的個別資料列。 請確定您可以存取此檔案。 如果您的檔案受到 SAS 金鑰或自訂身分識別的保護，您就必須設定[sql 登入的伺服器層級認證](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)。 

### <a name="data-source-usage"></a>資料來源使用方式

上一個範例使用檔案的完整路徑。 或者，您可以建立外部資料源，其位置會指向儲存體的根資料夾，並使用該資料來源和函式中檔案的相對路徑 `OPENROWSET` ：

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

如果資料來源受到 SAS 金鑰或自訂身分識別的保護，您可以[使用資料庫範圍認證來設定資料來源](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)。

在下列各節中，您可以瞭解如何查詢各種類型的 JSON 檔案。

## <a name="parse-json-documents"></a>剖析 JSON 檔

先前範例中的查詢會以單一字串的形式，將每個 JSON 檔傳回為結果集的個別資料列。 您可以使用函數 `JSON_VALUE` 和 `OPENJSON` 來剖析 JSON 檔中的值，並將它們當做關聯式值傳回，如下列範例所示：

| 日期 \_ 代表 | 案例 | 地理 \_ 識別碼 |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>範例 JSON 檔

查詢範例會讀取*json*檔案，其中包含具有下列結構的檔：

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
> 如果這些檔儲存為以行分隔的 JSON，您必須將和設定為 `FIELDTERMINATOR` `FIELDQUOTE` 0x0b。 如果您有標準 JSON 格式，您必須將設定 `ROWTERMINATOR` 為0x0b。

### <a name="query-json-files-using-json_value"></a>使用 JSON_VALUE 查詢 JSON 檔案

下列查詢會示範如何使用[JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)從 JSON 檔中取出純量值（title、publisher）：

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

下列查詢使用 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 它會抓取塞爾維亞中報告的 COVID 統計資料：

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
