---
title: Synapse SQL 中的資料表資料類型
description: 在 Synapse SQL 中定義資料表資料類型的建議。
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
ms.openlocfilehash: 5a831d79d315cb71712515eed45d9a679d307e06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760915"
---
# <a name="table-data-types-in-synapse-sql"></a>Synapse SQL 中的資料表資料類型

在本文中，您將找到在 Synapse SQL 中定義資料表資料類型的建議。 

## <a name="data-types"></a>資料類型

Synapse SQL 支援最常使用的資料類型。 如需支援的資料類型清單，請參閱 CREATE TABLE 陳述式中的[資料類型](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes&preserve-view=true)。 

## <a name="minimize-row-length"></a>將資料列長度最小化

將資料類型的大小最小化可縮短資料列長度，這樣會提升查詢效能。 使用您的資料適用之最小資料類型。

- 避免使用較大的預設長度來定義字元資料行。 例如，如果最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。
- 當您僅需要 VARCHAR 時，請避免使用 [NVARCHAR][NVARCHAR]。
- 儘可能使用 NVARCHAR(4000) 或 VARCHAR(8000)，而非 NVARCHAR(MAX) 或 VARCHAR(MAX)。

> [!NOTE]
> 如果您使用 PolyBase 外部資料表載入您的 SQL 集區資料表，定義的資料表資料列長度不得超過 1 MB。 當資料列的變數長度資料超過 1 MB 時，您可以使用 BCP 但不可使用 PolyBase 載入資料列。

## <a name="identify-unsupported-data-types"></a>識別不支援的資料類型

如果您要從另一個 SQL 資料庫遷移您的資料庫，您可能會遇到 Synapse SQL 中不支援的資料類型。 使用此查詢可找出您現有 SQL 結構描述中不支援的資料類型。

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>不支援的資料類型可用的因應措施

下列清單顯示 Synapse SQL 不支援的資料類型，並提供您可以使用的替代方案，而不是不支援的資料類型。

| 不支援的資料類型 | 因應措施 |
| --- | --- |
| [幾何](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [地理位置](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[Nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (4000)  |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |將資料行分割成數個強型別資料行。 |
| [table](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |如果使用 SQL 集區，您可以轉換成臨時表。 如果使用 SQL (preview) ，您可以考慮使用 [CETAS](../sql/develop-tables-cetas.md)將資料儲存至儲存體。 |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |修改程式碼來使用 [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 和 [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 函式。 僅支援常數做為預設值，因此 current_timestamp 不可定義為預設條件約束。 如果您需要從 timestamp 類型資料行移轉資料列版本值，請對 NOT NULL 或 NULL 資料列版本值使用 [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) 或 [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8)。 |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [使用者定義型別](/sql/relational-databases/native-client/features/using-user-defined-types) |可能時，請轉換回原生資料類型。 |
| 預設值 | 預設值僅支援常值和常數。 |

## <a name="next-steps"></a>後續步驟

如需開發資料表的詳細資訊，請參閱[資料表概觀](develop-overview.md)。
