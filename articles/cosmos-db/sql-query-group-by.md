---
title: Azure Cosmos DB 中的 GROUP BY 子句
description: 了解 Azure Cosmos DB 的 GROUP BY 子句。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: 48f272ea9005d8eb24f639b4039a84163441cffa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101437"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 GROUP BY 子句
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

GROUP BY 子句會根據一或多個指定屬性值來分割查詢的結果。

## <a name="syntax"></a>語法

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>引數

- `<scalar_expression_list>`

   指定將用來分割查詢結果的運算式。

- `<scalar_expression>`
  
   除了純量子查詢和純量彙總之外，允許任何純量運算式。 每個純量運算式都必須至少包含一個屬性參考。 個別運算式的數目或每個運算式的基數沒有任何限制。

## <a name="remarks"></a>備註
  
  當查詢使用 GROUP BY 子句時，SELECT 子句只能包含 GROUP BY 子句中包含的屬性和系統函式子集。 其中一個例外狀況是[彙總系統函式](sql-query-aggregates.md)，這可以出現在 SELECT 子句中，而不會包含在 GROUP BY 子句中。 您也一律可以在 SELECT 子句中包含常值。

  GROUP BY 子句必須在 SELECT、FROM 和 WHERE 子句之後，以及 OFFSET LIMIT 子句之前。 您目前無法搭配 ORDER BY 子句使用 GROUP BY，但這是計畫中的。

  GROUP BY 子句不允許下列任何一個：
  
- 別名屬性或別名系統函式 (SELECT 子句中仍然允許使用別名)
- 子查詢
- 彙總系統函式 (這些只允許用於 SELECT 子句)

不支援具有彙總系統函式的查詢，以及具有 `GROUP BY` 的子查詢。 例如，不支援下列查詢：

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>範例

這些範例會使用透過 [Azure Cosmos DB 查詢園地](https://www.documentdb.com/sql/demo) \(英文\) 所提供的營養資料集。

例如，下列查詢會在每個 foodGroup 中傳回項目的總計數：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

部分結果如下 (TOP 關鍵字用來限制結果)：

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

此查詢有兩個用來分割結果的運算式：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

部分結果如下：

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

此查詢在 GROUP BY 子句中有系統函式：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

部分結果如下：

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

此查詢會在項目屬性運算式中同時使用關鍵字和系統函式：

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

結果如下：

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>後續步驟

- [快速入門](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [彙總函數](sql-query-aggregates.md)
