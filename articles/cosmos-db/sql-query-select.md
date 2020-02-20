---
title: Azure Cosmos DB 中的 SELECT 子句
description: 瞭解 Azure Cosmos DB 的 SQL SELECT 子句。 使用 SQL 做為 Azure Cosmos DB JSON 查詢語言。
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77469930"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 SELECT 子句

每個查詢都包含 SELECT 子句，以及每個 ANSI SQL 標準的選擇性[from](sql-query-from.md)和[WHERE](sql-query-where.md)子句。 通常會列舉 FROM 子句中的來源，而 WHERE 子句會在來源上套用篩選，以抓取 JSON 專案的子集。 然後，SELECT 子句會在選取清單中投射要求的 JSON 值。

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
  
  指定應該移除投影屬性的重複專案。  

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

下列 SELECT 查詢範例會從 `id` 符合 `AndersenFamily`的 `Families` 傳回 `address`：

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

### <a name="quoted-property-accessor"></a>加上引號的屬性存取子
您可以使用加上引號的屬性運算子 [] 來存取屬性。 例如， `SELECT c.grade` and `SELECT c["grade"]` 是相等的。 此語法適用于將包含空格、特殊字元或名稱與 SQL 關鍵字或保留字相同的屬性加以轉義。

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>巢狀屬性

下列範例會投射兩個 `f.address.state` 和 `f.address.city`的嵌套屬性。

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果如下：

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>JSON 運算式

投射也支援 JSON 運算式，如下列範例所示：

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果如下：

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

在上述範例中，SELECT 子句需要建立 JSON 物件，而且由於此範例不會提供索引鍵，因此子句會使用 `$1`的隱含引數變數名稱。 下列查詢會傳回兩個隱含引數變數： `$1` 和 `$2`。

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果如下：

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```
## <a name="reserved-keywords-and-special-characters"></a>保留的關鍵字和特殊字元

如果您的資料所包含的屬性具有與保留關鍵字相同的名稱，例如 "order" 或 "Group"，則針對這些檔的查詢將會導致語法錯誤。 您應該在 `[]` 字元中明確包含屬性，才能成功執行查詢。

例如，以下的檔具有名為 `order` 的屬性，以及包含特殊字元的屬性 `price($)`：

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

如果您執行包含 `order` 屬性或 `price($)` 屬性的查詢，將會收到語法錯誤。

```sql
SELECT * FROM c where c.order.orderid = "12345"
```
```sql
SELECT * FROM c where c.order.price($) > 50
```
結果如下：

`
Syntax error, incorrect syntax near 'order'
`

您應該重寫相同的查詢，如下所示：

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>後續步驟

- [快速入門](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE 子句](sql-query-where.md)
