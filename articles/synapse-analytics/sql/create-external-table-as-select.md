---
title: 將查詢結果儲存到儲存體
description: 在本文中，您將了解如何使用 SQL 隨選 (預覽) 將查詢結果儲存至儲存體。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d73e895371764d9dd28290648551d84181e022cd
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117584"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>透過 Azure Synapse Analytics 使用 SQL 隨選 (預覽) 將查詢結果儲存至儲存體

在本文中，您將了解如何使用 SQL 隨選 (預覽) 將查詢結果儲存至儲存體。

## <a name="prerequisites"></a>必要條件

您的第一個步驟是**建立資料庫**，您將在其中執行查詢。 然後藉由在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)來初始化物件。 此安裝指令碼會建立資料來源、資料庫範圍認證，以及用來讀取這些範例中資料的外部檔案格式。

依照本文中的指示，建立資料來源、資料庫範圍認證，以及用來將資料寫入輸出儲存體的外部檔案格式。

## <a name="create-external-table-as-select"></a>Create external table as select

您可以使用 CREATE EXTERNAL TABLE AS SELECT (CETAS) 陳述式將查詢結果儲存至儲存體。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-the-external-table"></a>使用外部資料表

您可以像一般外部資料表一樣地使用透過 CETAS 所建立的外部資料表。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>後續步驟

如需有關如何查詢不同檔案類型的詳細資訊，請參閱[查詢單一 CSV 檔案](query-single-csv-file.md)、[查詢 Parquet 檔案](query-parquet-files.md)及[查詢 JSON 檔案](query-json-files.md)文章。
