---
title: Azure Cosmos DB 中的彙總函式
description: 瞭解 SQL 彙總函式語法、Azure Cosmos DB 支援的彙總函式類型。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: tisande
ms.openlocfilehash: df9700dd51c8915ff28c34cf0a29c2f5e48baa44
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897834"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 中的彙總函式

彙總函式會對 SELECT 子句中的一組值執行計算，並傳回單一值。 例如，下列查詢會傳回 `Families` 容器內的專案計數：

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

您也可以使用 VALUE 關鍵字，只傳回匯總的純量值。 例如，下列查詢會以單一數字傳回值的計數：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果如下：

```json
    [ 2 ]
```

您也可以將匯總與篩選結合在一起。 例如，下列查詢會傳回位址狀態為 `WA`的專案計數。

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

SQL API 支援下列彙總函式。 SUM 和 AVG 會對數值進行運算，而 COUNT、MIN 和 MAX 會在數位、字串、布林值和 null 上運作。

| 函數 | 描述 |
|-------|-------------|
| COUNT | 以運算式傳回項目的數目。 |
| SUM   | 以運算式傳回所有值的總和。 |
| MIN   | 傳回運算式中的最小值。 |
| MAX   | 傳回運算式中的最大值。 |
| AVG   | 以運算式傳回值的平均。 |

您也可以匯總陣列反復專案的結果。

> [!NOTE]
> 在 Azure 入口網站的資料總管中，匯總查詢可能只會匯總一個查詢頁面上的部分結果。 SDK 會產生所有頁面的單一累計值。 若要使用程式碼執行匯總查詢，您需要 .NET SDK 1.12.0、.NET Core SDK 1.1.0 或 JAVA SDK 1.9.5 或更新版本。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [系統函數](sql-query-system-functions.md)
- [使用者定義函數](sql-query-udfs.md)