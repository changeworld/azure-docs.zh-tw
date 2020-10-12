---
title: Azure Cosmos DB 中的 WHERE 子句
description: 瞭解 Azure Cosmos DB 的 SQL WHERE 子句
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: ceffb203ccc2cca1ff6e1c53644cde955c2e0acb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86523497"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 WHERE 子句

選擇性的 WHERE 子句 (`WHERE <filter_condition>`) 指定) 的條件，來源 JSON 專案必須滿足這些 (條件，查詢才會將它們包含在結果中。 JSON 專案必須評估要 `true` 針對結果考慮的指定條件。 索引層級會使用 WHERE 子句來判斷可作為結果一部分之來源專案的最小子集。
  
## <a name="syntax"></a>語法
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>引數

- `<filter_condition>`  
  
   指定要傳回的文件必須滿足的條件。  
  
- `<scalar_expression>`  
  
   表示要計算之值的運算式。 如需詳細資料，請參閱純量 [運算式](sql-query-scalar-expressions.md) 。  
  
## <a name="remarks"></a>備註
  
  為了傳回文件，指定為篩選條件的運算式必須評估為 True。 只有布林值 `true` 會滿足條件，任何其他值：未定義、null、false、數位、陣列或物件都不會滿足條件。

  如果您在子句中包含分割區索引鍵 `WHERE` 做為等號比較篩選準則的一部分，您的查詢將會自動篩選為相關的資料分割。

## <a name="examples"></a>範例

下列查詢會要求包含 `id` 其值為之屬性的專案 `AndersenFamily` 。 它會排除任何沒有 `id` 屬性或其值不相符的專案 `AndersenFamily` 。

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果為：

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 子句中的純量運算式

前一個範例已顯示簡單的相等查詢。 SQL API 也支援各種純量 [運算式](sql-query-scalar-expressions.md)。 最常用的是二元和一元運算式。 來源 JSON 物件中的屬性參考也是有效的運算式。

您可以使用下列支援的二元運算子：  

|**運算子類型**  | **值** |
|---------|---------|
|算術 | +,-,*,/,% |
|位元    | \|, &, ^, <<, >>, >>> (zero-fill right shift) |
|邏輯    | AND、OR、NOT      |
|比較 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (串連) |

下列查詢會使用二元運算子：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

您也可以使用一元運算子 +,-, ~，而不是在查詢中，如下列範例所示：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

您也可以在查詢中使用屬性參考。 例如，傳回 `SELECT * FROM Families f WHERE f.isRegistered` JSON 專案，其中包含 `isRegistered` 值等於的屬性 `true` 。 任何其他值（例如、、、、、 `false` `null` `Undefined` `<number>` `<string>` `<object>` 或）都會 `<array>` 從結果中排除專案。 此外，您也可以使用 `IS_DEFINED` 類型檢查函式，根據指定的 JSON 屬性是否存在來進行查詢。 例如，會傳回沒有 `SELECT * FROM Families f WHERE NOT IS_DEFINED(f.isRegistered)` 值的任何 JSON 專案 `isRegistered` 。

## <a name="next-steps"></a>後續步驟

- [快速入門](sql-query-getting-started.md)
- [IN 關鍵字](sql-query-keywords.md#in)
- [FROM 子句](sql-query-from.md)
