---
title: 使用 SQL 以查詢 JSON 檔(預覽)
description: 本節介紹如何在 Azure Synapse 分析中使用 SQL 按需讀取 JSON 檔。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 645baf9102785d223fd1f23ae52a4609725f795b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770816"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>在 Azure 同步分析中使用 SQL 按需(預覽)查詢 JSON 檔案

在本文中,您將學習如何在 Azure 同步分析中使用 SQL 按需(預覽)編寫查詢。 查詢的目標是讀取 JSON 檔。

## <a name="prerequisites"></a>Prerequisites

在閱讀本文的其餘部分之前,請查看以下文章:

- [第一次設定](query-data-storage.md#first-time-setup)
- [先決條件](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>範例 JSON 檔

以下部分包含用於讀取 JSON 檔的示例腳本。 檔案儲存在*json*容器、資料夾*簿*中,並包含具有以下結構的單個書籍項目:

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

## <a name="read-json-files"></a>閱讀 JSON 檔

要使用JSON_VALUE和[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)處理 JSON 檔,您需要從儲存中讀取 JSON 檔作為單個列。 以下文稿將*book1.json*檔作為單列讀取:

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
> 您將整個 JSON 檔讀為單行或列。 因此,現場終結器、外場和 ROW 終結器設置為 0x0b。

## <a name="query-json-files-using-json_value"></a>使用JSON_VALUE查詢 JSON 檔

下面的查詢顯示了如何使用[JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)從一本名為《*密碼學中的概率和統計方法》* 一書中檢索標量值(標題、出版商),由選定文章介紹:

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

## <a name="query-json-files-using-json_query"></a>使用JSON_QUERY查詢 JSON 檔

以下查詢示範如何使用[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)從一本名為《*密碼學中的概率和統計方法,按選定主題進行介紹》* 的書檢索物件和陣列(作者):

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

以下查詢使用[OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。 它將檢索在一本名為《密碼學中*概率和統計方法》* 的書中檢索物件和屬性,由選定文章介紹:

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
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>後續步驟

本系列的下一篇文章將展示如何:

- [查詢資料夾與多個檔案](query-folders-multiple-csv-files.md)
- [建立和使用檢視](create-use-views.md)
