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
ms.openlocfilehash: dd7666bb9f22214fb4701e6be9edc171912d9bf9
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691871"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>透過 Azure Synapse Analytics 使用 SQL 隨選 (預覽) 將查詢結果儲存至儲存體

在本文中，您將了解如何使用 SQL 隨選 (預覽) 將查詢結果儲存至儲存體。

## <a name="prerequisites"></a>Prerequisites

您的第一步是檢閱下列文章，並確定您已符合必要條件：

- [第一次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Create external table as select

您可以使用 CREATE EXTERNAL TABLE AS SELECT (CETAS) 陳述式將查詢結果儲存至儲存體。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。 如果您尚未建立資料庫，請參閱[第一次設定](query-data-storage.md#first-time-setup)。 您必須將 Mydatasource 外部資料來源的 LOCATION 變更為指向您擁有其寫入權限的位置。 

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
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
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>使用已建立的外部資料表

您可以像一般外部資料表一樣地使用透過 CETAS 所建立的外部資料表。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。 如果您尚未建立資料庫，請參閱[第一次設定](query-data-storage.md#first-time-setup)。

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
