---
title: 使用 SQL 隨選 (預覽) 查詢 Parquet 檔案
description: 本文會說明如何使用 SQL 隨選 (預覽) 來查詢 Parquet 檔案。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8083edaf647f52a07d55dddf21fe5751340783be
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496231"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用 SQL 隨選 (預覽) 來查詢 Parquet 檔案

本文中會介紹如何使用會讀取 Parquet 檔案的 SQL 隨選 (預覽) 來寫入查詢。

## <a name="quickstart-example"></a>快速入門範例

`OPENROWSET`函式可讓您藉由提供檔案的 URL 來讀取 parquet 檔案的內容。

### <a name="read-parquet-file"></a>讀取 parquet 檔案

查看檔案內容最簡單的方式， `PARQUET` 就是提供要運作的檔案 URL `OPENROWSET` ，並指定 parquet `FORMAT` 。 如果檔案可公開使用，或者您的 Azure AD 身分識別可以存取此檔案，則您應該能夠使用查詢來查看檔案的內容，如下列範例所示：

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

請確定您已存取此檔案。 如果您的檔案受到 SAS 金鑰或自訂 Azure 身分識別的保護，您就必須設定[sql 登入的伺服器層級認證](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)。

### <a name="data-source-usage"></a>資料來源使用方式

上一個範例使用檔案的完整路徑。 或者，您可以建立外部資料源，其位置會指向儲存體的根資料夾，並使用該資料來源和函式中檔案的相對路徑 `OPENROWSET` ：

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

如果資料來源受到 SAS 金鑰或自訂身分識別的保護，您可以[使用資料庫範圍認證來設定資料來源](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)。

### <a name="explicitly-specify-schema"></a>明確指定架構

`OPENROWSET`可讓您使用子句明確指定您想要從檔案讀取的資料行 `WITH` ：

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

在下列各節中，您可以瞭解如何查詢各種類型的 PARQUET 檔案。

## <a name="prerequisites"></a>Prerequisites

第一步是**建立資料庫**，其資料來源是參考 [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 儲存體帳戶。 然後在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)，將物件初始化。 此安裝指令碼會建立資料來源、資料庫範圍認證，以及用於這些範例中的外部檔案格式。

## <a name="dataset"></a>資料集

此範例使用 [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 資料集。 您可以使用與[讀取 CSV 檔案](query-parquet-files.md)相同的方式來查詢 Parquet 檔案。 唯一的差別為 `FILEFORMAT` 參數應設定為 `PARQUET`。 本文中的範例會介紹讀取 Parquet 檔案的細節。

## <a name="query-set-of-parquet-files"></a>Parquet 檔案的查詢集

當您查詢 Parquet 檔案時，可以只指定感興趣的資料行。

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>自動推斷結構描述

在讀取 Parquet 檔案時，不需要使用 OPENROWSET WITH 子句。 會自動從 Parquet 檔案中讀取資料行名稱和資料類型。

下列範例顯示 Parquet 檔案的自動結構描述推斷功能。 其會傳回 2017 年 9 月的資料列數目，而不需要指定結構描述。

> [!NOTE]
> 讀取 Parquet 檔案時，您不需要在 OPENROWSET WITH 子句中指定資料行。 在此案例中，SQL 隨選查詢服務會利用 Parquet 檔案中的中繼資料，並依名稱繫結資料行。

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>查詢分割區的資料

這個範例中提供的資料集會分割成不同的子資料夾。 您可以使用 filepath 函式將特定的分割區做為目標。 此範例會依年、月和 payment_type 顯示 2017 年前三個月的費用金額。

> [!NOTE]
> SQL 隨選查詢與 Hive/Hadoop 資料分割配置相容。

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>類型對應

Parquet 檔案包含每個資料行的類型描述。 下表說明 Parquet 類型如何對應至 SQL 原生類型。

| Parquet 類型 | Parquet 邏輯類型 (註釋) | SQL 資料類型 |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |BINARY |
| BINARY |UTF8 |varchar \*(UTF8 collation) |
| BINARY |STRING |varchar \*(UTF8 collation) |
| BINARY |ENUM|varchar \*(UTF8 collation) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(max) \*(UTF8 collation) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar(max)，序列化為標準格式 |
| INT32 |INT(8, true) |SMALLINT |
| INT32 |INT(16, true) |SMALLINT |
| INT32 |INT(32, true) |int |
| INT32 |INT(8, false) |TINYINT |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |BIGINT |
| INT32 |日期 |date |
| INT32 |DECIMAL |decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |BIGINT |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TIME (MICROS / NANOS) |time |
|INT64 |TIMESTAMP (MILLIS / MICROS / NANOS) |datetime2 |
|[複雜類型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |清單 |varchar(max)，序列化為 JSON |
|[複雜類型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(max)，序列化為 JSON |

## <a name="next-steps"></a>後續步驟

請繼續閱讀下一篇文章，了解如何[查詢 Parquet 巢狀類型](query-parquet-nested-types.md)。
