---
title: Azure Cosmos DB 中的彙總函式
description: 深入瞭解 SQL 彙總函式語法、Azure Cosmos DB 所支援的彙總函式類型。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 7c988f379e94bf2f69854c90d45af42fe2a7ec4f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332775"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 中的彙總函式
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

彙總函式會對子句中的一組值執行計算 `SELECT` ，並傳回單一值。 例如，下列查詢會傳回容器內的專案計數 `Families` ：

## <a name="examples"></a>範例

```sql
    SELECT COUNT(1)
    FROM Families f
```

結果為：

```json
    [{
        "$1": 2
    }]
```

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

## <a name="types-of-aggregate-functions"></a>彙總函式的類型

SQL API 支援下列彙總函式。 `SUM` 以及 `AVG` 處理數值、 `COUNT` `MIN` `MAX` 字串、布林值和 null 等運算。

| 函式 | 描述 |
|-------|-------------|
| COUNT | 以運算式傳回項目的數目。 |
| SUM   | 以運算式傳回所有值的總和。 |
| 最小值   | 傳回運算式中的最小值。 |
| 最大值   | 傳回運算式中的最大值。 |
| AVG   | 以運算式傳回值的平均。 |

您也可以對陣列反覆運算的結果進行匯總。

> [!NOTE]
> 在 Azure 入口網站的資料總管中，匯總查詢可能只會在一個查詢頁面上匯總部分結果。 SDK 會跨所有頁面產生單一的累計值。 若要使用程式碼執行彙總查詢，您需要 .NET SDK 1.12.0、.NET Core SDK 1.1.0 或 JAVA SDK 1.9.5 或更新版本。

## <a name="remarks"></a>備註

這些匯總系統函數將受益于 [範圍索引](index-policy.md#includeexclude-strategy)。 如果您想要在 `COUNT` `SUM` 屬性上進行、、 `MIN` 、或， `MAX` `AVG` 您應該 [在編制索引原則中包含相關的路徑](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [系統函數](sql-query-system-functions.md)
- [使用者定義函數](sql-query-udfs.md)