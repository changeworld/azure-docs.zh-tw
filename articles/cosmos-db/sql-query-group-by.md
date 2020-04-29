---
title: Azure Cosmos DB 中的 GROUP BY 子句
description: 瞭解 Azure Cosmos DB 的 GROUP BY 子句。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 8a3cbbafc066747b62f79934f2cd12301aa1ba17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261596"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 GROUP BY 子句

GROUP BY 子句會根據一個或多個指定屬性的值來分割查詢的結果。

> [!NOTE]
> Azure Cosmos DB 目前支援 .NET SDK 3.3 和更新版本，以及 JavaScript SDK 3.4 和更新版本中的 GROUP BY。
> 目前無法使用其他語言 SDK 的支援，但已進行規劃。

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
  
   除了純量子查詢和純量匯總以外，允許任何純量運算式。 每個純量運算式都必須包含至少一個屬性參考。 個別運算式的數目或每個運算式的基數沒有任何限制。

## <a name="remarks"></a>備註
  
  當查詢使用 GROUP BY 子句時，SELECT 子句只能包含 GROUP BY 子句中包含的屬性和系統函數子集。 其中一個例外狀況是[匯總系統函數](sql-query-aggregates.md)，它可以出現在 SELECT 子句中，而不會包含在 group BY 子句中。 您也可以在 SELECT 子句中一律包含常值。

  GROUP BY 子句必須在 SELECT、FROM 和 WHERE 子句之後以及 OFFSET LIMIT 子句之前。 您目前無法搭配 ORDER BY 子句使用 GROUP BY，但這是計畫的。

  GROUP BY 子句不允許下列任何一項：
  
- 別名屬性或別名系統函數（SELECT 子句中仍然允許別名）
- 子查詢
- 匯總系統函數（這些只允許用於 SELECT 子句）

不支援具有匯總系統函數和子查詢的`GROUP BY`查詢。 例如，不支援下列查詢：

```sql
SELECT COUNT(UniqueLastNames) FROM (SELECT AVG(f.age) FROM f GROUP BY f.lastName) AS UniqueLastNames
```

## <a name="examples"></a>範例

這些範例會使用[Azure Cosmos DB 查詢遊樂場](https://www.documentdb.com/sql/demo)所提供的營養資料集。

例如，下列查詢會傳回每個 foodGroup 中專案的總計數：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

有些結果是（TOP 關鍵字是用來限制結果）：

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

此查詢有兩個用來分割結果的運算式：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

結果如下：

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

此查詢在 GROUP BY 子句中具有系統函數：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

結果如下：

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

此查詢會在 item 屬性運算式中使用關鍵字和系統函數：

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

結果如下：

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [彙總函數](sql-query-aggregates.md)
