---
title: 主要、外和唯一索引鍵
description: Azure Synapse Analytics 中的 Synapse SQL 集區支援資料表條件約束
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 562e2cce317d8774ecf72971d53be4f66f9c3da4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212763"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Synapse SQL 集區中的主鍵、外鍵和唯一索引鍵

瞭解 Synapse SQL 集區中的資料表條件約束，包括主鍵、外鍵和唯一索引鍵。

## <a name="table-constraints"></a>資料表條件約束

Synapse SQL 集區支援下列資料表條件約束： 
- 只有在同時使用非叢集和未強制執行的情況時，才支援主要索引鍵。    
- 只有在使用 NOT 強制的情況時，才支援 UNIQUE 條件約束。

如需語法，請核取 [ [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) ] 和 [ [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)]。 

Synapse SQL 集區中不支援 FOREIGN KEY 條件約束。  


## <a name="remarks"></a>備註

具有主鍵和（或）唯一索引鍵可讓 Synapse SQL 集區引擎為查詢產生最佳執行計畫。  主鍵資料行或唯一條件約束資料行中的所有值都應該是唯一的。

在 Synapse SQL 集區中建立具有 primary key 或 unique 條件約束的資料表之後，使用者必須確定這些資料行中的所有值都是唯一的。  違反可能會導致查詢傳回不正確的結果。  這個範例會顯示如果 primary key 或 unique 條件約束資料行包含重複的值，查詢可能會傳回不正確的結果。  

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

建立具有主要金鑰的 Synapse SQL 集區資料表： 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
建立具有 unique 條件約束的 Synapse SQL 集區資料表：

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>後續步驟

建立 Synapse SQL 集區的資料表之後，下一步就是將資料載入資料表。 如需載入教學課程，請參閱將[資料載入至 SYNAPSE SQL 集](load-data-wideworldimportersdw.md)區。
