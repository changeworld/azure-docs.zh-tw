---
title: Azure Cosmos DB 中的彙總函式
description: 深入瞭解 SQL 彙總函式語法、Azure Cosmos DB 所支援的彙總函式類型。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552365"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 中的彙總函式
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

彙總函式會對子句中的一組值執行計算 `SELECT` ，並傳回單一值。 例如，下列查詢會傳回容器內的專案計數：

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>彙總函式的類型

SQL API 支援下列彙總函式。 `SUM` 以及 `AVG` 處理數值、 `COUNT` `MIN` `MAX` 字串、布林值和 null 等運算。

| 函式 | 說明 |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | 以運算式傳回值的平均。 |
| [COUNT](sql-query-aggregate-count.md) | 以運算式傳回項目的數目。 |
| [MAX](sql-query-aggregate-max.md) | 傳回運算式中的最大值。 |
| [MIN](sql-query-aggregate-min.md) | 傳回運算式中的最小值。 |
| [SUM](sql-query-aggregate-sum.md) | 以運算式傳回所有值的總和。 |


您也可以使用 VALUE 關鍵字，只傳回匯總的純量值。 例如，下列查詢會以單一數字傳回值的計數：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果為：

```json
    [ 2 ]
```

您也可以將匯總與篩選合併。 例如，下列查詢會傳回位址狀態為的專案計數 `WA` 。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

結果為：

```json
    [ 1 ]
```

## <a name="remarks"></a>備註

這些匯總系統函數將受益于 [範圍索引](index-policy.md#includeexclude-strategy)。 如果您想要在 `AVG` 屬性上進行、、 `COUNT` `MAX` 、 `MIN` 或， `SUM` 您應該 [在編制索引原則中包含相關的路徑](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [系統函數](sql-query-system-functions.md)
- [使用者定義函數](sql-query-udfs.md)