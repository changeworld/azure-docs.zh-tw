---
title: 在 SQL 隨選 (預覽) 中建立及使用外部資料表
description: 在本節中，您將了解如何在 SQL 隨選 (預覽) 中建立和使用外部資料表。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 182deba959144f6a3992bb41243f29023bad5e5c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289322"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 在 SQL 隨選 (預覽) 中建立及使用外部資料表

在本節中，您將了解如何在 SQL 隨選 (預覽) 中建立和使用[外部資料表](develop-tables-external-tables.md)。 當您想要在 SQL 隨選中控制外部資料的存取權，以及想要使用 Power BI 等工具搭配隨選 SQL 時，外部資料表會很實用。 外部資料表可以存取兩種類型的儲存體：
- 公用儲存體，可供使用者存取公用儲存體檔案。
- 受保護的儲存體，可供使用者使用 SAS 認證、Azure AD 身分識別或 Synapse 工作區受控識別來存取儲存體檔案。

## <a name="prerequisites"></a>Prerequisites

您的第一個步驟是建立將在其中建立資料表的資料庫。 然後藉由在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)來初始化物件。 此安裝指令碼會建立下列物件以用於此範例：
- DATABASE SCOPED CREDENTIAL `sqlondemand`，可讓您存取受 SAS 保護的 `https://sqlondemandstorage.blob.core.windows.net` Azure 儲存體帳戶。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- EXTERNAL DATA SOURCE `sqlondemanddemo` (會參考以 SAS 金鑰保護的示範儲存體帳戶)，以及 EXTERNAL DATA SOURCE `YellowTaxi` (會參考位置 `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` 上公開可用的 Azure 儲存體帳戶)。

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- 檔案格式 `QuotedCSVWithHeaderFormat` 和 `ParquetFormat`，會描述 CSV 和 Parquet 檔案類型。

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

本文中的查詢將會在您的範例資料庫上執行，並使用這些物件。 

## <a name="create-an-external-table-on-protected-data"></a>在受保護的資料上建立外部資料表

您可以建立外部資料表來存取 Azure 儲存體帳戶上的資料，以允許具有某些 Azure AD 身分識別或 SAS 金鑰的使用者存取。 您可以建立外部資料表，就像建立一般 SQL Server 外部資料表一樣。 

下列查詢會建立外部資料表，以從 SynapseSQL 示範 Azure 儲存體帳戶 (使用 `sqlondemanddemo` 資料來源加以參考，並以名為 `sqlondemand` 的資料庫範圍認證加以保護) 中讀取 *population.csv* 檔案。 

資料來源和資料庫範圍認證會於[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)中建立。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>在公用資料上建立外部資料表

您可以建立外部資料表，以從公開可用 Azure 儲存體上所放置的檔案讀取資料。 此[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)會建立公用外部資料來源和 Parquet 檔案格式定義，以用於下列查詢：

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>使用外部資料表

您可以在查詢中使用[外部資料表](develop-tables-external-tables.md)，就像在 SQL Server 查詢中使用外部資料表一樣。

下列查詢會使用我們在上一節建立的 *population* 外部資料表來示範此操作。 此查詢會以遞減順序傳回各個國家/地區名稱和其 2019 年的人口數目。

> [!NOTE]
> 變更查詢中的第一行，也就是 [mydbname]，以使用您所建立的資料庫。

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

如需如何將查詢結果儲存到儲存體的相關資訊，請參閱[將查詢結果儲存到儲存體](../sql/create-external-table-as-select.md)一文。
