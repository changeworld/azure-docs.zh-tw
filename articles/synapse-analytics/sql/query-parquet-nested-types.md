---
title: 使用 SQL 隨選 (預覽) 查詢 Parquet 巢狀型別
description: 本文會說明如何查詢 Parquet 巢狀型別。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 82edee84317b5d542bf65e29514286f96c18bbcc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744229"
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

下列查詢會讀取 *structExample. parquet* 檔案，並顯示如何呈現巢狀資料行的元素：

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
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
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

## <a name="next-steps"></a>後續步驟

下一篇文章將說明如何[查詢 JSON 檔案](query-json-files.md)。
