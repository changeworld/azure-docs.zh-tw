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
ms.openlocfilehash: bf2dbf501b5cd3b6cd0ab6b0e9bbbc2208c98a58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478445"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用 SQL 隨選 (預覽) 查詢 Parquet 巢狀型別

本文會說明如何在 Azure Synapse Analytics 中使用 SQL 隨選 (預覽) 來寫入查詢。  此查詢會讀取 Parquet 巢狀型別。

## <a name="prerequisites"></a>Prerequisites

第一步是使用資料庫參考的資料來源**建立資料庫**。 然後在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)，將物件初始化。 此安裝指令碼會建立資料來源、資料庫範圍認證，以及用於這些範例中的外部檔案格式。

## <a name="project-nested-or-repeated-data"></a>投射巢狀或重復資料

下列查詢會讀取 *justSimpleArray.parquet* 檔案。 其會投影 Parquet 檔案中的所有資料行，包括巢狀或重複的資料。

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>從巢狀資料行存取元素

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
        -- you can see original nested columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateValue] DATE '$.DateStruct.Date',
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        --FloatStruct VARCHAR(8000),
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

結構會當做資料 `MakOfPersons` `VARCHAR` 行傳回，並格式化為 JSON 字串。

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
