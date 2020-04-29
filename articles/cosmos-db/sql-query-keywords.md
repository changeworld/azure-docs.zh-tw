---
title: Azure Cosmos DB 的 SQL 關鍵字
description: 瞭解 Azure Cosmos DB 的 SQL 關鍵字。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 069548b9b69ef6f7f6bde85ede830d97f3d312db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261562"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB 中的關鍵字

本文詳細說明可用於 Azure Cosmos DB SQL 查詢中的關鍵字。

## <a name="between"></a>BETWEEN

您可以使用`BETWEEN`關鍵字，根據字串或數值的範圍來表示查詢。 例如，下列查詢會傳回第一個子系的成績為1-5 （含）的所有專案。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

您也可以在`BETWEEN` `SELECT`子句中使用關鍵字，如下列範例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

在 SQL API 中，與 ANSI SQL 不同的是，您可以針對混合類型的屬性工作表示範圍查詢。 例如， `grade`可能是像`5`在某些專案中的數位，以及如`grade4`其他的字串。 在這些情況下，如同在 JavaScript 中，兩個不同類型之間的比較`Undefined`會產生，因此會略過專案。

> [!TIP]
> 如需更快速的查詢執行時間，請建立索引編制原則，針對`BETWEEN`子句所篩選的任何數值屬性或路徑，使用範圍索引類型。

## <a name="distinct"></a>DISTINCT

`DISTINCT`關鍵字會排除查詢投影中的重複專案。

在此範例中，查詢會針對每個姓氏執行下列專案的值：

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

結果如下：

```json
[
    "Andersen"
]
```

您也可以投影唯一的物件。 在此情況下，lastName 欄位不存在於這兩份檔的其中一個，因此查詢會傳回空的物件。

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

結果如下：

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT 也可以用於子查詢內的投影：

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

此查詢會投射一個陣列，其中包含每個子系的 givenName，並移除重複的專案。 此陣列的別名為 ChildNames，並預計在外部查詢中。

結果如下：

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

不支援具有匯總系統函數和子查詢的`DISTINCT`查詢。 例如，不支援下列查詢：

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

使用 IN 關鍵字來檢查指定的值是否符合清單中的任何值。 例如，下列查詢會傳回為`id` `WakefieldFamily`或`AndersenFamily`的所有家族專案。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

下列範例會傳回狀態為任何指定值的所有專案：

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API 支援[反覆運算 JSON 陣列](sql-query-object-array.md#Iteration)，並透過從來源的 in 關鍵字新增結構。

如果您在篩選中包含分割區`IN`索引鍵，您的查詢將會自動篩選成隻有相關的資料分割。

## <a name="top"></a>頂端

TOP 關鍵字會以未定義`N`的順序傳回第一個查詢結果數目。 最佳做法是使用 TOP 搭配`ORDER BY`子句，將結果限制為第一個`N`排序值的數目。 結合這兩個子句是可預測的唯一方式，指出最常影響的資料列。

您可以使用 TOP 搭配常數值，如下列範例所示，或搭配使用參數化查詢的變數值。

```sql
    SELECT TOP 1 *
    FROM Families f
```

結果如下：

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [聯結](sql-query-join.md)
- [子查詢](sql-query-subquery.md)