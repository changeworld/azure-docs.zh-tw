---
title: 主要、外和唯一索引鍵
description: Azure Synapse 分析中的 SQL 分析支援資料表條件約束
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0379bed08c3ee6931e931a78a2d2c91664535250
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198128"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>SQL 分析中的主鍵、外鍵和唯一索引鍵

瞭解 SQL 分析中的資料表條件約束，包括主鍵、外鍵和唯一索引鍵。

## <a name="table-constraints"></a>資料表條件約束 
SQL 分析支援下列資料表條件約束： 
- 只有在同時使用非叢集和未強制執行的情況時，才支援主要索引鍵。    
- 只有在使用 NOT 強制的情況時，才支援 UNIQUE 條件約束。   

SQL 分析中不支援 FOREIGN KEY 條件約束。  

## <a name="remarks"></a>備註
具有主鍵和（或）唯一索引鍵可讓 SQL 分析引擎為查詢產生最佳執行計畫。  主鍵資料行或唯一條件約束資料行中的所有值都應該是唯一的。 

在 SQL 分析中建立具有 primary key 或 unique 條件約束的資料表之後，使用者必須確定這些資料行中的所有值都是唯一的。  違反可能會導致查詢傳回不正確的結果。  這個範例會顯示如果 primary key 或 unique 條件約束資料行包含重複的值，查詢可能會傳回不正確的結果。  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>範例
建立具有主要金鑰的 SQL 分析資料表： 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
建立具有 unique 條件約束的 SQL 分析資料表：

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>後續步驟

建立 SQL 分析資料庫的資料表之後，下一步就是將資料載入資料表。 如需載入教學課程，請參閱[將資料載入 SQL 分析資料庫](load-data-wideworldimportersdw.md)。
