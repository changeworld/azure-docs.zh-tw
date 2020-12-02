---
title: 使用 T-sql 迴圈
description: 使用 T-sql 迴圈進行解決方案開發的秘訣，以及在 Azure Synapse Analytics 中取代專用 SQL 集區的資料指標。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3477b3095414248afa9fbc7417ab707c94f35546
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462720"
---
# <a name="using-t-sql-loops-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>針對 Azure Synapse Analytics 中的專用 SQL 集區使用 T-sql 迴圈

本文提供使用 T-sql 迴圈和取代資料指標進行專用 SQL 集區解決方案開發的秘訣。

## <a name="purpose-of-while-loops"></a>WHILE 迴圈的用途

Azure Synapse 中的專用 SQL 集區可支援 [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 迴圈，以重複執行語句區塊。 只要指定的條件都成立，或者在程式碼使用 BREAK 關鍵字特別終止迴圈之前，這個 WHILE 迴圈都會繼續下去。

迴圈適用於取代 SQL 程式碼中定義的資料指標。 幸運的是，幾乎所有以 SQL 程式碼撰寫的資料指標都是向前快轉，並且只讀取多樣性。 因此，WHILE 迴圈是取代資料指標的絕佳替代方案。

## <a name="replacing-cursors-in-dedicated-sql-pool"></a>取代專用 SQL 集區中的資料指標

不過，在開始之前，您應該先自問下列問題：「這個資料指標是否可以重寫以使用集合型作業？」

在許多情況下，答案是肯定的，且通常是最好的方法。 集合型作業的執行速度通常會比反覆的逐列方法更快。

向前快轉唯讀資料指標可以輕鬆地以迴圈建構取代。 下列範例是一個簡單的範例。 程式碼範例會更新資料庫中每個資料表的統計資料。 藉由反覆迴圈中的資料表，每個命令就能依序執行。

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
