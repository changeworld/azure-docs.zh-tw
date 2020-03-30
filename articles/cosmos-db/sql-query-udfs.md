---
title: Azure 宇宙 DB 中的使用者定義的函數 （UdF）
description: 瞭解 Azure Cosmos DB 中使用者定義的函數。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614325"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的使用者定義的函數 （UdF）

SQL API 支援使用者定義的函數 （UF）。 使用標量 UDF，可以傳遞零個或多個參數，並返回單個參數結果。 API 檢查每個參數為合法的 JSON 值。  

API 擴展 SQL 語法以支援使用 UF 的自訂應用程式邏輯。 您可以將 UdF 註冊到 SQL API 中，並在 SQL 查詢中引用它們。 實際上，UDF 是特別設計來透過查詢進行呼叫的。 作為推論，UDF 不能像其他 JavaScript 類型（如預存程序和觸發器）一樣訪問上下文物件。 查詢是唯讀的，可以在主副本或輔助副本上運行。 與其他 JavaScript 類型不同，UF 設計用於在輔助副本上運行。

以下示例在 Cosmos 資料庫中的專案容器下註冊 UDF。 該示例創建一個 UDF，`REGEX_MATCH`其名稱為 。 它接受兩個 JSON`input`字串`pattern`值，並檢查第一個值是否與使用 JavaScript`string.match()`函數在第二個字串中指定的模式匹配。

## <a name="examples"></a>範例

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

現在，在查詢投影中使用此 UDF。 在從查詢中調用 UDF 時，必須`udf.`限定具有區分大小寫的首碼。

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

結果如下：

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

可以使用限定在篩選器內`udf.`的首碼的 UDF，如以下示例所示：

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

結果如下：

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

從本質上講，UDF 是有效的標量運算式，可用於投影和篩選器。

要擴展 UdF 的電源，請查看另一個具有條件邏輯的示例：

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

下面的示例練習 UDF：

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

結果如下：

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

如果 UDF 參數引用的屬性在 JSON 值中不可用，則該參數將被視為未定義，並跳過 UDF 調用。 同樣，如果 UDF 的結果未定義，則結果中不包括它。

如前面的示例所示，UDF 將 JavaScript 語言的強大功能與 SQL API 集成。 UF 提供了豐富的可程式設計介面，借助內置的 JavaScript 運行時功能，可以執行複雜的程式性條件邏輯。 SQL API 在當前 WHERE 或 SELECT 子句階段處理時，為每個源項向 UdF 提供參數。 結果無縫地包含在整個執行管道中。 總之，UF 是作為查詢的一部分執行複雜業務邏輯的絕佳工具。

## <a name="next-steps"></a>後續步驟

- [Azure 宇宙 DB 簡介](introduction.md)
- [系統功能](sql-query-system-functions.md)
- [彙總](sql-query-aggregates.md)