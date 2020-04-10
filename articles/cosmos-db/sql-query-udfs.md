---
title: Azure 宇宙 DB 的使用者定義的函數 (UdF)
description: 瞭解 Azure Cosmos DB 中使用者定義的函數。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011118"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure 宇宙 DB 的使用者定義的函數 (UdF)

SQL API 支援使用者定義的函數 (UF)。 使用標量 UDF,可以傳遞零個或多個參數,並返回單個參數結果。 API 檢查每個參數為合法的 JSON 值。  

## <a name="udf-use-cases"></a>UDF 使用常用

API 擴展 SQL 語法以支援使用 UF 的自訂應用程式邏輯。 您可以將 UdF 註冊到 SQL API 中,並在 SQL 查詢中引用它們。 與存儲過程和觸發器不同,UDF 是唯讀的。

使用 UF,可以擴展 Azure Cosmos DB 的查詢語言。 UF 是在查詢投影中表達複雜業務邏輯的好方法。

但是,我們建議在:

- Azure Cosmos DB 中已存在等效[的系統函數](sql-query-system-functions.md)。 系統功能將始終使用比等效 UDF 更少的 RU。
- UDF 是查詢子句`WHERE`中 的唯一篩選器。 UDF 不使用索引,因此評估 UDF 需要載入文檔。 將使用索引的其他篩選器謂詞與 UDF`WHERE`結合在子句中,將減少 UDF 處理的文檔數。

如果在查詢中必須多次使用相同的 UDF,則應在[子查詢](sql-query-subquery.md#evaluate-once-and-reference-many-times)中引用 UDF,從而允許您使用 JOIN 運算式計算 UDF 一次,但多次引用它。

## <a name="examples"></a>範例

以下範例在Cosmos資料庫中的專案容器下註冊 UDF。 這個樣本的名稱建立 UDF,`REGEX_MATCH`其名稱稱為 。 它接受兩個 JSON`input`字`pattern`串值,並檢查第一個值是否與使用 JAvaScript`string.match()`函數在第二個字串中指定的模式匹配。

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

現在,在查詢投影中使用此 UDF。 在從查詢中調用 UDF 時`udf.`,必須 限定具有區分大小寫的前置碼。

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

可以使用限定在篩選器內`udf.`的前置字串的 UDF,如以下範例的名稱 :

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

從本質上講,UDF 是有效的標量運算式,可用於投影和篩選器。

要延伸 UdF 的電源,請檢視另一個具有條件邏輯的範例:

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

下面的範例練習 UDF:

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

如果 UDF 參數引用的屬性在 JSON 值中不可用,則該參數將被視為未定義,並跳過 UDF 調用。 同樣,如果 UDF 的結果未定義,則結果中不包括它。

如前面的示例所示,UDF 將 JavaScript 語言的強大功能與 SQL API 整合。 UF 提供了豐富的可程式設計介面,藉助內置的 JavaScript 運行時功能,可以執行複雜的程式性條件邏輯。 SQL API 在當前 WHERE 或 SELECT 子句階段處理時,為每個源項向 UdF 提供參數。 結果無縫地包含在整個執行管道中。 總之,UF 是作為查詢的一部分執行複雜業務邏輯的絕佳工具。

## <a name="next-steps"></a>後續步驟

- [Azure 宇宙 DB 簡介](introduction.md)
- [系統函式](sql-query-system-functions.md)
- [彙總](sql-query-aggregates.md)