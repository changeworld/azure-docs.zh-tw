---
title: Azure Cosmos DB 的 SQL 關鍵字
description: 瞭解 Azure Cosmos DB 的 SQL 關鍵字。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: tisande
ms.openlocfilehash: 09148e65e446d723fbfe7a54602db59ee0739f83
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599355"
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

## <a name="like"></a>LIKE

根據特定字元字串是否符合指定的模式，傳回布林值。 模式中可以包含一般字元及萬用字元。 您可以使用 `LIKE` 關鍵字或 [RegexMatch](sql-query-regexmatch.md) 系統函數撰寫邏輯相等的查詢。 無論您選擇哪一種，您都會觀察到相同的索引使用率。 因此， `LIKE` 如果您偏好使用比正則運算式更多的語法，則應該使用。

> [!NOTE]
> 因為 `LIKE` 可以利用索引，所以您應該針對要使用比較的屬性 [建立範圍索引](indexing-policy.md) `LIKE` 。

您可以使用下列萬用字元，如下所示：

| 萬用字元 | 描述                                                  | 範例                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | 任何零或多個字元的字串                      | 其中 c. 描述，例如 "% SO% PS%"      |
| _ (底線)      | 任何單一字元                                       | 其中 c. 描述，例如 "% SO_PS%"      |
| [ ]                  | 指定範圍內的任何單一字元 ( [a-f] ) 或設定 ( [abcdef] ) 。 | 其中 c. 描述，例如 "% SO [t z] PS%"  |
| [^]                  | 不在指定範圍內的任何單一字元 ( [^ a-f] ) 或設定 ( [^ abcdef] ) 。 | 其中 c. 描述，例如 "% SO [^ abc] PS%" |


### <a name="using-like-with-the--wildcard-character"></a>使用 LIKE 搭配 % 萬用字元

`%`字元符合零或多個字元的任何字串。 例如，將放 `%` 在模式的開頭和結尾，下列查詢會傳回包含下列其中一個描述的所有專案 `fruit` ：

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

如果您只使用 `%` 模式開頭的字元，則只會傳回具有開頭為之描述的專案 `fruit` ：

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>使用不贊

下列範例會傳回描述不包含的所有專案 `fruit` ：

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>使用 escape 子句

您可以使用 ESCAPE 子句來搜尋包含一或多個萬用字元的模式。 例如，如果您想要搜尋包含字串的描述 `20-30%` ，您不會想要將 `%` 作為萬用字元來解讀。

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>使用萬用字元做為常值

您可以用括弧括住萬用字元，將這些字元視為常值字元。 當您以括弧括住萬用字元時，就會移除所有的特殊屬性。 以下是一些範例：

| 模式           | 意義 |
| ----------------- | ------- |
| 例如 "20-30 [%]" | 20-30%  |
| 例如 "[_] n"     | _n      |
| 例如 "[[]"    | [       |
| 例如 "]"        | ]       |

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