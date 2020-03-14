---
title: Azure Cosmos DB 的 SQL 關鍵字
description: 瞭解 Azure Cosmos DB 的 SQL 關鍵字。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 711e961bd5eb1607e2e6f11b0b5762423d78c0e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246574"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB 中的關鍵字
本文詳細說明可用於 Azure Cosmos DB SQL 查詢中的關鍵字。

## <a name="between"></a>BETWEEN

如同在 ANSI SQL 中，您可以使用 BETWEEN 關鍵字，根據字串或數值的範圍來表示查詢。 例如，下列查詢會傳回第一個子系的成績為1-5 （含）的所有專案。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

不同于 ANSI SQL，您也可以在 FROM 子句中使用 BETWEEN 子句，如下列範例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

在 SQL API 中，與 ANSI SQL 不同的是，您可以針對混合類型的屬性工作表示範圍查詢。 例如，`grade` 可能是某些專案中的 `5` 之類的數位，以及其他類似 `grade4` 的字串。 在這些情況下，如同在 JavaScript 中，兩個不同類型之間的比較會導致 `Undefined`，因此會略過專案。

> [!TIP]
> 如需更快速的查詢執行時間，請建立索引編制原則，針對任何數值屬性或 BETWEEN 子句篩選準則的路徑使用範圍索引類型。

## <a name="distinct"></a>DISTINCT

DISTINCT 關鍵字會排除查詢投影中的重複專案。

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

不支援具有匯總系統函數的查詢，以及具有 DISTINCT 的子查詢。 例如，不支援下列查詢：

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

使用 IN 關鍵字來檢查指定的值是否符合清單中的任何值。 例如，下列查詢會傳回 `id` `WakefieldFamily` 或 `AndersenFamily`的所有家族專案。

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

如果您在 `IN` 篩選準則中包含分割區索引鍵，您的查詢將會自動篩選成隻有相關的磁碟分割。

## <a name="top"></a>頂端

TOP 關鍵字會以未定義的順序傳回查詢結果的第一個 `N` 數目。 最佳做法是搭配 ORDER BY 子句使用 TOP，將結果限制為排序值的前 `N` 數目。 結合這兩個子句是可預測的唯一方式，指出最常影響的資料列。

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

- [快速入門](sql-query-getting-started.md)
- [聯結](sql-query-join.md)
- [子查詢](sql-query-subquery.md)