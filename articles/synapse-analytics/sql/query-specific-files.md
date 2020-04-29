---
title: 在查詢中使用檔案中繼資料
description: OPENROWSET 函數會提供查詢中所使用之每個檔案的相關檔案和路徑資訊，以根據檔案名和（或）資料夾路徑來篩選或分析資料。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431549"
---
# <a name="using-file-metadata-in-queries"></a>在查詢中使用檔案中繼資料

SQL 隨選查詢服務可以處理多個檔案和資料夾，如[查詢資料夾和多個](query-folders-multiple-csv-files.md)檔案一文所述。 在本文中，您將瞭解如何在查詢中使用有關檔案和資料夾名稱的中繼資料資訊。

有時候，您可能需要知道哪些檔案或資料夾來源與結果集中的特定資料列相互關聯。

您可以使用函`filepath`式`filename`和（或）傳回結果集中的檔案名和（或）路徑。 或者，您可以使用它們來根據檔案名和（或）資料夾路徑來篩選資料。 這些函式會在[filename](develop-storage-files-overview.md#filename-function)函式和[filepath](develop-storage-files-overview.md#filepath-function)函式的語法一節中說明。 您可以在下方找到範例中的簡短描述。

## <a name="prerequisites"></a>先決條件

閱讀本文的其餘部分之前，請先參閱下列必要條件：

- [第一次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="functions"></a>函式

### <a name="filename"></a>檔案名稱

此函式會傳回資料列來源的檔案名。

下列範例會在2017的過去三個月內讀取 NYC 的黃色計程車資料檔案，並傳回每個檔案乘車的數目。 查詢的 OPENROWSET 部分會指定要讀取的檔案。

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

下列範例顯示如何在 WHERE 子句中使用*filename （）* ，以篩選要讀取的檔案。 它會存取查詢 OPENROWSET 部分中的整個資料夾，並篩選 WHERE 子句中的檔案。

您的結果會與先前的範例相同。

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

### <a name="filepath"></a>Filepath

Filepath 函數會傳回完整或部分路徑：

- 當呼叫時，如果沒有參數，它會傳回資料列來源的完整檔案路徑。
- 當以參數呼叫時，它會傳回路徑的一部分，此部分會符合參數中指定之位置上的萬用字元。 例如，參數值1會傳回符合第一個萬用字元之路徑的一部分。

下列範例會在2017的過去三個月內讀取 NYC 的黃色計程車資料檔案。 它會傳回每個檔案路徑的乘車數目。 查詢的 OPENROWSET 部分會指定要讀取的檔案。

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

下列範例顯示如何在 WHERE 子句中使用*filepath （）* ，以篩選要讀取的檔案。

您可以在查詢的 OPENROWSET 部分中使用萬用字元，並在 WHERE 子句中篩選檔案。 您的結果會與先前的範例相同。

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

在下一篇文章中，您將瞭解如何[查詢 Parquet](query-parquet-files.md)檔案。
