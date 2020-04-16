---
title: 在查詢中使用檔案中繼資料
description: OPENROWSET 函數提供有關查詢中使用的每個檔的文件和路徑資訊,以便根據檔案名和/或資料夾路徑篩選或分析數據。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431549"
---
# <a name="using-file-metadata-in-queries"></a>在查詢中使用檔案中繼資料

SQL 按需查詢服務可以處理多個檔和資料夾,如[查詢資料夾和多個檔](query-folders-multiple-csv-files.md)文章中所述。 在本文中,您將瞭解如何在查詢中使用有關檔和資料夾名稱的元數據資訊。

有時,您可能需要知道哪個文件或資料夾源與結果集中的特定行相關聯。

可以使用函數`filepath``filename`並返回結果集中的檔名和/或路徑。 或者,您可以使用它們根據檔名和/或資料夾路徑篩選數據。 這些函數在語法部分[檔名函數](develop-storage-files-overview.md#filename-function)和[文件路徑函數](develop-storage-files-overview.md#filepath-function)中描述。 下面您將找到樣品的簡要說明。

## <a name="prerequisites"></a>Prerequisites

在閱讀本文的其餘部分之前,請查看以下先決條件:

- [首次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="functions"></a>函式

### <a name="filename"></a>檔案名稱

此函數返回該行源自的檔名。

以下範例讀取 2017 年最後三個月的 NYC 黃色計程車數據檔,並返回每個檔的乘車次數。 查詢的 OPENROWSET 部份指定將讀取哪些檔。

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

下面的範例展示如何在 WHERE 子句中使用*filename()* 來篩選要讀取的檔。 它訪問查詢 OPENROWSET 部分中的整個資料夾,並篩選 WHERE 子句中的檔。

您的結果將與上一個範例相同。

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>檔案路徑

檔案路徑函數傳回完整或部份路徑:

- 當調用沒有參數時,它將返回行源自的完整文件路徑。
- 使用參數呼叫時,它將返回與參數中指定位置上的通配符匹配的部分路徑。 例如,參數值 1 將返回與第一個通配符匹配的路徑的一部分。

以下範例顯示 2017 年最後三個月的 NYC 黃色計程車數據檔。 它返回每個文件路徑的行駛次數。 查詢的 OPENROWSET 部份指定將讀取哪些檔。

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

下面的範例展示如何在 WHERE 子句中使用*filepath()* 來篩選要讀取的檔。

您可以在查詢的 OPENROWSET 部分中使用通配符,並篩選 WHERE 子句中的檔。 您的結果將與上一個範例相同。

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>後續步驟

在下一篇文章中,您會學習如何[查詢 Parquet 檔案](query-parquet-files.md)。
