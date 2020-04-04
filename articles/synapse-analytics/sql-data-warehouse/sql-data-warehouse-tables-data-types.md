---
title: 定義資料類型
description: 在 Synapse SQL 池中定義表數據類型的建議。
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
ms.openlocfilehash: 5b3d24232c26bb4d483d360eb593bc361190ccfe
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631248"
---
# <a name="table-data-types-in-synapse-sql-pool"></a>Synapse SQL 池中的表資料類型
本文中包括用於在 SQL 池中定義表數據類型的建議。 

## <a name="supported-data-types"></a>支援的資料類型

SQL 池支援最常用的數據類型。 如需支援的資料類型清單，請參閱 CREATE TABLE 陳述式中的[資料類型](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes)。 

## <a name="minimize-row-length"></a>將資料列長度最小化
將資料類型的大小最小化可縮短資料列長度，這樣會提升查詢效能。 使用您的資料適用之最小資料類型。 

- 避免使用較大的預設長度來定義字元資料行。 例如，如果最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。 
- 當您僅需要 VARCHAR 時，請避免使用 [NVARCHAR](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。
- 儘可能使用 NVARCHAR(4000) 或 VARCHAR(8000)，而非 NVARCHAR(MAX) 或 VARCHAR(MAX)。

如果使用 PolyBase 外部表載入表,則表行的定義長度不能超過 1 MB。 當資料列的變數長度資料超過 1 MB 時，您可以使用 BCP 但不可使用 PolyBase 載入資料列。

## <a name="identify-unsupported-data-types"></a>識別不支援的資料類型
如果要從另一個 SQL 資料庫遷移資料庫,可能會發現 SQL 池中不支援的數據類型。 使用以下查詢在現有 SQL 架構中發現不支援的資料型態:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>不支援的資料類型可用的因應措施

下面的清單顯示了 SQL 池不支援的數據類型,並為不支援的數據類型提供了有用的替代方法。

| 不支援的資料類型 | 因應措施 |
| --- | --- |
| [幾何](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [地理](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [層次結構](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[恩瓦爾查爾](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [文字](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |將資料行分割成數個強型別資料行。 |
| [表](/sql/t-sql/data-types/table-transact-sql) |轉換成暫存資料表。 |
| [時間 戳](/sql/t-sql/data-types/date-and-time-types) |修改程式碼來使用 [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) 和 [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) 函式。 只有常量作為預設值支援,因此current_timestamp不能定義為默認約束。 如果需要從時間戳類型列遷移行版本值,請使用[BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) 或[VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) 來表示"無 NULL"或 NULL 行版本值。 |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [使用者定義型別](/sql/relational-databases/native-client/features/using-user-defined-types) |可能時，請轉換回原生資料類型。 |
| 預設值 | 預設值僅支援常值和常數。 |


## <a name="next-steps"></a>後續步驟

如需開發資料表的詳細資訊，請參閱[資料表概觀](sql-data-warehouse-tables-overview.md)。
