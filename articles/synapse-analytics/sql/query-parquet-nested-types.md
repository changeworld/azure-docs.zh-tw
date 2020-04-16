---
title: 使用 SQL 按需(預覽)查詢鑲嵌巢狀類型
description: 在本文中,您將學習如何查詢 Parquet 嵌套類型。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431653"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>使用 Azure 同步分析中的 SQL 按需(預覽)查詢鑲嵌類型

在本文中,您將學習如何在 Azure 同步分析中使用 SQL 按需(預覽)編寫查詢。  此查詢將讀取鑲嵌嵌套類型。

## <a name="prerequisites"></a>Prerequisites

在閱讀本文的其餘部分之前,請查看以下文章:

- [首次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>專案嵌入或重複資料

下面的查詢讀取*只是SimpleArray.parquet*檔。 它投射 Parquet 檔案中的所有欄,包括嵌套或重複的資料。

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>從巢狀存取元素

以下查詢讀取*structexample.parquet*檔案,並展示如何顯示巢狀列的元素:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
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

## <a name="access-elements-from-repeated-columns"></a>從重複欄取元素

以下查詢讀取*justSimpleArray.parquet*檔案,並使用[JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)從重複欄(如陣列或映射)中檢索**標量**元素:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

以下查詢讀取*mapExample.parquet*檔案,並使用[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)從重複欄(如陣列或映射)中檢索**非標點**元素:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>後續步驟

下一篇文章會向您展示如何[查詢JSON檔](query-json-files.md)。
