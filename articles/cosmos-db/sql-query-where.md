---
title: Azure 宇宙 DB 中的 WHERE 子句
description: 瞭解 Azure 宇宙 DB 的 SQL WHERE 子句
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898759"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Azure 宇宙 DB 中的 WHERE 子句

可選的 WHERE`WHERE <filter_condition>`子句 （ ） 指定源 JSON 項必須滿足的條件才能將它們包含在結果中。 JSON 項必須評估要`true`考慮結果的指定條件。 索引層使用 WHERE 子句來確定可作為結果一部分的源項的最小子集。
  
## <a name="syntax"></a>語法
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>引數

- `<filter_condition>`  
  
   指定要傳回的文件必須滿足的條件。  
  
- `<scalar_expression>`  
  
   表示要計算之值的運算式。 有關詳細資訊[，請參閱 Scalar 運算式](sql-query-scalar-expressions.md)。  
  
## <a name="remarks"></a>備註
  
  為了傳回文件，指定為篩選條件的運算式必須評估為 True。 只有布林值`true`才能滿足條件，任何其他值：未定義、空、假、數位、陣列或物件將無法滿足條件。

  如果將分區鍵包含在`WHERE`子句中作為相等篩選器的一部分，則查詢將自動篩選為僅相關分區。

## <a name="examples"></a>範例

以下查詢請求包含`id`其值為`AndersenFamily`的屬性的項。 它排除沒有任何`id`屬性或其值不匹配`AndersenFamily`的任何項。

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

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 子句中的扇點運算式

前一個範例已顯示簡單的相等查詢。 SQL API 還支援各種[標量運算式](sql-query-scalar-expressions.md)。 最常用的是二元和一元運算式。 來源 JSON 物件中的屬性參考也是有效的運算式。

您可以使用以下支援的二進位運算子：  

|**運算子類型**  | **值** |
|---------|---------|
|算術 | +,-,*,/,% |
|位元    | \|, &, ^, <<, >>, >>> (zero-fill right shift) |
|邏輯    | AND、OR、NOT      |
|比較 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (串連) |

以下查詢使用二進位運算子：

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

您還可以使用一元運算子 _,-, *，而不是查詢，如以下示例所示：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

您還可以在查詢中使用屬性引用。 例如，`SELECT * FROM Families f WHERE f.isRegistered`返回包含值等於`isRegistered``true`的屬性的 JSON 項。 任何其他值，`false`如`null`、、、、、、、`Undefined``<number>``<string>``<object>`或`<array>`，從結果中排除項。

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [IN 關鍵字](sql-query-keywords.md#in)
- [from 子句](sql-query-from.md)