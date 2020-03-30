---
title: 在 Azure 宇宙 DB 查詢語言中複製
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 REPLICATE。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302180"
---
# <a name="replicate-azure-cosmos-db"></a>複製（Azure 宇宙資料庫）
 將字串值重複指定的次數。
  
## <a name="syntax"></a>語法
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   是字串運算式。
  
*num_expr*  
   為數值運算式。 如果*num_expr*為負數或非有限，則結果為未定義。
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。
  
## <a name="remarks"></a>備註
  結果的最大長度為 10，000 個字元，即（長度（*長度（str_expr*） = *num_expr*） <= 10，000。

## <a name="examples"></a>範例
  
  下面的示例演示如何`REPLICATE`在查詢中使用。
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 以下為結果集。
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>備註

此系統功能不會利用索引。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
