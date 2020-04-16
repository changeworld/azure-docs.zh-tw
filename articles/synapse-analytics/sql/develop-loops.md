---
title: 使用 T-SQL 循環
description: 使用 T-SQL 循環、替換游標和在 Synapse SQL 中的 SQL 池開發相關解決方案的提示。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429950"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>在突觸 SQL 中使用 T-SQL 迴圈
本文為您提供了使用 T-SQL 循環、替換游標以及使用 Synapse SQL 中的 SQL 池開發相關解決方案的基本提示。

## <a name="purpose-of-while-loops"></a>WHILE 迴圈的用途

Synapse SQL 支援用於重複執行語句塊的[WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15)迴圈。 只要指定的條件都成立，或者在程式碼使用 BREAK 關鍵字特別終止迴圈之前，這個 WHILE 迴圈都會繼續下去。 

SQL 池中的循環可用於替換 SQL 代碼中定義的游標。 幸運的是，幾乎所有以 SQL 程式碼撰寫的資料指標都是向前快轉，並且只讀取多樣性。 因此,[WHILE] 迴圈是替換游標的絕佳選擇。

## <a name="replacing-cursors-in-sql-pool"></a>取代 SQL 池中的游標

在潛入之前,應考慮以下問題:「是否可以重寫此游標以使用基於集的操作? 在許多情況下,答案是肯定的,並且通常是最好的方法。 基於集的操作的執行速度通常比反覆運算的逐行方法快。

快進唯讀游標可輕鬆替換為迴圈構造。 以下代碼是一個簡單的示例。 程式碼範例會更新資料庫中每個資料表的統計資料。 藉由反覆迴圈中的資料表，每個命令就能依序執行。

首先，建立暫存資料表，其中包含用來識別個別陳述式的唯一資料列數目：

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

其次,初始化執行迴圈所需的變數:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

現在每次對一個陳數式執行一次迴圈：

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

最後，將第一個步驟中建立的暫存資料表卸除

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](develop-overview.md)。
