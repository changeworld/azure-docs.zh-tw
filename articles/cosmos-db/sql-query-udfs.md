---
title: Azure Cosmos DB 中 (Udf) 的使用者定義函數
description: 瞭解 Azure Cosmos DB 中的使用者定義函數。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 82251cf803168c282e55438ca801ae1f30a2d019
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93093702"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB 中 (Udf) 的使用者定義函數
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

SQL API 可支援 (Udf) 的使用者定義函數。 使用純量 Udf 時，您可以傳入零個或多個引數，並傳回單一引數結果。 API 會檢查每個引數是否為合法的 JSON 值。  

## <a name="udf-use-cases"></a>UDF 使用案例

API 會擴充 SQL 語法，以支援使用 Udf 的自訂應用程式邏輯。 您可以向 SQL API 註冊 Udf，然後在 SQL 查詢中參考它們。 與預存程式和觸發程式不同的是，Udf 是唯讀的。

您可以使用 Udf 延伸 Azure Cosmos DB 的查詢語言。 Udf 是在查詢投射中表達複雜商務邏輯的絕佳方法。

不過，我們建議您在下列情況下避免 Udf：

- Azure Cosmos DB 中已經有對等的 [系統函數](sql-query-system-functions.md) 。 系統函式一律會使用比對等 UDF 更少的 RU。
- UDF 是查詢子句中唯一的篩選準則 `WHERE` 。 UDF 不會利用索引，因此評估 UDF 將需要載入檔。 在子句中結合搭配 UDF 使用索引的其他篩選述詞， `WHERE` 會減少 udf 所處理的檔數目。

如果您必須在查詢中多次使用相同的 UDF，您應該參考 [子查詢](sql-query-subquery.md#evaluate-once-and-reference-many-times)中的 udf，讓您可以使用聯結運算式來評估 udf 一次，但多次參考。

## <a name="examples"></a>範例

下列範例會在 Cosmos 資料庫的專案容器下註冊 UDF。 此範例會建立名稱為的 UDF `REGEX_MATCH` 。 它接受兩個 JSON 字串值（ `input` 和 `pattern` ），並使用 JavaScript 的函式來檢查第一個是否符合第二個中指定的模式 `string.match()` 。

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

現在，在查詢投影中使用此 UDF。 從查詢內呼叫 Udf 時，您必須使用區分大小寫的前置詞來限定 Udf `udf.` 。

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

結果為：

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

您可以使用限定于 `udf.` 篩選內前置詞的 UDF，如下列範例所示：

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

結果為：

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

基本上，Udf 是可在投影和篩選器中使用的有效純量運算式。

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

下列範例會演練 UDF：

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

結果為：

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

如果未在 JSON 值中提供 UDF 參數所參考的屬性，則會將參數視為未定義，而且會略過 UDF 調用。 同樣地，如果 UDF 的結果未定義，則不會包含在結果中。

如先前的範例所示，Udf 將 JavaScript 語言的強大功能與 SQL API 整合。 Udf 提供豐富的可程式化介面，可透過內建的 JavaScript 執行時間功能來進行複雜的程式化條件式邏輯。 SQL API 會針對目前 WHERE 或 SELECT 子句階段處理的每個來源專案，提供 Udf 的引數。 結果會在整體執行管線中順暢地合併。 總而言之，Udf 是很棒的工具，可在查詢過程中進行複雜的商務邏輯。

## <a name="next-steps"></a>下一步

- [Azure Cosmos DB 簡介](introduction.md)
- [系統函數](sql-query-system-functions.md)
- [彙總](sql-query-aggregates.md)