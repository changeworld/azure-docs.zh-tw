---
title: 使用 SQL 隨選 (預覽) 查詢 Parquet 巢狀型別
description: 在本文中，您將瞭解如何使用 SQL 隨選 (預覽) 來查詢 Parquet 的巢狀型別。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 08502704515c791bf63f4803b7446a0471c0a869
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288251"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用 SQL 隨選 (預覽) 來查詢 Parquet 和 JSON 檔案中的巢狀型別

在本文中，您將瞭解如何使用 Azure Synapse Analytics 中的 SQL 隨選 (預覽版) 來撰寫查詢。 查詢將會讀取 Parquet 的巢狀型別。
巢狀型別是代表物件或陣列的複雜結構。 巢狀型別可以儲存在： 
- [Parquet](query-parquet-files.md)，您可以在其中包含包含陣列和物件的多個複雜資料行。
- 階層式 Json 檔案，您可以在其中將複雜 JSON [檔](query-json-files.md)讀取為單一資料行。
- Azure Cosmos DB 的 (集合目前位於閘道公開預覽) 下，每份檔都可以包含複雜的嵌套屬性。

Azure Synapse SQL 隨選將所有巢狀型別格式化為 JSON 物件和陣列。 因此，您可以使用 JSON 函式來 [解壓縮或修改複雜物件](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) ，或 [使用 OPENJSON 函數來剖析 JSON 資料](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server)。 

以下是從 [covid-19-19 Open Research 資料集](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) JSON 檔案（其中包含嵌套物件）解壓縮純量和物件值的查詢範例： 

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

`JSON_VALUE`函數會從指定路徑的欄位傳回純量值。 `JSON_QUERY`函數會從指定路徑的欄位傳回格式化為 JSON 的物件。

> [!IMPORTANT]
> 此範例會使用 COVID-19-19 Open Research 資料集的檔案。 [請參閱此處的授權和資料結構](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)。

## <a name="prerequisites"></a>必要條件

第一個步驟是建立將建立資料來源的資料庫。 然後，您將在資料庫上執行 [安裝腳本](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) 來初始化物件。 安裝腳本會建立範例中所使用的資料來源、資料庫範圍的認證，以及外部檔案格式。

## <a name="project-nested-or-repeated-data"></a>投射巢狀或重復資料

Parquet 檔案可以有多個具有複雜類型的資料行。 這些資料行中的值會格式化為 JSON 文字，並傳回為 VARCHAR 資料行。 下列查詢會讀取 structExample. parquet 檔案，並顯示如何讀取嵌套資料行的值： 

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

此查詢會傳回下列結果。 每個嵌套物件的內容會以 JSON 文字傳回。

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date"： "2009-04-25"}| {"Time"： "20：51： 54.3598000"}|    {"Timestamp"： "5501-04-08 12：13： 57.4821000"}|    {"Decimal"： 11143412.25350}| {"Float"： 0.5}|
|{"Date"： "1916-04-29"}| {"Time"： "00：16： 04.6778000"}|    {"Timestamp"： "1990-06-30 20：50： 52.6828000"}|    {"Decimal"： 1963545.62800}|  {"Float"：-2.125}|

下列查詢會讀取 justSimpleArray.parquet 檔案。 它會從 Parquet 檔案中投影所有資料行，包括嵌套和重複的資料。

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

## <a name="read-properties-from-nested-object-columns"></a>從嵌套物件資料行讀取屬性

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
| Epidemiolo 的補充資訊 .。。 | 朱利安   | -圖 S1： Phylogeny 的 .。。 | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

不同于 JSON 檔案，在大部分情況下會傳回包含複雜 JSON 物件的單一資料行，Parquet 檔案可以有多個複雜的資料行。 您可以使用每個資料行上的函數來讀取嵌套資料行的屬性 `JSON_VALUE` 。 `OPENROWSET` 可讓您直接在子句中指定嵌套屬性的路徑 `WITH` 。 您可以將路徑設定為數據行的名稱，也可以在資料行類型之後加入 [JSON 路徑運算式](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) 。

下列查詢會讀取 structExample. parquet 檔案，並顯示如何呈現嵌套資料行的元素。 有兩種方式可參考嵌套值：
- 藉由在類型規格之後指定嵌套值路徑運算式。
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

下列查詢會讀取 justSimpleArray parquet 檔案，並使用 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 從重複的資料行內取出純量元素，例如陣列或對應：

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

結果如下︰

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11，12，13] | 11   | 12 | 13 |
| [21，22，23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>從複雜資料行存取子物件

下列查詢會讀取 mapExample. parquet 檔案，並使用 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 從重複的資料行（例如陣列或地圖）中取出非純量元素：

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

您也可以明確地參考要在子句中傳回的資料行 `WITH` ：

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

此結構 `MapOfPersons` 會以 VARCHAR 資料行傳回並格式化為 JSON 字串。

## <a name="project-values-from-repeated-columns"></a>重復資料行中的專案值

如果您有純量值的陣列 (例如 `[1,2,3]` ，在某些資料行中) ，您可以使用下列腳本，輕鬆地加以展開，並將其與主要資料列聯結：

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
