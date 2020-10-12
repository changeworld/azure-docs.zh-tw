---
title: '使用 SQL 隨選 (預覽版查詢資料夾和多個檔案) '
description: SQL 隨選 (預覽版) 支援使用萬用字元（類似于 Windows OS 中使用的萬用字元）來讀取多個檔案/資料夾。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 54ef116878dee2ed1c351fac3dacdf359abbe574
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288336"
---
# <a name="query-folders-and-multiple-files"></a>查詢資料夾和多個檔案  

本文會說明如何在 Azure Synapse Analytics 中使用 SQL 隨選 (預覽) 來寫入查詢。

SQL 隨選支援使用萬用字元（類似于 Windows OS 中使用的萬用字元）來讀取多個檔案/資料夾。 不過，因為允許多個萬用字元，所以會有更大的彈性。

## <a name="prerequisites"></a>必要條件

您的第一個步驟是**建立資料庫**，您將在其中執行查詢。 然後藉由在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)來初始化物件。 此安裝指令碼會建立資料來源、資料庫範圍認證，以及用於這些範例中的外部檔案格式。

您將使用資料夾 *csv/計程車* 來遵循範例查詢。 它包含從2016年7月到6月2018的 NYC 計程車-黃色計程車行程記錄資料。 *Csv/計程車*中的檔案會使用下列模式，以年和月命名： yellow_tripdata_ <year> - <month> .csv

## <a name="read-all-files-in-folder"></a>讀取資料夾中的所有檔案
    
下列範例會讀取 *csv/計程車* 資料夾中所有 NYC 的黃色計程車資料檔案，並傳回每年乘客和乘車點的總數。 它也會顯示彙總函式的使用方式。

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構 (例如，資料行的數目和其資料類型) 。

### <a name="read-subset-of-files-in-folder"></a>讀取資料夾中的檔案子集

下列範例會使用萬用字元從 *csv/計程車* 資料夾讀取 2017 NYC 的黃色計程車資料檔案，並傳回每個付款類型的總費用金額。

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構 (例如，資料行的數目和其資料類型) 。

## <a name="read-folders"></a>讀取資料夾

您提供給 OPENROWSET 的路徑也可以是資料夾的路徑。 下列各節包含這些查詢類型。

### <a name="read-all-files-from-specific-folder"></a>讀取特定資料夾中的所有檔案

您可以使用檔案層級萬用字元來讀取資料夾中的所有檔案，如 [ [讀取資料夾中的所有](#read-all-files-in-folder)檔案] 所示。 但是，有一種方法可以查詢資料夾，並取用該資料夾內的所有檔案。

如果 OPENROWSET 中提供的路徑指向資料夾，則會使用該資料夾中的所有檔案作為查詢的來源。 下列查詢會讀取 *csv/計程車* 資料夾中的所有檔案。

> [!NOTE]
> 請注意下列查詢中路徑結尾的/是否存在。 它代表資料夾。 如果省略/，則查詢會改為將名稱為 *計程車* 的檔案設為目標。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構 (例如，資料行的數目和其資料類型) 。

### <a name="read-all-files-from-multiple-folders"></a>讀取多個資料夾中的所有檔案

您可以使用萬用字元讀取多個資料夾中的檔案。 下列查詢會從 *csv* 資料夾中名稱開頭為 *t* 且結尾為 *i*的所有資料夾讀取所有檔案。

> [!NOTE]
> 請注意下列查詢中路徑結尾的/是否存在。 它代表資料夾。 如果省略/，查詢將會以名為 *t &ast; i* 的檔案為目標。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構 (例如，資料行的數目和其資料類型) 。

由於您只有一個符合準則的資料夾，因此查詢結果與 [ [讀取資料夾中的所有](#read-all-files-in-folder)檔案] 相同。

## <a name="multiple-wildcards"></a>多個萬用字元

您可以在不同的路徑層級上使用多個萬用字元。 例如，您可以擴充先前的查詢，唯讀取2017資料的檔案，從所有名稱開頭為 *t* 並以 *i*結尾的資料夾中。

> [!NOTE]
> 請注意下列查詢中路徑結尾的/是否存在。 它代表資料夾。 如果省略/，查詢將會以名為 *t &ast; i* 的檔案為目標。
> 每個查詢有10個萬用字元的最大限制。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> 使用單一 OPENROWSET 存取的所有檔案都必須具有相同的結構 (例如，資料行的數目和其資料類型) 。

由於您只有一個符合準則的資料夾，因此查詢結果與 [資料夾中的檔案 [] 的 [讀取] 子集](#read-subset-of-files-in-folder) 相同，而且會 [從特定資料夾讀取所有](#read-all-files-from-specific-folder)檔案。 [查詢 Parquet](query-parquet-files.md)檔中涵蓋更複雜的萬用字元使用案例。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱「 [查詢特定](query-specific-files.md) 檔案」一文。
