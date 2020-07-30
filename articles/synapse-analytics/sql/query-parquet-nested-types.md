---
title: 使用 SQL 隨選 (預覽) 查詢 Parquet 巢狀型別
description: 本文會說明如何查詢 Parquet 巢狀型別。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386600"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的 SQL 隨選（預覽）在 Parquet 和 JSON 檔案中查詢巢狀型別

本文會說明如何在 Azure Synapse Analytics 中使用 SQL 隨選 (預覽) 來寫入查詢。 此查詢會讀取 Parquet 巢狀型別。
巢狀型別是代表物件或陣列的複雜結構。 巢狀型別可以儲存在： 
- [PARQUET](query-parquet-files.md) ，您可以在其中擁有多個包含陣列和物件的複雜資料行。
- 階層式 JSON 檔案，您可以在其中將複雜的 JSON[檔](query-json-files.md)讀取為單一資料行。
- CosmosDB 集合，其中每個檔都可以包含複雜的嵌套屬性（目前位於閘道公開預覽下）。

Synapse SQL 隨選將所有巢狀型別格式化為 JSON 物件和陣列，因此您可以[使用 json 函式來解壓縮或修改複雜物件](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server)，或[使用 OPENJSON 函式剖析 JSON 資料](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server)。 

以下顯示使用嵌套物件從[COVID-19 Open Research 資料集](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)JSON 檔案中，將純量和物件值解壓縮的查詢的其中一個範例。 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`函數會從指定路徑的欄位傳回純量值。 `JSON_QUERY`函式會從指定路徑的欄位傳回格式為 JSON 的物件。

> [!IMPORTANT]
> 這個範例會使用[COVID-19 開放式研究資料集](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)的檔案。 請參閱此頁面上的此授權和資料結構。

## <a name="prerequisites"></a>Prerequisites

第一步是使用資料庫參考的資料來源**建立資料庫**。 然後在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)，將物件初始化。 此安裝指令碼會建立資料來源、資料庫範圍認證，以及用於這些範例中的外部檔案格式。

## <a name="project-nested-or-repeated-data"></a>投射巢狀或重復資料

PARQUET 檔案可以有多個具有複雜類型的資料行。 這些資料行中的值會格式化為 JSON 文字，並傳回為 VARCHAR 資料行。 下列查詢會讀取*structExample. parquet*檔案，並顯示如何讀取嵌套資料行的值： 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

此查詢會傳回下列結果，其中每個嵌套物件的內容都會以 JSON 文字形式傳回：

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date"： "2009-04-25"}| {"Time"： "20：51： 54.3598000"}|    {"Timestamp"： "5501-04-08 12：13： 57.4821000"}|    {"Decimal"： 11143412.25350}| {"Float"： 0.5}|
|{"Date"： "1916-04-29"}| {"Time"： "00：16： 04.6778000"}|    {"Timestamp"： "1990-06-30 20：50： 52.6828000"}|    {"Decimal"： 1963545.62800}|  {"Float"：-2.125}|

下列查詢會讀取 *justSimpleArray.parquet* 檔案。 其會投影 Parquet 檔案中的所有資料行，包括巢狀或重複的資料。

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

此查詢會傳回下列結果：

|SimpleArray|
| --- |
|[11，12，13]|
|[21，22，23]|

## <a name="read-properties-from-nested-object-columns"></a>讀取來自嵌套物件資料行的屬性

`JSON_VALUE`函數可讓您從格式化為 JSON 文字的資料行傳回值：

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

結果如下表所示：

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| 補充資訊的 epidemiolo .。。 | Julien   | -圖 S1： Phylogeny .。。 | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

不同于大部分情況下的 JSON 檔案會傳回包含複雜 JSON 物件的單一資料行。 PARQUET 檔案可能會有多個複雜的。 您可以使用每個資料行上的函數來讀取 nested 資料行的屬性 `JSON_VALUE` 。 `OPENROWSET`可讓您直接指定在子句中嵌套屬性的路徑 `WITH` 。 路徑可以設定為數據行的名稱，或者您可以在資料行類型之後加入[JSON 路徑運算式](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server)。

下列查詢會讀取*structExample. parquet*檔案，並顯示如何呈現嵌套資料行的元素。 您有兩種方式可以參考嵌套的值：
- 在類型規格之後指定嵌套值路徑運算式。
- 使用 do "." 將資料行名稱格式化為嵌套路徑，以參考欄位。

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>從重複的資料行存取元素

下列查詢會讀取 *justSimpleArray.parquet* 檔案，並使用 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 從重複的資料行 (例如陣列或對應) 內擷取**純量** 元素：

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

結果如下表所示：

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11，12，13] | 11   | 12 | 13 |
| [21，22，23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>從複雜的資料行存取子物件

下列查詢會讀取 *mapExample. parquet* 檔案，並使用 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)，從重複的資料行 (例如 Array 或 Map) 擷取**非純量**元素：

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

您也可以明確地參考您想要在子句中傳回的資料行 `WITH` ：

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

結構會當做資料 `MapOfPersons` `VARCHAR` 行傳回，並格式化為 JSON 字串。

## <a name="projecting-values-from-repeated-columns"></a>投射重復資料行的值

如果您在某些資料行中有純量值的陣列（例如 `[1,2,3]` ），您可以使用下列腳本輕鬆地展開它們，並將其與主要資料列聯結：

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>後續步驟

下一篇文章將說明如何[查詢 JSON 檔案](query-json-files.md)。
