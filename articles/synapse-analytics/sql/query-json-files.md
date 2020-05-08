---
title: 使用隨選 SQL 查詢 JSON 檔案（預覽）
description: 本節說明如何使用 Azure Synapse 分析中的隨選 SQL 來讀取 JSON 檔案。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0db022ff4fdf092c2286d2da333df2ea23406569
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692705"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>使用 Azure Synapse 分析中的 SQL 隨選（預覽）來查詢 JSON 檔案

在本文中，您將瞭解如何使用 Azure Synapse 分析中的 SQL 隨選（預覽）來撰寫查詢。 查詢的目標是要讀取 JSON 檔案。

## <a name="prerequisites"></a>Prerequisites

閱讀本文的其餘部分之前，請先參閱下列文章：

- [第一次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>範例 JSON 檔案

下一節包含可讀取 JSON 檔案的範例腳本。 檔案會儲存在*json*容器、資料夾*書籍*中，並包含具有下列結構的單一書籍專案：

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>讀取 JSON 檔案

若要使用 JSON_VALUE 和[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)來處理 json 檔案，您必須以單一資料行的形式從儲存體讀取 json 檔案。 下列腳本會將*book1*讀取為單一資料行：

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> 您正在將整個 JSON 檔案讀取為單一資料列或資料行。 因此，FIELDTERMINATOR、FIELDQUOTE 和 ROWTERMINATOR 都會設定為0x0b。

## <a name="query-json-files-using-json_value"></a>使用 JSON_VALUE 查詢 JSON 檔案

下列查詢會示範如何使用[JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)從 Cryptology 中概率和統計方法的書籍中取出純量值（標題、發行者） *，這是所選文章的簡介*：

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>使用 JSON_QUERY 查詢 JSON 檔案

下列查詢會示範如何使用[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) *，從 Cryptology 中概率和統計方法*的書籍中取得物件和陣列（作者），這是所選主題的簡介：

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>使用 OPENJSON 查詢 JSON 檔案

下列查詢會使用[OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 它會在 Cryptology 中取得概率和統計方法的書籍內抓取物件和屬性 *，這是所選文章的簡介*：

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>後續步驟

本系列的下一篇文章將示範如何：

- [查詢資料夾和多個檔案](query-folders-multiple-csv-files.md)
- [建立和使用檢視](create-use-views.md)
