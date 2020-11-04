---
title: 在查詢中使用檔案中繼資料
description: OPENROWSET 函式會提供查詢中所使用每個檔案的相關檔案和路徑資訊，以根據檔案名稱和/或資料夾路徑來篩選或分析資料。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3b4755d1d2e14b8ce3b05cfef6d30d7f6102905d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318819"
---
# <a name="use-file-metadata-in-serverless-sql-pool-queries"></a>在無伺服器 SQL 集區查詢中使用檔案中繼資料

無伺服器 SQL 集區可以處理多個檔案和資料夾，如 [查詢資料夾和多個](query-folders-multiple-csv-files.md) 檔案一文所述。 在本文中，您會了解如何在查詢時使用檔案和資料夾名稱的相關中繼資料資訊。

有時可能需要知道哪些檔案或資料夾來源與結果集中特定的資料列相互關聯。

您可以使用函式 `filepath` 和 `filename` 傳回結果集中的檔案名稱和/或路徑。 或是用以根據檔案名稱和/或資料夾路徑來篩選資料。 上述函式會在 [filename 函式](query-data-storage.md#filename-function)與 [filepath 函式](query-data-storage.md#filepath-function)等語法區段中說明。 在下列各節中，您會在範例中找到簡短描述。

## <a name="prerequisites"></a>Prerequisites

第一步是 **建立資料庫** ，其資料來源是參考儲存體帳戶。 然後在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)，將物件初始化。 此安裝指令碼會建立資料來源、資料庫範圍認證，以及用於這些範例中的外部檔案格式。

## <a name="functions"></a>函式

### <a name="filename"></a>檔案名稱

此函式會傳回資料列來源的檔案名稱。

下列範例會讀取 2017 年最後三個月的 NYC Yellow Taxi 資料檔案，並傳回每個檔案的乘車次數。 查詢的 OPENROWSET 部分會指定要讀取哪一個檔案。

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

下列範例會示範如何在 WHERE 子句中使用 filename() 來篩選要讀取的檔案。 它會存取查詢中 OPENROWSET 部分的整個資料夾，並篩選 WHERE 子句中的檔案。

您的結果會與先前的範例相同。

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

filepath 函式會傳回完整或部分路徑：

- 若在沒有參數的情況下進行呼叫，會傳回資料列來源的完整檔案路徑。
- 若以參數進行呼叫，則會傳回路徑的一部分，該部分會與參數中所指定位置上的萬用字元相符。 例如，參數值 1 會傳回符合第一個萬用字元的路徑部分。

下列範例會讀取 2017 年最後三個月的 NYC Yellow Taxi 資料檔案， 並傳回每個檔案路徑的乘車次數。 查詢的 OPENROWSET 部分會指定要讀取哪一個檔案。

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

下列範例會示範如何在 WHERE 子句中使用 filepath() 來篩選要讀取的檔案。

您可以在查詢的 OPENROWSET 部分中使用萬用字元，並在 WHERE 子句中篩選檔案。 您的結果會與先前的範例相同。

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
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

下一篇文章會說明如何[查詢 Parquet 檔案](query-parquet-files.md)。
