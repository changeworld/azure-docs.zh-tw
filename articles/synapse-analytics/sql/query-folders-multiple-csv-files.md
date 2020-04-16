---
title: 使用 SQL 以查詢資料夾與多個 CSV 檔案(預覽)
description: SQL 按需(預覽)支援使用通配符讀取多個檔/資料夾,這與 Windows OS 中使用的通配符類似。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 070574f0eec40c27eb05cec3bf0a84282b85050f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431796"
---
# <a name="query-folders-and-multiple-csv-files"></a>查詢資料夾與多個 CSV 檔案  

在本文中,您將學習如何在 Azure 同步分析中使用 SQL 按需(預覽)編寫查詢。

SQL 按需支援使用通配符讀取多個檔/資料夾,這與 Windows OS 中使用的通配符類似。 但是,由於允許使用多個通配符,因此存在更大的靈活性。

## <a name="prerequisites"></a>Prerequisites

在閱讀本文的其餘部分之前,請務必查看下面列出的文章:

- [首次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>讀取資料夾中的多個檔案

您將使用資料夾*csv/taxi*來追蹤範例查詢。 它包含 2016 年 7 月至 2018 年 6 月的紐約計程車 - 黃色計程車旅行記錄數據。

*csv/計程車*中的檔案以年份與月命名:

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

每個檔案具有以下結構:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>讀取資料夾中的所有檔案
    
下面的示例從*csv/計程車*資料夾中讀取所有 NYC 黃色計程車數據檔,並返回每年的乘客和乘車總數。 它還顯示了聚合函數的使用方式。

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
           pickup_location_id INT,'
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
> 使用單個 OPENROWSET 存取的所有文件必須具有相同的結構(即列數及其資料類型)。

### <a name="read-subset-of-files-in-folder"></a>讀取資料夾中的檔案子集

下面的示例使用通配符從*csv/計程車*資料夾中讀取 2017 年紐約市黃色計程車數據檔,並返回每個付款類型的總票價金額。

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
> 使用單個 OPENROWSET 存取的所有文件必須具有相同的結構(即列數及其資料類型)。

## <a name="read-folders"></a>讀取資料夾

提供給 OPENROWSET 的路徑也可以是資料夾的路徑。 以下部分包括這些查詢類型。

### <a name="read-all-files-from-specific-folder"></a>從特定資料夾讀取所有檔案

您可以使用檔案級通配符讀取資料夾中的所有檔,如[「讀取資料夾中的所有檔案」中](#read-all-files-in-folder)所示。 但是,有一種方法來查詢資料夾並使用該資料夾中的所有檔。

如果 OPENROWSET 中提供的路徑指向資料夾,則該資料夾中的所有檔都將用作查詢的源。 以下查詢將讀取*csv/taxi*資料夾中的所有檔案。

> [!NOTE]
> 請注意下面的查詢中路徑末尾存在 /。 它表示一個資料夾。 如果省略 /,則查詢將針對名為*taxi*的檔。

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
> 使用單個 OPENROWSET 存取的所有文件必須具有相同的結構(即列數及其資料類型)。

### <a name="read-all-files-from-multiple-folders"></a>從多個資料夾中讀取所有檔案

可以使用通配符從多個資料夾中讀取檔。 以下查詢將從*csv*資料夾中的所有資料夾中讀取所有檔,這些資料夾中的名稱以*t*開頭,以*i*結尾。

> [!NOTE]
> 請注意下面的查詢中路徑末尾存在 /。 它表示一個資料夾。 如果省略 /,則查詢將針對名為*t&ast;i*的檔。

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
> 使用單個 OPENROWSET 存取的所有文件必須具有相同的結構(即列數及其資料類型)。

由於您只有一個與條件匹配的資料夾,因此查詢結果與[讀取資料夾中的所有檔](#read-all-files-in-folder)相同。

## <a name="multiple-wildcards"></a>多個通配子

您可以在不同的路徑等級上使用多個通配符。 例如,您可以豐富以前的查詢,以便僅從以*t*開頭和以*i*開頭的所有資料夾中讀取包含 2017 數據的檔。

> [!NOTE]
> 請注意下面的查詢中路徑末尾存在 /。 它表示一個資料夾。 如果省略 /,則查詢將針對名為*t&ast;i*的檔。
> 每個查詢的最大限制為 10 個通配符。

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
> 使用單個 OPENROWSET 存取的所有文件必須具有相同的結構(即列數及其資料類型)。

由於您只有一個與條件匹配的資料夾,因此查詢結果與[資料夾中檔的「讀取」子集](#read-subset-of-files-in-folder)和[從特定資料夾中讀取所有檔](#read-all-files-from-specific-folder)相同。 更複雜的通配符使用方案在[查詢鑲拍檔中](query-parquet-files.md)介紹。

## <a name="next-steps"></a>後續步驟

有關詳細資訊,請參閱[查詢特定檔](query-specific-files.md)一文。
