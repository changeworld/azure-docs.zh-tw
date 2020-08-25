---
title: Azure Cosmos DB 查詢語言中的 StringToArray
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 StringToArray。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b00fe7a515d1d27ce9be2ab62a96c719d5e045a5
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798579"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB) 
 傳回轉譯成陣列的運算式。 如果無法轉譯運算式，則會傳回 undefined。  
  
## <a name="syntax"></a>語法
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是要剖析為 JSON 陣列運算式的字串運算式。 
  
## <a name="return-types"></a>傳回類型
  
  傳回陣列運算式或未定義。 
  
## <a name="remarks"></a>備註
  以雙引號撰寫的嵌套字串值必須是有效的 JSON。 如需 JSON 格式的詳細資訊，請參閱 [json.org](https://json.org/)。這個系統函數將不會使用索引。
  
## <a name="examples"></a>範例
  
  下列範例顯示 `StringToArray` 不同類型的行為。 
  
 以下是具有有效輸入的範例。

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

以下為結果集。

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

以下是無效輸入的範例。 
   
 陣列內的單引號不是有效的 JSON。
雖然它們在查詢中是有效的，但不會剖析為有效的陣列。 陣列字串內的字串必須是 "[" " \\ \\ ]"，或周圍的引號必須是單一 ' [""] '。

```sql
SELECT
    StringToArray("['5','6','7']")
```

以下為結果集。

```json
[{}]
```

以下是無效輸入的範例。
   
 傳遞的運算式將會剖析為 JSON 陣列;下列不會評估為類型陣列，因此會傳回 undefined。
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

以下為結果集。

```json
[{}]
```

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
