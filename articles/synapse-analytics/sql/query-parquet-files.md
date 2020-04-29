---
title: 使用 SQL 隨選查詢 Parquet 檔（預覽）
description: 在本文中，您將瞭解如何使用隨選 SQL （預覽）來查詢 Parquet 檔案。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431692"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的 SQL 隨選（預覽）來查詢 Parquet 檔案

在本文中，您將瞭解如何使用可讀取 Parquet 檔案的 SQL 隨選（預覽）來撰寫查詢。

## <a name="prerequisites"></a>先決條件

閱讀本文的其餘部分之前，請先參閱下列文章：

- [第一次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="dataset"></a>資料集

您可以用讀取 CSV 檔案的相同方式來查詢 Parquet 檔案。 唯一的差別在於 FILEFORMAT 參數應該設定為 PARQUET。 本文中的範例會示範讀取 Parquet 檔的細節。

> [!NOTE]
> 在讀取 parquet 檔時，您不需要在 OPENROWSET WITH 子句中指定資料行。 SQL 隨選會使用 Parquet 檔案中的中繼資料，並依名稱系結資料行。

您將使用*parquet/計程車*資料夾來進行範例查詢。 它包含2016年7月的 NYC 計程車-黃色計程車旅程記錄資料。 2018年6月。

資料是依年和月分割，而資料夾結構如下所示：

- year = 2016
  - 月份 = 6
  - ...
  - 月份 = 12
- year = 2017
  - 月份 = 1
  - ...
  - 月份 = 12
- year = 2018
  - 月份 = 1
  - ...
  - 月份 = 6

## <a name="query-set-of-parquet-files"></a>Parquet 檔的查詢集

當您查詢 Parquet 檔案時，您可以只指定感利率的資料行。

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>自動推斷結構描述

在讀取 Parquet 檔時，您不需要使用 OPENROWSET WITH 子句。 資料行名稱和資料類型會自動從 Parquet 檔案讀取。

下列範例顯示 Parquet 檔案的自動架構推斷功能。 它會傳回2017年9月的資料列數目，而不指定架構。

> [!NOTE]
> 在讀取 Parquet 檔時，您不需要在 OPENROWSET WITH 子句中指定資料行。 在這種情況下，SQL 隨選查詢服務會利用 Parquet 檔案中的中繼資料，並依名稱系結資料行。

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>查詢分割的資料

這個範例中提供的資料集會分割（分割）成不同的子資料夾。 您可以使用 filepath 函數，以特定的資料分割為目標。 這個範例會依年、月和 payment_type 顯示2017前三個月的費用金額。

> [!NOTE]
> SQL 隨選查詢與 Hive/Hadoop 資料分割配置相容。

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>類型對應

Parquet files 包含每個資料行的類型描述。 下表描述 Parquet 類型如何對應至 SQL 原生類型。

| Parquet 類型 | Parquet 邏輯類型（注釋） | SQL 資料類型 |
| --- | --- | --- |
| BOOLEAN | | bit |
| 二進位/BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |BINARY |
| BINARY |UTF8 |Varchar \*（UTF8 定序） |
| BINARY |STRING |Varchar \*（UTF8 定序） |
| BINARY |列舉|Varchar \*（UTF8 定序） |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |Varchar （max） \*（UTF8 定序） |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |Varchar （max），序列化為標準化格式 |
| INT32 |INT （8，true） |SMALLINT |
| INT32 |INT （16，true） |SMALLINT |
| INT32 |INT （32，true） |int |
| INT32 |INT （8，false） |TINYINT |
| INT32 |INT （16，false） |int |
| INT32 |INT （32，false） |BIGINT |
| INT32 |日期 |date |
| INT32 |DECIMAL |decimal |
| INT32 |時間（MILLIS）|time |
| INT64 |INT （64，true） |BIGINT |
| INT64 |INT （64，false） |decimal （20，0） |
| INT64 |DECIMAL |decimal |
| INT64 |TIME （MICROS/NANOS） |time |
|INT64 |TIMESTAMP （MILLIS/MICROS/NANOS） |datetime2 |
|[複雜類型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |清單 |Varchar （max），序列化為 JSON |
|[複雜類型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|Varchar （max），序列化為 JSON |

## <a name="next-steps"></a>後續步驟

前進到下一篇文章，以瞭解如何[查詢 Parquet 的巢狀型別](query-parquet-nested-types.md)。
