---
title: 在 Synapse SQL 中使用動態 SQL
description: 在 Synapse SQL 中使用動態 SQL 的秘訣。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 4a4ce92278d5f6704fec4a5b9d30b44ddf89b0ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033433"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Synapse SQL 中的動態 SQL
在本文中，您將找到使用動態 SQL 以及使用 Synapse SQL 開發解決方案的秘訣。

## <a name="dynamic-sql-example"></a>動態 SQL 範例

開發應用程式程式碼時，您可能需要使用動態 SQL，以協助提供有彈性、泛型和模組化的解決方案。

> [!NOTE]
> SQL 集區目前不支援 blob 資料類型。 不支援 Blob 資料類型可能會限制字串大小，因為 Blob 資料類型包括 varchar(max) 與 nvarchar(max) 類型。 如果您在曾在應用程式的程式碼中使用這些類型建立大型字串，便必須將這些程式碼分成許多區塊，並以 EXEC 陳述式取代。

以下是簡單的範例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串簡短，您可以像平常一樣使用 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

> [!NOTE]
> 以動態 SQL 的形式執行的語句仍會受限於所有的 T-sql 驗證規則。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](develop-overview.md)。
