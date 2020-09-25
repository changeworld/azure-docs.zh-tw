---
title: 在 Azure Cosmos DB 中使用 JSON
description: 了解如何在 Azure Cosmos DB 中查詢和存取巢狀 JSON 屬性，以及使用特殊字元
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 355f73d46215aa9e05f4ea6d91bb173c77509b63
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270845"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用 JSON

在 Azure Cosmos DB 的 SQL (核心) API 中，會將項目儲存為 JSON。 類型系統和運算式僅限於處理 JSON 類型。 如需詳細資訊，請參閱 [JSON 規格](https://www.json.org/) \(英文\)。

我們將摘要說明使用 JSON 的一些重要層面：

- JSON 物件一律會以 `{` 的左大括弧開頭，並以 `}` 的右大括弧結束
- 您可以讓 JSON 屬性彼此以[巢狀](#nested-properties)形式放在一起
- JSON 屬性值可以是陣列
- JSON 屬性名稱會區分大小寫
- JSON 屬性名稱可以是任何字串值 (包括空格或非字母的字元)

## <a name="nested-properties"></a>巢狀屬性

您可以使用點存取子來存取巢狀 JSON。 您可以在查詢中，利用與使用任何其他屬性相同的方式來使用巢狀 JSON 屬性。

以下是具有巢狀 JSON 的文件：

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

在此案例中，`state`、`country` 和 `city` 屬性全都以巢狀形式放在 `address` 屬性內。

下列範例會投影兩個巢狀屬性：`f.address.state` 和 `f.address.city`。

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

除了巢狀屬性，JSON 也支援陣列。

以下是具有陣列的範例文件：

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

不過，在大多數案例中，您將在使用陣列時，使用[子查詢](sql-query-subquery.md)或[自我聯結](sql-query-join.md)。

例如，以下是顯示客戶銀行帳戶每日餘額的文件。

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

如果您想要執行查詢來顯示在某個時間點餘額為負數的所有客戶，您可以搭配子查詢使用 [EXISTS](sql-query-subquery.md#exists-expression)：

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Null 和未定義之間的差異

如果某個屬性未在專案中定義，則其值為 `undefined` 。 具有值的屬性 `null` 必須明確定義並指派 `null` 值。

例如，請考慮下列範例專案：

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

在此範例中，屬性的 `isRegistered` 值為， `undefined` 因為它會從專案中省略。 屬性 `creationDate` 具有 `null` 值。

Azure Cosmos DB 針對和屬性支援兩種實用的型別檢查系統函數 `null` `undefined` ：

* [IS_Null](sql-query-is-null.md) -檢查屬性值是否為 `null`
* [IS_DEFINED](sql-query-is-defined.md) -檢查是否已定義屬性值

您可以瞭解 [支援的運算子](sql-query-operators.md) 及其 `null` 和值的行為 `undefined` 。

## <a name="reserved-keywords-and-special-characters-in-json"></a>JSON 中的保留關鍵字和特殊字元

您可以使用加上引號的屬性運算子 `[]` 來存取屬性。 例如， `SELECT c.grade` and `SELECT c["grade"]` 是相等的。 在逸出包含空格、特殊字元的屬性，或具有和 SQL 關鍵字或保留字相同的名稱時，此語法很有用。

例如，下列文件具有名為 `order` 的屬性，以及包含特殊字元的屬性 `price($)`：

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
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

您應該重新撰寫相同查詢，如下所示：

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>JSON 運算式

投影也支援 JSON 運算式，如下列範例所示：

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

在上述範例中，`SELECT` 子句需要建立 JSON 物件，而且由於此範例不會提供索引鍵，因此，子句會使用隱含引數變數名稱 `$1`。 下列查詢會傳回兩個隱含引數變數：`$1` 和 `$2`。

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

您可以在查詢中明確使用值的別名。 如果查詢具有兩個同名的屬性，請使用別名來為這其中一個或兩個屬性重新命名，使其在投影的結果中變得更為明確。

### <a name="examples"></a>範例

將第二個值投影為 `NameInfo` 時，用於別名的 `AS` 關鍵字是選擇性的，如下列範例所示：

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

您無法使用別名，將值投影為具有空格、特殊字元或保留字的屬性名稱。 例如，如果您想要將值的投影變更為具有空格的屬性名稱，您可以使用 [JSON 運算式](#json-expressions)。

以下是範例：

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>後續步驟

- [快速入門](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [WHERE 子句](sql-query-where.md)
