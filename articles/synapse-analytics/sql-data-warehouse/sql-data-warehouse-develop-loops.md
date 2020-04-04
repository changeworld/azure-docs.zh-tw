---
title: 使用 T-SQL 循環
description: 使用 T-SQL 循環和替換 Synapse SQL 池中的游標的解決方案開發提示。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 72a39804931c0834233e91190aacffa8d35912df
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633486"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>在突觸 SQL 池中使用 T-SQL 迴圈

本文包括使用 T-SQL 循環和替換游標的 SQL 池解決方案開發提示。

## <a name="purpose-of-while-loops"></a>WHILE 迴圈的用途

Synapse SQL 池支援用於重複執行語句塊的[WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)迴圈。 只要指定的條件都成立，或者在程式碼使用 BREAK 關鍵字特別終止迴圈之前，這個 WHILE 迴圈都會繼續下去。

迴圈適用於取代 SQL 程式碼中定義的資料指標。 幸運的是，幾乎所有以 SQL 程式碼撰寫的資料指標都是向前快轉，並且只讀取多樣性。 因此,WHILE 迴圈是替換游標的絕佳選擇。

## <a name="replacing-cursors-in-synapse-sql-pool"></a>取代 Synapse SQL 池中的游標

但是,在先潛入頭部之前,您應該問自己以下問題:"是否可以重寫此游標以使用基於集的操作?

在許多情況下,答案是肯定的,並且通常是最好的方法。 集合型作業的執行速度通常會比反覆的逐列方法更快。

向前快轉唯讀資料指標可以輕鬆地以迴圈建構取代。 下面的示例很簡單。 程式碼範例會更新資料庫中每個資料表的統計資料。 藉由反覆迴圈中的資料表，每個命令就能依序執行。

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

其次，初始化執行迴圈的必要變數：

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

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
