---
title: '使用無伺服器 SQL 集區來查詢 Parquet 檔 (預覽) '
description: 在本文中，您將瞭解如何使用無伺服器 SQL 集區 (預覽版) 來查詢 Parquet 檔。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: c8e454f523f081681d359dfd852c5f88d69a99d7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661047"
---
# <a name="query-parquet-files-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用無伺服器 SQL 集區 (預覽版) 查詢 Parquet 檔

在本文中，您將瞭解如何使用可讀取 Parquet 檔案的無伺服器 SQL 集區 (預覽版) 來撰寫查詢。

## <a name="quickstart-example"></a>快速入門範例

`OPENROWSET` 函數可讓您藉由提供檔案的 URL 來讀取 parquet 檔案的內容。

### <a name="read-parquet-file"></a>讀取 parquet 檔案

若要查看檔案的內容，最簡單的方式 `PARQUET` 就是提供檔案 URL 來 `OPENROWSET` 函數並指定 parquet `FORMAT` 。 如果檔案可公開取得，或您的 Azure AD 識別可以存取此檔案，您應該能夠使用如下列範例所示的查詢來查看檔案的內容：

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

請確定您可以存取此檔案。 如果您的檔案受到 SAS 金鑰或自訂 Azure 身分識別的保護，您必須設定 [sql 登入的伺服器層級認證](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)。

> [!IMPORTANT]
> 請確定您使用的是某些 UTF-8 資料庫定序 (例如) ， `Latin1_General_100_CI_AS_SC_UTF8` 因為 PARQUET 檔中的字串值是使用 utf-8 編碼進行編碼。
> PARQUET 檔案和定序中的文字編碼不相符可能會導致非預期的轉換錯誤。
> 您可以使用下列 T-sql 語句，輕鬆地變更目前資料庫的預設定序： `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>資料來源使用量

上一個範例使用檔案的完整路徑。 或者，您可以使用指向儲存體根資料夾的位置來建立外部資料源，然後使用該資料來源以及函式中檔案的相對路徑 `OPENROWSET` ：

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

如果使用 SAS 金鑰或自訂身分識別來保護資料來源，您可以 [使用資料庫範圍認證來設定資料來源](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)。

### <a name="explicitly-specify-schema"></a>明確指定架構

`OPENROWSET` 可讓您明確地指定要使用子句從檔案讀取的資料行 `WITH` ：

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> 確定您正在 explicilty 指定某些 UTF-8 定序 (例如 `Latin1_General_100_CI_AS_SC_UTF8`) 所有字串資料行 in `WITH` 子句，或在資料庫層級設定部分 utf-8 定序。
> 檔案和字串資料行定序中的文字編碼不相符可能會導致非預期的轉換錯誤。
> 您可以使用下列 T-sql 語句，輕鬆地變更目前資料庫的預設定序： `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> 您可以使用下列定義，輕鬆地設定上類型的定序： `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8`

在下列各節中，您可以瞭解如何查詢各種類型的 PARQUET 檔案。

## <a name="prerequisites"></a>Prerequisites

第一步是 **建立資料庫**，其資料來源是參考 [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 儲存體帳戶。 然後在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)，將物件初始化。 此安裝指令碼會建立資料來源、資料庫範圍認證，以及用於這些範例中的外部檔案格式。

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
> 讀取 Parquet 檔案時，您不需要在 OPENROWSET WITH 子句中指定資料行。 在此情況下，無伺服器 SQL 集區查詢服務將會利用 Parquet 檔中的中繼資料，並依名稱系結資料行。

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
> 無伺服器 SQL 集區查詢與 Hive/Hadoop 資料分割配置相容。

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

若為 Parquet 類型對應至 SQL 原生類型，請檢查 [Parquet 的類型對應](develop-openrowset.md#type-mapping-for-parquet)。

## <a name="next-steps"></a>後續步驟

請繼續閱讀下一篇文章，了解如何[查詢 Parquet 巢狀類型](query-parquet-nested-types.md)。
