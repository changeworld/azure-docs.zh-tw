---
title: Azure Cosmos DB 中的使用者定義函數（Udf）
description: 瞭解 Azure Cosmos DB 中的使用者定義函數。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81011118"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB 中的使用者定義函數（Udf）

SQL API 提供使用者定義函數（Udf）的支援。 使用純量 Udf，您可以傳入零個或多個引數，並傳回單一引數結果。 API 會檢查每個引數是否為合法的 JSON 值。  

## <a name="udf-use-cases"></a>UDF 使用案例

此 API 會擴充 SQL 語法，以支援使用 Udf 的自訂應用程式邏輯。 您可以向 SQL API 註冊 Udf，並在 SQL 查詢中參考它們。 不同于預存程式和觸發程式，Udf 是唯讀的。

使用 Udf，您可以擴充 Azure Cosmos DB 的查詢語言。 Udf 是在查詢投射中表達複雜商務邏輯的絕佳方式。

不過，我們建議您在下列情況下避免 Udf：

- Azure Cosmos DB 中已經有對等的[系統函數](sql-query-system-functions.md)。 系統函數一律會使用比對等 UDF 少的 RU。
- UDF 是查詢`WHERE`子句中唯一的篩選準則。 UDF 的不會利用索引，因此評估 UDF 需要載入檔。 在`WHERE`子句中結合使用索引的其他篩選述詞與 UDF，可以減少 udf 處理的檔數目。

如果您必須在查詢中多次使用相同的 UDF，您應該參考[子查詢](sql-query-subquery.md#evaluate-once-and-reference-many-times)中的 udf，讓您可以使用聯結運算式來評估 udf 一次，但參考多次。

## <a name="examples"></a>範例

下列範例會在 Cosmos 資料庫中的專案容器下註冊 UDF。 此範例會建立一個 UDF，其`REGEX_MATCH`名稱為。 它接受兩個 JSON 字串值`input`和`pattern`，並使用 JavaScript 的`string.match()`函式來檢查第一個是否符合第二個中所指定的模式。

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

現在，請在查詢投影中使用此 UDF。 從查詢內呼叫 Udf 時，必須以區分`udf.`大小寫的前置詞來限定它們。

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

您可以在篩選準則中使用以`udf.`前置詞限定的 UDF，如下列範例所示：

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

基本上，Udf 是可以在投影和篩選中使用的有效純量運算式。

若要展開 Udf 的強大功能，請查看具有條件式邏輯的另一個範例：

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

下列範例會練習 UDF：

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

如果在 JSON 值中無法使用 UDF 參數所參考的屬性，則會將參數視為未定義，並略過 UDF 調用。 同樣地，如果 UDF 的結果未定義，則不會包含在結果中。

如上述範例所示，Udf 會將 JavaScript 語言的強大功能與 SQL API 整合在一起。 Udf 提供了豐富的程式設計介面，可在內建 JavaScript 執行時間功能的協助下，執行複雜的程式性、條件式邏輯。 SQL API 會在處理的目前 WHERE 或 SELECT 子句階段，為每個來源專案提供 Udf 的引數。 結果會順暢地合併在整體執行管線中。 總而言之，Udf 是很棒的工具，可在查詢過程中執行複雜的商務邏輯。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [系統函式](sql-query-system-functions.md)
- [彙總](sql-query-aggregates.md)