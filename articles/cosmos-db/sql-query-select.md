---
title: Azure Cosmos DB 中的 SELECT 子句
description: 瞭解 Azure Cosmos DB 的 SQL SELECT 子句。 使用 SQL 做為 Azure Cosmos DB 的 JSON 查詢語言。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 4d518461f8526a742a59d92140e2cf323e8bfc16
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082550"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 SELECT 子句
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

每個查詢都是由 `SELECT` 每個 ANSI SQL 標準的子句以及選擇性的 [From](sql-query-from.md) 和 [WHERE](sql-query-where.md) 子句所組成。 通常 `FROM` 會列舉子句中的來源，而 `WHERE` 子句會在來源上套用篩選，以取得 JSON 專案的子集。 `SELECT`子句接著會在選取清單中投射要求的 JSON 值。

## <a name="syntax"></a>語法

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>引數
  
- `<select_specification>`  

  要針對此結果集選取的屬性或值。  
  
- `'*'`  

  指定應該在不做出任何變更的情況下指定該值。 特別是若已處理的值是物件，則會擷取所有屬性。  
  
- `<object_property_list>`  
  
  指定要擷取的屬性清單。 每個傳回的值都會是具備指定屬性的物件。  
  
- `VALUE`  

  指定應該擷取 JSON 值，而非擷取完整的 JSON 物件。 這與 `<property_list>` 不同，不會在物件中包裝預估的值。  

- `DISTINCT`
  
  指定應該移除重複的投射屬性。  

- `<scalar_expression>`  

  表示要計算之值的運算式。 請參閱[純量運算式](sql-query-scalar-expressions.md)一節以取得詳細資料。  

## <a name="remarks"></a>備註

`SELECT *` 語法只有在 FROM 子句已明顯宣告一個別名時才會有效。 `SELECT *` 提供一個身分識別投影，在無需投影的情況下會非常實用。 SELECT * 只有在已指定 FROM 子句且只導入單一輸入來源時才有效。  
  
請注意，`SELECT <select_list>` 和 `SELECT *` 為「語法捷徑」，可以使用如下所示的簡單 SELECT 陳述式另外表示。  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   相當於：  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   相當於：  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>範例

下列 SELECT 查詢範例會 `address` 從其符合專案傳回 `Families` `id` `AndersenFamily` ：

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果如下：

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>後續步驟

- [快速入門](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE 子句](sql-query-where.md)
