---
title: 使用 SQL 以查詢 Parquet 檔案(預覽)
description: 在本文中,您將學習如何使用 SQL 按需(預覽)查詢 Parquet 檔。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431692"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure 同步分析中使用 SQL 按需(預覽)查詢 Parquet 檔案

在本文中,您將學習如何使用 SQL 按需(預覽)編寫查詢,該 SQL 將讀取 Parquet 檔。

## <a name="prerequisites"></a>Prerequisites

在閱讀本文的其餘部分之前,請查看以下文章:

- [首次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="dataset"></a>資料集

您可以查詢 Parquet 檔,就像讀取 CSV 檔一樣。 唯一的區別是,應將 FILEFORMAT 參數設置為 PARQUET。 本文中的範例顯示了讀取 Parquet 檔的細節。

> [!NOTE]
> 讀取鑲木地板檔時,不必在 OPENROWSET WITH 子句中指定列。 SQL 按需將使用 Parquet 檔中的中繼資料,並按名稱綁定列。

您將使用資料夾*鑲木地板/計程車*進行範例查詢。 它包含紐約計程車 - 黃色計程車旅行記錄數據從2016年7月。 至2018年6月。

數據按年和月進行分區,文件結構如下:

- 年份=2016年
  - 月=6
  - ...
  - 月=12
- 年份=2017
  - 月=1
  - ...
  - 月=12
- 年份=2018
  - 月=1
  - ...
  - 月=6

## <a name="query-set-of-parquet-files"></a>查詢鑲木地板檔案集

查詢 Parquet 檔時,只能指定感興趣的欄。

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

## <a name="automatic-schema-inference"></a>自動架構推理

讀取 Parquet 檔時,不需要使用 OPENROWSET WITH 子句。 列名稱和資料類型會自動從 Parquet 檔案中讀取。

下面的範例顯示了 Parquet 檔的自動架構推理功能。 它不指定架構即可返回 2017 年 9 月的行數。

> [!NOTE]
> 讀取 Parquet 檔時,不必在"OPENROWSET WITH"子句中指定列。 在這種情況下,SQL 按需查詢服務將利用 Parquet 檔中的中繼資料,並按名稱綁定列。

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>查詢分割區資料

此示例中提供的數據集被劃分為(分區)到單獨的子資料夾中。 您可以使用檔案路徑函數定位特定分區。 此示例顯示 2017 年前三個月的票價金額(按年份、月份和 payment_type。

> [!NOTE]
> SQL 按需查詢與 Hive/Hadoop 分區方案相容。

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

鑲木地板檔包含每列的類型描述。 下表描述了如何將鑲木地板類型映射到 SQL 本機類型。

| 鑲木地板類型 | 鑲木地板邏輯類型(註解) | SQL 資料類型 |
| --- | --- | --- |
| BOOLEAN | | bit |
| 二進位 / BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |BINARY |
| BINARY |UTF8 |瓦爾查爾\*(UTF8 排序) |
| BINARY |STRING |瓦爾查爾\*(UTF8 排序) |
| BINARY |枚舉|瓦爾查爾\*(UTF8 排序) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |瓦爾恰爾(最大\*) (UTF8 排序) |
| BINARY |布森 |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar(最大),序列化為標準化格式 |
| INT32 |INT(8,真) |SMALLINT |
| INT32 |INT (16, 真) |SMALLINT |
| INT32 |INT (32, 真) |int |
| INT32 |INT(8,假) |TINYINT |
| INT32 |INT (16, 假) |int |
| INT32 |INT (32, 假) |BIGINT |
| INT32 |日期 |date |
| INT32 |DECIMAL |decimal |
| INT32 |時間 (米莉斯 )|time |
| INT64 |INT (64, 真 ) |BIGINT |
| INT64 |INT (64, 假 ) |十進制 (20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |時間(MICROS / NANOS) |time |
|INT64 |時間戳 (米裡斯 / MICROS / NANOS) |datetime2 |
|[複雜類型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |清單 |瓦爾恰爾(最大),序列化為JSON |
|[複雜類型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|瓦爾恰爾(最大),序列化為JSON |

## <a name="next-steps"></a>後續步驟

往下一篇文章,瞭解如何查詢[Parquet 巢狀態 。](query-parquet-nested-types.md)
