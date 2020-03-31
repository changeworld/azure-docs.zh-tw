---
title: Azure 宇宙 DB 中的 SELECT 子句
description: 瞭解 Azure 宇宙 DB 的 SQL SELECT 子句。 使用 SQL 作為 Azure 宇宙 DB JSON 查詢語言。
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469930"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的 SELECT 子句

每個查詢都包含一個 SELECT 子句和可選[的 FROM](sql-query-from.md)和[WHERE](sql-query-where.md)子句，每個查詢都包含 ANSI SQL 標準。 通常，將枚舉 FROM 子句中的源，並且 WHERE 子句在源上應用篩選器來檢索 JSON 項的子集。 然後，SELECT 子句在挑選清單中預測請求的 JSON 值。

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
  
  指定應刪除投影屬性的重複項。  

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

以下 SELECT 查詢示例`address`返回`Families`其`id`匹配`AndersenFamily`項 ：

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
您可以使用引號屬性運算子 [] 訪問屬性。 例如， `SELECT c.grade` and `SELECT c["grade"]` 是相等的。 此語法可用於轉義包含空格、特殊字元或與 SQL 關鍵字或保留項同名的屬性。

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>巢狀屬性

下面的示例預測兩個嵌套屬性 和`f.address.state``f.address.city`。

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

投影還支援 JSON 運算式，如以下示例所示：

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

在前面的示例中，SELECT 子句需要創建一個 JSON 物件，並且由於示例不提供任何鍵，因此子句使用隱式`$1`參數變數名稱 。 以下查詢返回兩個隱式參數變數： `$1` `$2`和 。

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
## <a name="reserved-keywords-and-special-characters"></a>保留關鍵字和特殊字元

如果資料包含與保留關鍵字（如"訂單"或"組"）相同的名稱的屬性，則對這些文檔的查詢將導致語法錯誤。 應顯式在字元中`[]`包含該屬性以成功執行查詢。

例如，下面是一個帶有名為`order`屬性的文檔，`price($)`並且屬性包含特殊字元：

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

如果運行包含屬性或`order``price($)`屬性的查詢，您將收到語法錯誤。

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

應重寫與以下內容相同的查詢：

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE 條款](sql-query-where.md)
