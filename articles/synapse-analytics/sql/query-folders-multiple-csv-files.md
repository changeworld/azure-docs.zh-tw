---
title: 使用 SQL 隨選查詢資料夾和多個 CSV 檔案（預覽）
description: SQL 隨選（預覽）支援使用萬用字元讀取多個檔案/資料夾，這類似于 Windows 作業系統中使用的萬用字元。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457360"
---
# <a name="query-folders-and-multiple-csv-files"></a>查詢資料夾和多個 CSV 檔案  

在本文中，您將瞭解如何使用 Azure Synapse 分析中的 SQL 隨選（預覽）來撰寫查詢。

SQL 隨選支援使用萬用字元讀取多個檔案/資料夾，這類似于 Windows 作業系統中使用的萬用字元。 不過，因為允許多個萬用字元，所以有更大的彈性。

## <a name="prerequisites"></a>先決條件

閱讀本文的其餘部分之前，請務必先參閱下列文章：

- [第一次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>讀取資料夾中的多個檔案

您將使用*csv/計程車*資料夾來遵循範例查詢。 其中包含 NYC 計程車-黃色計程車旅程記錄資料，從2016年7月到6月2018。

*Csv/計程車*中的檔案會以年和月命名：

- yellow_tripdata_2016-07 .csv
- yellow_tripdata_2016 2018 .csv
- yellow_tripdata_2016-09 .csv
- ...
- yellow_tripdata_2018-04 .csv
- yellow_tripdata_2018-05 .csv
- yellow_tripdata_2018 06-01.5.1 .csv

每個檔案都有下列結構：
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>讀取資料夾中的所有檔案
    
下列範例會從*csv/計程車*資料夾讀取所有 NYC 的黃色計程車資料檔案，並傳回每年乘客和乘車的總數。 它也會顯示彙總函式的使用方式。

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構（也就是資料行數目和其資料類型）。

### <a name="read-subset-of-files-in-folder"></a>讀取資料夾中的檔案子集

下列範例會使用萬用字元從*csv/計程車*資料夾讀取 2017 NYC 的黃色計程車資料檔案，並傳回每個付款類型的總費用數量。

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構（也就是資料行數目和其資料類型）。

## <a name="read-folders"></a>讀取資料夾

您提供給 OPENROWSET 的路徑也可以是資料夾的路徑。 下列各節包含這些查詢類型。

### <a name="read-all-files-from-specific-folder"></a>讀取特定資料夾中的所有檔案

您可以使用檔案層級萬用字元讀取資料夾中的所有檔案，如[讀取資料夾中的所有](#read-all-files-in-folder)檔案所示。 但是，有一種方法可以查詢資料夾，並取用該資料夾內的所有檔案。

如果 OPENROWSET 中提供的路徑指向資料夾，則會使用該資料夾中的所有檔案作為查詢的來源。 下列查詢會讀取*csv/計程車*資料夾中的所有檔案。

> [!NOTE]
> 請注意下列查詢中，路徑結尾是否有/。 它代表一個資料夾。 如果省略/，查詢將會改為以名為*計程車*的檔案為目標。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構（也就是資料行數目和其資料類型）。

### <a name="read-all-files-from-multiple-folders"></a>讀取多個資料夾中的所有檔案

您可以使用萬用字元，從多個資料夾讀取檔案。 下列查詢會從*csv*資料夾中名稱開頭為*t*並以*i*結尾的所有資料夾讀取所有檔案。

> [!NOTE]
> 請注意下列查詢中，路徑結尾是否有/。 它代表一個資料夾。 如果省略/，查詢將會以名為*t&ast;i*的檔案作為目標。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構（也就是資料行數目和其資料類型）。

因為您只有一個符合準則的資料夾，所以查詢結果與 [[讀取資料夾中的所有](#read-all-files-in-folder)檔案] 相同。

## <a name="multiple-wildcards"></a>多個萬用字元

您可以在不同的路徑層級上使用多個萬用字元。 例如，您可以擴充先前的查詢，以讀取只有2017資料的檔案，從所有名稱開頭為*t*並以*i*結尾的資料夾。

> [!NOTE]
> 請注意下列查詢中，路徑結尾是否有/。 它代表一個資料夾。 如果省略/，查詢將會以名為*t&ast;i*的檔案作為目標。
> 每個查詢的最大限制為10個萬用字元。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構（也就是資料行數目和其資料類型）。

因為您只有一個符合準則的資料夾，所以查詢結果與 [[讀取資料夾中的檔案子集]](#read-subset-of-files-in-folder)和 [[讀取來自特定資料夾的所有](#read-all-files-from-specific-folder)檔案] 相同。 [查詢 Parquet](query-parquet-files.md)檔中涵蓋了更複雜的萬用字元使用案例。

## <a name="next-steps"></a>後續步驟

您可以在[查詢特定](query-specific-files.md)檔案一文中找到詳細資訊。
