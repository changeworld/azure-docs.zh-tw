---
title: Azure 宇宙 DB 的 SQL 關鍵字
description: 瞭解 Azure 宇宙 DB 的 SQL 關鍵字。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: f2da2695ec20eac9dd2636104d3314427e60d541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498548"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的關鍵字

本文詳細介紹了在 Azure Cosmos DB SQL 查詢中可以使用的關鍵字。

## <a name="between"></a>BETWEEN

可以使用 關鍵字`BETWEEN`對字串或數值的範圍表示查詢。 例如，以下查詢返回第一個子級為 1-5 的所有專案（包括）。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

您還可以在`BETWEEN``SELECT`子句中使用關鍵字，如以下示例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

在 SQL API 中，與 ANSI SQL 不同，您可以針對混合類型的屬性工作表示範圍查詢。 例如，`grade`可能是一個數位，類似于`5`某些項，另一個字串與其他`grade4`項類似。 在這些情況下，如 JavaScript 中，兩種不同類型之間的比較會導致`Undefined`在 中，因此將跳過該專案。

> [!TIP]
> 為了加快查詢執行時間，請創建索引策略，該策略對`BETWEEN`子句篩選的任何數值屬性或路徑使用範圍索引類型。

## <a name="distinct"></a>DISTINCT

關鍵字`DISTINCT`消除了查詢投影中的重複項。

在此示例中，查詢對每個姓氏的值進行專案：

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

您還可以投影唯一物件。 在這種情況下，兩個文檔中的一個中不存在姓氏欄位，因此查詢返回一個空物件。

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

在子查詢中的投影中也可以使用 DISTINCT：

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

此查詢將生成包含每個子項給定名稱的陣列，並刪除重複項。 此陣列別名為子名稱，並投影到外部查詢中。

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

不支援具有聚合系統函數的查詢和具有 子`DISTINCT`查詢的查詢。 例如，不支援以下查詢：

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

使用 IN 關鍵字檢查指定值是否與清單中的任何值匹配。 例如，以下查詢返回 作為`id``WakefieldFamily`或`AndersenFamily`的所有族項。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

以下示例返回狀態為任何指定值的所有項：

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API 支援[通過 JSON 陣列進行反覆運算](sql-query-object-array.md#Iteration)，並通過 FROM 源中的 in 關鍵字添加新構造。

如果在`IN`篩選器中包含分區鍵，則查詢將自動篩選為僅相關分區。

## <a name="top"></a>頂端

TOP 關鍵字按未定義`N`的順序返回第一個查詢結果數。 最佳做法是，將 TOP 與`ORDER BY`子句一起限制為第`N`一個排序值數。 組合這兩個子句是可預測地指示 TOP 影響的行的唯一方法。

可以使用具有常量值的 TOP（如以下示例所示）或使用參數化查詢使用變數值。

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