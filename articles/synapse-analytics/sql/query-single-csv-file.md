---
title: 使用 SQL 以查詢 CSV 檔(預覽)
description: 在本文中,您將學習如何使用 SQL 按需(預覽)查詢具有不同檔案格式的單個 CSV 檔。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431588"
---
# <a name="query-csv-files"></a>查詢 CSV 檔案

在本文中,您將學習如何在 Azure 同步分析中使用 SQL 按需(預覽)查詢單個 CSV 檔。 CSV 檔案可能具有不同的格式: 

- 標題與未命名列
- 逗號和製表符分隔值
- 視窗與 Unix 樣式行結尾
- 非引號和引號值以及轉義字元

下面將介紹上述所有變體。

## <a name="prerequisites"></a>Prerequisites

在閱讀本文的其餘部分之前,請查看以下文章:

- [首次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Windows 樣式新行

以下查詢演示如何讀取沒有標題行、Windows 樣式的新行和逗號分隔列的 CSV 檔。

檔案預覽:

![CSV 檔的前 10 行沒有標頭,Windows 樣式新行。](./media/query-single-csv-file/population.png)

```sql
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
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Unix 風格的新行

以下查詢演示如何讀取沒有標題行、具有 Unix 樣式的新行和逗號分隔列的檔。 請注意檔與其他示例相比的不同位置。

檔案預覽:

![CSV 檔的前 10 行沒有頭行和 Unix 樣式新行。](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>標題列

以下查詢示範如何使用頭行、Unix 樣式的新行和逗號分隔列的讀取檔。 請注意檔與其他示例相比的不同位置。

檔案預覽:

![CSV 檔的前 10 行,包含頭行和 Unix 樣式新行。](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>自訂報價字元

以下查詢示範如何讀取具有頭行、具有 Unix 樣式的新行、逗號分隔列和引號值的檔。 請注意檔與其他示例相比的不同位置。

檔案預覽:

![CSV 檔的前 10 行,包含頭行和 Unix 樣式新行和引用值。](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> 如果省略 FIELDQUOTE 參數,則此查詢將返回相同的結果,因為 FIELDQUOTE 的預設值為雙引號。

## <a name="escaping-characters"></a>逸出字元

以下查詢演示如何讀取具有頭行的檔,該檔具有 Unix 樣式的新行、逗號分隔列和用於值內欄位分隔符 (逗號)的轉義字元。 請注意檔與其他示例相比的不同位置。

檔案預覽:

![CSV 檔的前 10 行,包含頭行和 Unix 樣式新行和用於欄位分隔符的轉義字元。](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slov,enia';
```

> [!NOTE]
> 如果未指定 ESCAPECHAR,則此查詢將失敗,因為「Slov,enia」中的逗號將被視為欄位分隔符,而不是國家名稱的一部分。 "斯洛夫,伊尼亞"將被視為兩列。 因此,特定行的列數將大於其他行,並且一列的列將超過在 WITH 子句中定義的列。

## <a name="tab-delimited-files"></a>選項卡分隔檔案

以下查詢示範如何使用頭行、Unix 樣式的新行和選項卡分隔列讀取檔。 請注意檔與其他示例相比的不同位置。

檔案預覽:

![CSV 檔的前 10 行,包含頭行和 Unix 樣式新行和選項卡分隔符。](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>返回欄子集

到目前為止,您已經使用 WITH 並列出所有列指定了 CSV 檔架構。 您只能為每個所需的列使用一個批號來指定查詢中實際需要的列。 您還將省略不感興趣的列。

以下查詢傳回檔案中不同的國家/地區名稱數,僅指定所需的欄:

> [!NOTE]
> 請查看下面的查詢中的 WITH 子句,並注意行末尾有"2"(無引號),您可以在其中定義 *[country_name]* 列。 這意味著 *[country_name]* 列是檔中的第二列。 查詢將忽略檔中的所有列,但第二列除外。

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>後續步驟

下一篇文章將向您展示如何:

- [查詢 Parquet 檔案](query-parquet-files.md)
- [查詢資料夾與多個檔案](query-folders-multiple-csv-files.md)
