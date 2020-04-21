---
title: 在 SQL 隨選中建立和使用檢視 (預覽)
description: 在本節中，您將了解如何建立和使用檢視來包裝 SQL 隨選 (預覽) 查詢。 檢視可讓您重複使用這些查詢。 如果您想要使用工具 (例如 Power BI) 來搭配隨選 SQL，也是需要檢視。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420772"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 在 SQL 隨選 (預覽) 中建立及使用檢視

在本節中，您將了解如何建立和使用檢視來包裝 SQL 隨選 (預覽) 查詢。 檢視可讓您重複使用這些查詢。 如果您想要使用工具 (例如 Power BI) 來搭配隨選 SQL，也是需要檢視。

## <a name="prerequisites"></a>Prerequisites

您的第一個步驟是檢閱下列文章，並確定您已符合建立和使用 SQL 隨選檢視的必要條件：

- [第一次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>建立檢視

您可以用建立一般 SQL Server 檢視的相同方式來建立檢視。 下列查詢會建立可讀取 population.csv  檔案的檢視。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。 如果您尚未建立資料庫，請參閱[第一次設定](query-data-storage.md#first-time-setup)。

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

## <a name="use-a-view"></a>使用檢視

您可以在查詢中使用檢視，就像在 SQL Server 查詢中使用檢視一樣。

下列查詢會示範如何使用我們在[建立檢視](#create-a-view)中建立的 population_csv  檢視。 此範例會以遞減順序傳回各個國家/地區名稱和其 2019 年的人口數目。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。 如果您尚未建立資料庫，請參閱[第一次設定](query-data-storage.md#first-time-setup)。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>後續步驟

如需有關如何查詢不同檔案類型的詳細資訊，請參閱[查詢單一 CSV 檔案](query-single-csv-file.md)、[查詢 Parquet 檔案](query-parquet-files.md)及[查詢 JSON 檔案](query-json-files.md)文章。
