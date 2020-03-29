---
title: Azure 宇宙 DB 中的組 BY 子句
description: 瞭解 Azure 宇宙 DB 的 GROUP BY 子句。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819097"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的組 BY 子句

GROUP BY 子句根據一個或多個指定屬性的值劃分查詢的結果。

> [!NOTE]
> Azure Cosmos DB 目前支援 .NET SDK 3.3 及以上中的 GROUP BY 以及 JavaScript SDK 3.4 及以上。
> 對其他語言 SDK 的支援當前不可用，但已計畫提供。

## <a name="syntax"></a>語法

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>引數

- `<scalar_expression_list>`

   指定將用於劃分查詢結果的運算式。

- `<scalar_expression>`
  
   除標量子查詢和標量聚合外，允許任何標量運算式。 每個標量運算式必須至少包含一個屬性引用。 單個運算式的數量或每個運算式的基數沒有限制。

## <a name="remarks"></a>備註
  
  當查詢使用 GROUP BY 子句時，SELECT 子句只能包含 GROUP BY 子句中包含的屬性和系統函數的子集。 一個例外是[聚合系統函數](sql-query-aggregates.md)，它可以出現在 SELECT 子句中，而不包含在 GROUP BY 子句中。 您還可以始終在 SELECT 子句中包含文本值。

  GROUP BY 子句必須以 SELECT、FROM 和 WHERE 子句之後以及 OFFSET LIMIT 子句之前。 您當前無法使用帶有 ORDER BY 子句的 GROUP BY，但這是計畫進行的。

  GROUP BY 子句不允許以下任何條款：
  
- 別名屬性或別名系統功能（SELECT 子句中仍允許進行別名）
- 子查詢
- 聚合系統函數（這些功能僅在 SELECT 子句中允許）

## <a name="examples"></a>範例

這些示例使用通過[Azure Cosmos DB 查詢運動場](https://www.documentdb.com/sql/demo)提供的營養資料集。

例如，下面是一個查詢，它返回每個食品組中的專案總數：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

某些結果（TOP 關鍵字用於限制結果）：

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

此查詢有兩個用於劃分結果的運算式：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

一些結果是：

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

此查詢在 GROUP BY 子句中具有系統功能：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

一些結果是：

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

此查詢在項屬性運算式中使用關鍵字和系統函數：

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
- [選擇子句](sql-query-select.md)
- [彙總函數](sql-query-aggregates.md)
