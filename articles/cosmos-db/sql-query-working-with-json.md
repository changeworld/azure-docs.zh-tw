---
title: 在 Azure Cosmos DB 中使用 JSON
description: 瞭解如何查詢和存取嵌套的 JSON 屬性，並在 Azure Cosmos DB 中使用特殊字元
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: a8f32ad69d32844305c1cc785afc9f1df3c102b8
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006348"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用 JSON

在 Azure Cosmos DB 的 SQL （核心） API 中，專案會儲存為 JSON。 類型系統和運算式僅限於處理 JSON 類型。 如需詳細資訊，請參閱[JSON 規格](https://www.json.org/)。

我們將摘要說明使用 JSON 的一些重要層面：

- JSON 物件一律以`{`左大括弧開頭，並以`}`右大括弧結尾
- 您可以讓 JSON 屬性彼此[嵌套](#nested-properties)
- JSON 屬性值可以是陣列
- JSON 屬性名稱會區分大小寫
- JSON 屬性名稱可以是任何字串值（包括空格或不是字母的字元）

## <a name="nested-properties"></a>巢狀屬性

您可以使用點存取子來存取嵌套 JSON。 您可以在查詢中使用嵌套的 JSON 屬性，方法與使用任何其他屬性的方式相同。

以下是具有 nested JSON 的檔：

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

在此情況下， `state`、 `country`和`city`屬性全都嵌套在`address`屬性內。

下列範例會投射兩個嵌套的`f.address.state`屬性`f.address.city`：和。

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

## <a name="working-with-arrays"></a>使用陣列

除了嵌套屬性之外，JSON 也支援陣列。

以下是具有陣列的範例檔：

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

使用陣列時，您可以藉由參考其位置來存取陣列中的特定元素：

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

不過，在大多數情況下，當您使用陣列時，您會使用[子查詢](sql-query-subquery.md)或[自我聯結](sql-query-join.md)。

例如，以下是顯示客戶的銀行帳戶每日餘額的檔。

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

如果您想要執行一個查詢，以顯示某個時間點有負餘額的所有客戶，您可以使用[EXISTS](sql-query-subquery.md#exists-expression)搭配子查詢：

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="reserved-keywords-and-special-characters-in-json"></a>JSON 中的保留關鍵字和特殊字元

您可以使用加上引號的屬性運算子`[]`來存取屬性。 例如， `SELECT c.grade` and `SELECT c["grade"]` 是相等的。 此語法適用于將包含空格、特殊字元或名稱與 SQL 關鍵字或保留字相同的屬性加以轉義。

例如，以下是具有名為`order`之屬性的檔，以及包含`price($)`特殊字元的屬性：

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

如果您執行包含`order`屬性或`price($)`屬性的查詢，將會收到語法錯誤。

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

## <a name="json-expressions"></a>JSON 運算式

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

在上述範例中， `SELECT`子句需要建立 JSON 物件，而且由於此範例不會提供索引鍵，因此子句會使用隱含引數變數名稱`$1`。 下列查詢會傳回兩個隱含引數`$1`變數`$2`：和。

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

## <a name="aliasing"></a>別名

您可以在查詢中明確地別名值。 如果查詢有兩個具有相同名稱的屬性，請使用別名來重新命名其中一個或兩個屬性，以便在預測的結果中加以區分。

### <a name="examples"></a>範例

用於`AS`別名的關鍵字是選擇性的，如下列範例所示，將第二個值投射`NameInfo`為：

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果如下：

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>使用保留關鍵字或特殊字元的別名

您無法使用別名將值投影為具有空格、特殊字元或保留字的屬性名稱。 比方說，如果您想要將值的投影變更為，例如，有一個具有空格的屬性名稱，您可以使用[JSON 運算式](#json-expressions)。

以下是範例：

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           { "JSON expression with a special character": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [WHERE 子句](sql-query-where.md)
