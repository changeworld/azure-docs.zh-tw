---
title: 在 Azure 宇宙 DB 查詢語言中啟動
description: 瞭解 Azure 宇宙 DB 中的 SQL 系統函數 STARTSWITH。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295703"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH（Azure 宇宙資料庫）
 傳回布林值，表示第一個字串運算式是否以第二個字串運算式開頭。  
  
## <a name="syntax"></a>語法
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   是字串運算式。
  
*str_expr2*  
   是要與*str_expr1*開頭比較的字串運算式。

## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會檢查字串 "abc" 是否以 "b" 和 "a" 開頭。  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 以下為結果集。  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>備註

該系統功能將受益于[範圍索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure 宇宙 DB](sql-query-string-functions.md)
- [系統功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 簡介](introduction.md)
