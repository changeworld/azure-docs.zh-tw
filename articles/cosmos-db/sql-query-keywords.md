---
title: Azure Cosmos DB 的 SQL 關鍵字
description: 瞭解 Azure Cosmos DB 的 SQL 關鍵字。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 35232f95bc18432db05775807d95f23ceab66aea
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333761"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB 中的關鍵字
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文詳述可在 Azure Cosmos DB SQL 查詢中使用的關鍵字。

## <a name="between"></a>BETWEEN

您可以使用 `BETWEEN` 關鍵字，針對字串或數值的範圍表達查詢。 例如，下列查詢會傳回第一個子系的等級為1-5 （含）的所有專案。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

您也可以 `BETWEEN` 在子句中使用關鍵字 `SELECT` ，如下列範例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

在 SQL API 中，與 ANSI SQL 不同的是，您可以針對混合類型的屬性工作表示範圍查詢。 例如， `grade` 可能是 `5` 某些專案中的數位，像是其他專案中的字串 `grade4` 。 在這些情況下（如同在 JavaScript 中），這兩種不同類型之間的比較 `Undefined` 會產生，因此會略過專案。

## <a name="distinct"></a>DISTINCT

`DISTINCT`關鍵字會排除查詢投射中的重複專案。

在此範例中，查詢會將每個姓氏的值投射：

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

結果為：

```json
[
    "Andersen"
]
```

您也可以投影唯一的物件。 在此情況下，lastName 欄位不存在於這兩份檔的其中一種，因此查詢會傳回空的物件。

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

結果為：

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` 也可以用於子查詢內的投射：

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

此查詢會投射包含每個子系 givenName 的陣列，並移除重複專案。 此陣列的別名為 ChildNames 並投射在外部查詢中。

結果為：

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

不支援具有彙總系統函式的查詢，以及具有 `DISTINCT` 的子查詢。 例如，不支援下列查詢：

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

使用 IN 關鍵字來檢查指定的值是否符合清單中的任何值。 例如，下列查詢會傳回的所有系列專案 `id` 為 `WakefieldFamily` 或 `AndersenFamily` 。

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

SQL API 支援 [反覆運算 JSON 陣列](sql-query-object-array.md#Iteration)，並透過從來源的 in 關鍵字加入新的結構。

如果您在篩選中包含分割區索引鍵 `IN` ，則您的查詢會自動篩選為相關的資料分割。

## <a name="top"></a>頂端

TOP 關鍵字會 `N` 以未定義的順序傳回第一個查詢結果數目。 最佳做法是使用 TOP 搭配 `ORDER BY` 子句來將結果限制為第一個 `N` 排序值的數目。 結合這兩個子句是可預測最重要的資料列會有何影響的唯一方法。

您可以使用 TOP 搭配常數值（如下列範例所示），或搭配使用參數化查詢的變數值。

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