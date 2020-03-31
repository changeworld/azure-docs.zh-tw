---
title: Azure 宇宙 DB 查詢語言中的 StringToArray
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 StringToArray。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302911"
---
# <a name="stringtoarray-azure-cosmos-db"></a>字串陣列（Azure 宇宙資料庫）
 返回轉換為數組的運算式。 如果無法轉換運算式，則返回未定義。  
  
## <a name="syntax"></a>語法
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是要作為 JSON 陣列運算式解析的字串運算式。 
  
## <a name="return-types"></a>傳回類型
  
  返回陣列運算式或未定義。 
  
## <a name="remarks"></a>備註
  嵌套字串值必須用雙引號寫入才能有效。 有關 JSON 格式的詳細資訊，請參閱[json.org](https://json.org/)
  
## <a name="examples"></a>範例
  
  下面的示例顯示了不同類型`StringToArray`之間的表現。 
  
 以下是具有有效輸入的示例。

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

下面是無效輸入的示例。 
   
 陣列中的單個引號無效。
即使它們在查詢中有效，它們也不會解析為有效的陣列。 陣列字串中的字串必須轉義\\""*"，\\否則周圍的引號必須為單個"*"。"*"。

```sql
SELECT
    StringToArray("['5','6','7']")
```

以下為結果集。

```json
[{}]
```

以下是無效輸入的示例。
   
 傳遞的運算式將作為 JSON 陣列進行解析;以下不計算為類型陣列，因此返回未定義。
   
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

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
