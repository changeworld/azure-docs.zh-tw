---
title: 在突觸 SQL 中使用動態 SQL
description: 在突觸 SQL 中使用動態 SQL 的提示。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429599"
---
# <a name="dynamic-sql-in-synapse-sql"></a>同步 SQL 中的動態 SQL
在本文中,您將找到使用動態 SQL 和使用 Synapse SQL 開發解決方案的提示。

## <a name="dynamic-sql-example"></a>動態 SQL 範例

在開發應用程式代碼時,您可能需要使用動態 SQL 來説明提供靈活的通用和模組化解決方案。

> [!NOTE]
> SQL 池目前不支援 blob 資料類型。 不支援 Blob 資料類型可能會限制字串大小，因為 Blob 資料類型包括 varchar(max) 與 nvarchar(max) 類型。 如果您在曾在應用程式的程式碼中使用這些類型建立大型字串，便必須將這些程式碼分成許多區塊，並以 EXEC 陳述式取代。

以下是簡單的範例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串簡短，您可以像平常一樣使用 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

> [!NOTE]
> 作為動態 SQL 執行的語句仍將受所有 T-SQL 驗證規則的約束。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](develop-overview.md)。
