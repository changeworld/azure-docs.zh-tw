---
title: 使用 T-SQL 迴圈
description: 在 Azure SQL 資料倉儲中使用 T-SQL 迴圈和取代資料指標以開發解決方案的秘訣。
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
ms.openlocfilehash: afb2160cb9b4e34d3d17db86bac9cd3be79886d0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351599"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>在 SQL 資料倉儲中使用 T-SQL 迴圈
在 Azure SQL 資料倉儲中使用 T-SQL 迴圈和取代資料指標以開發解決方案的秘訣。

## <a name="purpose-of-while-loops"></a>WHILE 迴圈的用途

SQL 資料倉儲支援 [WHILE](/sql/t-sql/language-elements/while-transact-sql) 迴圈重複執行陳述式區塊。 只要指定的條件都成立，或者在程式碼使用 BREAK 關鍵字特別終止迴圈之前，這個 WHILE 迴圈都會繼續下去。 迴圈適用於取代 SQL 程式碼中定義的資料指標。 幸運的是，幾乎所有以 SQL 程式碼撰寫的資料指標都是向前快轉，並且只讀取多樣性。 因此，[WHILE] 迴圈是用來取代資料指標的絕佳替代方式。

## <a name="replacing-cursors-in-sql-data-warehouse"></a>取代 SQL 資料倉儲中的資料指標
不過，在深入說明之前，您應該先自問下列問題：「這個資料指標是否能重新撰寫以使用集合型作業？」。 在許多狀況下答案是肯定的，通常也是最佳方案。 集合型作業的執行速度通常會比反覆的逐列方法更快。

向前快轉唯讀資料指標可以輕鬆地以迴圈建構取代。 以下是簡單的範例。 程式碼範例會更新資料庫中每個資料表的統計資料。 藉由反覆迴圈中的資料表，每個命令就能依序執行。

首先，建立暫存資料表，其中包含用來識別個別陳述式的唯一資料列數目：

```
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

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

現在每次對一個陳數式執行一次迴圈：

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

最後，將第一個步驟中建立的暫存資料表卸除

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。

