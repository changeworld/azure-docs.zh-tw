---
title: 聚合 Azure 宇宙 DB 中的函數
description: 瞭解 SQL 彙總函式語法、Azure Cosmos DB 支援的彙總函式類型。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464456"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>聚合 Azure 宇宙 DB 中的函數

彙總函式對`SELECT`子句中的一組值執行計算，並返回單個值。 例如，以下查詢返回`Families`容器中的項計數：

## <a name="examples"></a>範例

```sql
    SELECT COUNT(1)
    FROM Families f
```

結果如下：

```json
    [{
        "$1": 2
    }]
```

您還可以使用 VALUE 關鍵字僅返回聚合的標量值。 例如，下列查詢會以單一數字傳回值的計數：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果如下：

```json
    [ 2 ]
```

您還可以將聚合與篩選器合併。 例如，以下查詢返回位址狀態為 的項的`WA`計數。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

結果如下：

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>彙總函式的類型

SQL API 支援以下彙總函式。 `SUM`並`AVG`運算元值，和`COUNT`，`MIN`以及`MAX`處理數位、字串、布林和空。

| 函式 | 描述 |
|-------|-------------|
| COUNT | 以運算式傳回項目的數目。 |
| SUM   | 以運算式傳回所有值的總和。 |
| 最小值   | 傳回運算式中的最小值。 |
| 最大值   | 傳回運算式中的最大值。 |
| 平均   | 以運算式傳回值的平均。 |

您還可以聚合陣列反覆運算的結果。

> [!NOTE]
> 在 Azure 門戶的資料資源管理器中，彙總查詢可能僅將部分結果聚合到一個查詢頁上。 SDK 在所有頁面上生成單個累積值。 要使用代碼執行彙總查詢，您需要 .NET SDK 1.12.0、.NET 核心 SDK 1.1.0 或 JAVA SDK 1.9.5 或以上。

## <a name="remarks"></a>備註

這些聚合系統函數將受益于[範圍索引](index-policy.md#includeexclude-strategy)。 如果希望在`COUNT`屬性`SUM``MIN``MAX``AVG`上執行 、、、或屬性，則應[在索引策略中包括相關路徑](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [Azure 宇宙 DB 簡介](introduction.md)
- [系統功能](sql-query-system-functions.md)
- [使用者定義的函數](sql-query-udfs.md)