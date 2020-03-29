---
title: 在 Azure Cosmos DB 查詢語言中IS_STRING
description: 瞭解 Azure Cosmos DB 中IS_STRING的 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a717f343b0f46522a3ce2bb56c32e3f15998d777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303761"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING（Azure 宇宙資料庫）
 傳回布林值，表示指定之運算式的類型為字串。  
  
## <a name="syntax"></a>語法
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>引數
  
*expr*  
   是任何運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下面的示例使用`IS_STRING`函數檢查 JSON 布林、數位、字串、空、物件、陣列和未定義類型的物件。  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 以下為結果集。  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>備註

該系統功能將受益于[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [類型檢查功能 Azure 宇宙資料庫](sql-query-type-checking-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
