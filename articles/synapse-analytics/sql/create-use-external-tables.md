---
title: 在 SQL 隨選 (預覽) 中建立及使用外部資料表
description: 在本節中，您將了解如何在 SQL 隨選 (預覽) 中建立和使用外部資料表。 當您想要在 SQL 隨選中控制外部資料的存取權，以及想要使用 Power BI 等工具搭配隨選 SQL 時，外部資料表會很實用。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420792"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 在 SQL 隨選 (預覽) 中建立及使用外部資料表

在本節中，您將了解如何在 SQL 隨選 (預覽) 中建立和使用外部資料表。 當您想要在 SQL 隨選中控制外部資料的存取權，以及想要使用 Power BI 等工具搭配隨選 SQL 時，外部資料表會很實用。

## <a name="prerequisites"></a>Prerequisites

您的第一個步驟是檢閱下列文章，並確定您已符合建立和使用 SQL 隨選外部資料表的必要條件：

- [第一次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>建立外部資料表

您可以建立外部資料表，就像建立一般 SQL Server 外部資料表一樣。 下列查詢會建立可讀取 population.csv  檔案的外部資料表。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。 如果您尚未建立資料庫，請參閱[第一次設定](query-data-storage.md#first-time-setup)。

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>使用外部資料表

您可以在查詢中使用外部資料表，就像在 SQL Server 查詢中使用外部資料表一樣。

下列查詢會示範如何使用我們在[建立外部資料表](#create-an-external-table)一節中建立的 population  外部資料表。 此範例會以遞減順序傳回各個國家/地區名稱和其 2019 年的人口數目。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。 如果您尚未建立資料庫，請參閱[第一次設定](query-data-storage.md#first-time-setup)。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>後續步驟

如需如何將查詢結果儲存到儲存體的相關資訊，請參閱[將查詢結果儲存到儲存體](../sql/create-external-table-as-select.md)。
